---
title: 快速入門：使用 C# 來執行新聞搜尋 - Bing 新聞搜尋 REST API
titlesuffix: Azure Cognitive Services
description: 使用此快速入門以運用 C# 來傳送要求給「Bing 新聞搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 74ac8602e61404214f300682f5696100b8fa0c60
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868849"
---
# <a name="quickstart-search-for-news-using-c-and-the-bing-news-search-rest-api"></a>快速入門：使用 C# 和 Bing 新聞搜尋 REST API 來搜尋新聞

使用本快速入門以第一次呼叫 Bing 新聞搜尋 API，並檢視 JSON 回應。 這個簡單的 C# 應用程式會將新聞搜尋查詢傳送給 API，並顯示回應。 此範例的完整程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingNewsSearchv7.cs) 上找到。

雖然此應用程式是以 C# 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>必要條件

* 任何一版的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* [Json.NET](https://www.newtonsoft.com/json) 架構 (以 NuGet 套件形式提供)。
* 如果您使用 Linux/MacOS，則可以使用 [Mono](http://www.mono-project.com/)來執行此應用程式。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在 Visual Studio 中建立新的 C# 主控台解決方案。 然後將下列命名空間新增至主要程式碼檔案。
    
    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. 建立適用於 API 端點、您訂用帳戶金鑰及搜尋字詞的變數。

    ```csharp
    const string accessKey = "enter key here";
    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/news/search";
    const string searchTerm = "Microsoft";
    ```
## <a name="create-a-struct-to-format-the-bing-news-search-response"></a>建立結構來製作 Bing 新聞搜尋回應的格式

1. 定義 `SearchResult` 結構以包含影像搜尋結果及 JSON 標頭資訊。

    ```csharp
    struct SearchResult
    {
        public String jsonResult;
        public Dictionary<String, String> relevantHeaders;
    }
    ```

## <a name="create-and-handle-a-news-search-request"></a>建立及處理新聞搜尋要求

建立一個名為 `BingNewsSearch` 的方法來執行對 API 的呼叫，並將傳回類型設定為稍早建立的 `SearchResult` 結構。 在方法中，執行下列步驟：

1. 建構搜尋要求的 URI。 請注意，搜尋字詞 `toSearch` 必須先格式化，才能附加到字串。

    ```csharp
    static SearchResult BingNewsSearch(string toSearch){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. 執行 Web 要求並取得 JSON 字串形式的回應。

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. 建立搜尋結果物件並擷取 Bing HTTP 標頭。 然後傳回 `searchResult`。

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

3. 建立搜尋結果物件並擷取 Bing HTTP 標頭。 然後傳回 `searchResult`。

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-the-response"></a>處理回應

1. 在主要方法中，呼叫 `BingNewsSearch()` 並儲存傳回的回應。 然後將 JSON 還原序列化成物件。 接著，您可以檢視回應的值。

    ```csharp
    SearchResult result = BingNewsSearch(searchTerm);
    //deserialize the JSON response
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    Console.WriteLine(jsonObj["value"][0])
    ```

## <a name="json-response"></a>JSON 回應

如以下範例所示，成功的回應會以 JSON 格式來傳回：

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .

      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](tutorial-bing-news-search-single-page-app.md)
