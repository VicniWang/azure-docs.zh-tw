---
title: 試用雲端式 IoT 預測性維護解決方案 - Azure | Microsoft Docs
description: 在本快速入門中，您會部署預測性維護 Azure IoT 解決方案加速器，然後登入和使用解決方案儀表板。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: fdb481961680d5f84170a25c7a76bffa8423e0cc
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097162"
---
# <a name="quickstart-try-a-cloud-based-solution-to-run-a-predictive-maintenance-analysis-on-my-connected-devices"></a>快速入門：嘗試以雲端式解決方案對我的連線裝置執行預測性維護分析

本快速入門會說明如何部署 Azure IoT 預測性維護解決方案加速器，來執行雲端式預測性維護模擬。 在部署解決方案加速器之後，您要使用解決方案的 [儀表板] 頁面，針對模擬飛機引擎的資料執行預測性維護分析。 您可以使用這個解決方案加速器，作為您自己實作的起點，或是作為學習工具。

在此模擬中，Fabrikam 是區域性航空公司；以優惠的價格為客戶提供優良的服務，是該公司努力的目標。 維護問題是造成航班延誤的原因之一，而引擎維護又是其中最為棘手的項目。 Fabrikam 必須嚴防飛行期間發生引擎故障，所以會定期檢查其引擎，並根據計劃安排維護時間表。 但因為飛機引擎的問題不一。 所以有一些引擎維護工作並非必要。 但嚴重者若在執行維護工作之前發生問題，可能會造成飛機停飛。 如果飛機所在地點缺少適當的技術人員或備品零件，這些問題會更嚴重。

Fabrikam 的飛機引擎由各種感應器進行檢測，而這些感應器會監視飛行期間的引擎狀況。 Fabrikam 累積了這些感應器多年來在引擎運作和故障方面的資料。 Fabrikam 的資料科學家使用這些資料開發出一個模型，用以預測飛機引擎的剩餘使用壽命 (RUL)。 此模型會在四個引擎感應器的資料和最終導致故障的引擎耗損之間找出相互關聯。 Fabrikam 現在除了繼續執行定期安全檢查以外，也會在每次飛行後使用這些模型來計算每具引擎的 RUL。 Fabrikam 現在已可以預測未來的失敗點，並規劃維修以盡可能縮短停飛時間。 此程序可降低營運成本，同時確保乘客與機組員的安全。

若要完成本快速入門，您需要有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="deploy-the-solution"></a>部署解決方案

在將解決方案加速器部署到 Azure 訂用帳戶時，必須設定一些組態選項。

瀏覽至 [Microsoft Azure IoT 解決方案加速器](https://www.azureiotsolutions.com)，並使用您的 Azure 帳戶認證登入。

按一下 [預測性維護] 圖格。 在 [預測性維護] 圖格上，按一下 [立即試用]：

![立即試用](./media/quickstart-predictive-maintenance-deploy/predictivemaintenance.png)

在 [建立預測性維護解決方案] 頁面上，輸入預測性維護解決方案加速器的唯一 [解決方案名稱]。 在本快速入門中，我們會使用 **MyPredictiveMaintenance**。

選取您要用來部署解決方案加速器的 [訂用帳戶] 和 [區域]。 一般而言，您會選擇與您最接近的區域。 在本快速入門中，我們會使用 **Visual Studio Enterprise** 和**美國東部**。 您必須是訂用帳戶中的[全域管理員或使用者](iot-accelerators-permissions.md)。

按一下 [建立] 來開始部署。 此程序至少需要執行五分鐘：

![預測性維護解決方案詳細資料](./media/quickstart-predictive-maintenance-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>登入解決方案

當您完成部署至 Azure 訂用帳戶時，會看到解決方案圖格上的綠色核取記號和 [就緒]。 您現在可以登入預測性維護解決方案加速器儀表板。

在 [已佈建的解決方案] 頁面上，按一下新的預測性維護解決方案加速器。

![解決方案面板](./media/quickstart-predictive-maintenance-deploy/solution.png)

 您可以在顯示的頁面中檢視解決方案加速器的相關資訊。 選擇 [移至解決方案加速器] 以檢視預測性維護解決方案加速器：

![解決方案面板](./media/quickstart-predictive-maintenance-deploy/solutionpaneldetails.png)

按一下 [接受] 以接受使用權限要求，瀏覽器中隨即會顯示預測性維護解決方案儀表板：

![解決方案儀表板](./media/quickstart-predictive-maintenance-deploy/solutiondashboard.png)

按一下 [開始模擬] 來開始模擬。 感應器記錄、RUL、週期和 RUL 記錄會填入儀表板：

![模擬執行中](./media/quickstart-predictive-maintenance-deploy/simulationrunning.png)

當 RUL 小於 160 時 (為了示範目的而選擇的任意臨界值)，解決方案入口網站會在 RUL 旁邊顯示警告符號。 解決方案入口網站也會將飛機引擎醒目標示成黃色。 請注意 RUL 值有整體向下的趨勢，但傾向於上下波動。 此行為起因於變動的週期長度和模型精確度。

![模擬警告](./media/quickstart-predictive-maintenance-deploy/simulationwarning.png)

完整模擬需要約 35 分鐘的時間才能完成 148 個週期。 160 RUL 臨界值第一次在大約 5 分鐘的時候達到，而這兩個引擎在大約 8 分鐘的時候同時達到臨界值。

模擬會徹底執行 148 個週期的完整資料集並確定最終的 RUL 和週期值。

您可以隨時停止模擬，但按一下 [開始模擬]  會從資料集的開頭重新執行模擬。

## <a name="clean-up-resources"></a>清除資源

如果您打算進一步探索，請讓預測性維護解決方案加速器維持部署。

如果您不再需要解決方案加速器，可加以選取，然後按一下 [刪除解決方案]，從[已佈建的解決方案](https://www.azureiotsolutions.com/Accelerators#dashboard)頁面中加以刪除：

![刪除解決方案](media/quickstart-predictive-maintenance-deploy/deletesolution.png)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署預測性維護解決方案加速器，並執行模擬。

如需有關解決方案加速器與模擬飛機引擎的詳細資訊，請繼續下列文章。

> [!div class="nextstepaction"]
> [預測性維護解決方案加速器概觀](iot-accelerators-predictive-walkthrough.md)
