---
title: 什麼是
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio 是可從已準備就緒可供使用的演算法與模組程式庫快速建置模型的拖放工具。
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: 44f2c24f1fe53b6fadabf23f03a6feda75c87883
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738278"
---
# <a name="what-is-azure-machine-learning-studio"></a>什麼是 Azure Machine Learning Studio？
Microsoft Azure Machine Learning Studio 是共同作業式的拖放工具，您可以用來依據您的資料建置、測試及部署預測分析解決方案。 Machine Learning Studio 會以 Web 服務方式發佈模型，讓自訂應用程式或 BI 工具 (例如 Excel) 都能夠很容易地使用。

Machine Learning Studio 讓資料科學、預測分析、雲端資源和您的資料齊聚一堂！


## <a name="the-machine-learning-studio-interactive-workspace"></a>Machine Learning Studio 互動式工作區
若要開發預測分析模型，您通常會使用一或多個來源的資料，透過各種資料操作和統計函數來轉換和分析該資料，然後產生一組結果。 開發此類模型是一種反覆式過程。 隨著您修改各種函數及其參數，結果會不斷收斂，直到您對已訓練的有效模型感到滿意為止。

**Azure Machine Learning Studio** 提供互動式的視覺化工作區，讓您輕鬆建置、測試和反覆運算預測分析模型。 您可以將「資料集」和分析「模組」拖放到互動式畫布，將它們連接在一起以構成「實驗」，然後在 Machine Learning Studio 中執行。 若要反覆調整模型設計，請編輯實驗，儲存複本 (若需要)，然後重新提交。 當您準備好時，可以將您的「訓練實驗」轉換成「預測實驗」，然後發佈為「Web 服務」，讓其他人可以存取您的模型。

不需要設計程式，只要在視覺上連接資料集和模組，即可建構預測分析模型。

