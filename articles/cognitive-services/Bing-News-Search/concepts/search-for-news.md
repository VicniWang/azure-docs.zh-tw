---
title: 使用 Bing 新聞搜尋 API 來搜尋新聞
titlesuffix: Azure Cognitive Services
description: 了解如何傳送一般新聞、熱門話題和頭條新聞的搜尋查詢。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 01/11/2019
ms.author: scottwhi
ms.openlocfilehash: b7da1782491c4317713aafe54c4fd01c2c7110a9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55880766"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>使用 Bing 新聞搜尋 API 來搜尋新聞

Bing 影像搜尋 API 可讓您輕鬆地將 Bing 的認知新聞搜尋功能整合到應用程式中。

Bing 新聞搜尋 API 主要用於尋找及傳回相關的新聞文章，同時也提供多個在 Web 上智慧擷取重點新聞的功能。

## <a name="suggest-and-use-search-terms"></a>建議和使用搜尋字詞

若您提供使用者可在其中輸入其搜尋字詞的搜尋方塊，請使用 [Bing 自動建議 API](../../bing-autosuggest/get-suggested-search-terms.md) 來改善使用經驗。 API 會根據部分搜尋字詞傳回建議的查詢字串，作為使用者類型。

在使用者輸入其搜尋字詞之後，URL 會先將此字詞編碼，再設定 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query) 查詢參數。 例如，如果使用者輸入 *sailing dinghies*，請將 `q` 設定為 `sailing+dinghies` 或 `sailing%20dinghies`。

## <a name="get-general-news"></a>取得一般新聞

若要從 Web 取得與使用者的搜尋字詞相關聯的一般新聞文章，請傳送下列 GET 要求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

如果這是您第一次呼叫任何的 Bing API，請勿包含用戶端識別碼標頭。 如果您先前已呼叫 Bing API 且 Bing 傳回了使用者和裝置組合的用戶端識別碼，則只要包含用戶端識別碼。

若要從特定網域取得新聞，請使用 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 查詢運算子。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

以下顯示前一個查詢的回應。 由於 Bing 搜尋 API 的[使用和顯示需求](../useanddisplayrequirements.md)，您必須以回應中提供的順序來顯示每則新聞文章。 如果該文章有叢集化文章，您應該指出有相關文章存在，並在要求時加以顯示。

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

[新聞](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#news) (英文) 回答中會列出 Bing 認為與查詢相關的新聞文章清單。 `totalEstimatedMatches` 欄位包含可供檢視的文章數目估計值。 如需有關如何逐頁瀏覽文章的資訊，請參閱[逐頁瀏覽新聞](../paging-news.md)。

清單中每篇[新聞文章](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#newsarticle) (英文) 都包含文章的名稱、描述以及文章在主機網站上的 URL。 如果文章包含影像，物件就會包含影像的縮圖。 使用 `name` 和 `url` 來建立帶領使用者前往主機網站上新聞文章的超連結。 如果文章包含影像，請讓影像也可以使用 `url` 進行點按。 請務必使用 `provider` 來歸類文章。

如果 Bing 可以判斷出新聞文章的類別，文章就會包含 `category` 欄位。

## <a name="get-todays-top-news"></a>取得今天的熱門新聞

若要取得今天的熱門新聞文章，請進行和取得一般新聞相同的要求，不同之處在於不要設定 `q`。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

取得熱門新聞的回應幾乎和取得一般新聞相同。 不過，`news` 回應中不會包含 `totalEstimatedMatches` 欄位，原因是其會有一組固定數目的結果。 不同新聞週期可能會有不同的熱門新聞數目。 請務必使用 `provider` 來歸類文章。

## <a name="get-news-by-category"></a>取得各類別的新聞

若要依類別取得新聞文章 (例如，運動或娛樂的熱門新聞)，請對 Bing 傳送下列 GET 要求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

使用 [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) 查詢參數來指定要取得的文章類別。 如需可指定的新聞類別清單，請參閱[依市場的新聞類別](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-categories-by-market)。

依類別取得新聞的回應幾乎和取得一般新聞相同。 不過，文章全都來自指定的類別。

## <a name="get-headline-news"></a>取得頭條新聞

若要要求頭條新聞文章，並取得所有新聞類別的文章，請對 Bing 傳送下列 GET 要求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

請勿包含 [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) 查詢參數。

取得頭條新聞的回應和取得今天的熱門新聞相同。 如果文章是頭條新聞文章，其 `headline` 欄位會設定為 **true**。

根據預設，回應中最多會包含 12 篇頭條新聞文章。 若要變更所傳回的頭條新聞文章數目，請指定 [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headlinecount) 查詢參數。 回應中也會針對每個新聞類別包含最多四個非頭條新聞文章。

回應會將叢集視為一篇文章。 因為叢集中可能有數篇文章，回應可能會包含超過 12 篇頭條新聞文章，以及每個類別超過四篇的非頭條新聞文章。

## <a name="get-trending-news"></a>取得熱門新聞

若要取得社交網路上引領趨勢的新聞主題，請對 Bing 傳送下列 GET 要求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> 趨勢主題僅適用於 en-US 和 zh-CN 市場。

下列 JSON 是先前要求的回應。 每個趨勢新聞文章都包含相關的影像、最新消息旗標，以及 Bing 搜尋針對該文章所傳回結果的 URL。 請在 `webSearchUrl` 欄位中使用該 URL，以讓使用者前往 Bing 搜尋結果頁面。 或者，請使用查詢文字來呼叫 [Web 搜尋 API](../../bing-web-search/search-the-web.md) 來自己顯示結果。

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>取得相關新聞

如果有其他與新聞文章相關的文章，新聞文章內可能會包括 [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle-clusteredarticles) 欄位。 下列內容顯示具有叢集化文章的文章。

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>節流要求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
[如何逐頁檢視 Bing 新聞搜尋結果](../paging-news.md)
