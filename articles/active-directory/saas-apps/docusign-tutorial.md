---
title: 教學課程：Azure Active Directory 與 DocuSign 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 DocuSign 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb6e130b772afabe939516ffa37ae292e2db5859
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178939"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>教學課程：Azure Active Directory 與 DocuSign 整合

在本教學課程中，您會了解如何整合 DocuSign 與 Azure Active Directory (Azure AD)。

DocuSign 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 DocuSign 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 DocuSign (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 DocuSign 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 DocuSign 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 DocuSign
2. 設定並測試 Azure AD 單一登入

## <a name="adding-docusign-from-the-gallery"></a>從資源庫新增 DocuSign

若要設定將 DocuSign 整合到 Azure AD 中，您需要從資源庫將 DocuSign 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 DocuSign，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中輸入 **DocuSign**，從結果面板中選取 [DocuSign]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 DocuSign](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 DocuSign 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 DocuSign 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 DocuSign 中相關使用者之間的連結關聯性。

若要設定及測試與 DocuSign 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 DocuSign 測試使用者](#creating-a-docusign-test-user)** - 在 DocuSign 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 DocuSign 應用程式中設定單一登入。

**若要使用 DocuSign 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [DocuSign] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](common/tutorial_general_301.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](common/editconfigure.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![DocuSign 網域與 URL 單一登入資訊](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值 (本教學課程稍後的**檢視 SAML 2.0 端點**一節會加以說明)。

5. 在 [SAML 簽署憑證] 頁面上的 [SAML 簽署憑證] 區段中，按一下 [下載] 以下載**憑證 (Base64)**，然後將憑證檔案儲存在電腦上。

    ![憑證下載連結](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. 在 [安裝 DocuSign] 區段上，依據您的需求複製適當的 URL。

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    ![DocuSign 組態](common/configuresection.png)

7. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **DocuSign 系統管理入口網站**。

8. 在頁面右上方按一下設定檔**標誌**，然後按一下 [移至系統管理員]。
  
    ![設定單一登入][51]

9. 在您的網域解決方案頁面上，按一下 [網域]

    ![設定單一登入][50]

10. 在 [網域] 區段下方，按一下 [宣告網域]。

    ![設定單一登入][52]

11. 在 [宣告網域] 對話方塊，於 [網域名稱] 文字方塊內輸入您的公司網域，然後按一下 [宣告]。 確定您已驗證網域，而且狀態為作用中。

    ![設定單一登入][53]

12. 在您的網域解決方案頁面上，按一下 [識別提供者]。
  
    ![設定單一登入][54]

13. 在 [識別提供者] 區段下方，按一下 [新增識別提供者]。 

    ![設定單一登入][55]

14. 在 [識別提供者設定]  頁面上執行下列步驟：

    ![設定單一登入][56]

    a. 在 [名稱] 文字方塊中，輸入組態的唯一名稱。 請勿使用空格。

    b. 在 [識別提供者簽發者] 文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼] 值。

    c. 在 [識別提供者登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL] 值。

    d. 在 [識別提供者登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。

    e. 選取 [簽署驗證要求]。

    f. 選取 [POST] 做為 [驗證要求傳送方式]。

    g. 選取 [GET] 作為 [登出要求傳送方式]。

    h. 在 [自訂屬性對應] 區段中，按一下 [新增對應]。

    ![設定單一登入][62]

    i. 選擇要與 Azure AD 宣告對應的欄位。 在此範例中，**emailaddress** 宣告的對應值是 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。 這是 Azure AD 針對電子郵件宣告所提供的預設宣告名稱；然後，按一下 [儲存]。

    ![設定單一登入][57]

    > [!NOTE]
    > 請使用適當的 **使用者識別碼**，將使用者從 Azure AD 對應到 DocuSign 使用者對應。 選取適當的欄位，並根據組織的設定輸入適當的值。

    j. 在 [識別提供者憑證] 區段中按一下 [新增憑證]，然後上傳您從 Azure AD 入口網站下載的憑證，再按一下 [儲存]。

    ![設定單一登入][58]

    k. 在 [識別提供者] 區段中按一下 [動作]，然後按一下 [端點]。

    ![設定單一登入][59]

    l. 在 **DocuSign 系統管理入口網站**的 [檢視 SAML 2.0 端點] 區段中，執行下列步驟：

    ![設定單一登入][60]

    * 複製 [服務提供者簽發者 URL]，並將其貼到 Azure 入口網站上的 [DocuSign 網域與 URL] 區段的 [識別碼] 文字方塊中。

    * 複製 [服務提供者登入 URL]，並將其貼到 Azure 入口網站上的 [DocuSign 網域與 URL] 區段的 [登入 URL] 文字方塊中。

    * 按一下 [關閉]。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 在畫面頂端選取 [新增使用者]。

    ![建立 Azure AD 測試使用者](common/create_aaduser_01.png) 

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](common/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="creating-a-docusign-test-user"></a>建立 DocuSign 測試使用者

本節的目標是在 DocuSign 中建立名為 Britta Simon 的使用者。 DocuSign 支援預設為啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 當您嘗試存取 DocuSign 時，如果 DocuSign 還沒有使用者，它就會建立新的使用者。
>[!Note]
>如果您需要手動建立使用者，請連絡  [DocuSign 支援小組](https://support.docusign.com/)。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 DocuSign 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [DocuSign]。

    ![設定單一登入](./media/docusign-tutorial/tutorial_docusign_app.png)

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [DocuSign] 磚時，應該會自動登入您的 DocuSign 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
