---
title: 在 Azure Kubernetes Service (AKS) 中使用 Kured 更新並重新啟動節點
description: 了解如何在 Azure Kubernetes Service (AKS) 中，使用 Kured 更新節點並自動重新啟動它們
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 11/06/2018
ms.author: iainfou
ms.openlocfilehash: 0bcc49df6540b73b8feb5bb1ec4312e680572797
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617806"
---
# <a name="apply-security-and-kernel-updates-to-nodes-in-azure-kubernetes-service-aks"></a>將安全性和核心更新套用至 Azure Kubernetes Service (AKS) 中的節點

為了保護您的叢集，系統會自動將安全性更新套用至 AKS 中的節點。 這些更新包括 OS 安全性修正或核心更新。 這其中有一些更新需要重新啟動節點，才能完成此程序。 AKS 不會自動重新啟動節點來完成更新程序。

本文將示範如何使用開放原始碼 [Kured (KUbernetes REboot Daemon)][kured] 來監看需要重新啟動的節點，然後自動處理執行 Pod 和節點重新啟動程序的重新排程。

> [!NOTE]
> `Kured` 是由 Weaveworks 所提供的開放原始碼專案。 我們會盡力在 AKS 中提供對此專案的支援。 如需其他支援，請參閱 #weave-community Slack 頻道。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也必須安裝並設定 Azure CLI 版本 2.0.49 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="understand-the-aks-node-update-experience"></a>了解 AKS 節點更新體驗

在 AKS 叢集中，您的 Kubernetes 節點會當作 Azure 虛擬機器 (VM) 來執行。 這些 Linux 型 VM 會使用 Ubuntu 映像，其 OS 已設定為每晚自動檢查更新。 如果有安全性或核心更新可供使用，就會自動下載並安裝它們。

![使用 Kured 的 AKS 節點更新並重新啟動程序](media/node-updates-kured/node-reboot-process.png)

某些安全性更新 (例如核心更新) 需要重新啟動節點，才能完成此程序。 需要重新啟動的節點會建立名為 */var/run/reboot-required* 的檔案。 此重新啟動程序不會自動執行。

您可以使用自己的工作流程和程序來處理節點重新啟動，或使用 `kured` 來協調此程序。 使用 `kured` 來部署 [DaemonSet][DaemonSet]，以便在叢集中的每個節點上執行 Pod。 DaemonSet 中的這些 Pod 會先監看 */var/run/reboot-required* 檔案是否存在，然後初始重新啟動節點的程序。

### <a name="node-upgrades"></a>節點升級

AKS 中有一個額外的程序可讓您的「升級」叢集。 升級通常會移至較新版的 Kubernetes，而不只是套用節點安全性更新。 AKS 升級會執行下列動作：

* 利用已套用的最新安全性更新與 Kubernetes 版本來部署新節點。
* 舊節點會遭到封鎖並清空。
* 在新節點上為 Pod 設定排程。
* 刪除舊節點。

在升級事件期間，您不能保持在同一個 Kubernetes 版本。 您必須指定較新版的 Kubernetes。 若要升級至最新版的 Kubernetes，您可以[升級 AKS 叢集][aks-upgrade]。

## <a name="deploy-kured-in-an-aks-cluster"></a>在 AKS 叢集中部署 Kured

若要部署 `kured` DaemonSet，從他們的 GitHub 專案頁面套用下列範例 YAML 資訊清單。 此資訊清單會建立角色和叢集角色、繫結及服務帳戶，然後使用 `kured` 1.1.0 版 (支援 AKS 叢集 1.9 或更新版本) 部署 DaemonSet。

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.1.0/kured-1.1.0.yaml
```

您也可以設定適用於 `kured` 的其他參數，例如，與 Prometheus 或 Slack 整合。 如需其他設定參數的詳細資訊，請參閱 [Kured 安裝文件][kured-install]。

## <a name="update-cluster-nodes"></a>更新叢集節點

根據預設，AKS 節點每晚都會檢查更新。 如果您不想等待，則可手動執行更新以檢查 `kured` 能夠正確執行。 首先，遵循步驟以 [SSH 到您的其中一個 AKS 節點][aks-ssh]。 一旦建立與節點的 SSH 連線之後，請檢查更新並套用它們，如下所示：

```console
sudo apt-get update && sudo apt-get upgrade -y
```

如果已套用需要重新啟動節點的更新，即會將檔案寫入 */var/run/reboot-required*。 `Kured` 預設每隔 60 分鐘就會檢查需要重新啟動的節點。

## <a name="monitor-and-review-reboot-process"></a>監視和檢閱重新啟動程序

已偵測到 DaemonSet 內的其中一個複本需要重新啟動節點時，就會透過 Kubernetes API 在節點上放置一個鎖定。 此鎖定可防止在節點上為其他 Pod 設定排程。 此鎖定也指出一次應該只能重新啟動一個節點。 封鎖節點之後，即會清空執行中的 Pod，並將節點重新啟動。

您可以使用 [kubectl get nodes][kubectl-get-nodes] 命令來監視節點狀態。 下列範例輸出會在節點準備好進行重新啟動程序時，顯示狀態為 *SchedulingDisabled* 的節點：

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-79590246-2   Ready,SchedulingDisabled   agent     1h        v1.9.11
```

當更新程序完成之後，您就可以使用 [kubectl get nodes][kubectl-get-nodes] 命令搭配 `--output wide` 參數來檢視節點狀態。 這個額外的輸出可讓您查看 *KERNEL-VERSION* 基礎節點中的差異，如下列範例輸出所示。 *aks-nodepool1-79590246-2* 已在上一個步驟更新，並顯示核心版本 *4.15.0-1025-azure*。 尚未更新的節點 *aks-nodepool1-79590246-1* 會顯示核心版本 *4.15.0-1023-azure*。

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.11   10.240.0.6    <none>        Ubuntu 16.04.5 LTS   4.15.0-1023-azure   docker://1.13.1
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.11   10.240.0.4    <none>        Ubuntu 16.04.5 LTS   4.15.0-1025-azure   docker://1.13.1
```

## <a name="next-steps"></a>後續步驟

本文已詳細說明如何使用 `kured`，在安全性更新程序過程中自動重新啟動節點。 若要升級至最新版的 Kubernetes，您可以[升級 AKS 叢集][aks-upgrade]。

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
