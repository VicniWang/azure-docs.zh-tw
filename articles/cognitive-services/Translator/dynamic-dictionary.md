---
title: 動態字典 - 翻譯工具文字 API
titlesuffix: Azure Cognitive Services
description: 如何使用翻譯工具文字 API 的動態字典功能。
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 6140361d3cffaf7938aa8f24b6f0c3d27784af88
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876006"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>如何使用翻譯工具文字 API 的動態字典功能

如果已經知道想要套用至單字或片語的翻譯，則可以在要求內以標記來提供。 動態字典僅適用於專有名稱和產品名稱這類複合名詞。

**語法：**

<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>

**範例：en-de:**

來源輸入：The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

目標輸出：Das Wort "wordomatic" ist ein Wörterbucheintrag.

不論有無 HTML 模式，此功能都以相同的方式運作。

應該謹慎使用此功能。 自訂翻譯的適當且最適合方式是使用 Custom Translator。 Custom Translator 會完全利用內容和統計機率。 如果您已有或能夠建立定型資料，以便在上下文中顯示您的單字或片語，則會得到更好的結果。 您可以在 [http://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) \(英文\) 找到更多 Custom Translator 的詳細資訊。
