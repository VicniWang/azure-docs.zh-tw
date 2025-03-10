---
title: 運算子最佳做法 - Azure Kubernetes Services (AKS) 中的進階排程器功能
description: 了解叢集運算子在 Azure Kubernetes Service (AKS) 中使用進階排程器功能 (如污點和容差、節點選取器和親和性，或 Inter-pod 親和性和反親和性) 的最佳作法
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 395b0cadf3ba3313a9a1304d9244f1fe72a8209c
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016873"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) 中進階排程器功能的最佳做法

當您管理 Azure Kubernetes Service (AKS) 中的叢集時，通常必須隔離小組和工作負載。 Kubernetes 排程器提供了進階功能，可讓您控制可以在特定節點上排程哪些 pod，或者如何在叢集中適當地散發多 pod 應用程式。 

本最佳做法文章著重於叢集運算子的進階 Kubernetes 排程功能。 在本文中，您將了解：

> [!div class="checklist"]
> * 使用污點和容差來限制可以在節點上排程的 pod
> * 提供 pod 在使用節點選取器或節點親和性的特定節點上執行的喜好設定
> * 使用 inter-pod 親和性或反親和性分割 pod 或將 pod 群組在一起

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>使用污點和容差提供專用節點

**最佳作法指引** - 限制耗用大量資源的應用程式 (如輸入控制器) 對特定節點的存取權。 保持節點資源可提供需要它們的工作負載使用，並且不允許在節點上排程其他工作負載。

建立 AKS 叢集時，可以部署具有 GPU 支援的節點或大量功能強大的 CPU。 這些節點通常用於巨量資料處理工作負載，例如機器學習 (ML) 或人工智慧 (AI)。 由於這種類型的硬體通常是要部署的昂貴節點資源，因此請限制可在這些節點上排程的工作負載。 您可能希望在叢集中專用某些節點來執行輸入服務，並防止其他工作負載。

Kubernetes 排程器可以使用污點和容差來限制可以在節點上執行的工作負載。

* **污點**會套用至節點，該節點指示僅可以在其上排程特定的 pod。
* 然後**容差**會套用至容器，允許它們*容許*節點的污點。

將 pod 部署至 AKS 叢集時，Kubernetes 只會在容差與污點對齊之節點上排程 pod。 例如，假設您的 AKS 叢集中有一個節點池，用於具有 GPU 支援的節點。 您可以定義名稱，例如 *gpu*，然後定義排程的值。 如果將此值設定為 *NoSchedule*，則如果 pod 未定義適當的容差，則 Kubernetes 排程器無法在節點上排程 pod。

```console
kubectl taint node aks-nodepool1 gpu:NoSchedule
```

