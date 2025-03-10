---
title: 什麼是 Azure Active Directory Identity Protection (已改版)？ | Microsoft Docs
description: 什麼是 Azure Active Directory Identity Protection (已改版)？
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: e60804e6bdf37607a08b6c530fd900e0f481a45b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204503"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>什麼是 Azure Active Directory Identity Protection (已改版)？

Identity Protection 體驗已經過改版，可以更完善地保護組織的身分識別。 已改版的體驗可提供：

- 重新設計的管理員體驗主要著重於與您最為相關的風險 - 使用者風險和登入風險

- 功能強大的調查體驗可支援篩選、排序和智慧型下載

- 經過改善的使用者風險計算，可協助您將工作重心放在最有可能遭到入侵的使用者上

- 新的 API 支援可讓您以程式設計方式存取風險資料

- 簡化的管理員意見回應程序，可讓您為使用者提供即時的保護

- 受監督的新式機器學習，可提高風險評估的正確性



安全性是現今組織的首要考量。 大部分的安全性缺口，都出現在攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。 這些年來，攻擊者變得越來越擅於利用第三方缺口，以及使用複雜的網路釣魚攻擊。 即便攻擊者僅取得較低權限的使用者帳戶存取權，他們即可輕易透過橫向移動存取重要的公司資源。 

為了因應這些威脅，Azure AD Identity Protection 為您提供了下列能力： 

- 主動防止遭入侵的身分識別被濫用 

- 在偵測到可疑的活動時自動緩解風險 

- 調查有風險的使用者和登入以解決潛在弱點  

- 在使用者的風險達到指定的閾值時收到警示 

 

Azure AD Identity Protection 是 Azure Active Directory Premium P2 功能之一，可讓您設定適當原則，以在使用者的身分識別遭到入侵時，或是帳戶擁有者以外的其他人嘗試使用其身分識別登入時，自動予以回應。 除了 Azure AD 所提供的其他條件式存取控制以外，這些原則還可以自動封鎖存取或起始緩解動作，例如重設密碼或強制執行多重要素驗證。 此外，Identity Protection 也提供監視和報告功能，讓您更深入了解組織中的風險和可能遭受的危害。 



## <a name="risk-events"></a>風險事件

Azure AD Identity Protection 可偵測下列風險事件： 

 

| 風險事件類型 | 說明 | 偵測類型 |
| ---             | ---         | ---            |
| 非慣用登入位置 | 以使用者最近的登入為準，從非慣用的位置登入。 | 離線 |
| 匿名 IP 位址 | 從匿名 IP 位址登入 (例如：Tor 瀏覽器、Anonymizer VPN)。 | 即時 |
| 不熟悉的登入屬性 | 以給定使用者近期未出現過的屬性登入。 | 即時 |
| 已連結惡意程式碼的 IP 位址 | 從已連結惡意程式碼的 IP 位址登入 | 離線 |
| 認證外洩 | 此風險事件指出使用者的有效認證已外洩 | 離線 |





## <a name="types-of-risk"></a>風險類型 

Identity Protection 以兩種類型的風險為基礎：

- 登入風險

- 使用者風險

### <a name="sign-in-risk"></a>登入風險

登入風險表示指定的驗證要求未獲身分識別擁有者授權的機率。

登入風險有兩種評估方式： 

- **登入風險 (即時)** - 登入風險 (即時) 的評估基礎是在登入處理期間觸發的所有即時偵測。  

- **登入風險 (彙總)** -登入風險 (彙總) 則是登入的總風險。 此風險由機器學習模型計算，其考量包括：

    - 即時偵測 (如上所述)
    
    - 離線偵測 (在登入完成後引發) 
    
    - 登入的所有其他功能


### <a name="user-risk"></a>使用者風險

使用者風險表示指定的身分識別遭到入侵的機率。 

計算使用者風險時，會考量所有與使用者相關聯的風險：

- 所有具風險的登入
- 未連結至登入的所有風險事件
- 目前的使用者風險
- 目前已對使用者執行的任何風險補救或消除動作



## <a name="how-identity-protection-detects-risk"></a>Identity Protection 偵測風險的方式  

Azure AD 會使用機器學習來偵測異常與可疑的活動，所使用的包括在登入期間即時偵測到的訊號，以及與使用者及其登入活動有關的非即時訊號。 Identity Protection 會使用這項資料，在使用者每次進行驗證時計算即時登入風險，並判斷每個使用者的整體使用者風險層級。 Identity Protection 可讓您設定 Identity Protection 使用者風險和登入風險原則，以自動這些風險偵測採取相關動作。  

 

要了解 Identity Protection 偵測風險的方式，應掌握兩個重要概念：使用者風險和登入風險。 登入風險反映指定的驗證要求未獲身分識別擁有者授權的機率。 登入風險有兩種類型：即時風險和總風險。 在指定的登入嘗試 (例如從匿名 IP 位址登入) 發生時，會偵測即時登入風險。 總登入風險則會彙總已偵測到的即時登入風險，以及任何與使用者的登入相關聯的後續非即時風險事件 (例如不可能到達的位置)。 使用者風險反映惡意執行者入侵給定身分識別的整體可能性。 使用者風險包含給定使用者的所有風險活動，包括：

