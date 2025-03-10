---
title: 語言支援 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 針對您的知識庫支援的文化特性、自然語言清單。 請勿在相同知識庫中混用語言。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/04/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3bdc3a91e87f1bbd06ef0181d0042ee618f519c9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861437"
---
# <a name="language-and-region-support-for-qna-maker"></a>QnA Maker 支援的語言與區域

知識庫的語言會影響 QnA Maker 從[來源](../Concepts/data-sources-supported.md)自動擷取問題和答案的能力，以及 QnA Maker 為回應使用者查詢所提供之結果的相關性。

## <a name="auto-extraction"></a>自動擷取
QnA Maker 支援在任何語言頁面中擷取問題/答案，但下列語言的擷取有效性會較高，因為 QnA Maker 使用關鍵字來識別問題。

|支援的語言| 地區設定|
|-----|----|
|English|en-*|
|法文|fr-*|
|義大利文|it-*|
|德文|de-*|
|西班牙文|es-*|

## <a name="query-matching-and-relevance"></a>查詢比對和相關性
QnA Maker 需要 Azure 搜尋服務中的[語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)才能提供結果。 特殊的重新排名功能適用於 En-* 語言，可改善相關性。

雖然 Azure 搜尋服務功能與支援的語言同等重要，但 QnA Maker 有位於上述 Azure 搜尋結果的其他順位排定程式。 在此順位排定程式模型中，我們使用一些採用 en-* 的特殊語意和字組型功能 (尚無法適用於其他語言)。 我們不會提供這些功能，因為它們是順位排定程式內部運作的一部分。 

QnA Maker 會在建立期間自動偵測知識庫的語言，並據以設定分析器。 您可以建立下列語言的知識庫。 如需 QnA Maker 如何處理語言的詳細資料，請閱讀[這篇文章](../How-To/language-knowledge-base.md)。


> [!Tip]
> 語言分析器一旦設定後即無法變更。 此外，語言分析器適用於 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)中的所有知識庫。 若您打算有不同語言的知識庫，您應該在個別 QnA Maker 服務下建立這些知識庫。

|支援的語言|
|-----|
|阿拉伯文|
|亞美尼亞文|，
孟加拉文|
|巴斯克文|
|保加利亞文|
|卡達隆尼亞文|
|簡體中文|
|繁體中文|
|克羅埃西亞文|
|捷克文|
|丹麥文|
|荷蘭文|
|English|
|愛沙尼亞文|
|芬蘭文|
|法文|
|加利西亞文|
|德文|
|希臘文|
|古吉拉特文|
|希伯來文|
|北印度文|
|匈牙利文|
|冰島文|
|印尼文|
|愛爾蘭文|
|義大利文|
|日文|
|坎那達文|
|韓文|
|拉脫維亞文|
|立陶宛文|
|馬來亞拉姆文|
|馬來文|
|挪威文|
|波蘭文|
|葡萄牙文|
|旁遮普文|
|羅馬尼亞文|
|俄文|
|塞爾維亞文_斯拉夫|
|塞爾維亞文_拉丁|
|斯洛伐克文|
|斯洛維尼亞文|
|西班牙文|
|瑞典文|
|坦米爾文|
|泰盧固文|
|泰文|
|土耳其文|
|烏克蘭文|
|烏都文|
|越南文|
