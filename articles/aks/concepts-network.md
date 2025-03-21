---
title: 概念 - Azure Kubernetes Service (AKS) 中的網路功能
description: 了解 Azure Kubernetes Service (AKS) 中的網路功能，包括 kubenet 和 Azure CNI 網路功能、輸入控制器、負載平衡器和靜態 IP 位址。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 6affa19c61ff4a824e390c42b7fd97554a30c9bb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176232"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中的網路概念

在應用程式開發的容器型微服務方法中，應用程式元件必須搭配運作以處理其工作。 Kubernetes 提供多種可支援此類應用程式通訊的資源。 您可以在內部或外部連接和公開應用程式。 若要建置具有高可用性的應用程式，您可以對應用程式進行負載平衡。 較複雜的應用程式可能需要設定終止 SSL/TLS 的輸入流量或多個元件的路由。 基於安全考量，您可能也需要限制進入 Pod 和節點或其間的網路流量。

本文將介紹為 AKS 中的應用程式提供網路功能的核心概念：

- [服務](#services)
- [Azure 虛擬網路](#azure-virtual-networks)
- [輸入控制器](#ingress-controllers)
- 網路原則

## <a name="kubernetes-basics"></a>Kubernetes 基本概念

為了提供對您應用程式的存取能力，或讓應用程式元件能夠彼此通訊，Kubernetes 提供了虛擬網路的抽象層。 Kubernetes 節點會連線至虛擬網路，並且可提供 Pod 的輸入和輸出連線能力。 *Kube-proxy* 元件會在每個節點上執行以提供這些網路功能。

在 Kubernetes 中，「服務」會按邏輯將 Pod 分組，以允許透過 IP 位址或 DNS 名稱和特定連接埠上的直接存取。 您也可以使用*負載平衡器*來分散流量。 您也可以透過*輸入控制器*執行更複雜的應用程式流量路由。 Pod 網路流量的安全性和篩選可透過 Kubernetes *網路原則*來達成。

Azure 平台也有助於簡化 AKS 叢集的虛擬網路。 當您建立 Kubernetes 負載平衡器時，將會建立並設定基礎的 Azure Load Balancer資源。 當您對 Pod 開啟網路連接埠時，即會設定對應的 Azure 網路安全性群組規則。 對於 HTTP 應用程式路由，Azure 也會將*外部 DNS* 設定為新的輸入路由。

## <a name="services"></a>服務

為了簡化應用程式工作負載的網路設定，Kubernetes 會使用「服務」按邏輯將一組 Pod 分組在一起，並提供網路連線。 以下是可用的服務類型：

- **叢集 IP** - 建立在 AKS 叢集內使用的內部 IP 位址。 這非常適用於支援叢集內其他工作負載的內部專用應用程式。

    ![此圖顯示 AKS 叢集中的叢集 IP 流量][aks-clusterip]

- **NodePort** - 在基礎節點上建立連接埠對應，以便直接透過節點 IP 位址和連接埠存取應用程式。

    ![此圖顯示 AKS 叢集中的 NodePort 流量][aks-nodeport]

- **LoadBalancer** - 建立 Azure Load Balancer 資源、設定外部 IP 位址，並將要求的 Pod 連線至負載平衡器後端集區。 若要允許客戶流量傳至應用程式，可對所需的連接埠建立負載平衡規則。 

    ![此圖顯示 AKS 叢集中的負載平衡器流量][aks-loadbalancer]

    若要進行輸入流量的其他控制和路由，您可以改用[輸入控制器](#ingress-controllers)。

- **ExternalName** - 建立特定的 DNS 項目以方便存取應用程式。

您可以動態指派負載平衡器和服務的 IP 位址，或指定要使用的現有靜態 IP 位址。 內部和外部的靜態 IP 位址都可指派。 這個現有的靜態 IP 位址通常會繫結至 DNS 項目。

您可以指派內部和外部負載平衡器。 對內部負載平衡器只會指派私人 IP 位址，因此無法從網際網路加以存取。

## <a name="azure-virtual-networks"></a>Azure 虛擬網路

在 AKS 中，您可以部署使用下列兩種網路模型之一的叢集：

- *Kubenet* 網路 - 在部署 AKS 叢集時通常會建立並設定網路資源。
- *Azure 容器網路介面 (CNI)* 網路 - AKS 叢集會連線至現有的虛擬網路資源和組態。

### <a name="kubenet-basic-networking"></a>Kubenet (基本) 網路

*Kubenet* 網路選項是建立 AKS 叢集時的預設組態。 使用 *kubenet*，節點會從 Azure 虛擬網路子網路取得 IP 位址。 Pod 會從邏輯上不同的位址空間接收至節點的 Azure 虛擬網路子網路的 IP 位址。 然後設定網路位址轉譯 (NAT)，以便 Pod 可以連線到 Azure 虛擬網路上的資源。 流量的來源 IP 位址會被轉譯為節點的主要 IP 位址。

節點會使用 [kubenet][kubenet] Kubernetes 外掛程式。 您可以讓 Azure 平台為您建立及設定虛擬網路，或選擇部署 AKS 叢集到現有的虛擬網路子網路。 同樣地，只有在節點接收可路由 IP 位址時，Pod 才能使用 NAT 與 AKS 叢集外的其他資源進行通訊。 這種方法可大幅減少您需要在網路空間中保留，以供 Pod 使用的 IP 位址數目。

如需詳細資訊，請參閱[為 AKS 叢集設定 kubenet 網路][aks-configure-kubenet-networking]。

### <a name="azure-cni-advanced-networking"></a>Azure CNI (進階) 網路

使用 Azure CNI，每個 Pod 都從子網路取得 IP 位址，並且可以直接存取。 這些 IP 位址在您的網路空間中必須是唯一的，且必須事先規劃。 每個節點都有一個組態參數，用於所支援的最大 Pod 數目。 然後，為該節點預先保留每個節點的相同 IP 位址數目。 此方法需要更多的規劃，並且通常會導致 IP 位址耗盡，或者隨著應用程式需求增加，需要在更大的子網路中重建叢集。

節點會使用 [Azure 容器網路介面 (CNI)][cni-networking] Kubernetes 外掛程式。

![此圖表顯示兩個節點，且各有橋接器將其連線至單一 Azure VNet][advanced-networking-diagram]

Azure CNI 透過 kubenet 網路提供下列功能：

- 叢集中的每個 Pod 都會被指派虛擬網路中的 IP 位址。 Pod 可直接與叢集中的其他 Pod 以及虛擬網路中的其他節點通訊。
- 在已啟用服務端點的子網路中，Pod 可以安全地連線至 Azure 服務，例如，Azure 儲存體和 SQL DB。
- 您可以建立使用者定義的路由 (UDR)，將流量從 Pod 路由到網路虛擬設備。

如需詳細資訊，請參閱[為 AKS 叢集設定 Azure CNI][aks-configure-advanced-networking]。

## <a name="ingress-controllers"></a>輸入控制器

當您建立 LoadBalancer 類型服務時，將會建立基礎的 Azure Load Balancer 資源。 在您的服務中，負載平衡器會設定為透過指定的連接埠將流量分散到 Pod。 LoadBalancer 僅適用於第 4 層 - 服務無法辨識實際的應用程式，且無法考量任何其他路由方式。

*輸入控制器*會在第 7 層運作，並且可使用更具智慧的規則來分散應用程式流量。 輸入控制器的常見用途是根據輸入 URL 將 HTTP 流量路由到不同的應用程式。

![此圖顯示 AKS 叢集中的輸入流量][aks-ingress]

在 AKS 中，您可以使用 NGINX 之類的功能建立輸入資源，或是使用 AKS HTTP 應用程式路由功能。 當您為 AKS 叢集啟用 HTTP 應用程式路由時，Azure 平台會建立輸入控制器和 *External-DNS* 控制器。 在 Kubernetes 中建立新的輸入資源時，會在叢集特定的 DNS 區域中建立所需的 DNS A 記錄。 如需詳細資訊，請參閱[部署 HTTP 應用程式路由][aks-http-routing]。

輸入的另一個常見功能是終止 SSL/TLS。 在透過 HTTPS 存取的大型 Web 應用程式上，可由輸入資源來處理 TLS 終止，而無須由應用程式本身處理。 若要提供自動 TLS 憑證產生和設定的功能，您可以將輸入資源設定為使用 Let's Encrypt 之類的資源提供者。 如需為 NGINX 輸入控制器設定 Let's Encrypt 的詳細資訊，請參閱[輸入和 TLS][aks-ingress-tls]。

## <a name="network-security-groups"></a>網路安全性群組

網路安全性群組可篩選 VM 的流量，例如 AKS 節點。 當您建立服務 (例如 LoadBalancer) 時，Azure 平台會自動設定任何所需的網路安全性群組規則。 請勿以手動方式設定對 AKS 叢集中的 Pod 進行流量篩選的網路安全性群組規則。 請將任何必要的連接埠和轉送定義為 Kubernetes 服務資訊清單的一部分，然後由 Azure 平台建立或更新適當的規則。 您也可以使用網路原則 (如下一節所述)，自動將流量篩選規則套用至 Pod。

SSH 之類的流量有預設的網路安全性群組規則。 這些預設規則適用於叢集管理和存取的疑難排解。 刪除這些預設規則可能會導致 AKS 管理的問題，並且會中斷服務等級目標 (SLO)。

## <a name="network-policies"></a>網路原則

根據預設，AKS 叢集內的所有 Pod 都可無限制地傳送及接收流量。 為了提升安全性，您可以定義控制流量的規則。 後端應用程式通常只會對必要的前端服務公開，或是資料庫元件僅供與其連線的應用程式層存取。

網路原則是一種 Kubernetes 功能，可讓您控制 Pod 之間的流量。 您可以根據指派的標籤、命名空間或流量連接埠等設定，選擇允許或拒絕流量。 網路安全性群組較適用於 AKS 節點而非 Pod。 使用網路原則是一種控制流量的較合適且雲端原生的方式。 由於 Pod 是在 AKS 叢集內以動態方式建立的，因此可以自動套用所需的網路原則。

如需詳細資訊，請參閱[使用 Azure Kubernetes Service (AKS) 中的網路原則來保護 Pod 之間的流量][use-network-policies] \(英文\)。

## <a name="next-steps"></a>後續步驟

若要開始使用 AKS 網路功能，請使用 [kubenet][aks-configure-kubenet-networking] 或 [Azure CNI][aks-configure-advanced-networking] 以您自己的 IP 位址範圍建立及設定 AKS 叢集。

如需關於 Kubernetes 及 AKS 核心概念的詳細資訊，請參閱下列文章：

- [Kubernetes / AKS 叢集和工作負載][aks-concepts-clusters-workloads]
- [Kubernetes / AKS 存取和身分識別][aks-concepts-identity]
- [Kubernetes / AKS 安全性][aks-concepts-security]
- [Kubernetes / AKS 儲存體][aks-concepts-storage]
- [Kubernetes / AKS 調整][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
