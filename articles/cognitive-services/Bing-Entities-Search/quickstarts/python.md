---
title: 快速入門：使用 Python 將搜尋要求傳送至 Bing 實體搜尋 REST API
titlesuffix: Azure Cognitive Services
description: 使用此快速入門以運用 Python 來傳送要求給「Bing 實體搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 73b666116a23ab8d861d38af4dc9fa5e19d5d1bd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857153"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>快速入門：使用 Python 將搜尋要求傳送至 Bing 實體搜尋 REST API

使用本快速入門以第一次呼叫 Bing 實體搜尋 API，並檢視 JSON 回應。 這個簡單的 Python 應用程式會將新聞搜尋查詢傳送給 API，並顯示回應。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) 上有此範例的原始程式碼。

雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>必要條件

* [Python](https://www.python.org/downloads/) 2.x 或 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列匯入項目。 針對您的訂用帳戶金鑰、端點、市場和搜尋查詢建立變數。 您可以在 Azure 儀表板中找到您的端點。

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. 藉由將市場變數附加至 `?mkt=` 參數來建立要求 url。 對查詢進行 URL 編碼，並將它附加至 `&q=` 參數。 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>傳送要求並取得回應

1. 建立稱為 `get_suggestions()` 的函式。 然後執行下列步驟。
    1. 使用 `Ocp-Apim-Subscription-Key` 作為金鑰，將訂用帳戶金鑰新增至字典中。
    2. 使用 `http.client.HTTPSConnection()` 建立 HTTPS 用戶端物件。 使用 `request()` 與路徑和參數以及標頭資訊來傳送 `GET` 要求。
    3. 使用 `getresponse()` 儲存回應，並傳回 `response.read()`。

    ```python
    def get_suggestions ():
        headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
        conn = http.client.HTTPSConnection (host)
        conn.request ("GET", path + params, None, headers)
        response = conn.getresponse ()
        return response.read()
    ```

2. 呼叫 `get_suggestions()`，並列印 json 回應。

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
    ```

## <a name="example-json-response"></a>範例 JSON 回應

如以下範例所示，成功的回應會以 JSON 格式來傳回： 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置單頁 Web 應用程式](../tutorial-bing-entities-search-single-page-app.md)

* [什麼是 Bing 實體搜尋 API](../search-the-web.md)
* [Bing 實體搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
