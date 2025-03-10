---
title: 開始使用 WebJobs SDK - Azure
description: 使用 WebJobs SDK 進行事件驅動幕後處理的簡介。 了解如何存取 Azure 服務和第三方服務中的資料。
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/15/2019
ms.author: glenga
ms.openlocfilehash: b39c8c19b494422d34bae7145d69e8e95cb16846
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416815"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>開始使用 Azure WebJobs SDK 進行事件驅動幕後處理

本文說明如何建立 Azure WebJobs SDK 專案並於本機執行，再部署至 Azure App Service。

這些指示適用於 [Visual Studio 2017](https://www.visualstudio.com/vs/)，但可使用其他工具完成相同的工作，例如 [Visual Studio Code](https://code.visualstudio.com/)。

## <a name="what-is-the-azure-webjobs-sdk"></a>什麼是 Azure WebJobs SDK

WebJobs SDK 是一套架構，可簡化撰寫背景處理程式碼的工作，以便存取 Azure 服務中的資料。 SDK 的特色是宣告式語法，可供指定應觸發函式的事件，例如加入佇列的新訊息。 類似的宣告式語法可控制觸發函式後的資料讀取和寫入。 這個觸發程序與繫結系統會負責與存取 Azure 和第三方服務相關聯的大部分低階編碼工作。

### <a name="functions-triggers-and-bindings"></a>函式、觸發程序和繫結

WebJobs SDK 專案會定義一或多個「函式」。 函式是在其方法簽章中具有觸發程序屬性的方法。 觸發程序可指定呼叫函式的條件，而繫結可指定要讀取和寫入的項目。 例如，以下函式中的觸發程序屬性會告知執行階段每當 `items` 佇列中出現佇列訊息時，就呼叫該函式。 `Blob` 屬性會告知執行階段使用佇列訊息來讀取 workitems 容器中的 blob。 佇列訊息的內容 &mdash; 在 `queueTrigger` 參數中提供 &mdash; 是 blob 的名稱。

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>2.x 和 3.x 版

指示會說明如何建立 WebJobs SDK 版本 3.x 專案。 3.x 版所帶來的主要變更，就是使用 .NET Core，而不是使用 .NET Framework。 v2.x 與 v3.x 之間的差異會在本文中敘述。

### <a name="azure-functions"></a>Azure Functions

[Azure Functions](../azure-functions/functions-overview.md) 為您的函式提供無伺服器選項。 Functions 是以 WebJobs SDK 為基礎，而且是您不需要直接使用 WebJobs SDK 時的選項。 Azure Functions 2.x 使用 WebJobs SDK 3.x。 如需詳細資訊，請參閱 [Azure Functions 與 WebJobs SDK 的比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 [Azure 帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)以及 [Azure App Service 中的應用程式](overview.md)使用經驗。 若要完成本文中的步驟：

* 透過 **Azure 開發**工作負載[安裝 Visual Studio 2017](/visualstudio/install/)。 如果您已有 Visual Studio 但沒有該工作負載，請選取 [工具] > [取得工具和功能] 來新增該工作負載。
* [建立 App Service 應用程式](app-service-web-get-started-dotnet-framework.md)。 如果您已經有一個可部署 WebJob 的應用程式，您可以使用該應用程式，而不需建立新的。

## <a name="create-a-project"></a>建立專案

1. 在 Visual Studio 中，選取 [檔案 > 新增 > 專案]。

2. 選取 [Windows 傳統桌面] > [主控台應用程式] \(.NET Framework\)。

3. 將專案命名為 WebJobsSDKSample，然後選取 [確定]。

   ![[新增專案] 對話方塊](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs NuGet 套件

安裝 WebJobs SDK 的 NuGet 套件在 v2.x 與 v3.x 之間有些微不同。

### <a name="install-sdk-version-3x-packages"></a>安裝 SDK 版本 3.x 套件

1. 安裝下列 NuGet 套件的最新穩定 3.x 版本：

    * `Microsoft.Azure.WebJobs`
    * `Microsoft.Azure.WebJobs.Extensions`

    以下是 3.0.3 版的 [套件管理員主控台] 命令：

    ```powershell
    Install-Package Microsoft.Azure.WebJobs -version 3.0.2
    Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
    ```

### <a name="install-the-sdk-version-2x-package"></a>安裝 SDK 版本 2.x 套件

1. 安裝 NuGet 套件的最新穩定 2.x 版：`Microsoft.Azure.WebJobs`。

    以下是 2.2.0 版的 [Package Manager Console] 命令：

    ```powershell
    Install-Package Microsoft.Azure.WebJobs -version 2.2.0
    ```

## <a name="create-the-host"></a>建立主機

主機是函式執行階段容器：它會接聽觸發程序及呼叫函式。 您建立的主機依 SDK 版本而定。

### <a name="version-3x"></a>3.x 版

以下會建立實作 [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) 的主機，它是 ASP.NET Core 中的泛型主機。 

1. 在 Program.cs 中，新增 `using` 陳述式：

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. 以下列程式碼取代 `Main` 方法：

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

在 ASP.NET Core 中，主機組態是透過在 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 執行個體上呼叫方法來設定。 如需詳細資訊，請參閱 [.NET 泛型主機](/aspnet/core/fundamentals/host/generic-host)。 `ConfigureWebJobs` 擴充方法會初始化 WebJobs 主機。

### <a name="version-2x"></a>2.x 版

下列程式碼會建立 **JobHost** 物件。

1. 在 Program.cs 中，新增 `using` 陳述式：

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. 以下列程式碼取代 `Main` 方法：

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

主機組態是在用來建立 `JobHost` 的 `JobHostConfiguration` 執行個體中建立。

## <a name="enable-console-logging"></a>啟用主控台記錄

對於 2.x 版和 3.x 版的 WebJobs SDK，建議使用針對 ASP.NET Core 開發的記錄架構。 此架構可提供更佳的效能，且在儲存區媒體和篩選方面更有彈性。 若要深入了解，請參閱 [ASP.NET Core 中的記錄](/aspnet/core/fundamentals/logging)。

在這一節中，您會設定使用此架構的主控台記錄。

1. 安裝下列 NuGet 套件的最新穩定版本：

   * `Microsoft.Extensions.Logging` - 記錄架構。
   * `Microsoft.Extensions.Logging.Console` - 主控台「提供者」。 提供者會將記錄傳送到特定目的地，在此情況下會傳送到主控台。

   以下是 2.2.0 版的 [套件管理員主控台] 命令：

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. 在 Program.cs 中，新增 `using` 陳述式：

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. 記錄程式碼更新取決於您的 SDK 版本：

    **3.x 版**

    呼叫 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 上的 [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) 方法。 [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) 方法會將主控台記錄新增至組態。

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main` 方法現在如下所示：

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    **2.x 版**

    在 `Main` 方法中，新增程式碼以在建立 `JobHost` 之前更新 `JobHostConfiguration`：

    ```cs
    config.DashboardConnectionString = "";
    var loggerFactory = new LoggerFactory();
    config.LoggerFactory = loggerFactory
        .AddConsole();
    ```

    `Main` 方法現在如下所示：

    ```cs
    var config = new JobHostConfiguration();
    config.DashboardConnectionString = "";
    var loggerFactory = new LoggerFactory();
    config.LoggerFactory = loggerFactory
        .AddConsole();
    var host = new JobHost(config);
    host.RunAndBlock();
    ```

    這些更新會執行下列作業：

    * 停用[儀表板記錄](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)。 儀表板是舊版監視工具，而且不建議將儀表板記錄用於高輸送量的生產案例。
    * 透過預設[篩選](webjobs-sdk-how-to.md#log-filtering)新增主控台提供者。

現在您可以新增函式，此函式是由抵達 [Azure 儲存體佇列](../azure-functions/functions-bindings-storage-queue.md)的訊息觸發。

## <a name="install-binding-extensions"></a>安裝繫結延伸模組

從 3.x 版開始，您必須明確地為您在函式中使用的 WebJobs SDK 繫結屬性安裝延伸模組。 唯一的例外狀況是 [Timer 觸發程序](../azure-functions/functions-bindings-timer.md)和 [HTTP 觸發程序](../azure-functions/functions-bindings-http-webhook.md)，它們不需要延伸模組。 2.x 版的 WebJobs SDK 不會使用延伸模組，它們包含在 SDK 中。 如果您使用 2.x 版，請略過並繼續進行下一節。

1. 安裝 [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 套件 3.x 版的最新穩定版本。 

    以下是 3.0.2 版的 [套件管理員主控台] 命令：

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.2
    ```

2. 在 `ConfigureWebJobs` 擴充方法中，在 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 執行個體上呼叫 `AddAzureStorage` 方法以初始化儲存體延伸模組。 此時，`ConfigureWebJobs` 方法如下列範例所示：

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>建立函式

1. 以滑鼠右鍵按一下專案，選取 [新增]  >  [新增項目...]，然後將新的 C# 類別檔案命名為 *Functions.cs*。

1. 在 Functions.cs 中，以下列程式碼取代產生的範本：

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   `QueueTrigger` 屬性會告知執行階段當名為 `queue` 的 Azure 儲存體佇列上有新訊息寫入時，請呼叫此函式。 佇列訊息的內容會提供給 `message` 參數中的方法程式碼。 方法的主體是您處理觸發程序資料的地方。 在此範例中，程式碼只會記錄訊息。

   `message` 參數不必是字串。 您也可以繫結至 JSON 物件、位元組陣列或 [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) 物件。 [請參閱佇列觸發程序使用方式](../azure-functions/functions-bindings-storage-queue.md#trigger---usage)。 每個繫結類型 (例如佇列、blob 或資料表) 都有一組您可以繫結至的不同參數類型。

## <a name="create-a-storage-account"></a>建立儲存體帳戶

在本機執行的 Azure 儲存體模擬器沒有 WebJobs SDK 所需的全部功能。 所以在本節中，您會在 Azure 中建立儲存體帳戶，並設定專案來使用它。

1. 在 Visual Studio 中開啟 [伺服器總管] 並登入 Azure。 以滑鼠右鍵按一下 **Azure** 節點，然後選取 [連線至 Microsoft Azure 訂用帳戶]。

   ![登入 Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. 在 [伺服器總管] 中的 [Azure] 之下，以滑鼠右鍵按一下 [儲存體]，然後選取 [建立儲存體帳戶]。

   ![建立儲存體帳戶功能表](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. 在 [建立儲存體帳戶]  對話方塊中，輸入儲存體帳戶的唯一名稱。

1. 選擇您在其中建立 App Service 應用程式的相同 [區域]，或您附近的區域。

1. 選取 [建立] 。

   ![建立儲存體帳戶](./media/webjobs-sdk-get-started/create-storage-account.png)

1. 在 [伺服器總管] 中的 [儲存體] 之下，選取新的儲存體帳戶。 在 [屬性] 視窗中，選取[連接字串] 值欄位右邊的省略符號 (**...**)。

   ![連接字串省略符號](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. 複製連接字串，並將此值儲存在某處，您即可隨時再次複製它。

   ![複製連接字串](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>設定儲存體在本機執行

WebJobs SDK 會在 Azure 中的 [應用程式設定] 尋找儲存體連接字串。 當您在本機執行時，它會在組態檔或環境變數中尋找此值。

### <a name="appsettingsjson-sdk-version-3x"></a>appsettings.json (SDK 3.x 版)

1. 建立 appsettings.json 檔案，或新增 `AzureWebJobsStorage` 欄位，如下列範例所示：

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. 使用您先前複製的連接字串取代 {儲存體連接字串}。

### <a name="appconfig-sdk-version-2x"></a>App.config (SDK 2.x 版)

1. 將下列 XML 新增至 App.config 檔案中，緊接在左側 `<configuration>` 標記的後面。

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. 使用您先前複製的連接字串取代 {儲存體連接字串}。

稍後，您會在 Azure App Service 的應用程式中新增相同連接字串應用程式設定。

## <a name="test-locally"></a>本機測試

在這一節中，您會在本機建置並執行專案，以及藉由建立佇列訊息來觸發函式。

1. 按 Ctrl+F5 執行專案。

   主控台會顯示執行階段已找到您的函式並等候佇列訊息觸發它。 下列輸出是由 v3.x 主機產生：

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. 關閉主控台視窗。

1. 在 Visual Studio 的 [伺服器總管] 中，展開新儲存體帳戶的節點，然後以滑鼠右鍵按一下 [佇列]。 

1. 選取 [建立佇列]。

1. 輸入 queue 作為佇列的名稱，然後選取 [建立]。

   ![建立佇列](./media/webjobs-sdk-get-started/create-queue.png)

1. 以滑鼠右鍵按一下新佇列的節點，然後選取 [檢視佇列]。

1. 選取 [新增訊息] 圖示。

   ![建立佇列](./media/webjobs-sdk-get-started/create-queue-message.png)

1. 在 [新增訊息] 對話方塊中，輸入 Hello World! 作為 [訊息文字]，然後選取 [確定]。

   ![建立佇列](./media/webjobs-sdk-get-started/hello-world-text.png)

1. 再次執行此專案。

   因為您在 `ProcessQueueMessage` 函式中使用 `QueueTrigger` 屬性，所以 WeJobs SDK 執行階段會在啟動時接聽佇列訊息。 它會在名為 queue 的佇列中發現新佇列訊息並呼叫此函式。

   由於[佇列輪詢指數輪詢](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)，它可能會需要 2 分鐘的時間，讓執行階段尋找訊息及叫用函式。 在[開發模式](webjobs-sdk-how-to.md#host-development-settings)中執行可以縮短此等候時間。

  針對 3.x 版，主控台輸出如下所示：

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. 關閉主控台視窗。

## <a name="add-application-insights-logging"></a>新增 Application Insights 記錄

當專案在 Azure 中執行時，您無法藉由檢視主控台輸出來監視函式執行。 我們建議的監視解決方案為 [Application Insights](../azure-monitor/app/app-insights-overview.md)。 如需詳細資訊，請參閱[監視 Azure Functions](../azure-functions/functions-monitoring.md)。

在這一節中，您可執行下列工作，以在部署至 Azure 之前設定 Application Insights 記錄：

* 確定您有 App Service 應用程式和 Application Insights 執行個體可以使用。
* 將 App Service 應用程式設定為使用 Application Insights 執行個體和您稍早建立的儲存體帳戶。
* 設定專案以便記錄到 Application Insights。

### <a name="create-app-service-app-and-application-insights-instance"></a>建立 App Service 應用程式和 Application Insights 執行個體

1. 如果您還沒有可以使用的 App Service 應用程式，請[建立一個](app-service-web-get-started-dotnet-framework.md)。 當您建立您的應用程式時，您也可以建立已連線的 Application Insights 資源。 當您這樣做時，會為您在應用程式中設定 `APPINSIGHTS_INSTRUMENTATIONKEY`。

1. 如果您還沒有可以使用的 Application Insights 資源，請[建立一個](../azure-monitor/app/create-new-resource.md )。 將 [應用程式類型] 設定為 [一般]，並略過 [複製檢測金鑰] 後面的各節。

1. 如果您已經有想要使用的 Application Insights 資源，請[複製檢測金鑰](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)。

### <a name="configure-app-settings"></a>進行應用程式設定 

1. 在 Visual Studio 的 [伺服器總管] 中，展開 [Azure] 之下的 [App Service] 節點。

1. 展開 App Service 應用程式所在的資源群組，然後以滑鼠右鍵按一下您的 App Service 應用程式。

1. 選取 [檢視設定]。

1. 在 [連接字串] 中，新增下列項目。

   |Name  |連接字串  |資料庫類型|
   |---------|---------|------|
   |AzureWebJobsStorage | {您先前複製的儲存體連接字串}|自訂|

1. 如果 [應用程式設定] 方塊沒有 Application Insights 檢測金鑰，請新增您先前複製的檢測金鑰。 (視您建立 App Service 應用程式的方式而言，檢測金鑰可能已經存在。)

   |Name  |值  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {檢測金鑰} |

1. 使用來自您所用 Application Insights 資源的檢測金鑰取代 {檢測金鑰}。

1. 選取 [ **儲存**]。

1. 將 Application Insights 連線新增至專案，讓您可以在本機執行：

    **3.x 版**

    在 appsettings.json 檔案中新增 `APPINSIGHTS_INSTRUMENTATIONKEY` 欄位，如下列範例所示：

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    **2.x 版**

    將下列 XML 新增至 App.config 檔案中，緊接在連接字串集合的後面。

    ```xml
    <appSettings>
        <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
    </appSettings>
    ```

    無論是在哪個版本中，使用來自您所用 Application Insights 資源的檢測金鑰取代 {檢測金鑰}。

1. 儲存您的變更。

### <a name="add-application-insights-logging-provider"></a>新增 Application Insights 記錄提供者

若要利用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 記錄，請更新您的記錄程式碼以執行下列操作：

* 新增具有預設[篩選](webjobs-sdk-how-to.md#log-filtering)的 Application Insights 記錄提供者；所有資訊和更高層級的記錄會在您於本機執行時移至主控台和 Application Insights。
* 在 `using` 區塊中置入 `LoggerFactory` 物件，確保記錄輸出會在主機結束時排清。

#### <a name="version-3x"></a>3.x 版

1. 為 Application Insights 記錄提供者安裝 NuGet 套件的最新穩定 3.x 版：`Microsoft.Azure.WebJobs.Logging.ApplicationInsights`。

   以下是 3.0.2 版的 [套件管理員主控台] 命令：

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. 開啟 Program.cs，並以下列程式碼取代 `Main` 方法中的程式碼：

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

#### <a name="version-2x"></a>2.x 版

1. 為 Application Insights 記錄提供者安裝 NuGet 套件的最新穩定 2.x 版：`Microsoft.Azure.WebJobs.Logging.ApplicationInsights`。

   以下是 2.2.0 版的 [Package Manager Console] 命令：

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. 為 .NET 組態管理員安裝 NuGet 套件的最新穩定 4.x 版：`System.Configuration.ConfigurationManager`。

   以下是 4.4.1 版的 [Package Manager Console] 命令：

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. 開啟 Program.cs 並新增組態管理員的 `using` 陳述式：

   ```csharp
   using System.Configuration;
   ```

1. 使用下列程式碼取代 `Main` 方法中的程式碼：

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="test-application-insights-logging"></a>測試 Application Insights 記錄

您會在本節中再度於本機執行，確認記錄資料現在正移至 Application Insights 和主控台。

1. 在 Visual Studio 中使用 [伺服器總管] 建立佇列訊息，做法如[稍早](#trigger-the-function-in-azure)一樣，但輸入 Hello App Insights! 作為訊息文字。

1. 執行專案。

   WebJobs SDK 會處理佇列訊息，而您會在主控台視窗中看見記錄。

1. 關閉主控台視窗。

1. 開啟 [Azure 入口網站](https://portal.azure.com/)，然後移至 Application Insights 資源。

1. 選取 [搜尋]。

   ![選取搜尋](./media/webjobs-sdk-get-started/select-search.png)

1. 如果沒看到 Hello App Insights! 訊息，請選取每隔幾分鐘定期 [重新整理]。 (因為 Application Insights 用戶端需要一些時間排清它所處理的記錄，所以記錄不會立即出現。)

   ![Application Insights 中的記錄](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. 關閉主控台視窗。

## <a name="deploy-as-a-webjob"></a>部署為 WebJob

在本節中，您會將專案部署為 WebJob。 您會將它部署至您[稍早建立](#create-app-service-app-and-application-insights-instance)的 App Service 應用程式。 若要在程式碼於 Azure 中執行時進行測試，您會藉由建立佇列訊息來觸發函式引動過程。

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [發佈為 Azure WebJob]。

1. 在 [新增 Azure WebJob] 對話方塊中，選取 [確定]。

   ![新增 Azure WebJob](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio 會自動安裝 NuGet 套件，以供 WebJob 發佈。

1. 在 [發佈] 精靈的 [設定檔] 步驟中，選取 [Microsoft Azure App Service]。

   ![發佈對話方塊](./media/webjobs-sdk-get-started/publish-dialog.png)

1. 在 [App Service] 對話方塊中，選取**您的資源群組 > 您的 App Service 應用程式**，然後選取 [確定]。

   ![App Service 對話方塊](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. 在發佈設定檔產生之後，請選取 [發佈] 。

## <a name="trigger-the-function-in-azure"></a>在 Azure 中觸發函式

1. 確定您不是在本機執行 (如果主控台視窗仍然開啟，請予以關閉)。 否則，本機執行個體可能是第一個處理您建立之任何佇列訊息的執行個體。

1. 在 Visual Studio 的 [佇列] 頁面上，如同以前一般將訊息新增至佇列。

1. 重新整理 [佇列] 頁面，新訊息會消失，因為它已由 Azure 中執行的函式進行處理。

   > [!TIP]
   > 當您在 Azure 中測試時，使用[開發模式](webjobs-sdk-how-to.md#host-development-settings)以確保立即叫用佇列觸發程序函式，並且避免因[佇列輪詢指數輪詢](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)所造成的延遲。

### <a name="view-logs-in-application-insights"></a>在 Application Insights 中檢視記錄

1. 開啟 [Azure 入口網站](https://portal.azure.com/)，然後移至 Application Insights 資源。

1. 選取 [搜尋]。

1. 如果沒看到 Hello Azure! 訊息，請選取每隔幾分鐘定期 [重新整理]。

   您會看到來自在 WebJob 中執行之函式的記錄，包括您在上一節中輸入的 Hello Azure! 文字。

## <a name="add-an-input-binding"></a>新增輸入繫結

輸入繫結可簡化可讀取資料的程式碼。 在此範例中，佇列訊息會是 blob 名稱，而您將使用 blob 名稱在 Azure 儲存體中尋找和讀取 blob。

1. 在 Functions.cs 中，以下列程式碼取代 `ProcessQueueMessage` 方法：

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   在此程式碼中，`queueTrigger` 是[繫結運算式](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns)，這表示它會在執行階段解析成不同的值。  在執行階段，它會有佇列訊息的內容。

1. 新增 `using`：

   ```cs
   using System.IO;
   ```

1. 在儲存體帳戶中建立 Blob 容器。

   a. 在 Visual Studio 的 [伺服器總管] 中，展開您儲存體帳戶的節點，以滑鼠右鍵按一下 [Blob]，然後選取 [建立 Blob 容器]。

   b. 在 [建立 Blob 容器] 對話方塊中，輸入 container 作為容器名稱，然後按一下 [確定]。

1. 將 Program.cs 檔案上傳至 Blob 容器。 (這個檔案在此作為範例；您可以上傳任何文字檔案，並使用檔案的名稱建立佇列訊息。)

   a. 在 [伺服器總管] 中，按兩下您建立的容器節點。

   b. 在 [容器] 視窗中，選取 [上傳] 按鈕。

   ![Blob 上傳按鈕](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. 尋找並選取 Program.cs，然後選取 [確定]。

1. 以 Program.cs 作為訊息文字，在您稍早建立的佇列中建立佇列訊息。

   ![佇列訊息 Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. 執行專案。

   佇列訊息會觸發函式，然後讀取 blob 並且記錄其長度。 主控台輸出如下所示：

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>新增輸出繫結

輸出繫結可簡化可寫入資料的程式碼。 此範例藉由撰寫一份 blob 而不是記錄其大小，以修改前一個範例。 Blob 儲存體繫結會包含在我們先前安裝的 Azure 儲存體延伸模組套件中。

1. 以下列程式碼取代 `ProcessQueueMessage` 方法：

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. 以 Program.cs 作為訊息文字，建立另一則佇列訊息。

1. 執行專案。

   佇列訊息會觸發函式，然後讀取 blob、記錄其長度，然後建立新的 Blob。 主控台輸出相同，但是當您移至 blob 容器視窗並選取 [重新整理] 時，您會看到名為 copy-Program.cs 的新 blob。

## <a name="next-steps"></a>後續步驟

本指南已說明如何建立、執行及部署 WebJobs SDK 專案。

若要顯示進入 WebJobs SDK 專案中的所有項目，系統會指示您從頭開始建立專案。 不過，當您建立下一個專案時，請考慮使用 [雲端] 類別中的 [Azure WebJob] 範本。 此範本會建立一個專案，其已設定 NuGet 套件和範例程式碼。 範例程式碼可能需要變更，才能使用新的記錄架構。

> [!div class="nextstepaction"]
> [深入了解 WebJobs SDK](webjobs-sdk-how-to.md)
