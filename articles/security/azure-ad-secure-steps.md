---
title: 可在 Azure Active Directory 中保護身分識別基礎結構的五個步驟
description: 本文件概述系統管理員所應實作以便有助自身使用 Azure AD 功能來保護組織的重要動作清單
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: f0b76e54da60396e01b5893b143bcee9048e2184
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750319"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>可保護身分識別基礎結構的五個步驟

如果您正在閱讀本文件，想必您已了解安全性有多重要。 您可能已承擔起保護組織的重任。 如果您需要說服他人，讓其了解安全性有多重要，請讓其閱讀最新的 [安全情報報告](https://www.microsoft.com/security/intelligence-report)。

本文件會協助您使用 Azure Active Directory 的功能，透過有五個步驟的檢查清單讓組織預防網路攻擊來獲得更安全的狀態。

這份檢查清單會藉由說明如何執行下列作業，協助您快速部署重要的建議動作來立即保護組織：

* 強化認證。
* 減少受攻擊面。
* 將威脅回應自動化。
* 增加對稽核和監視的認識。
* 透過自助服務實現更具預測性且更完整的使用者安全性。

> [!NOTE]
> 這份文件中有諸多建議，僅對設定為以 Azure Active Directory 作為其識別提供者的應用程式有效。 為應用程式設定單一登入，可確保應用程式會享有認證原則、威脅偵測、稽核、記錄，以及其他已新增至這些應用程式的功能。 [透過 Azure Active Directory 的單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso)是實現這些建議的基礎。

本文件中的建議事項是與[身分識別安全分數](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)相互搭配，其能針對您 Azure AD 租用戶的身分識別安全設定進行自動化評估。 組織可以使用 Azure AD 入口網站中的 [身分識別安全分數] 頁面，來尋找其目前安全性設定中的缺口，以確實遵循最新的 Microsoft 安全性最佳做法。 實作 [安全分數] 頁面中的每個建議，將能提升您的分數並允許您追蹤自己的進度，並能協助您與業界其他類似規模的組織比較彼此的實作情況。

![身分識別安全分數](media/azure-ad/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>開始之前：使用 MFA 保護特殊權限帳戶

在開始進行此檢查清單之前，請確定您不會在閱讀此檢查清單時遭到入侵。 您必須先保護特殊權限帳戶。

攻擊者若掌控了特殊權限帳戶，將會造成極大的破壞，因此請務必先保護這些帳戶。 對組織中使用[基準保護](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection)的所有系統管理員，啟用並要求 [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA)。 如果您尚未實作 MFA，請立即實作！ 這一點非常重要。

全都準備好了嗎？ 那就開始進行此檢查清單吧。

## <a name="step-1---strengthen-your-credentials"></a>步驟 1 - 強化認證 

企業的安全性缺口，大多是因為帳戶遭到少數幾種入侵方法 (例如，密碼噴灑、入侵重播或網路釣魚) 入侵。 若要深入了解這些攻擊，請觀看這段影片 (45 分鐘)：
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>確定組織使用的是強式驗證

有鑑於入侵者經常會猜測、誘騙或以惡意程式碼竊取密碼，使用者也經常會重複使用密碼，所以請務必使用某種形式的強式認證來支援密碼 - 詳情請參閱 [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)。

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>開始禁用常常受到攻擊的密碼，並關閉傳統的複雜性規則及到期規則。

有許多組織都是使用傳統的複雜性規則 (要求特殊字元、數字、大寫及小寫) 和密碼到期規則。 [Microsoft 的研究](https://aka.ms/passwordguidance) \(英文\) 顯示，這些原則會導致使用者選擇較容易猜到的密碼。

Azure AD 的[動態禁用密碼](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)功能會使用目前的攻擊者行為來防止使用者設定可輕易猜到的密碼。 此功能先前在雲端中建立使用者時便已持續開啟，但現在也可供混合式組織在部署[適用於 Windows Server Active Directory 的 Azure AD 密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)時使用。 Azure AD 密碼保護會阻止使用者選擇這些常見密碼，並可延伸以封鎖包含您所指定之自訂關鍵字的密碼。 例如，您可以避免使用者選擇包含您公司產品名稱或當地球隊的密碼。

Microsoft 建議採用下列根據 [NIST 指導方針](https://pages.nist.gov/800-63-3/sp800-63b.html) \(英文\) 的新式密碼原則：

1. 要求密碼至少要有 8 個字元。 密碼並非越長越好，密碼越長會導致使用者選擇可預測的密碼、將密碼儲存在檔案中或寫下來。
2. 停用到期規則，因為到期規則會讓使用者想要使用容易猜測的密碼，例如 **Summer2018!**
3. 停用字元組合規定，防止使用者選擇常常受到攻擊的密碼，原因是這些規定會導致使用者在密碼中選擇可預測的替代字元。

如果您是直接在 Azure AD 中建立身分識別，則可以使用 [PowerShell 來防止使用者的密碼到期](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)。 混合式組織應使用[網域群組原則設定](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) \(英文\) 或 [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy) \(英文\) 來實作這些原則。

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>防止認證外洩並新增遇到中斷時的復原能力

如果您的組織使用搭配傳遞驗證或同盟的混合式身分識別解決方案，則基於下列兩個原因，您應該啟用密碼雜湊同步處理：

* Azure AD 管理中的[認證外洩的使用者](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)報告會就「暗網 (Dark Web)」上已公開的使用者名稱和密碼組向您發出警告。 數量驚人的密碼是透過網路釣魚、惡意程式碼，以及在第三方網站上重複使用密碼並於之後遭到入侵而外洩的。 Microsoft 已發現許多這樣的外洩認證，並會在此報告中告訴您它們是否與您組織的認證相符，但前提是您必須[啟用密碼雜湊同步處理](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)！
* 發生內部部署中斷 (例如遭遇勒索軟體攻擊) 時，您將能切換成使用[採用密碼雜湊同步處理的雲端驗證](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)。此備用驗證方法可讓您繼續存取已設定為使用 Azure Active Directory 進行驗證的應用程式，包括 Office 365。 在此情況下，在解決內部部署中斷之前，IT 員工將不需要被迫使用個人電子郵件帳戶來共用資料。

深入了解[密碼雜湊同步處理](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)的運作方式。

> [!NOTE]
> 如果您啟用密碼雜湊同步處理，並且要使用 Azure AD Domain Services，則 Kerberos (AES 256) 雜湊以及選擇性地 NTLM (RC4，沒有 salt) 雜湊也會加密並同步處理至 Azure AD。 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>實作 AD FS 外部網路智慧鎖定

將應用程式設定為直接向 Azure AD 進行驗證的組織，可受益於 [Azure AD 智慧鎖定](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)。 如果您是在 Windows Server 2012R2 中使用 AD FS，請實作 AD FS [外部網路鎖定保護](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) \(英文\)。 如果您是在 Windows Server 2016 上使用 AD FS，請實作[外部網路智慧鎖定](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016) \(機器翻譯\)。 AD FS 智慧外部網路鎖定可防範以 AD FS 目標的暴力密碼破解攻擊，並防止系統在 Active Directory 中鎖定使用者。

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>利用真正安全、更容易使用的認證

使用 [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)，即可在 PC 和行動裝置上將密碼改為強式雙因素驗證。 這個驗證包含與裝置安全繫結的新型使用者認證，且會使用生物特徵辨識或 PIN 碼。

## <a name="step-2---reduce-your-attack-surface"></a>步驟 2 - 減少受攻擊面

由於密碼入侵問題無所不在，所以盡量減少組織的受攻擊面就相當重要。 不要使用老舊且不安全的通訊協定、限制存取進入點，以及更加嚴格地控制資源的系統管理存取權，可協助減少受攻擊面。

### <a name="block-legacy-authentication"></a>封鎖舊式驗證

使用自有舊式方法來向 Azure AD 進行驗證和存取公司資料的應用程式，會對組織造成其他風險。 使用舊式驗證的應用程式範例包括 POP3、IMAP4 或 SMTP 用戶端。 舊式驗證應用程式會代表使用者進行驗證，而讓 Azure AD 無法進行進階的安全性評估。 作為替代的新式驗證可支援多重要素驗證和條件式存取，所以會減少安全性風險。 建議您進行下列三個動作：

1. 封鎖[舊式驗證 (如果您使用 AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)。
2. 將 [SharePoint Online 和 Exchange Online 設定為使用新式驗證](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)。
3. 使用[條件式存取原則來封鎖舊式驗證](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions)。

### <a name="block-invalid-authentication-entry-points"></a>封鎖無效的驗證進入點

使用假想入侵心態，應該就能在使用者認證遭到入侵時降低其影響。 對於環境中的每個應用程式，請考慮有效的使用案例：哪些群組、哪些網路、哪些裝置和其他元素有獲得授權，然後將其餘項目封鎖。 透過 [Azure AD 條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)，即可根據所定義的特定條件，控制獲得授權的使用者要如何存取其應用程式和資源。

### <a name="block-end-user-consent"></a>封鎖使用者同意

根據預設，系統會允許 Azure AD 中的所有使用者向採用 OAuth 2.0 及 Microsoft 身分識別[同意架構](https://docs.microsoft.com/azure/active-directory/develop/consent-framework)的應用程式授與公司資料的存取權限。 雖然同意功能確實可讓使用者輕鬆取得能與 Microsoft 365 和 Azure 整合的有用應用程式，如果沒有小心使用及監視，此作法可能會帶來風險。 [停用未來所有的使用者同意作業](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access)可協助減少介面區並降低此風險。 停用使用者同意功能時，先前所授與的同意仍然會有效，但未來所有的同意作業都必須由系統管理員執行。 在停用此功能之前，建議您先確定使用者已了解針對新應用程式要求系統管理員同意的方式；這麼做應該能協助減少使用者摩擦、將支援量降至最低，並確保使用者不會使用非 Azure AD 認證來註冊應用程式。

### <a name="implement-azure-ad-privileged-identity-management"></a>實作 Azure AD Privileged Identity Management

「假想入侵」的另一個影響是需要將遭到入侵的帳戶可使用特殊權限角色進行操作的可能性降到最低。 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) 可協助您進行下列作業，讓您將帳戶特殊權限最小化：

* 識別和管理指派給系統管理角色的使用者。
* 了解您應該移除的未用或多餘特殊權限角色。
* 建立規則以確定特殊權限角色會受到多重要素驗證保護。
* 建立規則以確定所授與的特殊權限角色有效時間僅足以完成特殊權限工作。

啟用 Azure AD PIM，然後檢視已獲派系統管理角色的使用者，並將這些角色中的不必要帳戶移除。 對於其餘特殊權限使用者，則將它們從永久性角色改為合適角色。 最後，請建立適當原則，以確定當使用者需要存取那些特殊權限角色時，可以搭配必要的變更控制安全地進行。

在部署特殊權限帳戶程序時，請遵循[最佳做法建立至少兩個緊急帳戶](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices)，以確定您自己在遭到鎖定時，仍可存取 Azure AD。

## <a name="step-3---automate-threat-response"></a>步驟 3 - 將威脅回應自動化

Azure Active Directory 有許多會自動攔截攻擊的功能，可讓偵測與回應之間沒有延遲。 若能減少罪犯可供用來將自身埋藏到您環境的時間，您就能降低成本和風險。 您可以採取的具體步驟如下。

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>使用 Azure AD Identity Protection 來實作使用者風險安全性原則

使用者風險可指出使用者的身分識別已遭到入侵的可能性，並會根據與使用者身分識別相關聯的[使用者風險事件](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)來算出。 使用者風險原則是條件式存取原則，可評估特定使用者或群組的風險層級。 根據低、中、高風險層級，原則可以設定為封鎖存取，也可以設定為必須使用多重要素驗證來進行安全密碼變更。 Microsoft 會建議您要求高風險使用者必須進行安全密碼變更。

![標示有風險的使用者](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>使用 Azure AD Identity Protection 來實作登入風險原則

登入風險是指帳戶擁有者以外人士嘗試使用身分識別來登入的可能性。 [登入風險原則](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)是條件式存取原則，可評估特定使用者或群組的風險層級。 根據風險層級 (高/中/低)，原則可以設定為封鎖存取，也可以設定為強制使用多重要素驗證。 請確定您有對中等以上風險的登入，強制使用多重要素驗證。

![從匿名 IP 登入](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>步驟 4 - 增加認識

安全性相關事件的稽核和記錄及相關警示是有效保護策略的重要元件。 安全性記錄和報告會提供可疑活動的電子記錄，並協助您偵測可能指出從外部嘗試或成功滲透網路以及內部攻擊的模式。 您可以使用稽核來監視使用者活動、文件合規性、執行鑑識分析等等。 警示會提供安全性事件通知。

### <a name="monitor-azure-ad"></a>監視 Azure AD

Microsoft Azure 服務和功能提供可設定的安全性稽核和記錄選項，以協助您識別安全性原則和機制間的差距，並解決這些差距以協助防止破壞。 您可以使用 [Azure 記錄與稽核](https://docs.microsoft.com/azure/security/azure-log-audit)以及使用 [Azure Active Directory 入口網站中的稽核活動報告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)。

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>在混合式環境中監視 Azure AD Connect Health

[使用 Azure AD Connect Health 監視 AD FS](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) 可針對您的 AD FS 基礎結構，在潛在問題及攻擊的可見性上，為您提供更加深入的見解。 Azure AD Connect Health 會提供警示與詳細資料、解決步驟，以及相關文件的連結；提供數個驗證流量相關計量的使用情況分析；提供效能監控和報告。

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>監視 Azure AD Identity Protection 事件

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 是一種通知、監視和報告工具，可用來偵測會影響組織身分識別的潛在弱點。 它會偵測風險事件，例如外洩的認證、不可能的行進路線、源自受感染裝置的登入、匿名 IP 位址、與可疑活動相關聯的 IP 位址，以及不明的位置。 請啟用通知警示，以接收有風險使用者的電子郵件和/或每週摘要電子郵件。

Azure AD Identity Protection 能提供兩個您應該每天監視的重要報告：
1. 具風險的登入報告將能顯示您應該調查的使用者登入活動，因為執行該登入的可能不是帳戶的合法使用者。
2. 具風險的使用者報告將能顯示可能已被入侵的使用者帳戶，例如偵測到外洩的認證，或是當使用者從兩個不可能在該時間範圍內於它們之間移動的不同位置登入時。 

![標示有風險的使用者](media/azure-ad/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>稽核應用程式和已同意的權限

使用者可能會被誘騙而瀏覽至被入侵的網站或應用程式，並使它們取得其設定檔資訊和使用者資料 (例如其電子郵件)。 惡意的執行者可以使用其所接收到的已同意權限來對使用者的信箱內容進行加密，並要求使用者支付贖金以重新存取其信箱資料。 [系統管理員應該檢閱並稽核](https://blogs.technet.microsoft.com/office365security/defending-against-illicit-consent-grants/) \(英文\) 使用者所提供的權限。

## <a name="step-5---enable-end-user-self-help"></a>步驟 5 - 啟用使用者自助服務

請盡可能地在安全性與生產力之間取得平衡。 和運用打下長遠安全性基礎的心態來著手處理旅途的思路相同，您可以藉由既讓使用者獲得能力同時又保持警覺的方式，去除組織中的分歧意見。 

### <a name="implement-self-service-password-reset"></a>實作自助密碼重設

Azure 的[自助密碼重設 (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) 提供簡單的方法，讓 IT 系統管理員允許使用者重設或解除鎖定其密碼或帳戶，而不必系統管理員介入處理。 系統包含詳細的報告，可追蹤使用者何時存取系統與通知來警示您誤用或濫用。 

### <a name="implement-self-service-group-management"></a>實作自助群組管理

Azure AD 可讓您使用安全性群組和 Office 365 群組來管理資源的存取權。 這些群組可由群組擁有者 (而不是 IT 系統管理員) 負責管理。 這項功能又稱為[自助群組管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)，可讓未獲得系統管理角色的群組擁有者建立和管理群組，而不需要依賴系統管理員來處理其要求。

### <a name="implement-azure-ad-access-reviews"></a>實作 Azure AD 存取權檢閱

透過 [Azure AD 存取權檢閱](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)功能，您可以管理群組成員資格、企業應用程式的存取權和特殊權限角色指派，以確保能維持安全性標準，不讓使用者在不需要時獲得更長時間的存取權。

## <a name="summary"></a>總結

安全的身分識別基礎結構涉及許多層面，但這份有五個步驟的檢查清單會協助您快速完成更安全可靠的身分識別基礎結構：

* 強化認證。
* 減少受攻擊面。
* 將威脅回應自動化。
* 增加對稽核和監視的認識。
* 透過自助服務實現更具預測性且更完整的使用者安全性。

我們非常感謝您如此嚴肅地看待身分識別安全性，希望這份文件有助於讓貴組織達成更安全的狀態。

## <a name="next-steps"></a>後續步驟
如果您需要協助來規劃和部署各項建議，請參閱 [Azure AD 專案部署方案](https://aka.ms/deploymentplans)中的說明。
