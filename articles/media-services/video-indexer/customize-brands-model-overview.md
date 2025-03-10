---
title: 在影片索引器中自訂品牌模型 - Azure
titlesuffix: Azure Media Services
description: 本文概要說明影片索引器中的品牌模型是什麼，以及如何加以自訂。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 48255614291086fe85666bec407ea62f7a365410
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237360"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>在影片索引器中自訂品牌模型

影片索引器支援在視訊和音訊內容的編製索引及重新編製索引期間，從語音和視覺文字偵測品牌。 品牌偵測功能可識別 Bing 的品牌資料庫建議所提及的產品、服務以及公司。 例如，如果在視訊或音訊內容中提及 Microsoft，或者視訊或音訊內容出現在視訊的視覺文字中，影片索引器會將其偵測為內容中的品牌。 您可以使用上下文來區分品牌和其他詞彙。

品牌偵測可用於各種商務案例，例如內容封存和探索、內容相關的廣告、社交媒體分析、零售競爭分析等等。 影片索引器的品牌偵測可讓您使用 Bing 的品牌資料庫以及自訂項目，針對每個影片索引器帳戶建置自訂品牌模型，藉此為語音和視覺化文字中提及的品牌編製索引。 自訂品牌模型功能可讓您選取影片索引器是否要從 Bing 品牌資料庫偵測品牌、從偵測到的品牌排除特定品牌 (基本上就是建立品牌的封鎖清單)，以及包含應該是模型的一部分但可能不在 Bing 品牌資料庫的品牌 (基本上就是建立品牌的允許清單)。 您建立的自訂品牌模型僅適用於您的模型建立所在的帳戶。

## <a name="out-of-the-box-detection-example"></a>現成的偵測範例

在 [Microsoft Build 2017 Day 2](https://www.videoindexer.ai/media/ed6ede78ad/) 簡報中，"Microsoft Windows" 這個品牌會出現多次。 有時候是在文字記錄中，有時候是以視覺化文字的方式，而且絕不會逐字出現。 影片索引器會以高精確度的方式，根據上下文偵測到某個詞彙確實是品牌，其中涵蓋超過 90 個現成的品牌，而且持續更新中。 在 02:25 處，影片索引器從語音偵測到品牌，然後再次在 02:40 處，從視覺化文字偵測到該品牌，這是 Windows 標誌的一部分。

![品牌概觀](./media/content-model-customization/brands-overview.png)

根據知道如何從上下文釐清的進階機器學習演算法，談論建築方面的 windows (窗戶) 時，不會將 "Windows" 這個字偵測為品牌，這同樣適用於 Box、Apple、Fox 等等。 品牌偵測適用於所有我們支援的語言。 按一下這裡可取得[完整的 Microsoft Build 2017 Day 2 重點演說視訊和索引](http://www.videoindexer.ai/media/ed6ede78ad/)。

若要產生您自己的品牌，請參閱「後續步驟」。

## <a name="next-steps"></a>後續步驟

[使用 API 自訂品牌模型](customize-brands-model-with-api.md)

[使用網站自訂品牌模型](customize-brands-model-with-website.md)
