---
title: 部署方案 - Azure Active Directory | Microsoft Docs
description: 如何部署眾多 Azure Active Directory 功能的相關端對端指引。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dfce660340654e98883ae6e696644175c005459
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343080"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署方案
在尋找有關如何部署某些 Azure Active Directory (Azure AD) 功能的端對端指引嗎？ 下列部署方案會逐步帶您了解商業價值、規劃考量、設計，以及要成功推出數個較常見的 Azure AD 功能所需進行的操作程序。 

在文件內，您會發現電子郵件範本、系統架構圖、常見測試案例等資料。 

歡迎您提供關於這些文件的意見反應。 請您完成這份關於文件適用程度的簡短[問卷](https://aka.ms/deploymentplanfeedback)。 

|案例 |說明 |
|-|-|
|[Multi-Factor Authentication](https://aka.ms/MFADPDownload)|Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 Azure MFA 可使用系統管理員核准的驗證方法，以利保護對資料與應用程式的存取，同時達到對簡單登入程序的需求。|
|條件式存取[可下載的計劃](https://aka.ms/CADPDownload)或[線上計劃](https://aka.ms/deploymentplans/ca)|使用條件式存取，您便可以實作自動化的存取控制決定，來根據條件指出誰可以存取雲端應用程式。|
|[自助式密碼重設](https://aka.ms/SSPRDPDownload)|自助式密碼重設可協助使用者在必要時重設其密碼，而不需要系統管理員介入。|
|[Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)|Azure AD Privileged Identity Management (PIM) 可協助您管理遍及 Azure AD、Azure 資源及其他 Microsoft Online Services 的特殊權限系統管理角色。 PIM 提供 Just-In-Time 存取、要求核准工作流程，以及完全整合的存取權檢閱等解決方案，讓您能夠即時識別、發現和防止特殊權限角色的惡意活動。|
|[單一登入](https://aka.ms/SSODPDownload)|單一登入可協助您只使用單一使用者帳戶登入一次，然後就能夠存取所有進行工作所需的應用程式和資源。 登入之後，您就可以從 Microsoft Office 前往 SalesForce、Box，而不需要再驗證一次 (例如，輸入密碼)。|
|[無縫 SSO](https://aka.ms/SeamlessSSODPDownload)|使用者位於連線到公司網路的公司裝置時，Azure Active Directory 無縫單一登入 (Azure AD 無縫 SSO) 就會自動將他們登入。 開啟此功能後，使用者不需要輸入密碼就能登入 Azure AD，而且在大部分情況下，甚至也不用輸入其使用者名稱。 這項功能可讓使用者輕鬆存取雲端式應用程式，而不需要任何額外的內部部署元件。|
|[存取面板](https://aka.ms/AccessPanelDPDownload)|為使用者提供可探索及存取其所有應用程式的簡單中樞。 讓他們能藉由自助服務功能來提升生產力，例如能夠要求存取新的應用程式和群組，或代表其他人管理這些資源的存取權。|
|[以 ADFS 進行密碼雜湊同步處理](https://aka.ms/ADFSTOPHSDPDownload)|使用密碼雜湊同步處理時，使用者密碼的雜湊會從內部部署 Active Directory 同步至 Azure AD，讓 Azure AD 來驗證使用者，而不需要與內部部署 Active Directory 互動|
|[以 ADFS 通過驗證](https://aka.ms/ADFSTOPTADPDownload)|Azure AD 傳遞驗證可協助使用者以相同密碼同時登入內部部署和雲端式應用程式。 這項功能可讓您的使用者獲得更好的體驗，不僅少了一個要記住的密碼，還會因為使用者不太可能忘了如何登入而降低 IT 技術服務人員成本。 當使用者使用 Azure AD 登入時，此功能會向您的內部部署 Active Directory 直接驗證使用者的密碼。|
|[Azure AD 應用程式 Proxy](https://aka.ms/AppProxyDPDownload)|現今的員工想要隨時隨地都能在任何裝置發揮生產力。 他們想要在自己的裝置上工作，不論這些裝置是平板電腦、手機或膝上型電腦。 而且員工期望能夠存取其所有的應用程式︰雲端中的 SaaS 應用程式以及內部部署的公司應用程式。 傳統上，提供內部部署應用程式的存取權會涉及虛擬私人網路 (VPN) 或周邊網路 (DMZ)。 這些解決方案不僅複雜且難以確保安全，而且設定及管理成本也很高。 還有更好的辦法！ - Azure AD 應用程式 Proxy|
|[使用者佈建](https://aka.ms/UserProvisioningDPDownload)|Azure AD 可協助您自動化在雲端 (SaaS) 應用程式 (例如 Dropbox、Salesforce、ServiceNow 等等) 中建立、維護和移除使用者身分識別的作業。|
|[Workday 導向的輸入使用者佈建](https://aka.ms/WorkdayDeploymentPlan)|對 Active Directory 進行 Workday 導向的輸入使用者佈建，可為進行中的身分識別治理建立基底，並為依賴權威性身分識別資料的商務程序加強品質。 您可以使用這項功能來設定規則，將「新進-調職-離職」程序 (例如新僱用、終止雇用、調職) 對應至 IT 佈建動作 (例如建立、啟用、停用、刪除帳戶)，以無縫地管理員工和約聘人員的身分識別週期。|
