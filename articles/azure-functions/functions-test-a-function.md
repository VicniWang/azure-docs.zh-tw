---
title: 測試 Azure Functions
description: 針對 Visual Studio 中的 C# 函式和 VS Code 中的 JavaScript 函式建立自動化測試
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, 函數, 事件處理, webhook, 動態計算, 無伺服器架構, 測試
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: cshoe
ms.openlocfilehash: 19a5dee53bee20438098d1aaeb773ebf08f252d4
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993445"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>在 Azure Functions 中測試程式碼的策略

本文將示範如何為 Azure Functions 建立自動化測試。 

建議測試所有的程式碼，不過藉由包裝函式的邏輯並在函式外建立測試，您或許可取得最佳結果。 將邏輯抽象化可脫離函式的程式行限制，並允許函式單獨負責呼叫其他類別或模組。 不過，在本文中將示範如何針對 HTTP 和以計時器觸發的函式建立自動化測試。

接下來的內容分為兩個不同小節，以不同的語言和環境為目標。 您可了解如何在下列案例中建置測試：

- [Visual Studio 中的 C# 搭配 xUnit](#c-in-visual-studio)
- [VS Code 中的 JavaScript 搭配 Jest](#javascript-in-vs-code)

範例存放庫可於 [GitHub](https://github.com/Azure-Samples/azure-functions-tests) 取得。

## <a name="c-in-visual-studio"></a>Visual Studio 中的 C#
下列範例說明如何在 Visual Studio 中建立 C# 函數應用程式並執行，和使用 [xUnit](https://xunit.github.io) 測試。

![使用 Visual Studio 中的 C# 測試 Azure Functions](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>設定

若要設定您的環境，請建立函式並測試應用程式。 下列步驟協助您建立支援測試所需的應用程式和函式：

1. [建立新的 Functions 應用程式](./functions-create-first-azure-function.md)，並將它命名為 *Functions*
2. [從範本建立 HTTP 函式](./functions-create-first-azure-function.md)，並將它命名為 *HttpTrigger*。
3. [從範本建立計時器函式](./functions-create-scheduled-function.md)，並將它命名為 *TimerTrigger*。
4. 在 Visual Studio 中按一下 [檔案] > [新增] > [專案] > [Visual C#] > [.NET Core] > [xUnit 測試專案] 來[建立 xUnit 測試應用程式](https://xunit.github.io/docs/getting-started-dotnet-core)，並將它命名為 *Functions.Test*。 
5. 使用 Nuget，從測試應用程式將參考新增至 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/) 和 [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. 從 *Functions.Test* 應用程式[參考 *Functions* 應用程式](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017)。

### <a name="create-test-classes"></a>建立測試類別

現在，既然已建立應用程式，您可以建立用來執行自動化測試的類別。

每個函式都可接受 [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) 的執行個體以處理訊息記錄。 有些測試不會記錄訊息，或是沒有考量如何實作記錄。 其他測試需要評估已記錄的訊息，來判斷是否通過測試。

`ListLogger` 是用來實作 `ILogger` 介面且會保留內部訊息清單，以供測試期間評估使用。

**以滑鼠右鍵按一下** *Functions.Test* 應用程式，並選取 [新增] > [類別]，將它命名為 **ListLogger.cs**，然後輸入下列程式碼：

```csharp
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions.Internal;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel, 
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

`ListLogger` 類別實作下列成員，如 `ILogger` 介面所縮減：

- **BeginScope**：設定新增內容至記錄的範圍。 在此案例中，測試只會指向 [NullScope](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.abstractions.internal.nullscope) 類別上的靜態執行個體，以讓測試運作。

- **IsEnabled**：提供的預設值為 `false`。

- **Log**：此方法使用提供的 `formatter` 函式來將訊息格式化，接著將產生的文字新增到 `Logs` 集合。

`Logs` 集合是 `List<string>` 的執行個體，且在建構函式中初始化。

接下來，**以滑鼠右鍵按一下** *Functions.Test* 應用程式，並選取 [新增] > [類別]，將它命名為 **ListTypes.cs**，然後輸入下列程式碼：

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```
此列舉指定測試使用的記錄器類型。 

接下來，**以滑鼠右鍵按一下** *Functions.Test* 應用程式，並選取 [新增] > [類別]，將它命名為 **TestFactory.cs**，然後輸入下列程式碼：

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```
`TestFactory` 類別實作下列成員：

- **Data**：此屬性會傳回範例資料的 [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) 集合。 索引鍵值組代表傳入查詢字串的值。

- **CreateDictionary**：此方法接受索引鍵/值組作為引數，且會傳回新的 `Dictionary`，用來建立 `QueryCollection` 以代表查詢字串值。

- **CreateHttpRequest**：這個方法會建立使用指定之查詢字串參數初始化的 HTTP 要求。

- **CreateLogger**：根據記錄器類型，此方法會傳回用於測是的記錄器類別。 `ListLogger` 會保留已記錄訊息的追蹤以提供給測試的評估使用。

接下來，**以滑鼠右鍵按一下** *Functions.Test* 應用程式，並選取 [新增] > [類別]，將它命名為 **FunctionsTests.cs**，然後輸入下列程式碼：

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
成員在此案例中的實作為：

- **Http_trigger_should_return_known_string**：這項測試會建立對 HTTP 函式的要求 (具有 `name=Bill` 的查詢字串值)，並檢查是否傳回預期的回應。

- **Http_trigger_should_return_string_from_member_data**：此測試使用 xUnit 屬性以提供範例資料給 HTTP 函式。

- **Timer_should_log_message**：此測試會建立 `ListLogger` 的執行個體，並將它傳遞給計時器函式。 一旦函式執行之後，便會檢查記錄以確保是否存在預期的訊息。

### <a name="run-tests"></a>執行測試

若要執行測試，請瀏覽至 [測試總管]，然後按一下 [全部執行]。

![使用 Visual Studio 中的 C# 測試 Azure Functions](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>偵錯測試

若要針對測試偵錯，請在測試上設定中斷點，瀏覽至 [測試總管] 然後按一下 [執行] > [對上一個回合偵錯]。

## <a name="javascript-in-vs-code"></a>VS Code 中的 JavaScript

下列範例說明如何在 VS Code 中建立 JavaScript 函數應用程式並執行，和使用 [Jest](https://jestjs.io) 測試。 此程序使用 [VS Code Functions 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)來建立 Azure Functions。

![使用 VS Code 中的 JavaScript 測試 Azure Functions](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>設定

若要設定您的環境，請執行 `npm init`，在空資料夾中初始化新的 Node.js 應用程式。

```bash
npm init -y
```
接著，執行下列命令來安裝 Jest：

```bash
npm i jest
```
現在，更新 _package.json_，使用下列命令取代現有測試命令：

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>建立測試模組
透過已初始化的專案，您可以建立用來執行自動化測試的模組。 一開始請建立名為 *testing* 的新資料夾，來保存支援模組。

在 *testing* 資料夾中新增名為 **defaultContext.js** 的檔案，然後加入下列程式碼：

```javascript
module.exports = {
    log: jest.fn()
};
```
此模組會模擬 *log* 函式，來代表預設的執行內容。

接下來，新增名為 **defaultTimer.js** 的檔案，並加入下列程式碼：

```javascript
module.exports = {
    isPastDue: false
};
```
此模組實作 `isPastDue` 屬性，以作為假計時器執行個體。

接下來，使用 VS Code Functions 延伸模組[建立新的 JavaScript HTTP 函式](https://code.visualstudio.com/tutorials/functions-extension/getting-started)，並將它命名為 *HttpTrigger*。 一旦函式建立之後，在相同資料夾中新增名為 **index.test.js** 的檔案，並加入下列程式碼：

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```
範本的 HTTP 函式會傳回 "Hello" 以及與查詢字串中提供之名稱串連的字串。 這項測試會建立假的要求執行個體，並將它傳給 HTTP 函式。 測試會檢查 *log* 方法是否有呼叫過一次，並傳回等於 "Hello Bill" 的文字。

接下來，使用 VS Code Functions 延伸模組建立新的 JavaScript 計時器函式，並將它命名為 *TimerTrigger*。 一旦函式建立之後，在相同資料夾中新增名為 **index.test.js** 的檔案，並加入下列程式碼：

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
範本的計時器函式會在函式主體結尾記錄訊息。 此測試可確保 *log* 函數會呼叫一次。

### <a name="run-tests"></a>執行測試
若要執行測試，請按 **CTRL + ~** 以開啟命令視窗，並執行 `npm test`：

```bash
npm test
```

![使用 VS Code 中的 JavaScript 測試 Azure Functions](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>偵錯測試

若要針對您的測試偵錯，請將下列設定加入至您的 *launch.json* 檔案：

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

接下來，在測試中設定中斷點，然後按 **F5**。

## <a name="next-steps"></a>後續步驟

現在既然您已經了解如何為您的函式撰寫自動化測試，請繼續使用這些資源：
- [手動執行非 HTTP 觸發的函式](./functions-manually-run-non-http.md)
- [Azure Functions 錯誤處理](./functions-bindings-error-pages.md)
- [Azure Functions 事件方格觸發程序本機偵錯](./functions-debug-event-grid-trigger-local.md)
