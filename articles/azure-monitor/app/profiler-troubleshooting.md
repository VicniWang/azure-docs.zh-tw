---
title: 針對 Azure Application Insights Profiler 的問題進行疑難排解 | Microsoft Docs
description: 本文提供疑難排解步驟和資訊，可為無法啟用或使用 Application Insights Profiler 的開發人員提供協助。
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
ms.openlocfilehash: b6a7fe2c12b2f1f5bcc0ba8cccd1a51ee39c4a6f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882076"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>針對啟用或檢視 Application Insights Profiler 的問題進行疑難排解

## <a id="troubleshooting"></a>一般疑難排解

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>如果在 Profiler 執行時有對應用程式提出的要求，才會上傳設定檔

Azure Application Insights Profiler 每小時會收集一次分析資料，每次兩分鐘。 此外，當您選取 [設定 Application Insights Profiler] 窗格中的 [立即分析] 按鈕時，也會收集資料。 不過，只有在分析資料可附加至 Profiler 執行期間所提出的要求時，才會上傳該資料。 

Profiler 會將追蹤訊息和自訂事件寫入至您的 Application Insights 資源。 您可以使用這些事件來查看 Profiler 的執行情形。 如果您認為 Profiler 應該正在執行並擷取追蹤，但 [效能] 窗格中並未顯示這些追蹤，您可以查看 Profiler 的執行情形：

1. 搜尋 Profiler 傳送給 Application Insights 資源的追蹤訊息和自訂事件。 您可以使用此搜尋字串來尋找相關資料：

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    下圖顯示從兩個 AI 資源進行搜尋的兩個範例： 
    
    * 在左側，應用程式在 Profiler 執行時並未接收要求。 訊息指出上傳已因沒有活動而取消。 

    * 在右側，Profiler 已啟動，並在偵測到於 Profiler 執行期間有要求提出時傳送自訂事件。 如果顯示 ServiceProfilerSample 自訂事件，表示 Profiler 已將追蹤附加至要求，而您可以在 [Application Insights 效能] 窗格中檢視追蹤。

    若未顯示遙測資料，表示 Profiler 並未執行。 若要進行疑難排解，請參閱本文後續與您的應用程式類型有關的疑難排解章節。  

     ![搜尋 Profiler 遙測資料][profiler-search-telemetry]

1. 如果在 Profiler 執行期間有要求，請確定這些要求會由已啟用 Profiler 的應用程式元件處理。 雖然應用程式有時候會由多個元件組成，但其中只有部分元件會啟用 Profiler。 [設定 Application Insights Profiler] 窗格會顯示已上傳追蹤的元件。

### <a name="other-things-to-check"></a>需要檢查的其他事項
* 確定應用程式執行於 .NET Framework 4.6 上。
* 如果 Web 應用程式是 ASP.NET Core 應用程式，則必須至少執行 ASP.NET Core 2.0。
* 如果您嘗試檢視的資料已存在好幾週，請試著限縮時間篩選條件，然後再試一次。 追蹤會在七天後刪除。
* 確定 Proxy 或防火牆未封鎖對 https://gateway.azureserviceprofiler.net 的存取。

### <a id="double-counting"></a>平行執行緒重複計算

在某些情況下，堆疊檢視器中的總時間計量會比要求的持續時間還長。

如果要求有兩個或更多個關聯的執行緒，且這些執行緒以平行方式運作，就可能發生這種情形。 在此情況下，執行緒的總時間就會超出經過時間。 一個執行緒可能會等候另一個執行緒完成。 檢視器會嘗試偵測這種情況，並省略不重要的等候。 此時，它會寧可顯示過多資訊，以免省略可能具重要性的資訊。

當您在追蹤資料內看到平行執行緒時，請判斷正在等候的執行緒，以便確定要求的關鍵路徑。 通常，快速進入等候狀態的執行緒就只是在等候其他執行緒。 請專注在其他執行緒上，並忽略等候中執行緒的時間。

### <a name="error-report-in-the-profile-viewer"></a>設定檔檢視器中的錯誤報表
您可以在入口網站中提交支援票證。 請務必包含錯誤訊息中的相互關聯識別碼。

## <a name="troubleshoot-profiler-on-azure-app-service"></a>對 Azure App Service 上的 Profiler 進行疑難排解
若要讓 Profiler 正常運作：
* Web 應用程式服務方案必須至少是「基本」層。
* 您的 Web 應用程式必須已啟用 Application Insights。
* 在設定 Web 應用程式的 **APPINSIGHTS_INSTRUMENTATIONKEY** 應用程式設定時，所使用的檢測金鑰必須和 Application Insights SDK 所使用的檢測金鑰相同。
* Web 應用程式必須已定義 **APPINSIGHTS_PROFILERFEATURE_VERSION** 應用程式設定，並將其設定為 1.0.0。
* Web 應用程式必須已定義 **DiagnosticServices_EXTENSION_VERSION** 應用程式設定，並將其值設定為 ~3。
* **ApplicationInsightsProfiler3** WebJob 必須正在執行。 若要檢查 WebJob：
   1. 移至 [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)。
   1. 在 [工具] 功能表中，選取 [WebJob 儀表板]。  
      [WebJob] 窗格隨即開啟。 
   
      ![profiler-webjob]   
   
   1. 若要檢視 WebJob 的詳細資料 (包括記錄)，請選取 [ApplicationInsightsProfiler2] 連結。  
     [連續 WebJob 詳細資料] 窗格隨即開啟。

      ![profiler-webjob-log]

