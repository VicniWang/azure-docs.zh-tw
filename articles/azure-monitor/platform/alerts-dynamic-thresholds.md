---
title: 在 Azure 監視器中建立具有動態閾值的警示
description: 建立警示，內含以機器學習為基礎的動態閾值
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 92a6d0f0cd9ef9a7d246624f89315a87a7fb26f9
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097804"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>在 Azure 監視器中具有動態閾值的計量警示 (公開預覽)

「具有動態閾值的計量警示」偵測會利用進階的機器學習 (ML) 來學習計量的歷史行為，以及識別模式和可能表示服務有問題的異常狀況。 它可讓使用者以完全自動化的方式透過 Azure Resource Manager API 設定警示規則，因此對於簡單的 UI 和大規模作業均提供支援。

警示規則建立後，只有在受監視的計量未依預期運作 (依據其專用的閾值) 時，才會引發。

我們很希望聽聽您的意見反應，來信請寄到 <azurealertsfeedback@microsoft.com>。

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>建議使用動態條件類型的原因和時機為何？

1. **可調整的警示** – 動態閾值警示規則可同時為數百個計量序列建立合適的閾值。 此外，定義單一計量的警示規則也同樣容易。 使用 UI 或 Azure Resource Manager API，可減少需要管理的警示規則。 在處理計量維度，或套用至多個資源 (例如所有的訂用帳戶資源) 時，可調整的方法尤有效用。 這意味著在警示規則的管理和建立方面都可省下大量時間。 [深入了解如何使用範本設定具有動態閾值的計量警示](alerts-metric-create-templates.md)。

1. **智慧型計量模式認知** – 透過我們獨特的 ML 技術，我們能夠自動偵測計量模式並依據一段時間的計量變化而調整，其中常會包括季節性 (每小時/每天/每週)。 依據一段時間的計量行為而調整，並根據其偏離模式的程度發出警示，就能更輕易地找出每個計量的「正確」閾值。 動態閾值中使用的 ML 演算法可防止不具備預期模式的雜訊 (低精確度) 或廣泛 (低召回率) 閾值。

1. **直覺式組態** – 動態閾值可讓您使用基本概念來設定計量警示，而無須具備廣泛的計量領域相關知識。

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>如何設定具有動態閾值的警示規則？

具有動態閾值的警示可透過 Azure 監視器中的計量警示來設定。 [深入了解如何設定計量警示](alerts-metric.md)。

## <a name="how-are-the-thresholds-calculated"></a>閾值的計算方式為何？

「動態閾值」會持續學習計量序列的資料，並嘗試使用一組演算法和方法來建立其模型。 它會偵測資料中的模式 (例如每小時/每天/每週之類的季節性)，並且能夠處理雜訊計量 (例如電腦 CPU 或記憶體) 以及具有低離散程度的計量 (例如可用性和錯誤率)。

選取閾值的方式，是偏離這些閾值的程度必須表示計量行為出現異常。

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>動態閾值中的「敏感度」設定有何意義？

警示閾值敏感度是一項基本概念，可控制須偏離計量行為達何種程度才會觸發警示。
此選項不像靜態閾值那麼需要計量領域相關知識。 可用的選項如下︰

- 高 – 閾值會嚴格且接近計量序列模式。 最小的偏差即會觸發警示規則，因此會產生較多警示。
- 中 – 較不嚴格而較為中庸的閾值，產生的警示會比高敏感度少 (預設值)。
- 低 – 閾值會寬鬆且偏離計量序列模式較遠。 只有較大的偏差才會觸發警示規則，因此會產生較少警示。

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>動態閾值中有哪些「運算子」設定選項？

動態閾值警示規則可根據計量行為，針對使用相同警示規則的上限和下限建立自訂的閾值。
您可以選擇在符合下列三個條件之一時觸發警示：

- 大於閾值上限或低於閾值下限 (預設值)
- 大於閾值上限
- 低於閾值下限。

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>動態閾值中的進階設定有何意義？

**失敗期間** - 動態閾值也可讓您設定「觸發警示的違規次數」，也就是系統在特定時間範圍內要引發警示所需的最小偏差次數 (預設時間範圍為 20 分鐘內四次偏差)。 使用者可以設定失敗期間，並藉由變更失敗期間和時間範圍來選擇要警示的標的。 這項功能可減少暫時性尖峰所產生的警示雜訊。 例如︰

若要在問題持續 20 分鐘、在 5 分鐘的指定期間分組內連續發生 4 次時觸發警示，請使用下列設定：

![問題持續 20 分鐘、在 5 分鐘的指定期間分組內連續發生 4 次的失敗期間設定](media/alerts-dynamic-thresholds/0008.png)

若要以 5 分鐘為期間，在過去 30 分鐘內有 20 分鐘發生違反動態閾值的情況時觸發警示，請使用下列設定：

![以 5 分鐘為期間分組，在過去 30 分鐘內有 20 分鐘發生問題的失敗期間設定](media/alerts-dynamic-thresholds/0009.png)

