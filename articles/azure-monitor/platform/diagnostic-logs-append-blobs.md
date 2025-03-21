---
title: 準備 Azure 監視器診斷記錄的格式變更
description: Azure 診斷記錄將會在 2018 年 11 月 1 日移至使用附加 Blob。
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: f626ef4cfb385a62f68ab611b77dd0c307cd3820
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882374"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>為 Azure 監視器的診斷記錄在封存至儲存體帳戶時的格式變更做好準備

> [!WARNING]
> 如果您要使用資源診斷設定傳送 [Azure 資源診斷記錄或計量至儲存體帳戶](./../../azure-monitor/platform/archive-diagnostic-logs.md) 或使用記錄設定檔傳送[活動記錄至儲存體帳戶](./../../azure-monitor/platform/archive-activity-log.md)，則儲存體帳戶中的資料格式將會在 2018 年 11 月 1 日變更為 JSON 資料行。 下方的指示會說明新格式的影響，以及如何更新工具來加以因應。 
>
> 

## <a name="what-is-changing"></a>變更內容

Azure 監視器提供一種功能，可讓您將資源診斷資料和活動記錄資料傳送至 Azure 儲存體帳戶、事件中樞命名空間或 Log Analytics。 為解決系統效能問題，用來將記錄資料傳送至 Blob 儲存體的格式將會在 **2018 年 11 月 1 日午夜 12:00 UTC** 變更。 如果您有工具會從 Blob 儲存體讀取資料，便需要更新工具才能了解新的資料格式。

* 在 2018 年 11 月 1 日星期四午夜 12:00 UTC，Blob 格式將會變更為 [JSON 資料行](http://jsonlines.org/)。 這表示每筆記錄都會以新行字元隔開、沒有外部記錄陣列，而且 JSON 記錄之間沒有逗號。
* 所有訂用帳戶和所有診斷設定的 Blob 格式都將會同時變更。 11 月 1 日發出的第一個 PT1H.json 檔案將會使用這個新格式。 Blob 和容器名稱維持不變。
* 設定現在與 11 月 1 日之間的診斷設定，以在 11 月 1 日之前繼續使用目前格式發出資料。
* 在所有的公用雲端區域中，這項變更會同時發生。 變更還不會出現在 Azure 中國、Azure 德國或 Azure Government 雲端中。
* 這項變更會影響下列資料類型：
  * [Azure 資源診斷記錄](./../../azure-monitor/platform/archive-diagnostic-logs.md) ([請參閱這裡的資源清單](./../../azure-monitor/platform/diagnostic-logs-schema.md))
  * [診斷設定所匯出的 Azure 資源計量](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)
  * [記錄設定檔所匯出的 Azure 活動記錄資料](./../../azure-monitor/platform/archive-activity-log.md)
* 這項變更不會影響：
  * 網路流量記錄
  * 尚未透過 Azure 監視器提供的 Azure 服務記錄 (例如 Azure App Service 診斷記錄、儲存體分析記錄)
  * 將 Azure 診斷記錄和活動記錄路由傳送至其他目的地 (事件中樞、Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>如何查看您是否受影響

您只有在下列情況下才會受到這項變更的影響：
1. 使用資源診斷設定將記錄資料傳送至 Azure 儲存體帳戶，以及
2. 有工具依存著儲存體中這些記錄的 JSON 結構。
 
若要識別您的資源診斷設定是否將資料傳送至 Azure 儲存體帳戶，您可以巡覽至入口網站的 [監視器] 區段，並按一下 [診斷設定]，然後識別將 [診斷狀態] 設定為 [已啟用] 的任何資源：

![Azure 監視器診斷設定刀鋒視窗](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

如果 [診斷狀態] 設定為 [已啟用]，則您會有該資源的作用中診斷設定。 按一下資源，以查看是否有任何診斷設定將資料傳送至儲存體帳戶：

![已啟用儲存體帳戶](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

如果您的資源使用這些資源診斷設定將資料傳送至儲存體帳戶，則這項變更會影響該儲存體帳戶中的資料格式。 除非您的自訂工具可以在這些儲存體帳戶外部操作，否則格式變更不會影響您。

### <a name="details-of-the-format-change"></a>格式變更的詳細資料

Azure Blob 儲存體中 PT1H.json 檔案的目前格式會使用記錄的 JSON 陣列。 下列範例是目前的 KeyVault 記錄檔：

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

新的格式會使用 [JSON 資料行](http://jsonlines.org/)，其中每個事件都是一行，而新行字元表示新事件。 以下是在變更之後，上述範例在 PT1H.json 檔案中的外觀：

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

這種新格式可讓 Azure 監視器使用[附加 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) 發送記錄檔，以更有效率地持續附加新的事件資料。

## <a name="how-to-update"></a>更新方式

如果您的自訂工具內嵌這些記錄檔以供進一步處理，則只需要更新。 如果您使用外部記錄分析或 SIEM 工具，則建議[改為使用事件中樞內嵌此資料](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)。 事件中樞整合在處理來自許多服務的記錄以及將特定記錄中的位置加上書籤方面更為簡單。

應該更新自訂工具，以處理目前格式和上述 JSON 資料行格式。 這確保開始以新格式顯示資料時，您的工具不會中斷。

## <a name="next-steps"></a>後續步驟

* 了解[將資源診斷記錄封存至儲存體帳戶](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* 了解[將活動記錄資料封存至儲存體帳戶](./../../azure-monitor/platform/archive-activity-log.md)

