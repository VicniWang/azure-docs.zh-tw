---
title: 開始使用 Azure AD 報告 API | Microsoft Docs
description: 如何開始使用 Azure Active Directory 報告 API
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0cab77617a72aeb43d7bb1786c7ccfad0382bf9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166193"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>開始使用 Azure Active Directory 報告 API

Azure Active Directory 會向您提供各種[報告](overview-reports.md)，內含 SIEM 系統、稽核和商業智慧工具等應用程式的實用資訊。 

藉由對 Azure AD 報告使用 Microsoft Graph API，您可透過一組以 REST 為基礎的 API 為您提供資料的程式設計存取方式。 您可以從各種程式設計語言和工具呼叫這些 API。

本文會概述報告 API，包括其存取方式。

如果您遇到問題，請參閱[如何取得 Azure Active Directory 支援](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)。

## <a name="prerequisites"></a>必要條件

若要存取報告 API (不論是否需要使用者介入)，您需要：

1. 指派角色 (安全性讀取者、安全性管理員或全域管理員)
2. 註冊應用程式
3. 授與權限
4. 收集組態設定

如需詳細指示，請參閱[存取 Azure Active Directory 報告 API 的必要條件](howto-configure-prerequisites-for-reporting-api.md)。 

## <a name="api-endpoints"></a>API 端點 

稽核記錄的 Microsoft Graph API 端點是 `https://graph.microsoft.com/beta/auditLogs/directoryAudits`，登入的 Microsoft Graph API 端點則是 `https://graph.microsoft.com/beta/auditLogs/signIns`。 如需詳細資訊，請參閱[稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)和[登入 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn)。

此外，您可以使用 [Identity Protection 風險事件 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) 來透過 Microsoft Graph 以程式設計方式存取安全性偵測。 如需詳細資訊，請參閱[開始使用 Azure Active Directory Identity Protection 和 Microsoft Graph](../identity-protection/graph-get-started.md)。 

> [!NOTE]
>  **https:\/\/graph.windows.net\/\<tenant-name\>\/reports\/** 端點已淘汰。 請使用上面所述的新 API 端點，以程式設計方式存取活動和安全性報告。
  
## <a name="apis-with-graph-explorer"></a>API 與 Graph 總管

您可以使用 [MSGraph 總管](https://developer.microsoft.com/graph/graph-explorer)來確認登入並稽核 API 資料。 請務必同時使用 Graph 總管 UI 中的兩個登入按鈕來登入帳戶，並如下所示地設定租用戶的 **AuditLog.Read.All** 和 **Directory.ReadAll** 權限。   

![Graph 總管](./media/concept-reporting-api/graph-explorer.png)

![修改權限 UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>使用憑證存取 Azure AD 報告 API 

如果您打算在不需使用者介入的情況下擷取報告資料，請使用 Azure AD 報告 API 搭配憑證。

如需詳細指示，請參閱[使用 Azure AD 報告 API 搭配憑證來取得資料](tutorial-access-api-with-certificates.md)。

## <a name="next-steps"></a>後續步驟

 * [存取報告 API 的必要條件](howto-configure-prerequisites-for-reporting-api.md) 
 * [使用 Azure AD 報告 API 搭配憑證來取得資料](tutorial-access-api-with-certificates.md)
 * [針對 Azure AD 報告 API 中的錯誤進行疑難排解](troubleshoot-graph-api.md)


