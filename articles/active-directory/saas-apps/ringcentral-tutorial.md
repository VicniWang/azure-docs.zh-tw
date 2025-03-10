---
title: 教學課程：Azure Active Directory 與 RingCentral 的整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 RingCentral 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e01b06b2031da20ee9349588bd4a7ee757fbc22c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173295"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>教學課程：Azure Active Directory 與 RingCentral 的整合

在本教學課程中，您會了解如何將 RingCentral 與 Azure Active Directory (Azure AD) 整合。

將 RingCentral 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 RingCentral 的人員。
- 您可以讓使用者使用自己的 Azure AD 帳戶自動登入 RingCentral (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 RingCentral 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 RingCentral 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 RingCentral
2. 設定並測試 Azure AD 單一登入

## <a name="adding-ringcentral-from-the-gallery"></a>從資源庫新增 RingCentral
若要設定將 RingCentral 整合至 Azure AD 中，您需要從資源庫將 RingCentral 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 RingCentral，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![映像](./media/ringcentral-tutorial/selectazuread.png)

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![映像](./media/ringcentral-tutorial/a_select_app.png)
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![映像](./media/ringcentral-tutorial/a_new_app.png)

4. 在搜尋方塊中，鍵入 **RingCentral**，從結果面板選取 [RingCentral]，然後按一下 [新增] 按鈕新增應用程式。

     ![映像](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 RingCentral 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 RingCentral 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 RingCentral 中相關使用者之間的連結關聯性。

若要設定並測試與 RingCentral 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 RingCentral 測試使用者](#create-a-ringcentral-test-user)** - 在 RingCentra 中建立對應到 Britta Simon 的使用者，這位使用者會連結到 Azure AD 中其所代表的使用者。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 RingCentral 應用程式中設定單一登入。

**若要使用 RingCentral 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [RingCentral] 應用程式整合頁面上，選取 [單一登入]。

    ![映像](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. 按一下畫面頂端的 [變更單一登入模式]，選取 [SAML] 模式。

      ![映像](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. 在 [選取單一登入方法] 對話方塊中，選取 [SAML] 模式以啟用單一登入。

    ![映像](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [基本 SAML 組態] 對話方塊。

    ![映像](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. 如果您有**服務提供者中繼資料檔案**，請在 [基本 SAML 設定] 區段上執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]。

    ![映像](./media/ringcentral-tutorial/b9_saml.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]。

    ![映像](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. 當中繼資料檔案成功上傳後，會自動在 [基本 SAML 設定] 區段文字方塊中填入 [識別碼] 及 [回覆 URL] 值，如下所示：

    ![映像](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. 在 [登入 URL] 文字方塊中，鍵入 URL：
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > 您會在 RingCentral SSO 設定頁面上取得**服務提供者中繼資料檔案**，本教學課程稍後會予以說明。

6. 如果您沒有**服務提供者中繼資料檔案**，請執行下列步驟：

    a. 在 [登入 URL] 文字方塊中，鍵入 URL：
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. 在 [識別碼] 文字方塊中輸入 URL：
    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. 在 [回覆 URL] 文字方塊中輸入 URL：
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![映像](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載] 以依據您的需求從指定選項下載憑證，並儲存在您的電腦上。

    ![映像](./media/ringcentral-tutorial/certificatebase64.png)
    
8. 在不同的 Web 瀏覽器視窗中，以安全性系統管理員身分登入 RingCentral。

9. 在頂端處按一下 [工具]。

    ![映像](./media/ringcentral-tutorial/ringcentral1.png)

10. 瀏覽至 [單一登入]。

    ![映像](./media/ringcentral-tutorial/ringcentral2.png)

11. 在 [單一登入] 頁面的 [SSO 設定] 區段下，從 [步驟 1] 按一下 [編輯] 並執行下列步驟：

    ![映像](./media/ringcentral-tutorial/ringcentral3.png)

12. 在 [安裝單一登入] 頁面上，執行下列步驟：

    ![映像](./media/ringcentral-tutorial/ringcentral4.png)

    a. 按一下 [瀏覽] 以上傳您從 Azure 入口網站下載的中繼資料檔案。

    b. 上傳中繼資料後，會自動在 [SSO 一般資訊] 區段中填入這些值。

    c. 在 [屬性對應] 區段下，將 [電子郵件屬性對應目標] 選取為 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. 按一下 [檔案] 。

    e. 從 [步驟 2] 按一下 [下載] 以下載**服務提供者中繼資料檔案**，並在 Azure 入口網站中，上傳至 [基本 SAML 設定] 區段，以自動填入 [識別碼] 及 [回覆 URL] 值。

    ![映像](./media/ringcentral-tutorial/ringcentral6.png) 

    f. 在同一頁上，瀏覽至 [啟用 SSO] 區段，並執行下列步驟：

    ![映像](./media/ringcentral-tutorial/ringcentral5.png)

    a. 選取 [啟用 SSO 服務]。
    
    b. 選取 [允許使用者使用 SSO 或 RingCentral 認證登入]。

    c. 按一下 [檔案] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![映像](./media/ringcentral-tutorial/d_users_and_groups.png)

2. 在畫面頂端選取 [新增使用者]。

    ![映像](./media/ringcentral-tutorial/d_adduser.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![映像](./media/ringcentral-tutorial/d_userproperties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
 
### <a name="create-a-ringcentral-test-user"></a>建立 RingCentral 測試使用者

在本節中，您會在 RingCentral 中建立名為 Britta Simon 的使用者。 請連絡  [RingCentral 用戶端支援小組](https://success.ringcentral.com/RCContactSupp)，以在 RingCentral 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 RingCentral 的存取權授與 Britta Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![映像](./media/ringcentral-tutorial/d_all_applications.png)

2. 在應用程式清單中，選取 [RingCentral]。

    ![映像](./media/ringcentral-tutorial/d_all_proapplications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![映像](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. 選取 [新增] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![映像](./media/ringcentral-tutorial/d_assign_user.png)

4. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

5. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [RingCentral] 磚時，應該會自動登入您的 RingCentral 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

