---
title: 使用 Azure 監視器跨資源查詢 | Microsoft Docs
description: 本文說明如何針對來自訂用帳戶中多個工作區和 App Insights 應用程式的資源執行查詢。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: ccc9a74c4e238ebfcab0fc05a3bf825000917843
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998927"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>在 Azure 監視器中執行跨資源記錄查詢  

先前使用 Azure 監視器時，您只能分析來自目前工作區內的資料，這項限制讓您無法跨訂用帳戶中定義的多個工作區執行查詢。  此外，您只能搜尋在 Application Insights 中或從 Visual Studio 直接使用 Application Insights 從 Web 應用程式收集的遙測項目。  如此一來，也難以將作業和應用程式資料一起以原生方式分析。   

現在您不僅可以跨多個 Log Analytics 工作區查詢，也可以查詢來自相同資源群組、另一個資源群組或其他訂用帳戶中特定 Application Insights 應用程式中的資料。 這讓您對資料能有全系統的檢視。  您只能在 [Log Analytics](portals.md) 中執行這些類型的查詢。 單一查詢中可納入的資源 (Log Analytics 工作區和 Application Insights 應用程式) 數目上限為 100 個。 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>跨 Log Analytics 工作區和從 Application Insights 查詢
若要在查詢中參考另一個工作區，請使用 [*workspace*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) 識別項，而若要查詢來自 Application Insights 的應用程式，請使用 [*app*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression) 識別項。  

### <a name="identifying-workspace-resources"></a>識別工作區資源
下列範例示範對 Log Analytics 工作區的查詢，從名為 contosoretail-it 工作區上的 Update 資料表，傳回記錄彙總計數。 

您可以下列數種方式之一來完成識別工作區：

* 資源名稱 - 是人類看得懂的工作區名稱，有時稱為「元件名稱」。 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >以名稱來識別工作區時，會假設該名稱在所有可存取的訂用帳戶中是唯一的。 如果您有多個應用程式具有該指定名稱，查詢就會因為語意模糊而失敗。 在此情況下，您必須使用其中一種其他識別碼。

* 限定名稱 - 是工作區的「完整名稱」，由訂用帳戶名稱、資源群組及元件名稱所組成，格式如下：*subscriptionName/resourceGroup/componentName*。 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >由於 Azure 訂用帳戶名稱並非唯一，因此這個識別碼可能語意模糊。 
    >

* 工作區識別碼 - 工作區識別碼是指派給每個工作區的唯一、不可變識別碼，會以全域唯一識別碼 (GUID) 來表示。

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure 資源識別碼 – Azure 定義的工作區唯一身分識別。 當資源名稱語意模糊時，您可以使用資源識別碼。  就工作區而言，格式為：*/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*。  

    例如︰
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>識別應用程式
下列範例會傳回對 Application Insights 中名為 *fabrikamapp* 的應用程式所進行要求的彙總計數。 

在 Application Insights 中識別應用程式可以使用 *app(Identifier)* 運算式來完成。  *Identifier* 引數會使用下列其中之一指定應用程式：

* 資源名稱 - 是人類看得懂的應用程式名稱，有時稱為「元件名稱」。  

    `app("fabrikamapp")`

* 限定名稱 - 是應用程式的「完整名稱」，由訂用帳戶名稱、資源群組及元件名稱所組成，格式如下：*subscriptionName/resourceGroup/componentName*。 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >由於 Azure 訂用帳戶名稱並非唯一，因此這個識別碼可能語意模糊。 
    >

* 識別碼 - 應用程式的應用程式 GUID。

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure 資源識別碼 – Azure 定義的應用程式唯一身分識別。 當資源名稱語意模糊時，您可以使用資源識別碼。 格式為：*/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName*。  

    例如︰
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>跨多個資源執行查詢
您可以查詢任何資源執行個體的多個資源，這些資源執行個體可以是合併的工作區和應用程式。
    
跨兩個工作區的查詢範例：    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>使用多個資源的跨資源查詢
使用跨資源查詢將資料從多個 Log Analytics 工作區和 Application Insights 資源相互關聯時，查詢可能會變得複雜且難以維護。 您應該利用 [Azure 監視器記錄查詢中的函式](functions.md)，將查詢邏輯與查詢資源的範圍分開，如此可簡化查詢結構。 下列範例示範如何監視多個 Application Insights 資源，並將失敗的要求計數依應用程式名稱視覺化。 

建立參考 Application Insights 資源範圍的查詢，如下所示。 `withsource= SourceApp` 命令會新增一個資料行，此資料行可指定傳送記錄的應用程式名稱。 [將查詢儲存為函式](functions.md#create-a-function)，且別名為 _applicationsScoping_。

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



您現在可以在跨資源查詢中[使用此函式](../../azure-monitor/log-query/functions.md#use-a-function)，如下所示。 函式別名 _applicationsScoping_ 會從所有定義的應用程式傳送要求資料表的聯集。 接著，查詢會篩選失敗的要求，並依應用程式將趨勢視覺化。 在此範例中，_parse_ 運算子是選用的。 它會從 _SourceApp_ 屬性擷取應用程式名稱。

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![時間圖](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>後續步驟

- 檢閱[分析 Azure 監視器中的記錄資料](log-query-overview.md)，了解記錄查詢的簡短概觀，以及建置 Azure 監視器記錄資料結構的方式。
- 檢閱 [Azure 監視器記錄查詢](query-language.md)，以檢視所有 Azure 監視器記錄查詢的資源。
