---
title: 開始使用 Azure IoT 中樞裝置管理 (Java) | Microsoft Docs
description: 如何使用 Azure IoT 中樞裝置管理來起始遠端裝置重新開機。 您可以使用適用於 Java 的 Azure IoT 裝置 SDK，實作模擬的裝置應用程式 (包含直接方法)，也可以使用適用於 Java 的 Azure IoT 服務 SDK，實作服務應用程式 (叫用直接方法)。
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 46b094e8436d74915a1cd5a0ccec402bee03e33b
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515279"
---
# <a name="get-started-with-device-management-java"></a>開始使用裝置管理 (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教學課程說明如何：

* 使用 Azure 入口網站來建立 IoT 中樞，並且在 IoT 中樞建立裝置識別。

* 建立模擬的裝置應用程式，以實作重新啟動裝置的直接方法。 直接方法是從雲端叫用。

* 建立應用程式，以透過您的 IoT 中樞在模擬的裝置應用程式中叫用 reboot 直接方法。 此應用程式會接著監視從裝置回報的屬性，以查看何時完成重新開機作業。

在本教學課程結尾處，您會有兩個 Java 主控台應用程式：

**simulated-device**。 此應用程式會：

* 使用稍早建立的裝置識別連線到您的 IoT 中樞。

* 收到 reboot 直接方法呼叫。

* 模擬實體重新開機。

* 透過回報屬性報告上次重新開機的時間。

**trigger-reboot**。 此應用程式會：

* 在模擬的裝置應用程式中呼叫直接方法。

* 顯示對模擬的裝置所傳送之直接方法呼叫的回應。

* 顯示更新的回報屬性。

> [!NOTE]
> 如需可用來建置應用程式，以在裝置與您的解決方案後端執行之 SDK 的資訊，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

若要完成本教學課程，您需要：

* Java SE 8。 <br/> [準備您的開發環境](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md)說明如何在 Windows 或 Linux 上安裝本教學課程的 Java。

* Maven 3。  <br/> [準備您的開發環境](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md)說明如何在 Windows 或 Linux 上安裝本教學課程的 [Maven](https://maven.apache.org/what-is-maven.html)。

* [Node.js 版本 0.10.0 或更新版本](http://nodejs.org)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>擷取 IoT 中樞的連接字串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端重新啟動

在本節中，您將建立 Java 主控台應用程式以：

1. 在模擬的裝置應用程式中叫用 reboot 直接方法。

2. 顯示回應。

3. 輪詢從裝置傳送的回報屬性，來判斷何時完成重新開機。

此主控台應用程式會連線到您的 IoT 中樞來叫用直接方法，並讀取回報屬性。

1. 建立稱為 dm-get-started 的空資料夾。

2. 在 dm-get-started 資料夾的命令提示字元下，使用下列命令建立名為 **trigger-reboot** 的 Maven 專案。 以下顯示完整的單一命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. 在命令提示字元中，巡覽至 trigger-reboot 資料夾。

4. 使用文字編輯器，在 trigger-reboot 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。 這個相依性可讓您在應用程式中使用 iot-service-client 套件與 IoT 中樞通訊：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)來檢查最新版的 **iot-service-client**。

5. 將下列 [建置] 節點新增至 [相依性] 節點之後。 此設定會指示 Maven 使用 Java 1.8 來建置應用程式：

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. 儲存並關閉 pom.xml 檔案。

7. 使用文字編輯器開啟 trigger-reboot\src\main\java\com\mycompany\app\App.java 來源檔案。

8. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. 將下列類別層級變數新增到 **App** 類別中。 以您在＜建立 IoT 中樞＞一節中所記下的 IoT 中樞連接字串取代 `{youriothubconnectionstring}`：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. 若要實作執行緒以每隔 10 秒從裝置對應項讀取回報屬性，請將下列巢狀類別新增至 **App** 類別：

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. 修改 **main** 方法的簽章以擲回下列例外狀況：

    ```java
    public static void main(String[] args) throws IOException
    ```

12. 若要在模擬的裝置上叫用 reboot 直接方法，請將下列程式碼新增至 **main** 方法：

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. 若要啟動執行緒以從模擬的裝置輪詢回報屬性，請將下列程式碼新增至 **main** 方法：

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. 若要讓您停止應用程式，請將下列程式碼新增至 **main** 方法：

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. 儲存並關閉 trigger-reboot\src\main\java\com\mycompany\app\App.java 檔案。

16. 建置 **trigger-reboot** 後端應用程式，並更正任何錯誤。 在命令提示字元中，巡覽至 trigger-reboot 資料夾，並執行下列命令：

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您將建立模擬裝置的 Java 主控台應用程式。 此應用程式會從您的 IoT 中樞接聽 reboot 直接方法呼叫，並立即回應該呼叫。 然後會休眠一段時間，以模擬重新開機程序，再使用回報屬性來通知 **trigger-reboot** 後端應用程式重新開機已完成。

1. 在 dm-get-started 資料夾的命令提示字元下，使用下列命令建立名為 **simulated-device** 的 Maven 專案。 以下是完整的單一命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. 在命令提示字元中，瀏覽到 simulated-device 資料夾。

3. 使用文字編輯器，在 simulated-device 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。 這個相依性可讓您在應用程式中使用 iot-service-client 套件與 IoT 中樞通訊：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)來檢查最新版的 **iot-device-client**。

