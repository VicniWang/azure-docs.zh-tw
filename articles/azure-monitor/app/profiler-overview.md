---
title: 使用 Application Insights Profiler 來分析 Azure 中的生產應用程式 | Microsoft Docs
description: 使用低資源使用量的分析工具來找出 Web 伺服器程式碼中的最忙碌路徑。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 2a2c2667ae3180fd4f7b114ce6cef05ac7a1080c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859720"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>使用 Application Insights 來分析 Azure 中的生產應用程式
## <a name="enable-application-insights-profiler-for-your-application"></a>為您的應用程式啟用 Application Insights Profiler

Azure Application Insights Profiler 可為在 Azure 生產環境中執行的應用程式提供效能追蹤。 Profiler 可自動大規模擷取資料，且不會對您的使用者產生負面影響。 Profiler 可協助您識別在處理特定 Web 要求時花費時間最長且「經常存取」的程式碼路徑。 

Profiler 可與部署於下列 Azure 服務的 .NET 應用程式搭配運作。 以下連結提供為每個服務類型啟用 Profiler 的特定指示。

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲端服務](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器和虛擬機器擴展集](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

如果您已啟用 Profiler 卻未看到追蹤，請參閱[疑難排解指南](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。

## <a name="view-profiler-data"></a>檢視 Profiler 資料

若要讓 Profiler 上傳追蹤，應用程式必須主動處理要求。 如果您要進行實驗，可以使用 [Application Insights 效能測試](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)產生對 Web 應用程式的要求。 如果您剛剛啟用 Profiler，則可以執行簡短的負載測試。 在負載測試執行時，選取 [[Profiler 設定] 窗格](profiler-settings.md#profiler-settings-pane)上的 [立即分析] 按鈕。 Profiler 開始執行後，大約每隔一小時就會隨機分析一次，時間會持續兩分鐘。 如果應用程式所處理的是穩定不斷的要求，則 Profiler 會每小時上傳追蹤。

在應用程式收到某些流量，且 Profiler 有時間上傳追蹤後，您應該就會有可供檢視的追蹤。 此程序可能需要 5 到 10 分鐘的時間。 若要檢視追蹤，請在 [效能] 窗格中選取 [採取動作]，然後選取 [Profiler 追蹤] 按鈕。

![Application Insights [效能] 窗格預覽分析工具追蹤][performance-blade]

選取某個範例就會顯示要求在執行時所用時間的程式碼層級細目。

![Application Insights 追蹤總管][trace-explorer]

追蹤總管會顯示下列資訊：

* **顯示最忙碌路徑**：開啟最大的分葉節點，或至少是類似的節點。 在大部分情況下，這個節點會接近效能瓶頸。
* **標籤**︰函式或事件的名稱。 樹狀結構會混合顯示程式碼和發生的事件，例如 SQL 和 HTTP 事件。 最上層事件代表要求整體的持續時間。
* **經過時間**︰作業開始和作業結束之間的時間間隔。
* **何時**︰顯示函式或事件相對於其他函式的執行時間。

## <a name="how-to-read-performance-data"></a>如何讀取效能資料

Microsoft 服務分析工具會合併使用取樣方法和檢測功能，來分析應用程式的效能。 當系統在進行詳細的收集作業時，服務分析工具會對電腦的每個 CPU 指令指標每毫秒進行取樣一次。 每次取樣都會擷取目前執行中執行緒的完整呼叫堆疊。 它會提供與該執行緒執行的工作有關的詳細資訊，概括與詳細的抽象概念均提供。 服務分析工具也會收集其他事件來追蹤活動的相互關聯和因果關係，包括內容切換事件、「工作平行程式庫」(TPL) 事件及執行緒集區事件。

時間表檢視中顯示的呼叫堆疊是取樣和檢測的結果。 由於每次取樣時都會擷取執行緒的完整呼叫堆疊，因此其中會包含來自 Microsoft .NET Framework 和您所參考之其他架構的程式碼。

### <a id="jitnewobj"></a>物件配置 (clr!JIT\_New 或 clr!JIT\_Newarr1)

**clr!JIT\_New** 和 **clr!JIT\_Newarr1** 是 .NET Framework 內的 Helper 函式，可從受控堆積配置記憶體。 配置物件時，會叫用 **clr!JIT\_New**。 配置物件陣列時，會叫用 **clr!JIT\_Newarr1**。 這兩個函式的執行速度通常很快，花費的時間相當少。 如果 **clr!JIT\_New** 或 **clr!JIT\_Newarr1** 在您的時間表中花費相當長的時間，表示程式碼可能配置了許多物件，而耗用大量的記憶體。

### <a id="theprestub"></a>載入程式碼 (clr!ThePreStub)

**clr!ThePreStub** 是 .NET Framework 內的 Helper 函式，可讓程式碼做好首次執行的準備。 此執行通常包括 (但不限於) Just-In-Time (JIT) 編譯。 在程序執行期間，每個 C# 方法最多應該只會叫用 **clr!ThePreStub** 一次。

如果 **clr!ThePreStub** 為某個要求花費了大量時間，表示該要求是第一個執行該方法的要求。 .NET Framework 執行階段載入第一個方法的時間相當重要。 您可以考慮先使用熱身程序來執行該部分的程式碼，再讓使用者存取該程式碼，或考慮在您的組件上執行 Native Image Generator (ngen.exe)。

### <a id="lockcontention"></a>鎖定爭用 (clr!JITutil\_MonContention 或 clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** 或 **clr!JITutil\_MonEnterWorker** 表示目前的執行緒正在等候鎖定被釋放。 通常在執行 C# **LOCK** 陳述式、叫用 **Monitor.Enter** 方法，或叫用含有 **MethodImplOptions.Synchronized** 屬性的方法時，都會顯示此文字。 鎖定爭用常發生在執行緒 _A_ 已取得鎖定，但執行緒 _B_ 在執行緒 _A_ 釋放鎖定之前就嘗試取得同一鎖定的情況下。

### <a id="ngencold"></a>載入程式碼 ([COLD])

如果方法名稱包含 **[COLD]** (例如 **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**)，即表示 .NET Framework 執行階段是第一次執行程式碼，該程式碼尚未由[特性指引最佳化](https://msdn.microsoft.com/library/e7k32f4k.aspx)進行最佳化。 在程序執行期間，每個方法最多應該只會顯示此名稱一次。

如果某個要求在載入程式碼時花費相當長的時間，表示該要求是第一個執行方法中未最佳化部分的要求。 請考慮先使用熱身程序來執行該部分的程式碼，再讓使用者存取該程式碼。

### <a id="httpclientsend"></a>傳送 HTTP 要求

**HttpClient.Send** 之類的方法表示程式碼正在等候 HTTP 要求完成。

### <a id="sqlcommand"></a>資料庫作業

**SqlCommand.Execute** 之類的方法表示程式碼正在等候資料庫作業完成。

### <a id="await"></a>等候 (AWAIT\_TIME)

**AWAIT\_TIME** 表示程式碼正在等候另一個工作完成。 此延遲通常發生在 C# **AWAIT** 陳述式。 當程式碼執行 C# **AWAIT** 時，執行緒會將控制權回溯並交還給執行緒集區，而且不會有任何執行緒被封鎖以等候 **AWAIT** 完成。 不過，就邏輯而言，執行 **AWAIT** 的執行緒會被「封鎖」，並等候作業完成。 **AWAIT\_TIME** 陳述式表示為了等候工作完成而封鎖的時間。

### <a id="block"></a>封鎖時間

**BLOCKED_TIME** 表示程式碼正在等候另一個資源可供使用。 例如，它可能正在等候同步處理物件、等候執行緒可供使用，或等候要求完成。

### <a id="cpu"></a>CPU 時間

CPU 正忙於執行指令。

### <a id="disk"></a>磁碟時間

應用程式正在執行磁碟作業。

### <a id="network"></a>網路時間

應用程式正在執行網路作業。

### <a id="when"></a>何時資料行

[何時] 資料行會以視覺方式呈現針對節點收集的 INCLUSIVE 樣本如何隨著時間變化。 要求的總範圍會分成 32 個時間貯體。 該節點的內含範例會在這 32 個貯體中累積。 每個貯體都以一個長條表示。 長條的高度代表換算值。 節點如果標示為 **CPU_TIME** 或 **BLOCKED_TIME**，或是與耗用資源 (例如，CPU、磁碟或執行緒) 有明顯的關聯性，則長條代表其中一項資源在貯體期間內的耗用量。 如果耗用多個資源，這些計量的值便有可能大於 100%。 例如，如果您在一段間隔內平均使用兩個 CPU，值就會是 200%。

## <a name="limitations"></a>限制

預設資料保留期為 5 天。 每天的內嵌資料上限為 10 GB。

使用 Profiler 服務為免費。 若要加以使用，您的 Web 應用程式必須至少裝載在 Azure App Service 的基本層 Web Apps 功能。

## <a name="overhead-and-sampling-algorithm"></a>額外負荷和取樣演算法

Profiler 會在裝載已啟用 Profiler 之應用程式的每部虛擬機器上，每小時隨機執行 2 分鐘來擷取追蹤。 Profiler 在執行時會對伺服器增加 5% 到 15% 的 CPU 額外負荷。

## <a name="next-steps"></a>後續步驟
為您的 Azure 應用程式啟用 Application Insights Profiler。 另請參閱：
* [應用程式服務](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲端服務](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器和虛擬機器擴展集](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
