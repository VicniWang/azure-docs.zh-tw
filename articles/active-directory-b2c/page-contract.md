---
title: 在 Azure Active Directory B2C 中選取頁面合約 | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中選取頁面合約。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fa48d3825b5b942497b1eabd19a1eeb7e0141058
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003137"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>使用自訂原則在 Azure Active Directory B2C 中選取頁面合約

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

無論您使用的是使用者流程或自訂原則，都可以在 Azure Active Directory (Azure AD) B2C 原則中啟用 JavaScript 用戶端程式碼。 本文探討如何藉由在[自訂原則](active-directory-b2c-overview-custom.md)中設定頁面合約，以在 Azure AD B2C 中選取之。 頁面合約是由 Azure AD B2C 提供的元素與您提供的內容所相關聯的內容。 如果您想要使用 [JavaScript](javascript-samples.md)，您必須針對您自訂原則中的所有內容定義，定義頁面合約版本。

> [!NOTE]
> 如果要為使用者流程啟用 JavaScript，請參閱[關於在使用者流程中使用 JavaScript 和頁面合約版本](user-flow-javascript-overview.md)。

## <a name="replace-datauri-values"></a>取代 DataUri 值

在您的自訂原則中，您可能會有定義用於使用者旅程圖中之 HTML 範本的 [ContentDefinitions](contentdefinitions.md)。 **ContentDefinition** 包含會參考由 Azure AD B2C 所提供之頁面元素的 **DataUri**。 **LoadUri** 是針對您提供之 HTML 和 CSS 內容的相對路徑。

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

若要選取頁面合約，您必須變更您原則中之 [ContentDefinitions](contentdefinitions.md) 的 **DataUri** 值。 藉由從舊的 **DataUri** 值切換到新的值，您將會選取不可變的套件。 使用此套件的優點，在於您知道它不會變更，並在您的頁面上造成未預期的行為。 

若要設定頁面合約，請使用下表來尋找 **DataUri** 值。 

| 舊的 DataUri 值 | 新的 DataUri 值 |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>後續步驟

如需如何自訂應用程式之使用者介面的詳細資訊，請參閱[在 Azure Active Directory B2C 中使用自訂原則來自訂應用程式的使用者介面](active-directory-b2c-ui-customization-custom.md)。



