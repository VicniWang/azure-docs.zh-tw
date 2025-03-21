---
title: 使用 Microsoft Authenticator 應用程式進行無密碼的 Azure AD 登入 (預覽)
description: 使用 Microsoft Authenticator 應用程式而非使用您的密碼登入 Azure AD (公開預覽)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 247e772873cf467b371b4aea45f8ceb43bce16e3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180517"
---
# <a name="password-less-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>使用 Microsoft Authenticator 應用程式進行無密碼的手機登入 (公開預覽)

Microsoft Authenticator 應用程式可用來登入任何 Azure AD 帳戶 (而不需使用密碼)。 類似於 [Windows Hello 企業版](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 的技術，Microsoft Authenticator 會使用金鑰型驗證來啟用已繫結至裝置並使用生物特徵識別或 PIN 的使用者認證。

![要求使用者在其 Microsoft Authenticator 應用程式中核准登入嘗試的瀏覽器登入範例](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

已在 Microsoft Authenticator 應用程式中啟用手機登入的人員會看到一則訊息，告訴他們點選其應用程式中的數字，而不會在輸入使用者名稱之後看到輸入密碼的提示。 在應用程式中，使用者必須符合數目，選擇 [核准]，然後提供他們的 PIN 或生物特徵辨識，然後驗證將會完成。

## <a name="enable-my-users"></a>啟用我的使用者

對於公開預覽版，系統管理員必須先透過 powershell 將原則，以允許在租用戶中使用認證。 在執行此步驟之前，請檢閱「已知問題」一節。

### <a name="tenant-prerequisites"></a>租用戶的必要條件

* Azure Active Directory
* 針對 Azure Multi-Factor Authentication 啟用的使用者
* 使用者可以註冊其裝置

### <a name="steps-to-enable"></a>啟用的步驟

1. 確保您有 Azure Active Directory V2 PowerShell 模組的最新公開預覽版本。 您可藉由執行下列命令，解除安裝後並重新安裝來確認這點：
    ```powershell
    Uninstall-Module -Name AzureADPreview
    Install-Module -Name AzureADPreview
    ```

2. 向 Azure AD 租用戶進行驗證，以使用 Azure AD V2 PowerShell 模組。 使用的帳戶必須是安全性管理員或全域管理員。
    ```powershell
    Connect-AzureAD
    ```

3. 建立 Authenticator 登入原則：
    ```powershell
    New-AzureADPolicy -Type AuthenticatorAppSignInPolicy -Definition '{"AuthenticatorAppSignInPolicy":{"Enabled":true}}' -isOrganizationDefault $true -DisplayName AuthenticatorAppSignIn
    ```

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>我的使用者如何啟用手機登入？

針對公開預覽版，沒有任何方式可強制使用者建立或使用這個新認證。 只有在系統管理員已啟用使用者的租用戶，而且使用者已更新其 Microsoft Authenticator 應用程式來啟用手機登入之後，使用者才會遇到無密碼登入的情況。

> [!NOTE]
> 此功能自 2017 年 3 月起納入應用程式，因此有可能針對租用戶啟用此原則時，使用者可能會立即遇到此流程。 請留意這項變更，並且讓使用者做好準備。
>

1. 在 Azure Multi-Factor Authentication 中註冊
1. 在執行 iOS 8.0 或更新版本或 Android 6.0 或更新版本的裝置上安裝的最新版 Microsoft Authenticator。
1. 推播通知已新增至應用程式的工作或學校帳戶。 在 [https://aka.ms/authappstart](https://aka.ms/authappstart) 可以找到使用者文件。

使用者在 Microsoft Authenticator 應用程式中設定具有推播通知的 MFA 帳戶之後，他們可以依照[使用您的手機而非密碼登入](../user-help/microsoft-authenticator-app-phone-signin-faq.md)一文中的步驟來完成電話登入註冊。

## <a name="known-issues"></a>已知問題

### <a name="ad-fs-integration"></a>AD FS 整合

當使用者已啟用 Microsoft Authenticator 無密碼認證時，該使用者的驗證將一律預設為傳送通知以供核准。 此邏輯可防止混合式租用戶中的使用者導向至 ADFS 進行登入驗證，而不需使用者採取額外的步驟來按一下 [改用您的密碼]。 此程序也會略過任何內部部署條件式存取原則和傳遞驗證流程。 此程序的例外狀況是，如果已指定 login_hint，系統就會自動將使用者轉送至 AD FS，而略過使用無密碼認證的選項。

### <a name="azure-mfa-server"></a>Azure MFA Server

使用者如果能夠透過組織內部部署 Azure MFA Server 進行 MFA，便仍然可以建立及使用單一無密碼的手機登入認證。 如果使用者嘗試使用認證升級 Microsoft Authenticator 的多個安裝 (5+)，這項變更可能會導致錯誤。  

### <a name="device-registration"></a>裝置註冊

建立這個新的強式認證的其中一個必要條件，就是它所在的裝置已在 Azure AD 租用戶內對個別使用者註冊。 由於裝置註冊限制，一個裝置只能在單一租用戶中註冊。 此限制表示，Microsoft Authenticator 應用程式中只有一個工作或學校帳戶可以啟用手機登入。

## <a name="next-steps"></a>後續步驟

[深入了解裝置註冊](../devices/overview.md#getting-devices-under-the-control-of-azure-ad)

[深入了解 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
