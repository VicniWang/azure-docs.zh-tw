---
title: 使用 Azure Functions 和認知服務建立 IoT DevKit 翻譯工具 | Microsoft Docs
description: 使用 IoT DevKit 上的麥克風來接收語音訊息，然後使用 Azure 認知服務將其處理為以英文表示的翻譯文字
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 038b1d9fa319837f3877c20c9fc3b1b83970e7b4
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158613"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>使用 IoT DevKit AZ3166 搭配 Azure Functions 和認知服務製作語言翻譯工具

在本文中，您可了解如何藉由使用 [Azure 認知服務](https://azure.microsoft.com/services/cognitive-services/)，讓 IoT DevKit 成為語言翻譯工具。 它會記錄您的語音，並將它轉譯成在 DevKit 畫面上顯示的英文文字。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是全方位 Arduino 相容板，具有豐富的週邊設備和感應器。 您可以使用 Visual Studio Code 中的 [Azure IoT Device Workbench](https://aka.ms/iot-workbench) 或 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 擴充套件來對它進行開發。 [專案目錄](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)包含範例應用程式，可協助您設計 IoT 解決方案原型。

## <a name="before-you-begin"></a>開始之前

若要完成本教學課程中的步驟，請先執行下列工作：

* 遵循[將 IoT DevKit AZ3166 連線到雲端中的 Azure IoT 中樞](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)中的步驟來準備您的 DevKit。

## <a name="create-azure-cognitive-service"></a>建立 Azure 認知服務

1. 在 Azure 入口網站中，按一下 [建立資源] 並搜尋 [語音]。 填寫表單以建立語音服務。
  ![語音服務](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. 移至您剛剛建立的語音服務，按一下 [金鑰] 區段以複製並記下 **Key1**，供 DevKit 加以存取。
  ![複製金鑰](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>開啟範例專案

1. 確定 IoT DevKit **未**連線至您的電腦。 先啟動 VS Code，然後將 DevKit 連線至您的電腦。

1. 按一下 `F1` 以開啟命令選擇區，輸入並選取 [Azure IoT Device Workbench：**開啟範例...]**。然後選取 [IoT DevKit] 作為面板。

1. 在 [IoT Workbench 範例] 頁面上找出 [DevKit 翻譯工具]，然後按一下 [開啟範例]。 然後選取預設路徑來下載範例程式碼。
  ![開啟範例](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>搭配使用語音服務與 Azure Functions

1. 在 VS Code 中按一下 `F1`，然後輸入並選取 [Azure IoT Device Workbench：**佈建 Azure 服務...]**。![佈建 Azure 服務](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. 依照下列步驟完成 Azure IoT 中樞和 Azure Functions 的佈建。
  ![佈建步驟](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

  記下您所建立的 Azure IoT 中樞裝置名稱。

1. 開啟 `Functions\DevKitTranslatorFunction.cs`，並以您記下的裝置名稱與語音服務金鑰更新下列程式碼行。
  ```csharp
  // Subscription Key of Speech Service
  const string speechSubscriptionKey = "";

  // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
  const string speechServiceRegion = "";

  // Device ID
  const string deviceName = "";
  ```

1. 按一下 `F1`，然後輸入並選取 [Azure IoT Device Workbench：**部署至 Azure...]**。如果 VS Code 要求您確認是否要重新部署，請按一下 [是]。
  ![部署警告](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. 確定部署成功。
  ![部署成功](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. 在 Azure 入口網站中移至 [函式應用程式] 區段，找出剛才建立的 Azure 函式應用程式。 按一下 `devkit_translator`，然後按一下 [</> 取得函式 URL] 以複製 URL。
  ![複製函式 URL](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. 將 URL 貼到 `azure_config.h` 檔案中。
  ![Azure 組態](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

  > [!NOTE]
  > 如果函式應用程式未正常運作，請參閱此[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function)區段來解決問題。

## <a name="build-and-upload-device-code"></a>建置並上傳裝置程式碼

1. 藉由下列動作將 DevKit 切換為 [組態模式]：
  * 按住 **A** 按鈕。
  * 按下再放開 [重設] 按鈕。

  您會看到畫面顯示 DevKit 識別碼和 [組態]。

  ![DevKit 組態模式](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. 按一下 `F1`，然後輸入並選取 [Azure IoT Device Workbench：**設定裝置設定...] > [設定裝置連接字串]**。 選取 [選取 IoT 中樞裝置連接字串]，以將其設定為 DevKit。
  ![設定連接字串](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. 作業順利完成後，您會看到通知。
  ![連接字串設定成功](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. 再按一次 `F1`，然後輸入並選取 [Azure IoT Device Workbench：**上傳裝置程式碼]**。 此時會開始編譯程式碼，並上傳到 DevKit。
  ![裝置上傳](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>測試專案

在應用程式初始化之後，依照 DevKit 畫面上的指示操作。 預設來源語言是中文。

若要選取另一種語言進行翻譯：

1. 按下按鈕 A 進入設定模式。

2. 按下按鈕 B 捲動所有支援的來源語言。

3. 按下按鈕 A 確認您選擇的來源語言。

4. 在說話時按住按鈕 B，然後放開按鈕 B 開始翻譯。

5. 以英文表示的翻譯文字會顯示在畫面上。

![捲動以選取語言](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![翻譯結果](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

在翻譯結果畫面上，您可以：

- 按下按鈕 A 和 B 以捲動並選取來源語言。

- 按 B 按鈕進行交談。 若要傳送語音和取得翻譯文字，請放開 B 按鈕。

## <a name="how-it-works"></a>運作方式

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT DevKit 會記錄您的語音，然後發佈 HTTP 要求以觸發 Azure Functions。 Azure Functions 會呼叫認知服務語音翻譯工具 API 來進行翻譯。 在 Azure Functions 取得翻譯文字之後，它會將 C2D 訊息傳送到裝置。 然後翻譯會顯示在畫面上。

## <a name="problems-and-feedback"></a>問題與意見反應

如果發生問題，請參閱 [IoT DevKit 常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或使用下列管道與我們連絡：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>後續步驟

您已了解如何使用 Azure Functions 和認知服務讓 IoT DevKit 成為翻譯工具。 在此操作說明中，您已了解如何：

> [!div class="checklist"]
> * 使用 Visual Studio Code 工作自動進行雲端佈建
> * 設定 Azure IoT 裝置連接字串
> * 部署 Azure Function
> * 測試語音訊息翻譯

請前進到其他教學課程來了解如何：

> [!div class="nextstepaction"]
> [將 IoT DevKit AZ3166 連線到 Azure IoT 遠端監視解決方案加速器](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
