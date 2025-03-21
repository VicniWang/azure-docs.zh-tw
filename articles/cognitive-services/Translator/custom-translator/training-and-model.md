---
title: 什麼是定型和模型？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 模型是一種系統，提供特定語言組的翻譯。 成功定型的輸出就是模型。 當定型模型時，需要三種互斥的資料集：定型資料集、微調資料集和測試資料集。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: v-rada
ms.openlocfilehash: bd4921cb959e3ea2e893c6837fb47792d3585ca9
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220122"
---
# <a name="what-are-trainings-and-models"></a>什麼是定型和模型？

模型是一種系統，提供特定語言組的翻譯。
成功定型的輸出就是模型。 當定型模型時，需要三種互斥的資料集：定型資料集、微調資料集和測試資料集。 您也可以提供字典資料。

如果只提供定型資料，當定型排入佇列時，自訂翻譯工具會自動調整微調資料集和測試資料集。 它會從您的定型資料中排除 5,000 個句子，並對微調資料集和測試資料集各使用 2,500 個句子來調整。

## <a name="training-dataset-for-custom-translator"></a>自訂翻譯工具的定型資料集

自訂翻譯工具會使用包含在定型集中的文件作為建置模型的基礎。 在執行定型期間，這些文件中出現的句字會對齊 (或配對)。 您可以自由地撰寫自己的定型文件集。 您可以在一個模型中，納入您認為有些許相關性的文件。 在另一個模型中再次將它們排除在外，看看對於 [BLEU (Bilingual Evaluation Understudy，雙語評估替補) 分數](what-is-bleu-score.md)的影響。 只要您保留微調集和測試集常數，便可放心地撰寫定型集。 這是修改翻譯系統品質的有效方法。

您可以在專案中執行多個定型，並比較所有執行之定型的 [BLEU 分數](what-is-bleu-score.md)。 當您執行多個定型以進行比較時，請確定每次都指定相同的微調/測試資料。 另外也請務必在 [[測試](how-to-view-system-test-results.md)] 索引標籤中手動檢查結果。

## <a name="tuning-dataset-for-custom-translator"></a>自訂翻譯工具的微調資料集

自訂翻譯工具會使用此資料集中包含的平行處理文件來微調翻譯系統，以獲得最佳結果。

微調集是用來在定型期間將翻譯系統的所有參數和加權調整為最佳值。 請務必謹慎選擇微調集：微調集應具有您未來想翻譯之文件內容的代表性。 微調集對於所產生的翻譯品質有重大影響。 微調可讓翻譯系統提供最接近您在微調資料集中所提供範例的翻譯。 您只需要 2500 個以下的句子來作為微調集。 如需最佳的翻譯品質，建議您選擇最具代表性的句子選取項目，手動選取微調集。

建立微調集時，選擇您希望翻譯的未來句子，其為有意義和代表性長度的句子。 您也應該選擇含有要翻譯的單字和片語，其在句子中的散佈方式類似於您未來翻譯中所預期的方式。 在實務上，長度 8 到 18 個字的句子會產生最佳結果，因為這些句子包含足夠的內容以顯示變化，且能提供有效的片語長度，而不會太過複雜。

對於要用在微調集中的句子類型，有一個很合適的描述是「散文」：實際流暢的句子。 不是資料表格、不是詩詞，也不是清單，句子中也不只有標點符號或數字，而是普通的語言。

如果您手動選取微調資料集，在您定型和測試資料時，不應該有任何相同的句子。 微調集對於翻譯的品質有重大的影響，請謹慎選擇句子。

如果您不確定要為微調集選擇什麼內容，請直接選取定型集，並讓自訂翻譯工具為您選取微調集。 當您讓自訂翻譯工具自動選擇微調集時，它會從您的雙語定型文件中隨機使用一部分句子，並將這些句子從定型材料本身中排除。

## <a name="testing-dataset-for-custom-translator"></a>自訂翻譯工具的測試資料集

測試集中包含的平行處理的文件可用來計算 BLEU (Bilingual Evaluation Understudy，雙語評估替補) 分數。 此分數表示翻譯系統的品質。 此分數可實際指出此定型所產生之翻譯系統的翻譯結果，與測試資料集內之參考句子的相符程度。

BLEU 分數是自動翻譯和參考翻譯之間差異的度量單位。 其值範圍從 0 到 100。 分數為 0 表示翻譯中未出現任何一個參考的單字。 分數為 100 表示自動翻譯與參考完全相符：相同的字位於完全相同的位置。 您收到的分數是測試集所有句子的 BLEU 分數平均。

測試集應包含平行處理的文件，其中目標語言句子是語言組中對應的原始語言句子最令人滿意的翻譯。 您可以使用您用來組成微調集的相同準則。 不過，測試集對於翻譯系統的品質並無影響。 它是專門用來為您產生 BLEU 分數，此外並無其他用途。

您只需要 2500 個以下的句子來作為測試集。 當您讓系統自動選擇測試集時，它會從您的雙語定型文件中隨機使用一部分句子，並將這些句子從定型材料本身中排除。

您可以檢視測試集的自訂翻譯，並瀏覽至模型中的 [測試] 索引標籤，將它們與測試集中提供的翻譯比較。