**忽略資料 (以下時間之前)** - 使用者也可以選擇性地定義系統應開始計算閾值的開始日期。 如果資源先前執行於測試模式中，而現已升級供生產工作負載之用，因此應忽略任何計量在測試階段的行為，就可能會發生常見的使用案例。

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>計量中緩慢的行為變更是否會觸發警示？

大概沒有。 動態閾值適合用於偵測明顯的偏差，而不是緩慢發展的問題。

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>會有多少資料用來預覽並計算閾值？

圖表中顯示的閾值在計量的警示規則建立前是根據歷史資料的最後 10 天而計算的。在警示規則建立後，動態閾值會取得其他可用的歷史資料，並且根據新資料持續學習，讓閾值更準確。

## <a name="dynamic-thresholds-best-practices"></a>動態閾值最佳做法

「動態閾值」可以套用至任何平台或「Azure 監視器」中的自訂計量，而且它也已針對常見的應用程式和基礎結構計量進行過調整。
下列項目是有關如何使用「動態閾值」來設定這其中一些計量相關警示的最佳做法。

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>虛擬機器 CPU 百分比計量的相關動態閾值

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [監視]。 [監視] 檢視會將您的所有監視設定與資料合併在一個檢視中。

2. 按一下 [警示]，然後按一下 [+ 新增警示規則]。

    > [!TIP]
    > 大部分的資源刀鋒視窗也都有 [警示] (位於 [監視] 下的資源功能表)，您也可以從該處建立警示。

3. 在載入的內容窗格中，按一下 [選取目標]，選取您要發出警示的目標資源。 請使用 [訂用帳戶] 與 ['虛擬機器' 資源類型] 下拉式清單來尋找您想要監視的資源。 您也可以使用搜尋列來尋找您的資源。

4. 選取目標資源之後，請按一下 [新增準則]。

5. 選取 [CPU 百分比]。

6. (選擇性) 藉由調整 [期限] 和 [彙總] 來精簡計量。 針對此計量類型，不建議使用 [最大值] 彙總類型，因為它比較無法代表行為。 就 [最大值] 彙總類型而言，靜態閾值可能較為適當。

7. 您會看到過去 6 小時的計量圖表。 定義警示參數：
    1. **條件類型** - 選擇 [動態] 選項。
    1. **敏感度** - 選擇 [中]/[低] 敏感度以減少警示干擾。
    1. **運算子** - 除非行為代表應用程式使用量，否則請選擇 [大於]。
    1. **頻率** - 請考慮根據警示的業務影響降低頻率。
    1. **失敗期間** (進階選項) - 回顧時間範圍應該至少為 15 分鐘。 例如，如果期間設定為 5 分鐘，則失敗期間應該至少為 3 個以上。

8. 計量圖表會顯示以最近資料為基礎的計算臨界值。

9. 按一下 [完成] 。

10. 填寫 [警示詳細資料]，例如 [警示規則名稱]、[描述] 及 [嚴重性]。

11. 透過選取現有動作群組或建立新的動作群組，將動作群組新增至警示。

12. 按一下 [完成] 以儲存計量警示規則。

> [!NOTE]
> 透過入口網站建立的計量警示規則，會在與目標資源相同的資源群組中建立。

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Application Insights HTTP 要求執行時間的相關動態閾值

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [監視]。 [監視] 檢視會將您的所有監視設定與資料合併在一個檢視中。

2. 按一下 [警示]，然後按一下 [+ 新增警示規則]。

    > [!TIP]
    > 大部分的資源刀鋒視窗也都有 [警示] (位於 [監視] 下的資源功能表)，您也可以從該處建立警示。

3. 在載入的內容窗格中，按一下 [選取目標]，選取您要發出警示的目標資源。 使用 [訂用帳戶] 和 ['Application Insights' 資源類型] 下拉式清單來尋找您想要監視的資源。 您也可以使用搜尋列來尋找您的資源。

4. 選取目標資源之後，請按一下 [新增準則]。

5. 選取 [HTTP 要求執行時間]。

6. (選擇性) 藉由調整 [期限] 和 [彙總] 來精簡計量。 針對此計量類型，不建議使用 [最大值] 彙總類型，因為它比較無法代表行為。 就 [最大值] 彙總類型而言，靜態閾值可能較為適當。

7. 您會看到過去 6 小時的計量圖表。 定義警示參數：
    1. **條件類型** - 選擇 [動態] 選項。
    1. **運算子** - 選擇 [大於] 以減少在持續期間內針對改善而引發的警示。
    1. **頻率** - 請考慮根據警示的業務影響降低頻率。

8. 計量圖表會顯示以最近資料為基礎的計算臨界值。

9. 按一下 [完成] 。

10. 填寫 [警示詳細資料]，例如 [警示規則名稱]、[描述] 及 [嚴重性]。

11. 透過選取現有動作群組或建立新的動作群組，將動作群組新增至警示。

12. 按一下 [完成] 以儲存計量警示規則。

> [!NOTE]
> 透過入口網站建立的計量警示規則，會在與目標資源相同的資源群組中建立。
