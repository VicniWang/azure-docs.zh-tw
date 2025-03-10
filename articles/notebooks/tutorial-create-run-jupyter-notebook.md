---
title: 教學課程：在 Azure 上建立和執行 Jupyter Notebook
description: 如何在 Azure Notebooks 中建立和執行 Jupyter Notebook，以示範資料科學的線性迴歸程序。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: dcee7df0b5da53bd7014a26f1f09695fe874833d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849649"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>教學課程：使用 Python 建立和執行 Jupyter Notebook

本教學課程將逐步引導您完成使用 Azure Notebooks 來建立完整 Jupyter Notebook 程序，以示範簡單的線性迴歸。 在本教學課程的過程中，您會熟悉 Jupyter Notebook UI，其中包括建立不同的資料格、執行資料格，以及以投影片放映方式呈現 Notebook。

在 [GitHub - Azure Notebooks 範例](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)上可找到已完成的 Notebook。 不過，本教學課程會從新的專案和空白的 Notebook 開始，讓您得以體驗逐步建立。

## <a name="create-the-project"></a>建立專案

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入。 (如需詳細資訊，請參閱[快速入門 - 登入 Azure Notebooks](quickstart-sign-in-azure-notebooks.md))。

1. 從您的公用設定檔頁面中，選取頁面頂端的 [我的專案]：

    ![瀏覽器視窗頂端的 [我的專案] 連結](media/quickstarts/my-projects-link.png)

1. 在 [我的專案] 頁面上，選取 [+ 新增專案] \(鍵盤快速鍵：n\)；只有在瀏覽器視窗很窄時，該按鈕才會顯示為 **+**：

    ![[我的專案] 頁面上的 [新增專案] 命令](media/quickstarts/new-project-command.png)

1. 在顯示的 [建立新專案] 快顯視窗中，輸入或設定下列詳細資料，然後選取 [建立]：

    - **專案名稱**：線性迴歸範例 - 蟋蟀鳴叫
    - **專案識別碼**：linear-regression-example
    - **公用專案**：(已清除)
    - **建立 README.md**：(已清除)

1. 幾分鐘後，Azure Notebooks 會帶您瀏覽至新的專案。

## <a name="create-the-data-file"></a>建立資料檔案

您在 Notebook 中建立的線性迴歸模型，會從您專案中的 cricket_chirps.csv 檔案繪製資料。 您可藉由從 [GitHub - Azure Notebooks 範例] (https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps) 複製此檔案，或藉由直接輸入資料來建立該檔案。 下列幾節會說明這兩種方法。

### <a name="upload-the-data-file"></a>上傳資料檔案

