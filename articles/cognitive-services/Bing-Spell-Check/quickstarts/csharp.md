---
title: 快速入門：使用 Bing 拼字檢查 REST API 和 C# 進行檢查拼字
titlesuffix: Azure Cognitive Services
description: 開始使用 Bing 拼字檢查 REST API 來檢查拼字和文法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a33dfe2e20cdb6c1944d4be89692ec1da5a5482e
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889659"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>快速入門：使用 Bing 拼字檢查 REST API 和 C# 進行檢查拼字

使用本快速入門，第一次呼叫 Bing 拼字檢查 REST API。 此簡單 C# 應用程式會將要求傳送至 API 並傳回建議的修正清單。 雖然此應用程式是以 C# 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs) 上找到此應用程式的原始程式碼。

## <a name="prerequisites"></a>必要條件

* 任何一版的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* [Json.NET](https://www.newtonsoft.com/json) 架構 (以 NuGet 套件形式提供)。
* 如果您使用 Linux/MacOS，則可以使用 [Mono](http://www.mono-project.com/) 來執行此應用程式。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在 Visual Studio 中建立一個名為 `SpellCheckSample` 的新主控台解決方案。 然後將下列命名空間新增至主要程式碼檔案。
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. 針對 API 端點、您的訂用帳戶金鑰以及要拼字檢查的文字，建立變數。

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "enter your key here";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. 針對您的搜尋參數建立變數。 將您的市場代碼附加至 `mkt=`，以及將您的拼字檢查模式新增至 `&mode=`。
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>建立及傳送拼字檢查要求

1. 建立名為 `SpellCheck()` 的非同步函式，將要求傳送至 API。 建立 `HttpClient`，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。 然後在函式中執行下列步驟。

    ```csharp
    async static void SpellCheck()
    {
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = new HttpResponseMessage();
        //...
    }

2. Create the URI for your request by appending your host, path, and parameters. 
    
    ```csharp
    string uri = host + path + params_;
    ```

3. 使用 `KeyValuePair` 物件建立包含您文字的清單，並使用它來建立 `FormUrlEncodedContent` 物件。 設定標頭資訊，並使用 `PostAsync()` 來傳送要求。

    ```csharp
    List<KeyValuePair<string, string>> values = new List<KeyValuePair<string, string>>();
    values.Add(new KeyValuePair<string, string>("text", text));
    
    using (FormUrlEncodedContent content = new FormUrlEncodedContent(values))
    {
        content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
        response = await client.PostAsync(uri, content);
    }
    ```

## <a name="get-and-print-the-api-response"></a>取得並列印 API 回應

### <a name="get-the-client-id-header"></a>取得用戶端識別碼標頭

如果回應包含 `X-MSEdge-ClientID` 標頭，請取得此值並加以列印。

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>取得回應

取得來自 API 的回應。 將 JSON 物件還原序列化，並將它列印到主控台。

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>呼叫拼字檢查函式

在您專案的 Main 函式中，呼叫 `SpellCheck()`。

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="example-json-response"></a>範例 JSON 回應

如以下範例所示，成功的回應會以 JSON 格式來傳回： 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../tutorials/spellcheck.md)

- [什麼是 Bing 拼字檢查 API？](../overview.md)
- [Bing 拼字檢查 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
