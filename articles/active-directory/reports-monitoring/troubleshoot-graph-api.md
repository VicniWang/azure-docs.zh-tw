---
title: 針對 Azure Active Directory 報告 API 中的錯誤進行疑難排解 | Microsoft Docs
description: 為您提供呼叫 Azure Active Directory 報告 API 時所發生錯誤的解決方式。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
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
ms.openlocfilehash: 41d085b8043470ea16b4c2493760e4dc5875fe15
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176436"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>針對 Azure Active Directory 報告 API 中的錯誤進行疑難排解

本文列出當您使用 MS Graph API 存取活動報告時，可能會遇到的常見錯誤訊息，以及其解決方式的步驟。

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>存取 Microsoft Graph V2 端點時發生 500 HTTP 內部伺服器錯誤

我們目前不支援 Microsoft Graph v2 端點 - 請務必使用 Microsoft Graph v1 端點來存取活動記錄。

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Error:無法從 AD Graph 取得使用者角色

當嘗試使用 Graph 總管存取登入時，您可能會遇到這個錯誤訊息。 請確定您同時使用 Graph 總管 UI 中的兩個登入按鈕來登入帳戶，如下圖所示。 

![Graph 總管](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Error:無法從 AD Graph 進行進階授權檢查 

如果您在嘗試使用 Graph 總管存取登入時，遇到這個錯誤訊息，請在您帳戶左側導覽列底下選擇 [修改權限]，然後選取 [Tasks.ReadWrite] 和 [Directory.Read.All]。 

![修改權限 UI](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Error:沒有任何 B2C 租用戶，或租用戶沒有進階授權

存取登入報表需要 Azure Active Directory 進階 1 (P1) 授權。 如果您在存取登入時看到這個錯誤訊息，請確定您的租用戶已獲得 Azure AD P1 授權。

### <a name="error-user-is-not-in-the-allowed-roles"></a>Error:使用者不是允許的角色 

如果您在嘗試使用 API 存取稽核記錄或登入時，看到這個錯誤訊息，請確定您的帳戶屬於 Azure Active Directory 租用戶中的**安全性讀者**或**報告讀者**角色。 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Error:應用程式遺漏 AAD「讀取目錄資料」權限 

請遵循[存取 Azure Active Directory 報告 API 的必要條件](howto-configure-prerequisites-for-reporting-api.md)中的步驟，以確保應用程式是以正確的權限集執行。 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Error:應用程式遺漏 MS Graph API「讀取所有稽核記錄資料」權限

請遵循[存取 Azure Active Directory 報告 API 的必要條件](howto-configure-prerequisites-for-reporting-api.md)中的步驟，以確保應用程式是以正確的權限集執行。 

## <a name="next-steps"></a>後續步驟

[使用稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[使用登入活動報告 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
