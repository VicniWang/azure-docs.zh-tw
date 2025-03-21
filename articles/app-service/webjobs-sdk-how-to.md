---
title: 如何使用 WebJobs SDK - Azure
description: 深入了解如何針對 WebJobs SDK 撰寫程式碼。 建立事件驅動的幕後處理作業，以存取 Azure 服務和協力廠商服務中的資料。
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
ms.date: 01/19/2019
ms.author: glenga
ms.openlocfilehash: ab502c25a632977065e55d2eeafd684203636b14
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109906"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>如何使用 Azure WebJobs SDK 進行事件驅動幕後處理

本文提供有關如何撰寫 [Azure WebJobs SDK](webjobs-sdk-get-started.md) 程式碼的指引。 此文件適用於 3.x 和 2.x 版的 WebJobs SDK。 針對 API 的不同之處，會提供兩個版本的範例。 3.x 版所帶來的主要變更，就是使用 .NET Core，而不是使用 .NET Framework。

>[!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) 建置在 WebJobs SDK 上，本文針對某些主題提供 Azure Functions 文件的連結。 請注意以下有關函式與 WebJobs SDK 之間的差異：
> * Azure Functions 2.x 版對應至 WebJobs SDK 3.x 版，Azure Functions 1.x 對應至 WebJobs SDK 2.x。 原始程式碼存放庫會採用 WebJobs SDK 編號。
> * Azure Functions C# 類別庫程式碼範例類似 WebJobs SDK 程式碼，但您不需要 WebJobs SDK 專案中的 `FunctionName` 屬性。
> * Functions 中僅支援部分繫結型別，例如 HTTP、webhook 及事件方格 (以 HTTP 為基礎)。
> 
> 如需詳細資訊，請參閱[比較 WebJobs SDK 和 Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs) (英文)。

## <a name="prerequisites"></a>必要條件

本文假設您已閱讀[開始使用 WebJobs SDK](webjobs-sdk-get-started.md) 並完成其中的工作。

## <a name="webjobs-host"></a>WebJobs 主機

此主機是函式的執行階段容器。  它會接聽觸發程序並呼叫函式。 在 3.x 版中，主機是 `IHost` 的實作，而 2.x 版則使用 `JobHost` 物件。 您可以在程式碼中建立主機執行個體，並撰寫程式碼以自訂其行為。

這是 WebJobs SDK 直接使用與透過使用 Azure Functions 間接使用之間的主要差異。 在 Azure Functions 中，服務會控制主機，且您無法撰寫程式碼加以自訂。 Azure Functions 可讓您透過設定 *host.json* 檔案自訂主機行為。 這些設定是字串，不是程式碼，會限制您可以進行自訂化的種類。

### <a name="host-connection-strings"></a>主機連接字串 

當您在本機執行時，或若您在 Azure 中執行時位於 WebJob 的環境中，WebJobs SDK 會在 *local.settings.json* 檔案中尋找 Azure 儲存體與 Azure 服務匯流排連接字串。 根據預設，必須要有名為 `AzureWebJobsStorage` 的儲存體連接字串設定。  

2.x 版的 SDK 可讓您自行為這些連接字串命名，或將其儲存於他處。 您可在程式碼中加以設定，如下所示：

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

由於它使用預設的 .NET Core 組態 API，因此 3.x 版中沒有 API 可用來變更連接字串名稱。

### <a name="host-development-settings"></a>主機開發設定

您可以在開發模式中執行主機，讓本機開發更有效率。 以下是在開發模式中執行時須變更的一些設定：