如果您無法釐清 Profiler 無法運作的原因，您可以下載記錄並傳送給我們的小組，以尋求協助。 
    
### <a name="manual-installation"></a>手動安裝

當您設定 Profiler 時，系統會對 Web 應用程式的設定進行更新。 您可以視環境需要手動套用這些更新。 可能的範例為您的應用程式在 PowerApps 的 Web Apps 環境中執行。 若要手動套用更新，請執行下列作業：

1. 在 [Web 應用程式控制項] 窗格中，開啟 [設定]。

1. 將 [.Net Framework 版本] 設定為 [v4.6]。

1. 將 [一律開啟] 設定為 [開啟]。

1. 新增 **APPINSIGHTS_INSTRUMENTATIONKEY** 應用程式設定，並將值設定為與 SDK 所用相同的檢測金鑰。

1. 新增 **APPINSIGHTS_PROFILERFEATURE_VERSION** 應用程式設定，並將其值設定為 1.0.0。

1. 新增 **DiagnosticServices_EXTENSION_VERSION** 應用程式設定，並將其值設定為 ~3。

### <a name="too-many-active-profiling-sessions"></a>太多個使用中分析工作階段

目前，您最多可以在於相同服務方案中執行的 4 個 Azure Web 應用程式及部署位置上啟用 Profiler。 如果您在一個 App Service 方案中執行四個以上的 Web 應用程式，Profiler 可能會擲回 *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*。 Profiler 會針對每個 Web 應用程式個別執行，並嘗試為每個應用程式啟動 Windows 事件追蹤 (ETW) 工作階段。 但一次可執行的 ETW 工作階段數目是有限的。 如果 Profiler WebJob 回報有太多作用中的分析工作階段，請將一些 Web 應用程式移至不同的服務方案。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>部署錯誤：目錄尚有資料 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

如果您要將 Web 應用程式重新部署到已啟用 Profiler 的 Web Apps 資源，您可能會看到下列訊息：

目錄尚有資料 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

如果您從指令碼或從 Azure DevOps 部署管線執行 Web Deploy，就會發生此錯誤。 解決方法是將下列額外的部署參數新增至 Web Deploy 工作：

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

這些參數會刪除 Application Insights Profiler 所使用的資料夾，並將重新部署程序解除封鎖。 它們並不會影響目前正在執行的 Profiler 執行個體。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>如何判斷 Application Insights Profiler 是否有在執行？

Profiler 會在 Web 應用程式中以連續 WebJob 的形式執行。 您可以在 [Azure 入口網站](https://portal.azure.com)中開啟 Web 應用程式資源。 在 [WebJobs] 窗格中，檢查 **ApplicationInsightsProfiler** 的狀態。 如果它並沒有在執行，請開啟 [記錄] 以取得詳細資訊。

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>對 Profiler 和 Azure 診斷的問題進行疑難排解

若要查看 Azure 診斷是否已正確設定 Profiler，請執行下列三項作業： 
1. 首先，請確認部署的 Azure 診斷組態內容是否符合您的預期。 

1. 其次，請確定 Azure 診斷在 Profiler 命令列上傳遞了正確的 iKey。 

1. 最後，請檢查 Profiler 記錄檔，以確認 Profiler 是否已執行但傳回錯誤。 

若要檢查用來設定 Azure 診斷的設定：

1. 登入虛擬機器 (VM)，然後開啟位於下列位置的記錄檔： 

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
    ```

1. 在該檔案中，您可以搜尋 **WadCfg** 字串，以找出傳至 VM 用以設定 Azure 診斷的設定。 您可以確認 Profiler 接收所使用的 iKey 是否正確。

1. 檢查用來啟動 Profiler 的命令列。 用來啟動 Profiler 的引數會位於下列檔案中：

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. 確定 Profiler 命令列上的 iKey 正確無誤。 

1. 使用在上述 *config.json* 檔案中找到的路徑，檢查 Profiler 記錄檔。 其中會顯示偵錯資訊，指出 Profiler 所使用的設定。 此外也會顯示來自 Profiler 的狀態和錯誤訊息。  

    如果在應用程式接收要求時，Profiler 正在執行中，則會顯示下列訊息：*從 iKey 偵測到活動*。 

    在追蹤上傳期間，會顯示下列訊息：*開始上傳追蹤*。 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








