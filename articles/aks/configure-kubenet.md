---
title: 在 Azure Kubernetes Service (AKS) 中設定 kubenet 網路
description: 了解如何在 Azure Kubernetes Service (AKS) 中設定 kubenet (基本) 網路，以將 AKS 叢集部署到現有的虛擬網路和子網路中。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/31/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 6d2b6ce2804fce35af9c184c4a7c72c0b332f6fb
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701514"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用 kubenet 網路與您自己的 IP 位址範圍

根據預設，AKS 叢集會使用 [kubenet][kubenet]，並為您建立 Azure 虛擬網路和子網路。 使用 *kubenet*，節點會從 Azure 虛擬網路子網路取得 IP 位址。 Pod 會從邏輯上不同的位址空間到節點的 Azure 虛擬網路子網路接收 IP 位址。 然後設定網路位址轉譯 (NAT)，以便 Pod 可以連線到 Azure 虛擬網路上的資源。 流量的來源 IP 位址是 NAT 到節點的主要 IP 位址。 這種方法可大幅減少您需要在網路空間中保留，以供 Pod 使用的 IP 位址數目。

使用 [Azure 容器網路介面 (CNI)][cni-networking]，每個 Pod 都從子網路取得 IP 位址，並且可以直接存取。 這些 IP 位址在您的網路空間中必須是唯一的，且必須事先規劃。 每個節點都有一個組態參數，用於它支援的最大 Pod 數目。 然後，為該節點預先保留每個節點的相同 IP 位址數目。 此方法需要更多的規劃，並且通常會導致 IP 位址耗盡，或者隨著應用程式需求增加，需要在更大的子網路中重建叢集。

本文將說明如何使用 *kubenet* 網路來建立虛擬網路子網路，並將其與 AKS 叢集搭配使用。 如需關於網路選項與考量的詳細資訊，請參閱 [Kubernetes 和 AKS 的網路概念][aks-network-concepts]。

## <a name="before-you-begin"></a>開始之前

您必須安裝並設定 Azure CLI 版本 2.0.56 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>使用您自己的子網路的 Kubenet 網路概觀

在許多環境中，您已定義具有已配置的 IP 位址範圍的虛擬網路和子網路。 這些虛擬網路資源用來支援多個服務和應用程式。 若要提供網路連線，AKS 叢集可以使用 *kubenet* (基本網路) 或 Azure CNI (*進階網路*)。

使用 *kubenet*，只有節點在虛擬網路子網路中接收 IP 位址。 Pod 無法彼此直接通訊。 相反地，使用者定義路由 (UDR) 和 IP 轉送會用來進行 跨節點的 Pod 之間的連線。 您還可以在接收已指派 IP 位址的服務後面部署 Pod，並為應用程式的流量進行負載平衡。 下圖顯示 AKS 節點如何在虛擬網路子網路中 (而不是 Pod 中) 接收 IP 位址：

![Kubenet 網路模型與 AKS 叢集](media/use-kubenet/kubenet-overview.png)

Azure 在 UDR 中最多支援 400 條路由，因此您不能擁有超過 400 個節點的 AKS 叢集。 *kubenet* 不支援 AKS 功能，例如[虛擬節點][virtual-nodes]或網路原則。

使用 *Azure CNI*，每個 Pod 都會接收 IP 子網路中的 IP 位址，並可以直接與其他 Pod 和服務進行通訊。 您的叢集可以與您指定的 IP 位址範圍一樣大。 不過，必須事先規劃 IP 位址範圍，並且 AKS 節點根據它們可以支援的最大 Pod 數目來使用所有 IP 位址。 *Azure CNI* 支援進階網路功能和案例，例如[虛擬節點][virtual-nodes]或網路原則。

### <a name="ip-address-availability-and-exhaustion"></a>IP 位址可用性與耗盡

對於 *Azure CNI*，常見問題是指派的 IP 位址範圍太小，無法在調整或將叢集升級時加入其他節點。 網路小組可能也無法發出夠大的 IP 位址範圍來支援您預期的應用程式需求。

作為折衷方案，您可以建立使用 *kubenet* 並連接到現有虛擬網路子網路的 AKS 叢集。 此方法可讓節點接收定義的 IP 位址，而無需事先為叢集中可能執行的所有潛在 Pod 保留大量的 IP 位址。

使用 *kubenet*，您可以使用更小的 IP 位址範圍，並能夠支援大型叢集和應用程式需求。 例如，即使具有 */27* IP 位址範圍，您也可以執行具有足夠擴充或升級空間的 20-25 節點叢集。 此叢集大小可支援最多 *2,200-2,750* 個 Pod (每個節點預設最多 110 個 Pod)。

下列基本計算會比較網路模型中的差異：

- **kubenet** - 一個簡單的 */24* IP 位址範圍，最多可支援叢集中的 *251* 個節點 (每個 Azure 虛擬網路子網路會保留前三個用於管理作業的 IP 位址)
  - 此節點計數最多可以支援 *27,610* 個 Pod (*kubenet* 每個節點預設最多 110 個 Pod)
- **Azure CNI** - 相同的基本 */24* 子網路範圍只能支援叢集中最多 *8* 個節點
  - 此節點計數最多僅支援 *240* 個 Pod (*Azure CNI* 每個節點預設最多 30 個 Pod)

> [!NOTE]
> 這些最大值不列入帳戶升級，或調整規模作業。 實際上，您無法執行子網路 IP 位址範圍所支援的節點數目上限。 您必須在升級作業期間保留一些可用的 IP 位址。

### <a name="virtual-network-peering-and-expressroute-connections"></a>虛擬網路對等互連和 ExpressRoute 連線

