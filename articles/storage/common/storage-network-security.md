---
title: 設定 Azure 儲存體防火牆和虛擬網路 | Microsoft Docs
description: 為儲存體帳戶設定多層式的網路安全性。
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 10/30/2018
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: ce682263663c0547d9e35a4d29037fd125d8bc6c
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699171"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>設定 Azure 儲存體防火牆和虛擬網路

Azure 儲存體提供分層的安全性模型。 此模型可讓您設定一組特定的支援網路來保護儲存體帳戶。 設定網路規則時，只有透過一組特定網路發出要求資料的應用程式可以存取儲存體帳戶。

應用程式若在網路規則生效時存取儲存體帳戶，則要求上必須有適當的授權。 支援使用 Azure Active Directory (AD) 認證 (適用於 Blob 和佇列) (預覽)、有效的帳戶存取金鑰或 SAS 權杖來進行授權。

> [!IMPORTANT]
> 開啟儲存體帳戶的防火牆規則會預設封鎖傳入的資料要求，除非要求來自 Azure 虛擬網路 (VNet) 內運作的服務。 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。
>
> 您可以透過允許服務執行個體的子網路，將存取權授與從 VNet 內執行的 Azure 服務。 讓有限的案例可以透過下面章節所述的[例外](#exceptions)機制來啟用。 若要存取 Azure 入口網站，您所用的機器必須位於已設定的信任界限 (IP 或 VNet) 內。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>案例

根據預設，您可設定儲存體帳戶來拒絕存取所有網路流量 (包括網際網路流量)。 然後對來自特定 VNet 的流量授與存取權。 此設定可讓您為應用程式設定安全的網路界限。 您也可以授與存取公用網際網路 IP 位址範圍的權限，啟用來自特定網際網路或內部部署用戶端的連線。

所有的 Azure 儲存體網路通訊協定都會執行網路規則，包括 REST 和 SMB。 若要使用 Azure 入口網站、儲存體總管和 AZCopy 等工具來存取資料，則需要明確的網路規則。

您可以將網路規則套用到現有的儲存體帳戶，或在您建立新的儲存體帳戶時套用。

一旦套用網路規則，就會對所有要求執行。 授與特定 IP 位址存取權的 SAS 權杖，是用來限制權杖持有者的存取權，但不會授與所設定網路規則以外的新存取權。

虛擬機器磁碟流量 (包括掛接和取消掛接作業以及磁碟 IO) 不受網路規則影響。 分頁 Blob 的 REST存取受網路規則保護。

傳統的儲存體帳戶不支援防火牆與虛擬網路。

您可以藉由建立例外狀況，使用儲存體帳戶中的非受控磁碟與已套用的網路規則來備份和還原 VM。 此程序記載於本文的[例外狀況](#exceptions)一節。 防火牆例外不適用於受控磁碟，因為它們已受 Azure 管理。

## <a name="change-the-default-network-access-rule"></a>變更預設的網路存取規則

儲存體帳戶預設接受來自任何網路用戶端的連線。 若要限制對所選網路的存取，您必須先變更預設動作。

> [!WARNING]
> 變更網路規則會影響應用程式連接到 Azure 儲存體的能力。 將預設的網路規則設定為**拒絕**以封鎖所有的資料存取，除非也套用了特定網路規則來**授與**存取權。 請務必先將存取權授與任何使用網路規則的允許網路，再變更預設規則以拒絕存取。

### <a name="managing-default-network-access-rules"></a>管理預設的網路存取規則

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理儲存體帳戶的預設網路存取規則。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路] 的設定功能表。

1. 若要預設拒絕存取，請選擇允許**所選網路**存取權。 若要允許來自所有網路的流量，請選擇允許**所有網路**存取權。

1. 按一下 [儲存] 套用變更。

#### <a name="powershell"></a>PowerShell

1. 安裝 [Azure PowerShell](/powershell/azure/install-Az-ps) 並[登入](/powershell/azure/authenticate-azureps)。

1. 顯示儲存體帳戶的預設規則狀態。

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. 根據預設，將預設規則設定為拒絕網路存取。

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. 根據預設，將預設規則設定為允許網路存取。

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並[登入](/cli/azure/authenticate-azure-cli)。

1. 顯示儲存體帳戶的預設規則狀態。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. 根據預設，將預設規則設定為拒絕網路存取。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. 根據預設，將預設規則設定為允許網路存取。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>授與虛擬網路存取權

您可以將儲存體帳戶設定為只允許從特定 Vnet 進行存取。

為 VNet 內的 Azure 儲存體啟用[服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)。 此端點為流量提供一個到 Azure 儲存體服務的最佳路由。 虛擬網路和子網路的身分識別也會隨著每項要求傳輸。 系統管理員接著可以設定儲存體帳戶的網路規則，允許接收來自 VNet 中特定子網路的要求。 透過這些網路規則授與存取的用戶端，必須仍要繼續符合儲存體帳戶的授權需求，才能存取資料。

每個儲存體帳戶最多可支援 100 個虛擬網路規則，它們也可以結合 [IP 網路規則](#grant-access-from-an-internet-ip-range)。

### <a name="available-virtual-network-regions"></a>可用的虛擬網路區域

一般情況下，服務端點是在虛擬網路與相同 Azure 區域的服務執行個體之間運作。 當搭配 Azure 儲存體使用服務端點時，此範圍會擴充以包含[配對的區域](/azure/best-practices-availability-paired-regions)。 服務端點可在地區性容錯移轉期間維持持續性，而且能讓您存取唯讀的異地備援儲存體 (RA-GRS) 執行個體。 將虛擬網路存取權授與儲存體帳戶的網路規則，也會將存取權授與任何 RA-GRS 執行個體。

規劃地區服務中斷期間的災害復原時，應該事先在配對區域中建立 VNet。 為 Azure 儲存體啟用服務端點，並設定網路規則，以允許從這些替代虛擬網路進行存取。 然後將這些規則套用至您的異地備援儲存體帳戶。

> [!NOTE]
> 服務端點不適用於虛擬網路區域外和指定配對區域外的流量。 允許從虛擬網路進行存取的網路規則，只能套用到儲存體帳戶主要區域中的儲存體帳戶或指定配對區域中的儲存體帳戶。

### <a name="required-permissions"></a>所需的權限

為將虛擬網路規則套用至儲存體帳戶，使用者必須在要新增的子網路上具有適當權限。 所需的權限為「將服務加入子網路」，以及加入「儲存體帳戶參與者」內建角色。 這也可以新增至自訂角色定義。

儲存體帳戶和虛擬網路授與的存取權可能在不同的訂用帳戶，但這些訂用帳戶必須屬於相同的 Azure AD 租用戶。

### <a name="managing-virtual-network-rules"></a>管理虛擬網路規則

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理儲存體帳戶的虛擬網路規則。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路] 的設定功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 若要使用新網路規則將存取權授與虛擬網路，請按一下 [虛擬網路] 下的 [新增現有的虛擬網路]，選取 [虛擬網路] 和 [子網路] 選項，然後按一下 [新增]。 若要建立新的虛擬網路並授與存取權，請按一下 [新增新的虛擬網路]。 提供建立新虛擬網路所需的資訊，並按一下 [建立]。

    > [!NOTE]
    > 如果 Azure 儲存體的服務端點未針對所選虛擬網路和子網路事先設定，可以將其設定為這項作業的一部分。

1. 若要移除虛擬網路或子網路規則，請按一下 […] 開啟虛擬網路或子網路的快顯功能表，然後按一下 [移除]。

1. 按一下 [儲存] 套用變更。

#### <a name="powershell"></a>PowerShell

1. 安裝 [Azure PowerShell](/powershell/azure/install-Az-ps) 並[登入](/powershell/azure/authenticate-azureps)。

1. 列出虛擬網路規則。

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. 啟用現有虛擬網路和子網路上的 Azure 儲存體服務端點。

    ```PowerShell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. 為虛擬網路和子網路新增網路規則。

    ```PowerShell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. 移除虛擬網路和子網路的網路規則。

    ```PowerShell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]，否則網路規則不會生效。

#### <a name="cliv2"></a>CLIv2

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並[登入](/cli/azure/authenticate-azure-cli)。

1. 列出虛擬網路規則。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. 啟用現有虛擬網路和子網路上的 Azure 儲存體服務端點。

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. 為虛擬網路和子網路新增網路規則。

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. 移除虛擬網路和子網路的網路規則。

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]，否則網路規則不會生效。

## <a name="grant-access-from-an-internet-ip-range"></a>授與網際網路 IP 範圍存取權

您可以將儲存體帳戶設定為允許從特定公用網際網路 IP 位址範圍進行存取。 此設定可將存取權授與特定的網際網路型服務和內部部署網路，並且封鎖一般網際網路流量。

使用 [CIDR 標記法](https://tools.ietf.org/html/rfc4632)以 16.17.18.0/24 的格式，或 16.17.18.19 一類的個別 IP 位址，提供允許的網際網路位址範圍。

   > [!NOTE]
   > 不支援使用 "/31" 或 "/32" 前置詞大小的小型位址範圍。 這些範圍應該使用個別的 IP 位址規則設定。

只有**公用網際網路** IP 位址允許使用 IP 網路規則。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) 中所定義)。 私人網路包括以 _10.*_、_172.16.*_ - _172.31.*_ 和 _192.168.*_ 開頭的位址。

   > [!NOTE]
   > IP 網路規則不會影響源自儲存體帳戶所在 Azure 區域的要求。 使用[虛擬網路規則](#grant-access-from-a-virtual-network)來允許同一個區域的要求。

目前僅支援 IPV4 位址。

每個儲存體帳戶最多可支援 100 個 IP 網路規則，它們也可以結合[虛擬網路規則](#grant-access-from-a-virtual-network)。

### <a name="configuring-access-from-on-premises-networks"></a>設定內部部署網路存取權

為將內部部署網路存取權授與使用 IP 網路規則的儲存體帳戶，您必須識別網路所使用的網際網路對應 IP 位址。 請連絡網路系統管理員尋求協助。

您可以使用 [ExpressRoute](/azure/expressroute/expressroute-introduction) 將您的網路連線到 Azure 網路。 在這裡，每個線路上會設定兩個公用 IP 位址。 這些位址可以在 Microsoft Edge 中找到，並可使用 [Azure 公用對等互連](/azure/expressroute/expressroute-circuit-peerings)來連線到 Azure 儲存體等 Microsoft 服務。 若要使用 Azure 儲存體通訊，請為電路的公用 IP 位址建立 IP 網路規則。 若要尋找您 ExpressRoute 線路的公用 IP 位址，請透過 Azure 入口網站[開啟具有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

### <a name="managing-ip-network-rules"></a>管理 IP 網路規則

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理儲存體帳戶的 IP 網路規則。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路] 的設定功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 若要授與網際網路 IP 範圍的存取權，請在 [防火牆] > [位址範圍] 之下輸入 IP 位址或位址範圍 (採用 CIDR 格式)。

1. 若要移除 IP 網路規則，請按一下位址範圍旁的垃圾桶圖示。

1. 按一下 [儲存] 套用變更。

#### <a name="powershell"></a>PowerShell

1. 安裝 [Azure PowerShell](/powershell/azure/install-Az-ps) 並[登入](/powershell/azure/authenticate-azureps)。

1. 列出 IP 網路規則。

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. 新增個別 IP 位址的網路規則。

    ```PowerShell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. 新增 IP 位址範圍的網路規則。

    ```PowerShell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. 移除個別 IP 位址的網路規則。

    ```PowerShell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. 移除 IP 位址範圍的網路規則。

    ```PowerShell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]，否則網路規則不會生效。

#### <a name="cliv2"></a>CLIv2

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並[登入](/cli/azure/authenticate-azure-cli)。

1. 列出 IP 網路規則。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. 新增個別 IP 位址的網路規則。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. 新增 IP 位址範圍的網路規則。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. 移除個別 IP 位址的網路規則。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. 移除 IP 位址範圍的網路規則。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]，否則網路規則不會生效。

## <a name="exceptions"></a>例外狀況

網路規則可以為大部分情況啟用安全的網路設定。 不過，在某些情況下，必須授與例外狀況才能啟用完整的功能。 您可以對儲存體帳戶設定例外狀況，以允許受信任的 Microsoft 服務進行存取，以及存取儲存體分析資料。

### <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服務

有些與儲存體帳戶互動的 Microsoft 服務是從無法透過網路規則授與存取權的網路運作。

若要讓這類服務如預期方式運作，請允許受信任的 Microsoft 服務集合略過網路規則。 這些服務會使用增強式驗證存取儲存體帳戶。

如果啟用 **「允許信任的 Microsoft 服務...」** 例外狀況，下列服務 (在您的訂用帳戶中註冊時) 會獲得存取儲存體帳戶的權限：

|服務|資源提供者名稱|目的|
|:------|:---------------------|:------|
|Azure 備份|Microsoft.Backup|在 IAAS 虛擬機器中執行未受控磁碟備份與還原。 (若為受控磁碟則非必要)。 [深入了解](/azure/backup/backup-introduction-to-azure-backup)。|
|Azure Site Recovery|Microsoft.SiteRecovery |藉由啟用 Azure IaaS 虛擬機器的複寫來設定災害復原。 如果您使用已啟用防火牆的快取儲存體帳戶、來源儲存體帳戶或目標儲存體帳戶，則這會是必要項目。  [深入了解](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)。|
|Azure DevTest Labs|Microsoft.DevTestLab|自訂映像建立和成品安裝。 [深入了解](/azure/devtest-lab/devtest-lab-overview)。|
|Azure Event Grid|Microsoft.EventGrid|啟用 Blob 儲存體事件發佈，並允許事件方格發佈到儲存體佇列。 深入了解 [Blob 儲存體事件](/azure/event-grid/event-sources)及[發佈至佇列](/azure/event-grid/event-handlers)。|
|Azure 事件中心|Microsoft.EventHub|使用事件中樞擷取封存資料。 [深入了解](/azure/event-hubs/event-hubs-capture-overview)。|
|Azure 網路|Microsoft.Networking|儲存及分析網路流量記錄檔。 [深入了解](/azure/network-watcher/network-watcher-packet-capture-overview)。|
|Azure 監視器|Microsoft.Insights|允許將監視資料寫入受保護的儲存體帳戶 [深入了解](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)。|
|Azure SQL 資料倉儲|Microsoft.Sql|允許使用 PolyBase 匯入和匯出案例。 [深入了解](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)。|

### <a name="storage-analytics-data-access"></a>儲存體分析資料存取

在某些情況下，需要來自網路界限外的存取權才能讀取診斷記錄檔和計量。 您可以授與網路規則的例外狀況，允許讀取存取儲存體帳戶記錄檔、計量資料表，或兩者都存取。 [深入了解儲存體分析的使用方式。](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>管理例外狀況

您可以透過 Azure 入口網站、PowerShell 或 CLIv2 管理網路規則例外狀況。

#### <a name="azure-portal"></a>Azure 入口網站

1. 移至您要保護的儲存體帳戶。

1. 按一下名為 [防火牆與虛擬網路] 的設定功能表。

1. 請確定您已選取允許從**所選網路**進行存取。

1. 在 [例外狀況] 下選取您希望授與權限的例外狀況。

1. 按一下 [儲存] 套用變更。

#### <a name="powershell"></a>PowerShell

1. 安裝 [Azure PowerShell](/powershell/azure/install-Az-ps) 並[登入](/powershell/azure/authenticate-azureps)。

1. 顯示儲存體帳戶網路規則的例外狀況。

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. 設定儲存體帳戶網路規則的例外狀況。

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. 移除儲存體帳戶網路規則的例外狀況。

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]，否則移除例外狀況不會生效。

#### <a name="cliv2"></a>CLIv2

1. 安裝 [Azure CLI](/cli/azure/install-azure-cli) 並[登入](/cli/azure/authenticate-azure-cli)。

1. 顯示儲存體帳戶網路規則的例外狀況。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. 設定儲存體帳戶網路規則的例外狀況。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. 移除儲存體帳戶網路規則的例外狀況。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> 請務必將[預設規則設定](#change-the-default-network-access-rule)為 [拒絕]，否則移除例外狀況不會生效。

## <a name="next-steps"></a>後續步驟

深入了解[服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview) 中的 Azure 網路服務端點。

在 [Azure 儲存體安全性指南](storage-security-guide.md)中深入了解 Azure 儲存體安全性。
