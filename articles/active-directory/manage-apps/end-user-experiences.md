---
title: 應用程式的使用者體驗 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) 提供幾種可自訂的方式，來對您組織中的使用者部署應用程式。
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: celested
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2488cb085c3be68265a787bd062028598c9243b8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190019"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory 中的應用程式使用者體驗
Azure Active Directory (Azure AD) 提供幾種可自訂的方式，來對您組織中的使用者部署應用程式：

* Azure AD 存取面板
* Office 365 應用程式啟動程式
* 直接登入同盟應用程式
* 同盟、密碼或現有應用程式的深層連結

您選擇要在組織中部署哪一種方法由您自行決定。

## <a name="azure-ad-access-panel"></a>Azure AD 存取面板
位於 https://myapps.microsoft.com 的「存取面板」是一種 Web 入口網站，可讓 Azure Active Directory 中擁有組織帳戶的使用者檢視和啟動 Azure AD 系統管理員已授與他們存取權的雲端應用程式。 如果您是 [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/) 的使用者，也可以透過 [存取面板] 使用自助式群組管理功能。

![Azure AD 存取面板](media/what-is-single-sign-on/azure-ad-access-panel.png)

存取面板與 Azure 入口網站不同，使用者不需要具備 Azure 訂用帳戶或 Office 365 訂用帳戶。

如需有關 Azure AD 存取面板的詳細資訊，請參閱 [存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="office-365-application-launcher"></a>Office 365 應用程式啟動程式
針對已部署 Office 365 的組織，透過 Azure AD 指派給使用者的應用程式也會出現在位於 https://portal.office.com/myapps 的 Office 365 入口網站中。 這對於組織中的使用者來說，能夠啟動應用程式又不需要使用第二個入口網站，非常簡單而且方便，建議使用 Office 365 的組織採取這個應用程式啟動解決方案。

![](./media/what-is-single-sign-on/officeapphub.png)

如需有關 Office 365 應用程式啟動程式的詳細資訊，請參閱 [讓您的應用程式出現在 Office 365 應用程式啟動程式中](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)。

## <a name="direct-sign-on-to-federated-apps"></a>直接登入同盟應用程式
大部分支援 SAML 2.0、WS-同盟或 OpenID Connect 的同盟應用程式也支援使用者在應用程式啟動，然後再透過 Azure AD 的自動重新導向或按一下連結登入。 這稱為服務提供者起始的登入，Azure AD 應用程式資源庫中大部分的同盟應用程式都支援這個方式 (請參閱 Azure 入口網站中應用程式的單一登入設定精靈連結的文件來了解詳細資訊)。

![](./media/what-is-single-sign-on/workdaymobile.png)

## <a name="direct-sign-on-links"></a>直接登入連結
Azure AD 也支援對支援密碼單一登入、已連結的單一登入，以及任何形式的同盟單一登入的個別應用程式使用直接單一登入連結。

這些連結是特別撰寫的 URL，會透過 Azure AD 登入程序針對特定應用程式傳送給使用者，使用者不需要從 Azure AD 存取面板或 Office 365 啟動。 您可以在 Azure 入口網站 [Active Directory] 區段中任何預先整合之應用程式的 [儀表板] 索引標籤下，找到這些單一登入 URL，如以下螢幕擷取畫面所示。

![](./media/what-is-single-sign-on/deeplink.png)

您可以複製這些連結，然後貼到任何您想要提供選取應用程式登入連結的位置。 可以是在電子郵件中，或是任何您已設定使用者應用程式存取權的自訂網頁型入口網站中。 以下是 Azure AD 直接單一登入 Twitter 的 URL 範例：

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

類似於存取面板的組織特定 URL，您可以在 myapps.microsoft.com 網域之後加入其中一個作用中或經過驗證的網域做為您的目錄，進一步自訂這個 URL。 這樣可以確保使用者不需要先輸入其使用者識別碼，登入頁面就可以立即載入任何組織品牌：

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

當授權的使用者按一下其中一個應用程式特定連結時，他們會先看到其組織登入頁面 (假設他們尚未登入)，登入之後會重新導向至該應用程式而不會先停在存取面板。 如果使用者遺漏存取應用程式所需的先決條件，例如密碼單一登入瀏覽器延伸，則連結將會提示使用者安裝遺漏的延伸。 如果應用程式的單一登入組態有變更，連結 URL 也會維持不變。

這些連結使用與存取面板和 Office 365 相同的存取控制機制，只有在 Azure 入口網站中已指派給應用程式的使用者或群組能夠順利通過驗證。 不過，任何未經授權的使用者都會看到一個訊息，說明他們未獲得存取權，且會獲得一個載入存取面板的連結，以檢視可存取的應用程式。

## <a name="next-steps"></a>後續步驟
如需部署方案的相關資訊，請參閱 [Azure Active Directory 部署方案](../fundamentals/active-directory-deployment-plans.md)
