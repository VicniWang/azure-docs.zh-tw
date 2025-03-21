---
title: 使用 Jupyter Notebook 和 Kqlmagic 來分析資料
description: 本主題會說明如何使用 Jupyter Notebook 和 Kqlmagic 來分析資料
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 83c4ae7ff942579d55a5855c24a28809f60fdc62
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382455"
---
# <a name="analyze-data-using-jupyter-notebook-and-kql-magic"></a>使用 Jupyter Notebook 和 KQL magic 來分析資料

Jupyter Notebook 是開放原始碼的 Web 應用程式，可讓您建立及共用含有即時程式碼、方程式、視覺效果和敘述文字的文件。 使用方式包含資料清理和轉換、數字模擬、統計模型、資料視覺效果和機器學習。
[Jupyter Notebook](https://jupyter.org/) 支援 magic 函式，可藉由支援其他命令來擴展核心的功能。 KQL magic 是一種命令，可在 Jupyter Notebook 中擴展 Python 核心的功能，讓您能夠以原生方式執行 Kusto 語言查詢。 您可以輕鬆地結合 Python 和 Kusto 查詢語言，以使用與 `render` 命令整合在一起的豐富 Plot.ly 程式庫，來查詢並以視覺方式呈現資料。 支援用於執行查詢的資料來源。 這些資料來源包含 Azure 資料總管 (兼具速度與極佳調整能力的資料探索服務，適用於記錄和遙測資料)，以及 Log Analytics 和 Application Insights。 KQL magic 也可以與 Azure Notebooks、Jupyter Lab 及 Visual Studio Code Jupyter 延伸模組搭配運作。

## <a name="prerequisites"></a>必要條件

- 屬於 Azure Active Directory (AAD) 成員的組織電子郵件帳戶。
- 本機電腦上已安裝 Jupyter Notebook，或使用 Azure Notebook 並複製範例 [Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>安裝 KQL magic 文件庫

1. 安裝 KQL magic：

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > 使用 Azure Notebooks 時，不需要執行此步驟。

1. 載入 KQL magic：

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>連線至 Azure 資料總管協助叢集

使用下列命令來連線至裝載於「協助」叢集的「範例」資料庫。 若為非 Microsoft AAD 使用者，請將租用戶名稱 `Microsoft.com` 替換為您的 AAD 租用戶。

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>查詢並以視覺方式呈現

使用 [render 運算子](/azure/kusto/query/renderoperator)查詢資料，並使用 ploy.ly 程式庫將資料視覺化。 此查詢與視覺效果會提供使用原生 KQL 的整合式體驗。 Kqlmagic 支援大部分的圖表，但 `timepivot`、`pivotchart` 和 `ladderchart` 除外。 所有屬性皆支援轉譯，但 `kind`、`ysplit` 和 `accumulate` 除外。 

### <a name="query-and-render-piechart"></a>查詢和轉譯圓形圖

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>查詢和轉譯時間圖

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> 這些圖表具有互動功能。 選取時間範圍即可放大特定時間。

### <a name="customize-the-chart-colors"></a>自訂圖表色彩

如果您不喜歡預設的調色盤，請使用調色盤選項自訂圖表。 可用的調色盤可以在這裡找到：[為 KQL magic 查詢圖表結果選擇調色盤](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. 獲取調色盤清單：

    ```python
    %kql --palettes -popup_window
    ```

1. 選取 `cool` 調色盤，然後重新轉譯查詢：

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>使用 Python 將查詢參數化

KQL magic 可讓您簡單地在 Kusto 查詢語言與 Python 之間進行交換。 若要深入了解：[使用 Python 將 KQL magic 查詢參數化](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>在 KQL 查詢中使用 Python 變數

您可以在查詢中使用 Python 變數的值來篩選資料：

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>將查詢結果轉換為 Pandas 資料框架

您可以在 Pandas 資料框架中存取 KQL 查詢的結果。 依變數 `_kql_raw_result_` 存取最後一次執行查詢的結果，並輕鬆地將結果轉換為 Pandas 資料框架，如下所示：

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>範例

在許多分析案例中，您可能會想要建立包含許多查詢的可重複使用 Notebook，並將某個查詢的結果饋送給後續查詢。 下列範例會使用 Python 變數 `statefilter` 來篩選資料。

1. 執行查詢來檢視具有最大 `DamageProperty` 的前 10 個狀態：

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. 執行查詢來擷取排行最前的狀態，並將它設定到 Python 變數中：

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. 使用 `let` 陳述式與 Python 變數來執行查詢：

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. 執行 help 命令：

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>後續步驟

執行 help 命令來瀏覽下列包含所有支援功能的範例 Notebook：
- [開始使用適用於 Azure 資料總管的 KQL magic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [開始使用適用於 Application Insights 的 KQL magic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [開始使用適用於 Log Analytics 的 KQL magic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [使用 Python 將 KQL magic 查詢參數化](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [為 KQL magic 查詢圖表結果選擇調色盤](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
