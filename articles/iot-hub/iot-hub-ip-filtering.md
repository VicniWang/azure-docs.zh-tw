---
title: Azure IoT 中樞 IP 連線篩選器 | Microsoft Docs
description: 如何使用 IP 篩選來封鎖從特定 IP 位址至 Azure IoT 中樞的連接。 您可以封鎖來自個別 IP 位址或 IP 位址範圍的連接。
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: d549127b5cbdb3a94e435e753592f3227cb95f3a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232209"
---
# <a name="use-ip-filters"></a>使用 IP 篩選器

安全性是任何以 Azure IoT 中樞為基礎之 IoT 解決方案的重要一環。 有時候您需要在執行安全性設定的程序中明確地指定可連線的裝置 IP 位址。 「IP 篩選器」功能可讓您設定規則，以拒絕或接受來自特定 IPv4 位址的流量。

## <a name="when-to-use"></a>使用時機

有兩個特定使用案例適合封鎖特定 IP 位址的 IoT 中樞端點︰

* 您的 IoT 中樞只應接收來自指定 IP 位址範圍的流量，並拒絕其他所有流量。 例如，您搭配 [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) 使用 IoT 中樞來建立 IoT 中樞與內部部署基礎結構之間的私人連線。

* 您需要拒絕 IoT 中樞系統管理員認為可疑的 IP 位址所傳來的流量。

## <a name="how-filter-rules-are-applied"></a>篩選器規則的套用方式

IP 篩選器規則會套用在 IoT 中樞服務層級。 因此，IP 篩選器規則會套用至來自裝置和後端應用程式的所有連接 (使用任何受支援的通訊協定)。

嘗試建立連線的 IP 位址若符合 IoT 中樞內的拒絕 IP 規則，將會收到未授權 401 狀態碼和描述。 回應訊息則不涉及 IP 規則。

## <a name="default-setting"></a>預設設定

根據預設，IoT 中樞之入口網站中的 **IP 篩選器**方格是空的。 這個預設設定表示您的中樞會接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。

![IoT 中樞預設 IP 篩選器設定](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>新增或編輯 IP 篩選器規則

當您新增 IP 篩選器規則時，系統會提示您輸入下列值︰

* [IP 篩選器規則名稱] 必須是唯一、不區分大小寫的英數字元字串，最長可為 128 個字元。 所能接受的字元只有 ASCII 7 位元英數字元以及 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 選取 [拒絕] 或 [接受] 做為 IP 篩選器規則的 [動作]。

* 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。

![新增 IP 篩選器規則到 IoT 中樞](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

儲存規則之後，您會看到通知您正在進行更新的警示。

![有關儲存 IP 篩選器規則的通知](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

當您達到 10 個 IP 篩選器規則的上限後，[新增] 選項便會停用。

您可以按兩下包含規則的資料列，以編輯現有規則。

> [!NOTE]
> 拒絕 IP 位址可防止其他 Azure 服務 (例如 Azure 串流分析、Azure 虛擬機器，或入口網站中的裝置總管) 與 IoT 中樞互動。

> [!WARNING]
> 如果您使用 Azure 串流分析 (ASA) 從啟用 IP 篩選的 IoT 中樞讀取訊息，請在 ASA 連接字串中使用 IoT 中樞之與事件中樞相容的名稱和端點。

## <a name="delete-an-ip-filter-rule"></a>刪除 IP 篩選器規則

若要刪除 IP 篩選器規則，請選取方格中的一個或多個規則，然後按一下 [刪除]。

![刪除 IoT 中樞 IP 篩選器規則](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>使用 Azure CLI 擷取和更新 IP 篩選條件

您可以透過 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 擷取和更新 IoT 中樞的 IP 篩選條件。 

若要擷取 IoT 中樞目前的 IP 篩選條件，請執行：

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

這會傳回 JSON 物件，其中您現有的 IP 篩選條件會列在 `properties.ipFilterRules` 機碼之下：

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

若要為 IoT 中樞新增 IP 篩選條件，請執行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

若要移除 IoT 中樞中的現有 IP 篩選條件，請執行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

請注意，`<ipFilterIndexToRemove>` 必須對應到 IoT 中樞的 `properties.ipFilterRules` 中的 IP 篩選條件排序方式。


## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>使用 Azure PowerShell 擷取和更新 IP 篩選條件

您可以透過 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 擷取和設定 IoT 中樞的 IP 篩選條件。 

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzureRmResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzureRmResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>使用 REST 更新 IP 篩選規則

您也可以使用 Azure 資源提供者的 REST 端點，擷取和修改 IoT 中樞的 IP 篩選條件。 請參閱 [createorupdate 方法](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate)中的 `properties.ipFilterRules`。


## <a name="ip-filter-rule-evaluation"></a>IP 篩選器規則評估

IP 篩選器規則會依序套用，第一個符合 IP 位址的規則會決定接受或拒絕動作。

例如，如果您想要接受 192.168.100.0/22 範圍中的位址，並拒絕其他所有位址，則方格中的第一個規則應接受位址範圍 192.168.100.0/22。 下一個規則應使用 0.0.0.0/0 範圍拒絕所有位址。

按一下資料列前端呈垂直方向的三個點並使用拖放功能，即可變更方格中的 IP 篩選器規則順序。

若要儲存新的 IP 篩選器規則順序，請按一下 [儲存]。

![變更 IoT 中樞 IP 篩選器規則的順序](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>後續步驟

若要進一步探索 IoT 中樞的功能，請參閱︰

* [作業監視](iot-hub-operations-monitoring.md)
* [IoT 中樞計量](iot-hub-metrics.md)