透過套用至節點的污點，您可以在 pod 規格中定義允許在節點上進行排程的容差。 下列範例定義 `key: gpu` 和 `effect: NoSchedule` 以容許上一個步驟中套用至節點的污點：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
  resources:
    requests:
      cpu: 0.5
      memory: 2Gi
    limits:
      cpu: 4.0
      memory: 16Gi
  tolerations:
  - key: "gpu"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```

部署此 pod 時 (例如使用 `kubectl apply -f gpu-toleration.yaml`)，Kubernetes 可以在套用污點的節點上成功排程 pod。 此邏輯隔離可讓您控制對叢集內資源的存取權。

套用污點時，請與應用程式開發人員和擁有者合作，以允許他們在部署中定義必要的容差。

如需污點和容差的相關詳細資訊，請參閱[套用污點和容差][k8s-taints-tolerations]。

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>使用節點選取器和親和性來控制 pod 排程

**最佳作法指引** - 使用節點選取器、節點親和性或 Inter-pod 親和性，來控制節點上的 pod 排程。 這些設定可讓 Kubernetes 排程器以邏輯方式隔離工作負載，例如節點中的硬體。

使用污點和容差以邏輯方式隔離永久截止的資源 - 如果 pod 不能容許節點的污點，則不會在節點上排程它。 另一種方法是使用節點選取器。 您將節點加上標籤，例如指出本機連接的 SSD 儲存體或大量的記憶體，然後在 pod 規格中定義節點選取器。 然後 Kubernetes 在比對的節點上排程這些 pod。 與容差不同，可以在加上標籤的節點上排程沒有相符節點選取器的 pod。 此行為允許節點上未使用的資源取用，但優先使用定義相符節點選取器的 pod。

讓我們看一下使用大量記憶體的節點範例。 這些節點可以提供要求大量記憶體的 pod 的喜好設定。 為了確定資源不會閒置，它們也會允許其他 pod 執行。

```console
kubectl label node aks-nodepool1 hardware:highmem
```

然後，pod 規格會新增 `nodeSelector` 屬性，以定義與節點上設定之標籤相符的節點選取器：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

當您使用這些排程器選項時，請與應用程式開發人員和擁有者一起使用，以允許他們正確定義其 pod 規範。

如需使用節點選取器的詳細資訊，請參閱[將 Pod 指派給節點][k8s-node-selector]。

### <a name="node-affinity"></a>節點親和性

節點選取器是將 pod 指派給指定節點的基本方法。 使用*節點親和性*可以獲得更多的彈性。 使用節點親和性，您可以定義如果pod無法與節點匹配會發生什麼事。 您可以*要求* Kubernetes 排程器與加上標籤的主機的 pod 相符。 或者，您可以*偏好*相符項目，但如果沒有相符項目，則允許在其他主機上排程 pod。

下列範例將節點親和性設定為 *requiredDuringSchedulingIgnoredDuringExecution*。 此親和性要求Kubernetes計劃使用具有匹配標籤的節點。 如果沒有可用節點，則 pod 必須等候排程以繼續。 若要允許在其他節點上排程 pod，您可以將值設定為 *preferredDuringScheduledIgnoreDuringExecution*：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: hardware
          operator: In
          values: highmem
```

設定的 *IgnoredDuringExecution* 部分指出如果節點標籤發生變更，則不應從節點中刪除該 pod。 Kubernetes 排程器僅對正在排程的新 pod，而不是已在節點上排程的 pod 使用更新的節點標籤。

如需詳細資訊，請參閱[親和性和反親和性][k8s-affinity]。

### <a name="inter-pod-affinity-and-anti-affinity"></a>Inter-pod 親和性和反親和性

Kubernetes 排程器以邏輯方式隔離工作負載的最後一種方法，是使用 inter-pod 親和性或反親和性。 這些設定定義「不應該」在具有現有相符 pod 的節點上排程 pod，或者「應該」排程。 根據預設，Kubernetes 排程器會嘗試跨節點在複本集中排程多個 pod。 您可以圍繞此行為定義更特定的規則。

也會使用 Azure Cache for Redis 的 Web 應用程式是一個很好的例子。 您可以使用 pod 反親和性的規則來要求 Kubernetes 排程器跨節點散發複本。 然後，您可以使用親和性規則來確保每個 Web 應用程式元件都在與對應快取相同的主機上排程。 跨節點的 pod 散發如下例所示：

| **節點 1** | **節點 2** | **節點 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

這個範例比使用節點選取器或節點親和性更複雜。 透過該部署，您可以控制 Kubernetes 在節點上排程 pod 的方式，並可以邏輯方式隔離資源。 如需此 Web 應用程式使用 Azure Cache for Redis 範例的完整範例，請參閱[在相同的節點上共置 Pod][k8s-pod-affinity]。

## <a name="next-steps"></a>後續步驟

這篇文章著重於 Kubernetes 排程器的進階功能。 如需 AKS 中叢集作業的相關詳細資訊，請參閱下列最佳作法：

* [多租用戶和叢集隔離][aks-best-practices-scheduler]
* [基本的 Kubernetes 排程器功能][aks-best-practices-scheduler]
* [驗證和授權][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
