---
title: 教學課程：Azure Active Directory 與 Cerner Central 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Cerner Central 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d2bc549d-d286-4679-854e-bb67c62b0475
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62888afb29c3b721da978c7180b36d62d6a575a8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200559"
---
# <a name="tutorial-azure-active-directory-integration-with-cerner-central"></a>教學課程：Azure Active Directory 與 Cerner Central 整合

在本教學課程中，您會了解如何整合 Cerner Central 與 Azure Active Directory (Azure AD)。

將 Cerner Central 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 Cerner Central 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Cerner Central (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Cerner Central 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已核准的 Cerner Central 系統帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Cerner Central
1. 設定並測試 Azure AD 單一登入

## <a name="adding-cerner-central-from-the-gallery"></a>從資源庫新增 Cerner Central
若要設定將 Cerner Central 整合到 Azure AD 中，您需要從資源庫將 Cerner Central 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Cerner Central，執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

1. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![[應用程式]][2]

1. 若要新增新的應用程式，按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![[應用程式]][3]

1. 在搜尋方塊中輸入 **Cerner Central**。

    ![建立 Azure AD 測試使用者](./media/cernercentral-tutorial/tutorial_cernercentral_search.png)

1. 在結果面板中，選取 [Cerner Central]，然後按一下 [新增] 按鈕以新增該應用程式。

    ![建立 Azure AD 測試使用者](./media/cernercentral-tutorial/tutorial_cernercentral_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Cerner Central 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Cerner Central 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Cerner Central 中的相關使用者之間建立連結關聯性。

若要設定及測試 Azure AD 與 Cerner Central 的單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
1. **[建立 Cerner Central 測試使用者](#creating-a-cerner-central-test-user)** - 在 Cerner Central 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的 Britta Simon 對應項目。
1. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Cerner Central 應用程式中設定單一登入。

**若要設定與 Cerner Central 搭配運作的 Azure AD 單一登入，執行下列步驟：**

1. 在 Azure 入口網站的 [Cerner Central] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

1. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。

    ![設定單一登入](./media/cernercentral-tutorial/tutorial_cernercentral_samlbase.png)

1. 在 [Cerner Central 網域及 URL] 區段上，執行下列步驟：

    ![設定單一登入](./media/cernercentral-tutorial/tutorial_cernercentral_url.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入值：

    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/metadata` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/metadata` |
    
    b. 在 [回覆 URL] 文字方塊中，使用下列模式輸入 URL：
    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/sso` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/sso` |

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Cerner Central 支援小組](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations)以取得這些值。

1. 在 [SAML 簽署憑證] 區段中，按一下「複製」按鈕複製「應用程式同盟中繼資料 URL」，並將它貼到 [記事本]。

    ![設定單一登入](./media/cernercentral-tutorial/tutorial_metadataurl.png)

1. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/cernercentral-tutorial/tutorial_general_400.png)

1. 為了要在 **Cerner Central** 端設定單一登入，您必須將「應用程式同盟中繼資料 URL」傳送給 [Cerner Central 支援小組](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations) \(英文\)。 由他們設定應用程式端的單一登入以完成整合。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/cernercentral-tutorial/create_aaduser_01.png) 

1. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![建立 Azure AD 測試使用者](./media/cernercentral-tutorial/create_aaduser_02.png) 

1. 按一下 [新增] 開啟 [使用者] 對話方塊。

    ![建立 Azure AD 測試使用者](./media/cernercentral-tutorial/create_aaduser_03.png) 

1. 在 [使用者]  對話頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/cernercentral-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 文字方塊中，輸入 Britta Simon 的「電子郵件地址」。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="creating-a-cerner-central-test-user"></a>建立 Cerner Central 測試使用者

**Cerner Central** 應用程式可允許從任何同盟身分識別提供者進行驗證。 如果使用者能夠登入應用程式首頁，即表示他們已同盟，而不需進行任何手動佈建。 您可以在[這裡](cernercentral-provisioning-tutorial.md)找到關於如何設定自動使用者佈建的更多詳細資料。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Cerner Central 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200]

**若要將 Britta Simon 指派給 Cerner Central ，執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201]

1. 在應用程式清單中，選取 [Cerner Central]。

    ![設定單一登入](./media/cernercentral-tutorial/tutorial_cernercentral_app.png)

1. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202]

1. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

1. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

1. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

1. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。

### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [Cerner Central] 圖格時，應該會自動登入您的 Cerner Central 應用程式。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [設定使用者佈建](cernercentral-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/cernercentral-tutorial/tutorial_general_01.png
[2]: ./media/cernercentral-tutorial/tutorial_general_02.png
[3]: ./media/cernercentral-tutorial/tutorial_general_03.png
[4]: ./media/cernercentral-tutorial/tutorial_general_04.png

[100]: ./media/cernercentral-tutorial/tutorial_general_100.png

[200]: ./media/cernercentral-tutorial/tutorial_general_200.png
[201]: ./media/cernercentral-tutorial/tutorial_general_201.png
[202]: ./media/cernercentral-tutorial/tutorial_general_202.png
[203]: ./media/cernercentral-tutorial/tutorial_general_203.png
