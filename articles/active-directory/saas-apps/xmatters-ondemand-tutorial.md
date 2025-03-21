---
title: 教學課程：Azure Active Directory 與 xMatters OnDemand 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 xMatters OnDemand 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e947b71fe6bb9e6bd884b8b2dd0397a63bea2a95
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203636"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>教學課程：Azure Active Directory 與 xMatters OnDemand 整合

在本教學課程中，您將了解如何整合 xMatters OnDemand 與 Azure Active Directory (Azure AD)。

xMatters OnDemand 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 xMatters OnDemand 的人員
- 您可以讓使用者利用自己的 Azure AD 帳戶，來自動登入 xMatters OnDemand (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 xMatters OnDemand 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 xMatters OnDemand 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 xMatters OnDemand
1. 設定並測試 Azure AD 單一登入

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>從資源庫新增 xMatters OnDemand
若要設定將 xMatters OnDemand 整合到 Azure AD 中，您需要從資源庫將 xMatters OnDemand 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 xMatters OnDemand，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![[應用程式]][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![[應用程式]][3]

1. 在搜尋方塊中，輸入 **xMatters OnDemand**。

    ![建立 Azure AD 測試使用者](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

1. 在結果窗格中，選取 [xMatters OnDemand]，然後按一下 [新增] 按鈕來新增應用程式。

    ![建立 Azure AD 測試使用者](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 xMatters OnDemand 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 xMatters OnDemand 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 xMatters OnDemand 中相關使用者之間的連結關聯性。

在 xMatters OnDemand 中，將 Azure AD 中**使用者名稱**的值，指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 xMatters OnDemand 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 xMatters OnDemand 測試使用者](#creating-a-xmatters-ondemand-test-user)** - 在 xMatters OnDemand 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
1. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 xMatters OnDemand 應用程式中設定單一登入。

**若要使用 xMatters OnDemand 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [xMatters OnDemand] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。

    ![設定單一登入](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

1. 在 [xMatters OnDemand 網域與 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [xMatters OnDemand 支援小組](https://www.xmatters.com/company/contact-us/)以取得這些值。

1. 在 [SAML 簽署憑證] 區段上，按一下 [憑證]\(Base64\)，然後將憑證檔案儲存在本機 **c:\\XMatters OnDemand.cer**。

    ![設定單一登入](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > 您需要將憑證轉送給 [xMatters OnDemand 支援小組](https://www.xmatters.com/company/contact-us/)。 xMatters 支援小組需要先上傳憑證，您才能完成單一登入組態。 

1. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

1. 在 [xMatters OnDemand 設定] 區段中，按一下 [設定 xMatters OnDemand] 可開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![設定單一登入](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 xMatters OnDemand 公司網站。

1. 在頂端工具列中按一下 [管理]，然後按一下左側導覽列中的 [公司詳細資料]。

    ![管理](./media/xmatters-ondemand-tutorial/IC776795.png "管理")

1. 在 [SAML 組態]  頁面上，執行下列步驟：

    ![SAML 組態](./media/xmatters-ondemand-tutorial/IC776796.png "SAML 組態")

    a. 選取 [啟用 SAML] 。

    b. 在 [識別提供者識別碼] 文字方塊中，貼上您從 Azure 入口網站複製的 **SAML 實體識別碼**值。

    c. 在 [單一登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 **SAML 單一登入服務 URL** 值。

    d. 在 [單一登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的**登出 URL**。

    e. 在 [公司詳細資料] 頁面頂端，按一下 [儲存變更] 。

    ![公司詳細資料](./media/xmatters-ondemand-tutorial/IC776797.png "公司詳細資料")

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![建立 Azure AD 測試使用者](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

1. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。

    ![建立 Azure AD 測試使用者](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

1. 在 [使用者]  對話頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="creating-a-xmatters-ondemand-test-user"></a>建立 xMatters OnDemand 測試使用者

本節的目標是在 xMatters OnDemand 中建立名為 Britta Simon 的使用者。

**如果您需要手動建立使用者，請執行下列步驟：**

1. 登入 **xMatters OnDemand** 租用戶。

1.  按一下 [使用者] 索引標籤，然後按一下 [新增使用者]。

    ![使用者](./media/xmatters-ondemand-tutorial/IC781048.png "使用者")

1. 在 [加入使用者]  區段中，執行下列步驟：

    ![加入使用者](./media/xmatters-ondemand-tutorial/IC781049.png "加入使用者")

    a. 選取 [使用中] 。

    b. 在 [使用者識別碼] 文字方塊中，輸入使用者的使用者識別碼，例如 Brittasimon@contoso.com。

    c. 在 [名字] 文字方塊中，輸入使用者的名字，例如 Britta。

    d. 在 [姓氏] 文字方塊中，輸入使用者的姓氏，例如 Simon。

    e. 在 [網站] 文字方塊中，輸入您想要佈建之有效 Azure AD 帳戶的有效網站。

    f. 按一下 [檔案] 。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 xMatters OnDemand 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 xMatters OnDemand，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 **xMatters OnDemand**。

    ![設定單一登入](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

1. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [xMatters OnDemand] 圖格時，應該會自動登入您的 xMatters OnDemand 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/xmatters-ondemand-tutorial/tutorial_general_203.png
