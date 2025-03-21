---
title: 快速入門：使用 Java 將搜尋要求傳送至 Bing 實體搜尋 REST API
titlesuffix: Azure Cognitive Services
description: 使用此快速入門以運用 Java 來傳送要求給「Bing 實體搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: ba8d1fa44ca3eacf4c8feeede18b594dfe72ea37
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884115"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>快速入門：使用 Java 將搜尋要求傳送至 Bing 實體搜尋 REST API

使用本快速入門以第一次呼叫 Bing 實體搜尋 API，並檢視 JSON 回應。 這個簡單的 Java 應用程式會將新聞搜尋查詢傳送給 API，並顯示回應。

雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>必要條件

* [Java 開發套件 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
* [Gson 程式庫](https://github.com/google/gson)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 Java 專案，並匯入下列程式庫。

  ```java
  import java.io.*;
  import java.net.*;
  import java.util.*;
  import javax.net.ssl.HttpsURLConnection;
  import com.google.gson.Gson;
  import com.google.gson.GsonBuilder;
  import com.google.gson.JsonObject;
  import com.google.gson.JsonParser;
  import com.google.gson.Gson;
  import com.google.gson.GsonBuilder;
  import com.google.gson.JsonObject;
  import com.google.gson.JsonParser;
  ```

2. 在新類別中，建立 API 端點變數、您的訂用帳戶金鑰及搜尋查詢。

  ```java
  public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
  //...
    
  ```

## <a name="construct-a-search-request-string"></a>建構搜尋要求字串

1. 建立稱為 `search()` 且會傳回 JSON `String` 的函式。 對搜尋查詢進行 url 編碼，並使用 `&q=` 將其新增至參數字串。 使用 `?mkt=` 將您的市場新增至字串中。
 
2. 使用您的主機、路徑和參數字串建立 URL 物件。
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>傳送搜尋要求並接收回應

1. 在上面建立的 `search()` 函式中，使用 `url.openCOnnection()` 建立新的 `HttpsURLConnection` 物件。 將要求方法設定為 `GET`，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. 建立新的 `StringBuilder`。 在具現化 `BufferedReader` 時使用新的 `InputStreamReader` 作為參數以讀取 API 回應。  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. 建立 `String` 物件以儲存從 `BufferedReader` 傳回的回應。 逐一查看，並將每一行附加至字串。 然後關閉讀取器，並傳回回應。 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>格式化 JSON 回應

1. 建立稱為 `prettify` 的新函式以格式化 JSON 回應。 建立新的 `JsonParser`，並在 json 文字上呼叫 `parse()`，然後將其儲存為 JSON 物件。 

2. 使用 Gson 程式庫來建立新的 `GsonBuilder()`，並使用 `setPrettyPrinting().create()` 格式化 json。 然後將其傳回。    
  
  ```java
  //...
  public static String prettify (String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
  }
  //...
  ```

## <a name="call-the-search-function"></a>呼叫搜尋函式

1. 從專案的主要方法呼叫 `search()`，然後使用 `prettify()` 來格式化文字。
    
    ```java
        public static void main(String[] args) {
            try {
                String response = search ();
                System.out.println (prettify (response));
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
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

[回到頁首](#main)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置單頁 Web 應用程式](../tutorial-bing-entities-search-single-page-app.md)

* [什麼是 Bing 實體搜尋 API？](../overview.md )
* [Bing 實體搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