若要提供內部部署連線能力，*kubenet* 和 *Azure-CNI* 網路方法都可以使用 [Azure 虛擬網路對等互連][vnet-peering]或是 [ExpressRoute 連線][express-route]。 仔細規劃您的 IP 位址範圍，以避免重疊和不正確的流量路由。 例如，許多內部部署網路使用透過 ExpressRoute 連線通告的 *10.0.0.0/8* 位址範圍。 建議您將 AKS 叢集建立到此位址範圍之外的 Azure 虛擬網路子網路中，例如 *172.26.0.0/16*。

### <a name="choose-a-network-model-to-use"></a>選擇要使用的網路模型

選擇用於AKS群集的網絡插件通常是靈活性和高級配置需求之間的平衡。 下列考量有助於概述每種網路模型最合適的時機。

*kubenet* 使用時機：

- 您的 IP 位址空間有限。
- 大部分的 Pod 通訊是在叢集內。
- 您不需要虛擬節點或網路原則等進階功能。

*Azure CNI* 使用時機：

- 您有可用的 IP 位址空間。
- 大部分的 Pod 通訊是在叢集外部的資源。
- 您不想管理 UDR。
- 您需要進階功能，例如虛擬節點或網路原則。

## <a name="create-a-virtual-network-and-subnet"></a>建立虛擬網路和子網路

若要開始使用 *kubenet* 和您自己的虛擬網路子網路，請首先使用 [az group create][az-group-create] 命令建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

如果您沒有要使用的現有虛擬網路和子網路，請使用 [az network vnet create][az-network-vnet-create] 命令建立這些網路資源。 在下列範例中，虛擬網路的名稱為 myVnet，位址首碼為 10.0.0.0/8。 建立名為 myAKSSubnet 的子網路，位址首碼為 10.240.0.0/16。

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

## <a name="create-a-service-principal-and-assign-permissions"></a>建立服務主體並指派權限

為了允許 AKS 叢集與其他 Azure 資源互動，則會使用 Azure Active Directory 服務主體。 服務主體必須具有管理 AKS 節點使用的虛擬網路和子網路的權限。 若要建立服務主體，請使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令：

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

下列範例輸出顯示您的服務主體的應用程式識別碼和密碼。 這些值用於其他步驟，以將角色指派給服務主體，然後再建立 AKS 叢集：

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

若要在其餘步驟中指派正確的委派，請使用 [az network vnet show][az-network-vnet-show] 和 [az network vnet subnet show][az-network-vnet-subnet-show] 命令取得所需的資源識別碼。 這些資源識別碼會儲存為變數，並在其餘的步驟中參考：

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

現在，使用 [az role assignment create][az-role-assignment-create] 命令為虛擬網路上的 AKS 叢集「參與者」權限指派服務主體。 提供先前命令輸出中所顯示您自己的 */<appId/>* 以建立服務主體：

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>在虛擬網路中建立 AKS 叢集

您現在已經建立虛擬網路和子網路，並為服務主體建立並指派使用這些網路資源的權限。 現在，使用 [az aks create][az-aks-create] 命令在虛擬網路和子網路中建立 AKS 叢集。 定義先前命令輸出中所顯示您自己的 */<appId/>* 和 */<password/>* 以建立服務主體。

下列 IP 位址範圍也定義為叢集建立程序的一部分：

* *--service-cidr* 用於為 AKS 叢集中的內部服務指派 IP 位址。 此 IP 位址範圍應該是您的網路環境中其他未使用的位址空間。 如果您使用 Express Route 或站對站 VPN 連線連接或計劃連接 Azure 虛擬網路，這會包括任何內部部署網路範圍。

* *--dns-service-ip* 位址應該是服務 IP 位址範圍的 *.10* 位址。

* *--pod-cidr* 應該是您的網路環境中未使用的大型位址空間。 如果您使用 Express Route 或站對站 VPN 連線連接或計劃連接 Azure 虛擬網路，這會包括任何內部部署網路範圍。
    * 此位址範圍必須大到足以容納您希望相應增加的節點數目。 如果您需要更多位址用於其他節點，則無法在部署叢集之後變更此位址範圍。
    * Pod IP 位址範圍用來為叢集中的每個節點指派 */24* 位址空間。 在下列範例中，*192.168.0.0/16* 的 *--pod-cidr* 指派第一個節點 *192.168.0.0/24*、第二個節點 *192.168.1.0/24* 和第三個節點 *192.168.2.0/24*。
    * 隨著叢集縮放比例或升級，Azure 平台會繼續為每個新的節點指派一個 Pod IP 位址範圍。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 192.168.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

## <a name="associate-network-resources-with-the-node-subnet"></a>將網路資源與節點子網路相關聯

當您建立 AKS 叢集時，會建立網路安全群組和路由表。 這些網路資源是受 AKS 控制平面管理，並在您建立並公開 (expose) 服務時進行更新。 將網路安全性群組和路由資料表與虛擬網路子網路關聯，如下所示：

```azurecli-interactive
# Get the MC_ resource group for the AKS cluster resources
MC_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)

# Get the route table for the cluster
ROUTE_TABLE=$(az network route-table list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Get the network security group
NODE_NSG=$(az network nsg list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Update the subnet to associate the route table and network security group
az network vnet subnet update \
    --route-table $ROUTE_TABLE \
    --network-security-group $NODE_NSG \
    --ids $SUBNET_ID
```

## <a name="next-steps"></a>後續步驟

透過將 AKS 叢集部署到您現有的虛擬網路子網路中，您現在可以如往常一樣使用叢集。 從[使用 Azure Dev Spaces 建置應用程式][dev-spaces]或[使用草稿][use-draft]，或[使用 Helm 部署應用程式][use-helm]開始。

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
