---
title: 設定 ExpressRoute Global Reach：Azure CLI | Microsoft Docs
description: 本文可協助您將 ExpressRoute 線路連結在一起，在內部部署網路之間產生私人網路，並使觸角擴及全球。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: be10489d731b9e01d148ce1ac7892cb6de956662
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659315"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli-preview"></a>使用 Azure CLI (預覽) 設定 ExpressRoute Global Reach

本文可協助您使用 Azure CLI 設定 Azure ExpressRoute Global Reach。 如需詳細資訊，請參閱 [ExpressRoute Global Reach](expressroute-global-reach.md)。
 
## <a name="before-you-begin"></a>開始之前

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

開始設定之前，請先完成下列需求：

* 安裝最新版的 Azure CLI。 請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli) 和[開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)。
* 了解 ExpressRoute 線路佈建[工作流程](expressroute-workflows.md)。
* 確定 ExpressRoute 線路處於已佈建狀態。
* 請確定 ExpressRoute 線路上已設定 Azure 私用對等互連。  

### <a name="sign-in-to-your-azure-account"></a>登入您的 Azure 帳戶

若要開始設定，請登入您的 Azure 帳戶。 下列命令會開啟預設瀏覽器，並提示您輸入 Azure 帳戶的登入認證：  

```azurecli
az login
```

如果您有多個 Azure 訂用帳戶，請檢查帳戶的訂用帳戶：

```azurecli
az account list
```

指定您要使用的訂用帳戶：

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>識別要設定的 ExpressRoute 線路

您可以在任兩個 ExpressRoute 線路之間啟用 ExpressRoute Global Reach，只要它們位於支援的國家/地區且建立在不同的對等互連位置即可。 如果這兩個線路皆為您的訂用帳戶所擁有，您可以選擇任一線路來執行設定，如本文稍後所述。 如果這兩個線路位於不同的 Azure 訂用帳戶，您就必須具備其中一個 Azure 訂用帳戶的授權，並在您於另一個 Azure 訂用帳戶中執行設定命令時傳入它的授權金鑰。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>在內部部署網路之間啟用連線

執行命令以啟用連線時，請注意下列參數值的需求：

* *peer-circuit* 應該是完整的資源識別碼。 例如︰

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *address-prefix* 必須是 "/29" IPv4 子網路 (例如 "10.0.0.0/29")。 我們會使用此子網路中的 IP 位址，在兩個 ExpressRoute 線路之間建立連線。 您不得在 Azure 虛擬網路或內部部署網路中使用此子網路中的位址。

執行下列 CLI 命令以連接兩條 ExpressRoute 線路：

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI 輸出如下所示：

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

完成此作業後，您將透過兩個 ExpressRoute 線路，在內部部署網路的兩端建立連線。

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>在不同 Azure 訂用帳戶中啟用 ExpressRoute 線路之間的連線

如果這兩個線路位於不同的 Azure 訂用帳戶，您就需要授權。 在下列設定中，您會在線路 2 的訂用帳戶中產生授權，並將授權金鑰傳遞至線路 1。

1. 產生授權金鑰：

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   CLI 輸出如下所示：

   ```azurecli
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. 記下線路 2 的資源識別碼和授權金鑰。

1. 針對線路 1 執行下列命令，傳入線路 2 的資源識別碼和授權金鑰：

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

完成此作業後，您將透過兩個 ExpressRoute 線路，在內部部署網路的兩端建立連線。

## <a name="get-and-verify-the-configuration"></a>取得及驗證設定

使用下列命令來驗證已進行設定之線路上的設定 (上一個範例中的線路 1)：

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

在 CLI 輸出中，您將會看到 *CircuitConnectionStatus*。 它會告訴您這兩個線路之間的連線已建立 (「已連線」) 還是未建立 (「已中斷連線」)。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>在內部部署網路之間停用連線

若要停用連線，請針對已進行設定的線路 (先前範例中的線路 1) 執行命令。

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

使用 ```show``` 命令來確認狀態。

完成此作業後，您的內部部署網路之間將無法再透過 ExpressRoute 線路進行連線。

## <a name="next-steps"></a>後續步驟

* [深入了解 ExpressRoute 觸角擴及全球](expressroute-global-reach.md)
* [驗證 ExpressRoute 連線](expressroute-troubleshooting-expressroute-overview.md)
* [將 ExpressRoute 線路連結到虛擬網路](expressroute-howto-linkvnet-arm.md)
