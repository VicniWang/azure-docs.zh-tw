---
title: Concepts - Azure Kubernetes Services (AKS) 的 Kubernetes 基本概念
description: 了解 Kubernetes 的基本叢集和工作負載元件，及其與 Azure Kubernetes Service (AKS) 中的功能有何關聯
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 7f964397b476d5a97ecdde0ae22bd6662a435e1a
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456515"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念

隨著應用程式開發逐漸改採容器型方法，協調和管理相互連線的資源，就成為日漸重要的需求。 Kubernetes 是領先的平台，可用來提供容錯應用程式工作負載的可靠排程。 Azure Kubernetes Service (AKS) 是受管理的 Kubernetes 供應項目，可進一步簡化容器型應用程式的部署和管理。

本文介紹核心 Kubernetes 基礎結構元件，例如*叢集主機*、*節點*和*節點集區*。 此外也介紹 *Pod*、*部署*和*集合*等工作負載資源，並說明如何將資源分組到*命名空間*中。

## <a name="what-is-kubernetes"></a>什麼是 Kubernetes？

Kubernetes 是一個快速發展中的平台，可管理容器型應用程式及其相關聯的網路和儲存體元件。 重點在於應用程式工作負載，而不是基礎結構元件。 Kubernetes 提供宣告式部署方法，並以一組完善的 API 管理作業，作為此部署方法的後盾。

您可以建置並執行新型、可攜、以微服務為基礎的應用程式，藉由使用 Kubernetes 來協調和管理這些應用程式元件的可用性而獲益。 隨著小組採用以微服務為基礎的應用程式而獲得進展，Kubernetes 對於無狀態與具狀態的應用程式均提供支援。

Kubernetes 屬於開放式平台，可讓您使用慣用的程式設計語言、作業系統、程式庫或訊息匯流排來建置您的應用程式。 現有的持續整合與持續傳遞 (CI/CD) 工具可與 Kubernetes 整合，以排程及部署發行。

Azure Kubernetes Service (AKS) 提供受控 Kubernetes 服務，可降低部署和核心管理工作的複雜度，包括協調升級。 AKS 叢集主機由 Azure 平台所管理，且您只需針對執行應用程式的 AKS 節點付費。 AKS 以開放原始碼 Azure Kubernetes Service 引擎 (aks-engine) 作為建置基礎。

## <a name="kubernetes-cluster-architecture"></a>Kubernetes 叢集架構

Kubernetes 叢集分成兩個元件：

- *叢集主機*節點提供核心 Kubernetes 服務和應用程式工作負載的協調流程。
- *節點*會執行您的應用程式工作負載。

