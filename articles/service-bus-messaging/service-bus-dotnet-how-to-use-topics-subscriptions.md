---
title: 開始使用 Azure 服務匯流排主題和訂用帳戶 | Microsoft Docs
description: 撰寫使用服務匯流排傳訊主題和訂用帳戶的 C# .NET Core 主控台應用程式。
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 28b15e950aa4ebbfcb1b04bfd495b3accf13528e
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586897"
---
# <a name="get-started-with-service-bus-topics"></a>開始使用服務匯流排主題

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本教學課程涵蓋下列步驟：

1. 使用 Azure 入口網站建立服務匯流排命名空間。
2. 使用 Azure 入口網站建立服務匯流排主題。
3. 使用 Azure 入口網站，針對該主題建立服務匯流排訂用帳戶。
4. 撰寫 .NET Core 主控台應用程式，以將一組訊息傳送到主題。
5. 撰寫 .NET Core 主控台應用程式，以從訂用帳戶接收這些訊息。

## <a name="prerequisites"></a>必要條件

1. [Visual Studio 2017 Update 3 (版本 15.3, 26730.01)](https://www.visualstudio.com/vs) 或更新版本。
2. [.NET Core SDK](https://www.microsoft.com/net/download/windows)，2.0 版或更新版本。
2. Azure 訂用帳戶。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-subscriptions-portal](../../includes/service-bus-create-topics-subscriptions-portal.md)]

## <a name="send-messages-to-the-topic"></a>將訊息傳送到主題

為了將訊息傳送至主題，請使用 Visual Studio 撰寫 C# 主控台應用程式。

### <a name="create-a-console-application"></a>建立主控台應用程式

啟動 Visual Studio 並建立新的**主控台應用程式 (.NET Core)** 專案。

### <a name="add-the-service-bus-nuget-package"></a>新增服務匯流排 NuGet 封裝

1. 以滑鼠右鍵按一下新建立的專案，然後選取 [管理 NuGet 套件]。
2. 按一下 [瀏覽] 索引標籤，搜尋 **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**，然後選取 [Microsoft.Azure.ServiceBus] 項目。 按一下 [安裝]  完成安裝作業，然後關閉此對話方塊。
   
    ![選取 NuGet 封裝][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>撰寫程式碼以將訊息傳送到主題

1. 在 Program.cs 中，在命名空間定義的頂端新增下列 `using` 陳述式 (在類別宣告的前面)：
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. 在 `Program` 類別中，宣告下列變數。 將 `ServiceBusConnectionString` 變數設定為建立命名空間時取得的連接字串，並將 `TopicName` 設定為建立主題時使用的名稱：
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. 以下列這一行程式碼取代 `Main()` 的預設內容：

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```
   
4. 直接在 `Main()` 後面，新增下列非同步 `MainAsync()` 方法，以呼叫傳送訊息方法：

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```

5. 直接在 `MainAsync()` 方法後面，新增下列 `SendMessagesAsync()` 方法，以執行傳送 `numberOfMessagesToSend` 所指定訊息數目 (目前設為 10) 的工作：

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. 傳送者 Program.cs 檔案看起來應該會像下面這樣。
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. 執行程式，並檢查 Azure 入口網站：按一下命名空間 [概觀] 視窗中的主題名稱。 主題的 [基本資訊] 畫面即會顯示。 請注意，在靠近視窗底部所列的訂用帳戶中，訂用帳戶的 [訊息計數] 值現在是 **10**。 每次執行傳送者應用程式而未擷取訊息 (如下一節所述)，這個值就會增加 10。 也請注意，每當應用程式將訊息新增到主題時，主題的目前大小就會讓 [基本資訊] 視窗上的 [目前] 值增加。
   
      ![訊息大小][topic-message]

## <a name="receive-messages-from-the-subscription"></a>自訂用帳戶接收訊息

若要接收您剛傳送的訊息，請建立另一個 .NET Core 主控台應用程式，並安裝 **Microsoft.Azure.ServiceBus** NuGet 套件 (類似於先前的傳送者應用程式)。

### <a name="write-code-to-receive-messages-from-the-subscription"></a>撰寫程式碼以從訂用帳戶接收訊息

1. 在 Program.cs 中，在命名空間定義的頂端新增下列 `using` 陳述式 (在類別宣告的前面)：
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. 在 `Program` 類別中，宣告下列變數。 將 `ServiceBusConnectionString` 變數設定為建立命名空間時取得的連接字串，將 `TopicName` 設定為建立主題時使用的名稱，以及將 `SubscriptionName` 設定為建立主題的訂用帳戶時使用的名稱：
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. 以下列這一行程式碼取代 `Main()` 的預設內容：

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. 直接在 `Main()` 後面，新增下列非同步 `MainAsync()` 方法，以呼叫 `RegisterOnMessageHandlerAndReceiveMessages()` 方法：

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
    }
    ```

5. 直接在 `MainAsync()` 方法後面，新增下列方法，以註冊訊息處理常式及接收傳送者應用程式所傳送的訊息：

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. 直接在前一個方法後面，新增下列 `ProcessMessagesAsync()` 方法，以處理收到的訊息：
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. 最後，新增下列方法以處理任何可能發生的例外狀況：
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. 接收者 Program.cs 檔案看起來應該會像下面這樣：
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. 執行程式，並再次檢查入口網站。 請注意，**訊息計數**和**目前**值現在是 **0**。
   
    ![主題長度][topic-message-receive]

恭喜！ 使用 .NET Standard 程式庫，您現在已建立主題和訂用帳戶，傳送 10 則訊息，以及接收這些訊息。

## <a name="next-steps"></a>後續步驟

查看服務匯流排 [GitHub 存放庫以及範例](https://github.com/Azure/azure-service-bus/tree/master/samples)，其中會展示一些更進階的服務匯流排傳訊功能。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
