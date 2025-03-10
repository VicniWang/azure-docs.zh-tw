---
title: 使用 Azure 入口網站設定 VNet 對 VNet 的 VPN 閘道連線 | Microsoft Docs
description: 使用 Resource Manager 和 Azure 入口網站建立 VNet 間的 VPN 閘道連接。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 0646488c3dde4b0702d58bbd8905f4ae6bee1485
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821660"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>使用 Azure 入口網站設定 VNet 對 VNet 的 VPN 閘道連線

本文協助您使用 VNet 對 VNet 連線類型來連線虛擬網路 (VNet)。 VNet 可位於不同的區域且來自不同的訂用帳戶。 當您連線來自不同訂用帳戶的 VNet 時，訂用帳戶不需與相同的 Active Directory 租用戶相關聯。 

![v2v 圖表](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

本文中的步驟適用於 Azure Resource Manager 部署模型並使用 Azure 入口網站。 您可以使用下列文章中所述的選項，透過不同的部署工具或模型來建立這個組態：

> [!div class="op_single_selector"]
> * [Azure 入口網站](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure 入口網站 (傳統)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [連線不同的部署模型 - Azure 入口網站](vpn-gateway-connect-different-deployment-models-portal.md)
> * [連線不同的部署模型 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>


## <a name="about-connecting-vnets"></a>關於連線 VNet

下列各節說明不同的虛擬網路連線方式。

### <a name="vnet-to-vnet"></a>VNet 對 VNet

設定 VNet 對 VNet 連線是輕鬆連線 VNet 的方法。 當您透過 VNet 對 VNet 連線類型 (VNet2VNet) 將虛擬網路連線到另一個虛擬網路時，類似於建立內部部署位置的站對站 IPsec 連線。 這兩種連線類型都使用 VPN 閘道來提供採用 IPsec/IKE 的安全通道，而且在通訊時的運作方式相同。 不過，其差異在於區域網路閘道的設定方式。 

當您建立 VNet 對 VNet 連線時，系統會自動建立和填入區域網路閘道位址空間。 如果您更新一個 VNet 的位址空間，另一個 VNet 就會自動路由到已更新的位址空間。 相較於站對站連線，建立 VNet 對 VNet 連線通常比較快速而容易。

### <a name="site-to-site-ipsec"></a>站對站 (IPsec)

如果您使用複雜的網路組態，您可能偏好使用[站對站連線](vpn-gateway-howto-site-to-site-resource-manager-portal.md)來連線 VNet。 當您遵循站對站 IPsec 步驟時，您會以手動方式建立及設定區域網路閘道。 每個 VNet 的區域網路閘道都會將其他 VNet 視為本機網站。 這些步驟可讓您為區域網路閘道指定其他位址空間，以便路由傳送流量。 如果 VNet 的位址空間變更，您必須手動更新對應的區域網路閘道。

### <a name="vnet-peering"></a>VNet 對等互連

您也可以使用 VNet 對等互連來連線 VNet。 VNet 對等互連不會使用 VPN 閘道，且具有不同的條件約束。 此外，[VNet 對等互連價格](https://azure.microsoft.com/pricing/details/virtual-network)與 [VNet 對 VNet VPN 閘道價格](https://azure.microsoft.com/pricing/details/vpn-gateway)的計算方式不同。 如需詳細資訊，請參閱 [VNet 對等互連](../virtual-network/virtual-network-peering-overview.md)。

## <a name="why-create-a-vnet-to-vnet-connection"></a>為何要建立 VNet 對 VNet 連線？

基於下列原因，建議您使用 VNet 對 VNet 連線來進行虛擬網路連線：

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>跨區域的異地備援和異地目前狀態

  * 您可以使用安全連線設定自己的異地複寫或同步處理，而不用查看網際網路對向端點。
  * 您可以使用 Azure 流量管理員和 Azure Load Balancer，利用異地備援跨多個 Azure 區域設定高度可用的工作負載。 例如，您可以設定分散於多個 Azure 區域的 SQL Server Always On 可用性群組。

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>具有隔離或管理界限的區域性多層式應用程式

  * 在相同區域中，您可以因為隔離或管理需求，設定將多層式應用程式與多個虛擬網路連線在一起。

您可以將 VNet 對 VNet 通訊與多站台組態結合。 這些組態可讓您建立使用內部虛擬網路連線結合跨單位連線的網路拓撲，如下圖所示：

![關於連接](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "關於連接")

本文說明如何使用 VNet 對 VNet 連線類型來連線 VNet。 練習這些步驟時，您可以使用下列範例設定值。 在範例中，虛擬網路位於相同的訂用帳戶，但在不同的資源群組。 如果您的 Vnet 位於不同的訂用帳戶中，就無法在入口網站中建立連線。 改用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 或 [CLI](vpn-gateway-howto-vnet-vnet-cli.md)。 如需 VNet 對 VNet 連線的詳細資訊，請參閱 [VNet 對 VNet 常見問題集](#vnet-to-vnet-faq)。

### <a name="example-settings"></a>範例設定

**TestVNet1 的值︰**

- **虛擬網路設定**
    - **名稱**：輸入 TestVNet1。
    - **位址空間**：輸入 10.11.0.0/16。
    - 訂用帳戶：選取您要使用的訂用帳戶。
    - **資源群組**：輸入 TestRG1。
    - **位置**：選取 [美國東部]。
    - **子網路**
        - **名稱**：輸入 FrontEnd。
        - **位址範圍**：輸入 10.11.0.0/24。
    - **閘道器子網路**：
        - **名稱**：自動填入 GatewaySubnet。
        - **位址範圍**：輸入 10.11.255.0/27。
    - **DNS 伺服器**：選取 [自訂]，並輸入您 DNS 伺服器的 IP 位址。

- **虛擬網路閘道設定** 
    - **名稱**：輸入 TestVNet1GW。
    - **閘道類型**：選取 [VPN]。
    - **VPN 類型**：選取 [依路由]。
    - **SKU**：選取您想要使用的閘道 SKU。
    - **公用 IP 位址名稱**：輸入 TestVNet1GWIP。
    - **連接** 
       - **名稱**：輸入 TestVNet1toTestVNet4。
       - **共用金鑰**：輸入 abc123。 您可以自行建立共用金鑰。 當您建立 VNet 之間的連線時，值必須相符。

**TestVNet4 的值︰**

- **虛擬網路設定**
   - **名稱**：輸入 TestVNet4。
   - **位址空間**：輸入 10.41.0.0/16。
   - 訂用帳戶：選取您要使用的訂用帳戶。
   - **資源群組**：輸入 TestRG4。
   - **位置**：選取 [美國西部]。
   - **子網路** 
      - **名稱**：輸入 FrontEnd。
      - **位址範圍**：輸入 10.41.0.0/24。
   - **GatewaySubnet** 
      - **名稱**：自動填入 GatewaySubnet。
      - **位址範圍**：輸入 10.41.255.0/27。
   - **DNS 伺服器**：選取 [自訂]，並輸入您 DNS 伺服器的 IP 位址。

- **虛擬網路閘道設定** 
    - **名稱**：輸入 TestVNet4GW。
    - **閘道類型**：選取 [VPN]。
    - **VPN 類型**：選取 [依路由]。
    - **SKU**：選取您想要使用的閘道 SKU。
    - **公用 IP 位址名稱**：輸入 TestVNet4GW。
    - **連接** 
       - **名稱**：輸入 TestVNet4toTestVNet1。
       - **共用金鑰**：輸入 abc123。 您可以自行建立共用金鑰。 當您建立 VNet 之間的連線時，值必須相符。

## <a name="create-and-configure-testvnet1"></a>建立及設定 TestVNet1
如果您已經有 VNet，請驗證設定是否與您的 VPN 閘道設計相容。 請特別注意任何可能與其他網路重疊的子網路。 如果有重疊的子網路，您的連線便無法正常運作。 您的 VNet 已設定為正確的設定後，即可開始執行「指定 DNS 伺服器」一節中的步驟。

### <a name="to-create-a-virtual-network"></a>建立虛擬網路
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>新增其他位址空間和建立子網路
建立 VNet 之後，您可以新增其他位址空間和建立子網路。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-gateway-subnet"></a>建立閘道子網路
建立虛擬網路的虛擬網路閘道之前，您必須先建立閘道子網路。 閘道子網路包含虛擬網路閘道所使用的 IP 位址。 可能的話，最好使用 /28 或 /27 的 CIDR 區塊來建立閘道子網路，以便提供足以容納未來其他組態需求的 IP 位址。

如果您要練習建立此組態，請在建立閘道子網路時參考這些[範例值](#example-settings)。

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>建立閘道子網路
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="specify-a-dns-server-optional"></a>指定 DNS 伺服器 (選擇性)
VNet 對 VNet 連線不需要 DNS。 不過，如果您想要對部署至虛擬網路的資源進行名稱解析，請指定 DNS 伺服器。 此設定可讓您指定要用於此虛擬網路之名稱解析的 DNS 伺服器服務。 它不會建立 DNS 伺服器。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="create-a-virtual-network-gateway"></a>建立虛擬網路閘道
此步驟將帶您建立 VNet 的虛擬網路閘道。 建立閘道通常可能需要 45 分鐘或更久，視選取的閘道 SKU 而定。 如果您要練習建立此組態，請參閱[範例設定](#example-settings)。

### <a name="to-create-a-virtual-network-gateway"></a>建立虛擬網路閘道
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="create-and-configure-testvnet4"></a>建立及設定 TestVNet4
在設定 TestVNet1 之後，請重複先前步驟並換成 TestVNet4 的值，以建立 TestVNet4。 您不需要等到 TestVNet1 的虛擬網路閘道建立完成後才設定 TestVNet4。 如果您使用自己的值，請確定位址空間沒有與任何您想要連線的 VNet 重疊。

## <a name="configure-the-testvnet1-gateway-connection"></a>設定 TestVNet1 閘道連線
當 TestVNet1 和 TestVNet4 的虛擬網路閘道完成後，您可以建立虛擬網路閘道連接。 在本節中，您要建立從 VNet1 到 VNet4 的連線。 這些步驟只適用於相同的訂用帳戶中的 VNet。 如果您的 VNet 位於不同的訂用帳戶中，則必須使用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 來進行連線。 不過，如果 Vnet 位於相同訂用帳戶中的不同資源群組，您可以使用入口網站將它們連線。

1. 在 Azure 入口網站中，選取 [所有資源]，在搜尋方塊中輸入「虛擬網路閘道」，然後瀏覽至您 VNet 的虛擬網路閘道。 例如，**TestVNet1GW**。 選取它來開啟 [虛擬網路閘道] 頁面。

  ![連線頁面](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "連線頁面")
2. 在 [設定] 之下，選取 [連線]，然後選取 [新增] 以開啟 [新增連線] 頁面。

  ![新增連接](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "新增連接")
3. 在 [新增連線] 頁面上，填入您的連線值：

   - **名稱**：輸入您的連線名稱。 例如，*TestVNet1toTestVNet4*。

   - **連線類型**：從下拉式清單中選取 [VNet 對 VNet]。

   - **第一個虛擬網路閘道**：因為您是從指定的虛擬網路閘道建立此連線，所以會自動填入這個欄位值。

   - **第二個虛擬網路閘道**：這個欄位是您想要建立連線的 VNet 的虛擬網路閘道。 選取 [選擇另一個虛擬網路閘道]，以開啟 [選擇虛擬網路閘道] 頁面。

    - 檢視此頁面上列出的虛擬網路閘道。 請注意，只會列出您的訂用帳戶中的虛擬網路閘道。 如果想要連線的虛擬網路閘道不在您的訂用帳戶中，請使用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)。

    - 選取您想要連線的虛擬網路閘道。

    - **共用金鑰 (PSK)**：在此欄位中，輸入您連線的共用金鑰。 您可以產生此金鑰，或自行建立此金鑰。 在站對站連線中，您用於內部部署裝置與虛擬網路閘道連線的金鑰完全相同。 此處的概念類似，差別在於是連線到另一個虛擬網路閘道，而不是連線到 VPN 裝置。
    
4. 選取 [ **確定** ] 以儲存變更。

## <a name="configure-the-testvnet4-gateway-connection"></a>設定 TestVNet4 閘道連線
接下來，建立從 TestVNet4 至 TestVNet1 的連接。 在入口網站中，找出與 TestVNet4 相關聯的虛擬網路閘道。 遵循上一節中的步驟，取代建立 TestVNet4 到 TestVNet1 連線的值。 請確定您使用相同的共用金鑰。

## <a name="verify-your-connections"></a>確認您的連線

在 Azure 入口網站中找出虛擬網路閘道。 在 [虛擬網路閘道] 頁面上，選取 [連線]，以檢視虛擬網路閘道的 [連線] 頁面。 建立連線後，您會看到 [狀態] 值變更為 [成功] 和 [已連線]。 選取連線來開啟 [基本資訊] 頁面，並檢視的詳細資訊。

![已成功](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "已成功")

當資料開始流動時，您會看到 [資料輸入] 和 [資料輸出] 的值。

![程式集](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "程式集")

## <a name="add-additional-connections"></a>新增其他連線

如果您需要新增其他連線，請瀏覽至您想建立連線的虛擬網路閘道，然後選取 [連線]。 您可以建立另一個 VNet 對 VNet 連線，或建立 IPsec 站對站連線到內部部署位置。 請務必調整 [連線類型] 來符合您需要建立的連線類型。 建立其他連線之前，請確認您虛擬網路的位址空間與任何您需要連線的位址空間不重疊。 如需建立站對站連線的步驟，請參閱[建立站對站連線](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

## <a name="vnet-to-vnet-faq"></a>VNet 對 VNet 常見問題集
檢視常見問題集詳細資料以取得 VNet 對 VNet 連線的其他資訊。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>後續步驟

如需如何在虛擬網路中限制資源網路流量的資訊，請參閱[網路安全性](../virtual-network/security-overview.md)。

如需 Azure 如何在 Azure、內部部署和網際網路資源間路由流量的資訊，請參閱[虛擬網路流量路由](../virtual-network/virtual-networks-udr-overview.md)。
