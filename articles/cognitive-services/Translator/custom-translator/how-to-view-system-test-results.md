---
title: 檢視系統測試結果和部署 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 在定型成功後，請檢閱系統測試以分析您的定型結果。 如果您滿意定型結果，請為定型的模型提出部署要求。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 6cad142c050ca1de7e4c5f9b4415132a82f38c7b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215241"
---
# <a name="view-system-test-results"></a>檢視系統測試結果

在定型成功後，請檢閱系統測試以分析您的定型結果。 如果您滿意定型結果，請為定型的模型提出部署要求。 

## <a name="system-test-results-page"></a>系統測試結果頁面

選取專案，然後選取該專案的模型索引標籤，找出您要使用的模型，最後選取測試索引標籤。

測試索引標籤會顯示：

1.  **系統測試結果：** 定型中的測試程序所產生的結果。 測試程序會產生 BLEU 分數。

    **句子計數：** 在測試集中使用了多少個平行句子。

     **BLEU 分數：** 在定型完成後為模型產生的 BLEU 分數。

    **狀態：** 指出測試程序已完成或進行中。

    ![系統測試結果](media/how-to/how-to-system-test-results.png)

2.  按一下系統測試結果後，您將會前往測試結果詳細資料頁面。 此頁面會顯示測試資料集所含句子的機器翻譯。

3.  測試結果詳細資料頁面上的資料表有兩個資料行 - 語言組中的每種語言各一個。 來源語言的資料行會顯示要翻譯的句子。 目標語言的資料行則會在每個資料列中包含兩個句子。

    **Ref：** 這個句子是測試資料集中給定來源句子的參考翻譯。

    **MT：** 這個句子是執行定型之後建置的模型對來源句子所做的自動翻譯。

    ![系統測試結果比較](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>下載測試

按一下 [下載翻譯] 連結以下載 ZIP 檔案。 此 ZIP 檔案包含測試資料集中所含來源句子的機器翻譯。

![下載測試](media/how-to/how-to-system-test-download.png)

這個下載的 ZIP 封存檔包含三個檔案。

1.  **custom.mt.txt：** 此檔案包含以使用者的資料定型的模型對來源語言句子所做的目標語言機器翻譯。

2.  **ref.txt：** 此檔案包含使用者以目標語言為來源語言句子提供的翻譯。

3.  **source.txtref.txt：** 此檔案包含來源語言的句子。

    ![下載系統測試結果](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>部署模型

若要要求部署：

1.  選取專案，移至 [模型] 索引標籤。

2. 針對成功定型的模型會顯示 [部署] 按鈕 (若尚未部署)。

    ![部署模型](media/how-to/how-to-deploy-model.png)

3.  按一下 [部署]。
4.  針對您要部署模型的區域選取 [已部署]，然後按一下 [儲存]。 您可以對多個區域選取 [已部署]。

    ![部署模型](media/how-to/how-to-deploy-model-regions.png)

5.  您可以在 [狀態] 資料行中檢視模型的狀態。

## <a name="update-deployment-settings"></a>更新部署設定

若要更新部署設定：

1.  選取專案，然後移至 [模型] 索引標籤。

2. 針對成功部署的模型會顯示 [更新] 按鈕。

    ![部署模型](media/how-to/how-to-update-undeploy-model.png)

3.  選取 [更新]。
4.  對您想要部署或解除部署模型的區域選取 [已部署] 或 [已解除部署]，然後按一下 [儲存]。

    ![部署模型](media/how-to/how-to-undeploy-model.png)

>[!Note]
>如果您對所有區域選取 [已解除部屬]，則模型會從所有區域中解除部署，並進入已解除部署的狀態。 現在無法使用。

## <a name="next-steps"></a>後續步驟

- 透過 [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 開始使用您已部署的自訂翻譯模型。
- 了解[如何管理設定](how-to-manage-settings.md)以共用您的工作區，和管理訂用帳戶金鑰。
- 了解[如何移轉您的工作區和專案](how-to-migrate.md) (從 [Microsoft Translator 中樞](https://hub.microsofttranslator.com))
