---
title: LinkedIn 整合資料共用與同意 - Azure Active Directory | Microsoft Docs
description: 說明 LinkedIn 整合如何在 Azure Active Directory 中透過 Microsoft 應用程式共用資料
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e96c7da9ac53927ed48003370f40f5861f48d3d6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204760"
---
# <a name="linkedin-integration-data-sharing-and-consent"></a>LinkedIn 整合資料共用與同意

身為 Azure Active Directory (Azure AD) 管理員，您可以讓您組織中的使用者同意將其 Microsoft 公司或學校帳戶連接到其 LinkedIn 帳戶。 使用者連接帳戶時，某些 Microsoft 應用程式和服務會提供 LinkedIn 的資訊和重點摘要。 使用者也可以預期本身使用 LinkedIn 的網路體驗會得到改善，也會獲得更多 Microsoft 的資訊。

若要查看 Microsoft 應用程式和服務中的 LinkedIn 資訊，使用者必須同意連接自己的 Microsoft 與 LinkedIn 帳戶。 使用者首次按一下來查看某人在 Outlook、OneDrive 或 SharePoint Online 的個人檔案卡片上顯示的 LinkedIn 資訊時，系統會提示使用者連接其帳戶。 LinkedIn 帳戶連線不會向您的使用者完全啟用，必須等到使用者同意接受此體驗並連接自己帳戶。

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>共用 LinkedIn 資訊的優點

可存取 Microsoft 應用程式和服務中的 LinkedIn 資訊，使用者就更容易與組織內外的同事、客戶和合作夥伴溝通互動，並建立起專業的人際關係。 新的使用者可以加快與同事建立聯繫的速度，更加深入了解同事，並輕鬆獲得更多資訊。 下列範例顯示了 LinkedIn 資訊在 Microsoft 應用程式中的個人檔案卡片上的顯示方式：

![啟用 LinkedIn 整合](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>啟用並宣佈 LinkedIn 整合

您必須是 Azure Active Directory 管理員，才能管理您組織的設定。 您可以啟用所有使用者的連線，也可以僅啟用特定一組使用者的設定。

1. 若要啟用或停用整合，請依照 [LinkedIn 整合](linkedin-integration.md)中的步驟進行操作。
2. 您在組織中宣佈整合 LinkedIn 時，請告知使用者參閱 [Microsoft 應用程式和服務中的 LinkedIn 資訊](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381) (英文) 的常見問題集。 本文會提供 LinkedIn 資訊的顯示位置、連接帳戶方法等資訊。

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>使用者同意 Microsoft 與 LinkedIn 的資料存取

從 LinkedIn 存取的資料不會永久儲存於 Microsoft 服務中。 從 Microsoft 存取的資料不會與 LinkedIn 一起永久儲存，但連絡人除外。 Microsoft 連絡人會儲存在 LinkedIn 上，直到使用者移除為止，如[管理匯入的聯絡人](https://www.linkedin.com/help/linkedin/answer/43377)一文所述。

使用者連接其帳戶時，某些 Microsoft 應用程式會提供 LinkedIn 的資訊和見解，例如個人檔案卡片。 使用者也可以預期本身使用 LinkedIn 的網路體驗會得到改善，也會獲得更多 Microsoft 的資訊。
當貴組織中的使用者連線其 LinkedIn 和 Microsoft 公司或學校帳戶時，有兩個選項可用：

* 授予從這兩個帳戶存取資料的權限。 這表示他們授予 Microsoft 帳戶或公司帳戶權限，因此可存取其 LinkedIn 帳戶的資料，而且[其 LinkedIn 帳戶可存取其 Microsoft 公司或學校帳戶的資料](https://www.linkedin.com/help/linkedin/answer/84077) (英文)。
* 授予僅允許其 Microsoft 工作和學校帳戶存取 LinkedIn 資料的權限。

使用者可隨時中斷帳戶的連線並移除資料存取權限，而且[使用者可以控制自有 LinkedIn 設定檔的檢視方式](https://www.linkedin.com/help/linkedin/answer/83) (英文)，包括是否可以在 Microsoft 應用程式中檢視其設定檔。

### <a name="linkedin-account-data"></a>LinkedIn 帳戶資料

您連接您的 Microsoft 和 LinkedIn 帳戶時，表示您允許 LinkedIn 向 Microsoft 提供下列資料：

* 設定檔資料，包括 LinkedIn 的身分識別、連絡資訊，以及您在 [LinkedIn 設定檔](https://www.linkedin.com/help/linkedin/answer/15493) (英文) 對其他人分享的資訊。
* 興趣資料，包含在 LinkedIn 上的興趣，例如您關注的人員和主題、課程群組，以及您喜歡並分享的內容。
* 訂用帳戶資料，包含 LinkedIn 應用程式和服務以及相關聯資料的訂閱。 
* 連線資料，包含您的[linkedin 人脈網路](https://www.linkedin.com/help/linkedin/answer/110) (英文)，包括個人檔案以及您的第 1 度連線連絡資訊。

從 LinkedIn 存取的資料不會永久儲存於 Microsoft 服務中。 如需 Microsoft 使用個人資料的詳細資訊，請參閱 [Microsoft 隱私權聲明](https://privacy.microsoft.com/privacystatement/)。

### <a name="microsoft-work-or-school-account-data"></a>Microsoft 工作或學校帳戶資料

您連接您的 Microsoft 和 LinkedIn 帳戶時，表示您允許 Microsoft 向 LinkedIn 提供下列資料：

* 個人檔案資料，包括您的名字、姓氏、個人檔案相片、電子郵件地址、管理員以及您管理的人員等等的資訊。
* 行事曆資料，包過您的行事曆中的會議、會議的時間、地點和出席者的連絡資訊。 行事曆資料中未包含的會議資訊，例如議程、內容或會議主題。
* 興趣資料，包含根據您使用 Microsoft 服務的情況而與您的帳戶有關的興趣，例如 Cortana 和 Bing for Business。
* 訂用帳戶資料，包括您的組織對 Microsoft 應用程式和服務 (例如 Office 365) 提供的訂用帳戶。
* 連絡人資料，包括 Outlook、Skype 和其他 Microsoft 帳戶服務中的連絡人清單，包括您經常通訊或共事的人本身的連絡資訊。 連絡人會由 LinkedIn 定期匯入、儲存和使用，以便建議連接、協助組織連絡人，並顯示連絡人相關的更新等等。

從 Microsoft 存取的資料不會與 LinkedIn 一起永久儲存，但連絡人除外。 Microsoft 連絡人會儲存在 LinkedIn 上，直到使用者移除為止。 深入了解[從 LinkedIn 刪除匯入的連絡人](https://www.linkedin.com/help/linkedin/answer/43377) (英文)。

如需 LinkedIn 使用個人資料的詳細資訊，請參閱 [LinkedIn 隱私權原則](https://www.linkedin.com/legal/privacy-policy) (英文)。 對於 LinkedIn 服務、資料傳輸和儲存，資料會從歐盟傳輸到北美洲，而且您的隱私權受到保護，如[歐盟資料轉送](https://www.linkedin.com/help/linkedin/answer/62533) (英文) 所述。

## <a name="next-steps"></a>後續步驟

* [Microsoft 應用程式中與您的公司或學校帳戶關聯的 LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077) (英文)
