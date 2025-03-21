---
title: Azure Log Analytics 中檢視表設計工具組件的參考指南 | Microsoft Docs
description: 藉由使用 Log Analytics 中的檢視設計工具，您可以在 Azure 入口網站中建立能顯示 Log Analytics 工作區中各種資料視覺效果的自訂檢視。 此文章是自訂檢視中可用的視覺效果組件設定的參考指南。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: bwren
ms.openlocfilehash: 61118520e84b53a7669e482694f48d60fd8e67ad
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193870"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>Log Analytics 中的檢視設計工具視覺效果組件參考指南
藉由使用 Azure Log Analytics 中的檢視設計工具，您可以在 Azure 入口網站中建立能呈現來自 Log Analytics 工作區之各種資料視覺效果的自訂檢視。 此文章是自訂檢視中可用的視覺效果組件設定的參考指南。

如需檢視設計工具的詳細資訊，請參閱：

* [檢視設計工具](view-designer.md)：提供檢視設計工具的概觀以及建立和編輯自訂檢視的程序。
* [圖格參考](view-designer-tiles.md)：提供您的自訂檢視中每個可用圖格設定的參考。


下表說明可用的檢視設計工具圖格類型：

| 檢視類型 | 說明 |
|:--- |:--- |
| [查詢清單](#list-of-queries-part) |顯示記錄檔搜尋查詢清單。 您可以選取每個查詢以顯示其結果。 |
| [數字與清單](#number-and-list-part) |標頭會顯示一個數字，可顯示來自記錄搜尋查詢的記錄計數。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。 |
| [兩個數字與清單](#two-numbers-and-list-part) |標頭顯示一個數字，可顯示來自不同記錄搜尋查詢的記錄計數。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。 |
| [環圈圖與清單](#donut-and-list-part) |標頭會顯示一個數字，彙總了記錄檔查詢中的值資料行。 環圈會以圖形方式顯示前三筆記錄的結果。 |
| [兩個時間軸與清單](#two-timelines-and-list-part) |標頭會將一段時間內兩個記錄檔查詢的結果顯示為直條圖，其圖說文字會顯示一個數字，彙總了記錄檔查詢中的值資料行。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。 |
| [資訊](#information-part) |標頭會顯示靜態文字和選擇性連結。 清單會顯示一或多個包含靜態標題和文字的項目。 |
| [折線圖、圖說文字與清單](#line-chart-callout-and-list-part) |標頭會顯示一個折線圖 (其中包含一段時間內記錄查詢中的多個數列)，以及包含彙總值的圖說文字。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。 |
| [折線圖與清單](#line-chart-and-list-part) |標頭會顯示一個折線圖，其中包含一段時間內記錄查詢中的多個數列。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。 |
| [折線圖部分堆疊](#stack-of-line-charts-part) |顯示三個獨立的折線圖，其中包含一段時間內記錄查詢中的多個數列。 |

以下幾節詳細說明圖格類型和其屬性。

## <a name="list-of-queries-part"></a>查詢清單部分
查詢清單組件會顯示記錄搜尋查詢的清單。 您可以選取每個查詢以顯示其結果。 根據預設，檢視會包含單一查詢，您可以選取 [+ 查詢] 來新增額外的查詢。

![查詢清單檢視](media/view-designer-parts/view-list-queries.png)

| 設定 | 說明 |
|:--- |:--- |
| **一般** | |
| 標題 |檢視上方顯示的文字。 |
| 新增群組 |選取此連結即可在檢視中建立新群組 (開始於目前檢視)。 |
| 預先選取的篩選器 |當您選取查詢時，要納入左側篩選窗格中的屬性清單 (以逗號分隔)。 |
| 轉譯模式 |選取查詢時顯示的初始檢視。 您可以在開啟查詢後，選取任何可用的檢視。 |
| **查詢** | |
| 搜尋查詢 |要執行的查詢。 |
| 易記名稱 | 顯示的描述性名稱。 |

## <a name="number-and-list-part"></a>數字與清單組件
標頭會顯示一個數字，可顯示來自記錄搜尋查詢的記錄計數。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。

![查詢清單檢視](media/view-designer-parts/view-number-list.png)

| 設定 | 說明 |
|:--- |:--- |
| **一般** | |
| 群組標題 |檢視上方顯示的文字。 |
| 新增群組 |選取此連結即可在檢視中建立新群組 (開始於目前檢視)。 |
| 圖示 |顯示於標頭中結果旁邊的影像檔。 |
| 使用圖示 |選取此連結可以顯示圖示。 |
| **標題** | |
| 圖例 |標頭上方顯示的文字。 |
| 查詢 |針對標頭執行的查詢。 顯示查詢所傳回的記錄計數。 |
| 點選瀏覽 | 當您按一下標頭時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **清單** | |
| 查詢 |針對清單執行的查詢。 將顯示結果中前 10 筆記錄的前兩個屬性。 第一個屬性是文字值，而第二個屬性是數值。 根據數值資料行的相對值，自動建立長條圖。<br><br>在查詢中使用 `Sort` 命令，將清單中的記錄排序。 若要執行查詢並傳回所有記錄，您可以選取 [查看全部]。 |
| 隱藏圖形 |選取此連結來停用數值資料行右側的圖形。 |
| 啟用走勢圖 |選取此連結可顯示走勢圖 (而非水平橫條圖)。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 色彩 |橫條或走勢圖的色彩。 |
| 名稱和值分隔符號 |要用於將文字屬性剖析成多個值的單一字元分隔符號。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 點選瀏覽 | 當您按一下清項目時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **清單** |**> 資料行標題** |
| Name |圖表第一個資料行上方顯示的文字。 |
| 值 |圖表第二個資料行上方顯示的文字。 |
| **清單** |**> 臨界值** |
| 啟用臨界值 |選取此連結以啟用閾值。 如需詳細資訊，請參閱[一般設定](#thresholds)。 |

## <a name="two-numbers-and-list-part"></a>兩個數字與清單組件
標頭中有兩個數字可顯示不同記錄檔搜尋查詢中的記錄計數。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。

![兩個數字與清單檢視](media/view-designer-parts/view-two-numbers-list.png)

| 設定 | 說明 |
|:--- |:--- |
| **一般** | |
| 群組標題 |檢視上方顯示的文字。 |
| 新增群組 |選取此連結即可在檢視中建立新群組 (開始於目前檢視)。 |
| 圖示 |顯示於標頭中結果旁邊的影像檔。 |
| 使用圖示 |選取此連結可以顯示圖示。 |
| **標題瀏覽** | |
| 點選瀏覽 | 當您按一下標頭時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **標題** | |
| 圖例 |標頭上方顯示的文字。 |
| 查詢 |針對標頭執行的查詢。 顯示查詢所傳回的記錄計數。 |
| **清單** | |
| 查詢 |針對清單執行的查詢。 將顯示結果中前 10 筆記錄的前兩個屬性。 第一個屬性是文字值，而第二個屬性是數值。 根據數值資料行的相對值，自動建立長條圖。<br><br>在查詢中使用 `Sort` 命令，將清單中的記錄排序。 若要執行查詢並傳回所有記錄，您可以選取 [查看全部]。 |
| 隱藏圖形 |選取此連結來停用數值資料行右側的圖形。 |
| 啟用走勢圖 |選取此連結可顯示走勢圖 (而非水平橫條圖)。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 色彩 |橫條或走勢圖的色彩。 |
| 作業 |要對走勢圖執行的作業。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 名稱和值分隔符號 |要用於將文字屬性剖析成多個值的單一字元分隔符號。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 點選瀏覽 | 當您按一下清項目時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **清單** |**> 資料行標題** |
| Name |圖表第一個資料行上方顯示的文字。 |
| 值 |圖表第二個資料行上方顯示的文字。 |
| **清單** |**> 臨界值** |
| 啟用臨界值 |選取此連結以啟用閾值。 如需詳細資訊，請參閱[一般設定](#thresholds)。 |

## <a name="donut-and-list-part"></a>環圈圖與清單組件
標頭會顯示一個數字，彙總了記錄檔查詢中的值資料行。 環圈會以圖形方式顯示前三筆記錄的結果。

![環圈圖與清單檢視](media/view-designer-parts/view-donut-list.png)

| 設定 | 說明 |
|:--- |:--- |
| **一般** | |
| 群組標題 |圖格上方顯示的文字。 |
| 新增群組 |選取此連結即可在檢視中建立新群組 (開始於目前檢視)。 |
| 圖示 |顯示於標頭中結果旁邊的影像檔。 |
| 使用圖示 |選取此連結可以顯示圖示。 |
| **標頭** | |
| 標題 |標頭上方顯示的文字。 |
| 副標題 |標頭上方的標題下方顯示的文字。 |
| **環圈** | |
| 查詢 |針對環圈執行的查詢。 第一個屬性是文字值，而第二個屬性是數值。 |
| 點選瀏覽 | 當您按一下標頭時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **環圈** |**> 中心** |
| 文字 |環圈圖內的值下方顯示的文字。 |
| 作業 |對值屬性執行作業以彙總為單一值。<ul><li>總和：將所有記錄的值相加。</li><li>百分比：[用於中心作業的結果值] 中的值所傳回之記錄數對查詢中記錄總數的比率。</li></ul> |
| 中心作業使用的結果值 |選擇性地選取加號 (+) 以新增一或多個值。 查詢結果僅限於具有您指定之屬性值的記錄。 若沒有新增任何值，則查詢會包含所有記錄。 |
| **其他選項** |**> 色彩** |
| 色彩 1<br>色彩 2<br>色彩 3 |選取環圈中所顯示每個值的色彩。 |
| **其他選項** |**> 進階色彩對應** |
| 欄位值 |如果是環圈中包含的欄位，您可以輸入欄位的名稱來以不同色彩顯示它。 |
| 色彩 |選取唯一欄位的色彩。 |
| **清單** | |
| 查詢 |針對清單執行的查詢。 顯示查詢所傳回的記錄計數。 |
| 隱藏圖形 |選取此連結來停用數值資料行右側的圖形。 |
| 啟用走勢圖 |選取此連結可顯示走勢圖 (而非水平橫條圖)。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 色彩 |橫條或走勢圖的色彩。 |
| 作業 |要對走勢圖執行的作業。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 名稱和值分隔符號 |要用於將文字屬性剖析成多個值的單一字元分隔符號。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 點選瀏覽 | 當您按一下清項目時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **清單** |**> 資料行標題** |
| Name |圖表第一個資料行上方顯示的文字。 |
| 值 |圖表第二個資料行上方顯示的文字。 |
| **清單** |**> 臨界值** |
| 啟用臨界值 |選取此連結以啟用閾值。 如需詳細資訊，請參閱[一般設定](#thresholds)。 |

## <a name="two-timelines-and-list-part"></a>兩個時間軸與清單組件
標頭會將一段時間內兩個記錄檔查詢的結果顯示為直條圖，其圖說文字會顯示一個數字，彙總了記錄檔查詢中的值資料行。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。

![兩個時間軸與清單檢視](media/view-designer-parts/view-two-timelines-list.png)

| 設定 | 說明 |
|:--- |:--- |
| **一般** | |
| 群組標題 |圖格上方顯示的文字。 |
| 新增群組 |選取此連結即可在檢視中建立新群組 (開始於目前檢視)。 |
| 圖示 |顯示於標頭中結果旁邊的影像檔。 |
| 使用圖示 |選取此連結可以顯示圖示。 |
| **標題瀏覽** | |
| 點選瀏覽 | 當您按一下標頭時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **第一個圖表<br>第二個圖表** | |
| 圖例 |第一個數列圖說文字下方顯示的文字。 |
| 色彩 |數列中的直條所使用的色彩。 |
| 查詢 |針對第一個數列執行的查詢。 將以圖表直條來表示每個時間間隔內的記錄計數。 |
| 作業 |對值屬性執行作業以彙總為圖說文字的單一值。<ul><li>總和：所有記錄值的加總。</li><li>平均：所有記錄值的平均值。</li><li>上次取樣：圖表中所含最後一個間隔的值。</li><li>第一次取樣：圖表中所含第一個間隔的值。</li><li>計數：查詢所傳回所有記錄的計數。</li></ul> |
| **清單** | |
| 查詢 |針對清單執行的查詢。 顯示查詢所傳回的記錄計數。 |
| 隱藏圖形 |選取此連結來停用數值資料行右側的圖形。 |
| 啟用走勢圖 |選取此連結可顯示走勢圖 (而非水平橫條圖)。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 色彩 |橫條或走勢圖的色彩。 |
| 作業 |要對走勢圖執行的作業。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 點選瀏覽 | 當您按一下清項目時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **清單** |**> 資料行標題** |
| Name |圖表第一個資料行上方顯示的文字。 |
| 值 |圖表第二個資料行上方顯示的文字。 |
| **清單** |**> 臨界值** |
| 啟用臨界值 |選取此連結以啟用閾值。 如需詳細資訊，請參閱[一般設定](#thresholds)。 |

## <a name="information-part"></a>資訊部分
標頭會顯示靜態文字和選擇性連結。 清單會顯示一或多個包含靜態標題和文字的項目。

![資訊檢視](media/view-designer-parts/view-information.png)

| 設定 | 說明 |
|:--- |:--- |
| **一般** | |
| 群組標題 |圖格上方顯示的文字。 |
| 新增群組 |選取此連結即可在檢視中建立新群組 (開始於目前檢視)。 |
| 色彩 |標頭的背景色彩。 |
| **標頭** | |
| 映像 |標頭中顯示的影像檔案。 |
| 標籤 |標頭中顯示的文字。 |
| **標頭** |**> 連結** |
| 標籤 |連結文字。 |
| Url |連結的 URL。 |
| **資訊項目** | |
| 標題 |對每個項目的標題顯示的文字。 |
| 內容 |對每個項目顯示的文字。 |

## <a name="line-chart-callout-and-list-part"></a>折線圖、圖說文字與清單組件
標頭會顯示一個折線圖，其中包含一段時間內記錄查詢中的多個數列，以及包含彙總值的圖說文字。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。

![折線圖、圖說文字與清單檢視](media/view-designer-parts/view-line-chart-callout-list.png)

| 設定 | 說明 |
|:--- |:--- |
| **一般** | |
| 群組標題 |圖格上方顯示的文字。 |
| 新增群組 |選取此連結即可在檢視中建立新群組 (開始於目前檢視)。 |
| 圖示 |顯示於標頭中結果旁邊的影像檔。 |
| 使用圖示 |選取此連結可以顯示圖示。 |
| **標頭** | |
| 標題 |標頭上方顯示的文字。 |
| 副標題 |標頭上方的標題下方顯示的文字。 |
| **折線圖** | |
| 查詢 |針對折線圖執行的查詢。 第一個屬性是文字值，而第二個屬性是數值。 此查詢通常是使用 *measure* 關鍵字來彙總結果。 如果查詢使用 *interval* 關鍵字，則圖表的 X 軸會使用此時間間隔。 如果查詢未包含 *interval* 關鍵字，則 X 軸會使用每小時的間隔。 |
| 點選瀏覽 | 當您按一下標頭時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **折線圖** |**> 圖說文字** |
| 圖說文字標題 |圖說文字值上方顯示的文字。 |
| 系列名稱 |將用於圖說文字值之系列的屬性值。 如未提供任何系列，則會使用查詢中的所有記錄。 |
| 作業 |對值屬性執行作業以彙總為圖說文字的單一值。<ul><li>平均：所有記錄值的平均值。</li><li>計數：查詢所傳回所有記錄的計數。</li><li>上次取樣：圖表中所含最後一個間隔的值。</li><li>最大值：圖表內含間隔中的最大值。</li><li>最小值︰圖表內含間隔中的最小值。</li><li>總和：所有記錄值的加總。</li></ul> |
| **折線圖** |**> Y 軸** |
| 使用對數刻度 |選取此連結可對 Y 軸使用對數刻度。 |
| Units |指定要查詢傳回之值的單位。 這項資訊用來顯示圖表上的標籤，指出值的類型，並選擇性地用來轉換值。 *單位*類型指定單位的分類，並定義可用的*目前的單位*類型值。 如果您選取*轉換成*中的值，則數值會從*目前單位*類型轉換為*轉換成*類型。 |
| 自訂標籤 |Y 軸的顯示文字，就在*單位*類型的標籤旁。 如未指定任何標籤，只會顯示*單位*類型。 |
| **清單** | |
| 查詢 |針對清單執行的查詢。 顯示查詢所傳回的記錄計數。 |
| 隱藏圖形 |選取此連結來停用數值資料行右側的圖形。 |
| 啟用走勢圖 |選取此連結可顯示走勢圖 (而非水平橫條圖)。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 色彩 |橫條或走勢圖的色彩。 |
| 作業 |要對走勢圖執行的作業。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 名稱和值分隔符號 |要用於將文字屬性剖析成多個值的單一字元分隔符號。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 點選瀏覽 | 當您按一下清項目時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **清單** |**> 資料行標題** |
| Name |圖表第一個資料行上方顯示的文字。 |
| 值 |圖表第二個資料行上方顯示的文字。 |
| **清單** |**> 臨界值** |
| 啟用臨界值 |選取此連結以啟用閾值。 如需詳細資訊，請參閱[一般設定](#thresholds)。 |

## <a name="line-chart-and-list-part"></a>折線圖與清單組件
標頭會顯示一個折線圖，其中包含一段時間內記錄查詢中的多個數列。 清單會顯示查詢的前 10 筆結果，並包含表示數值資料行的相對值或一段時間內之變更的圖表。

![折線圖與清單檢視](media/view-designer-parts/view-line-chart-callout-list.png)

| 設定 | 說明 |
|:--- |:--- |
| **一般** | |
| 群組標題 |圖格上方顯示的文字。 |
| 新增群組 |選取此連結即可在檢視中建立新群組 (開始於目前檢視)。 |
| 圖示 |顯示於標頭中結果旁邊的影像檔。 |
| 使用圖示 |選取此連結可以顯示圖示。 |
| **標頭** | |
| 標題 |標頭上方顯示的文字。 |
| 副標題 |標頭上方的標題下方顯示的文字。 |
| **折線圖** | |
| 查詢 |針對折線圖執行的查詢。 第一個屬性是文字值，而第二個屬性是數值。 此查詢通常是使用 *measure* 關鍵字來彙總結果。 如果查詢使用 *interval* 關鍵字，則圖表的 X 軸會使用此時間間隔。 如果查詢未包含 *interval* 關鍵字，則 X 軸會使用每小時的間隔。 |
| 點選瀏覽 | 當您按一下標頭時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **折線圖** |**> Y 軸** |
| 使用對數刻度 |選取此連結可對 Y 軸使用對數刻度。 |
| Units |指定要查詢傳回之值的單位。 這項資訊用來顯示圖表上的標籤，指出值的類型，並選擇性地用來轉換值。 *單位*類型指定單位的分類，並定義可用的*目前的單位*類型值。 如果您選取*轉換成*中的值，則數值會從*目前單位*類型轉換為*轉換成*類型。 |
| 自訂標籤 |Y 軸的顯示文字，就在*單位*類型的標籤旁。 如未指定任何標籤，只會顯示*單位*類型。 |
| **清單** | |
| 查詢 |針對清單執行的查詢。 顯示查詢所傳回的記錄計數。 |
| 隱藏圖形 |選取此連結來停用數值資料行右側的圖形。 |
| 啟用走勢圖 |選取此連結可顯示走勢圖 (而非水平橫條圖)。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 色彩 |橫條或走勢圖的色彩。 |
| 作業 |要對走勢圖執行的作業。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 名稱和值分隔符號 |要用於將文字屬性剖析成多個值的單一字元分隔符號。 如需詳細資訊，請參閱[一般設定](#sparklines)。 |
| 點選瀏覽 | 當您按一下清項目時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **清單** |**> 資料行標題** |
| Name |圖表第一個資料行上方顯示的文字。 |
| 值 |圖表第二個資料行上方顯示的文字。 |
| **清單** |**> 臨界值** |
| 啟用臨界值 |選取此連結以啟用閾值。 如需詳細資訊，請參閱[一般設定](#thresholds)。 |

## <a name="stack-of-line-charts-part"></a>折線圖部分堆疊
堆疊的折線圖會顯示三個獨立的折線圖，其中包含一段時間內記錄查詢中的多個系列，如下所示：

![折線圖堆疊](media/view-designer-parts/view-stack-line-charts.png)

| 設定 | 說明 |
|:--- |:--- |
| **一般** | |
| 群組標題 |圖格上方顯示的文字。 |
| 新增群組 |選取此連結即可在檢視中建立新群組 (開始於目前檢視)。 |
| 圖示 |顯示於標頭中結果旁邊的影像檔。 |
| **圖表 1<br>圖表 2<br>圖表 3** |**> 標頭** |
| 標題 |圖表上方顯示的文字。 |
| 副標題 |圖表上方的標題下方顯示的文字。 |
| **圖表 1<br>圖表 2<br>圖表 3** |**折線圖** |
| 查詢 |針對折線圖執行的查詢。 第一個屬性是文字值，而第二個屬性是數值。 此查詢通常是使用 *measure* 關鍵字來彙總結果。 如果查詢使用 *interval* 關鍵字，則圖表的 X 軸會使用此時間間隔。 如果查詢未包含 *interval* 關鍵字，則 X 軸會使用每小時的間隔。 |
| 點選瀏覽 | 當您按一下標頭時所採取的動作。  如需詳細資訊，請參閱[一般設定](#click-through-navigation)。 |
| **圖表** |**> Y 軸** |
| 使用對數刻度 |選取此連結可對 Y 軸使用對數刻度。 |
| Units |指定要查詢傳回之值的單位。 這項資訊用來顯示圖表上的標籤，指出值的類型，並選擇性地用來轉換值。 *單位*類型指定單位的分類，並定義可用的*目前的單位*類型值。 如果您選取*轉換成*中的值，則數值會從*目前單位*類型轉換為*轉換成*類型。 |
| 自訂標籤 |Y 軸的顯示文字，就在*單位*類型的標籤旁。 如未指定任何標籤，只會顯示*單位*類型。 |

## <a name="common-settings"></a>一般設定
以下小節說明幾個視覺化組件的通用設定。

### <a name="name-value-separator"></a>名稱和值分隔符號
名稱和值分隔符號是單一字元分隔符號，用來將來自清單查詢的文字屬性剖析成多個值。 如果您指定分隔符號，您可以在 [名稱] 方塊中提供每個欄位的名稱 (以相同的分隔符號分隔)。

例如，考慮名為 *Location* 的屬性，其包含 *Redmond-Building 41* 和 *Bellevue-Building 12* 等值。 您可以指定虛線 (-) 作為名稱和值分隔符號，以及指定 *City-Building* 作為名稱。 此方法會將每個值剖析成兩個名為 *City* 和 *Building* 的屬性。

### <a name="click-through-navigation"></a>點選瀏覽
點選瀏覽會定義當您按一下標頭或檢視中的清單項目時，要採取哪個動作。  這樣會在[記錄搜尋入口網站](../../azure-monitor/log-query/portals.md)中開啟查詢，或啟動另一個檢視。

下表描述點選瀏覽的設定。

| 設定           | 說明 |
|:--|:--|
| 記錄搜尋 (自動) | 當您選取標頭項目時要執行的記錄搜尋。  項目也會以相同的記錄搜尋作為基礎。
| 記錄搜尋        | 當您在清單中選取項目時要執行的記錄搜尋。  將查詢輸入 [瀏覽查詢] 方塊。   使用 *{selected item}* 來包含使用者所選項目的語法。  例如，如果查詢具有名為 *Computer* 的資料行且瀏覽查詢是 *{selected item}*，則會在您選取電腦時執行 *Computer="MyComputer"* 之類的查詢。 如果瀏覽查詢是 *Type=Event {selected item}*，則會執行 *Type=Event Computer="MyComputer"*。 |
| 檢視              | 當您選取標頭項目或清單項目時開啟的檢視。  在 [檢視名稱] 方塊中，選取工作區中的檢視名稱。 |



### <a name="sparklines"></a>走勢圖
走勢圖是說明一段時間內清單項目值的小型折線圖。 針對包含清單的視覺化組件，您可以選取要顯示表示數值資料行相對值的水平橫條圖，或顯示表示其一段時間內的值的走勢圖。

下表描述走勢圖的設定：

| 設定 | 說明 |
|:--- |:--- |
| 啟用走勢圖 |選取此連結可顯示走勢圖 (而非水平橫條圖)。 |
| 作業 |如果啟用走勢圖，此即為對清單中每個屬性執行的作業，以計算走勢圖的值。<ul><li>上次取樣：時間間隔內系列的最後一個值。</li><li>最大值：時間間隔內系列的最大值。</li><li>最小值︰時間間隔內系列的最小值。</li><li>總和：時間間隔內系列的值總和。</li><li>摘要：使用相同的 `measure` 命令作為標頭中的查詢。</li></ul> |

### <a name="thresholds"></a>閾值
藉由使用閾值，您可以在清單中的每個項目旁顯示彩色的圖示。 閾值是當項目超過特定值或落在特定範圍內的快速視覺指標。 例如，您可以為含有可接受值的項目顯示綠色圖示，如果值是在指示警告的範圍內就顯示黃色圖示，如果值超出錯誤值，則顯示紅色圖示。

當您針對組件啟用臨界值時，必須指定一或多個臨界值。 如果項目的值大於某個閾值，並低於下一個閾值，就使用該值的色彩。 如果項目大於最高閾值，就會使用另一個色彩。 

每個臨界值組都有一個具有 **Default** 值的臨界值。 如果沒有超出其他值，就會設定這個色彩。 您可以選取 [新增] \(+) 或 [刪除] \(x) 按鈕來新增或移除閾值。

下表描述閾值的設定：

| 設定 | 說明 |
|:--- |:--- |
| 啟用臨界值 |選取此連結可在每個值的左側顯示色彩圖示。 圖示表示相對於指定的閾值，值的健全狀況。 |
| Name |閾值的名稱。 |
| 閾值 |閾值。 每個清單項目的健康狀態色彩都是設定為項目值所超出之最高閾值的色彩。 如果未超過任何閾值，即會使用預設色彩。 |
| 色彩 |表示閾值的色彩。 |

## <a name="next-steps"></a>後續步驟
* 深入了解[記錄檔搜尋](../../azure-monitor/log-query/log-query-overview.md)以支援在視覺效果組件中查詢。
