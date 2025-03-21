---
title: 品牌偵測 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 偵測品牌/標誌的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: a5cf4a2b0f5e2e2c83e9fe6aa9237c943fa23ed9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864796"
---
# <a name="brand-detection"></a>品牌偵測

品牌偵測是[物件偵測](concept-object-detection.md)的特製化模式，可使用全球數千個標誌的資料庫識別影像或影片中的商業品牌。 例如，您可以使用這項功能探索哪些品牌在社交媒體最受歡迎或在媒體產品位置中最常見。

電腦視覺服務可偵測特定影像中是否有品牌標誌；如果有，會傳回品牌名稱、信賴分數，以及標誌週框的座標。

內建的標誌資料庫涵蓋消費性電子產品、服飾等等的熱門品牌。 如果您發現電腦視覺服務偵測不到您尋找的品牌，您可以使用[自訂視覺](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)服務建立並定型您自己的標誌偵測器來獲得更好的服務。

## <a name="brand-detection-example"></a>品牌偵測範例

下列 JSON 回應說明了在範例影像中偵測到品牌時，電腦視覺傳回的內容。

![有 Microsoft 標籤和標誌的灰色運動衫](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
在某些情況下，品牌偵測器會將標誌影像和樣式化品牌名稱視為兩個不同的標誌。

![有 Microsoft 標籤和標誌的紅色 T 恤](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>使用 API
品牌偵測功能是[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 您取得完整的 JSON 回應時，只需要對於 `"brands"` 一節的內容剖析字串。

* [快速入門：分析影像 (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [快速入門：分析影像 (REST API)](./quickstarts/csharp-analyze.md)