![Azure Machine Learning Studio 圖表：建立實驗、讀取許多來源的資料、寫入評分的資料及寫入模型。](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>下載 Machine Learning Studio 概觀圖表
下載 **Microsoft Azure Machine Learning Studio 功能概觀** 圖表，並取得 Machine Learning Studio 功能的概略檢視。 若要就近保留它，您可以將圖表列印於 Tabloid 大小 (11 x 17 英吋) 的紙張上。

**在這裡下載圖表：[Microsoft Azure Machine Learning Studio 功能概觀](http://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Microsoft Azure Machine Learning Studio 功能概觀](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>開始使用 Machine Learning Studio
第一次進入 [Machine Learning Studio](https://studio.azureml.net) 時，您會看到 [首頁]。 您可以從這裡檢視文件、影片、網路研討會，以及尋找其他重要資源。

按一下左上方的功能表 ![功能表](./media/what-is-ml-studio/menu.png) 您會看到幾個選項。
### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
這裡有兩個選項，**首頁** (您啟動的頁面)，和 **Studio**。

按一下 [Studio]，系統會將您帶到 **Azure Machine Learning Studio**。 首先，會要求您使用您的 Microsoft 帳戶，或是公司帳戶或學校帳戶登入。 登入之後，您會在左邊看到下列索引標籤：

* **專案** - 代表單一專案之實驗、資料集、Notebook 和其他資源的集合
* **實驗** - 已建立及執行或儲存為草稿的實驗
* **Web 服務** - 已從您的實驗部署的 Web 服務
* **Notebook** - 您已建立的 Jupyter Notebook
* **資料集** - 您已上傳到 Studio 的資料集
* **定型模型** - 您已在實驗中訓練並儲存在 Studio 中的模型
* **設定** - 可用來設定帳戶和資源的一組設定。

### <a name="gallery"></a>資源庫
按一下 [資源庫]，會帶您進入 **[Azure AI 資源庫](http://gallery.azure.ai/)**。 [資源庫] 可以讓資料科學家和開發人員社群在此分享使用 Cortana Intelligence 套件的元件建立的解決方案。

如需有關資源庫的詳細資訊，請參閱[共用及探索 Azure AI 資源庫中的解決方案](gallery-how-to-use-contribute-publish.md)。

## <a name="components-of-an-experiment"></a>實驗的元件
實驗由資料集組成，資料集提供資料給分析模組，將模組連接起就能建構預測分析模型。 明確地說，有效的實驗有三個特性：

* 實驗至少有一個資料集和一個模組
* 資料集只能連接到模組
* 模組可以連接到資料集或其他模組
* 模組的所有輸入埠必須有資料流程的某些連線
* 必須設定每個模組的所有必要參數

您可以從頭建立實驗，或者使用現有的範例實驗做為範本。 如需詳細資訊，請參閱[複製範例實驗來建立新的機器學習服務實驗](sample-experiments.md)。

如需建立簡易實驗的範例，請參閱 [在 Azure Machine Learning Studio 中建立簡易實驗](create-experiment.md)。

如需建立預測性分析方案的更完整逐步解說，請參閱 [使用 Azure Machine Learning Studio 開發預測方案](tutorial-part1-credit-risk.md)。

### <a name="datasets"></a>資料集
資料集是指已上傳至 Machine Learning Studio 而可供模型化程序中使用的資料。 Machine Learning Studio 隨附許多範例資料集供您實驗，您可以在需要時上傳更多資料集。 以下是隨附資料集的一些例子：

* **各種汽車的 MPG 資料** - 汽車的每加侖英里 (MPG) 值，以汽缸數、馬力等來識別。
* **乳癌資料** - 乳癌診斷資料。
* **森林火災資料** - 葡萄牙西北部的森林火災範圍

當您建置實驗時，可以從畫布左邊的資料集清單選擇。

如需 Machine Learning Studio 隨附的範例資料集清單，請參閱 [在 Azure Machine Learning Studio 中使用範例資料集](use-sample-datasets.md)。

### <a name="modules"></a>模組
模組是指您在資料上可執行的演算法。 Machine Learning Studio 有許多模組，從資料輸入函數到訓練、評分和驗證程序都有。 以下是隨附模組的一些例子：

* [轉換成 ARFF][convert-to-arff] - 將 .NET 序列化資料集轉換為屬性關聯性檔案格式 (ARFF)。
* [計算基本統計資料][elementary-statistics] - 計算基本統計資料，例如平均值、標準差等。
* [線性迴歸][linear-regression] - 建立線上梯度下降線性迴歸模型。
* [評分模型][score-model] - 給訓練的分類或迴歸模型評分。

當您建置實驗時，可以從畫布左邊的模組清單選擇。

模組可能有一組參數可用來設定模組的內部演算法。 當您在畫布上選取模組時，模組的參數會顯示在畫布右邊的 [屬性]  窗格中。 您可以在此窗格中修改參數來調整模型。

如需了解機器學習演算法的一些說明，請參閱 [如何選擇 Microsoft Azure Machine Learning Studio 的演算法](algorithm-choice.md)。

## <a name="deploying-a-predictive-analytics-web-service"></a>部署預測性分析 Web 服務
當您的預測性分析模型準備好時，可以從 Machine Learning Studio 將它部署為 Web 服務。 如需此程序的詳細資訊，請參閱 [部署 Azure Machine Learning Web 服務](publish-a-machine-learning-web-service.md)。


## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Machine Learning Studio 與 Azure Machine Learning 服務有何不同？

[Azure Machine Learning 服務](../service/overview-what-is-azure-ml.md)提供 SDK 和服務，可快速準備資料、定型及部署機器學習服務模型。 透過自動調整運算與管線，提升產能及降低成本。 使用開放原始碼 Python 架構 (例如 PyTorch、TensorFlow 及 scikit-learn)，存取這些功能。 

當您想要快速且輕鬆地實驗機器學習模型，而內建的機器學習演算法即已足夠您用於解決方案時，請使用 Machine Learning Studio。

若在 Python 環境中運作，而想要更充分掌控您的機器學習演算法，或是想要使用開放原始碼機器學習程式庫，請使用 Machine Learning 服務。

> [!NOTE]
> 在 Azure Machine Learning Studio 中所建立的模型，無法由 Azure Machine Learning 服務進行部署或管理。

## <a name="free-trial"></a>免費試用

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>後續步驟
您可以使用[逐步快速入門](create-experiment.md)和[以範例建置](sample-experiments.md)，來了解預測性分析和機器學習的基本概念。

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
