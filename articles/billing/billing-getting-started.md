---
title: 在 Azure 中避免意外的成本、管理計費 | Microsoft Docs
description: 了解如何避免 Azure 帳單上的意外費用。 使用 Microsoft Azure 訂用帳戶的成本追蹤與管理功能。
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: banders
ms.openlocfilehash: 732ac5e1e82c67d2f4a0a3443b85c67712f4f651
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101646"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>使用 Azure 計費與成本管理避免非預期的費用

當您註冊 Azure 時，為了深入了解您的支出，有幾件事您可以做。 [定價計算機](https://azure.microsoft.com/pricing/calculator/)可以在您建立 Azure 資源之前提供成本估計值。 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)可為您提供目前的成本明細和您訂用帳戶的預測。 如果您需要將不同專案或小組的成本分組並加以了解，請查看[資源標記](../azure-resource-manager/resource-group-using-tags.md)。 如果您的組織有您偏好使用的報告系統，請查看[計費 API](billing-usage-rate-card-overview.md)。

- 如果您的訂用帳戶屬於 Enterprise 合約 (EA)，可使用上線的 Azure 入口網站費用公開預覽功能。 如果您透過雲端解決方案提供者 (CSP) 或 Azure 贊助取得訂用帳戶，以下某些功能可能不適用於您。 如需詳細資訊，請參閱[適用於 EA、CSP 和贊助的其他資源](#other-offers)。

- 如果您的訂用帳戶是免費試用版、[Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)、Azure in Open (AIO) 或 BizSpark，當您用完所有的信用額度時，將會自動停用您的訂用帳戶。 深入了解[消費限制](#spending-limit)，以避免您的訂用帳戶發生未預期的停用。

- 如果您已註冊 [Azure 免費帳戶](https://azure.microsoft.com/free/)，[您就能免費使用一些最常用的 Azure 服務 12 個月](billing-create-free-services-included-free-account.md)。 除了下列建議之外，請參閱[避免產生免費帳戶的費用](billing-avoid-charges-free-account.md)。

## <a name="get-estimated-costs-before-adding-azure-services"></a>在新增 Azure 服務之前取得估計的成本

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>使用價格計算機在線上評估成本

查看[價格計算機](https://azure.microsoft.com/pricing/calculator/)即可取得您感興趣的服務每月成本評估。 您可以新增任何第一方 Azure 資源來取得估計成本。

![定價計算機功能表的螢幕擷取畫面](./media/billing-getting-started/pricing-calc.png)

例如，如果您讓 A1 Windows 虛擬機器 (VM) 持續運作，則運算時間的預估成本為每月 $66.96 美元：

![價格計算機的螢幕擷取畫面，顯示 A1 Windows 虛擬機器的每月預估成本為 $66.96 美元](./media/billing-getting-started/pricing-calcVM.png)

如需價格的詳細資訊，請參閱此[常見問題集](https://azure.microsoft.com/pricing/faq/)。 或者，如果您需要洽詢 Azure 銷售人員，請連絡 1-800-867-1389。

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>在 Azure 入口網站中檢閱預估成本

一般而言，當您在 Azure 入口網站中新增服務時，會有一個檢視向您顯示類似的每月預估成本。 例如，當您選擇 Windows VM 的大小時，會看到計算時數的預估每月成本：

![範例︰A1 Windows VM 預估每月成本為 $66.96 美元](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="spending-limit"></a> 檢查您是否開啟消費限制

如果您有使用信用額度的訂用帳戶，那麼您的消費限制依預設是開啟的。 如此一來，當您花光您的信用額度時，就不會針對您的信用卡收費。 請參閱 [Azure 供應項目完整清單及消費限制可用性](https://azure.microsoft.com/support/legal/offer-details/)。

不過，如果您達到消費限制，就會停用您的服務。 這表示您的 VM 會被解除配置。 若要避免服務停機，您必須關閉消費限制。 任何超額部分都會用您在檔案上的信用卡收費。 

若要查看您是否已開啟消費限制，請移至[帳戶中心的訂用帳戶檢視](https://account.windowsazure.com/Subscriptions)。 如果您的消費限制是開啟的，則會出現橫幅︰

![顯示帳戶中心已開啟消費限制的警告的螢幕擷取畫面](./media/billing-getting-started/spending-limit-banner.PNG)

按一下橫幅並遵循提示，即可移除消費限制。 如果您在註冊時未輸入信用卡資訊，則必須輸入信用卡資訊，才能移除消費限制。 如需詳細資訊，請參閱 [Azure 消費限制 - 運作方式以及啟用或移除方法](https://azure.microsoft.com/pricing/spending-limits/)。

您可以使用 [Cloudyn](https://www.cloudyn.com/) 服務建立警示，就能在出現異常消費和超支風險時自動通知利害關係人。 您可以使用支援根據預算和成本閾值發出警示的報告來建立警示。 如需使用 Cloudyn 的詳細資訊，請參閱[教學課程：檢閱使用量和成本](../cost-management/tutorial-review-usage.md)。

此範例使用 [隨時間變化的實際成本] 報告，在您的 Azure VM 消費接近總預算時通知您。 在此案例中，您的總預算為 20,000 美元，而您想要在成本接近預算的一半 (9000 美元) 時收到通知，並且在成本達到 10,000 美元時收到額外的警示。

1. 在 Cloudyn 入口網站頂端的功能表中，選取 [成本] > [成本分析] > [隨時間變化的實際成本]。 
2. 將 [群組] 設定為 [服務]，並將 [Filter on the service] \(服務上的篩選) 設定為 [Azure/VM]。 
3. 在報告的右上方選取 [動作]，然後選取 [排程報告]。
4. 若要依排定的間隔傳送報告電子郵件給自己，請在 [儲存或排程此報告] 方塊中選取 [排程] 索引標籤。 務必要選取 [透過電子郵件傳送]。 您所使用的任何標記、群組和篩選都會包含在以電子郵件傳送的報告中。 
5. 選取 [閾值] 索引標籤，然後選取 [實際成本與閾值]。 
   1. 在 [紅色警示] 閾值方塊中輸入 10000。 
   2. 在 [黃色警示] 閾值方塊中輸入 9000。 
   3. 在 [連續警示數目] 方塊中，輸入要接收的連續警示數目。 當您收到所指定的警示總數時，不會再傳送其他警示。 
6. 選取 [ **儲存**]。

    ![根據費用閾值顯示紅色警示和黃色警示的範例](./media/billing-getting-started/schedule-alert01.png)

您也可以選擇 [成本百分比與預算] 閾值計量來建立警示。 這可讓您將閾值指定為預算的百分比，而不是貨幣值。

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>使用 Azure 服務時監視成本的方式

### <a name="tags"></a> 將標記新增至您的資源以便將計費資料分組

針對支援的服務，您可以使用標記來將您的計費資料分組。 例如，如果您有不同小組執行數個 VM，您可以使用標記，依照成本中心 (HR、行銷、財務) 或環境 (生產環境、預先生產環境、測試) 來將成本分類。 

![顯示在入口網站中設定標籤的螢幕擷取畫面](./media/billing-getting-started/tags.PNG)

這些標記會顯示在不同的成本報告檢視中。 例如，它們會立即顯示您的[成本分析檢視](#costs)，並在第一次計費期間之後，顯示詳細使用量 .csv。

如需詳細資訊，請參閱 [使用標記組織您的 Azure 資源](../azure-resource-manager/resource-group-using-tags.md)。

### <a name="costs"></a>定期查看入口網站以了解成本細分和完工速率

在您的服務開始執行之後，請定期檢查它們花了您多少錢。 您可以在 Azure 入口網站中看到目前的花費和完工速率。

1. 瀏覽 [Azure 入口網站中的 [訂用帳戶]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 然後選取訂用帳戶。

2. 如果您的訂用帳戶支援，您就會看到成本明細和完工速率。

    ![Azure 入口網站中完工速率和成本細分的螢幕擷取畫面](./media/billing-getting-started/burn-rate.PNG)

3. 按一下左側清單中的 [成本分析]，可依照資源檢視成本細分。 新增服務之後，請等候 24 小時以便資料填入。

    ![Azure 入口網站中成本分析檢視的螢幕擷取畫面](./media/billing-getting-started/cost-analysis.PNG)

4. 您可以依照不同屬性來篩選，例如[標記](#tags)、資源類型、資源群組和時間範圍。 按一下 [套用] 可確認篩選條件，如果您需要將檢視匯出至逗號分隔值 (.csv) 檔案，請按 [下載]。

5. 此外，您可以按一下資源來查看您的每日支出記錄，以及每一天的成本是多少。

    ![Azure 入口網站中消費歷程記錄檢視的螢幕擷取畫面](./media/billing-getting-started/costhistory.PNG)

我們建議您將您看到的成本與您在選取服務時看到的預估進行核對。 如果成本和預估的相差很多，請再次檢查您為資源選取的定價方案。

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>請考慮啟用成本削減功能，例如 VM 自動關機

視您的案例而定，您可以在 Azure 入口網站中設定 VM 自動關機。 如需詳細資訊，請參閱[使用 Azure Resource Manager 的 VM 自動關機](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) \(英文\)。

![在入口網站中的自動關機選項的螢幕擷取畫面](./media/billing-getting-started/auto-shutdown.PNG)

自動關機與您在 VM 中使用電源選項關閉不一樣。 自動關機會停止並解除配置您的 VM，以停止額外的使用費用。 如需詳細資訊，請參閱 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 的定價常見問題，以及 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)有關 VM 的狀態。

如需更多針對開發和測試環境的成本削減功能，請參閱 [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)。

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>開啟並查看 Azure 建議程式的建議

[Azure 建議程式](../advisor/advisor-overview.md)是一項功能，可協助您藉由找出低使用率的資源來降低成本。 在 Azure 入口網站中造訪 Adviso：

![Azure 入口網站中 Azure 建議程式按鈕的螢幕擷取畫面](./media/billing-getting-started/advisor-button.PNG)

然後，您可以從 [建議程式] 儀表板中的 [成本] 索引標籤取得可操作的建議：

![建議程式的成本建議範例的螢幕擷取畫面](./media/billing-getting-started/advisor-action.PNG)

如需詳細資訊，請參閱[建議程式成本建議](../advisor/advisor-cost-recommendations.md)。

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>在計費週期結束時檢閱成本

計費週期結束後，您將可以取用您的發票。 您也可以[下載過去的發票和詳細使用量檔案](billing-download-azure-invoice-daily-usage-date.md)，確定已向您正確收費。 如需比較每日使用量和發票的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。

### <a name="billing-api"></a>計費 API

使用我們的計費 API 可以用程式設計方式取得使用量資料。 RateCard API 與使用情況 API 一起使用即可取得您的計費使用量。 如需詳細資訊，請參閱[深入瞭解 Microsoft Azure 資源耗用量](billing-usage-rate-card-overview.md)。

## <a name="other-offers"></a> 其他資源和特殊案例

### <a name="ea-csp-and-sponsorship-customers"></a>EA、CSP 和贊助客戶
若要開始使用，請連絡帳戶管理員或 Azure 。

| 供應項目 | 資源 |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise 合約 (EA) | [EA 入口網站](https://ea.azure.com/)、[協助文件](https://ea.azure.com/helpdocs)和 [Power BI 報告](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| 雲端解決方案提供者 (CSP) | 與您的提供者討論 |
| Azure 贊助 | [贊助入口網站 (英文)](https://www.microsoftazuresponsorships.com/) |

如果您管理的是大型組織的 IT，我們建議您閱讀 [Azure 企業 Scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) 和[企業 IT 技術白皮書](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf 下載，僅提供英文版)。

#### <a name="EA"></a> 預覽 Azure 入口網站中的 Enterprise 合約成本檢視 

企業成本檢視目前只提供公開預覽。 下列為需注意的事項：

- 訂閱費用是以使用情況計費，不包含預付金額、超額部分、包含的數量、調整內容和稅金。 系統會在申請時計算實際費用。
- 在 Azure 入口網站中顯示的數量可能與在企業版入口網站中顯示的不同。 在企業版入口網站中的更新可能需要幾分鐘之後才會顯示在 Azure 入口網站中。
- 如果您沒有看到成本，原因可能為下列其中之一：
    - 您沒有訂用帳戶層級的權限。 若要查看企業成本檢視，您必須是訂用帳戶層級上的帳單讀者、讀者、參與者或擁有者。
    - 您是帳戶擁有者且您的申請管理員已停用「AO 檢視費用」設定。  請連絡您的申請管理員以取得費用存取權。 
    - 您是部門管理員且您的申請管理員已停用「DA 檢視費用」設定。  請連絡您的註冊管理員以取得存取權。
    - 您是透過通道合作夥伴購買 Azure，而該合作夥伴未釋出定價資訊。  
- 如果您在企業版入口網站中更新成本存取相關的設定，則變更可能需要幾分鐘才會在 Azure 入口網站中顯示。
- 消費限制和發票指導方針不適用於 EA 訂用帳戶。

### <a name="check-your-subscription-and-access"></a>檢查訂用帳戶和存取權

若要檢視成本，您必須有[帳單資訊的訂用帳戶層級存取權](billing-manage-access.md)。 只有帳戶管理員可以存取[帳戶中心](https://account.azure.com/Subscriptions)、變更帳單資訊，以及管理訂用帳戶。 帳戶管理員是完成註冊程序的人員。 如需詳細資訊，請參閱[新增或變更管理訂用帳戶或服務的 Azure 系統管理員角色](billing-add-change-azure-subscription-administrator.md)。

若要查看您是否為帳戶管理員，請移至 [Azure 入口網站中的訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 看看您有存取權的訂用帳戶清單。 查看 [我的角色] 底下。 如果它顯示*帳戶管理員*，那麼您就是帳戶管理員。 如果它顯示諸如*擁有者*之類的其他角色，則表示您沒有完整權限。

![Azure 入口網站的訂用帳戶檢視中您的角色的螢幕擷取畫面](./media/billing-getting-started/sub-blade-view.PNG)

如果您不是帳戶管理員，則表示有人可能藉由使用 [Azure Active Directory 角色型存取控制](../role-based-access-control/role-assignments-portal.md) (RBAC) 授與您部分存取權。 若要管理訂用帳戶及變更帳單資訊，請[尋找帳戶管理員](billing-subscription-transfer.md#whoisaa)。要求帳戶管理員執行該工作，或[將訂用帳戶移轉給您](billing-subscription-transfer.md)。

如果您的帳戶管理員已不在您組織，而您需要管理帳單，請[與我們連絡](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
