---
title: 呼叫文字分析 API
titlesuffix: Azure Cognitive Services
description: 了解如何呼叫文字分析 REST API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 52c5cb640bfb861fb2da52ee711fe3955a169bcf
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244023"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>如何呼叫文字分析 REST API

針對**文字分析 API** 的呼叫是 HTTP POST/GET 呼叫，可由您以任何語言編寫。 在本文中，我們會使用 REST 和 [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) \(英文\) 來示範重點概念。

每個要求都必須包含您的存取金鑰和 HTTP 端點。 端點會指定您在註冊時所選擇的區域、服務 URL，以及用於要求的資源：`sentiment`、`keyphrases`、`languages` 和 `entities`。 

文字分析是無狀態的，因此不會有資料資產需要管理。 系統會上傳您的文字、於接收到時便進行分析，並立即將結果傳回至呼叫應用程式。

> [!Tip]
> 若要進行單次呼叫以了解 API 的運作方式，您可以從內建的 **API 測試主控台** (可於任何 [API 文件頁面](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) \(英文\) 取得) 傳送 POST 要求。 不需任何設定，而唯一的需求便是將存取金鑰和 JSON 文件貼至要求內。 

## <a name="prerequisites"></a>先決條件

您必須具有含文字分析 API 的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)，以及在您註冊認知服務時所產生的[端點和存取金鑰](text-analytics-how-to-access-key.md)。 

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>JSON 結構描述定義

輸入必須為具有未經處理非結構化文字形式的 JSON。 不支援 XML。 結構描述很簡單，並由下列清單所述的元素所組成。 

您目前可以針對所有文字分析作業提交相同的文件：情感、關鍵片語、語言偵測，以及實體識別。 (未來結構描述很有可能會針對每個分析而有所不同)。

| 元素 | 有效值 | 必要？ | 使用量 |
|---------|--------------|-----------|-------|
|`id` |資料類型是字串，但實際上文件識別碼通常是整數。 | 必要 | 系統會使用您所提供的識別碼作為輸出的結構。 語言代碼、關鍵片語及情感分數會針對要求中的每個識別碼產生。|
|`text` | 非結構化的未經處理文字，最多 5,000 個字元。 | 必要 | 針對語言偵測，文字可以透過任何語言表示。 針對情感分析、關鍵片語擷取及實體識別，文字必須為[支援的語言](../text-analytics-supported-languages.md)。 |
|`language` | 適用於[支援語言](../text-analytics-supported-languages.md)的 2 個字元 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 代碼 | 視情況而異 | 針對情感分析、關鍵片語擷取及實體連結為必要，針對語言偵測為選擇性。 排除它將不會產生錯誤，但分析的效果會因此而減弱。 語言代碼應該對應至您提供的 `text`。 |

如需限制的詳細資訊，請參閱[文字分析概觀 > 資料限制](../overview.md#data-limits)。 

## <a name="set-up-a-request-in-postman"></a>在 Postman 中設定要求

服務接受最多 1 MB 大小的要求。 如果您是使用 Postman (或另一個 Web API 測試工具)，請設定端點以包含您想要使用的資源，並在要求標頭中提供存取金鑰。 每個作業都需要您將適當的資源附加至端點。 

1. 在 Postman 中：

   + 選擇 [Post] 作為要求類型。
   + 將您從入口網站頁面所複製的端點貼上。
   + 附加資源。

  資源端點如下 (您的區域可能有所不同)：

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

2. 設定三個要求標頭：

   + `Ocp-Apim-Subscription-Key`：您的存取金鑰 (從 Azure 入口網站取得)。
   + `Content-Type`：application/json。
   + `Accept`：application/json。

  您的要求應該與下列螢幕擷取畫面類似 (假設使用 **/keyPhrases** 資源)。

   ![具有端點和標頭的要求螢幕擷取畫面](../media/postman-request-keyphrase-1.png)

4. 按一下 [Body] \(主體\) 並選擇 [raw] \(未經處理\) 作為格式。

   ![具有 [body] \(主體\) 設定的要求螢幕擷取畫面](../media/postman-request-body-raw.png)

5. 針對想要進行的分析，貼上一些具備有效格式的 JSON 文件。 如需特定分析的詳細資訊，請參閱下列主題：

  + [語言偵測](text-analytics-how-to-language-detection.md)  
  + [關鍵片語擷取](text-analytics-how-to-keyword-extraction.md)  
  + [情感分析](text-analytics-how-to-sentiment-analysis.md)  
  + [實體辨識 (預覽)](text-analytics-how-to-entity-linking.md)  


6. 按一下 [Send] \(傳送\) 以提交要求。 您每分鐘可以提交最多 100 個要求。 

  在 Postman 中，回應會以單一 JSON 文件的形式顯示在下一個視窗中，且在要求中所提供的每個文件識別碼都會有一個項目。

## <a name="see-also"></a>另請參閱 

 [文字分析概觀](../overview.md)  
 [常見問題集 (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [偵測語言](text-analytics-how-to-language-detection.md)
