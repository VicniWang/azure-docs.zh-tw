---
title: 發行區域和端點
titleSuffix: Azure Cognitive Services
description: 您發行 LUIS 應用程式的區域，會對應至您在建立 Azure LUIS 端點金鑰時於 Azure 入口網站中指定的區域或位置。 當您發行應用程式時，LUIS 會針對與該金鑰相關的區域自動產生端點 URL。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/10/2019
ms.author: diberry
ms.openlocfilehash: 093c60287b16073a6c04e824dc439f5dc069ec96
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863664"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>撰寫和發佈區域及相關聯的金鑰

您發行 LUIS 應用程式的區域，會對應至您在建立 Azure LUIS 端點金鑰時於 Azure 入口網站中指定的區域或位置。 當您[發行應用程式](./luis-how-to-publish-app.md)時，LUIS 會針對與該金鑰相關的區域自動產生端點 URL。 若要將 LUIS 應用程式發行至多個區域，針對每個區域，您至少需要有一個金鑰。 

## <a name="luis-website"></a>LUIS 網站
根據區域的不同，一共有三個 LUIS 網站。 您必須在相同的區域內進行撰寫和發行。 

|LUIS|區域|
|--|--|
|[www.luis.ai][www.luis.ai]|美國<br>非歐洲<br>非澳大利亞|
|[au.luis.ai][au.luis.ai]|澳大利亞|
|[eu.luis.ai][eu.luis.ai]|歐洲|

## <a name="regions-and-azure-resources"></a>區域和 Azure 資源
應用程式會發佈至與 LUIS 入口網站中新增的 LUIS 資源相關聯的所有區域。 例如，對於在 [www.luis.ai][www.luis.ai] 上建立的應用程式，如果您在 **westus** 中建立 LUIS 資源並將它當作資源新增至應用程式，則會在該區域中發佈應用程式。 

## <a name="public-apps"></a>公開應用程式
公用應用程式會在所有區域中發佈，以便具有區域型 LUIS 資源金鑰的使用者在與其資源金鑰相關聯的區域中存取應用程式。

## <a name="publishing-regions"></a>發行區域

針對在 https://www.luis.ai 上建立的 LUIS 應用程式，除了不能將它發行至[歐洲](#publishing-to-europe)和[澳大利亞](#publishing-to-australia)區域之外，可以發行至其他所有端點。 

您只能將撰寫區域應用程式發行至相對應的發行區域。 若您的應用程式目前位於錯誤的撰寫區域，請匯出該應用程式，然後針對您的發行區域將它匯入至正確的撰寫區域。 

 全球區域 | 撰寫 API 區域和編寫網站| 發行與查詢區域<br>`API region name`   |  端點 URL 格式   |
|-----|------|------|------|
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 印度中部<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 東亞<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 日本東部<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 日本西部<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 南韓中部<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 東南亞<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[澳大利亞](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| 澳洲東部<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[歐洲](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 法國中部<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[歐洲](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 北歐<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[歐洲](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 西歐<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[歐洲](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 英國南部<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 加拿大中部<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國中部<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國東部<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國東部 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國中北部<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國中南部<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國中西部<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國西部<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國西部 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 南美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 巴西南部<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |




## <a name="publishing-to-europe"></a>發行至歐洲

若要發行至歐洲區域，您僅能在 https://eu.luis.ai 建立 LUIS 應用程式。 若您嘗試使用歐洲區域的金鑰於其他區域發行，LUIS 會顯示警告訊息。 請改用 https://eu.luis.ai。 在 [https://eu.luis.ai][eu.luis.ai] 建立的 LUIS 應用程式不會自動移轉至其他區域。 若要移轉它，請匯出然後再匯入該 LUIS 應用程式。

## <a name="publishing-to-australia"></a>發行至澳大利亞

若要發行至澳大利亞區域，您僅能在 https://au.luis.ai 建立 LUIS 應用程式。 若您嘗試使用澳大利亞區域的金鑰於其他區域發行，LUIS 會顯示警告訊息。 請改用 https://au.luis.ai。 在 [https://au.luis.ai][au.luis.ai] 建立的 LUIS 應用程式不會自動移轉至其他區域。 若要移轉它，請匯出然後再匯入該 LUIS 應用程式。

## <a name="endpoints"></a>端點

LUIS 目前有 2 個端點：一個用於撰寫，一個用於文字分析。

|目的|URL|
|--|--|
|編寫|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|文字分析 (查詢預測)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

下表說明上表中的參數 (以大括號 `{}` 表示)。

|參數|目的|
|--|--|
|region|Azure 區域 - 撰寫和發佈具有不同區域|
|appID|URL 路由中使用的 LUIS 應用程式識別碼，於應用程式儀表板上找到|
|q|用戶端應用程式 (例如聊天機器人) 所傳來的語句文字|


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [預先建置的實體參考](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
