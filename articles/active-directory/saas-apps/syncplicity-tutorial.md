---
title: 教學課程：Azure Active Directory 與 Syncplicity 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Syncplicity 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3df48fd42ca998e2a64f2fbe685047da16a8bae7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193472"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>教學課程：Azure Active Directory 與 Syncplicity 整合

在本教學課程中，您將了解如何整合 Syncplicity 與 Azure Active Directory (Azure AD)。

Syncplicity 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Syncplicity 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Syncplicity (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Syncplicity 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Syncplicity 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Syncplicity
1. 設定並測試 Azure AD 單一登入

## <a name="adding-syncplicity-from-the-gallery"></a>從資源庫新增 Syncplicity
若要設定 Syncplicity 與 Azure AD 整合，您需要從資源庫將 Syncplicity 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 Syncplicity，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![[應用程式]][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![[應用程式]][3]

1. 在搜尋方塊中，輸入 **Syncplicity**。

    ![建立 Azure AD 測試使用者](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

1. 在結果窗格中，選取 [Syncplicity]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Syncplicity 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Syncplicity 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Syncplicity 中的相關使用者之間建立連結關聯性。

在 Syncplicity 中，將 Azure AD 中 [使用者名稱] 的值指派為 [使用者名稱] 的值，以建立連結關聯性。

若要設定及測試與 Syncplicity 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 Syncplicity 測試使用者](#creating-a-syncplicity-test-user)** - 使 Syncplicity 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
1. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 Syncplicity 應用程式中設定單一登入。

**若要設定與 Syncplicity 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Syncplicity] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

1. 在 [Syncplicity 網域及 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<companyname>.syncplicity.com`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Syncplicity 客戶支援小組](https://www.syncplicity.com/contact-us)以取得這些值。 
 

1. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
1. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/syncplicity-tutorial/tutorial_general_400.png)

1. 在 [Syncplicity 組態] 區段上，按一下 [設定 Syncplicity] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![設定單一登入](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

1. 登入您的 **Syncplicity** 租用戶。

1. 在上方功能表中按一下 [管理]，選取 [設定]，然後按一下 [自訂網域和單一登入]。
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. 在 [單一登入 (SSO)]  對話方塊頁面執行下列步驟：
   
    ![單一登入 \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. 在 [自訂網域]  文字方塊中輸入您的網域名稱。
  
    b. 在 [單一登入狀態] 中選取 [啟用]。

    c. 在 [實體識別碼] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 實體識別碼] 值。

    d. 在 [登入頁面 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 單一登入服務 URL]。

    e. 在 [登出頁面 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]。

    f. 在 [識別提供者憑證] 中，按一下 [選擇檔案]，然後上傳您從 Azure 入口網站下載的憑證。 

    g. 按一下 [儲存變更]。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/syncplicity-tutorial/create_aaduser_01.png) 

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/syncplicity-tutorial/create_aaduser_02.png) 

1. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![建立 Azure AD 測試使用者](./media/syncplicity-tutorial/create_aaduser_03.png) 

1. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="creating-a-syncplicity-test-user"></a>建立 Syncplicity 測試使用者
AAD 使用者必須先佈建到 Syncplicity 應用程式，才可以登入。 本節描述如何建立 Syncplicity 內的 AAD 使用者帳戶。

**若要將使用者帳戶佈建到 Syncplicity，請執行下列步驟：**

1. 登入您的 **Syncplicity** 租用戶 (例如 `https://company.Syncplicity.com`)。

1. 按一下 [管理員]，然後選取 [使用者帳戶]。

1. 按一下 [新增使用者]。
   
    ![管理使用者](./media/syncplicity-tutorial/ic769764.png "管理使用者")

1. 輸入您想要佈建之 AAD 帳戶的 [電子郵件地址]，選取 [使用者] 作為 [角色]，然後按一下 [下一步]。
   
    ![帳戶資訊](./media/syncplicity-tutorial/ic769765.png "帳戶資訊")
   
    >[!NOTE]
    >AAD 帳戶的持有者會收到電子郵件，其中包含可確認並啟動帳戶的連結。 
    > 

1. 選取要讓新使用者成為成員的公司群組，然後按一下 [下一步]。
   
    ![群組成員資格](./media/syncplicity-tutorial/ic769772.png "群組成員資格")
   
    >[!NOTE]
    >如果未列出群組，請按一下 [下一步]。 
    > 

1. 選取您想要在使用者電腦上受 Syncplicity 控制的資料夾，然後按一下 [下一步]。
   
    ![Syncplicity 資料夾](./media/syncplicity-tutorial/ic769773.png "Syncplicity 資料夾")

>[!NOTE]
>您可以使用任何其他的 Syncplicity 使用者帳戶建立工具或 Syncplicity 提供的 API 來佈建 AAD 使用者帳戶。 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Syncplicity 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Syncplicity，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 [Syncplicity]。

    ![設定單一登入](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

1. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 [Syncplicity] 圖格時，應該會自動登入您的 Syncplicity 應用程式。
## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