1. 在 Azure Notebooks 中您的專案儀表板上，選取 [上傳] > [從 URL]
1. 在快顯視窗中，於 [檔案 URL] 中輸入下列 URL，並於 [檔案名稱] 中輸入 cricket_chirps.csv，然後選取 [完成]。

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. cricket_chirps.csv 檔案現在應該出現在您專案的檔案清單中：

    ![專案檔案清單中顯示的新建 CSV 檔案](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>從頭建立檔案

1. 在 Azure Notebooks 中您的專案儀表板上，選取 [+ 新增] > [空白檔案]
1. 專案的檔案清單中會出現一個欄位。 輸入 cricket_chirps.csv 並且按 Enter。
1. 以滑鼠右鍵按一下 cricket_chirps.csv，然後選取 [編輯檔案]。
1. 在顯示的編輯器中，輸入以下資料：

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. 選取 [儲存檔案] 以儲存檔案並返回專案儀表板。

## <a name="install-project-level-packages"></a>安裝專案層級套件

在 Notebook 中，您可以在程式碼資料格隨時使用 `!pip install` 之類的命令，以安裝必要的套件。 不過，這類命令會在您每次執行 Notebook 的程式碼資料格時執行，並可能需要相當長的時間。 基於這個理由，您可以使用 `requirements.txt` 檔案改為在專案層級安裝套件。

1. 使用[從頭建立檔案](#create-a-file-from-scratch)中的程序來建立名為 `requirements.txt` 的檔案，其中包含下列內容：

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    如[上傳資料檔案](#upload-the-data-file)所述，您也可以從本機電腦上傳 `requirements.txt` 檔案。

1. 在專案儀表板上，選取 [專案設定]。
1. 在出現的快顯視窗中，選取 [環境] 索引標籤，然後選取 [+新增]。
1. 在 [環境設定步驟] 底下的第一個下拉式控制項 (作業) 中，選擇 **Requirements.txt**。
1. 在第二個下拉式控制項 (檔案名稱) 中，選擇 *requirements.txt* (您所建立的檔案)。
1. 在第三個下拉式控制項 (Python 版本) 中，選擇 [Python 3.6 版]。
1. 選取 [ **儲存**]。

![用於指定 requirements.txt 檔案的 [專案設定環境] 索引標籤](media/tutorial/tutorial-requirements-txt.png)

透過這個安裝步驟，您在專案中執行任何的 Notebook 都會在這些套件安裝所在的環境中執行。

## <a name="create-and-run-a-notebook"></a>建立及執行筆記本

備妥資料檔案和專案環境設定，您現在即可建立並開啟 Notebook。

1. 在專案儀表板上，選取 [+ 新增] > [Notebook]。
1. 在快顯視窗中，針對 [項目名稱] 輸入 [線性迴歸範例 - Cricket Chirps.ipynb]，針對語言選擇 [Python 3.6]，然後選取 [新增]。
1. 在新的 Notebook 出現在檔案清單之後，選取它來啟動 Notebook。 自動開啟新的瀏覽器索引標籤。
1. 因為您的環境設定中有 requirements.txt 檔案，所以您會看到訊息「等候您的容器完成準備」。 您可以選取 [確定] 以關閉訊息並繼續在 Notebook 中操作；不過，在完整設定環境之前，您無法執行程式碼資料格。
1. 以單一空白程式碼資料格作為預設值，在 Jupyter 介面中開啟 Notebook。

    [![Azure Notebooks 中新 Notebook 的起始檢視](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Notebook 介面導覽

執行 Notebook，您可以新增程式碼和 Markdown 資料格，執行這些資料格，以及管理 Notebook 的作業。 不過，首先倒是值得花幾分鐘的時間來熟悉介面。 如需完整的文件，請選取 [說明] > [Notebook 說明] 功能表命令。

在視窗的頂端，您會看到下列項目：

(A) Notebook 的名稱，按一下即可加以編輯。
(B) 用以瀏覽至包含專案和專案儀表板的按鈕，其可在您的瀏覽器中開啟新的索引標籤。
(C) 一個功能表，內含搭配 Notebook 運作的命令。
(D) 內含一般作業快速鍵的工具列。
(E) 編輯畫布包含資料格。
(F) 表示 Notebook 是否受信任的指標 (預設值為 [不受信任])。
(G) 用來執行 Notebook 的核心以及活動指標。

[![Jupyter 介面的主要 UI 區域](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter 會提供主要 UI 元素的內建導覽。 選取 [說明] > [使用者介面導覽] 命令並點選快顯功能表，即可開始導覽。

功能表命令群組如下所示：

| 功能表 | 說明 |
| --- | --- |
| 檔案 | 用以管理 Notebook 檔案的命令，包括建立和複製 Notebook、顯示預覽列印，以及使用各種格式下載 Notebook 的命令。 |
| Edit | 用以剪下、複製和貼上資料格、尋找和取代值、管理資料格附件，以及插入映像的一般命令。  |
| 檢視 | 用以控制 Jupyter UI 的各部分可見性的命令。 |
| 插入 | 用以在目前資料格上方或下方插入新資料格的命令。 建立 Notebook 時，您可以使用這些命令。 |
| 資料格 | 各種 [Run] 命令會以不同的組合執行一或多個資料格。 [資料格類型] 命令可變更 [程式碼]、[Markdown] 及 [原始 NBConvert] (純文字) 之間的資料格類型。 [目前輸出] 和 [所有輸出] 命令可控制如何顯示來自執行程式碼的輸出，並且包含清除所有輸出的命令。 |
| 核心 | 用以管理在核心中如何執行程式碼的命令，以及 [變更核心] 用來變更語言或用來執行 Notebook 的 Python 版本。 |
| 資料 | 從專案或工作階段上傳和下載檔案的命令。 請參閱[使用專案資料檔案](work-with-project-data-files.md) |
| Widget | 用以管理 [Jupyter Widget](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html) 的命令，其可針對視覺效果、對應及繪圖提供額外功能。|
| 說明 | 可提供 Jupyter 介面說明和文件的命令。 |

工具列上大部分的命令都有對等的功能表命令。 已在[共用及呈現 Notebook](present-jupyter-notebooks-slideshow.md)討論過的 [輸入/編輯 RISE 投影片] 為其中一個例外狀況。

您在後續各節中填入 Notebook 時會使用其中一些命令。

## <a name="create-a-markdown-cell"></a>建立 Markdown 資料格

1. 按一下 Notebook 畫布上顯示的第一個空白資料格。 根據預設，資料格是一種**程式碼**類型，這表示其設計包含適用於所選核心的可執行程式碼 (Python、R 或 F#)。 目前類型會顯示在工具列上的類型下拉式清單中：

    ![資料格類型工具列下拉式清單](media/tutorial/tutorial-cell-type-drop-down.png)

1. 使用工具列下拉式清單將資料格類型變更為 [Markdown]；或者，使用 [資料格] > [資料格類型] >  [Markdown] 功能表命令：

    ![資料格類型功能表命令](media/tutorial/tutorial-cell-type-menu.png)

1. 在資料格中按一下以開始編輯，然後輸入下列 Markdown：

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](http://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. 若要將 Markdown 轉譯成瀏覽器適用的 HTML，請選取工具列上的 [執行] 命令，或使用 [資料格] > [執行資料格] 命令。 用於格式化的 Markdown 程式碼和連結現在會如您所預期出現在瀏覽器中。

1. 當您在 Notebook 中執行最後資料格時，Jupyter 會在您執行的資料格下面自動建立新的資料格。 使用下列 Markdown 重複這一節的步驟，將更多 Markdown 放入此資料格中：

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. 若要再次編輯 Markdown，連按兩下已轉譯的資料格。 若要在進行變更之後再次轉譯 HTML，請執行資料格。

## <a name="create-a-code-cell-with-commands"></a>使用命令建立程式碼資料格

如先前的 Markdown 資料格所述，您可以直接在 Notebook 中包含命令。 您可以使用命令來安裝封裝、執行 curl 或 wget 來擷取資料，或任何其他項目。 Jupyter Notebook 會在 Linux 虛擬機器中有效地執行，所以您有完整的 Linux 命令集可供使用。

1. 在您於前一個 Markdown 資料格上使用 [Run] 之後出現的程式碼資料格中，輸入以下命令。 如果您沒有看到新的資料格，可使用 [插入] > [在下方插入資料格]，或使用工具列上的 **+** 按鈕加以建立。

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. 在執行資料格之前，使用工具列上的 **+** 按鈕來建立新的資料格，將它設定為 Markdown，然後輸入以下說明：

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. 選取 [資料格] > [全部執行] 命令，以在 Notebook 中執行所有資料格。 請注意，Markdown 資料格會轉譯為 HTML，而命令會在核心中執行，以及觀察如 Markdown 本身所述的核心指標：

    ![Notebook 核心的忙碌指標](media/tutorial/tutorial-kernel-busy.png)

1. 所有 `pip install` 命令也會花一些時間執行，但因為您已在專案環境中安裝這些套件 (且因為它們也預設包含在 Azure Notebook 中)，所以您會看到許多訊息表示「需求已滿足」。 該輸出全都可能擾亂視覺，因此請選取該資料格 (使用按一下)，然後使用 [資料格] > [資料格輸出] > [切換] 來隱藏輸出。 您也可以使用該相同子功能表上的 [清除] 命令，完全移除輸出。

    [切換] 命令只會隱藏來自資料格的最新輸出；如果您再次執行資料格，輸出就會重新出現。

1. 因為套件已安裝於專案環境中，所以使用 `#` 來註解排除 `! pip install` 命令，如此這些命令即可留在 Notebook 中作為指示性資料，但不需任何時間來執行，也不會產生不必要的輸出。 在此情況下，在 Notebook 中保留註解的命令，也可指出 Notebook 的相依性。

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>建立其餘的資料格

若要填入 Notebook 的其餘部分，您可以接著建立一系列的 Markdown 和程式碼資料格。 對於下面所列的每個資料格，首先建立新的資料格，接著設定類型，然後在內容中貼上。

雖然您可以在建立每個資料格之後，等待執行 Notebook，但是在建立每個資料格時加以執行很有趣。 並非所有資料格會顯示輸出；如果您沒有看到任何錯誤，則會假設資料格正常執行。

每個程式碼資料格會取決於已在先前資料格中執行的程式碼，而如果您忘了執行其中一個資料格，則後續資料格可能會產生錯誤。 如果您發現您忘了執行資料格，請先嘗試使用 [資料格] > [執行上述全部]，再執行目前的資料格。

如果您看到非預期的結果 (這有可能！)，請檢查每個資料格是否依照需要設定為 [程式碼] 或 [Markdown]。 例如，「語法無效」錯誤通常發生於您已在程式碼資料格中輸入 Markdown 時。

1. Markdown 資料格：

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. 程式碼資料格；執行時，將資料表內容顯示為輸出。 您可藉由註解排除 `print` 陳述式來隱藏輸出。

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > 您可能會看到此程式碼發出的「numpy.dtype 大小已變更」相關警告；您可以放心地忽略警告。

1. Markdown 資料格：

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. 程式碼資料格；執行時，此資料格沒有輸出。

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown 資料格：

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept`).
    ```

1. 程式碼資料格；執行時，此資料格會顯示輸出 `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`。

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown 資料格：

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_` and `coef_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. 程式碼資料格；執行時，此資料格會顯示 `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` 之類的結果。

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown 資料格：

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown 資料格：

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. 程式碼資料格；執行時，此資料格會產生繪圖。 如果您第一次沒有看到此繪圖 (改為看到「具有 1 個座標軸的圖大小 640x480」)，請再次執行資料格。

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![來自 matplotlib 程式碼的繪圖輸出](media/tutorial/tutorial-plot-output.png)

1. Markdown 資料格：

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>清除輸出並重新執行所有資料格

遵循上一節中的步驟來填入整個 Notebook 之後，您已在線性迴歸的完整教學課程內容中，建立了執行中程式碼片段。 程式碼和文字的此種直接結合是 Notebook 的其中一項重要優點！

立即嘗試重新執行整個 Notebook：

1. 選取 [核心] > [重新啟動和清除輸出]，以清除所有核心的工作階段資料和所有資料格。 當您已完成 Notebook 時，此命令永遠是一個很好的命令，只為了確保您尚未在程式碼資料格之間建立任何奇怪的相依性。

1. 使用 [資料格] > [全部執行] 以重新執行 Notebook。 請注意，核心指標會在執行程式碼時填入。

    如果您有任何執行太久或者卡住的程式碼，您可以使用 [核心] > [中斷] 命令來停止核心。

1. 捲動 Notebook 來檢查結果。 (如果繪圖再次未出現，請重新執行該資料格。)

## <a name="save-halt-and-close-the-notebook"></a>儲存、停止及關閉 Notebook

在您編輯 Notebook 期間，您可以使用 [檔案] > [儲存和檢查點] 命令或工具列上的 [儲存] 按鈕，儲存其目前狀態。 「檢查點」會建立一個快照集，您可以在工作階段期間隨時加以還原。 檢查點可讓您進行一系列的實驗性變更，而如果這些變更無法運作，您只要使用 [檔案] > [還原至檢查點] 命令，即可還原至檢查點。 替代方法是建立額外的資料格，以及註解排除任何您不想要執行的程式碼；任一種方法都適用。

您也可以隨時使用 [檔案] > [製作複本] 命令，製作 Notebook 的目前狀態複本，使其成為您專案中的新檔案。 該複本會在新的瀏覽器索引標籤中自動開啟。

當您處理好 Notebook 時，請使用 [檔案] > [關閉並停止] 命令，這個命令會關閉 Notebook 並關閉已在執行它的核心。 Azure Notebooks 接著會自動關閉瀏覽器索引標籤。

## <a name="debug-notebooks-using-visual-studio-code"></a>使用 Visual Studio Code 進行 Notebook 偵錯

如果 Notebook 中的程式碼資料格並未以您預期的方式運作，則可能會有程式碼錯誤 (bug) 或其他缺失。 不過，除了使用 `print` 陳述式來顯示變數值，Jupyter 環境通常不會提供任何偵錯工具。

幸運的是，您可以在這裡下載 Notebook 的 *.ipynb* 檔案，然後在 Visual Studio Code 中使用 Python 擴充功能開啟它。 擴充功能會直接將 Notebook 匯入為單一程式碼檔案，在註解中保留您的 Markdown 資料格。 一旦您匯入 Notebook 後，就可以使用 Visual Studio Code 偵錯工具來逐步執行程式碼、設定中斷點、檢查狀態，以及進行其他操作。 在更正程式碼之後，您接著要從 Visual Studio Code 將 *.ipynb* 檔案匯出，然後將它上傳回 Azure Notebooks。

如需詳細資訊，請參閱 Visual Studio Code 文章中的[針對 Jupyter Notebook 進行偵錯](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook)。

另請參閱 [Visual Studio Code - Jupyter 支援](https://code.visualstudio.com/docs/python/jupyter-support)以了解適用於 Jupyter Notebook 的 Visual Studio Code。

## <a name="next-steps"></a>後續步驟

- [探索範例筆記本](azure-notebooks-samples.md)

操作說明文章：

- [建立及複製專案](create-clone-jupyter-notebooks.md)
- [設定和管理專案](configure-manage-azure-notebooks-projects.md)
- [從筆記本內安裝套件](install-packages-jupyter-notebook.md)
- [放映投影片](present-jupyter-notebooks-slideshow.md)
- [使用資料檔案](work-with-project-data-files.md)
- [存取資料資源](access-data-resources-jupyter-notebooks.md)
- [使用 Azure 機器學習服務](use-machine-learning-services-jupyter-notebooks.md)
