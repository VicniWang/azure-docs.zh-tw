---
title: 教學課程：Azure Active Directory 與 Work.com 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Work.com 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e5d097afa3af111a7de8341b90d8c6c05ac0347
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205744"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>教學課程：Azure Active Directory 與 Work.com 整合

在本教學課程中，您將了解如何整合 Work.com 與 Azure Active Directory (Azure AD)。

Work.com 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Work.com 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Work.com (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Work.com 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Work.com 單一登入功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Work.com
1. 設定和測試 Azure AD 單一登入

## <a name="add-workcom-from-the-gallery"></a>從資源庫新增 Work.com
若要設定將 Work.com 整合到 Azure AD 中，您需要從資源庫將 Work.com 新增到受控 SaaS app 清單。

**若要從資源庫新增 Work.com，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![[應用程式]][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![[應用程式]][3]

1. 在搜尋方塊中，輸入 **Work.com**，從結果面板中選取 [Work.com]，然後按一下 [新增] 按鈕以新增應用程式。

    ![從資源庫新增](./media/work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Work.com 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Work.com 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Work.com 中的相關使用者之間建立連結關聯性。

在 Work.com 中，將 Azure AD 中**使用者名稱**的值指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 Work.com 搭配運作的 Azure AD 單一登入，您需要完成下列建構元素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 Work.com 測試使用者](#create-a-workcom-test-user)** - 使Work.com 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[測試單一登入](#test-single-sign-on)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Work.com 應用程式中設定單一登入。

>[!NOTE]
>若要設定單一登入，您還需要設定自訂的 Work.com 網域名稱。 您至少需要定義一個網域名稱、測試網域名稱，並將它部署到整個組織。

**若要使用 Work.com 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Work.com] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![SAML 型登入](./media/work-com-tutorial/tutorial_work-com_samlbase.png)

1. 在 [Work.com 網域與 URL] 區段中，執行下列步驟：

    ![[Work.com 網域與 URL] 區段](./media/work-com-tutorial/tutorial_work-com_url.png)

    在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > 這不是真實的值。 請使用實際的登入 URL 來更新此值。 請連絡 [Work.com 用戶端支援小組](https://help.salesforce.com/articleView?id=000159855&type=3)以取得此值。 

1. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![[SAML 簽署憑證] 區段](./media/work-com-tutorial/tutorial_work-com_certificate.png) 

1. 按一下 [儲存]  按鈕。

    ![[儲存] 按鈕](./media/work-com-tutorial/tutorial_general_400.png)

1. 在 [Work.com 設定] 區段上，按一下 [設定 Work.com] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![[Work.com 設定] 區段](./media/work-com-tutorial/tutorial_work-com_configure.png) 
1. 以系統管理員身分登入 Work.com 租用戶。

1. 移到 [設定] 。
   
    ![設定](./media/work-com-tutorial/ic794108.png "設定")

1. 在 [系統管理員] 區段的左方導覽窗格中，按一下 [網域管理] 展開相關的區段，然後按一下 [我的網域] 來開啟 [我的網域] 頁面。 
   
    ![我的網域](./media/work-com-tutorial/ic767825.png "我的網域")

1. 若要確認已正確設定您的網域，請確定目前在 [步驟 4 已部署到使用者] 中，然後檢閱 [我的網域設定]。
   
    ![已部署到使用者的網域](./media/work-com-tutorial/ic784377.png "已部署到使用者的網域")

1. 登入 Work.com 租用戶。

1. 移到 [設定] 。
    
    ![設定](./media/work-com-tutorial/ic794108.png "設定")

1. 展開 [安全性控制] 功能表，然後再按一下 [單一登入設定]。
    
    ![單一登入設定](./media/work-com-tutorial/ic794113.png "單一登入設定")

1. 在 [單一登入設定]  對話方塊頁面執行下列步驟：
    
    ![啟用 SAML](./media/work-com-tutorial/ic781026.png "啟用 SAML")
    
    a. 選取 [已啟用 SAML] 。
    
    b. 按一下 [新增] 。

1. 在 [SAML 單一登入設定]  區段中，執行下列步驟：
    
    ![SAML 單一登入設定](./media/work-com-tutorial/ic794114.png "SAML 單一登入設定")
    
    a. 在 [名稱]  文字方塊中，輸入您的組態名稱。  
       
    > [!NOTE]
    > 提供 [名稱] 的值，[API 名稱] 文字方塊就會自動填入內容。
    
    b. 在 [簽發者] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 實體識別碼] 值。
    
    c. 若要上傳從 Azure 入口網站下載的憑證，請按一下 [瀏覽]。
    
    d. 在 [實體識別碼] 文字方塊中，輸入 `https://salesforce-work.com`。
    
    e. 對於 [SAML 身分識別類型]，選取 [判斷提示包含來自使用者物件的同盟識別碼]。
    
    f. 在 [SAML 識別位置]，請選取 [識別位於 Subject 陳述式的 NameIdentifier 元素中]。
    
    g. 在 [識別提供者登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 單一登入服務 URL] 值。

    h. 在 [識別提供者登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。
    
    i. 在 [服務提供者起始的要求繫結]，選取 [HTTP Post]。
    
    j. 按一下 [檔案] 。

1. 在 Work.com 傳統入口網站的左側導覽窗格中，按一下 [網域管理] 以展開相關區段，然後按一下 [我的網域] 來開啟 [我的網域] 頁面。 
    
    ![我的網域](./media/work-com-tutorial/ic794115.png "我的網域")

1. 在 [我的網域] 頁面的 [登入頁面商標] 區段中，按一下 [編輯]。
    
    ![登入頁面商標](./media/work-com-tutorial/ic767826.png "登入頁面商標")

1. [登入頁面商標] 頁面的 [驗證服務] 區段中，會顯示您的 [SAML SSO 設定] 的名稱。 請選取該名稱，然後按一下 [儲存] 。
    
    ![登入頁面商標](./media/work-com-tutorial/ic784366.png "登入頁面商標")

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/work-com-tutorial/create_aaduser_01.png) 

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![[使用者和群組] -> [所有使用者]](./media/work-com-tutorial/create_aaduser_02.png) 

1. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![加](./media/work-com-tutorial/create_aaduser_03.png) 

1. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![使用者對話方塊頁面](./media/work-com-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-a-workcom-test-user"></a>建立 Work.com 測試使用者
Azure Active Directory 使用者必須先佈建到 Work.com，才可以登入。 Work.com 需以手動方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 以系統管理員身分登入您的 Work.com 公司網站。

1. 移到 [設定] 。
   
    ![設定](./media/work-com-tutorial/IC794108.png "設定")
1. 移至 [管理使用者 \> 使用者]。
   
    ![管理使用者](./media/work-com-tutorial/IC784369.png "管理使用者")

1. 按一下 [新使用者] 。
   
    ![所有使用者](./media/work-com-tutorial/IC794117.png "所有使用者")

1. 在 [使用者編輯] 區段中，在您想要佈建至相關文字方塊之有效 Azure AD 帳戶的屬性中，執行下列步驟：
   
    ![使用者編輯](./media/work-com-tutorial/ic794118.png "使用者編輯")
   
    a. 在 [名字] 文字方塊中，輸入使用者的**名字**：**Britta**。
    
    b. 在 [姓氏] 文字方塊中，輸入使用者的**姓氏**：**Simon**。
    
    c. 在 [別名] 文字方塊中，輸入使用者的**名字**：**BrittaS**。
    
    d. 在 [電子郵件] 文字方塊中，輸入使用者 **Brittasimon@contoso.com** 的**電子郵件地址**。
    
    e. 在 [使用者名稱] 文字方塊中，輸入使用者的使用者名稱，例如 **Brittasimon@contoso.com**。
    
    f. 在 [暱稱] 文字方塊中，輸入使用者的**暱稱**：**Simon**。
    
    g. 選取 [角色]、[使用者授權] 和 [設定檔]。
    
    h. 按一下 [檔案] 。  
      
    > [!NOTE]
    > Azure AD 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Work.com 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Work.com，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 [Work.com]。

    ![應用程式清單中的 Work.com](./media/work-com-tutorial/tutorial_work-com_app.png) 

1. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [Work.com] 圖格時，應該會自動登入您的 Work.com 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/work-com-tutorial/tutorial_general_01.png
[2]: ./media/work-com-tutorial/tutorial_general_02.png
[3]: ./media/work-com-tutorial/tutorial_general_03.png
[4]: ./media/work-com-tutorial/tutorial_general_04.png

[100]: ./media/work-com-tutorial/tutorial_general_100.png

[200]: ./media/work-com-tutorial/tutorial_general_200.png
[201]: ./media/work-com-tutorial/tutorial_general_201.png
[202]: ./media/work-com-tutorial/tutorial_general_202.png
[203]: ./media/work-com-tutorial/tutorial_general_203.png

