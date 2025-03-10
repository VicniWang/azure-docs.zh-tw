---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742337"
---
若要在您的應用程式上啟用更細緻的密碼重設，您可使用**密碼重設**使用者流程。 請注意，[這裡](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md)包含租用戶密碼重設的所有選項。 此使用者流程描述客戶在密碼重設期間將經歷的體驗，以及成功完成時，應用程式將收到的權杖內容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在 [管理] 底下，選取 [使用者流程]，並按一下 [+新增使用者流程]。

![選取新的使用者流程](media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

在 [建議] 索引標籤上，選取 [密碼重設]。

輸入使用者流程 [名稱]，以供您的應用程式參考。 例如，輸入 `SSPR`。

在 [識別提供者] 底下，勾選 [使用電子郵件地址重設密碼]。

![輸入名稱，並且以電子郵件地址作為識別提供者來選取重設密碼](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

在 [應用程式宣告] 底下，按一下 [顯示更多]，並選擇在成功進行密碼重設之後，您要在授權權杖中傳回給應用程式的宣告。 例如，選取 [使用者的物件識別碼]。

按一下 [確定]。

![選取某些應用程式宣告，然後按一下 [確定] 按鈕](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

按一下 [建立]  新增使用者流程。 使用者流程列示為 **B2C_1_SSPR**。 名稱會附加 **B2C_1_** 前置詞。

按一下 [執行使用者流程]。 驗證資料表中指定的設定，然後按一下 [執行使用者流程]。

![選取並執行使用者流程](media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| 設定      | 值  |
| ------------ | ------ |
| **應用程式** | Contoso B2C 應用程式 |
| **選取回覆 URL** | `https://localhost:44316/` |

新的瀏覽器索引標籤隨即開啟，您可以在應用程式中驗證密碼重設取用者體驗。

> [!NOTE]
> 建立和更新原則後，需要經過一分鐘才會生效。
>
