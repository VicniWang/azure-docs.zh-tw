---
title: 教學課程：設定 Cisco Spark 來搭配 Azure Active Directory 進行自動使用者佈建 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 Cisco Spark。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03c45a19c8f6bbce2ffdfff0758eb3e57203b7cf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211201"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>教學課程：設定 Cisco Spark 來自動佈建使用者


本教學課程旨在示範設定 Azure AD 以將使用者自動佈建及取消佈建至 Cisco Spark 時，在 Cisco Spark 與 Azure Active Directory (Azure AD) 中要執行的步驟。


> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>先決條件

此教學課程中概述的案例假設您已經具有下列先決條件：

*   Azure AD 租用戶
*   Cisco Spark 租用戶
*   Cisco Spark 中具有管理員權限的使用者帳戶


> [!NOTE]
> Azure AD 佈建整合依賴 [Cisco Spark Webservice](https://developer.webex.com/getting-started.html) (可供 Cisco Spark 小組使用)。

## <a name="adding-cisco-spark-from-the-gallery"></a>從資源庫新增 Cisco Spark
設定 Cisco Spark 來搭配 Azure AD 進行自動使用者佈建之前，您必須從 Azure AD 應用程式庫將 Cisco Spark 新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Cisco Spark，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式] > [所有應用程式]。

    ![企業應用程式區段][2]

3. 若要新增 Cisco Spark，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Cisco Spark**。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. 在結果窗格中，選取 [Cisco Spark]，然後按一下 [新增] 按鈕，將 Cisco Spark 新增至您的 SaaS 應用程式清單。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>將使用者指派給 Cisco Spark

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者需要存取 Cisco Spark。 決定之後，即可遵循這裡的指示，將這些使用者指派給 Cisco Spark：

*   [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>將使用者指派給 Cisco Spark 的重要祕訣

*   建議將單一 Azure AD 使用者指派給 Cisco Spark，以測試自動使用者佈建設定。 其他使用者可能會稍後再指派。

*   將使用者指派給 Cisco Spark 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (若有)。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>設定對 Cisco Spark 的自動使用者佈建

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者指派，在 Cisco Spark 中建立、更新及停用使用者。


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>若要在 Azure AD 中為 Cisco Spark 設定自動使用者佈建：


1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [Azure Active Directory] > [企業應用程式] > [所有應用程式]。

2. 從您的 SaaS 應用程式清單中選取 [Cisco Spark]。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. 選取 [佈建] 索引標籤。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 [管理員認證] 區段下，輸入 Cisco Spark 帳戶的 [租用戶 URL] 和 [祕密權杖]。

    *   在 [租用戶 URL] 欄位中，填入您租用戶的 Cisco Spark SCIM API URL，格式為 `https://api.ciscospark.com/v1/scim/[Tenant ID]/`，其中 `[Tenant ID]` 為英數字元字串，如步驟 6 所述。

    *   在 [祕密權杖] 欄位中，填入祕密權杖，如步驟 6 所述。

1. 使用您的系統管理員帳戶登入 [Cisco Spark 開發人員網站](https://developer.webex.com/)，即可找到您 Cisco Spark 帳戶的 [租用戶識別碼] 和 [祕密權杖]。 登入之後 -
    * 移至 [Getting Started](https://developer.webex.com/getting-started.html) (開始使用) 頁面
    * 向下捲動至 [Authentication](https://developer.webex.com/getting-started.html#authentication) (驗證) 區段
    ![Cisco Spark 驗證權杖](./media/cisco-spark-provisioning-tutorial/SecretToken.png)
    * 方塊中的英數字元字串就是您的**祕密權杖**。 請將此權杖複製到剪貼簿
    * 移至 [Get My Own Details](https://developer.webex.com/endpoint-people-me-get.html) (取得我自己的詳細資料) 頁面
        * 確定 [Test Mode] \(測試模式\) 為 [ON] \(開啟\)
        * 鍵入 "Bearer" 單字，後面接著一個空格，然後將「祕密權杖」貼到 ![Cisco Spark 驗證權杖](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png) [Authorization] \(授權\) 欄位中
        * 按一下 [執行]
    * 在右側的回應文字中，[租用戶識別碼] 會顯示為 "orgId"：

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. 填入步驟 5 所示的欄位後，按一下 [測試連線] 以確定 Azure AD 可以連線到 Cisco Spark。 如果連線失敗，請確定您的 Cisco Spark 帳戶具有系統管理員權限，然後再試一次。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. 按一下 [檔案] 。

10. 在 [對應] 區段下，選取 [將 Azure Active Directory 使用者同步至 Cisco Spark]。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Cisco Spark 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Cisco Spark 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要為 Cisco Spark 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. 透過在 [設定] 區段的 [範圍] 中選擇所需的值，定義要佈建到 Cisco Spark 的使用者和/或群組。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![Cisco Spark 佈建](./media/cisco-spark-provisioning-tutorial/Save.png)


此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，然後遵循連結來佈建活動報告，此報告會描述 Azure AD 佈建服務在 Cisco Spark 上執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Cisco Spark 目前處於 Cisco 的早期現場測試 (EFT) 階段。 如需詳細資訊，請連絡 [Cisco 的支援小組](https://www.webex.co.in/support/support-overview.html)。 

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
