---
title: 教學課程：Azure Active Directory 與 Trisotech Digital Enterprise Server 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Trisotech Digital Enterprise Server 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4ed44bff002ed8422f199c24a566fe6ff606af8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190648"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>教學課程：Azure Active Directory 與 Trisotech Digital Enterprise Server 整合

在本教學課程中，您將了解如何整合 Trisotech Digital Enterprise Server 與 Azure Active Directory (Azure AD)。

Trisotech Digital Enterprise Server 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Trisotech Digital Enterprise Server 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Trisotech Digital Enterprise Server (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Trisotech Digital Enterprise Server 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Trisotech Digital Enterprise Server 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Trisotech Digital Enterprise Server
1. 設定並測試 Azure AD 單一登入

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>從資源庫新增 Trisotech Digital Enterprise Server
若要設定 Trisotech Digital Enterprise Server 與 Azure AD 整合，您需要從資源庫將 Trisotech Digital Enterprise Server 新增到受控 SaaS 應用程式清單中。

**若要從資源庫新增 Trisotech Digital Enterprise Server，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

1. 在搜尋方塊中，輸入 **Trisotech Digital Enterprise Server**，從結果面板中選取 [Trisotech Digital Enterprise Server]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Trisotech Digital Enterprise Server](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Trisotech Digital Enterprise Server 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Trisotech Digital Enterprise Server 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Trisotech Digital Enterprise Server 中的相關使用者之間建立連結關聯性。

若要設定和測試與 Trisotech Digital Enterprise Server 搭配運作的 Azure AD 單一登入，您必須完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 Trisotech Digital Enterprise Server測試使用者](#create-a-trisotech-digital-enterprise-server-test-user)** - 使 Trisotech Digital Enterprise Server 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 Trisotech Digital Enterprise Server 應用程式中設定單一登入。

**若要使用 Trisotech Digital Enterprise Server 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Trisotech Digital Enterprise Server] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

1. 在 [Trisotech Digital Enterprise Server 網域及 URL] 區段中，執行下列步驟：

    ![Trisotech Digital Enterprise Server 網域與 URL 單一登入資訊](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<companyname>.trisotech.com`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Trisotech Digital Enterprise Server 用戶端支援小組](mailto:support@trisotech.com)以取得這些值。

1. 在 [SAML 簽署憑證] 區段中，按一下「複製」按鈕複製「應用程式同盟中繼資料 URL」，並將它貼到 [記事本]。 

    ![憑證下載連結](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

1. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入「Trisotech Digital Enterprise Server 設定」公司網站。

1. 按一下 [功能表圖示]，然後選取 [管理]。

    ![設定單一登入](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

1. 選取 [使用者提供者]。

    ![設定單一登入](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

1. 在 [使用者提供者組態] 區段中執行下列步驟：

    ![設定單一登入](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. 從 [驗證方法] 的下拉式清單中選取 [安全的聲明標記語言 2 (SAML 2)]。

    b. 在 [中繼資料 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [應用程式同盟中繼資料 Url] 值。

    c. 在 [應用程式識別碼] 文字方塊中，使用下列模式輸入 URL：`https://<companyname>.trisotech.com`。

    d. 按一下 [儲存] 

    e. 在 [允許網域 (空白表示所有人)] 文字方塊中輸入網域名稱，它會自動為符合允許網域的使用者指派授權

    f. 按一下 [儲存] 

 ### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

1. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

1. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>建立 Trisotech Digital Enterprise Server 測試使用者

本節目標是在 Trisotech Digital Enterprise Server 中建立名為 Britta Simon 的使用者。 Trisotech Digital Enterprise Server 支援預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 當您嘗試存取 Trisotech Digital Enterprise Server 時，如果 Trisotech Digital Enterprise Server 還沒有使用者，它就會建立新的使用者。
>[!Note]
>如果您需要手動建立使用者，請連絡 [Trisotech Digital Enterprise Server 支援小組](mailto:support@trisotech.com)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Trisotech Digital Enterprise Server 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派給 Trisotech Digital Enterprise Server，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 [Trisotech Digital Enterprise Server]。

    ![應用程式清單中的 Trisotech Digital Enterprise Server 連結](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

1. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Trisotech Digital Enterprise Server] 圖格時，應該會自動登入您的 Trisotech Digital Enterprise Server 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

