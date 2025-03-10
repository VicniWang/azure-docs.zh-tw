---
title: 針對 Azure Active Directory B2B 共同作業問題進行疑難排解 | Microsoft Docs
description: Azure Active Directory B2B 共同作業常見問題的補救方式
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: f69bad355bf5644988416958c4b4f32ed66714d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186398"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>針對 Azure Active Directory B2B 共同作業問題進行疑難排解

以下是 Azure Active Directory (Azure AD) B2B 共同作業常見問題的補救方式。


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>我已新增外部使用者，但在我的全域通訊錄或人員選擇器中未看到他們

若清單中未顯示外部使用者，該物件可能需要一些時間才能複寫。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B 來賓使用者不會顯示在 SharePoint Online/OneDrive 人員選擇器中 
 
在 SharePoint Online (SPO) 人員選擇器中搜尋現有來賓使用者的功能預設為關閉，以符合舊版的行為。

您可以使用租用戶和網站集合層級中的 'ShowPeoplePickerSuggestionsForGuestUsers' 設定來啟用此功能。 您可以使用 Set-SPOTenant 和 Set-SPOSite Cmdlet 來設定此功能，讓成員搜尋目錄中所有的現有來賓使用者。 租用戶範圍中的變更不會影響已佈建的 SPO 網站。

## <a name="invitations-have-been-disabled-for-directory"></a>已針對目錄停用邀請

若收到通知指出您沒有邀請使用者的權限，請在 [使用者設定] 下，確認您的使用者帳戶已獲邀請外部使用者的授權：

![](media/troubleshoot/external-user-settings.png)

若您最近曾修改這些設定或已獲指派使用者的「來賓邀請者」角色，變更可能需要 15-60 分鐘才會生效。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>我邀請的使用者在兌換期間遇到錯誤

常見錯誤包括：

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>受邀者的管理員不允許在其租用戶中建立 EmailVerified 使用者

當邀請使用者的組織使用 Azure Active Directory，但指定使用者的帳戶不存在 (例如，使用者不存在於 Azure AD contoso.com 中)。 contoso.com 的系統管理員可能已建立防止建立使用者的原則。 使用者必須向管理員確認，以判斷是否允許外部使用者。 外部使用者的管理員需要允許其網域中存在已驗證的電子郵件使用者 (請參閱[本文](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)，了解允許已驗證的電子郵件使用者)。

![](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部使用者不存在於同盟網域中

如果您使用同盟驗證，而使用者目前不在 Azure Active Directory 中，則無法邀請使用者。

若要解決此問題，外部使用者的系統管理員必須將該使用者的帳戶同步到 Azure Active Directory。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>‘\#’ (通常是無效的字元) 如何與 Azure AD 同步？

因為受邀帳戶 user@contoso.com 會成為 user_contoso.com#EXT#@fabrikam.onmicrosoft.com，所以 “\#” 是 Azure AD B2B 共同作業或外部使用者之 UPN 中的保留字元。 因此，來自內部部署之 UPN 中的 \# 不可登入 Azure 入口網站。 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>我在將外部使用者新增到已同步的群組時遇到錯誤

您只能將外部使用者新增到「已指派」或「安全性」群組，而無法指派到主控內部部署群組。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>我的外部使用者未收到兌換電子郵件

受邀者應該連絡其 ISP 或檢查其垃圾郵件篩選設定，以確定下列地址在允許清單中：Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>我注意到自訂訊息有時候不會包含在邀請訊息中

為了符合隱私權法律，在下列情況下，我們的 API 不會在電子郵件邀請中包含自訂訊息：

- 邀請者在提出邀請的租用戶中沒有電子郵件地址
- 應用程式服務主體傳送邀請時

如果此案例對您很重要，您可以隱藏我們的 API 邀請電子郵件，然後透過您選擇的電子郵件機制傳送邀請電子郵件。 請諮詢貴組織的法律顧問，以確定透過此方式傳送的任何電子郵件也符合隱私權法律。

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>具有 Just-In-Time (JIT) 或「病毒式」租用戶的來賓使用者無法重設其密碼

如果身分識別租用戶是 Just-In-Time (JIT) 或病毒式租用戶 (表示它是獨立、非受控的 Azure 租用戶)，只有來賓使用者才能重設自己的密碼。 組織有時會[接管病毒式租用戶的管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)，這些是員工使用其工作電子郵件地址來註冊服務時所建立的租用戶。 在組織接管病毒式租用戶之後，只有該組織的系統管理員可以重設使用者的密碼或啟用 SSPR。 如有必要，作為發出邀請的組織，您可以從目錄中移除來賓使用者帳戶，並重新傳送邀請。

## <a name="next-steps"></a>後續步驟

- [取得 B2B 共同作業的支援](get-support.md)
