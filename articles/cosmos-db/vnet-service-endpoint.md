---
title: 使用 Azure 虛擬網路服務端點保護 Azure Cosmos DB 帳戶的存取權
description: 本文件說明關於 Azure Cosmos 帳戶的虛擬網路和子網路存取控制。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 199e1dda3e4629c0298d4aae1cb5d09e20e4b3b8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452035"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>從虛擬網路 (VNet) 存取 Azure Cosmos DB

您可以設定 Azure Cosmos 帳戶，以只允許從虛擬網路 (VNet) 的特定子網路存取。 藉由啟用[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)，即可存取虛擬網路內子網路中的 Azure Cosmos DB，來自該子網路的流量會傳送至 Azure Cosmos DB，並具備子網路和虛擬網路的身分識別。 Azure Cosmos DB 服務端點啟用之後，可以將它新增至您的 Azure Cosmos 帳戶以限制對子網路的存取。

根據預設，如果要求伴隨有效的授權權杖，就可以從任何來源存取 Azure Cosmos 帳戶。 當您在 VNet 內新增一或多個子網路時，只有來自那些子網路的要求會取得有效回應。 來自任何其他來源的要求將會收到 403 (禁止) 回應。 

## <a name="frequently-asked-questions"></a>常見問題集

以下是與從虛擬網路設定存取權的一些常見問答集：

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>是否可以在 Azure Cosmos 帳戶中同時指定虛擬網路服務端點和 IP 存取控制原則？ 

您可以在您的 Azure Cosmos 帳戶中同時啟用虛擬網路服務端點和 IP 存取控制原則 (也稱為防火牆)。 這兩個功能彼此互補，且可共同確保 Azure Cosmos 帳戶的隔離與安全性。 使用 IP 防火牆可確保靜態 IP 可以存取您的帳戶。 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>如何限制對虛擬網路內子網路的存取？ 

若要從子網路存取 Azure Cosmos 帳戶，必須執行兩個步驟。 首先，您要允許子網路流量將其子網路和虛擬網路身分識別攜帶至 Azure Cosmos DB。 您可以在子網路上啟用 Azure Cosmos DB 的服務端點來達到這個目的。 接下來要在 Azure Cosmos 帳戶中新增規則，將這個子網路指定為可存取帳戶的來源。

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>虛擬網路 ACL 與 IP 防火牆會拒絕要求或連線嗎？ 

在新增了 IP 防火牆或虛擬網路存取規則時，只有來自已允許來源的要求能取得有效回應。 系統會拒絕其他要求，並顯示 403 (禁止)。 請務必區別 Azure Cosmos 帳戶的防火牆和連線層級防火牆。 來源仍可連線至服務，且連線本身不會被拒絕。

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>當我在子網路上啟用 Azure Cosmos DB 的服務端點時，我的要求開始遭到封鎖。 發生什麼情形？

在子網路上啟用 Azure Cosmos DB 的服務端點後，送到帳戶的流量來源就會從公用 IP 切換至虛擬網路和子網路。 如果您的 Azure Cosmos 帳戶只有 IP 型防火牆，來自已啟用服務之子網路的流量將不再符合 IP 防火牆規則，因此會遭到拒絕。 請執行步驟以順暢地從 IP 型防火牆移轉至虛擬網路型存取控制。

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>對等互連的虛擬網路也可以存取 Azure Cosmos 帳戶嗎？ 
只有新增至 Azure Cosmos 帳戶的虛擬網路和其子網路可以存取。 只有將對等互連之虛擬網路內的子網路新增到帳戶之後，其對等互連的 VNet 才能存取帳戶。

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>允取存取單一 Cosmos 帳戶的子網路數目上限為何？ 
目前，您最多可以允許 64 個子網路存取一個 Azure Cosmos 帳戶。

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>可以啟用來自 VPN 和 Express Route 的存取嗎？ 
對於透過 Express Route 從內部部署存取 Azure Cosmos 帳戶，您需要啟用 Microsoft 對等互連。 一旦制訂 IP 防火牆或虛擬網路存取規則，就可以新增公用 IP 位址 (用於您 Azure Cosmos 帳戶 IP 防火牆上的 Microsoft 對等互連)，以允許內部部署服務存取 Azure Cosmos 帳戶。 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>我需要更新網路安全性群組 (NSG) 規則嗎？ 
NSG 規則可用來限制子網路和虛擬網路的往返連線。 當您將 Azure Cosmos DB 的服務端點新增至子網路時，不需要在 NSG 中針對您的 Azure Cosmos 帳戶開啟輸出連線。 

### <a name="are-service-endpoints-available-for-all-vnets"></a>服務端點是否可供所有 VNet 使用？
否，只有 Azure Resource Manager 虛擬網路可以啟用服務端點。 傳統虛擬網路不支援服務端點。


## <a name="next-steps"></a>後續步驟

* [如何限制 Azure Cosmos 帳戶對虛擬網路內子網路的存取](how-to-configure-vnet-service-endpoint.md)
* [如何設定 Azure Cosmos 帳戶的 IP 防火牆](how-to-configure-firewall.md)

