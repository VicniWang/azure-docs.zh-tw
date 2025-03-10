---
title: Azure Active Directory 中的 B2B 共同作業使用者宣告對應 | Microsoft Docs
description: 針對 Azure Active Directory (Azure AD) B2B 使用者自訂在 SAML 權杖中發出的使用者宣告。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 461476cea7b8551d5ae548ccd84a5333c21cf432
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202569"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Azure Active Directory 中的 B2B 共同作業使用者宣告對應

Azure Active Directory (Azure AD) 支援針對 B2B 共同作業使用者自訂在 SAML 權杖中發出的宣告。 當使用者對應用程式進行驗證時，Azure AD 會將 SAML 權杖簽發給應用程式，其中包含可唯一識別使用者的使用者相關資訊 (或宣告)。 根據預設值，其中包含使用者的使用者名稱、電子郵件地址、名字和姓氏。

在 [Azure 入口網站](https://portal.azure.com)中，您可以檢視或編輯在 SAML 權杖中傳送給應用程式的宣告。 若要存取設定，請選取 **Azure Active Directory** > **企業應用程式** > 針對單一登入設定的應用程式 > [單一登入]。 請參閱 [使用者屬性] 區段中的 SAML 權杖設定。

![在 UI 中顯示 SAML 權杖屬性](media/claims-mapping/view-claims-in-saml-token.png)

編輯在 SAML 權杖中發出的宣告有兩個可能原因：

1. 應用程式需要不同的宣告 URI 或宣告值組。

2. 應用程式要求 NameIdentifier 宣告必須是 Azure AD 中儲存的使用者主體名稱 (UPN) 以外的項目。

如需如何新增和編輯宣告的資訊，請參閱[針對 Azure Active Directory 中的企業應用程式自訂在 SAML 權杖中發出的宣告](../develop/active-directory-saml-claims-customization.md)。

對於 B2B 共同作業使用者，基於安全性原因，系統會防止對應 NameID 與 UPN 跨租用戶。

## <a name="next-steps"></a>後續步驟

- 如需 B2B 共同作業使用者屬性的相關資訊，請參閱 [Azure Active Directory B2B 共同作業使用者的屬性](user-properties.md)。
- 如需 B2B 共同作業使用者的使用者權杖相關資訊，請參閱[了解 Azure AD B2B 共同作業中的使用者權杖](user-token.md)。

