---
title: 什麼是 Bing 當地商家搜尋 API？ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Bing 當地商家搜尋 API 是一套 RESTful 服務，可讓您的應用程式依搜尋查詢項目，尋找當地地點及商家資訊。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: fd57c344c640974e9c73156902848fb1685861f2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163138"
---
# <a name="what-is-bing-local-business-search"></a>什麼是 Bing 當地商家搜尋？
Bing 當地商家搜尋 API 是一套 RESTful 服務，可讓您的應用程式依搜尋查詢項目，尋找當地商家資訊。 例如，`q=<business-name> in Redmond, Washington` 或 `q=Italian restaurants near me`。 

## <a name="features"></a>特性
| 功能 | 說明 |  
| -- | -- | 
| [尋找當地商家和位置](quickstarts/local-quickstart.md) | Bing 當地商家搜尋 API 會從查詢中取得本地化的結果。 結果包括商家網站的 URL 和顯示文字、電話號碼，以及地理位置，包括：GPS 座標、城市、街道地址 |  
| [篩選地理界限的當地結果](specify-geographic-search.md) | 加入座標作為搜尋參數，以將結果限制為特定的地理區域，由圓形區域或方形週框方塊指定。 | 
| [依類別篩選當地商家的結果](local-categories.md) | 依類別搜尋當地商家的結果。 此選項使用呼叫端的反向 IP 位置或 GPS 座標來傳回各種商家類別的本地化結果。|

## <a name="workflow"></a>工作流程
從任何可發出 HTTP 要求及剖析 JSON 回應的程式設計語言呼叫 Bing 當地商家搜尋 API。 可以使用 REST API 存取此服務。
 
1. 建立具備 Bing 搜尋 API 存取權的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。   
2. URL會對 `q=""` 查詢參數的搜尋字詞進行編碼。 例如，`q=nearby+restaurant` 或 `q=nearby%20restaurant`。 如果需要，也可以設定分頁。 
3. [將要求傳送至 Bing 當地商家搜尋 API](quickstarts/local-quickstart.md) 
4. 剖析 JSON 回應 

> [!NOTE]
> 目前，當地商家搜尋僅支援 `en-US` 市場。 
> [!NOTE]
> 目前，當地商家搜尋不支援自動建議。 

## <a name="next-steps"></a>後續步驟
- [查詢和回應](local-search-query-response.md)
- [當地商家搜尋快速入門](quickstarts/local-quickstart.md)
- [當地商家搜尋 API 參考](local-search-reference.md)
- [使用和顯示需求](use-display-requirements.md)
