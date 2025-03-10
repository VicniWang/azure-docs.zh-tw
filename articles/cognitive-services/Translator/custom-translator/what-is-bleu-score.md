---
title: 什麼是 BLEU 分數？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: BLEU 是指對於相同來源句子的自動翻譯與一或多個人工參考翻譯之間的差異所做的量測。 BLEU 演算法會比較自動翻譯的連續片語與它在參考翻譯中找到的連續片語，並以加權方式計算相符項數目。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.topic: article
ms.date: 11/13/2018
ms.author: v-rada
ms.openlocfilehash: b0fd9777b8c830a06195dbc22f0bb9081ff9753a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222009"
---
# <a name="what-is-a-bleu-score"></a>什麼是 BLEU 分數？

[BLEU (雙語評估互補)](https://en.wikipedia.org/wiki/BLEU) 是指對於相同來源句子的自動翻譯與一或多個人工參考翻譯之間的差異所做的量測。

## <a name="scoring-process"></a>評分程序

BLEU 演算法會比較自動翻譯的連續片語與它在參考翻譯中找到的連續片語，並以加權方式計算相符項數目。 這些相符項與位置無關。 相符程度越高，與參考翻譯的相似度就越高，分數也就越高。 可理解度和文法的正確性不會列入考量。

## <a name="how-bleu-works"></a>BLEU 的運作方式為何？

BLEU 的作用在於能夠為測試主體算出個別句子判斷錯誤的平均值，進而與人為判斷產生適當的關聯，而不是試圖找出每個句子確切的人為判斷。

如需 BLEU 分數的詳細討論，請參閱[這裡](https://youtu.be/-UqDljMymMg)。

BLEU 結果有很大一部分取決於您領域的廣度、測試資料與定型和調整資料的一致性，以及可用於定型的資料量。 如果您的模型已在較窄的領域中定型，且您的定型資料與測試資料相一致，則可望會有較高的 BLEU 分數。

>[!NOTE]
>以相同的測試集、相同的語言組和相同的 MT 引擎比較 BLEU 結果時，BLEU 分數的比較才有正當性。 來自不同測試集的 BLEU 分數必定是不同的。