| 屬性 | 開發設定 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` 將記錄輸出最大化。 |
| `Queues.MaxPollingInterval`  | 最低值可確保立即觸發佇列方法。  |
| `Singleton.ListenerLockPeriod` | 15 秒在快速反覆式開發法中有幫助。 |

啟用開發模式的方式，取決於 SDK 版本。 

#### <a name="version-3x"></a>3.x 版

3.x 版使用標準 ASP.NET Core API。 請在 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 執行個體上呼叫 [UseEnvironment](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) 方法。 傳入名為 `development` 的字串，如下列範例所示：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
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

#### <a name="version-2x"></a>2.x 版

`JobHostConfiguration` 類別具有可啟用開發模式的 `UseDevelopmentSettings` 方法。  下列範例示範如何使用開發設定。 在本機執行時，若要讓 `config.IsDevelopment` 傳回 `true`，請將本機環境變數設定為名為 `AzureWebJobsEnv` 且值為 `Development`。

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>管理並行連線 (v2.x)

在 3.x 版中，連線限制預設為無限連線。 如果您因故需要變更此限制，可以使用 [WinHttpHander](/dotnet/api/system.net.http.winhttphandler) 類別的 [MaxConnectionsPerServer](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) 屬性。

在 2.x 版中，您可以使用 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) API 來控制主機的並行連線數目。 在 2.x 版中，您應在啟動 WebJobs 主機之前從預設值 2 增加此值。

所有您使用 `HttpClient` 從某個函式產生的外寄 HTTP 要求都會流經 `ServicePointManager`。 一旦您到達 `DefaultConnectionLimit``ServicePointManager` 會開始先將要求排入佇列，然後再傳送。 假設您的 `DefaultConnectionLimit` 設為 2，且您的程式碼會發出 1,000 個 HTTP 要求。 一開始，只允許兩個要求傳至作業系統。 其他的 998 個要求會排入佇列，直到有足夠的空間給它們。 這表示您的 `HttpClient` 可能會逾時，因為它*認為*它已發出要求，但作業系統一直未將該要求傳送到目的地伺服器。 因此，您可能會看到一個似乎不合理的行為：您的本機 `HttpClient` 花費 10 秒完成要求，但您的服務正以 200 毫秒的時間傳回每個要求。 

ASP.NET 應用程式的預設值是 `Int32.MaxValue`，而且可能適用於在基本或更高等級 App Service 方案中執行的 WebJobs。 WebJobs 通常需要 Always On 設定，且只有基本與更高等級的 App Service 方案才支援。 

如果 WebJob 正在免費或共用的 App Service 方案中執行，則您的應用程式會受到 App Service 沙箱的限制，沙箱目前的[連線限制為 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)。 在 `ServicePointManager` 的未繫結連線限制下，更有可能到達沙箱連線閾值，且網站會關閉。 在此狀況下，將 `DefaultConnectionLimit` 設為更低的值，如 50 或 100，可防止這種狀況發生，同時仍允許有足夠的輸送量。

此設定必須先設定，才能發出任何 HTTP 要求。 基於這個理由，WebJobs 主機不應自動嘗試調整設定；可能會有 HTTP 要求在主機啟動之前發生，這會導致非預期的行為。 最佳方法就是先在 `Main` 方法中立即設定值，然後初始化 `JobHost`，如下例所示

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>觸發程序

Functions 必須是公用方法，且必須有一個觸發屬性與 [NoAutomaticTrigger](#manual-trigger) 屬性。

### <a name="automatic-trigger"></a>自動觸發程序

自動觸發程序會呼叫函式以回應事件。 如需範例，請參閱[開始使用文章](webjobs-sdk-get-started.md) (英文) 中的佇列觸發程序。

### <a name="manual-trigger"></a>手動觸發

若要手動觸發函式，請使用 `NoAutomaticTrigger` 屬性，如下列所示：

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

手動觸發函式的方式取決於 SDK 版本。

#### <a name="version-3x"></a>3.x 版

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>2.x 版

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>輸入與輸出繫結

輸入繫結會提供宣告式方法，讓 Azure 或協力廠商服務中的資料可供您的程式碼使用。 輸出繫結會提供更新資料的方法。 [開始使用文章](webjobs-sdk-get-started.md) (英文) 中提供每個繫結的範例。

您可以將方法傳回值用於輸出繫結，方法是將屬性套用至方法傳回值。 請參閱 Azure Functions [觸發程序和繫結](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) (英文) 文章中的範例。

## <a name="binding-types"></a>繫結型別

在 3.x 版和 2.x 版的 SDK 中，繫結類型的安裝和管理方式並不相同。 您可以在 Azure Functions 其該繫結型別[參考文章](#binding-reference-information) (英文) 的「封裝」一節中，找到可為特定繫結型別安裝的封裝。 有一個例外狀況，就是檔案觸發程序與繫結 (針對本機檔案系統)，其不受 Azure Functions 支援。

#### <a name="version-3x"></a>3.x 版

在 3.x 版中，儲存體繫結包含於 `Microsoft.Azure.WebJobs.Extensions.Storage` 套件中。 請在 `ConfigureWebJobs` 方法中呼叫 `AddAzureStorage` 擴充方法，如下列範例所示：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

若要使用其他觸發程序與繫結類型，請安裝包含這些項目的 NuGet 套件，並呼叫在擴充功能中實作的 `Add<binding>` 擴充方法。 例如，如果您要使用 Azure Cosmos DB 繫結，請安裝 `Microsoft.Azure.WebJobs.Extensions.CosmosDB`並呼叫 `AddCosmosDB`，如下列範例所示：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

若要使用核心服務中包含的計時器觸發程序或檔案繫結，請分別呼叫 `AddTimers` 或 `AddFiles` 擴充方法。

#### <a name="version-2x"></a>2.x 版

下列觸發程序與繫結類型包含於 2.x 版的 `Microsoft.Azure.WebJobs` 套件中：

* Blob 儲存體
* 佇列儲存體
* 表格儲存體

若要使用其他觸發程序與繫結型別，請安裝包含它們的 NuGet 封裝，並在 `JobHostConfiguration` 物件上呼叫 `Use<binding>` 方法。 例如，如果您想要使用計時器觸發程序，請安裝 `Microsoft.Azure.WebJobs.Extensions`並呼叫 `Main` 方法的 `UseTimers`，如此範例所示：

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

若要使用檔案繫結，請安裝 `Microsoft.Azure.WebJobs.Extensions` 並呼叫 `UseFiles`。

### <a name="executioncontext"></a>ExecutionContext

WebJobs 可讓您繫結至 [`ExecutionContext`]。 透過此繫結，您將可存取函式簽章中的參數 [`ExecutionContext`]。 例如，下列程式碼會使用內容物件存取引動過程識別碼，而您可以使用此識別碼讓給定的函式引動過程所產生的所有記錄相互關聯。  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

繫結至 [`ExecutionContext`] 的方式取決於您的 SDK 版本。

#### <a name="version-3x"></a>3.x 版

請在 `ConfigureWebJobs` 方法中呼叫 `AddExecutionContextBinding` 擴充方法，如下列範例所示：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>2.x 版

先前所提到的 `Microsoft.Azure.WebJobs.Extensions` 封裝方法也提供特殊的繫結型別，您可以呼叫 `UseCore` 方法來註冊該繫結型別。 此繫結可讓您定義函式簽章中的 [`ExecutionContext`] 參數，其啟用方式如下：

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>繫結設定

某些觸發程序和繫結可讓您設定其行為。 其設定方式取決於 SDK 版本。

* **3.x 版：** 在 `ConfigureWebJobs` 中呼叫 `Add<Binding>` 方法時，就會進行設定。
* **2.x 版：** 藉由在您傳至 `JobHost` 的設定物件中設定屬性。

### <a name="queue-trigger-configuration"></a>佇列觸發程序設定

Azure Functions [host.json 參照](../azure-functions/functions-host-json.md#queues) (英文) 中會說明您可以為儲存體佇列觸發程序設定的設定。 下列範例說明如何在您的組態中加以設定：

#### <a name="version-3x"></a>3.x 版

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>2.x 版

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configuration-for-other-bindings"></a>其他繫結的設定

某些觸發程序與繫結型別會定義自己的自訂設定型別。 例如，檔案觸發程序可讓您指定要監視的根路徑，如下列範例所示：

#### <a name="version-3x"></a>3.x 版

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>2.x 版

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>繫結運算式

在屬性建構函式參數中，您可以使用運算式來解析各種來源的值。 例如，在下列程式碼中，`BlobTrigger` 屬性的路徑會建立名為 `filename`。 當用於輸出繫結時，`filename` 會解析觸發中二進位大型物件的名稱。

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

如需繫結運算式的詳細資訊，請參閱 Azure Functions 文件中的[繫結運算式和模式](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) (英文)。

### <a name="custom-binding-expressions"></a>自訂繫結運算式

有時候您不想要採取硬式編碼的方式，而是在程式碼中指定佇列名稱、Blob 名稱或容器或資料表名稱。 例如，您可能想要在設定檔或環境變數中指定 `QueueTrigger` 屬性的佇列名稱。

方法是將 `NameResolver` 物件傳入 `JobHostConfiguration` 物件。 在觸發程序或繫結屬性建構函式中包含預留位置，然後 `NameResolver` 程式碼會提供實際要用以取代這些預留位置的值。 預留位置的識別方式是在其前後加上百分比 (%) 符號，如下面範例所示：

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

此程式碼可讓您在測試環境中使用名為 `logqueuetest` 的佇列，以及在生產環境中使用名為 `logqueueprod` 的佇列。 您不必使用硬式編碼的佇列名稱，而是在 `appSettings` 集合中指定項目的名稱。

如果您沒有提供自訂名稱，則預設的 NameResolver 會生效。 此預設值會從應用程式設定或環境變數取得值。

`NameResolver` 類別會從 `appSettings` 取得佇列名稱，如下面範例所示：

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>3.x 版

使用相依性插入來設定解析程式。 這些範例需要下列 `using` 陳述式：

```cs
using Microsoft.Extensions.DependencyInjection;
```

藉由在 [HostBuilder](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 上呼叫 [`ConfigureServices`] 擴充方法來新增解析程式，如下列範例所示：

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2.x 版

將 `NameResolver` 類別傳入 `JobHost` 物件，如下面範例所示：

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions 會實作 `INameResolver`，以從應用程式設定取得值，如下面範例所示。 當您直接使用 WebJobs SDK 時，您可以撰寫自訂實作，以從任何您偏好的來源取得預留位置取代值。 

## <a name="binding-at-runtime"></a>執行階段的繫結

如果您需要先在函式中執行部分工作，然後再使用繫結屬性，例如 `Queue`、`Blob` 或 `Table`，您可以使用 `IBinder` 介面。

下列範例會使用輸入佇列訊息，並在輸出佇列中建立含有相同內容的新訊息。 輸出佇列名稱會由函數主體中的程式碼設定。

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

如需詳細資訊，請參閱 Azure Functions 文件中的[執行階段的繫結](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) (英文)。

## <a name="binding-reference-information"></a>繫結參考資訊

Azure Functions 文件中會提供每個繫結型別的相關參考資訊。 以儲存體佇列為例，您將會在每個繫結參考文章中找到下列資訊：

* [封裝](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) - 要安裝哪一個封裝才能將繫結支援包含在 WebJobs SDK 專案中。
* [範例](../azure-functions/functions-bindings-storage-queue.md#trigger---example) - C# 類別庫範例適用於 WebJobs SDK；只要省略 `FunctionName` 屬性。
* [屬性](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) - 用於繫結型別的屬性。
* [設定](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) - 屬性 (attribute) 屬性 (property) 與建構函式參數的說明。
* [使用方式](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) - 您可以繫結至哪些型別，以及繫結運作方式的相關資訊。 例如：輪詢演算法、有害佇列處理。
  
如需繫結參考文章清單，請參閱 Azure Functions 其[觸發和繫結](../azure-functions/functions-triggers-bindings.md#supported-bindings)文章中的**支援的繫結**。 在該清單中，HTTP、webhook 和事件方格繫結是由 Azure Functions 支援，而非由 WebJobs SDK 支援。

## <a name="disable-attribute"></a>Disable 屬性 

[Disable](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 屬性可讓您控制哪些函式可觸發。 

在下列範例中，如果應用程式設定 `Disable_TestJob` 的值為 "1" 或 "True" (不區分大小寫)，該函式將不會執行。 在此情況下，執行階段會建立記錄訊息「*函式 'Functions.TestJob' 已停用*」。

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

當您在 Azure 入口網站中變更應用程式設定值時，會造成 WebJob 重新啟動，並挑選新的設定。

此屬性可在參數、方法或類別等級宣告。 設定名稱也可以包含繫結運算式。

## <a name="timeout-attribute"></a>Timeout 屬性

[Timeout](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) 屬性會在函式未於指定時間內完成時，使函式遭到取消。 在下列範例中，函式會執行一整天，而不會逾時。 有了逾時，該函式會在 15 秒後遭到取消。

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

您可以在類別或方法等級套用 Timeout 屬性，並可使用 `JobHostConfiguration.FunctionTimeout` 指定全域逾時。 類別或方法等級的逾時會覆寫全域逾時。

## <a name="singleton-attribute"></a>Singleton 屬性

[Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) 屬性用於確保即使主機 Web 應用程式有多個執行個體時，函式仍只有一個執行個體。 做法是實作[分散式鎖定](#viewing-lease-blobs)。

在下列範例中，`ProcessImage` 函式會在任何指定的時間只會有一個執行個體：

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

某些觸發程序內建並行管理的支援：

* **QueueTrigger** - 會將 `JobHostConfiguration.Queues.BatchSize` 設為 1。
* **ServiceBusTrigger** - 會將 `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` 設為 1。
* **FileTrigger** - 會將 `FileProcessor.MaxDegreeOfParallelism` 設為 1。

您可以使用這些設定確保函式在單一執行個體上以單一項目執行。 若要確保當 Web 應用程式擴充為多個執行個體時，函式只會有單一執行個體執行，請在該函式上套用接聽程式等級的 Singleton 鎖定 (`[Singleton(Mode = SingletonMode.Listener)]`)。 JobHost 啟動時需要接聽程式鎖定。 如果三個擴充的執行個體全部同時啟動，則其中只有一個執行個體會取得鎖定，且只有一個接聽程式會啟動。

### <a name="scope-values"></a>範圍值

您可以在 Singleton 上指定**範圍運算式/值**，這可確保將會序列化該範圍內之函式的所有執行。 以這種方式實作更細微的鎖定可允許您的函式進行某種層級的平行處理原則，同時根據您的需求序列化其他引動過程。 例如，在下列範例中，範圍運算式會繫結至傳入訊息的 `Region` 值。 如果佇列分別在區域 "East"、"East" 和 "West" 中包含三則訊息，則區域為 "East" 的訊息將循序執行，而區域為 "West" 的訊息會與 "West" 中的其他訊息平行執行。

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

鎖定的預設範圍是 `SingletonScope.Function`，這表示鎖定範圍 (二進位大型物件租用路徑) 繫結至完整的函式名稱。 若要跨函式鎖定，請指定 `SingletonScope.Host`，而且使用的範圍識別碼名稱在所有您不想要同時執行的函式中皆相同。 在下列範例中，一次只有 `AddItem` 或 `RemoveItem` 的一個執行個體會執行：

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>檢視租用二進位大型物件

WebJobs SDK 使用 [Azure 二進位大型物件租用](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)以實作分散式鎖定。 Singleton 所使用的租用二進位大型物件位在 "locks" 路徑下方，`AzureWebJobsStorage` 儲存體帳戶中的 `azure-webjobs-host` 容器中。 例如，稍早所示第一個 `ProcessImage` 範例的租用二進位大型物件路徑可以是 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`。 所有路徑皆包含 JobHost 識別碼，在此個案中為 061851c758f04938a4426aa9ab3869c0。

