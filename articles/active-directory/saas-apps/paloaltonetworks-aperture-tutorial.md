---
title: 教學課程：Azure Active Directory 與 Palo Alto Networks - Aperture 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Palo Alto Networks - Aperture 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61603ad5920b6242c3e36429173744125b9eb59e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206733"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>教學課程：Azure Active Directory 與 Palo Alto Networks 整合 - Aperture

在本教學課程中，您將了解如何整合 Palo Alto Networks - Aperture 與 Azure Active Directory (Azure AD)。

將 Palo Alto Networks - Aperture 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 Palo Alto Networks - Aperture 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Palo Alto Networks - Aperture (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Palo Alto Networks - Aperture 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Palo Alto Networks - Aperture 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Palo Alto Networks - Aperture
1. 設定並測試 Azure AD 單一登入

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>從資源庫新增 Palo Alto Networks - Aperture
若要設定將 Palo Alto Networks - Aperture 整合到 Azure AD 中，您需要將 Palo Alto Networks - Aperture 從資源庫新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Palo Alto Networks - Aperture，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

1. 在搜尋方塊中，輸入 **Palo Alto Networks - Aperture**，從結果面板中選取 [Palo Alto Networks - Aperture]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Palo Alto Networks - Aperture](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Palo Alto Networks - Aperture 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Palo Alto Networks - Aperture 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Palo Alto Networks - Aperture 中的相關使用者之間建立連結關聯性。

若要設定及測試與 Palo Alto Networks - Aperture 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 Palo Alto Networks - Aperture 測試使用者](#create-a-palo-alto-networks---aperture-test-user)** - 在 Palo Alto Networks - Aperture 中建立一個與 Azure AD 中代表 Britta Simon 之使用者連結的 Britta Simon 對應項目。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Palo Alto Networks - Aperture 應用程式中設定單一登入。

**若要設定與 Palo Alto Networks - Aperture 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Palo Alto Networks - Aperture] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

1. 如果您想要以 **IDP** 起始模式設定應用程式，請在 [Palo Alto Networks - Aperture 網域及 URL] 區段上執行下列步驟：

    ![Palo Alto Networks - Aperture 網域及 URL 單一登入資訊](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. 在 **[回覆 URL]** 文字方塊中，以下列模式輸入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

1. 如果您想要以 **SP** 起始模式設定應用程式，請勾選 [顯示進階 URL 設定]，然後執行下列步驟：

    ![Palo Alto Networks - Aperture 網域及 URL 單一登入資訊](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 請連絡 [Palo Alto Networks - Aperture 用戶端支援小組](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) \(英文\) 以取得這些值。 

1. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![憑證下載連結](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

1. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


1. 在 [Palo Alto Networks - Aperture 設定] 區段上，按一下 [設定 Palo Alto Networks - Aperture] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [SAML 實體識別碼] 和 [SAML 單一登入服務 URL]。

    ![設定連結](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Palo Alto Networks - Aperture。

1. 按一下頂端功能表列上的 [SETTINGS] \(設定\)。

    ![[SETTINGS] \(設定\) 索引標籤](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

1. 瀏覽至 [APPLICATION] \(應用程式\) 區段，然後從功能表左側按一下 [Authentication] \(驗證\)。

    ![[Authentication] \(驗證\) 索引標籤](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
1. 在 [Authentication] \(驗證\) 頁面上，執行下列步驟：
    
    ![[Authentication] \(驗證\) 索引標籤](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. 從 [Single Sign-On] \(單一登入\) 欄位中，選取 [Enable Single Sign-On(Supported SSP Providers are Okta, Onelogin)] \(啟用單一登入 (支援的 SSP 提供者為 Okta、Onelogin)\)。

    b. 在 [Identity Provider ID] \(身分識別提供者識別碼\) 文字方塊中，貼上您從 Azure 入口網站複製的 **SAML 實體識別碼**值。

    c. 按一下 [Identity Provider Certificate] \(身分識別提供者憑證\) 欄位中的 [Choose File] \(選擇檔案\)，以上傳從 Azure AD 下載的憑證。

    d. 在 [Identity Provider SSO URL] \(身分識別提供者 SSO URL\) 文字方塊中，貼上您從 Azure 入口網站複製的 **SAML 單一登入服務 URL** 值。

    e. 檢閱來自 [Aperture Info] \(Aperture 資訊\) 區段的 IdP 資訊，然後從 [Aperture Key] \(Aperture 金鑰\) 欄位下載憑證。

    f. 按一下 [檔案] 。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

1. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

1. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>建立 Palo Alto Networks - Aperture 測試使用者

在本節中，您會在 Palo Alto Networks - Aperture 中建立名為 Britta Simon 的使用者。 請與  [Palo Alto Networks - Aperture 用戶端支援小組](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support)  \(英文\) 合作，在 Palo Alto Networks - Aperture 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。 

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Palo Alto Networks - Aperture 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派給 Palo Alto Networks - Aperture，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

1. 在應用程式清單中，選取 [Palo Alto Networks - Aperture]。

    ![應用程式清單中的 Palo Alto Networks - Aperture 連結](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

1. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [Palo Alto Networks - Aperture] 圖格時，應該會自動登入您的 Palo Alto Networks - Aperture 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_203.png

