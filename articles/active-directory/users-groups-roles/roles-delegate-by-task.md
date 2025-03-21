---
title: 依管理員工作委派最低特殊權限角色 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中針對身分識別工作委派的角色
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ea25510596903e4020e8237bc5bd389c584ead9
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268425"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>在 Azure Active Directory 中依管理工作區分的系統管理員角色

在本文中，您可以找到藉由在 Azure Active Directory (Azure AD) 中指派最低特殊權限角色來限制使用者之系統管理員權限所需的資訊。 您將會發現依功能區域組織的系統管理員工作與執行每個工作所需的最低特殊權限角色，以及其他可執行工作的非全域系統管理員角色。

## <a name="application-proxy"></a>應用程式 Proxy

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
設定應用程式 Proxy 應用程式 | 應用程式管理員 | 
設定連接器群組屬性 | 應用程式管理員 | 
已針對所有使用者停用功能之後建立應用程式註冊 | 應用程式開發人員 | 雲端應用程式系統管理員、應用程式系統管理員
建立連接器群組 | 應用程式管理員 | 
刪除連接器群組 | 應用程式管理員 | 
停用應用程式 Proxy | 應用程式管理員 | 
下載連接器服務 | 應用程式管理員 | 
讀取所有設定 | 應用程式管理員 | 

