---
title: 關於語音轉換文字 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音轉換文字 API 可將音訊資料流轉譯成可供應用程式顯示或當作輸入來處理的文字。 此服務可透過 SDK 和 RESTful 端點來存取。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 5012245a79295f1e05079f6c0a368ac832b8974a
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978573"
---
# <a name="about-the-speech-to-text-api"></a>關於語音轉換文字 API

**語音轉換文字** API 將音訊資料流「謄寫」成文字，而您的應用程式可以向使用者顯示該文字或當作命令輸入處理。 API 可以和 SDK 用戶端程式庫 (適用於支援的平台和語言) 或 REST API 搭配使用。

**語音轉換文字** API 提供以下功能：

- Microsoft 的進階語音辨識技術 - Cortana、Office 和其他 Microsoft 產品都使用此技術。

- 即時連續辨識。 **語音轉換文字**可讓使用者將音訊即時謄寫成文字。 它也支援接收已經進行辨識之單字的中繼結果。 服務會自動辨識語音的結尾。 使用者也可以選擇其他格式設定選項，包括轉換成大寫和標點符號、粗話遮罩，以及反向文字正規化。

- 結果會同時以「語彙」和「顯示」形式傳回 (針對「語彙」結果，請參閱範例或 API 中的 DetailedSpeechRecognitionResult)。

- 支援許多語言和方言。 如需每個辨識模式中支援語言的完整清單，請參閱[支援的語言](language-support.md#speech-to-text)。

- 自訂的語言和原音模型，可讓您針對使用者的專業領域詞彙、說話環境以及說話方式，量身訂做您的應用程式。

- 自然語言理解。 透過整合 [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS)，您可以從語音衍伸出意圖和實體。 使用者不必知道您應用程式的詞彙，但他們可以使用自己的話描述他們想要什麼。

- 如果您在語音設定物件 (SpeechConfig.OutputFormat 屬性) 上指定詳細輸出，服務就會傳回信賴分數。 接著，您可以針對結果使用 Best() 方法，或直接從服務所傳回的 JSON 取得分數 (例如 result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult))。

## <a name="api-capabilities"></a>API 功能

**語音轉換文字** API 的某些功能 (特別是關於自訂的部分) 可透過 REST 取得。 下表摘要說明存取 API 的每個方法的功能。 如需完整的功能清單和 API 詳細資料，請參閱 [Swagger 參考](https://westus.cris.ai/swagger/ui/index) \(英文\)。

| 使用案例 | REST | SDK |
|-----|-----|-----|----|
| 謄寫簡短的語句，如命令 (長度小於 15 秒)；沒有過渡結果 | yes | yes |
| 謄寫較長的語句 (大於 15 秒) | 否 | yes |
| 謄寫含選擇性過渡結果的串流音訊 | 否 | yes |
| 透過 LUIS 了解說話者的意圖 | 否\* | yes |
| 建立正確性測試 | yes | 否 |
| 上傳資料集供模型調整使用 | yes | 否 |
| 建立及管理語音模型 | yes | 否 |
| 建立及管理模型部署 | yes | 否 |
| 管理訂用帳戶 | yes | 否 |
| 建立及管理模型部署 | yes | 否 |
| 建立及管理模型部署 | yes | 否 |

> [!NOTE]
> REST API 會實作節流，該節流會將 API 要求限制為每 5 秒 25 個。 訊息標頭會通知限制

\* *您可以使用個別的 LUIS 訂用帳戶來衍伸出 LUIS 意圖和實體。使用此訂用帳戶，SDK 可以為您呼叫 LUIS，並提供實體和意圖結果以及語音謄寫。若使用 REST API，您可以自行呼叫 LUIS，以使用您的 LUIS 訂用帳戶來衍伸出意圖和實體。*

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [快速入門：如何以 C# 辨識語音](quickstart-csharp-dotnet-windows.md)
* [了解如何以 C# 從語音辨識意圖](how-to-recognize-intents-from-speech-csharp.md)
