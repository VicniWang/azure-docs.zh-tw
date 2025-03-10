---
title: 適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄
description: 本文說明適用於 PostgreSQL 的 Azure 資料庫產生查詢和錯誤記錄的方式，以及設定記錄保留的方式。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 0e2dc2af6b4c7ddf531458136e6bcabb49be3b8f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538800"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄 
適用於 PostgreSQL 的 Azure 資料庫會產生查詢和錯誤記錄。 查詢和錯誤記錄可用來針對組態錯誤及未達最佳效能的情況，進行識別、疑難排解及修復。 (不包括對交易記錄的存取)。 

## <a name="configure-logging"></a>設定記錄 
您可以使用記錄伺服器參數來設定伺服器上的記錄功能。 在每部新伺服器上，**log_checkpoints** 和 **log_connections** 都是預設為開啟。 有其他可供您調整來符合您記錄需求的參數： 

![適用於 PostgreSQL 的 Azure 資料庫 - 記錄參數](./media/concepts-server-logs/log-parameters.png)

如需有關這些參數的詳細資訊，請參閱 PostgreSQL 的[錯誤報告和記錄](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) \(英文\) 文件。 若要了解如何設定「適用於 PostgreSQL 的 Azure 資料庫」參數，請參閱[入口網站文件](howto-configure-server-parameters-using-portal.md)或 [CLI 文件](howto-configure-server-parameters-using-cli.md)。

## <a name="access-server-logs-through-portal-or-cli"></a>透過入口網站或 CLI 存取伺服器記錄
如果您已啟用記錄，便可以使用 [Azure 入口網站](howto-configure-server-logs-in-portal.md)、[Azure CLI](howto-configure-server-logs-using-cli.md) 及 Azure REST API 從「適用於 PostgreSQL 的 Azure 資料庫」記錄檔儲存體存取記錄。 記錄檔每達 1 小時或 100 MB 大小 (以先達到者為準) 就會輪替。 您可以使用與您伺服器相關的  **log\_retention\_period**  參數，來設定此記錄檔儲存體的保留期。 預設值為 3 天；最大值為 7 天。 您的伺服器必須配置足夠的儲存體來保存記錄檔。 (此保留參數不會控管「Azure 診斷記錄」)。


## <a name="diagnostic-logs"></a>診斷記錄檔
「適用於 PostgreSQL 的 Azure 資料庫」已與「Azure 監視器診斷記錄」整合。 在 PostgreSQL 伺服器上啟用記錄之後，您可以選擇將它們發出至 [Log Analytics](../azure-monitor/log-query/log-query-overview.md)、「事件中樞」或「Azure 儲存體」。 若要深入了解如何啟用診斷記錄，請參閱[診斷記錄文件](../azure-monitor/platform/diagnostic-logs-overview.md)的＜如何＞一節。 


下表描述每個記錄的內容。 視您選擇的輸出端點而定，所含欄位及其出現順序可能會有所不同。 

|**欄位** | **說明** |
|---|---|
| TenantId | 您的租用戶識別碼 |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | 以 UTC 記錄記錄時的時間戳記 |
| 類型 | 記錄的類型。 一律為 `AzureDiagnostics` |
| SubscriptionId | 伺服器所屬訂用帳戶的 GUID |
| ResourceGroup | 伺服器所屬資源群組的名稱 |
| ResourceProvider | 資源提供者名稱。 一律為 `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | 資源 URI |
| 資源 | 伺服器的名稱 |
| 類別 | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | 記錄層級，範例：記錄、錯誤、通知 |
| 訊息 | 主要記錄訊息 | 
| 網域 | 伺服器版本，範例：postgres-10 |
| 詳細資料 | 次要記錄訊息 (如果適用) |
| ColumnName | 資料行的名稱 (如果適用) |
| SchemaName | 結構描述的名稱 (如果適用) |
| DatatypeName | 資料類型的名稱 (如果適用) |
| LogicalServerName | 伺服器的名稱 | 
| _ResourceId | 資源 URI |

## <a name="next-steps"></a>後續步驟
- 了解如何從 [Azure 入口網站](howto-configure-server-logs-in-portal.md) 或 [Azure CLI](howto-configure-server-logs-using-cli.md) 存取記錄。
- 深入了解 [Azure 監視器價格](https://azure.microsoft.com/pricing/details/monitor/)。
