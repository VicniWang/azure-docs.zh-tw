---
title: Azure 監視器記錄查詢中的 app() 運算式 |Microsoft Docs
description: 在 Azure監視器記錄查詢中，app 運算式可用來從相同資源群組、其他資源群組或其他訂用帳戶中的特定 Application Insights 應用程式擷取資料。
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
ms.date: 01/25/2019
ms.author: bwren
ms.openlocfilehash: 0abe2c869a05d359f692db105eb647f9e6a69df8
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266419"
---
# <a name="app-expression-in-azure-monitor-query"></a>Azure 監視器查詢中的 app() 運算式

在 Azure監視器記錄查詢中，`app` 運算式可用來從相同資源群組、其他資源群組或其他訂用帳戶中的特定 Application Insights 應用程式擷取資料。 這適用於在 Azure 監視器記錄查詢中包含應用程式資料，以及在 Application Insights 查詢中跨多個應用程式查詢資料。



## <a name="syntax"></a>語法

`app(`*識別碼*`)`


## <a name="arguments"></a>引數

- *識別碼*：使用下表中的其中一個格式來識別應用程式。

| 識別碼 | 說明 | 範例
|:---|:---|:---|
| 資源名稱 | 人類可閱讀的應用程式名稱 (也稱為「元件名稱」) | app("fabrikamapp") |
| 完整名稱 | 使用下列形式的應用程式完整名稱："subscriptionName/resourceGroup/componentName" | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | 應用程式的 GUID | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure 資源識別碼 | Azure 資源的識別碼 |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>注意

* 您必須有應用程式的讀取權限。
* 以應用程式名稱來識別應用程式時，會假設該名稱在所有可存取的訂用帳戶中是唯一的。 如果您有多個應用程式具有該指定名稱，查詢將會因為語意模糊而失敗。 在此情況下，您必須使用其中一種其他識別碼。
* 使用相關的運算式 [workspace](workspace-expression.md) 跨 Log Analytics 工作區進行查詢。
* 使用 Azure 入口網站來建立[自訂記錄搜尋警示規則](../platform/alerts-log.md)時，除非使用 Application Insights 應用程式作為警示規則的資源，否則搜尋查詢中目前不支援 app() 運算式。

## <a name="examples"></a>範例

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- 請參閱 [workspace 運算式](workspace-expression.md)，以參考 Log Analytics 工作區。
- 了解 [Azure 監視器資料](../../azure-monitor/log-query/log-query-overview.md)的儲存方式。
- 存取 [Kusto 查詢語言](/azure/kusto/query/)的完整文件。