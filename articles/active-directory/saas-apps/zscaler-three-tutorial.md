---
title: 教學課程：Azure Active Directory 與 Zscaler Three 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Zscaler Three 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5739c878812c09a8f2646320df4b1069dfa8810e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189034"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>教學課程：Azure Active Directory 與 Zscaler Three 整合

在本教學課程中，您將了解如何整合 Zscaler Three 與 Azure Active Directory (Azure AD)。

將 Zscaler Three 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 Zscaler Three 的人員。
- 您可以讓使用者使用其 Azure AD 帳戶自動登入 Zscaler Three (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Zscaler Three 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Zscaler Three 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Zscaler Three
2. 設定並測試 Azure AD 單一登入

## <a name="adding-zscaler-three-from-the-gallery"></a>從資源庫新增 Zscaler Three

若要設定將 Zscaler Three 整合到 Azure AD 中，您需要從資源庫將 Zscaler Three 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Zscaler Three，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中輸入 **Zscaler Three**，並從結果面板中選取 [Zscaler Three]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Zscaler Three](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Zscaler Three 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Zscaler Three 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Zscaler Three 中的相關使用者之間建立連結關聯性。

若要使用 Zscaler Three 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Zscaler Three 單一登入](#configure-zscaler-three-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 Zscaler Three 測試使用者](#create-zscaler-three-test-user)** - 使 Cisco Umbrella 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
5. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在 Zscaler Three 應用程式中設定單一登入。

**若要設定與 Zscaler Three 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Zscaler Three] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [選取單一登入方法] 對話方塊上，按一下 [SAML] 模式的 [選取]，啟用單一登入。

    ![設定單一登入](common/tutorial_general_301.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![設定單一登入](common/editconfigure.png)

4. 在 [基本 SAML 組態] 區段上，執行下列步驟：

    ![Zscaler Three 網域和 URL 單一登入資訊](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    在 [登入 URL] 文字方塊中，輸入 URL：`https://login.zscalerthree.net/sfc_sso`

5. Zscaler Three 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性與宣告] 區段中管理這些屬性的值。 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 按鈕以開啟 [使用者屬性與宣告] 對話方塊。

    ![屬性連結](./media/zscaler-three-tutorial/tutorial_zscalerthree_attribute.png)

6. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | 名稱  | 來源屬性  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![映像](./common/new_save_attribute.png)
    
    ![映像](./common/new_attribute_details.png)

    b. 從 [來源屬性] 清單中，選取屬性值。

    c. 按一下 [確定] 。

    d. 按一下 [檔案] 。

    > [!NOTE]
    > 請按一下[這裡](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)，以了解如何在 Azure AD 中設定角色

7. 在 [SAML 簽署憑證] 頁面上的 [SAML 簽署憑證] 區段中，按一下 [下載] 以下載**憑證 (Base64)**，然後將憑證檔案儲存在電腦上。

    ![憑證下載連結](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png) 

8. 在 [設定 Zscaler Three] 區段上，依據需求複製適當的 URL。

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

    ![Zscaler Three 組態](common/configuresection.png)

### <a name="configure-zscaler-three-single-sign-on"></a>設定 Zscaler Three 單一登入

9. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入 Zscaler Three 公司網站。

10. 移至 管理 > 驗證 > 驗證設定 並執行下列步驟：
   
    ![管理](./media/zscaler-three-tutorial/ic800206.png "管理")

    a. 在 [驗證類型] 下選擇 [SAML]。

    b. 按一下 [設定 SAML]。

11. 在 [編輯 SAML] 視窗上執行下列步驟，然後按一下 [儲存]。  
            
    ![管理使用者和驗證](./media/zscaler-three-tutorial/ic800208.png "管理使用者和驗證")
    
    a. 在 [SAML 入口網站 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]。

    b. 在 [登入名稱屬性] 文字方塊中，輸入 **NameID**。

    c. 按一下 [上傳]，以上傳您從 Azure 入口網站的 [公開 SSL 憑證] 下載的 Azure SAML 簽署憑證。

    d. 切換 [啟用 SAML 自動佈建]。

    e. 如果您想要啟用 displayName 屬性的 SAML 自動佈建，請在 [使用者顯示名稱屬性] 文字方塊中，輸入 **displayName**。

    f. 如果您想要啟用 memberOf 屬性的 SAML 自動佈建，請在 [群組名稱屬性] 文字方塊中，輸入 **memberOf**。

    g. 如果您想要啟用 department 屬性的 SAML 自動佈建，請在 [部門名稱屬性] 文字方塊中，輸入 **department**。

    i. 按一下 [檔案] 。

12. 在 [設定使用者驗證]  對話方塊頁面上執行下列步驟：

    ![系統管理](./media/zscaler-three-tutorial/ic800207.png)

    a. 將滑鼠停留在靠近左下方的 [啟用] 功能表上。

    b. 按一下 [啟用]。

## <a name="configuring-proxy-settings"></a>進行 Proxy 設定
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中進行 Proxy 設定

1. 啟動 **Internet Explorer**。

1. 從 [工具] 功能表選取 [網際網路選項] 可開啟 [網際網路選項] 對話方塊。   
    
     ![網際網路選項](./media/zscaler-three-tutorial/ic769492.png "網際網路選項")

1. 按一下 [連線]  索引標籤。   
  
     ![連線](./media/zscaler-three-tutorial/ic769493.png "連線")

1. 按一下 [區域網路設定] 可開啟 [區域網路設定] 對話方塊。

1. 在 [Proxy 伺服器] 區段中，執行下列步驟：   
   
    ![Proxy 伺服器](./media/zscaler-three-tutorial/ic769494.png "Proxy 伺服器")

    a. 選取 [在您的區域網路使用 Proxy 伺服器]。

    b. 在 [位址] 文字方塊中輸入 **gateway.Zscaler Three.net**。

    c. 在 [連接埠] 文字方塊中輸入 **80**。

    d. 選取 [近端網址不使用 Proxy 伺服器] 。

    e. 按一下 [確定] 關閉 [區域網路 (LAN) 設定] 對話方塊。

1. 按一下 [確定] 關閉 [網際網路選項] 對話方塊。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![建立 Azure AD 使用者][100]

2. 在畫面頂端選取 [新增使用者]。

    ![建立 Azure AD 測試使用者](common/create_aaduser_01.png) 

3. 在 [使用者] 屬性中，執行下列步驟。

    ![建立 Azure AD 測試使用者](common/create_aaduser_02.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 依序選取 [屬性] [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。

### <a name="create-zscaler-three-test-user"></a>建立 Zscaler Three 測試使用者

本節的目標是在 Zscaler Three 中建立名為 Britta Simon 的使用者。 Zscaler Three 支援依預設啟用的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 嘗試存取 Zscaler Three 時，如果使用者還不存在，就會建立新的使用者。
>[!Note]
>如果您需要手動建立使用者，請連絡  [Zscaler Three 支援小組](https://www.zscaler.com/company/contact)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Zscaler Three 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式] 和 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [Zscaler Three]。

    ![設定單一登入](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕，然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊中，從清單中選取使用者 (例如 **Britta Simon**)，然後按一下畫面底部的 [選取] 按鈕。

    ![映像](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

6. 從 [選取角色] 對話方塊的清單中選擇適當的使用者角色，然後按一下畫面底部的 [選取] 按鈕。

    ![映像](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

7. 在 [新增指派] 對話方塊中，選取 [指派] 按鈕。

    ![映像](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Zscaler Three 圖格時，應該會自動登入您的 Zscaler Three 應用程式。
如需存取面板的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
