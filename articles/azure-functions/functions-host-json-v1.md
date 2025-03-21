---
title: 適用於 Azure Functions 1.x 的 host.json 參考
description: Azure Functions host.json 檔案與 v1 執行階段的參考文件。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: ee82aab37973117b0c1960d8b75a29bfad38b7c7
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252070"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>適用於 Azure Functions 1.x 的 host.json 參考

> [!div class="op_single_selector" title1="選擇您正在使用的 Azure Functions 運行時的版本: "]
> * [第 1 版](functions-host-json-v1.md)
> * [第 2 版](functions-host-json.md)

*host.json* 中繼資料檔案所包含的全域設定選項會影響函式應用程式的所有函式。 本文列出 v1 執行階段可用的設定。 JSON 結構描述位於 http://json.schemastore.org/host。

> [!NOTE]
> 本文適用於 Azure Functions 1.x。  有關 Functions 2.x 中 host.json 的參考，請參閱[適用於 Azure Functions 2.x 的 host.json 參考](functions-host-json.md)。

其他函數應用程式設定選項的管理是在[應用程式設定](functions-app-settings.md)中進行。

有些 host.json 設定只有在本機執行時，才會在 [local.settings.json](functions-run-local.md#local-settings-file) 檔案中使用。

## <a name="sample-hostjson-file"></a>範例 host.json 檔案

下列範例 *host.json* 檔案已指定所有可能的選項。


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

本文的下列各節說明每個最上層屬性。 除非另有說明，否則全部都是選擇項目。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

[事件中樞觸發程序和繫結](functions-bindings-event-hubs.md)的組態設定。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

工作主機所執行的函式清單。 空陣列表示已執行所有函式。 預定只能在[本機執行](functions-run-local.md)時使用。 在 Azure 的函數應用程式中，您應該改為依照[如何停用 Azure Functions 中的函式](disable-function.md)中的步驟來停用特定函式，而不是使用此設定。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指出所有函式的逾時持續期間。 在無伺服器的使用情況方案中，有效範圍是從 1 秒到 10 分鐘，而預設值是 5 分鐘。 在 App Service 方案中，並沒有整體限制，而預設值則是取決於執行階段版本。

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[主機健康情況監視器](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)的組態設定。

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|已啟用|true|指定是否已啟用此功能。 | 
|healthCheckInterval|10 秒|定期背景健康情況檢查之間的時間間隔。 | 
|healthCheckWindow|2 分鐘|與 `healthCheckThreshold` 設定搭配使用的滑動時間範圍。| 
|healthCheckThreshold|6|在主機回收起始之前，健康情況檢查可以失敗的最大次數。| 
|counterThreshold|0.80|系統會將效能計數器視為狀況不良的閾值。| 

## <a name="http"></a>http

[HTTP 觸發程序和繫結](functions-bindings-http-webhook.md)的組態設定。

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*僅限 1.x 版。*

作業主機的唯一識別碼。 可以是已移除虛線的小寫 GUID。 在本機執行時為必要項目。 在 Azure 中執行時，建議您不要設定識別碼值。 當省略 `id` 時，在 Azure 中會自動產生識別碼。 

如果您在多個函數應用程式中共用儲存體帳戶，請確定每個函數應用程式具有不同的 `id`。 您可以省略 `id` 屬性或將每個函數應用程式的 `id` 手動設定為不同的值。 計時器觸發程序會使用儲存體鎖定，以確保當函數應用程式相應放大至多個執行個體時，只會有一個計時器執行個體。 如果兩個函數應用程式共用相同的 `id`，且每一個都是使用計時器觸發程序，則只有一個計時器會執行。

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

控制 [ILogger 物件](functions-monitoring.md#write-logs-in-c-functions)或 [context.log](functions-monitoring.md#write-logs-in-javascript-functions) 所寫入記錄的篩選。

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|categoryFilter|n/a|指定依類別的篩選| 
|defaultLevel|資訊|針對 `categoryLevels` 陣列中未指定的任何類別，會將這個層級和以上層級的記錄傳送至 Application Insights。| 
|categoryLevels|n/a|一個類別陣列，指定針對每個類別傳送至 Application Insights 的最小記錄層級。 這裡指定的類別控制所有開頭為相同值的類別，但會優先使用較長的值。 在上述範例 *host.json* 檔案中，所有開頭為 "Host.Aggregator" 的類別都會記錄在 `Information` 層級。 所有開頭為 "Host" 的其他類別 (例如 "Host.Executor") 都會記錄於 `Error` 層級。| 

## <a name="queues"></a>queues

[儲存體佇列觸發程序和繫結](functions-bindings-storage-queue.md)的組態設定。

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

[服務匯流排觸發程序和繫結](functions-bindings-service-bus.md)的組態設定。

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Singleton 鎖定行為的組態設定。 如需詳細資訊，請參閱[單一支援的 GitHub 問題](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)。

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|lockPeriod|00:00:15|取得函式層級鎖定的期間。 鎖定會自動更新。| 
|listenerLockPeriod|00:01:00|接聽程式鎖定所需的期間。| 
|listenerLockRecoveryPollingInterval|00:01:00|啟動時無法取得接聽程式鎖定時，用於接聽程式鎖定復原的時間間隔。| 
|lockAcquisitionTimeout|00:01:00|執行階段將嘗試取得鎖定的時間量上限。| 
|lockAcquisitionPollingInterval|n/a|鎖定取得嘗試之間的間隔。| 

## <a name="tracing"></a>tracing

*1.x 版*

使用 `TraceWriter` 物件所建立記錄的組態設定。 請參閱 [C# 記錄](functions-reference-csharp.md#logging)和 [Node.js 記錄](functions-reference-node.md#writing-trace-output-to-the-console)。

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|consoleLevel|info|主控台記錄的追蹤層級。 選項為：`off`、`error`、`warning`、`info` 和 `verbose`。|
|fileLoggingMode|debugOnly|檔案記錄的追蹤層級。 選項為 `never`、`always`、`debugOnly`。| 

## <a name="watchdirectories"></a>watchDirectories

應該監視其變更的一組[共用程式碼目錄](functions-reference-csharp.md#watched-directories)。  請確定，這些目錄中的程式碼變更時，函式會反映變更。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何更新 host.json 檔案](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [請參閱環境變數中的全域設定](functions-app-settings.md)