## <a name="async-functions"></a>Async 函數

如需如何將非同步函式編碼的相關資訊，請參閱[非同步函式](../azure-functions/functions-dotnet-class-library.md#async) (英文) 上的 Azure Functions 文件。

## <a name="cancellation-tokens"></a>取消權杖

如需如何處理取消權杖的相關資訊，請參閱[取消權杖與順利關機](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens) (英文) 上的 Azure Functions 文件。

## <a name="multiple-instances"></a>多個執行個體

如果您的 Web 應用程式在多個執行個體上執行，則會有一個連續的 WebJob 在每個執行個體上執行，以接聽觸發程序和呼叫函式。 各種觸發程序繫結的設計是為了有效地共用在執行個體之間合作的工作，如此便能擴充至更多執行個體，讓您能處理更多負載。

佇列與二進位大型物件觸發程序會自動防止函式處理佇列訊息或二進位大型物件多次；函式不一定要為等冪。

計時器觸發程序會自動確保計時器只有一個執行個體會執行，因此在指定的排程時間不會有多個函式正在執行中。

如果您想要確保在即使有多個主 Web 應用程式執行個體的情況下，仍然只有一個函式執行個體會執行，則可使用 [Singleton](#singleton-attribute) 屬性。

## <a name="filters"></a>篩選器

函式篩選條件 (預覽) 讓您能夠使用自己的邏輯自訂 WebJobs 執行管線。 篩選條件與 [ASP.NET 核心篩選條件](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters) 類似。 它們可以實作為套用到函式或類別上的宣告屬性。 如需詳細資訊，請參閱[函式篩選條件](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters) (英文)。

## <a name="logging-and-monitoring"></a>記錄和監視

建議使用針對 ASP.NET 開發的記錄架構，[開始使用](webjobs-sdk-get-started.md) (英文) 文章會說明使用方式。 

### <a name="log-filtering"></a>記錄篩選

`ILogger` 執行個體建立的每個記錄皆有相關聯的 `Category` 和 `Level`。 [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) 為一個列舉，而整數代碼表示相對的重要性：

|LogLevel    |代碼|
|------------|---|
|追蹤       | 0 |
|偵錯       | 1 |
|資訊 | 2 |
|警告     | 3 |
|Error       | 4 |
|重要    | 5 |
|None        | 6 |

每個分類皆可獨立篩選至特定的 [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel)。 例如，您可能想要看見二進位大型物件觸發程序處理的所有記錄，但只看見所有其他項目的 `Error` 以上等級記錄。

#### <a name="version-3x"></a>3.x 版

3.x 版的 SDK 相依於內建在 .NET Core 中的篩選條件。 `LogCategories` 類別可讓您為特定的函式、觸發程序或使用者定義類別。 它也可為特定的主機狀態定義篩選條件，例如 `Startup` 和 `Results`。 如此，您即可微調記錄輸出。 如果在已定義的類別內找不到相符項目，則在決定是否篩選訊息時，篩選條件會回復為 `Default` 值。

`LogCategories` 需要下列 using 陳述式：

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

下列範例建構一個預設會篩選 `Warning` 等級所有記錄的篩選條件。 `Function` 或 `results` 的類別 (等同於 2.x 版中的 `Host.Results`) 會在 `Error` 層級上篩選。 篩選條件會將目前的類別與 `LogCategories` 執行個體中所有已註冊的層級比較，並選擇最長的相符項目。 這表示為 `Host.Triggers` 註冊的 `Debug` 等級將符合 `Host.Triggers.Queue` 或 `Host.Triggers.Blob`。 這可讓您控制更廣泛的分類，但無需一一新增。

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2.x 版

在 2.x 版的 SDK 中，會使用 `LogCategoryFilter` 類別來控制篩選。 `LogCategoryFilter` 的 `Default` 屬性其初始值為 `Information`這表示系統會記錄任何等級為 `Information``Warning``Error` 或 `Critical` 的訊息，但會將任何等級為 `Debug` 或 `Trace` 的訊息篩選掉。

如同 3.x 版中的 `LogCategories`，`CategoryLevels` 屬性可讓您指定特定類別的記錄層級，以便微調記錄輸出。 如果在 `CategoryLevels` 目錄內找不到相符項目，則當決定是否篩選訊息時，篩選條件會回復到 `Default` 值。

下列範例建構一個預設會篩選 `Warning` 等級所有記錄的篩選條件。 `Function` 或 `Host.Results` 的分類會在 `Error` 等級篩選。 The `LogCategoryFilter` 會將目前分類與所有已註冊的 `CategoryLevels` 比較，並選擇最長的相符項目。 這表示為 `Host.Triggers` 註冊的 `Debug` 等級將符合 `Host.Triggers.Queue` 或 `Host.Triggers.Blob`。 這可讓您控制更廣泛的分類，但無需一一新增。

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Application Insights 的自訂遙測

為 [Application Insights](../azure-monitor/app/app-insights-overview.md) 實作自訂遙測的方式，取決於您所使用的 SDK 版本。 若要了解如何設定 Application Insights，請參閱[新增 Application Insights 記錄](webjobs-sdk-get-started.md#add-application-insights-logging)。

#### <a name="version-3x"></a>3.x 版

由於 3.x 版的 WebJobs SDK 相依於 .NET Core 泛型主機，因此不會再提供自訂遙測資料處理站。 不過，您可以使用相依性插入將自訂遙測新增至管線。 本節中的範例需要下列 `using` 陳述式：

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

[`ITelemetryInitializer`] 的下列自訂實作可讓您將自己的 [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) 新增至預設 [`TelemetryConfiguration`]。

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

在建立器中呼叫 [`ConfigureServices`]，以將您的自訂 [`ITelemetryInitializer`] 新增至管線。

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add Logging Providers
        b.AddConsole();

        // If this key exists in any config, use it to enable App Insights
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

[`TelemetryConfiguration`] 建構後，將會納入所有已註冊的 [`ITelemetryInitializer`] 類型。 若要深入了解其運作方式，請參閱[自訂事件和計量的 Application Insights API](../azure-monitor/app/api-custom-events-metrics.md)。

在 3.x 版中，您不再需要於當主機停止時排清 [`TelemetryClient`]。 .NET Core 相依性插入系統會自動處置已註冊的 `ApplicationInsightsLoggerProvider`，而排清 [`TelemetryClient`]。

#### <a name="version-2x"></a>2.x 版

在 2.x 版中，WebJobs SDK 的 Application Insights 提供者內部建立的 [`TelemetryClient`] 會使用 [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)。 當 Application Insights 端點無法使用或正在節流連入要求時，此通道[會將要求儲存在 Web 應用程式的檔案系統中，並於稍後重新提交](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)。

[`TelemetryClient`] 由實作 `ITelemetryClientFactory` 的類別所建立。 根據預設，這會是 [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)。

如果您想要修改 Application Insights 管線的任何一部分，您可以提供自己的 `ITelemetryClientFactory`，主機將使用您的類別來建構 [`TelemetryClient`]。 例如，此程式碼會覆寫 `DefaultTelemetryClientFactory` 以修改 `ServerTelemetryChannel` 的屬性：

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

SamplingPercentageEstimatorSettings 物件會設定[調適型取樣](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server)。 這表示在某些高容量的情況下，App Insights 會傳送所選取的遙測資料子集至伺服器。

一旦建立遙測中心後，您會將它傳遞給 Application Insights 記錄提供者：

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> 後續步驟

本指南提供了程式碼片段以示範如何處理使用 WebJobs SDK 的常見案例。 如需完整範例，請參閱 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples) (英文)。

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
