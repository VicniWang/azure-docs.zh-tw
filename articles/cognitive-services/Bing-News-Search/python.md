---
title: 快速入門：使用 Python 來執行新聞搜尋 - Bing 新聞搜尋 REST API
titlesuffix: Azure Cognitive Services
description: 使用此快速入門以運用 Python 來傳送要求給「Bing 新聞搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f6ae2315c14a92d72d2efa03b96397512120ab06
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866231"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>快速入門：使用 Python 和 Bing 新聞搜尋 REST API 來執行新聞搜尋

使用本快速入門以第一次呼叫 Bing 新聞搜尋 API，並接收 JSON 回應。 這個簡單的 JavaScript 應用程式會將搜尋查詢傳送給 API，並處理結果。 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

您可以按一下 [launch Binder] \(啟動 Binder\) 徽章，在 [MyBinder](https://mybinder.org) \(英文\) 上以 Jupyter Notebook 執行此程式碼範例： 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

您也可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py) 上找到此範例的原始程式碼。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您慣用的 IDE 或編輯器中建立新的 Python 檔案，以及匯入要求模組。 針對您的訂用帳戶金鑰、端點和搜尋字詞建立變數。 您可以在 Azure 儀表板中找到您的端點。

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>建立要求的參數

1. 使用 `"Ocp-Apim-Subscription-Key"` 作為訂用帳戶金鑰，並將金鑰新增至新字典中。 對您的搜尋參數執行相同的動作。

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>傳送要求並取得回應

1. 以您的訂用帳戶金鑰和最後一個步驟中建立的字典物件，使用要求程式庫來呼叫 Bing 圖像式搜尋 API。

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` 包含來自 API 的回應 (JSON 物件)。 存取回應中包含的發行項描述。
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>顯示結果

這些說明可以再轉譯為以**粗體**醒目提示搜尋關鍵字的資料表。

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
[建立單頁 Web 應用程式](tutorial-bing-news-search-single-page-app.md)
