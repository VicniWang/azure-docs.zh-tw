---
title: 教學課程：Azure Active Directory 與 Silverback 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Silverback 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5614c061586c39e44f04f3542285e55e07f14d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172701"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>教學課程：Azure Active Directory 與 Silverback 整合

在本教學課程中，您會了解如何整合 Silverback 與 Azure Active Directory (Azure AD)。

Silverback 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Silverback 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Silverback (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Silverback 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 有效的 Silverback 訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Silverback
2. 設定並測試 Azure AD 單一登入

## <a name="adding-silverback-from-the-gallery"></a>從資源庫新增 Silverback
若要設定 Silverback 與 Azure AD 整合，您需要從資源庫將 Silverback 加入受控 SaaS 應用程式清單中。

**若要從資源庫新增 Silverback，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Silverback**，從結果面板中選取 [Silverback]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Silverback](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Silverback 設定和測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Silverback 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Silverback 中的相關使用者之間建立連結關聯性。

若要使用 Silverback 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Silverback 測試使用者](#create-a-silverback-test-user)** - 使 Silverback 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Silverback 應用程式中設定單一登入。

**若要使用 Silverback 設定 Azure AD 單一登入功能，請執行下列步驟：**

1. 在 Azure 入口網站的 [Silverback] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. 在 [Silverback 網域與 URL] 區段上，執行下列步驟：

    ![Silverback 網域與 URL 單一登入資訊](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<YOURSILVERBACKURL>.com/ssp`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： `<YOURSILVERBACKURL>.com`

    c. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [Silverback 用戶端支援小組](mailto:helpdesk@matrix42.com)以取得這些值。 

4. 在 [SAML 簽署憑證] 區段中，按一下「複製」按鈕複製「應用程式同盟中繼資料 URL」，並將它貼到 [記事本]。

    ![憑證下載連結](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/silverback-tutorial/tutorial_general_400.png)

6.  以系統管理員身分登入您的 Silverback Server，然後執行下列步驟：

    a.  瀏覽至 [管理員]  >  [驗證提供者]。

    b. 在 [驗證提供者設定]  頁面上，執行下列步驟：

    ![系統管理員 ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  按一下 [從 URL 匯入]。
    
    d.  貼上複製的中繼資料 URL，然後按一下 [確定]。
    
    e.  按一下 [確定] 進行確定，系統便會自動填入值。
    
    f.  啟用 [在登入頁面上顯示]。
    
    g.  如果您想要自動新增 Azure AD 授權的使用者，請啟用 [動態使用者建立] (選擇性)。
    
    h.  在自助入口網站上為該按鈕建立 [標題]。

    i.  按一下 [選擇檔案]，上傳 [圖示]。
    
    j.  為按鈕選取**彩色**背景。
    
    k.  按一下 [檔案] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/silverback-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/silverback-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/silverback-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/silverback-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-a-silverback-test-user"></a>建立 Silverback 測試使用者

若要讓 Azure AD 使用者能夠登入 Silverback，必須將他們佈建到 Silverback。 在 Silverback 中，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入 Silverback Server。

2. 瀏覽至 [使用者]，並**新增新的裝置使用者**。

3. 在 [基本]  頁面上，執行下列步驟：

    ![使用者 ](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. 在 [使用者名稱] 文字方塊中，輸入使用者的名稱，例如 **Britta**。

    b. 在 [名字] 文字方塊中，輸入使用者的名字，例如 **Britta**。

    c. 在 [姓氏] 文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    d. 在 [電子郵件地址] 文字方塊中，輸入使用者的電子郵件，例如 **Brittasimon@contoso.com**。

    e. 在 [密碼] 文字方塊中，輸入您的密碼。
    
    f. 在 [確認密碼] 文字方塊中，再次輸入您的密碼並確認。

    g. 按一下 [檔案] 。

>[!NOTE]
>如果您不想要手動建立每個使用者，請到 [管理員]  >  [驗證提供者] 下方，啟用 [動態使用者建立] 核取方塊。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Silverback 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派到 Silverback，請執行以下步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Silverback] 。

    ![應用程式清單中的 Silverback 連結](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Silverback 圖格時，應該會自動登入您的 Silverback 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png

