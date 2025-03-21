---
title: 從 Azure IoT 中樞控制裝置快速入門 (Java) | Microsoft Docs
description: 在此快速入門中，您會執行兩個範例 Java 應用程式。 其中一個應用程式是後端應用程式，可以從遠端控制連線到中樞的裝置。 另一個應用程式則是模擬可以從遠端控制且連線到中樞的裝置。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/22/2019
ms.author: dobett
ms.openlocfilehash: 30c5026c0f7c8fcce3cee8780622f4c835f043ce
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670939"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-java"></a>快速入門：控制連線到 IoT 中樞的裝置 (Java)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT 中樞是一項 Azure 服務，可讓您從 IoT 裝置將大量遙測擷取到雲端，並從雲端管理您的裝置。 在此快速入門中，您可以使用「直接方法」來控制連線到 IoT 中樞的模擬裝置。 您可以使用直接方法，針對連線到 IoT 中樞的裝置，從遠端變更裝置的行為。

快速入門會使用兩個預先撰寫的 Java 應用程式：

* 可回應後端應用程式直接方法呼叫的模擬裝置應用程式。 為了接收直接方法呼叫，此應用程式會連線到 IoT 中樞上的特定裝置端點。

* 在模擬裝置上呼叫直接方法的後端應用程式。 為了在裝置上呼叫直接方法，此應用程式會連線到 IoT 中樞上的服務端端點。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

您在此快速入門中執行的兩個範例應用程式是使用 Java 所撰寫的。 您的開發電腦上需要 Java SE 8 或更高版本。

您可以從 [Oracle](https://aka.ms/azure-jdks) 下載適用於多種平台的 Java。

您可以使用下列命令，以確認開發電腦上目前的 Java 版本：

```cmd/sh
java -version
```

若要建置範例，您需要安裝 Maven 3。 您可以從 [Apache Maven](https://maven.apache.org/download.cgi) 下載適用於多種平台的 Maven。

您可以使用下列命令，以確認開發電腦上目前的 Maven 版本：

```cmd/sh
mvn --version
```

如果您尚未這樣做，請從 https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip 下載範例 Java 專案並將 ZIP 封存檔解壓縮。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-java.md)，則可以略過此步驟。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-java.md)，則可以略過此步驟。

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以新增 IoT 中樞 CLI 擴充功能和建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyJavaDevice**：您所註冊的裝置名稱。 使用所示的 **MyJavaDevice**。 如果您為裝置選擇不同的名稱，則必須在本文中使用該名稱，並先在應用程式範例中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得_裝置連接字串_：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyJavaDevice \
      --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

## <a name="retrieve-the-service-connection-string"></a>擷取服務連接字串

您也需要_服務連接字串_，讓後端應用程式能夠連線到您的 IoT 中樞並擷取訊息。 下列命令可擷取 IoT 中樞的服務連接字串：

**YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIoTHubName --output table
```

記下服務連接字串，它看起來如下：

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

您稍後會在快速入門中使用此值。 服務連接字符串與裝置連接字串不同。

## <a name="listen-for-direct-method-calls"></a>接聽直接方法呼叫

模擬裝置應用程式會連線到 IoT 中樞上的特定裝置端點、傳送模擬的遙測，並接聽來自中樞的直接方法呼叫。 在此快速入門中，來自中樞的直接方法呼叫會告知裝置變更其傳送遙測的間隔。 模擬的裝置在執行直接方法後，會將通知傳送回您的中樞。

1. 在本機終端機視窗中，瀏覽至範例 Java 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\simulated-device-2** 資料夾。

2. 在您選擇的文字編輯器中開啟 **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** 檔案。

    使用先前所記錄的裝置連接字串來取代 `connString` 變數的值。 然後將變更儲存到 **SimulatedDevice.java** 檔案。

3. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的程式庫，並建置模擬裝置應用程式：

    ```cmd/sh
    mvn clean package
    ```

4. 在本機終端機視窗中，執行下列命令以執行模擬裝置應用程式：

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![執行模擬的裝置](./media/quickstart-control-device-java/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>呼叫直接方法

後端應用程式會連線到 IoT 中樞上的服務端端點。 應用程式會透過您的 IoT 中樞對裝置進行直接方法呼叫，並接聽通知。 IoT 中樞後端應用程式通常會在雲端中執行。

1. 在另一個本機終端機視窗中，瀏覽至範例 Java 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\back-end-application** 資料夾。

2. 在您選擇的文字編輯器中開啟 **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** 檔案。

    使用先前所記錄的服務連接字串來取代 `iotHubConnectionString` 變數的值。 然後將您的變更儲存到 **BackEndApplication.java** 檔案。

3. 在本機終端機視窗中，執行下列命令安裝所需的程式庫並建置後端應用程式：

    ```cmd/sh
    mvn clean package
    ```

4. 在本機終端機視窗中，執行下列命令以執行後端應用程式：

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    下列螢幕擷取畫面顯示應用程式對裝置進行直接方法呼叫並接收通知時的輸出：

    ![執行後端應用程式](./media/quickstart-control-device-java/BackEndApplication.png)

    執行後端應用程式之後，在執行模擬裝置的主控台視窗中將會出現一則訊息，且它傳送訊息的速率也會變更：

    ![模擬用戶端的變更](./media/quickstart-control-device-java/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您從後端應用程式對裝置進行直接方法呼叫，並在模擬裝置應用程式中回應直接方法呼叫。

若要了解如何將「裝置到雲端」訊息路由傳送至雲端中的不同目的地，請繼續下一個教學課程。

> [!div class="nextstepaction"]
> [教學課程：將遙測路由傳送至不同的端點以進行處理](tutorial-routing.md)