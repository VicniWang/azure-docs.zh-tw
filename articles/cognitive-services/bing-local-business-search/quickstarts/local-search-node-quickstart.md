---
title: 快速入門 - 使用 Node.js 向 Bing 當地商家搜尋 API 傳送查詢 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 開始在 Node 中使用 Bing 當地商家搜尋 API。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: e58bd9afcbb7684cae93d3e71bae89d49f640111
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203958"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>快速入門：使用 Node.js 向 Bing 當地商家搜尋 API 傳送查詢

使用此快速入門開始向 Bing 當地商家搜尋 API 傳送要求，該 API 是 Azure 認知服務。 雖然此簡單應用程式是以 Node.js 撰寫，但 API 是一種與任何程式語言相容的 RESTful Web 服務，可產生 HTTP 要求，並剖析 JSON。

此範例應用程式會從 API 取得搜尋查詢 `hotel in Bellevue` 的當地回應資料。

## <a name="prerequisites"></a>必要條件

* 最新版的 [Node.js](https://nodejs.org/en/download/)。

* [JavaScript 要求程式庫](https://github.com/request/request)

您必須有具備 Bing API 的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免費試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)即足以供本快速入門使用。 使用免費試用所提供的存取金鑰。  另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="code-scenario"></a>程式碼案例

下列程式碼取得定義，並傳送要求。 其實作的步驟如下：

1. 宣告變數以依主機及路徑指定端點。
2. 指定查詢，並新增查詢參數。
3. 建立回應的處理常式函式。
4. 定義建立要求的搜尋函式，並新增 Ocp-Apim-Subscription-Key 標頭。
5. 執行搜尋函式。

此示範的完整程式碼如下：

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>後續步驟

* [當地商家搜尋快速入門](local-quickstart.md)
* [當地商家搜尋 Java 快速入門](local-search-java-quickstart.md)
* [當地商家搜尋 Python 快速入門](local-search-python-quickstart.md)
