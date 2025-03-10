---
title: 移除使用 Microsoft 身分識別平台所註冊的應用程式 | Azure
description: 了解如何移除使用 Microsoft 身分識別平台所註冊的應用程式。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d71489194422a785ee98ec70a07c765c3d686808
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182607"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform-preview"></a>快速入門：移除使用 Microsoft 身分識別平台所註冊的應用程式 (預覽)

已使用 Microsoft 身分識別平台註冊應用程式的企業開發人員和軟體即服務 (SaaS) 提供者，可能需要移除應用程式的註冊。

在本快速入門中，您將了解如何：

* 移除您或組織所編寫的應用程式
* 移除其他組織所編寫的應用程式

## <a name="prerequisites"></a>必要條件

開始之前，請先確定您已完成以下先決條件：

* 擁有已有應用程式在其中註冊的租用戶。 若要了解如何新增和註冊應用程式，請參閱[使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)。
* 在 Azure 入口網站中選擇加入應用程式註冊預覽版體驗。 本快速入門中的步驟會對應到新的 UI，而且必須選擇加入預覽版體驗才能進行。

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>移除您或組織所編寫的應用程式

您或組織所註冊的應用程式會同時由租用戶中的應用程式物件與服務主體物件來表示。 如需詳細資訊，請參閱 [應用程式物件和服務主體物件](active-directory-application-objects.md)。

### <a name="to-remove-an-application"></a>移除應用程式

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
2. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取您的帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
3. 在左側導覽窗格中，選取 [Azure Active Directory] 服務，然後選取 [應用程式註冊 (預覽)]。 尋找並選取您要設定的應用程式。 在選取應用程式後，您會看到應用程式的 [概觀] 頁面。
4. 從 [概觀] 頁面上，選取 [刪除]。
5. 選取 [是] 以確認要刪除應用程式。

  > [!NOTE]
  > 若要刪除應用程式，您必須列名為應用程式的擁有者或具有管理員權限。

## <a name="remove-an-application-authored-by-another-organization"></a>移除其他組織所編寫的應用程式

如果您正在檢視租用戶內容中的 [應用程式註冊]，則出現在 [所有應用程式] 索引標籤下方的應用程式子集，均來自另一個租用戶，並且是在同意程序進行期間註冊到租用戶的。 更具體來說，它們僅由您租用戶中的服務主體物件表示，而沒有對應的應用程式物件。 如需有關應用程式物件與服務主體物件之差異的詳細資訊，請參閱 [Azure AD 中的應用程式和服務主體物件](active-directory-application-objects.md)。

為了移除應用程式對目錄的存取權 (在授與同意之後)，公司系統管理員必須移除其服務主體。 系統管理員必須擁有全域系統管理員存取權，才能透過 Azure 入口網站移除應用程式或使用 [Azure AD PowerShell Cmdlet](https://go.microsoft.com/fwlink/?LinkId=294151) 移除存取權。

## <a name="next-steps"></a>後續步驟

了解下列其他的相關應用程式管理快速入門：

* [使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)
* [設定用戶端應用程式以存取 Web API](quickstart-configure-app-access-web-apis.md)
* [設定應用程式以公開 Web API](quickstart-configure-app-expose-web-apis.md)
* [修改應用程式所支援的帳戶](quickstart-modify-supported-accounts.md)
