---
title: 教學課程：Azure Active Directory 與 PolicyStat 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 PolicyStat 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43ddf56e4c72e7e59778fc43a808b9800bc3b9b3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187707"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>教學課程：Azure Active Directory 與 PolicyStat 整合

在本教學課程中，您會了解如何整合 PolicyStat 與 Azure Active Directory (Azure AD)。

PolicyStat 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 PolicyStat 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 PolicyStat (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 PolicyStat 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 PolicyStat 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 PolicyStat
1. 設定並測試 Azure AD 單一登入

## <a name="adding-policystat-from-the-gallery"></a>從資源庫新增 PolicyStat
若要設定將 PolicyStat 整合到 Azure AD 中，您需要從資源庫將 PolicyStat 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 PolicyStat，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![[應用程式]][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![[應用程式]][3]

1. 在搜尋方塊中，輸入 **PolicyStat**。

    ![建立 Azure AD 測試使用者](./media/policystat-tutorial/tutorial_policystat_search.png)

1. 在結果面板中，選取 [PolicyStat]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 PolicyStat 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 PolicyStat 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 PolicyStat 中的相關使用者之間，建立連結關聯性。

在 PolicyStat 中，將 Azure AD 中**使用者名稱**的值指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 PolicyStat 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 PolicyStat 測試使用者](#creating-a-policystat-test-user)** - 使 PolicyStat 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 PolicyStat 應用程式中設定單一登入。

**若要設定與 PolicyStat 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [PolicyStat] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/policystat-tutorial/tutorial_policystat_samlbase.png)

1. 在 [PolicyStat 網域及 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/policystat-tutorial/tutorial_policystat_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<companyname>.policystat.com`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [PolicyStat 客戶支援小組](http://www.policystat.com/support/)以取得這些值。 
 
1. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![設定單一登入](./media/policystat-tutorial/tutorial_policystat_certificate.png) 

1. 本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 PolicyStat 中進行驗證。

    PolicyStat 應用程式需要特定格式的 SAML 判斷提示，所以您必須將自訂屬性對應新增至 **SAML 權杖屬性**設定。  

     以下螢幕擷取畫面顯示上述的範例。

     ![屬性](./media/policystat-tutorial/tutorial_policystat_attribute.png "屬性")

1. 若要加入必要的屬性對應，請執行下列步驟：

    | 屬性名稱    |   屬性值 |
    |------------------- | -------------------- |
    | UID | ExtractMailPrefix([mail]) |
    
    a. 按一下 [新增屬性] 來開啟 [新增屬性] 對話方塊。

    ![設定單一登入](./media/policystat-tutorial/tutorial_policystat_04.png)

    ![設定單一登入](./media/policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. 在 [屬性名稱] 文字方塊中，輸入 **uid**。

    c. 在 [屬性值] 文字方塊中，選取 [ExtractMailPrefix()]。    
   
    d. 從 [郵件] 清單選取 [User.mail]。
    
    e. 按一下 [確定]。

1. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/policystat-tutorial/tutorial_general_400.png)

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 PolicyStat 公司網站。

1. 按一下 [管理] 索引標籤，然後按一下左側導覽窗格中的 [單一登入設定]。
   
    ![系統管理員功能表](./media/policystat-tutorial/ic808633.png "系統管理員功能表")

1. 在 [安裝] 區段中，選取 [啟用單一登入整合]。
   
    ![單一登入設定](./media/policystat-tutorial/ic808634.png "單一登入設定")

1. 按一下 [設定屬性]，然後在 [設定屬性]  區段中，執行下列步驟：
   
    ![單一登入設定](./media/policystat-tutorial/ic808635.png "單一登入設定")
   
    a. 在 [使用者名稱屬性] 文字方塊中，輸入 **uid**。

    b. 在 [名字屬性] 文字方塊中，輸入使用者的**名字**：**Britta**。

    c. 在 [姓氏屬性] 文字方塊中，輸入使用者的**姓氏**：**Simon**。

    d. 在 [電子郵件屬性] 文字方塊中，輸入使用者的**電子郵件地址**：**BrittaSimon@contoso.com**。

    e. 按一下 [儲存變更] 。

1. 按一下 [您的 IDP 中繼資料]，然後在 [您的 IDP 中繼資料] 區段中，執行下列步驟：
   
    ![單一登入設定](./media/policystat-tutorial/ic808636.png "單一登入設定")
   
    a. 開啟您下載的中繼資料檔案，複製內容，然後貼到 [您的識別提供者中繼資料] 文字方塊中。

    b. 按一下 [儲存變更] 。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/policystat-tutorial/create_aaduser_01.png) 

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/policystat-tutorial/create_aaduser_02.png) 

1. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![建立 Azure AD 測試使用者](./media/policystat-tutorial/create_aaduser_03.png) 

1. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/policystat-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="creating-a-policystat-test-user"></a>建立 PolicyStat 測試使用者

為了讓 Azure AD 使用者能夠登入 PolicyStat，必須將他們佈建到 PolicyStat。  

PolicyStat 支援即時使用者佈建。 這表示您不需要手動將使用者新增至 PolicyStat。 使用者第一次透過 SSO 登入時就會被自動新增。

>[!NOTE]
>您可以使用任何其他的 PolicyStat 使用者帳戶建立工具，或 PolicyStat 提供的 API，以佈建 AAD 使用者帳戶。
> 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 PolicyStat 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 PolicyStat，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 [PolicyStat]。

    ![設定單一登入](./media/policystat-tutorial/tutorial_policystat_app.png) 

1. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 PolicyStat 圖格時，應該會自動登入您的 PolicyStat 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/policystat-tutorial/tutorial_general_01.png
[2]: ./media/policystat-tutorial/tutorial_general_02.png
[3]: ./media/policystat-tutorial/tutorial_general_03.png
[4]: ./media/policystat-tutorial/tutorial_general_04.png

[100]: ./media/policystat-tutorial/tutorial_general_100.png

[200]: ./media/policystat-tutorial/tutorial_general_200.png
[201]: ./media/policystat-tutorial/tutorial_general_201.png
[202]: ./media/policystat-tutorial/tutorial_general_202.png
[203]: ./media/policystat-tutorial/tutorial_general_203.png

