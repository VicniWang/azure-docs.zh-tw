---
title: 什麼是平行處理的文件？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 平行文件是配對文件，其中的一份文件是另一份文件的翻譯。 該配對中的一份文件會包含原始語言的句子，而另一分文件會包含已翻譯成目標語言的這些句子。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: d7f479bbacef7270807d9292e7b91fe835485647
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217368"
---
# <a name="what-are-parallel-documents"></a>什麼是平行處理的文件？

平行文件是配對文件，其中的一份文件是另一份文件的翻譯。 該配對中的一份文件會包含原始語言的句子，而另一分文件會包含已翻譯成目標語言的這些句子。
無論哪種語言標示為「來源」，哪種語言標示為「目標」，平行處理的文件都可用來以任一方向定型翻譯系統。

## <a name="use-of-parallel-documents"></a>使用平行處理的文件

系統會使用平行處理的文件來：

1.  了解在兩個語言之間通常會以何種方式對應單字、片語和句子。

2.  了解如何根據前後的片語處理適當的內容。 一個字在另一種語言中不一定都會翻譯為同一個字。

最佳做法是，確定文件的來源和目標語言版本之間有 1 對 1 的句子對應性。

上傳的文件可供您在個別的工作區中私用，且您可以依需求將其用於數量不限的專案或定型中。 從您的文件中擷取的句子會以 Unicode 純文字檔的格式個別儲存在存放庫中，且可由您刪除。 請勿使用自訂翻譯工具作為文件存放庫，您已上傳的文件，將無法以其先前上傳的格式下載。

## <a name="recommendations"></a>建議

如果您的專案屬於特定領域 (類別)，您的文件即應與在該類別內的詞彙一致。 最終的翻譯系統品質，取決於文件集中的句子數目和句子的品質。 您的文件中有越多範例提供某個字在您類別中的特定用法，系統在翻譯期間的作業成效就越理想。

您至少要有 10,000 個平行處理的句子，才能進行系統的定型。 最佳做法是，您可以持續新增更多平行處理的內容並重新定型，以改善翻譯系統的品質。

Microsoft 要求，上傳到自訂翻譯工具的文件不可違反第三方著作權或智慧財產權。 如需詳細資訊，請參閱[使用規定](https://azure.microsoft.com/support/legal/cognitive-services-terms/)。
使用入口網站上傳文件，並不會改變文件本身的智慧財產權歸屬。

## <a name="next-steps"></a>後續步驟

- 了解如何在自訂翻譯工具中使用[字典](what-is-dictionary.md)。