- 即時登入風險
- 後續登入風險
- 有風險的使用者偵測。   

 

 
 ![Flow](./media/overview-v2/01.png)
 

 

上圖概略說明 Identity Protection 風險偵測的基準流程，和對於任何給定登入的回應。  

 

 

 

## <a name="common-scenarios"></a>常見案例 

我們來看看 Contoso 員工 Sarah 的範例。 

1. Sarah 嘗試從 Tor 瀏覽器登入 Exchange Online。 在登入時，Azure AD 會偵測即時風險事件。 

2. Azure AD 偵測到 Sarah 從匿名 IP 位址登入，而觸發了中級登入風險。 

3. Sarah 收到了 MFA 提示，因為 Contoso 的 IT 管理員設定了 Identity Protection 登入風險條件式存取原則。 該原則要求中級或更高的登入風險必須進行 MFA。 

4. Sarah 通過了 MFA 提示並存取 Exchange Online，且 Sarah 的使用者風險層級並未變更。 

幕後發生了哪些情況？ 嘗試從 Tor 瀏覽器登入的舉動因匿名 IP 位址，而觸發了 Azure AD 中的即時登入風險。 Azure AD 在處理要求時套用了 Identity Protection 所設定的登入風險原則，因為 Sarah 的登入風險層級達到閾值 (中級)。 由於 Sarah 先前已註冊了 MFA，因此她能夠回應並通過 MFA 挑戰。 她能夠成功通過 MFA 挑戰，即已向 Azure AD 表明她可能是合法的身分識別擁有者，因此她的使用者風險層級並未提高。 


但如果 Sarah 不是嘗試登入的那個人呢？ 

1. 具有 Sarah 認證的惡意執行者嘗試從 Tor 瀏覽器登入 Sarah 的 Exchange Online 帳戶，因為他們試圖隱藏其 IP 位址。 

2. Azure AD 偵測到從匿名 IP 位址登入的嘗試，而觸發了即時登入風險。 

3. 惡意執行者收到了 MFA 提示，因為 Contoso 的 IT 管理員設定了 Identity Protection 登入風險條件式存取原則，要求在登入風險達到中級或更高時必須進行 MFA。 

4. 惡意執行者未通過 MFA 挑戰，而無法存取 Sarah 的 Exchange Online 帳戶。 

5. 失敗的 MFA 提示觸發了應記錄的風險事件，進而提高了 Sarah 日後登入時的使用者風險。 

在惡意執行者嘗試存取 Sarah 的帳戶之後，我們來看看在 Sarah 下次嘗試登入時會發生什麼情況。 

1. Sarah 嘗試從 Outlook 登入 Exchange Online。 在登入時，Azure AD 會偵測即時風險事件以及任何先前的使用者風險。 

2. Azure AD 不會偵測任何即時登入風險，但會偵測因過去在前述情況下出現的風險活動而產生的高使用者風險。  

3. Sarah 收到了密碼重設提示，因為 Contoso 的 IT 管理員設定了 Identity Protection 使用者風險原則，要求具有高風險的使用者登入必須進行變更密碼。 

4. 由於 Sarah 已註冊 SSPR 和 MFA，因此能成功重設其密碼。 

5. 藉由重設其密碼，Sarah 的認證不會再遭到入侵，且其身分識別也恢復到安全狀態。 

6. Sarah 先前的風險事件都已解決，而她的使用者風險層級也因為認證遭入侵的影響有所緩解而自動重設。 

## <a name="how-do-i-configure-identity-protection"></a>如何設定 Identity Protection？ 

若要開始使用 Identity Protection，請先設定使用者風險原則和登入風險原則。 這些原則設定完成並套用至測試群組後，您可以模擬風險事件，以了解 Identity Protection 在您的環境中將如何回應。 下列快速入門指南將逐步說明如何設定上述原則，並在您的環境中加以測試。 

 

Identity Protection 支援 Azure AD 中的 3 種角色，以平衡與您的部署有關的管理活動： 

| 角色 | 可以執行 | 無法執行 |
| --- | --- | --- |
| 全域管理員 | 完整存取 Identity Protection、將 Identity Protection 上架 | |
| 安全性系統管理員 | 完整存取 Identity Protection | 將 Identity Protection 上架、重設使用者密碼 |
| 安全性讀取者 | 唯讀存取 Identity Protection | 將 Identity Protection 上架、修改使用者、設定原則、重設密碼| 

如需詳細資訊，請參閱[在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)

 
## <a name="licensing"></a>授權

>[!NOTE]
> 在 Identity Protection 公開預覽 (已改版) 期間，只有 Azure AD Premium P2 客戶能夠存取有風險的使用者報告和有風險的登入報告。



| 功能 | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- |
| 使用者風險原則 | yes | 否 | 否 |
| 登入風險原則 | yes | 否 | 否 |
| 具風險使用者報告 | 完整存取 | 有限資訊 | 有限資訊 |
| 有風險的登入報告 | 完整存取 | 有限資訊 | 有限資訊 |
| MFA 註冊原則 | yes | 否 | 否 |







## <a name="next-steps"></a>後續步驟 

若要開始使用 Identity Protection，請參閱[設定登入風險原則](quickstart-sign-in-risk-policy.md)。 






