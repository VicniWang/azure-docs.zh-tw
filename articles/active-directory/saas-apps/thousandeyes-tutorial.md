---
title: 教學課程：Azure Active Directory 與 ThousandEyes 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 ThousandEyes 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43aa43d37099d69441a5dde47327e784d67fa9ac
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189441"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>教學課程：Azure Active Directory 與 ThousandEyes 整合

在本教學課程中，您將了解如何整合 ThousandEyes 與 Azure Active Directory (Azure AD)。

ThousandEyes 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 ThousandEyes 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 ThousandEyes (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

如要設定 Azure AD 與 ThousandEyes 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 ThousandEyes 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在這裡取得一個月試用：[試用版供應項目](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 ThousandEyes
1. 設定並測試 Azure AD 單一登入

## <a name="adding-thousandeyes-from-the-gallery"></a>從資源庫新增 ThousandEyes
若要設定將 ThousandEyes 整合到 Azure AD 中，您需要從資源庫將 ThousandEyes 新增到受控 SaaS app 清單。

**若要從資源庫新增 ThousandEyes，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![[應用程式]][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![[應用程式]][3]

1. 在搜尋方塊中，輸入 **ThousandEyes**。

    ![建立 Azure AD 測試使用者](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

1. 在結果窗格中，選取 [ThousandEyes]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 ThousandEyes 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 ThousandEyes 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 ThousandEyes 中的相關使用者之間，建立連結關聯性。

在 ThousandEyes 中，將 Azure AD 中**使用者名稱**的值，指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 ThousandEyes 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 ThousandEyes 測試使用者](#creating-a-thousandeyes-test-user)** - 使 ThousandEyes 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
1. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 ThousandEyes 應用程式中設定單一登入。

**若要使用 ThousandEyes 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [ThousandEyes] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。

    ![設定單一登入](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

1. 在 [ThousandEyes 網域與 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    在 [登入 URL] 文字方塊中，將 URL 輸入為：`https://app.thousandeyes.com/login/sso`

1. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

1. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/thousandeyes-tutorial/tutorial_general_400.png)

1. 在 [ThousandEyes 組態] 區段上，按一下 [設定 ThousandEyes] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![設定單一登入](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **ThousandEyes** 公司網站。

1. 在頂端的功能表中，按一下 [設定] 。

    ![設定](./media/thousandeyes-tutorial/ic790066.png "設定")

1. 按一下 [帳戶] 

    ![帳戶](./media/thousandeyes-tutorial/ic790067.png "帳戶")

1. 按一下 [安全性與驗證] 索引標籤。

    ![安全性和驗證](./media/thousandeyes-tutorial/ic790068.png "安全性和驗證")

1. 在 [設定單一登入]  區段中，執行下列步驟：

    ![設定單一登入](./media/thousandeyes-tutorial/ic790069.png "設定單一登入")

    a. 選取 [啟用單一登入] 。

    b. 在 [登入頁面 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 **SAML 單一登入服務 URL**。

    c. 在 [登出頁面 URL] 文字方塊中，貼上您從 Azure 入口網站複製的**登出 URL**。

    d. 在 [識別提供者簽發者] 文字方塊中，貼上您從 Azure 入口網站複製的 **SAML 實體識別碼**。

    e. 在 [驗證憑證] 中，按一下 [選擇檔案]，然後上傳您從 Azure 入口網站下載的憑證。

    f. 按一下 [檔案] 。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/thousandeyes-tutorial/create_aaduser_01.png) 

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/thousandeyes-tutorial/create_aaduser_02.png) 

1. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。

    ![建立 Azure AD 測試使用者](./media/thousandeyes-tutorial/create_aaduser_03.png)

1. 在 [使用者]  對話頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="creating-a-thousandeyes-test-user"></a>建立 ThousandEyes 測試使用者

本節的目標是在 ThousandEyes 中建立名為 Britta Simon 的使用者。 ThousandEyes 支援自動使用者佈建，該功能預設為啟用。 您可以在[這裡](thousandeyes-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

**如果您需要手動建立使用者，請執行下列步驟：**

1. 以系統管理員身分登入您的 ThousandEyes 公司網站。

1. 按一下 [設定] 。

    ![設定](./media/thousandeyes-tutorial/IC790066.png "設定")

1. 按一下 [帳戶] 。

    ![帳戶](./media/thousandeyes-tutorial/IC790067.png "帳戶")

1. 按一下 [帳戶和使用者] 索引標籤。

    ![帳戶和使用者](./media/thousandeyes-tutorial/IC790073.png "帳戶和使用者")

1. 在 [加入使用者和帳戶] 區段中，執行下列步驟：

    ![新增使用者帳戶](./media/thousandeyes-tutorial/IC790074.png "新增使用者帳戶")

    a. 在 [名稱] 文字方塊中，輸入使用者的名稱，例如 **Britta Simon**。

    b. 在 [電子郵件] 文字方塊中，輸入使用者的電子郵件，例如 **brittasimon@contoso.com**。

    b. 按一下 [加入使用者至帳戶] 按鈕。

    > [!NOTE]
    > Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以供其確認並啟用帳戶。

> [!NOTE]
> 您可以使用任何其他的 ThousandEyes 使用者帳戶建立工具或 ThousandEyes 提供的 API 來佈建 Azure Active Directory 使用者帳戶。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 ThousandEyes 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 ThousandEyes，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 [ThousandEyes]。

    ![設定單一登入](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

1. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [ThousandEyes] 圖格時，應該會自動登入您的 ThousandEyes 應用程式。

如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [設定使用者佈建](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
