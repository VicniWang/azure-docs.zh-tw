---
title: 訂用帳戶金鑰
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 會使用兩個金鑰，一是用來建立模型的免費撰寫金鑰，另一個則是計量付費端點金鑰，用來查詢使用者談話的預測端點。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: diberry
ms.openlocfilehash: 1898d6e5119d3cbc2061aff8d4a7e673abd83198
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097452"
---
# <a name="authoring-and-query-prediction-endpoint-keys-in-luis"></a>LUIS 中的撰寫與查詢預測端點金鑰
LUIS 使用兩個金鑰：[撰寫](#programmatic-key)和[端點](#endpoint-key)。 當您建立 LUIS 帳戶時，系統會自動為您建立撰寫金鑰。 當您已做好發佈 LUIS 應用程式的準備時，必須[建立端點金鑰](luis-how-to-azure-subscription.md)、[將它指派](luis-how-to-azure-subscription.md)給 LUIS 應用程式，然後[將它與端點查詢搭配使用](#use-endpoint-key-in-query)。 

|Key|目的|
|--|--|
|[授權金鑰](#programmatic-key)|撰寫、發佈、管理共同作業者、進行版本設定|
|[端點金鑰](#endpoint-key)| 查詢|

請務必在您也要進行發佈及查詢的[區域](luis-reference-regions.md#publishing-regions)中撰寫 LUIS 應用程式。

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>撰寫金鑰

撰寫金鑰也稱為入門金鑰，會在您建立 LUIS 帳戶時自動為您建立，並且是免費的。 針對每個撰寫[區域](luis-reference-regions.md)，您都會有一個跨所有 LUIS 應用程式的撰寫金鑰。 提供撰寫金鑰的目的是要撰寫 LUIS 應用程式或測試端點查詢。 

若要尋找撰寫金鑰，請登入 [LUIS](luis-reference-regions.md#luis-website)，然後按一下右上方瀏覽列中的帳戶名稱以開啟 [帳戶設定]。

![撰寫金鑰](./media/luis-concept-keys/programatic-key.png)

當您想要進行**生產環境端點查詢**時，請建立 Azure [LUIS 訂用帳戶](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)。 

> [!CAUTION]
> 為了方便起見，許多範例都使用撰寫金鑰，因為它在其[配額](luis-boundaries.md#key-limits)中提供一些端點呼叫。  

## <a name="endpoint-key"></a>端點金鑰
當您需要**生產環境端點查詢**時，請建立 Azure 資源，然後將其指派給 LUIS 應用程式。 

[!INCLUDE [Azure resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Azure 資源建立程序完成時，請[將金鑰指派](luis-how-to-azure-subscription.md)給應用程式。 

    * 端點金鑰會根據您建立金鑰時所指定的使用量方案，來允許端點叫用次數配額。 如需定價資訊，請參閱[認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h)。

    * 端點金鑰可以用於您所有 LUIS 應用程式，也可以用於特定 LUIS 應用程式。 

    * 請勿將端點金鑰用於撰寫 LUIS 應用程式。 

## <a name="use-endpoint-key-in-query"></a>在查詢中使用端點金鑰
LUIS 端點接受兩種樣式的查詢，兩者都使用端點金鑰，但使用位置不同：

|指令動詞|範例 URL 和金鑰位置|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>`subscription-key` 的查詢字串值<br><br>將 `subscription-key` 的端點查詢值從撰寫 (入門) 金鑰變更為新的端點金鑰，以便使用 LUIS 端點金鑰配額率。 如果您建立金鑰並指派金鑰，但沒有變更 subscription-key 的端點查詢值，便不會使用端點金鑰配額。|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> `Ocp-Apim-Subscription-Key` 的標頭值<br><br>將 `Ocp-Apim-Subscription-Key` 的端點查詢值從撰寫 (入門) 金鑰變更為新的端點金鑰，以便使用 LUIS 端點金鑰配額率。 如果您建立金鑰並指派金鑰，但沒有變更 `Ocp-Apim-Subscription-Key` 的端點查詢值，便不會使用端點金鑰配額。|

先前 URL 中使用的應用程式識別碼 `df67dcdb-c37d-46af-88e1-8b97951ca1c2` 是適用於[互動式示範](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)的公用 IoT 應用程式。 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>API 的 Ocp-Apim-Subscription-Key 使用方式
LUIS API 會使用 `Ocp-Apim-Subscription-Key` 標頭。 此標頭名稱並不會根據您使用哪個金鑰及哪組 API 而有所變更。 請將此標頭設定為撰寫金鑰來撰寫 API。 如果您使用端點，則請將此標頭設定為端點金鑰。 

您無法傳遞端點金鑰來撰寫 API。 如果您這樣做，就會收到 401 錯誤 - 存取被拒，因為端點金鑰無效。 

## <a name="key-limits"></a>金鑰限制
請參閱[金鑰限制](luis-boundaries.md#key-limits)和 [Azure 區域](luis-reference-regions.md)。 撰寫金鑰是免費金鑰並用於撰寫。 LUIS 端點金鑰具有免費層，但必須由您建立，並在 [發佈] 頁面上與您的 LUIS 應用程式建立關聯。 它無法用於撰寫，只能用於端點查詢。

發佈區域與撰寫區域不同。 請務必在與您想要的發佈區域對應的撰寫區域中建立應用程式。

## <a name="key-limit-errors"></a>金鑰限制錯誤
如果您超出每秒配額，就會收到 HTTP 429 錯誤。 如果您超出每月配額，則會收到 HTTP 403 錯誤。 藉由取得 LUIS [端點](#endpoint-key)金鑰、在 [LUIS](luis-reference-regions.md#luis-website) 網站的 [發佈] 頁面上將金鑰[指派](luis-how-to-azure-subscription.md)給應用程式，來修正這些問題。

## <a name="assignment-of-the-endpoint-key"></a>指派端點金鑰

您可以在 [LUIS 入口網站](https://www.luis.ai)中或透過對應的 API 來[指派](luis-how-to-azure-subscription.md)端點金鑰。 


## <a name="next-steps"></a>後續步驟

* 了解撰寫金鑰和端點金鑰的相關[概念](luis-how-to-azure-subscription.md)。
