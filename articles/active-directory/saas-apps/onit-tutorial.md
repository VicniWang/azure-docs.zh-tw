---
title: 教學課程：Azure Active Directory 與 Onit 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Onit 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b856227733fcc65e12eb43ea1f6b704502a7b29a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169338"
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>教學課程：Azure Active Directory 與 Onit 整合

在本教學課程中，您將了解如何整合 Onit 與 Azure Active Directory (Azure AD)。

Onit 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Onit 的人員。
- 您可以讓使用者透過其 Azure AD 帳戶自動登入 Onit (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Onit 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Onit 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Onit
1. 設定並測試 Azure AD 單一登入

## <a name="adding-onit-from-the-gallery"></a>從資源庫新增 Onit
若要設定將 Onit 整合到 Azure AD 中，您需要從資源庫將 Onit 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Onit，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

1. 在搜尋方塊中，輸入 **Onit**，從結果面板中選取 [Onit]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Onit](./media/onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Onit 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Onit 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Onit 中的相關使用者之間建立連結關聯性。

在 Onit 中，將 Azure AD 中**使用者名稱**的值指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 Onit 搭配運作的 Azure AD 單一登入，您需要完成下列建構元素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 Onit 測試使用者](#create-an-onit-test-user)** - 使 Onit 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[測試單一登入](#test-single-sign-on)** 驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Onit 應用程式中設定單一登入。

**若要使用 Onit 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Onit] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/onit-tutorial/tutorial_onit_samlbase.png)

1. 在 [Onit 網域與 URL] 區段中，執行下列步驟：

    ![Onit 網域與 URL 單一登入資訊](./media/onit-tutorial/tutorial_onit_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<sub-domain>.onit.com`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： `https://<sub-domain>.onit.com`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Onit 用戶端支援小組](https://www.onit.com/support)以取得這些值。 
 
1. 在 [SAML 簽署憑證] 區段上，複製憑證的 [指紋] 值。

    ![憑證下載連結](./media/onit-tutorial/tutorial_onit_certificate.png) 

1. Onit 應用程式需要特定格式的 SAML 判斷提示。 請設定此應用程式的下列宣告。 您可以從應用程式的 [屬性]  索引標籤來管理這些屬性的值。 以下螢幕擷取畫面顯示上述的範例。 

    ![設定單一登入](./media/onit-tutorial/tutorial_onit_attribute.png) 

1. 在 [單一登入] 對話方塊的 [使用者屬性] 區段中，如圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
    | 屬性名稱 | 屬性值 |
    | ------------------- | -------------------- |
    | 電子郵件 | user.mail |
    
    a. 按一下 [新增屬性] 來開啟 [新增屬性] 對話方塊。

    ![設定單一登入](./media/onit-tutorial/tutorial_attribute_04.png)

    ![設定單一登入](./media/onit-tutorial/tutorial_attribute_05.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 在 [值] 清單中，選取該列所顯示的值。

    d. 讓 [命名空間] 保持空白。
    
    e. 按一下 [確定] 。

1. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/onit-tutorial/tutorial_general_400.png)

1. 在 [Onit 設定] 區段上，按一下 [設定 Onit] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL] 和 [SAML 單一登入服務 URL]。

    ![Onit 設定](./media/onit-tutorial/tutorial_onit_configure.png)

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Onit 公司網站。

1. 在頂端的功能表中，按一下 [系統管理] 。
   
   ![管理](./media/onit-tutorial/IC791174.png "管理")
1. 按一下 [編輯公司] 。
   
   ![編輯公司](./media/onit-tutorial/IC791175.png "編輯公司")
   
1. 按一下 [安全性]  索引標籤。
    
    ![編輯公司資訊](./media/onit-tutorial/IC791176.png "編輯公司資訊")

1. 在 [安全性]  索引標籤上執行下列步驟：。

    ![單一登入](./media/onit-tutorial/IC791177.png "單一登入")

    a. 對於**驗證策略**，請選取 [單一登入和密碼]。
    
    b. 在 [Idp 目標 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 單一登入服務 URL] 值。

    c. 在 [Idp 登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。

    d. 在 [Idp 憑證指紋 (SHA1)] 文字方塊中，貼上您從 Azure 入口網站複製的憑證 [指紋] 值。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/onit-tutorial/create_aaduser_01.png)

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/onit-tutorial/create_aaduser_02.png)

1. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/onit-tutorial/create_aaduser_03.png)

1. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/onit-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-an-onit-test-user"></a>建立 Onit 測試使用者

為了讓 Azure AD 使用者能夠登入 Onit，必須將他們佈建到 Onit。  

在 Onit 的情況下，需以手動方式佈建。

**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Onit** 公司網站。
1. 按一下 [新增使用者] 。
   
   ![管理](./media/onit-tutorial/IC791180.png "管理")
1. 在 [新增使用者]  對話頁面上，執行下列步驟：
   
   ![新增使用者](./media/onit-tutorial/IC791181.png "新增使用者")
   
  1. 在相關的文字方塊中，輸入您想要佈建之有效 Azure AD 帳戶的 [名稱] 與 [電子郵件地址]。
  1. 按一下頁面底部的 [新增] 。    
   
 > [!NOTE]
 > Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Onit 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派給 Onit，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 [Onit]。

    ![應用程式清單中的 Onit 連結](./media/onit-tutorial/tutorial_onit_app.png)  

1. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [Onit] 圖格時，應該會自動登入您的 Onit 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/onit-tutorial/tutorial_general_01.png
[2]: ./media/onit-tutorial/tutorial_general_02.png
[3]: ./media/onit-tutorial/tutorial_general_03.png
[4]: ./media/onit-tutorial/tutorial_general_04.png

[100]: ./media/onit-tutorial/tutorial_general_100.png

[200]: ./media/onit-tutorial/tutorial_general_200.png
[201]: ./media/onit-tutorial/tutorial_general_201.png
[202]: ./media/onit-tutorial/tutorial_general_202.png
[203]: ./media/onit-tutorial/tutorial_general_203.png

