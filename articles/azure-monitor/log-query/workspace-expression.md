---
title: Azure 監視器記錄查詢中的 workspace() 運算式 |Microsoft Docs
description: 在 Azure 監視器記錄查詢中，workspace 運算式用來從相同資源群組、另一個資源群組或另一個訂用帳戶中的特地工作區擷取資料。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: 933d37f576d0b8507d2311a3e31e34182a0a2e69
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269830"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>Azure 監視器記錄查詢中的 workspace() 運算式

在 Azure 監視器查詢中，`workspace` 運算式用來從相同資源群組、另一個資源群組或另一個訂用帳戶中的特地工作區擷取資料。 這適用於在 Application Insights 查詢中包含記錄資料，以及在記錄查詢中跨多個工作區查詢資料。


## <a name="syntax"></a>語法

`workspace(`*識別碼*`)`

## <a name="arguments"></a>引數

- *識別碼*：使用下表中的其中一個格式識別工作區。

| 識別碼 | 說明 | 範例
|:---|:---|:---|
| 資源名稱 | 人類可閱讀的工作區名稱 (也稱為「元件名稱」) | workspace("contosoretail") |
| 完整名稱 | 使用下列形式的工作區完整名稱："subscriptionName/resourceGroup/componentName" | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | 工作區的 GUID | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure 資源識別碼 | Azure 資源的識別碼 | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>注意

* 您必須有工作區的讀取權限。
* 相關的運算式是 `app`，這可讓您跨 Application Insights 應用程式執行查詢。

## <a name="examples"></a>範例

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>後續步驟

- 請參閱[應用程式運算式](app-expression.md)來參考 Application Insights 應用程式。
- 了解 [Azure 監視器資料](log-query-overview.md)的儲存方式。
- 存取 [Kusto 查詢語言](/azure/kusto/query/)的完整文件。