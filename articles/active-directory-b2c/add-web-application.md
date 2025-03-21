---
title: 新增 Web 應用程式 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何將 Web 應用程式新增至您的 Active Directory B2C 租用戶。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 4522914f249413300ffa5bb1545d840711777bff
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235904"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>將 Web API 應用程式新增至您的 Azure Active Directory B2C 租用戶

Web API 資源必須先在您的租用戶中註冊，才能接受及回應受到用戶端應用程式保護而提供存取權杖的資源要求。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [應用程式]，然後選取 [新增]。
5. 輸入應用程式的名稱。 例如，*webapi1*。
6. 針對 [包含 Web 應用程式/Web API] 和 [允許隱含流程]，選取 [是]。
7. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 在本教學課程中，範例會在本機執行並在 `https://localhost:44332` 接聽。
8. 針對**應用程式識別碼 URI**，請輸入您的 Web API 所使用的識別碼。 系統會為您產生包含網域的完整識別碼 URI。 例如： `https://contosotenant.onmicrosoft.com/api`。
9. 按一下頁面底部的 [新增] 。
10. 在 [屬性] 頁面上，記錄您會在設定 Web 應用程式時使用的應用程式識別碼。

## <a name="configure-scopes"></a>設定範圍

範圍可用來控管對受保護資源的存取。 Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，Web API 的使用者可以同時具有讀取和寫入權限，Web API 的使用者也可能只具有讀取權限。 在本教學課程中，您會使用範圍來定義 Web API 的讀取和寫入權限。

1. 選取 [應用程式]，然後選取 [webapi1]。
2. 選取 [發佈的範圍]。
3. 針對 [範圍]，輸入 `Read`，以及輸入 `Read access to the application` 作為描述。
4. 針對 [範圍]，輸入 `Write`，以及輸入 `Write access to the application` 作為描述。
5. 按一下 [檔案] 。

發佈的範圍可以用來為用戶端應用程式授與對 Web API 的權限。

## <a name="grant-permissions"></a>授與權限

若要從應用程式呼叫受保護的 Web API，您必須為應用程式授與對 API 的權限。 例如，在[教學課程：在 Azure Active Directory B2C 中註冊應用程式](tutorial-register-applications.md)中，會在 Azure AD B2C 中建立名為 *webapp1* 的 Web 應用程式。 您可以使用此應用程式來呼叫 Web API。

1. 選取 [應用程式]，然後選取您的 Web 應用程式。
2. 選取 [API 存取]，然後選取 [新增]。
3. 在 [選取 API] 下拉式清單中，選取 [webapi1]。
4. 在 [選取範圍] 下拉式清單中，選取您先前定義的 [讀取] 和 [寫入] 範圍。
5. 按一下 [確定]。

您的應用程式會進行註冊，以呼叫受保護的 Web API。 使用者會透過 Azure AD B2C 進行驗證以使用應用程式。 該應用程式會從 Azure AD B2C 取得授權授與，以存取受保護的 Web API。