---
title: 逐頁瀏覽 Bing 影像搜尋 API 傳回的影像
titleSuffix: Azure Cognitive Services
description: 在 Bing 影像搜尋 API 傳回的影像不同頁面之間移動。
services: cognitive-services
author: swhite-msft
manager: cgonlun
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: e9eb67cff82ce8c52684d523be7c662c5a2cbb40
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160317"
---
# <a name="page-through-the-images-results"></a>逐頁瀏覽影像結果

當您呼叫影像搜尋 API 時，Bing 會傳回結果清單。 此清單是查詢相關結果總數的子集。 若要取得可用結果的預估總數，請存取解答物件的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) 欄位。  

下列範例顯示影像解答包含的 `totalEstimatedMatches` 欄位。  

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  

若要逐頁瀏覽可用的影像，請使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) 查詢參數。  

`count` 參數指定要在回應中傳回的結果數目。 您可能在回應中要求的結果數目上限為 150。 預設值為 35。 傳遞的實際數目可能小於所要求的數目。

`offset` 參數指定要略過的結果數目。 `offset` 是以零為起始，而且應該小於 (`totalEstimatedMatches` - `count`)。  

如果您希望每頁顯示 20 個影像，您可以將 `count` 設定為 20 並將 `offset` 設定為 0，以取得結果的第一頁。 以下顯示的範例要求 20 個於位移 40 開始的影像。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

如果預設 `count` 值適用於您的實作，您只需要指定 `offset` 查詢參數。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

如果您想要一次逐頁瀏覽 35 個影像，您應該在第一個要求將 `offset` 查詢參數設定為 0，然後在每個後續要求將 `offset` 遞增 35。 不過，後續回應中的某些結果可能與前一個回應重複。 例如，此回應中的前兩個影像可能與前一個回應中的最後兩個影像相同。

若要消除重複的結果，請使用 `Images` 物件的 [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) 欄位。 `nextOffset` 欄位會告訴您要用於下一個要求的 `offset`。 例如，如果您想要一次逐頁瀏覽 30 個影像，您可將第一個要求中的 `count` 設定為 30 並將 `offset` 設定為 0。 在下一個要求中，您可將 `count` 設定為 30 並將 `offset` 設定為前一個回應的 `nextOffset` 值。 若要向後逐頁瀏覽，我們建議維護先前位移的堆疊並快顯最近使用的堆疊。

> [!NOTE]
> 逐頁瀏覽只適用於影像搜尋 (/images/search)，不適用於影像見解或發燒影像 (/images/trending)。

> [!NOTE]
> `TotalEstimatedAnswers` 欄位是針對您可從目前查詢中擷取之搜尋結果總數的估計。  當您設定 `count` 和 `offset` 參數時，`TotalEstimatedAnswers` 的數目可能會變更。 
