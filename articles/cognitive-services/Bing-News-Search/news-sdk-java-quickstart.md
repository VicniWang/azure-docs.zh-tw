---
title: 快速入門：執行新聞搜尋 - 適用於 Java 的 Bing 新聞搜尋 SDK
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用「適用於 Java 的 Bing 新聞搜尋 SDK」來搜尋新聞，並處理回應。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 0922010ad6993c8bb1571c24595370895e49c498
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866160"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>快速入門：使用適用於 Java 的 Bing 新聞搜尋 SDK 來搜尋新聞

透過本快速入門開始使用適用於 Java 的 Bing 新聞搜尋 SDK 來搜尋新聞。 雖然 Bing 新聞搜尋具有與大部分程式設計語言相容的 REST API，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) 上找到。

## <a name="prerequisites"></a>必要條件

使用 Maven、Gradle 或另一個相依性管理系統，來安裝 Bing 新聞搜尋 SDK 相依性。 Maven POM 檔案需要下列宣告：

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

在您最愛的 IDE 或編輯器中建立新的 Java 專案，並匯入下列程式庫。

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>建立搜尋用戶端與存放區認證

1. 建立呼叫 `getClient()` 的方法，以傳回新的 `NewsSearchAPIImpl` 搜尋用戶端。 將您的端點新增為新 `NewsSearchAPIImpl` 物件的第一個參數，並新增 `ServiceClientCredentials` 物件來儲存您的認證。

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. 若要建立 `ServiceClientCredentials` 物件，請覆寫 `applyCredentialsFilter()` 函式。 將 `OkHttpClient.Builder` 傳遞給方法，並使用產生器的 `addNetworkInterceptor()` 方法來建立 SDK 呼叫的認證。

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>傳送及接收搜尋要求

1. 建立呼叫 `getClient()` 的方法，並將搜尋要求傳送至 Bing 新聞搜尋服務。 以 market 和 count 參數來篩選搜尋，然後列印第一個新聞結果的相關資訊：名稱、URL、發行日期、描述、提供者名稱和預估的相符搜尋項目總數。

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. 將您的搜尋方法新增至 `main()` 方法，以執行程式碼。

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
[建立單頁 Web 應用程式](tutorial-bing-news-search-single-page-app.md)