4. 將下列 [建置] 節點新增至 [相依性] 節點之後。 此設定會指示 Maven 使用 Java 1.8 來建置應用程式：

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. 儲存並關閉 pom.xml 檔案。

6. 使用文字編輯器開啟 simulated-device\src\main\java\com\mycompany\app\App.java 來源檔案。

7. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

7. 將下列類別層級變數新增到 **App** 類別中。 將 `{yourdeviceconnectionstring}` 取代為您在＜建立裝置身分識別＞一節中所記下的裝置連接字串：

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

8. 若要實作直接方法狀態事件的回呼處理常式，請將下列巢狀類別新增至 **App** 類別：

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

9. 若要實作裝置對應項狀態事件的回呼處理常式，請將下列巢狀類別新增至 **App** 類別：

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

10. 若要實作屬性事件的回呼處理常式，請將下列巢狀類別新增至 **App** 類別：

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

11. 若要實作執行緒以模擬裝置重新開機，請將下列巢狀類別新增至 **App** 類別。 此執行緒會休眠五秒，再設定 **lastReboot** 回報屬性：

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

12. 若要在裝置上實作直接方法，請將下列巢狀類別新增至 **App** 類別。 當模擬的應用程式收到 **reboot** 直接方法的呼叫時，它會傳回通知給呼叫者，然後啟動執行緒以處理重新開機：

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

13. 修改 **main** 方法的簽章以擲回下列例外狀況：

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

14. 若要具現化 **main** 方法，請將下列程式碼新增至 **DeviceClient**：

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

15. 若要開始接聽直接方法呼叫，請將下列程式碼新增至 **main** 方法：

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

16. 若要關閉裝置模擬器，請將下列程式碼新增至 **main** 方法：

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

17. 儲存並關閉 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

18. 建置 **simulated-device** 後端應用程式，並更正所有錯誤。 在命令提示字元中，瀏覽到 simulated-device 資料夾，並執行下列命令︰

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>執行應用程式

您現在可以開始執行應用程式。

1. 在 simulated-device 資料夾的命令提示字元中，執行下列命令以開始接聽來自您 IoT 中樞的 reboot 方法呼叫：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![會接聽 reboot 直接方法呼叫的 Java IoT 中樞模擬裝置應用程式](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. 在 trigger-reboot 資料夾的命令提示字元中，執行下列命令以呼叫您模擬裝置上來自 IoT 中樞的 reboot 方法：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![會呼叫 reboot 直接方法的 Java IoT 中樞服務應用程式](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. 會回應 reboot 直接方法呼叫的模擬裝置：

    ![Java IoT 中樞模擬裝置會回應直接方法呼叫](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]