---
title: 如何安裝與使用適用於 Azure Active Directory 的 Log Analytics 檢視 (預覽) | Microsoft Docs
description: 了解如何安裝與使用適用於 Azure Active Directory 的 Log Analytics 檢視 (預覽)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7220fc6b239d20e9f1fc81e7d18b4d7ca6f0ded5
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453625"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>安裝與使用適用於 Azure Active Directory 的 Log Analytics 檢視

Azure Active Directory 的 Log Analytics 檢視可協助您分析和搜尋 Azure AD 租用戶中的 Azure AD 活動記錄。 Azure AD 活動記錄包含：

* 稽核記錄：[稽核記錄活動報告](concept-audit-logs.md)可讓您對每個在租用戶中執行的工作存取歷程記錄。
* 登入記錄：透過[登入活動報告](concept-sign-ins.md)，您可以判斷是誰執行了稽核記錄中所報告的工作。

## <a name="prerequisites"></a>必要條件

若要使用 Log Analytics 檢視，您需要：

* Azure 訂用帳戶中的 Log Analytics 工作區。 了解如何[建立 Log Analytics 工作區](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)。
* 首先，請完成[將 Azure AD 活動記錄路由至 Log Analytics 工作區](howto-integrate-activity-logs-with-log-analytics.md)的步驟。
* 從 [GitHub 存放庫](https://aka.ms/AADLogAnalyticsviews)將檢視下載到您的本機電腦。

## <a name="install-the-log-analytics-views"></a>安裝 Log Analytics 檢視

1. 瀏覽至您的 Log Analytics 工作區。 若要這麼做，請先瀏覽至 [Azure 入口網站](https://portal.azure.com)，然後選取 [所有服務]。 在文字方塊中輸入 **Log Analytics**，然後選取 [Log Analytics 工作區]。 請選取您的活動記錄路由到的工作區，作為必要條件的一部分。
2. 依序選取 [檢視設計工具]、[匯入] 和 [選擇檔案]，以從本機電腦匯入檢視。
3. 選取您根據必要條件下載的檢視，然後選取 [儲存] 以儲存匯入。 請為 [Azure AD 帳戶佈建事件] 檢視和 [登入事件] 檢視執行此動作。

## <a name="use-the-views"></a>使用檢視

1. 瀏覽至您的 Log Analytics 工作區。 若要這麼做，請先瀏覽至 [Azure 入口網站](https://portal.azure.com)，然後選取 [所有服務]。 在文字方塊中輸入 **Log Analytics**，然後選取 [Log Analytics 工作區]。 請選取您的活動記錄路由到的工作區，作為必要條件的一部分。

2. 在進入工作區後，選取 [工作區摘要]。 您應該會看到下列三個檢視：

    * **Azure AD 帳戶佈建事件**：此檢視會顯示與稽核佈建活動有關的報告，例如佈建的新使用者數目和佈建失敗次數、更新的使用者數目和更新失敗次數，以及取消佈建的使用者數目和對應的失敗次數。    
    * **登入事件**：此檢視會顯示與監視登入活動相關性最高的報告 (例如依應用程式、使用者、裝置區分的登入)，以及追蹤一段時間內登入次數的摘要檢視。

3. 選取其中一個檢視，以跳到個別的報告。 您也可以設定任何報告參數的警示。 例如，我們將設定發生任何登入錯誤時的警示。 為此，請依序選取 [登入事件] 檢視、[一段時間的登入錯誤] 報告和 [Analytics]，以開啟詳細資料頁面，內含構成報告的實際查詢。 

    ![詳細資料](./media/howto-install-use-log-analytics-views/details.png)


4. 選取 [設定警示]，然後在 [警示準則] 區段下方選取 [每當自訂記錄搜尋是&lt;未定義的邏輯&gt;時]。 由於我們想要在發生任何登入錯誤時發出警示，因此請將預設警示邏輯的 [閾值] 設定為 **1**，然後選取 [完成]。 

    ![設定訊號邏輯](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. 輸入警示的名稱和描述，並將嚴重性設定為 [警告]。

    ![建立規則](./media/howto-install-use-log-analytics-views/create-rule.png)

6. 選取要警示的動作群組。 一般情況下，這可以是您要透過電子郵件或文字訊息通知的某個小組，或是使用 Webhook、Runbook、函式、邏輯應用程式或外部 ITSM 解決方案的自動化工作。 了解如何[在 Azure 入口網站中建立和管理動作群組](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)。

7. 選取 [建立警示規則] 以建立警示。 現在，在每次發生登入錯誤時，您都將收到警示。

## <a name="next-steps"></a>後續步驟

* [如何使用 Azure 監視器記錄分析活動記錄](howto-analyze-activity-logs-log-analytics.md)
* [開始在 Azure 入口網站中使用 Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)
