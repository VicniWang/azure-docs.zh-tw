---
title: 教學課程：Azure Active Directory 與 KnowledgeOwl 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 KnowledgeOwl 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32f53742de3242fb3b60a15d436ec6cd842aa429
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183979"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>教學課程：Azure Active Directory 與 KnowledgeOwl 整合

在本教學課程中，您將了解如何整合 KnowledgeOwl 與 Azure Active Directory (Azure AD)。

KnowledgeOwl 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 KnowledgeOwl 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 KnowledgeOwl (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 KnowledgeOwl 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 KnowledgeOwl 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 KnowledgeOwl
1. 設定並測試 Azure AD 單一登入

## <a name="adding-knowledgeowl-from-the-gallery"></a>從資源庫新增 KnowledgeOwl
若要設定 KnowledgeOwl 與 Azure AD 整合，您需要從資源庫將 KnowledgeOwl 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 KnowledgeOwl，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

1. 在搜尋方塊中，輸入 **KnowledgeOwl**，從結果面板中選取 [KnowledgeOwl]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，搭配 KnowledgeOwl 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 KnowledgeOwl 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 KnowledgeOwl 中相關使用者之間的連結關聯性。

若要搭配 KnowledgeOwl 設定及測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 KnowledgeOwl 測試使用者](#create-a-knowledgeowl-test-user)** - 使 KnowledgeOwl 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在 KnowledgeOwl 應用程式中設定單一登入。

**若要搭配 KnowledgeOwl 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [KnowledgeOwl] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. 如果您想要以 **IDP** 起始模式設定應用程式，請在 [KnowledgeOwl 網域及 URL] 區段上執行下列步驟：

    ![KnowledgeOwl 網域及 URL 單一登入資訊](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. 在 [識別碼 (實體識別碼)] 文字方塊中，使用下列模式輸入 URL：
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. 如果您想要以 **SP** 起始模式設定應用程式，請勾選 [顯示進階 URL 設定]，然後執行下列步驟：

    ![KnowledgeOwl 網域及 URL 單一登入資訊](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > 這些都不是真正的值。 您將需要從實際的識別碼、回覆 URL 及登入 URL 更新這些值，本教學課程稍後會說明這些值。

1. KnowledgeOwl 應用程式需要特定格式的 SAML 判斷提示，需要您將自訂屬性對應新增到 SAML 權杖屬性設定。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中管理這些屬性的值。

    ![設定單一登入](./media/knowledgeowl-tutorial/attribute.png)

1. 在 [單一登入] 對話方塊的 [使用者屬性] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
    | 屬性名稱 | 屬性值 | 命名空間|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. 按一下 [新增屬性] 來開啟 [新增屬性] 對話方塊。
    
    ![設定單一登入](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![設定單一登入](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。
    
    c. 在 [值] 清單中，選取該列所顯示的值。

    d. 從 [命名空間] 清單中，輸入針對該資料列顯示的命名空間值。
    
    e. 按一下 [確定] 。

1. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (原始)]，然後將憑證檔案儲存在您的電腦上。

    ![憑證下載連結](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. 在 [KnowledgeOwl 設定] 區段上，按一下 [設定 KnowledgeOwl] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![KnowledgeOwl 設定](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 KnowledgeOwl 公司網站。

1. 按一下 [設定]，然後選取 [安全性]。

    ![KnowledgeOwl 設定](./media/knowledgeowl-tutorial/configure1.png)

1. 捲動至 [SAML SSO 整合]，然後執行下列步驟：
    
    ![KnowledgeOwl 設定](./media/knowledgeowl-tutorial/configure2.png)

    a. 選取 [啟用 SAML SSO]。

    b. 複製 **SP Entity ID** (SP 實體識別碼) 值，並將它貼至 Azure 入口網站上 [KnowledgeOwl 網域及 URL] 區段中的 [識別碼 (實體識別碼)] 文字方塊。

    c. 複製 **SP Login URL** (SP 登入 URL) 值，並將它貼至 Azure 入口網站上 [KnowledgeOwl 網域及 URL] 區段中的 [登入 URL 和回覆 URL] 文字方塊。

    d. 在 [IdP 實體識別碼] 文字方塊中，貼上您從 Azure 入口網站複製的 **SAML 實體識別碼**值。

    e. 在 [IdP 登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 **SAML 單一登入服務 URL** 值。

    f. 在 [IdP 登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的**登出 URL** 值。

    g. 按一下 [上傳 IdP 憑證]，以上傳從 Azure 入口網站下載的憑證。

    h. 按一下 [對應 SAML 屬性] 以對應屬性，然後執行下列步驟：
    
    ![KnowledgeOwl 設定](./media/knowledgeowl-tutorial/configure3.png)

    * 在 [SSO 識別碼] 文字方塊中輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid`。
    * 在 [使用者名稱/電子郵件] 文字方塊中輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。
    * 在 [名字] 文字方塊中輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。
    * 在 [姓氏] 文字方塊中輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。
    * 按一下 [儲存] 

    i. 按一下頁面底部的 [儲存]  。

    ![KnowledgeOwl 設定](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-a-knowledgeowl-test-user"></a>建立 KnowledgeOwl 測試使用者

本節目標是在 KnowledgeOwl 中建立名為 Britta Simon 的使用者。 KnowledgeOwl 支援預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 嘗試存取 KnowledgeOwl 時，如果使用者還不存在，就會建立新使用者。
>[!Note]
>如果您需要手動建立使用者，請連絡  [KnowledgeOwl 支援小組](mailto:support@knowledgeowl.com)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 KnowledgeOwl 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派給 KnowledgeOwl，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 [KnowledgeOwl]。

    ![應用程式清單中的 KnowledgeOwl 連結](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [KnowledgeOwl] 圖格時，應該會自動登入您的 KnowledgeOwl 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

