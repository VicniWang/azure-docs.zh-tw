---
title: 教學課程：Azure Active Directory 與 ForeSee CX Suite 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 ForeSee CX Suite 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2364152cc897ac02c3fcdff68423c64737bb1a78
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187657"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>教學課程：Azure Active Directory 與 ForeSee CX Suite 整合

在本教學課程中，您會了解如何整合 ForeSee CX Suite 與 Azure Active Directory (Azure AD)。

將 ForeSee CX Suite 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 ForeSee CX Suite 的人員。
- 您可以讓使用者使用其 Azure AD 帳戶來自動登入 ForeSee CX Suite (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 ForeSee CX Suite 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用單一登入的 ForeSee CX Suite 訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。
本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 ForeSee CX Suite
1. 設定並測試 Azure AD 單一登入

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>從資源庫新增 ForeSee CX Suite
若要設定以將 ForeSee CX Suite 整合到 Azure AD 中，您需要從資源庫將 ForeSee CX Suite 新增到受控的 SaaS 應用程式清單。

**若要從資源庫新增 ForeSee CX Suite，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]

1. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

1. 在搜尋方塊中，輸入 **ForeSee CX Suite**，從結果面板中選取 [ForeSee CX Suite]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 [ForeSee CX Suite]](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 ForeSee CX Suite 來設定和測試 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 需要知道 ForeSee CX Suite 與 Azure AD 中互相對應的使用者。 換句話說，需要在 Azure AD 使用者和 ForeSee CX Suite 中的相關使用者之間建立連結關聯性。

若要搭配 ForeSee CX Suite 設定和測試 Azure AD 單一登入，您需要完成下列基本工作：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 ForeSee CX Suite 測試使用者](#create-a-foresee-cx-suite-test-user)** - 讓 ForeSee CX Suite 中的 Britta Simon 對應項目得以連結至 Azure AD 中代表該使用者的項目。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在 ForeSee CX Suite 應用程式中設定單一登入。

**若要搭配 ForeSee CX Suite 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [ForeSee CX Suite] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。

    ![單一登入對話方塊](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

1. 如果您有**服務提供者中繼資料檔案**，請在 [ForeSee CX Suite 網域及 URL] 區段上執行下列步驟：

    ![[ForeSee CX Suite 網域及 URL] 單一登入資訊](./media/foreseecxsuite-tutorial/upload.png)

    a. 按一下 [上傳中繼資料檔案]。

    ![[ForeSee CX Suite 網域及 URL] 單一登入資訊](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]。

    c. 成功完成上傳**服務提供者中繼資料檔案**之後，[ForeSee CX Suite 網域及 URL] 區段文字方塊中會自動填入**識別碼**值，如下所示：

    ![[ForeSee CX Suite 網域及 URL] 單一登入資訊](./media/foreseecxsuite-tutorial/urlupload.png)

1. 如果您沒有**服務提供者中繼資料檔案**，請執行下列步驟：

    ![[ForeSee CX Suite 網域及 URL] 單一登入資訊](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. 在 [登入 URL] 文字方塊中，輸入 URL：`https://cxsuite.foresee.com/`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL： `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > 識別碼值不是實際值。 請使用實際的「識別碼」來更新此值。 請連絡 [ForeSee CX Suite 用戶端支援小組](mailto:support@foresee.com)以取得此值。

1. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

1. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

1. 若要在 **ForeSee CX Suite** 端設定單一登入，您必須將已下載的**中繼資料 XML** 傳送給 [ForeSee CX Suite 支援小組](mailto:support@foresee.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

1. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

1. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="create-a-foresee-cx-suite-test-user"></a>建立 ForeSee CX Suite 測試使用者

在本節中，您會在 ForeSee CX Suite 中建立名為 Britta Simon 的使用者。 請與 [ForeSee CX Suite 支援小組](mailto:support@foresee.com)合作，以新增需要在 ForeSee CX Suite 平台中列入白名單的使用者或網域。 如果是由小組新增網域，將會自動將使用者佈建到 ForeSee CX Suite 平台。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 ForeSee CX Suite 的存取權授與 Britta Simon，讓該使用者能使用 Azure 單一登入。

![指派使用者角色][200]

**若要將 Britta Simon 指派給 ForeSee CX Suite，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201]

1. 在應用程式清單中，選取 [ForeSee CX Suite]。

    ![應用程式清單中的 [ForeSee CX Suite] 連結](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

1. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您按一下 [存取面板] 中的 [ForeSee CX Suite] 圖格時，應該就會自動登入 ForeSee CX Suite 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

