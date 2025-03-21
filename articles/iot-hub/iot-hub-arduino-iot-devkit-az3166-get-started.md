---
title: IoT DevKit 到雲端 -- 將 IoT DevKit AZ3166 連線到 Azure IoT 中樞 | Microsoft Docs
description: 在本教學課程中了解如何設定 IoT DevKit AZ3166 並將其連線到 Azure IoT 中樞，以便它可以將資料傳送到 Azure 雲端平台。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: wesmc
ms.openlocfilehash: 2f480fd4d5d7d6261776660f1a7eab587320c849
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052505"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>將 IoT DevKit AZ3166 連線至 Azure IoT 中樞

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

您可以使用 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 來開發及設定運用 Microsoft Azure 服務的物聯網 (IoT) 解決方案原型。 其包含具有豐富週邊設備與感應器的 Arduino 相容開發板、開放原始碼開發板套件，以及不斷擴展的[專案目錄](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-do"></a>您要做什麼

將 DevKit 連線到您建立的 Azure IoT 中樞。 然後收集來自感應器的溫度與溼度，並傳送資料至 IoT 中樞。

還沒有 DevKit 嗎？ 請試用 [DevKit 模擬器](https://azure-samples.github.io/iot-devkit-web-simulator/)或[購買 DevKit](https://aka.ms/iot-devkit-purchase)。

## <a name="what-you-learn"></a>您學到什麼

* 如何將 IoT DevKit 連線到無線存取點以及準備您的開發環境。
* 如何建立 IoT 中樞並註冊 MXChip IoT DevKit 適用的裝置。
* 如何在 MXChip IoT DevKit 上執行範例應用程式來收集感應器資料。
* 如何將感應器資料傳送至 IoT 中樞。

## <a name="what-you-need"></a>您需要什麼

* 附有 Micro-USB 傳輸線的 MXChip IoT DevKit 開發板。 [立即買一個](https://aka.ms/iot-devkit-purchase)。
* 執行 Windows 10 或 macOS 10.10+ 的電腦。
* 有效的 Azure 訂用帳戶。 [啟動 30 天免費試用 Microsoft Azure 帳戶](https://azureinfo.microsoft.com/us-freetrial.html)。
  
## <a name="prepare-your-hardware"></a>準備硬體

將下列硬體連接到您的電腦：

* DevKit 面板
* Micro-USB 傳輸線

![必要的硬體](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

若要將 DevKit 連線至電腦，請執行下列步驟：

1. 將 USB 端連線到電腦。

2. 將 Micro-USB 端連線到 DevKit。

3. 綠色的電源 LED 燈可確認連線成功。

   ![硬體連線](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>設定 Wi-Fi

IoT 專案依賴網際網路連線。 請使用下列指示設定 DevKit，使其連線至 Wi-Fi。

### <a name="enter-ap-mode"></a>進入 AP 模式

按住按鈕 B，按下再放開 [Reset] 按鈕，然後放開按鈕 B。DevKit 會進入 AP 模式以供設定 Wi-Fi。 此畫面會顯示 DevKit 的服務組識別碼 (SSID) 以及設定入口網站 IP 位址。

![Reset 按鈕、按鈕 B 和 SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

![設定 AP 模式](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>連線至 DevKit AP

現在，使用另一個已啟用 Wi-Fi 的裝置 (電腦或行動電話) 來連線至 DevKit SSID (上圖中以紅框特別標示者)。 密碼則保持空白。

![網路資訊和 [連線] 按鈕](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>為 DevKit 設定 Wi-Fi

在電腦或行動電話的瀏覽器上開啟 DevKit 畫面中所顯示的 IP 位址，選取您希望 DevKit 連線的 Wi-Fi 網路，然後輸入密碼。 選取 [ **連接**]。

![[密碼] 方塊和 [連線] 按鈕](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

連線成功後，DevKit 會在幾秒內重新啟動。 然後，您會在畫面上看到 Wi-Fi 名稱和 IP 位址：

![Wi-Fi 名稱和 IP 位址](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> 圖中顯示的 IP 位址未必會符合 DevKit 畫面上所指派和顯示的實際 IP 位址。 這是正常現象，因為 Wi-Fi 會使用 DHCP 來動態指派 IP。

設定好 Wi-Fi 後，即使將裝置的接線拔除，該裝置上仍會留有用於該連線的認證。 例如，如果您設定 DevKit 來使用家中的 Wi-Fi，然後將 DevKit 帶到辦公室，您將必須重新設定 AP 模式 (從＜進入 AP 模式＞一節的步驟開始)，才能將 DevKit 連線到辦公室 Wi-Fi。 

## <a name="start-using-the-devkit"></a>開始使用 DevKit

在 DevKit 上執行的預設應用程式會檢查最新版本的韌體，並對您顯示某些感應器診斷資料。

### <a name="upgrade-to-the-latest-firmware"></a>升級至最新的韌體

> [!NOTE]
> 自 v1.1 起，DevKit 在開機載入器中啟用了 ST-SAFE。 如果您執行的是 v1.1 之前的版本，則必須升級韌體。

如果您需要升級韌體，畫面會顯示目前的和最新的韌體版本。 若要升級，請遵循[升級韌體](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)指南。

![顯示目前的和最新的韌體版本](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> 這項工作只需執行一次。 在 DevKit 上開始進行開發並上傳應用程式後，最新的韌體就會隨著應用程式一起進入開發板中。

### <a name="test-various-sensors"></a>測試各種感應器

按下按鈕 B 來測試感應器。 不斷按下再放開 B 按鈕，以循環測試各個感應器。

![按鈕 B 和感應器顯示](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>準備開發環境

### <a name="install-azure-iot-tools"></a>安裝 Azure IoT Tools

建議使用適用於 Visual Studio Code 的 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 延伸模組套件在 DevKit 上進行開發。 Azure IoT Tools 包含 [Azure IoT Device Workbench](https://aka.ms/iot-workbench) 和 [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit)，前者可讓您在各種 IoT devkit 裝置上進行開發和偵錯，後者可讓您管理 Azure IoT 中樞並與其互動。

您可以觀看下列 [Channel 9 影片](https://channel9.msdn.com/)，大致了解其功用：
* [Introduction to the new IoT Workbench extension for VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code) (適用於 VS Code 的新 IoT Workbench 延伸模組簡介)
* [What's new in the IoT Toolkit extension for VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code) (適用於 VS Code 的 IoT Toolkit 延伸模組新功能)

請遵循下列步驟以準備 DevKit 的開發環境：

1. 安裝 [Arduino IDE](https://www.arduino.cc/en/Main/Software)。 它會提供必要的工具鏈，以便編譯和上傳 Arduino 程式碼。
    * **Windows**：使用 Windows Installer 版本。 請勿從 App Store 安裝。
    * **macOS**：將解壓縮的 **Arduino.app** 拖放到 `/Applications` 資料夾中。
    * **Ubuntu**：將它解壓縮到資料夾中，例如 `$HOME/Downloads/arduino-1.8.8`

2. 安裝 [Visual Studio Code](https://code.visualstudio.com/)，這是具有強大開發人員工具 (例如 IntelliSense 程式碼完成和偵錯) 的跨平台原始程式碼編輯器。

3. 啟動 VS Code，在延伸模組市集中尋找 **Arduino** 並加以安裝。 此延伸模組提供在 Arduino 平台上進行開發的增強體驗。
    ![安裝 Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 在延伸模組市集中尋找 **Azure IoT Tools** 並加以安裝。
    ![安裝 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

5. 為 VS Code 設定 Arduino 設定。

    在 Visual Studio Code 中，按一下 [檔案] > [喜好設定] > [設定]。 然後按一下 [...] 和 [開啟 settings.json]。
    ![安裝 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    依據您的平台，新增下列幾行來設定 Arduino： 

    * **Windows**：
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**：

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**：
    
        以您的使用者名稱取代下方的 **{username}** 預留位置。

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. 按一下 `F1` 以開啟命令選擇區，輸入並選取 [Arduino:Board Manager]。 搜尋 **AZ3166** 並安裝最新版本。
    ![安裝 DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>安裝 ST-Link 驅動程式

[ST-Link/V2](http://www.st.com/en/development-tools/st-link-v2.html) 是 IoT DevKit 用來與開發電腦進行通訊的 USB 介面。 遵循 OS 特有步驟，以允許電腦存取您的裝置。

* **Windows**：從 [STMicroelectronics 網站](http://www.st.com/en/development-tools/stsw-link009.html)下載並安裝 USB 驅動程式。
* **macOS**：macOS 不需要任何驅動程式。
* **Ubuntu**：在終端機中執行下列程式碼並登出後再登入，以讓群組變更生效：
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

您現在已可準備及設定您的開發環境。 讓我們建置 IoT 的 "Hello World" 範例：將溫度遙測資料傳送至 Azure IoT 中樞。

## <a name="build-your-first-project"></a>建立您的第一個專案

1. 確定 IoT DevKit **未**連線至您的電腦。 先啟動 VS Code，然後將 DevKit 連線至您的電腦。


1. 按一下 `F1` 以開啟命令選擇區，輸入並選取 [Azure IoT Device Workbench：**開啟範例...]**。然後選取 [IoT DevKit] 作為面板。

1. 在 [IoT Workbench 範例] 頁面上，尋找 [開始使用]，然後按一下 [開啟範例]。 然後選取預設路徑來下載範例程式碼。
    ![開啟範例](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. 在新開啟的專案視窗中，按一下 `F1` 以開啟命令選擇區，鍵入並選取 [Azure IoT Device Workbench:Provision Azure Services...] \(Azure IoT Device Workbench：佈建 Azure 服務...\)。遵循逐步指南，完成您的 Azure IoT 中樞佈建並建立 IoT 中樞裝置。
    ![雲端佈建](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. 在右下方的狀態列中，檢查 **MXCHIP AZ3166** 是否顯示為已選取的面板，且使用 **STMicroelectronics** 作為序列埠。
    ![選取面板和 COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. 按一下 `F1` 以開啟命令選擇區，鍵入並選取 [Azure IoT Device Workbench:Configure Device Settings...] \(Azure IoT Device Workbench：設定裝置設定...\)，然後選取 [Config Device Connection String] \(設定裝置連接字串\) > [Select IoT Hub Device Connection String] \(選取 IoT 中樞裝置連接字串\)。

1. 在 DevKit 上，按住**按鈕 A**，按下再放開 [重設] 按鈕，然後放開**按鈕 A**。您的 DevKit 會進入設定模式並儲存連接字串。
    ![連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. 再按一次 `F1`，鍵入並選取 [Azure IoT Device Workbench:**上傳裝置程式碼]**。 即會開始編譯程式碼，並上傳到 DevKit。
    ![Arduino 上傳](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit 會重新開機，然後開始執行程式碼。

> [!NOTE]
> 如果發生任何錯誤或中斷，您一律可藉由再次執行命令來復原。

## <a name="test-the-project"></a>測試專案

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>檢視要傳送至 Azure IoT 中樞的遙測資料

按一下狀態列上的電源插頭圖示，以開啟「序列監視器」：![序列監視器](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

當您看到以下結果時，表示範例應用程式已成功執行：

* 「序列監視器」會顯示傳送至 IoT 中樞的訊息。
* MXChip IoT DevKit 上的 LED 燈在閃爍。

![序列監視器輸出](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>檢視 Azure IoT 中樞所接收的遙測資料

您可以使用 [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 來監視 IoT 中樞的裝置到雲端 (D2C) 訊息。

1. 登入 [Azure 入口網站](https://portal.azure.com/)，尋找您所建立的 IoT 中樞。
    ![Azure 入口網站](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. 在 [共用存取原則] 窗格中，按一下 [iothubowner] 原則，然後記下您 IoT 中樞的連接字串。
    ![Azure IoT 中樞連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. 在 VS Code 中，按一下 `F1`，鍵入並選取 [Azure IoT 中樞：設定 IoT 中樞連接字串]。 將連接字串複製到其中。
    ![設定 Azure IoT 中樞連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 展開右側的 [AZURE IOT 中樞裝置] 窗格，以滑鼠右鍵按一下您所建立的裝置名稱，然後選取 [開始監視 D2C 訊息]。
    ![監視 D2C 訊息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. 在 [輸出] 窗格中，您可以看到 IoT 中樞的連入 D2C 訊息。
    ![D2C 訊息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="problems-and-feedback"></a>問題與意見反應

如果發生問題，您可以查看 [IoT DevKit 常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)中的解決方案，或從 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) 連絡我們。 您也可以在這個頁面留下評論，以對我們提供意見反應。

## <a name="next-steps"></a>後續步驟

您已成功將 MXChip IoT DevKit 連線到 IoT 中樞，並將擷取到的感應器資料傳送至 IoT 中樞。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