![Kubernetes 叢集主機和節點元件](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>叢集主機

當您建立 AKS 叢集時，就會自動建立並設定叢集主機。 此叢集主機會以受控 Azure 資源的形式提供，使用者無需加以管理。 叢集主機不會產生費用，只有屬於 AKS 叢集的節點才需計費。

叢集主機包含下列核心 Kubernetes 元件：

- *kube-apiserver* - API 伺服器是基礎 Kubernetes API 得以公開的途徑。 此元件可提供管理工具的互動，例如 `kubectl` 或 Kubernetes 儀表板。
- *etcd* - 具有高可用性的 *etcd* 是 Kubernetes 中的金鑰值存放區，用以維護 Kubernetes 叢集和設定的狀態。
- *kube-scheduler* - 當您建立或調整應用程式時，排程器會判斷哪些節點可執行工作負載，並加以啟動。
- *kube-controller-manager* - 控制器管理員會監看較小型的，這些控制器負責執行複寫 Pod 和處理節點作業之類的動作。

AKS 提供具有專用 API 伺服器、排程器等項目的單一租用戶叢集主機。您可以定義節點的數目和大小，而 Azure 平台會設定叢集主機與節點之間的安全通訊。 與叢集主機之間的互動可透過 Kubernetes API 進行，例如 `kubectl` 或 Kubernetes 儀表板。

此叢集主機受到管理，這表示您不需要設定高可用性 *etcd* 存放區這類的元件，但也意味著您無法直接存取叢集主機。 Kubernetes 的升級可透過 Azure CLI 或 Azure 入口網站來協調，其程序會先升級叢集主機，再升級節點。 若要對可能的問題進行疑難排解，您可以透過 Azure 監視器記錄檔檢閱叢集主機記錄。

如果您需要以特定方式設定叢集主機，或需要直接加以存取，您可以使用 [aks-engine][aks-engine] 自行部署 Kubernetes 叢集。

## <a name="nodes-and-node-pools"></a>節點和節點集區

若要執行應用程式和支援的服務，您必須要有 Kubernetes *節點*。 AKS 叢集中有一或多個節點，而此類節點是可執行 Kubernetes 節點元件和容器執行階段的 Azure 虛擬機器 (VM)：

- `kubelet` 是 Kubernetes 代理程式，負責處理來自叢集主機的協調流程要求，和處理相關排程以執行要求的容器。
- 虛擬網路由每個節點上的 *kube-proxy* 負責處理。 Proxy 會路由網路流量，以及管理服務和 Pod 的 IP 定址。
- *容器執行階段*這項元件可讓容器化應用程式執行其他資源並與其互動，例如虛擬網路和儲存體。 在 AKS 中會以 Docker 作為容器執行階段。

![Kubernetes 節點的 Azure 虛擬機器和支援的資源](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

節點的 Azure VM 大小將定義可用的 CPU 數量、記憶體數量，以及儲存體的大小和類型 (例如高效能 SSD 或一般 HDD)。 如果您預期有應用程式需要大量的 CPU 和記憶體或高效能儲存體，請據以規劃節點大小。 您也可以在 AKS 叢集中相應增加節點數目，以符合需求。

在 AKS 中，您的叢集中各個節點的 VM 映像目前均以 Ubuntu Linux 為基礎。 當您建立 AKS 叢集或相應增加節點數目時，Azure 平台即會依據您要求的數目建立 VM，並加以設定。 沒有手動設定可供執行。

如果您需要使用不同的主機 OS、容器執行階段或要納入自訂套件，您可以使用 [aks-engine][aks-engine] 自行部署 Kubernetes 叢集。 上游 `aks-engine` 會在功能於 AKS 叢集中正式受到支援之前發行這些功能，並提供設定選項。 例如，如果您想要使用 Docker 以外的 Windows 容器或容器執行階段，您可以使用 `aks-engine` 設定及部署 Kubernetes 叢集，以符合您目前的需求。

### <a name="resource-reservations"></a>資源保留

您不需要管理每個節點上的核心 Kubernetes 元件，例如 kubelet、kube-proxy 和 kube-dns，但它們的確會耗用一些可用的計算資源。 為了維持節點的效能與功能，每個節點上都會保留下列計算資源：

- **CPU** - 60 毫秒
- **記憶體** - 20%，最多 4 GiB

保留這些資源代表應用程式可用的 CPU 和記憶體數量，看起來可能會小於節點本身所含的資源數量。 如果由於所執行的應用程式數量導致資源受限，則所保留的這些資源可確保仍有 CPU 和記憶體可供核心 Kubernetes 元件使用。 保留的資源無法變更。

例如︰

- **標準 DS2 v2** 節點大小包含 2 個 vCPU 和 7 GiB 記憶體
    - 7 GiB 記憶體的 20% = 1.4 GiB
    - 共有 (7 - 1.4) = 5.6 GiB 記憶體可供節點使用
    
- **標準 E4s v3** 節點大小包含 4 個 vCPU 和 32 GiB 記憶體
    - 32 GiB 記憶體的 20% = 6.4 GiB，但 AKS 最多只會保留 4 GiB
    - 共有 (32 - 4) = 28 GiB 可供節點使用
    
基礎節點 OS 也需要一定數量的 CPU 和記憶體資源，才能完成它自己的核心功能。

### <a name="node-pools"></a>節點集區

相同設定的節點會一起分組到*節點集區*中。 Kubernetes 叢集包含一或多個節點集區。 您在建立 AKS 叢集時會定義初始的節點數目和大小，而建立*預設節點集區*。 AKS 中的這個預設節點集區包含用來執行代理程式節點的基礎 VM。

當您調整或升級 AKS 叢集時，相關動作會對預設節點集區執行。 進行升級作業時，執行中的容器會排程於節點集區中的其他節點上，直到所有節點皆成功升級。

## <a name="pods"></a>Pod

Kubernetes 會使用 *Pod* 執行您的應用程式執行個體。 一個 Pod 代表應用程式的單一執行個體。 Pod 與容器之間通常會有 1:1 的對應，不過在進階案例中，Pod 可能會包含多個容器。 這些多容器 Pod 會一起排程於相同的節點上，並允許容器共用相關資源。

當您建立 Pod 時，您可以定義*資源限制*以要求特定數量的 CPU 或記憶體資源。 Kubernetes 排程器會嘗試將 Pod 排程在具有可用資源的節點上執行，以符合要求。 您也可以指定資源上限，以防止指定的 Pod 在基礎節點上耗用太多計算資源。 最佳做法是為所有 Pod 加上資源限制，以協助 Kubernetes 排程器了解哪些是必要且可供使用的資源。

如需詳細資訊，請參閱 [Kubernetes Pod][kubernetes-pods] 和 [Kubernetes Pod 生命週期][kubernetes-pod-lifecycle]。

Pod 是邏輯資源，但應用程式工作負載執行的所在之處是容器。 Pod 通常是暫時性、可處置的資源，且個別排程的 Pod 會無法獲益於 Kubernetes 所提供的一些高可用性和備援功能。 實際上，Pod 通常會由 Kubernetes *控制器*部署及管理，例如「部署控制器」。

## <a name="deployments-and-yaml-manifests"></a>部署和 YAML 資訊清單

一項*部署*可代表一或多個由 Kubernetes 部署控制器管理的相同 Pod。 部署會定義要建立的*複本* (Pod) 數目，且 Kubernetes 排程器會確保在 Pod 或節點發生問題時可在狀況良好的節點上排程其他 Pod。

您可以更新部署以變更 Pod 的設定、使用的容器映像，或連結的儲存體。 部署控制器會清空並終止指定數目的複本、從新的部署定義建立複本，然後繼續進行處理，直到部署中的所有複本皆完成更新。

AKS 中的多數無狀態應用程式均應使用部署模型，而不是排程個別的 Pod。 Kubernetes 可監視部署的健康情況和狀態，以確定有所需數量的複本執行於叢集內。 如果您僅排程個別的 Pod，Pod 在發生問題時並不會重新啟動，且在其目前的節點發生問題時也不會重新排程於狀況良好的節點上。

如果應用程式需要執行個體仲裁以便隨時可供管理決策擬定之用，您就不應讓更新程序中斷該項功能。 您可以使用 *Pod 中斷預算*，定義在更新或節點升級期間可在部署中停止多少個複本。 例如，如果您的部署中有 *5* 個複本，您可以將 Pod 中斷定義為 *4*，而每次僅允許一個複本不受刪除/重新排程。 與 Pod 資源限制相同，最佳做法是為需要有最少量複本持續存在的應用程式定義 Pod 中斷預算。

部署通常可透過 `kubectl create` 或 `kubectl apply` 來建立和管理。 若要建立部署，您可以定義 YAML (YAML 不是標記語言) 格式的資訊清單檔。 下列範例會建立 NGINX Web 伺服器的基本部署。 此部署會指定要建立 *3* 個複本，並開啟容器上的連接埠 *80*。 此外也會定義 CPU 和記憶體的資源要求和限制。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

您也可以在 YAML 資訊清單中納入負載平衡器之類的服務，以建立更複雜的應用程式。

如需詳細資訊，請參閱 [Kubernetes 部署][kubernetes-deployments]。

### <a name="package-management-with-helm"></a>使用 Helm 管理套件

[Helm][helm] 是在 Kubernetes 中管理應用程式的常見方法。 您可以建置和使用現有的公用 Helm *圖表*，其中包含封裝版的應用程式程式碼，和用來部署資源的 Kubernetes YAML 資訊清單。 這些 Helm 圖表可儲存於本機，或通常儲存在遠端存放庫中，例如 [Azure Container Registry Helm 圖表存放庫][acr-helm]。

若要使用 Helm，請在您的 Kubernetes 叢集中安裝名為 *Tiller* 的伺服器元件。 Tiller 會管理安裝在叢集內的圖表。 Helm 用戶端本身會安裝在您的本機電腦上，或可在 [Azure Cloud Shell][azure-cloud-shell] 內使用。 您可以使用用戶端搜尋或建立 Helm 圖表，然後將其安裝至 Kubernetes 叢集。

![Helm 包含可在 Kubernetes 叢集內建立資源的用戶端元件和伺服器端 Tiller 元件](media/concepts-clusters-workloads/use-helm.png)

如需詳細資訊，請參閱[在 Azure Kubernetes Service (AKS) 中使用 Helm 安裝應用程式][aks-helm]。

## <a name="statefulsets-and-daemonsets"></a>StatefulSet 和 Daemonset

部署控制器會使用 Kubernetes 排程器，在任何有可用資源的可用節點上執行指定數量的複本。 這種部署使用方法可能足以因應無狀態應用程式的需要，但不適用於需要持續性命名慣例或儲存體的應用程式。 對於需要有複本存在於叢集內各個節點 (或選取的節點) 上的應用程式，部署控制器並不會考量複本分散到各節點間的方式。

有兩項 Kubernetes 資源可讓您管理此類型的應用程式：

- *StatefulSet* - 跨個別 Pod 生命週期維護應用程式的狀態，例如儲存體。
- *Daemonset* - 及早在 Kubernetes 啟動程序執行時確定每個節點都有執行中的執行個體。

### <a name="statefulsets"></a>StatefulSet

現今的應用程式開發通常以無狀態應用程式為目標，但 *StatefulSet* 可用於具狀態的應用程式，例如包含資料庫元件的應用程式。 StatefulSet 類似於會建立和管理一或多個相同 Pod 的部署。 StatefulSet 中的複本會依照正常、循序的方法進行部署、調整、升級和終止。 透過 StatefulSet，命名慣例、網路名稱和儲存體在複本重新排程後仍可持續保存。

您可以使用 `kind: StatefulSet` 定義 YAML 格式的應用程式，隨後再由 StatefulSet 控制器處理必要複本的部署和管理。 資料會寫入至 Azure 受控磁碟或 Azure 檔案所提供的永續性儲存體。 透過 StatefulSet，即使在 StatefulSet 刪除後，基礎的永續性儲存體仍將保存。

如需詳細資訊，請參閱 [Kubernetes StatefulSet][kubernetes-statefulsets]。

StatefulSet 中的複本可在 AKS 叢集中任何可用的節點上排程及執行。 如果您必須確定每個節點都至少要執行您集合中的一個 Pod，您可以改用 DaemonSet。

### <a name="daemonsets"></a>DaemonSet

針對特定的記錄收集或監視需求，您可能需要在所有或選取的節點上執行指定的 Pod。 *DaemonSet* 同樣可用來部署一或多個相同的 Pod，但 DaemonSet 控制器可確保每個指定的節點都會執行一個 Pod 執行個體。

DaemonSet 控制器可及早在叢集啟動程序執行時，在預設 Kubernetes 排程器啟動之前將 Pod 排程於節點上。 這項功能可確保 DaemonSet 中的 Pod 會在部署或 StatefulSet 中的傳統 Pod 排程之前啟動。

和 StatefulSet 相同，DaemonSet 也可使用 `kind: DaemonSet` 定義為 YAML 定義的一部分。

如需詳細資訊，請參閱 [Kubernetes DaemonSet][kubernetes-daemonset]。

## <a name="namespaces"></a>命名空間

Kubernetes 資源 (例如 Pod 和部署) 會依邏輯分組到*命名空間*中。 藉由這樣的分組，即可依邏輯區隔 AKS 叢集，並限制建立、檢視或管理資源的存取權。 例如，您可以建立命名空間以區隔商務群組。 使用者只能與其指派的命名空間內包含的資源互動。

![依邏輯區隔資源和應用程式的 Kubernetes 命名空間](media/concepts-clusters-workloads/namespaces.png)

您在建立 AKS 叢集時，可以使用下列命名空間：

- *default* - 此命名空間是在未提供任何 Pod 和部署時依預設用來建立這些項目的位置。 在較小的環境中，您可以直接將應用程式部署到預設命名空間中，而無須建立額外的邏輯分隔。 當您與 Kubernetes API 互動時 (例如 `kubectl get pods`)，若未指定命名空間，將會使用預設值。
- *kube-system* - 此命名空間是核心資源的所在之處，例如 DNS 和 Proxy 之類的網路功能，或是 Kubernetes 儀表板。 您通常不會將自己的應用程式部署到此命名空間中。
- *kube-public* - 此命名空間通常不會使用，但可用於要在整個叢集中顯示，並且可供任何使用者檢視的資源。

如需詳細資訊，請參閱 [Kubernetes 命名空間][kubernetes-namespaces]。

## <a name="next-steps"></a>後續步驟

本文說明了部分核心 Kubernetes 元件，及其套用至 AKS 叢集的方式。 如需關於 Kubernetes 及 AKS 核心概念的詳細資訊，請參閱下列文章：

- [Kubernetes / AKS 存取和身分識別][aks-concepts-identity]
- [Kubernetes / AKS 安全性][aks-concepts-security]
- [Kubernetes / AKS 虛擬網路][aks-concepts-network]
- [Kubernetes / AKS 儲存體][aks-concepts-storage]
- [Kubernetes / AKS 調整][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
