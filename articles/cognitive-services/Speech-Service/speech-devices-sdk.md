---
title: 關於語音裝置 SDK - 語音服務
titleSuffix: Azure Cognitive Services
description: 開始使用語音裝置 SDK。 語音服務可與各種裝置和音訊來源搭配運作。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 語音裝置 SDK 是已預先微調的程式庫，可搭配有目的建置的麥克風陣列開發套件。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d7ef018b376b96f967a065857839761fc5822239
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876289"
---
# <a name="about-the-speech-devices-sdk-preview"></a>關於語音裝置 SDK (預覽)

[語音服務](overview.md)可與各種裝置和音訊來源搭配運作。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 語音裝置 SDK 是已預先微調的程式庫，可搭配有目的建置的麥克風陣列開發套件。

語音裝置 SDK 可協助您：
* 快速測試新的語音案例。
* 更輕鬆地將雲端式語音服務整合到您的裝置。
* 為客戶建立卓越的使用者體驗。

語音裝置 SDK 會取用[語音 SDK](speech-sdk.md)， 並且使用語音 SDK 將我們的進階音訊處理演算法所處理的音訊，從裝置的麥克風陣列傳送至[語音服務](overview.md)。 其可使用多聲道音訊，透過噪音抑制、回音消除、波束成形和消除迴響，來提供更精確的遠場[語音辨識](speech-to-text.md)。

您也可以使用語音裝置 SDK 打造擁有您專屬[自訂喚醒字](speech-devices-sdk-create-kws.md)的周邊裝置，以及開啟貴品牌所獨有的使用者互動語音提示。

Speech Devices SDK 有助於促成各種已啟用語音功能的案例，例如得來速訂購系統、市集內或住家行動助理，以及智慧型喇叭。 您可以利用文字來回應使用者、以預設或[自訂語音](how-to-customize-voice-font.md)回答他們、提供搜尋結果、[翻譯](speech-translation.md)成其他語言等等。 我們期待看到您所打造的不凡成果！

## <a name="development-kit-providers"></a>開發套件提供者

目前可提供以下這些完整的端對端系統參考設計：

|||
|-|-|
|[![ROOBO 標誌](media/speech-devices-sdk/roobo-logo.png)](http://ddk.roobo.com/)|ROOBO 針對家用電子設備、汽車、機器人、玩具及其他產業，提供完整的人工智慧 (AI) 系統解決方案。 ROOBO 的參考設計透過與 Microsoft 語音服務整合，大幅減少開發上市時間。 [造訪 ROOBO](http://ddk.roobo.com/)。|

## <a name="next-steps"></a>後續步驟

若要開始，請取得[免費的 Azure 帳戶](https://azure.microsoft.com/free/ai/)並註冊 Speech Devices SDK。

> [!div class="nextstepaction"]
> [註冊 Speech Devices SDK](get-speech-devices-sdk.md)