## <a name="b2c"></a>B2C

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
建立 Azure AD B2C 目錄 | 所有非來賓使用者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
建立 B2C 應用程式 | 全域管理員 | 
建立企業應用程式 | 雲端應用程式系統管理員 | 應用程式系統管理員
建立、讀取、更新及刪除 B2C 原則 | 全域管理員 | 
建立、讀取、更新及刪除識別提供者 | 全域管理員 | 
建立、讀取、更新及刪除密碼重設使用者流程 | 全域管理員 | 
建立、讀取、更新及刪除設定檔編輯使用者流程 | 全域管理員 | 
建立、讀取、更新及刪除登入使用者流程 | 全域管理員 | 
建立、讀取、更新及刪除註冊使用者流程 |全域管理員 | 
建立、讀取、更新及刪除使用者屬性 | 全域管理員 | 
建立、讀取、更新及刪除使用者 | 全域系統管理員 ([請參閱文件](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
讀取所有設定 | 全域管理員 | 
讀取 B2C 稽核記錄 | 全域系統管理員 ([請參閱文件](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

## <a name="company-branding"></a>公司商標

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
設定公司商標 | 全域管理員 | 
讀取所有設定 | 目錄讀取器 | 預設使用者角色 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>公司屬性

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
設定公司屬性 | 全域管理員 | 

## <a name="connect"></a>連線

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
傳遞驗證 | 全域管理員 | 
讀取所有設定 | 全域管理員 | 
無縫單一登入 | 全域管理員 | 

## <a name="connect-health"></a>Connect Health

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
新增或刪除服務 | 擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
套用對同步處理錯誤的修正 | 參與者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 擁有者
設定通知 | 參與者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 擁有者
配置設定 | 擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
設定同步處理通知 | 參與者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 擁有者
讀取 ADFS 安全性報告 | 安全性讀取者 | 參與者、擁有者
讀取所有設定 | 讀者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 參與者、擁有者
讀取同步處理錯誤 | 讀者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 參與者、擁有者
讀取同步處理服務 | 讀者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 參與者、擁有者
檢視計量與警示 | 讀者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 參與者、擁有者
檢視計量與警示 | 讀者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 參與者、擁有者
檢視同步處理服務計量與警示 | 讀者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 參與者、擁有者


## <a name="custom-domain-names"></a>自訂網域名稱

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
管理網域 | 全域管理員 | 
讀取所有設定 | 目錄讀取器 | 預設使用者角色 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>網域服務

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
建立 Azure AD Domain Services 執行個體 | 全域管理員 | 
執行所有的 Azure AD Domain Services 工作 | Azure AD DC 系統管理員群組 ([請參閱文件](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-domain#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
讀取所有設定 | 包含 AD DS 服務之 Azure 訂用帳戶上的讀者 | 

## <a name="devices"></a>裝置

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
停用裝置 | 雲端裝置管理員 | 
啟用裝置 | 雲端裝置管理員 | 
讀取基本設定 | 預設使用者角色 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
讀取 BitLocker 金鑰 | 安全性讀取者 | 密碼管理員、安全性系統管理員

## <a name="enterprise-applications"></a>企業應用程式

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
同意任何委派的權限 | 雲端應用程式系統管理員 | 應用程式管理員
同意不包括 Microsoft Graph 或 Azure AD Graph 的應用程式權限 | 雲端應用程式系統管理員 | 應用程式管理員
同意對 Microsoft Graph 或 Azure AD Graph 的應用程式權限 | 全域管理員 | 
同意應用程式存取自己的資料 | 預設使用者角色 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
建立企業應用程式 | 雲端應用程式系統管理員 | 應用程式管理員
管理應用程式 Proxy | 應用程式管理員 | 
管理使用者設定 | 全域管理員 | 
讀取群組或應用程式的存取權檢閱 | 安全性讀取者 | 安全性系統管理員、使用者管理員
讀取所有設定 | 預設使用者角色 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
更新企業應用程式指派 | 企業應用程式擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 雲端應用程式系統管理員、應用程式系統管理員
更新企業應用程式擁有者 | 企業應用程式擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 雲端應用程式系統管理員、應用程式系統管理員
更新企業應用程式屬性 | 企業應用程式擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 雲端應用程式系統管理員、應用程式系統管理員
更新企業應用程式佈建 | 企業應用程式擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 雲端應用程式系統管理員、應用程式系統管理員
更新企業應用程式自助 | 企業應用程式擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 雲端應用程式系統管理員、應用程式系統管理員
更新單一登入屬性 | 企業應用程式擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 雲端應用程式系統管理員、應用程式系統管理員

## <a name="groups"></a>群組

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
指派授權 | 使用者帳戶管理員 | 
建立群組 | 使用者帳戶管理員 | 
建立、更新或刪除群組或應用程式的存取權檢閱 | 使用者帳戶管理員 | 
管理群組到期日 | 使用者帳戶管理員 | 
管理群組設定 | 全域管理員 | 
讀取所有設定 (隱藏的成員資格除外) | 目錄讀取器 | 預設使用者角色 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
讀取隱藏的成員資格 | 群組成員 | 群組擁有者、密碼管理員、Exchange 系統管理員、SharePoint 系統管理員、Teams 管理員、使用者帳戶管理員
讀取具有隱藏成員資格之群組的成員資格 | 服務台系統管理員 | 使用者帳戶管理員、Teams 管理員
撤銷授權 | 授權管理員 | 使用者帳戶管理員
更新群組成員資格 | 群組擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 使用者帳戶管理員
更新群組擁有者 | 群組擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 使用者帳戶管理員
更新群組屬性 | 群組擁有者 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 使用者帳戶管理員

## <a name="identity-protection"></a>身分識別保護

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
設定警示通知| 安全性系統管理員 | 
設定和啟用或停用 MFA 原則| 安全性系統管理員 | 
設定和啟用或停用登入風險原則| 安全性系統管理員 | 
設定和啟用或停用使用者風險原則 | 安全性系統管理員 | 
設定每週摘要 | 安全性系統管理員| 
解除所有風險事件 | 安全性系統管理員 | 
修正或關閉弱點 | 安全性系統管理員 | 
讀取所有設定 | 安全性讀取者 | 
讀取所有風險事件 | 安全性讀取者 | 
讀取弱點 | 安全性讀取者 | 

## <a name="licenses"></a>授權

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
指派授權 | 授權管理員 | 使用者帳戶管理員
讀取所有設定 | 目錄讀取器 | 預設使用者角色 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
撤銷授權 | 授權管理員 | 使用者帳戶管理員
試用或購買訂用帳戶 | 計費管理員 | 


## <a name="monitoring---audit-logs"></a>監視 - 稽核記錄

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
讀取稽核記錄 | 報表讀者 | 安全性讀取者、安全性系統管理員

## <a name="monitoring---sign-ins"></a>監視 - 登入

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
讀取登入記錄 | 報表讀者 | 安全性讀取者、安全性系統管理員

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
刪除選定使用者產生的所有現有應用程式密碼 | 全域管理員 | 
停用 MFA | 全域管理員 | 
啟用 MFA | 全域管理員 | 
管理 MFA 服務設定 | 全域管理員 | 
要求選定使用者再次提供連絡方法 | 驗證系統管理員 | 
在所有記住的裝置上還原多重要素驗證  | 驗證系統管理員 | 

## <a name="mfa-server"></a>MFA Server

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
封鎖/解除封鎖使用者 | 全域管理員 | 
設定帳戶鎖定 | 全域管理員 | 
設定快取規則 | 全域管理員 | 
設定詐騙警示 | 全域管理員
設定通知 | 全域管理員 | 
設定單次許可 | 全域管理員 | 
設定通話設定 | 全域管理員 | 
設定提供者 | 全域管理員 | 
設定伺服器設定 | 全域管理員 | 
讀取活動報表 | 全域管理員 | 
讀取所有設定 | 全域管理員 | 
讀取伺服器狀態 | 全域管理員 |  

## <a name="organizational-relationships"></a>組織關係

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
管理識別提供者 | 全域管理員 | 
管理設定 | 全域管理員 | 
管理使用規定 | 全域管理員 | 
讀取所有設定 | 全域管理員 | 

## <a name="password-reset"></a>密碼重設

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
設定驗證方法 | 全域管理員 | 
設定自訂 | 全域管理員 | 
設定通知 | 全域管理員 | 
設定內部部署整合 | 全域管理員 | 
設定密碼重設屬性 | 全域管理員 | 
設定註冊 | 全域管理員 | 
讀取所有設定 | 安全性系統管理員、使用者管理員 | 

## <a name="privileged-identity-management"></a>Privileged Identity Management

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
將使用者指派給角色 | 特殊權限角色管理員 | 
配置角色設定 | 特殊權限角色管理員 | 
檢視稽核活動 | 安全性讀取者 | 
檢視角色成員資格 | 安全性讀取者 | 

## <a name="roles-and-administrators"></a>角色和系統管理員

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
管理角色指派 | 特殊權限角色管理員 | 
讀取 Azure AD 角色的存取權檢閱  | 安全性讀取者 | 安全性系統管理員、特殊權限角色管理員
讀取所有設定 | 預設使用者角色 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>安全性 - 驗證方法

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
設定驗證方法 | 全域管理員 | 
讀取所有設定 | 全域管理員 | 

## <a name="security---conditional-access"></a>安全性 - 條件式存取

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
設定 MFA 信任的 IP 位址 | 條件式存取系統管理員 | 
建立自訂控制項 | 條件式存取系統管理員 | 安全性系統管理員
建立具名位置 | 條件式存取系統管理員 | 安全性系統管理員
建立原則 | 條件式存取系統管理員 | 安全性系統管理員
建立使用規定 | 條件式存取系統管理員 | 安全性系統管理員
建立 VPN 連線憑證 | 條件式存取系統管理員 | 安全性系統管理員
刪除傳統原則 | 條件式存取系統管理員 | 安全性系統管理員
刪除使用規定 | 條件式存取系統管理員 | 安全性系統管理員
刪除 VPN 連線憑證 | 條件式存取系統管理員 | 安全性系統管理員
停用傳統原則 | 條件式存取系統管理員 | 安全性系統管理員
管理自訂控制項 | 條件式存取系統管理員 | 安全性系統管理員
管理具名位置 | 條件式存取系統管理員 | 安全性系統管理員
管理使用規定 | 條件式存取系統管理員 | 安全性系統管理員
讀取所有設定 | 安全性讀取者 | 安全性系統管理員
讀取具名位置 | 安全性讀取者 | 條件式存取管理員、安全性系統管理員

## <a name="security---identity-security-score"></a>安全性 - 身分識別安全性分數

Task | 最低特殊權限角色 | 其他角色 | 
---- | --------------------- | ----------------
讀取所有設定 | 安全性讀取者 | 安全性系統管理員
讀取安全性分數 | 安全性讀取者 | 安全性系統管理員
更新事件狀態 | 安全性系統管理員 | 

## <a name="security---risky-sign-ins"></a>安全性 - 具風險的登入

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
讀取所有設定 | 安全性讀取者 | 
讀取具風險的登入 | 安全性讀取者 | 

## <a name="security---users-flagged-for-risk"></a>安全性 - 標幟為有風險的使用者

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
關閉所有事件 | 安全性系統管理員 | 
讀取所有設定 | 安全性讀取者 | 
讀取標幟為有風險的使用者 | 安全性讀取者 | 

## <a name="users"></a>使用者

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
將使用者新增至目錄角色 | 特殊權限角色管理員 | 
將使用者新增至群組 | 使用者帳戶管理員 | 
指派授權 | 授權管理員 | 使用者帳戶管理員
建立來賓使用者 | 來賓邀請者 | 使用者帳戶管理員
建立使用者 | 使用者帳戶管理員 | 
刪除使用者 | 使用者帳戶管理員 | 
使受限制管理員的重新整理權杖失效 (請參閱文件) | 使用者帳戶管理員 | 
使非管理員的重新整理權杖失效 (請參閱文件) | 密碼管理員 | 使用者帳戶管理員
使具特殊權限管理員的重新整理權杖失效 (請參閱文件) | 全域管理員 | 
讀取基本設定 | 預設使用者角色 ([請參閱文件](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
為受限制的管理員重設密碼 (請參閱文件) | 使用者帳戶管理員 | 
為非管理員重設密碼 (請參閱文件) | 密碼管理員 | 使用者帳戶管理員
為具特殊權限的管理員重設密碼 | 全域管理員 | 
撤銷授權 | 授權管理員 | 使用者帳戶管理員
更新使用者主體名稱以外的所有屬性 | 使用者帳戶管理員 | 
更新適用於受限制管理員的使用者主體名稱 (請參閱文件) | 使用者帳戶管理員 | 
更新具特殊權限管理員上的使用者主體名稱屬性 (請參閱文件) | 全域管理員 | 
更新使用者設定 | 全域管理員 | 


## <a name="support"></a>支援

Task | 最低特殊權限角色 | 其他角色
---- | --------------------- | ----------------
提交支援票證 | 服務管理員 | 應用程式系統管理員、計費管理員、雲端應用程式系統管理員、規範管理員、Dynamics 365 系統管理員、電腦分析管理員、Exchange 系統管理員、密碼管理員、資訊保護管理員、Intune 系統管理員、商務用 Skype 系統管理員、Power BI 系統管理員、具特殊權限的驗證管理員、SharePoint 系統管理員、Teams 通訊管理員、Teams 管理員、使用者管理員、工作場所分析管理員

## <a name="next-steps"></a>後續步驟

* [如何指派或移除 Azure AD 系統管理員角色](directory-manage-roles-portal.md)
* [Azure AD 系統管理員角色參考](directory-assign-admin-roles.md)
