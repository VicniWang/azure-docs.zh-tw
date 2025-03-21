---
title: 教學課程：Azure Active Directory 與 Bonusly 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Bonusly 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62927e78b8f1a771de74c0f9215a7bd946a46672
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199590"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>教學課程：Azure Active Directory 與 Bonusly 整合

在本教學課程中，您將了解如何整合 Bonusly 與 Azure Active Directory (Azure AD)。

將 Bonusly 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Bonusly 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Bonusly (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Bonusly 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Bonusly 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Bonusly
1. 設定並測試 Azure AD 單一登入

## <a name="adding-bonusly-from-the-gallery"></a>從資源庫新增 Bonusly
若要設定將 Bonusly 整合到 Azure AD 中，您需要從資源庫將 Bonusly 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Bonusly，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

1. 在搜尋方塊中，輸入 **Bonusly**，從結果面板中選取 [Bonusly]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Bonusly](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Bonusly 設定及測試 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Bonusly 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Bonusly 中相關使用者之間的連結關聯性。

在 Bonusly 中，將 Azure AD 中**使用者名稱**的值指派為 **Username** 的值，以建立連結關聯性。

若要使用 Bonusly 設定及測試 Azure AD 單一登入功能，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 Bonusly 測試使用者](#create-a-bonusly-test-user)** - 在 Bonusly 中建立一個與 Azure AD 中代表使用者之項目連結的 Britta Simon 對應項目。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[測試單一登入](#test-single-sign-on)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會於 Azure 入口網站啟用 Azure AD 單一登入，並在您的 Bonusly 應用程式中設定單一登入。

**若要使用 Bonusly 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Bonusly] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

1. 在 [Bonusly 網域與 URL] 區段上，執行下列步驟：

    ![Bonusly 網域及 URL 單一登入資訊](./media/bonus-tutorial/tutorial_bonusly_url.png)

    在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > 這不是真正的值。 請使用實際的「回覆 URL」來更新此值。 請連絡 [Bonusly 支援小組](https://bonus.ly/contact)以取得該值。
 
1. 在 [SAML 簽署憑證] 區段上，複製憑證的 [指紋] 值。

    ![憑證下載連結](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

1. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/bonus-tutorial/tutorial_general_400.png)

1. 在 [Bonusly 組態] 區段上，按一下 [設定 Bonusly] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [SAML 實體識別碼] 和 [SAML 單一登入服務 URL]。

    ![Bonusly 組態](./media/bonus-tutorial/tutorial_bonusly_configure.png) 

1. 在不同的瀏覽器視窗中，登入您的 **Bonusly** 租用戶。

1. 在頂端工具列中，按一下 [設定]，然後選取 [整合與應用程式]。
   
    ![Bonusly 社交區段](./media/bonus-tutorial/ic773686.png "Bonusly")
1. 在 [單一登入] 下方，選取 [SAML]。

1. 在 [SAML]  對話頁面上，執行下列步驟：
   
    ![Bonusly Saml 對話方塊頁面](./media/bonus-tutorial/ic773687.png "Bonusly")
   
    a. 在 [IdP SSO 目標 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 單一登入服務 URL] 值。
   
    b. 在 [IdP 簽發者] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 實體識別碼] 值。 

    c. 在 [IdP 登入 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 單一登入服務 URL] 值。

    d. 將從 Azure 入口網站複製的 ****[指紋] 值貼到 [Cert Fingerprint] \(憑證指紋\) 文字方塊中。
   
1. 按一下 [檔案] 。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](./media/bonus-tutorial/create_aaduser_01.png) 

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![[使用者和群組] 與 [所有使用者] 連結](./media/bonus-tutorial/create_aaduser_02.png) 

1. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![[新增] 按鈕](./media/bonus-tutorial/create_aaduser_03.png) 

1. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![[使用者] 對話方塊](./media/bonus-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-a-bonusly-test-user"></a>建立 Bonusly 測試使用者

若要讓 Azure AD 使用者可以登入 Bonusly，必須將他們佈建到 Bonusly。 Bonusly 需以手動方式佈建。

>[!NOTE]
>您可使用任何其他的 Bonusly 使用者帳戶建立工具，或是使用 Bonusly 提供的 API 來佈建 AAD 使用者帳戶。
>  

**若要設定使用者佈建，請執行下列步驟：**

1. 在網頁瀏覽器視窗中，登入您的 Bonusly 租用戶。

1. 按一下 [設定] 。
 
    ![設定](./media/bonus-tutorial/ic781041.png "設定")

1. 按一下 [使用者和獎勵]  索引標籤。
   
    ![使用者和獎勵](./media/bonus-tutorial/ic781042.png "使用者和獎勵")

1. 按一下 [管理使用者] 。
   
    ![管理使用者](./media/bonus-tutorial/ic781043.png "管理使用者")

1. 按一下 [新增使用者] 。
   
    ![新增使用者](./media/bonus-tutorial/ic781044.png "新增使用者")

1. 在 [加入使用者]  對話方塊中，執行下列步驟：
   
    ![新增使用者](./media/bonus-tutorial/ic781045.png "新增使用者")  

    a. 在 [名字] 文字方塊中，輸入使用者的名字，例如 **Britta**。

    b. 在 [姓氏] 文字方塊中，輸入使用者的姓氏，例如 **Simon**。
 
    c. 在 [電子郵件] 文字方塊中，輸入使用者的電子郵件，例如 **brittasimon@contoso.com**。

    d. 按一下 [檔案] 。
   
     >[!NOTE]
     >Azure AD 帳戶持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。
     >  

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Bonusly 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派到 Bonusly，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 [Bonusly]。

    ![應用程式清單中的 Bonusly 連結](./media/bonus-tutorial/tutorial_bonusly_app.png) 

1. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202] 

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 Bonusly 圖格時，應該會自動登入 Bonusly 應用程式。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bonus-tutorial/tutorial_general_01.png
[2]: ./media/bonus-tutorial/tutorial_general_02.png
[3]: ./media/bonus-tutorial/tutorial_general_03.png
[4]: ./media/bonus-tutorial/tutorial_general_04.png

[100]: ./media/bonus-tutorial/tutorial_general_100.png

[200]: ./media/bonus-tutorial/tutorial_general_200.png
[201]: ./media/bonus-tutorial/tutorial_general_201.png
[202]: ./media/bonus-tutorial/tutorial_general_202.png
[203]: ./media/bonus-tutorial/tutorial_general_203.png

