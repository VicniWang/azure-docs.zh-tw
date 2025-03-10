---
title: Azure Log Analytics 中檢視表設計工具圖格的參考指南 | Microsoft Docs
description: 藉由使用 Log Analytics 中的檢視設計工具，您可以在 Azure 入口網站中建立能顯示 Log Analytics 工作區中各種資料視覺效果的自訂檢視。 本文章是自訂檢視中可用的圖格設定的參考指南。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: 5b01b55c8b2367861459069cc255840e883b9fc5
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817377"
---
# <a name="reference-guide-to-view-designer-tiles-in-log-analytics"></a>Log Analytics 中的檢視設計工具圖格參考指南
藉由使用 Azure Log Analytics 中的檢視設計工具，您可以在 Azure 入口網站中建立能顯示 Log Analytics 工作區中各種資料視覺效果的自訂檢視。 本文章是自訂檢視中可用的圖格設定的參考指南。

如需檢視設計工具的詳細資訊，請參閱：

* [檢視設計工具](view-designer.md)：提供檢視設計工具的概觀以及建立和編輯自訂檢視的程序。
* [視覺效果組件參考](view-designer-parts.md)：提供自訂檢視中可用的視覺效果組件設定的參考指南。


下表說明可用的檢視設計工具圖格：  

| 圖格 | 說明 |
|:--- |:--- |
| [Number](#number-tile) |來自查詢的記錄計數。 |
| [雙數字](#two-numbers-tile) |來自兩個不同查詢的記錄計數。 |
| [環圈](#donut-tile) | 以查詢為依據的圖表，中央有彙總值。 |
| 折線圖與圖說文字 | 以查詢為依據的折線圖，以及有彙總值的圖說文字。 |
| [折線圖](#line-chart-tile) |根據查詢的折線圖。 |
| [雙時間軸](#two-timelines-tile) | 有兩個數列的直條圖，數列各以不同查詢為依據。 |

以下幾節詳細說明圖格類型和其屬性。

## <a name="number-tile"></a>數字圖格
**數字**圖格會顯示記錄檔查詢傳回的記錄計數和標籤。

![數字圖格](media/view-designer-tiles/tile-number.png)

| 設定 | 說明 |
|:--- |:--- |
| Name |圖格上方顯示的文字。 |
| 說明 |圖格名稱下方顯示的文字。 |
| **圖格** | |
| 圖例 |值下方顯示的文字。 |
| 查詢 |執行的查詢。 顯示查詢所傳回的記錄計數。 |
| **進階** |**> 資料流驗證** |
| 已啟用 |如果應該為圖格啟用資料流驗證，請選取此連結。 如果沒有可用的資料，此方法可提供替代訊息。 您通常會使用此方法在暫存期間 (當檢視已安裝，開始有資料進入) 提供訊息。 |
| 查詢 |執行查詢以判斷資料是否可供檢視使用。 如果查詢未傳回任何結果，會顯示訊息來代替主查詢的值。 |
| 訊息 |資料流驗證查詢沒有傳回任何資料時所顯示的訊息。 如果您不提供任何訊息，會顯示*正在執行評量*狀態訊息。 |


## <a name="two-numbers-tile"></a>雙數字圖格
此圖格會顯示來自兩個不同記錄查詢的記錄計數，和各自的標籤。

![雙數字圖格](media/view-designer-tiles/tile-two-numbers.png)

| 設定 | 說明 |
|:--- |:--- |
| Name |圖格上方顯示的文字。 |
| 說明 |圖格名稱下方顯示的文字。 |
| **第一個圖格** | |
| 圖例 |值下方顯示的文字。 |
| 查詢 |執行的查詢。 顯示查詢所傳回的記錄計數。 |
| **第二個圖格** | |
| 圖例 |值下方顯示的文字。 |
| 查詢 |執行的查詢。 顯示查詢所傳回的記錄計數。 |
| **進階** |**> 資料流驗證** |
| 已啟用 |如果應該為圖格啟用資料流驗證，請選取此連結。 如果沒有可用的資料，此方法可提供替代訊息。 您通常會使用此方法在暫存期間 (當檢視已安裝，開始有資料進入) 提供訊息。 |
| 查詢 |執行查詢以判斷資料是否可供檢視使用。 如果查詢未傳回任何結果，會顯示訊息來代替主查詢的值。 |
| 訊息 |資料流驗證查詢沒有傳回任何資料時所顯示的訊息。 如果您不提供任何訊息，會顯示*正在執行評量*狀態訊息。 |


## <a name="donut-tile"></a>環圈圖格
**環圈**圖格會顯示從記錄檔查詢中的值資料行彙總而來的一個數字。 環圈會以圖形方式顯示前三筆記錄的結果。

![環圈圖格](media/view-designer-tiles/tile-donut.png)

| 設定 | 說明 |
|:--- |:--- |
| Name |圖格上方顯示的文字。 |
| 說明 |圖格名稱下方顯示的文字。 |
| **環圈** | |
| 查詢 |針對環圈圖執行的查詢。 第一個屬性是文字值，而第二個屬性是數值。 此查詢通常是使用 *measure* 關鍵字來彙總結果。 |
| **環圈** |**> 中心** |
| 文字 |環圈圖內的值下方顯示的文字。 |
| 作業 |對值屬性執行以彙總為單一值的作業。<ul><li>總和：將所有具有該屬性值之記錄的值相加。</li><li>百分比：與所有記錄的加總值相比，具有該屬性值之記錄的加總值所佔的百分比。</li></ul> |
| 中心作業使用的結果值 |選擇性地選取加號 (+) 以新增一或多個值。 查詢結果僅限於具有您指定之屬性值的記錄。 若沒有新增任何值，則查詢會包含所有記錄。 |
| **環圈** |**> 其他選項** |
| 色彩 |前三名屬性的個別顯示色彩。 若要為特定屬性值指定替代的色彩，請使用*進階色彩對應*。 |
| 進階色彩對應 |顯示代表特定屬性值的色彩。 如果您指定的值是在前三名，則會顯示替代色彩而不是標準色彩。 如果屬性不在前三名中，則不會顯示其色彩。 |
| **進階** |**> 資料流驗證** |
| 已啟用 |如果應該為圖格啟用資料流驗證，請選取此連結。 如果沒有可用的資料，此方法可提供替代訊息。 您通常會使用此方法在暫存期間 (當檢視已安裝，開始有資料進入) 提供訊息。 |
| 查詢 |執行查詢以判斷資料是否可供檢視使用。 如果查詢未傳回任何結果，會顯示訊息來代替主查詢的值。 |
| 訊息 |資料流驗證查詢沒有傳回任何資料時所顯示的訊息。 如果您不提供任何訊息，會顯示*正在執行評量*狀態訊息。 |


## <a name="line-chart-tile"></a>折線圖圖格
此圖格為一個折線圖，顯示來自一段時間內記錄查詢的多個數列。 

![折線圖與圖說文字圖格](media/view-designer-tiles/tile-line-chart.png)

| 設定 | 說明 |
|:--- |:--- |
| Name |圖格上方顯示的文字。 |
| 說明 |圖格名稱下方顯示的文字。 |
| **折線圖** | |
| 查詢 |針對折線圖執行的查詢。 第一個屬性是文字值，而第二個屬性是數值。 此查詢通常是使用 *measure* 關鍵字來彙總結果。 如果查詢使用 *interval* 關鍵字，則 X 軸會使用此時間間隔。 如果查詢未包含 *interval* 關鍵字，則 X 軸會使用每小時的間隔。 |
| **折線圖** |**> Y 軸** |
| 使用對數刻度 |選取此連結可對 Y 軸使用對數刻度。 |
| Units |指定查詢所傳回之值的單位。 這項資訊用來顯示圖上的標籤，指出值的類型，並選擇性用來將值轉換。 **單位類型**指定單位的分類，並定義可用的**目前的單位類型**值。 如果您選取**轉換成**中的值，則會將數值從**目前單位**類型轉換為**轉換成**的類型。 |
| 自訂標籤 |Y 軸的顯示文字，就在*單位*類型的標籤旁。 如未指定任何標籤，只會顯示*單位*類型。 |
| **進階** |**> 資料流驗證** |
| 已啟用 |如果應該為圖格啟用資料流驗證，請選取此連結。 如果沒有可用的資料，此方法可提供替代訊息。 您通常會使用此方法在暫存期間 (當檢視已安裝，開始有資料進入) 提供訊息。 |
| 查詢 |執行查詢以判斷資料是否可供檢視使用。 如果查詢未傳回任何結果，會顯示訊息來代替主查詢的值。 |
| 訊息 |資料流驗證查詢沒有傳回任何資料時所顯示的訊息。 如果您不提供任何訊息，會顯示*正在執行評量*狀態訊息。 |


## <a name="line-chart-and-callout-tile"></a>折線圖與圖說文字圖格
此圖格有一個折線圖會顯示一段時間來自記錄查詢的多個數列，以及包含彙總值的圖說文字。 

![折線圖與圖說文字圖格](media/view-designer-tiles/tile-line-chart-callout.png)

| 設定 | 說明 |
|:--- |:--- |
| Name |圖格上方顯示的文字。 |
| 說明 |圖格名稱下方顯示的文字。 |
| **折線圖** | |
| 查詢 |針對折線圖執行的查詢。 第一個屬性是文字值，而第二個屬性是數值。 此查詢通常是使用 *measure* 關鍵字來彙總結果。 如果查詢使用 *interval* 關鍵字，則 X 軸會使用此時間間隔。 如果查詢未包含 *interval* 關鍵字，則 X 軸會使用每小時的間隔。 |
| **折線圖** |**> 圖說文字** |
| 圖說文字標題 | 圖說文字值上方顯示的文字。 |
| 數列名稱 |要用作圖說文字值的數列屬性值。 如未提供任何系列，則會使用查詢中的所有記錄。 |
| 作業 |對值屬性執行以彙總為圖說文字單一值的作業。<ul><li>平均：所有記錄值的平均值。</li><li>計數：查詢所傳回所有記錄的計數。</li><li>上次取樣：圖表內含最後一個間隔的值。</li><li>最大值：圖表內含間隔的最大值。</li><li>最小值︰圖表內含間隔的最小值。</li><li>總和：所有記錄值的加總。</li></ul> |
| **折線圖** |**> Y 軸** |
| 使用對數刻度 |選取此連結可對 Y 軸使用對數刻度。 |
| Units |指定要查詢傳回之值的單位。 這項資訊用來顯示圖表上的標籤，指出值的類型，並選擇性地用來轉換值。 *單位*類型指定單位的分類，並定義可用的*目前的單位*類型值。 如果您選取*轉換成*中的值，則數值會從*目前單位*類型轉換為*轉換成*類型。 |
| 自訂標籤 |Y 軸的顯示文字，就在*單位*類型的標籤旁。 如未指定任何標籤，只會顯示*單位*類型。 |
| **進階** |**> 資料流驗證** |
| 已啟用 |如果應該為圖格啟用資料流驗證，請選取此連結。 如果沒有可用的資料，此方法可提供替代訊息。 您通常會使用此方法在暫存期間 (當檢視已安裝，開始有資料進入) 提供訊息。 |
| 查詢 |執行查詢以判斷資料是否可供檢視使用。 如果查詢未傳回任何結果，會顯示訊息來代替主查詢的值。 |
| 訊息 |資料流驗證查詢沒有傳回任何資料時所顯示的訊息。 如果您不提供任何訊息，會顯示*正在執行評量*狀態訊息。 |


## <a name="two-timelines-tile"></a>雙時間軸圖格
**雙時間軸**圖格以直條圖顯示一段時間內兩個記錄檔查詢的結果。 每個系列會顯示一個圖說文字。 

![雙時間軸圖格](media/view-designer-tiles/tile-two-timelines.png)

| 設定 | 說明 |
|:--- |:--- |
| Name |圖格上方顯示的文字。 |
| 說明 |圖格名稱下方顯示的文字。 |
| 第一個圖 | |
| 圖例 |第一個數列圖說文字下方顯示的文字。 |
| 色彩 |用於第一個數列中資料行的色彩。 |
| 圖表查詢 |針對第一個數列執行的查詢。 以圖表直條來表示每個時間間隔內的記錄計數。 |
| 作業 |對值屬性執行以彙總為圖說文字單一值的作業。<ul><li>平均：所有記錄值的平均值。</li><li>計數：查詢所傳回所有記錄的計數。</li><li>上次取樣：圖表內含最後一個間隔的值。</li><li>最大值：圖表內含間隔的最大值。</li></ul> |
| **第二個圖表** | |
| 圖例 |第二個數列圖說文字下方顯示的文字。 |
| 色彩 |用於第二個數列中資料行的色彩。 |
| 圖表查詢 |針對第二個數列執行的查詢。 以圖表直條來表示每個時間間隔內的記錄計數。 |
| 作業 |對值屬性執行以彙總為圖說文字單一值的作業。<ul><li>平均：所有記錄值的平均值。</li><li>計數：查詢所傳回所有記錄的計數。</li><li>上次取樣：圖表內含最後一個間隔的值。</li><li>最大值：圖表內含間隔的最大值。 |
| **進階** |**> 資料流驗證** |
| 已啟用 |如果應該為圖格啟用資料流驗證，請選取此連結。 如果沒有可用的資料，此方法可提供替代訊息。 您通常會使用此方法在暫存期間 (當檢視已安裝，開始有資料進入) 提供訊息。 |
| 查詢 |執行查詢以判斷資料是否可供檢視使用。 如果查詢未傳回任何結果，會顯示訊息來代替主查詢的值。 |
| 訊息 |資料流驗證查詢沒有傳回任何資料時所顯示的訊息。 如果您不提供任何訊息，會顯示*正在執行評量*狀態訊息。 |


## <a name="next-steps"></a>後續步驟
* 深入了解[記錄檔搜尋](../../azure-monitor/log-query/log-query-overview.md)以支援在圖格中查詢。
* 將[視覺效果組件](view-designer-parts.md)新增至您的自訂檢視。
