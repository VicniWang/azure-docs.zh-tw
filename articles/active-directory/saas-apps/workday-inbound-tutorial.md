---
title: 教學課程︰以 Azure Active Directory 設定 Workday 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 Workday。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e209fe0486b72c14912fd0af1b29c878e4b4545
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340105"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>教學課程：設定 Workday 來自動佈建使用者

本教學課程的目標是要說明將人員設定檔從 Workday 匯入到 Active Directory 和 Azure Active Directory，以及視需要將電子郵件地址回寫至 Workday，所需執行的步驟。

## <a name="overview"></a>概觀

[Azure Active Directory 使用者佈建服務](../manage-apps/user-provisioning.md)與 [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 整合以佈建使用者帳戶。 Azure AD 使用此連接啟用下列使用者佈建工作流程：

* **將使用者佈建至 Active Directory** - 將選取的使用者集合從 Workday 佈建至一或多個 Active Directory 網域。

* **將僅限雲端使用者佈建至 Azure Active Directory** - 在未使用內部部署 Active Directory 的情況下，使用 Azure AD 使用者佈建服務可以直接將使用者從 Workday 佈建至 Azure Active Directory。

* **將電子郵件地址回寫至 Workday** - Azure AD 使用者佈建服務可以將 Azure AD 使用者電子郵件地址回寫至 Workday。

### <a name="what-human-resources-scenarios-does-it-cover"></a>它涵蓋了哪些人力資源案例？

Azure AD 使用者佈建服務支援的 Workday 使用者佈建工作流程，可讓下列人力資源和身分識別生命週期管理案例的自動化：

* **雇用新員工** - 將新員工新增至 Workday 時，系統會在 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../manage-apps/user-provisioning.md)中自動建立使用者帳戶，並將電子郵件地址寫回 Workday。

* **員工屬性和設定檔更新** - 在 Workday 中更新員工記錄時 (例如姓名、職稱或經理)，系統會在 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../manage-apps/user-provisioning.md)中自動更新其使用者帳戶。

* **員工離職** - 在 Workday 中將員工設定為離職時，系統會在 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../manage-apps/user-provisioning.md)中自動停用其使用者帳戶。

* **重新雇用員工** - 在 Workday 中重新雇用員工時，系統會自動重新啟用其舊帳戶或將其重新佈建 (取決於您的喜好設定) 至 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../manage-apps/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

這個新的 Workday 的使用者佈建解決方案目前處於公開預覽狀態，最適合用於：

* 需要以預先建置的雲端式解決方案進行 Workday 使用者佈建的組織

* 需要直接將使用者從 Workday 佈建到 Active Directory 或 Azure Active Directory 的組織

* 需要使用從 Workday HCM 模組取得的資料來佈建使用者的組織 (請參閱 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* 需要僅根據在 Workday HCM 模組中偵測到的變更資訊聯結、移動及保留使用者，使其同步至一或多個 Active Directory 樹系、網域和 OU 的組織 (請參閱 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* 使用 Office 365 處理電子郵件的組織

## <a name="solution-architecture"></a>方案架構

本節針對常見的混合式環境，說明端對端使用者佈建方案架構。 有兩個相關的流程：

* **授權 HR 資料流程 – 從 Workday 到內部部署 Active Directory：** 在此流程中，人員事件 (例如新雇用、調動、解雇) 會先發生在雲端 Workday HR 租用戶，然後事件資料會透過 Azure AD 和佈建代理程式流入內部部署 Active Directory。 視事件而定，它可能會在 AD 中產生建立/更新/啟用/停用作業。
* **電子郵件回寫流程 – 從內部部署 Active Directory 到 Workday：** 一旦在 Active Directory 中完成建立帳號之後，它就會透過 Azure AD Connect 與 Azure AD 同步處理，而來自 Active Directory 的電子郵件屬性可回寫到 Workday。

![概觀](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>端對端使用者資料流程

1. HR 小組在 Workday HCM 中執行人員異動 (新進人員/異動人員/離職人員或新雇用/異動/解雇)
2. Azure AD 佈建服務會執行排程好的 Workday HR 身分識別同步處理作業，並找出需要處理以便與內部部署 Active Directory 同步的變更。
3. Azure AD 佈建服務會使用包含 AD 帳戶建立/更新/啟用/停用作業的要求承載，叫用內部部署 AAD Connect 佈建代理程式。
4. Azure AD Connect 佈建代理程式使用服務帳戶來新增/更新 AD 帳戶資料。
5. Azure AD Connect / AD 同步引擎會執行差異同步來提取 AD 中的更新。
6. Active Directory 會與 Azure Active Directory 同步更新。
7. 如果已設定 Workday 回寫連接器，它會將電子郵件屬性回寫到 Workday (根據所使用的比對屬性)。

## <a name="planning-your-deployment"></a>規劃您的部署

開始進行 Workday 整合之前，請檢查下列必要條件，並閱讀下列有關如何讓目前的 Active Directory 結構和使用者佈建需求與 Azure Active Directory 提供解決方案相符的指導。 此外，也有提供一個含規劃工作表的全方位[部署方案](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)，可協助您與 Workday 整合合作夥伴和 HR 專案關係人共同作業。

本節涵蓋下列規劃層面：

* [先決條件](#prerequisites)
* [選取要部署的佈建連接器應用程式](#selecting-provisioning-connector-apps-to-deploy)
* [Azure AD Connect 佈建代理程式的部署規劃](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [與多個 Active Directory 網域整合](#integrating-with-multiple-active-directory-domains)
* [規劃 Workday 至 Active Directory 的使用者屬性對應和轉換](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

* 具有全域管理員存取權的有效 Azure AD Premium P1 或更高等級訂用帳戶
* 可供測試和整合之用的 Workday 實作租用戶
* 可供測試之用的 Workday 系統管理員權限，以建立系統整合使用者和進行變更以測試員工資料
* 針對佈建至 Active Directory 的使用者，必須要有執行 Windows Server 2012 或更新版本且含有 .NET 4.7.1+ 執行階段的伺服器，才能裝載[內部部署佈建代理程式](https://go.microsoft.com/fwlink/?linkid=847801)
* 可在 Active Directory 與 Azure AD 之間同步處理使用者的 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>選取要部署的佈建連接器應用程式

為了加速 Workday 和 Active Directory 之間的佈建工作流程，Azure AD 提供可以從 Azure AD 應用程式庫新增的多個佈建連接器應用程式：

![AAD 應用程式庫](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday 至 Active Directory 使用者佈建** - 此應用程式可協助將使用者帳戶從 Workday 佈建至單一 Active Directory 網域。 如果您擁有多個網域，則可以針對要進行佈建的每個 Active Directory 網域，從 Azure AD 應用程式庫新增此應用程式的執行個體。

* **Workday 至 Azure AD 使用者佈建** -雖然將 Active Directory 使用者同步至 Azure Active Directory 時，應該使用 AAD Connect 這項工具，但此應用程式可用來協助將僅限雲端的使用者從 Workday 佈建至單一 Azure Active Directory 租用戶。

* **Workday 回寫** - 此應用程式可協助將使用者的電子郵件地址從 Azure Active Directory 回寫至 Workday。

> [!TIP]
> 您可以使用一般「Workday」應用程式設定 Workday 與 Azure Active Directory 之間的單一登入。

請使用下方的決策流程圖來識別哪些 Workday 佈建應用程式與您的案例相關。
    ![決策流程圖](./media/workday-inbound-tutorial/wday_app_flowchart.png "決策流程圖")

請使用目錄來移至本教學課程的相關小節。

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Azure AD Connect 佈建代理程式的部署規劃

> [!NOTE]
> 只有當您打算將 Workday 部署至「Active Directory 使用者佈建應用程式」時，才與本節相關。 如果您要將「Workday 回寫」或 Workday 部署至「Azure AD 使用者佈建應用程式」，則可以略過本節。

「Workday 至 AD 使用者佈建」解決方案要求必須在執行 Windows 2012 R2 或更新版本且至少具有 4 GB RAM 和 .NET 4.7.1+ 執行階段的伺服器上部署一或多個「佈建代理程式」。 在安裝佈建代理程式之前，您必須將下列事項列入考量：

* 確定執行佈建代理程式的主機伺服器具有目標 AD 網域的網路存取權限
* 「佈建代理程式設定精靈」會向 Azure AD 租用戶註冊代理程式，且註冊程序需要透過 SSL 連接埠 443 存取 *.msappproxy.net。 確定輸出防火牆規則已備妥可使用此通訊。 此代理程式支援[連出 HTTPS Proxy 設定](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)。
* 佈建代理程式使用服務帳戶與內部部署 AD 網域通訊。 在安裝代理程式之前，建議您先建立具有網域系統管理員權限且密碼不會過期的服務帳戶。  
* 在佈建代理程式設定期間，您可以選取應處理佈建要求的網域控制站。 如果您有數個分布在不同地理位置的網域控制站，在與您慣用之網域控制站的相同網站中安裝佈建代理程式，可改善端對端方案的可靠性和效能
* 如需高可用性，您可以部署多個佈建代理程式並加以註冊，來處理相同的內部部署 AD 網域集合。

> [!IMPORTANT]
> 在生產環境中，Microsoft 建議 Azure AD 租用戶設定至少 3 個佈建代理程式以實現高可用性。

### <a name="integrating-with-multiple-active-directory-domains"></a>與多個 Active Directory 網域整合

> [!NOTE]
> 只有當您打算將 Workday 部署至「Active Directory 使用者佈建應用程式」時，才與本節相關。 如果您要將「Workday 回寫」或 Workday 部署至「Azure AD 使用者佈建應用程式」，則可以略過本節。

視您的 Active Directory 拓撲而定，您將需要決定要設定的「使用者佈建連接器應用程式」數目和「佈建代理程式」數目。 以下列出一些您在規劃部署時可以參考的常見部署模式。

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>部署案例 #1：單一 Workday 租用戶 -> 單一 AD 網域

在此案例中，您有一個 Workday 租用戶並想要將使用者佈建至單一 AD 網域。 以下是針對此部署建議的生產環境設定。

|   |   |
| - | - |
| 沒有。 內部部署環境部署的代理程式數目 | 3 (適用於高可用性和容錯移轉) |
| 沒有。 Azure 入口網站中設定的「Workday 至 AD 使用者佈建應用程式」數目 | 1 |

  ![案例 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>部署案例 #2：單一 Workday 租用戶 -> 多個 AD 子網域

此案例牽涉到將使用者從 Workday 佈建到樹系中的多個目標 AD 子網域。 以下是針對此部署建議的生產環境設定。

|   |   |
| - | - |
| 沒有。 內部部署環境部署的代理程式數目 | 3 (適用於高可用性和容錯移轉) |
| 沒有。 Azure 入口網站中設定的「Workday 至 AD 使用者佈建應用程式」數目 | 每一子網域一個應用程式 |

  ![案例 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>部署案例 #3：單一 Workday 租用戶 -> 不相鄰的 AD 樹系

此案例牽涉到將使用者從 Workday 佈建到不相鄰 AD 樹系中的網域。 以下是針對此部署建議的生產環境設定。

|   |   |
| - | - |
| 沒有。 內部部署環境部署的代理程式數目 | 每一不相鄰的 AD 樹系 3 個 |
| 沒有。 Azure 入口網站中設定的「Workday 至 AD 使用者佈建應用程式」數目 | 每一子網域一個應用程式 |

  ![案例 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>規劃 Workday 至 Active Directory 的使用者屬性對應和轉換

> [!NOTE]
> 只有當您打算將 Workday 部署至「Active Directory 使用者佈建應用程式」時，才與本節相關。 如果您要將「Workday 回寫」或 Workday 部署至「Azure AD 使用者佈建應用程式」，則可以略過本節。

在設定對 Active Directory 網域的使用者佈建之前，請考量下列問題。 這些問題的答案將會決定您的篩選範圍和屬性對應必須如何設定。

* **Workday 中的哪些使用者需要佈建到此 Active Directory 樹系？**

  * *範例：Workday "Company" 屬性包含 "Contoso" 值，且 "Worker_Type" 屬性包含 "Regular" 的使用者*

* **使用者如何路由到不同的組織單位 (OU) 中？**

  * *範例：使用者路由到對應於辦公室所在地點的 OU，如 Workday "Municipality" 和 "Country_Region_Reference" 屬性所定義*

* **下列屬性應如何填入 Active Directory 中？**

  * 一般名稱 (cn)
    * *範例：使用 Workday User_ID 值，如人力資源所設定*

  * 員工識別碼 (employeeId)
    * *範例：使用 Workday Worker_ID 值*

  * SAM 帳戶名稱 (sAMAccountName)
    * *範例：使用 Workday User_ID 值，透過 Azure AD 佈建運算式進行篩選以移除不合法的字元*

  * 使用者主體名稱 (userPrincipalName)
    * *範例：使用 Workday User_ID 值，並使用 Azure AD 佈建運算式附加網域名稱*

* **如何在 Workday 與 Active Directory 之間比對使用者？**

  * *範例：具有特定 Workday "Worker_ID" 值的使用者與 "employeeID" 具有相同值的 Active Directory 使用者相比對。如果在 Active Directory 中找不到 Worker_ID 值，則建立新的使用者。*
  
* **Active Directory 樹系是否已包含比對邏輯運作所需的使用者識別碼？**

  * *範例：如果此設定是新的 Workday 部署，建議您為 Active Directory 預先填入正確的 Workday Worker_ID 值 (或選擇的唯一識別碼值)，讓比對邏輯越簡單越好。*

如何安裝及設定這些特殊佈建連接器應用程式是本教學課程其餘章節的主題。 您選擇要設定的應用程式將取決於需要進行佈建的系統，以及環境中的 Active Directory 網域和 Azure AD 租用戶數量。

## <a name="configure-integration-system-user-in-workday"></a>在 Workday 中設定整合系統使用者

有一個所有 Workday 佈建連接器常見的需求，就是它們必須要有 Workday 系統整合系統使用者的認證，才能連線至「Workday 人力資源 API」。 本節說明如何在 Workday 中建立整合系統使用者，並且包含下列各節：

* [建立整合系統使用者](#creating-an-integration-system-user)
* [建立整合安全性群組](#creating-an-integration-security-group)
* [設定網域安全性原則權限](#configuring-domain-security-policy-permissions)
* [設定商務程序安全性原則權限](#configuring-business-process-security-policy-permissions)
* [啟用安全性原則變更](#activating-security-policy-changes)

> [!NOTE]
> 您可以略過此程序，改用 Workday 全域系統管理員帳戶做為系統整合帳戶。 這可能會在示範中正常運作，但不建議用於生產環境部署。

### <a name="creating-an-integration-system-user"></a>建立整合系統使用者

**建立整合系統使用者：**

1. 使用系統管理員帳戶登入 Workday 租用戶。 在 [Workday 應用程式] 中，在搜尋方塊內輸入 create user，然後按一下 [建立整合系統使用者]。

    ![建立使用者](./media/workday-inbound-tutorial/wd_isu_01.png "建立使用者")
2. 為新的「整合系統使用者」 提供使用者名稱和密碼來完成「建立整合系統使用者」  工作。  
  
* 保持 [下次登入時要求新密碼] 選項未核取，因為該使用者會以程式設計的方式登入。
* 保持 [工作階段逾時分鐘數] 為預設值 [0]，這會防止使用者的工作階段提早逾時。
* 選取選項 [不允許 UI 工作階段]，因為它會提供一層額外的安全性，防止使用整合系統密碼的使用者登入 Workday。

    ![建立整合系統使用者](./media/workday-inbound-tutorial/wd_isu_02.png "建立整合系統使用者")

### <a name="creating-an-integration-security-group"></a>建立整合安全性雲組

在此步驟中，您將在 Workday 中建立不受限制或受限制的整合系統安全性群組，並將上個步驟中建立的整合系統使用者指派給此群組。

**建立安全性群組：**

1. 在搜尋方塊中輸入 create security group，然後按一下 [建立安全性群組] 連結。

    ![建立安全性群組](./media/workday-inbound-tutorial/wd_isu_03.png "建立安全性群組")
2. 完成**建立安全性群組**工作。 

  * Workday 中有兩種類型的安全性群組：
    * **不受限制：** 此安全性群組的所有成員都可以存取此安全性群組所保護的所有資料執行個體。
    * **受限制：** 所有安全性群組成員都只能對此安全性群組所能存取的一小部分資料執行個體 (資料列) 進行內容相關的存取。
  * 請洽詢您的 Workday 整合合作夥伴，以選取適用於整合的安全性群組類型。
  * 知道群組類型之後，請從 [Type of Tenanted Security Group] \(租用安全性群組類型\) 下拉式清單中選取 [Integration System Security Group (Unconstrained)] \(整合系統安全性群組 (不受限制)\) 或 [Integration System Security Group (Constrained)] \(整合系統安全性群組 (受限制)\)。

    ![建立安全性群組](./media/workday-inbound-tutorial/wd_isu_04.png "建立安全性群組")

3. 安全性群組成功建立之後，您會看到可用來將成員指派到安全性群組的頁面。 請將在上個步驟中建立的新整合系統使用者新增到此安全性群組。 如果您使用「受限制」安全性群組，則也需要選取適當的組織範圍。

    ![編輯安全性群組](./media/workday-inbound-tutorial/wd_isu_05.png "編輯安全性群組")

### <a name="configuring-domain-security-policy-permissions"></a>設定網域安全性原則權限

在此步驟中，您會為安全性群組的人員資料授與「網域安全性」原則權限。

**設定網域安全性原則權限：**

1. 在搜尋方塊中輸入**網域安全性設定**，然後按一下 [網域安全性設定報告] 上的連結。  

    ![網域安全性原則](./media/workday-inbound-tutorial/wd_isu_06.png "網域安全性原則")  
2. 在 [網域] 文字方塊中，搜尋下列網域，並將它們一一新增到篩選。  
   * *外部帳戶佈建*
   * *人員資料：公用人員報告*
   * *人員資料：工作連絡人資訊*
   * *人員資料：所有職位*
   * *人員資料：目前人員配置資訊*
   * *人員資料：人員個人檔案的職稱*

    ![網域安全性原則](./media/workday-inbound-tutorial/wd_isu_07.png "網域安全性原則")  

    ![網域安全性原則](./media/workday-inbound-tutorial/wd_isu_08.png "網域安全性原則") 

    按一下 [確定]。

3. 在顯示的報表中，選取顯示在 [外部帳戶佈建] 旁邊的省略號 (...)，然後按一下功能表選項 [網域] -> [編輯安全性原則權限]

    ![網域安全性原則](./media/workday-inbound-tutorial/wd_isu_09.png "網域安全性原則")  

4. 在 [編輯網域安全性原則權限] 頁面上，向下捲動至 [整合權限] 區段。 按一下 [+] 符號，將整合系統群組新增到具有**取得**和**放入**整合權限的安全性群組清單中。

    ![編輯權限](./media/workday-inbound-tutorial/wd_isu_10.png "編輯權限")  

5. 按一下 [+] 符號，將整合系統群組新增到具有**取得**和**放入**整合權限的安全性群組清單中。

    ![編輯權限](./media/workday-inbound-tutorial/wd_isu_11.png "編輯權限")  

6. 針對其餘的各個安全性原則重複前述的步驟 3-5：

   | 作業 | 網域安全性原則 |
   | ---------- | ---------- |
   | Get 和 Put | 人員資料：公用人員報告 |
   | Get 和 Put | 人員資料：工作連絡人資訊 |
   | 取得 | 人員資料：所有職位 |
   | 取得 | 人員資料：目前人員配置資訊 |
   | 取得 | 人員資料：人員個人檔案的職稱 |

### <a name="configuring-business-process-security-policy-permissions"></a>設定商務程序安全性原則權限

在此步驟中，您會為安全性群組的人員資料授與「商務程序安全性」原則權限。 這是設定「Workday 回寫」應用程式連接器的必要步驟。

**設定商務程序安全性原則權限：**

1. 在搜尋方塊中輸入**商務程序原則**，然後按一下連結 [編輯商務程序安全性原則] 工作。  

    ![商務程序安全性原則](./media/workday-inbound-tutorial/wd_isu_12.png "商務程序安全性原則")  

2. 在 [商務程序類型] 文字方塊中，搜尋*連絡人*，然後選取 [連絡人變更] 商務程序，並按一下 [確定]。

    ![商務程序安全性原則](./media/workday-inbound-tutorial/wd_isu_13.png "商務程序安全性原則")  

3. 在 [編輯商務程序安全性原則] 頁面上，捲動到 [維護連絡人資訊 (Web 服務)] 區段。

    ![商務程序安全性原則](./media/workday-inbound-tutorial/wd_isu_14.png "商務程序安全性原則")  

4. 選取新的整合系統安全性群組，並新增到可起始 Web 服務要求的安全性群組清單。 按一下 [完成]。 

    ![商務程序安全性原則](./media/workday-inbound-tutorial/wd_isu_15.png "商務程序安全性原則")  

### <a name="activating-security-policy-changes"></a>啟用安全性原則變更

**啟用安全性原則變更：**

1. 在搜尋方塊中輸入 activate，然後按一下 [啟用擱置的安全性原則變更] 連結。

    ![啟用](./media/workday-inbound-tutorial/wd_isu_16.png "啟用")

1. 輸入供稽核用的註解並按一下 [確定] 按鈕，以開始「啟用擱置的安全性原則變更」工作。
1. 在下一個畫面核取 [確認] 核取方塊，然後按一下 [確定] 以完成工作。

    ![啟用擱置的安全性](./media/workday-inbound-tutorial/wd_isu_18.png "啟用擱置的安全性")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>設定將使用者從 Workday 佈建至 Active Directory

本節提供將使用者帳戶從 Workday 佈建至整合範圍內每個 Active Directory 網域的步驟。

* [安裝並設定內部部署佈建代理程式](#part-1-install-and-configure-on-premises-provisioning-agents)
* [新增佈建連接器應用程式和建立 Workday 連線](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [設定屬性對應](#part-3-configure-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>第 1 部分：安裝並設定內部部署佈建代理程式

若要佈建至內部部署的 Active Directory，您必須在具有 .NET 4.7.1+ Framework 並可存取所需 Active Directory 網域的伺服器上安裝代理程式。

> [!TIP]
> 您可以使用[這裡](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)提供的指示，檢查您伺服器上的 .NET Framework 版本。
> 如果伺服器未安裝 .NET 4.7.1 或更新版本，您可以從[這裡](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)下載。  

部署 .NET 4.7.1+ 之後，您便可以**[下載這裡的內部部署佈建代理程式](https://go.microsoft.com/fwlink/?linkid=847801)**，然後依照下方提供的步驟來完成代理程式設定。

1. 登入您要安裝新代理程式的 Windows Server。
2. 啟動佈建代理程式安裝程式，同意條款並按一下 [安裝] 按鈕。

   ![安裝畫面](./media/workday-inbound-tutorial/pa_install_screen_1.png "安裝畫面")
3. 安裝完成之後，精靈將會啟動，而您將會看到 [連線 Azure AD] 畫面。 按一下 [驗證] 按鈕以連線到您的 Azure AD 執行個體。

   ![連線 Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "連線 Azure AD")
1. 使用全域管理員認證驗證您的 Azure AD 執行個體。

   ![管理員驗證](./media/workday-inbound-tutorial/pa_install_screen_3.png "管理員驗證")

> [!NOTE]
> Azure AD 系統管理員認證僅供用來連線至您的 Azure AD 租用戶。 代理程式不會將認證儲存在本機伺服器上。

1. 成功使用 Azure AD 驗證之後，您會看到 [連線 Active Directory] 畫面。 在此步驟中，請輸入您的 AD 網域名稱，然後按一下 [Add Directory] \(新增目錄\) 按鈕。

   ![新增目錄](./media/workday-inbound-tutorial/pa_install_screen_4.png "新增目錄")
  
1. 現在系統會提示您輸入認證，才能連線至 AD 網域。 在相同畫面中，您可以使用 [選取網域控制站優先權] 來指定代理程式應用來傳送佈建要求的網域控制站。

   ![網域認證](./media/workday-inbound-tutorial/pa_install_screen_5.png)
1. 設定網域之後，安裝程式會顯示已設定的網域清單。 在此畫面上，您可以重複步驟 #5 和 #6 來新增更多網域，或是按一下 [下一步] 來繼續進行代理程式註冊。

   ![已設定的網域](./media/workday-inbound-tutorial/pa_install_screen_6.png "已設定的網域")

   > [!NOTE]
   > 如果您有多個 AD 網域 (例如 a.contoso.com、emea.contoso.com)，則請將每個網域個別新增至清單。 只新增父系網域 (例如 contoso.com) 是不夠的。 您必須向代理程式註冊每個子網域。
1. 檢閱設定詳細資料，並按一下 [確認] 以註冊代理程式。
  
   ![確認畫面](./media/workday-inbound-tutorial/pa_install_screen_7.png "確認畫面")
1. 設定精靈會顯示代理程式註冊的進度。
  
   ![代理程式註冊](./media/workday-inbound-tutorial/pa_install_screen_8.png "代理程式註冊")
1. 一旦代理程式註冊成功之後，您可以按一下 [結束] 來結束精靈。
  
   ![結束畫面](./media/workday-inbound-tutorial/pa_install_screen_9.png "結束畫面")
1. 開啟 [服務] 嵌入式管理單元，並尋找名稱為「Microsoft Azure AD Connect 佈建代理程式」的服務，以確認此代理程式的安裝及確定它是否正在執行
  
   ![服務](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 2 部分：新增佈建連接器應用程式和建立 Workday 連線

**若要設定 Workday 至 Active Directory 佈建：**

1. 移至 <https://portal.azure.com>。

2. 在左側導覽列中，選取 [Azure Active Directory]

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋 **Workday Provisioning to Active Directory**，並從資源庫新增該應用程式。

6. 新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7. 將 [佈建模式] 變更為 [自動]

8. 完成 [系統管理員認證] 區段，如下所示：

   * **管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，需附加租用戶網域名稱。 這應該看起來像這樣：**username@tenant_name**

   * **管理員密碼** – 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL** – 輸入租用戶 Workday Web 服務端點的 URL。 此值應該看起來像這樣： https://wd3-impl-services1.workday.com/ccx/service/contoso4，其中會以您的正確租用戶名稱取代 *contoso4*，以及以正確的環境字串取代 *wd3-impl*。

   * **Active Directory 樹系** – 向代理程式註冊的 Active Directory 網域「名稱」。 請使用下拉式清單來選取用於佈建的目標網域。 此值通常是如下的字串：*contoso.com*

   * **Active Directory 容器 -** 輸入容器 DN，其中是代理程式預設應建立使用者帳戶的位置。
        範例：*OU=Standard Users,OU=Users,DC=contoso,DC=test*
> [!NOTE]
> 如果並未在屬性對應中設定 *parentDistinguishedName* 屬性，此設定僅適用於使用者帳戶建立。 此設定不適用於使用者搜尋或更新作業。 整個網域的子樹狀會落在搜尋作業的範圍中。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

> [!NOTE]
> 如果佈建作業進入[隔離](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine)狀態，Azure AD 佈建服務會傳送電子郵件通知。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查代理程式上設定的 Workday 認證和 AD 認證是否有效。

     ![Azure 入口網站](./media/workday-inbound-tutorial/wd_1.png)

   * 順利儲存認證之後，[對應] 區段會顯示 [Synchronize Workday Workers to On Premises Active Directory] \(將 Workday 人員同步至內部部署 Active Directory\) 預設對應

### <a name="part-3-configure-attribute-mappings"></a>第 3 部分：設定屬性對應

在本節中，您會設定使用者資料從 Workday 流動至 Active Directory 的方式。

1. 在 [佈建] 索引標籤的 [對應] 底下，按一下 [Synchronize Workday Workers to On Premises Active Directory] \(將 Workday 人員同步至內部部署 Active Directory\)。

2. 在 [來源物件範圍] 欄位中，您可以透過定義一組屬性型篩選，選取應該佈建至 AD 的 Workday 使用者集合範圍。 預設範圍是「Workday 中的所有使用者」。 範例篩選：

   * 範例：人員識別碼介於 1000000 到 2000000 之間的使用者範圍

      * 屬性：WorkerID

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅員工和非約聘人員

      * 屬性：EmployeeID

      * 運算子：IS NOT NULL

> [!TIP]
> 第一次設定佈建應用程式時，您將需要測試及確認屬性對應和運算式，以確保它提供您所需的結果。 Microsoft 建議您使用 [來源物件範圍] 底下的範圍篩選，利用幾個來自 Workday 的測試使用者來測試您的對應。 確認對應能夠運作之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

3. 在 [目標物件動作] 欄位中，您可以全域篩選在 Active Directory 上執行的動作。 最常見的動作是 [建立] 和 [更新]。

4. 在 [屬性對應] 區段中，您可以定義個別 Workday 屬性如何對應至 Active Directory 屬性。

5. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應] 以新增新對應。 個別屬性對應支援下列屬性：

      * **對應類型**

         * **直接** – 將 Workday 屬性的值原封不動地寫入至 AD 屬性

         * **常數** – 將靜態的常數字串值寫入至 AD 屬性

         * **運算式** – 可讓您根據一或多個 Workday 屬性，將自訂值寫入 AD 屬性。 [如需詳細資訊，請參閱這篇有關運算式的文章](../manage-apps/functions-for-customizing-application-data.md)。

      * **來源屬性** - 來自 Workday 的使用者屬性。 如果您要尋找的屬性不存在，請參閱[自訂 Workday 使用者屬性的清單](#customizing-the-list-of-workday-user-attributes)。

      * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

      * **目標屬性** – Active Directory 中的使用者屬性。

      * **使用此屬性比對物件** – 是否應該將此對應用於唯一識別 Workday 與 Active Directory 之間的使用者。 此值通常是在 Workday 的 [Worker ID] \(人員識別碼\) 欄位上設定的，這通常與 Active Directory 中的其中一個 [員工識別碼] 屬性對應。

      * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估任何進一步的比對屬性。

      * **套用此對應**

         * **一律** – 將此對應套用於使用者建立和更新動作

         * **僅限建立期間** - 僅將此對應套用於使用者建立動作

6. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

   ![Azure 入口網站](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>以下是 Workday 與 Active Directory 之間的一些範例屬性對應，以及一些常用運算式

* 對應至 *parentDistinguishedName* 屬性的運算式可用來根據一或多個 Workday 來源屬性，將使用者佈建至不同的 OU。 此處的範例會根據使用者的所在城市將使用者放在不同 OU 中。

* Active Directory 中的 *userPrincipalName* 屬性是使用重複資料刪除函式 [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) 來產生的，此函式會檢查目標 AD 網域中是否有所產生的值，且只有當該值是唯一值時，才會設定它。  

* [此為有關撰寫運算式的文件](../manage-apps/functions-for-customizing-application-data.md)。 本節包含說明如何移除特殊字元的範例。

| WORKDAY 屬性 | ACTIVE DIRECTORY 屬性 |  比對識別碼？ | 建立/更新 |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **是** | 僅於建立時寫入 |
| **PreferredNameData**    |  cn    |   |   僅於建立時寫入 |
| **SelectUniqueValue( Join("@", Join(".",  \[FirstName\], \[LastName\]), "contoso.com"), Join("@", Join(".",  Mid(\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join("@", Join(".",  Mid(\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | 僅於建立時寫入 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         僅於建立時寫入 |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | 建立 + 更新 |
| **名字**   | givenName       |     |    建立 + 更新 |
| **姓氏**   |   sn   |     |  建立 + 更新 |
| **PreferredNameData**  |  displayName |     |   建立 + 更新 |
| **Company**         | company   |     |  建立 + 更新 |
| **SupervisoryOrganization**  | department  |     |  建立 + 更新 |
| **ManagerReference**   | manager  |     |  建立 + 更新 |
| **BusinessTitle**   |  title     |     |  建立 + 更新 | 
| **AddressLineData**    |  streetAddress  |     |   建立 + 更新 |
| **Municipality**   |   l   |     | 建立 + 更新 |
| **CountryReferenceTwoLetter**      |   co |     |   建立 + 更新 |
| **CountryReferenceTwoLetter**    |  c  |     |         建立 + 更新 |
| **CountryRegionReference** |  st     |     | 建立 + 更新 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  建立 + 更新 |
| **PostalCode**  |   postalCode  |     | 建立 + 更新 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 建立 + 更新 |
| **Fax**      | facsimileTelephoneNumber     |     |    建立 + 更新 |
| **Mobile**  |    mobile       |     |       建立 + 更新 |
| **LocalReference** |  preferredLanguage  |     |  建立 + 更新 |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  建立 + 更新 |

完成屬性對應設定之後，您現在便可以[啟用及啟動使用者佈建服務](#enable-and-launch-user-provisioning)。

## <a name="configuring-user-provisioning-to-azure-ad"></a>設定將使用者佈建至 Azure AD

下列各節說明針對僅限雲端部署，設定將使用者從 Workday 佈建至 Azure AD 的步驟。

* [新增 Azure AD 佈建連接器應用程式和建立 Workday 連線](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [設定 Workday 與 Azure AD 的屬性對應](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> 如果您擁有需要佈建至 Azure AD 的僅限雲端使用者且沒有內部部署 Active Directory，請僅遵循下列程序。

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：新增 Azure AD 佈建連接器應用程式和建立 Workday 連接

**若要針對僅限雲端使用者設定 Workday 至 Azure Active Directory 佈建：**

1. 移至 <https://portal.azure.com>。

2. 在左側導覽列中，選取 [Azure Active Directory]

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋 **Workday to Azure AD Provisioning**，並從資源庫新增該應用程式。

6. 新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7. 將 [佈建模式] 變更為 [自動]

8. 完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 其應該類似於：username@contoso4

   * **管理員密碼** – 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL** – 輸入租用戶 Workday Web 服務端點的 URL。 此值應該看起來像這樣： https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources，其中會以您的正確租用戶名稱取代 *contoso4*，以及以正確的環境字串取代 *wd3-impl*。 如果不知道此 URL，請與您的 Workday 整合夥伴或支援代表合作，來判斷要使用的正確 URL。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

   * 按一下 [測試連線] 按鈕。

   * 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 Workday URL 和認證在 Workday 中是否有效。

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>第 2 部分：設定 Workday 與 Azure AD 的屬性對應

在本節中，您會針對僅限雲端使用者設定使用者資料從 Workday 流動至 Azure Active Directory 的方式。

1. 在 [佈建] 索引標籤的 [對應] 下，按一下 [將人員同步至 Azure AD]。

2. 在 [來源物件範圍] 欄位中，您可以透過定義一組屬性型篩選，選取應該佈建至 Azure AD 的 Workday 使用者集合範圍。 預設範圍是「Workday 中的所有使用者」。 範例篩選：

   * 範例：人員識別碼介於 1000000 到 2000000 之間的使用者範圍

      * 屬性：WorkerID

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅約聘人員和非正式員工

      * 屬性：ContingentID

      * 運算子：IS NOT NULL

3. 在 [目標物件動作] 欄位中，您可以全域篩選在 Azure AD 上執行的動作。 最常見的動作是 [建立] 和 [更新]。

4. 在 [屬性對應] 區段中，您可以定義個別 Workday 屬性如何對應至 Active Directory 屬性。

5. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應] 以新增新對應。 個別屬性對應支援下列屬性：

   * **對應類型**

      * **直接** – 將 Workday 屬性的值原封不動地寫入至 AD 屬性

      * **常數** – 將靜態的常數字串值寫入至 AD 屬性

      * **運算式** – 可讓您根據一或多個 Workday 屬性，將自訂值寫入 AD 屬性。 [如需詳細資訊，請參閱這篇有關運算式的文章](../manage-apps/functions-for-customizing-application-data.md)。

   * **來源屬性** - 來自 Workday 的使用者屬性。 如果您要尋找的屬性不存在，請參閱[自訂 Workday 使用者屬性的清單](#customizing-the-list-of-workday-user-attributes)。

   * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

   * **目標屬性** – Azure AD 中的使用者屬性。

   * **使用此屬性比對物件** – 是否應該使用此屬性來唯一識別 Workday 與 Azure AD 之間的使用者。 此值通常是在 Workday 的 [Worker ID] \(人員識別碼\) 欄位上設定的，這通常與 Azure AD 中的 [員工識別碼] 屬性 (新的) 或延伸屬性對應。

   * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。

   * **套用此對應**

     * **一律** – 將此對應套用於使用者建立和更新動作

     * **僅限建立期間** - 僅將此對應套用於使用者建立動作

6. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

完成屬性對應設定之後，您現在便可以[啟用及啟動使用者佈建服務](#enable-and-launch-user-provisioning)。

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>設定將電子郵件地址回寫至 Workday

請遵循下列指示來設定將使用者電子郵件地址從 Azure Active Directory 回寫至 Workday。

* [新增回寫連接器應用程式和建立 Workday 連線](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [設定回寫屬性對應](#part-2-configure-writeback-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：新增回寫連接器應用程式和建立 Workday 連線

**設定 Workday 回寫連接器：**

1. 移至 <https://portal.azure.com>。

2. 在左側導覽列中，選取 [Azure Active Directory]

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋 **Workday Writeback**，並從資源庫新增該應用程式。

6. 新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7. 將 [佈建模式] 變更為 [自動]

8. 完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 這應該看起來像這樣：*username@contoso4*

   * **管理員密碼** – 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL –** 輸入租用戶 Workday Web 服務端點的 URL。 此值應該看起來像這樣： https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources，其中會以您的正確租用戶名稱取代 *contoso4*，以及以正確的環境字串取代 *wd3-impl* (如有必要)。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 Workday URL 和認證在 Workday 中是否有效。

### <a name="part-2-configure-writeback-attribute-mappings"></a>第 2 部分：設定回寫屬性對應

在本節中，您將設定回寫屬性從 Azure AD 流向 Workday 的方式。

1. 在 [佈建] 索引標籤的 [對應] 底下，按一下 [Synchronize Azure Active Directory Users to Workday] \(將 Azure Active Directory 使用者同步至 Workday\)。

2. 在 [來源物件範圍] 欄位中，您可以視需要篩選 Azure Active Directory 中電子郵件地址應該回寫至 Workday 的使用者集合。 預設範圍是「Azure AD 中的所有使用者」。

3. 在 [屬性對應] 區段中，更新相符的識別碼，代表 Azure Active Directory 中儲存了 Workday 人員識別碼或員工識別碼的屬性。 常用的比對方法是將 Workday 人員識別碼或員工識別碼同步至 Azure AD 中的 extensionAttribute1-15，然後在 Azure AD 中使用此屬性再次比對 Workday 的使用者。

4. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

完成屬性對應設定之後，您現在便可以[啟用及啟動使用者佈建服務](#enable-and-launch-user-provisioning)。 

## <a name="enable-and-launch-user-provisioning"></a>啟用及啟動使用者佈建

在 Workday 佈建應用程式設定完成之後，您可以在 Azure 入口網站中開啟佈建服務。

> [!TIP]
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果有對應錯誤或 Workday 資料問題，則佈建作業可能失敗並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍] 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [檔案] 。

3. 此作業會啟動初始同步，所需花費的時數會視 Workday 租用戶中的使用者人數而定。 

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄] 索引標籤，查看佈建服務執行了哪些動作。 稽核記錄會列出佈建服務執行的所有個別同步處理事件，例如從 Workday 外部讀取了哪些使用者，接著又新增到或更新到 Active Directory 中。 如需有關如何檢閱稽核記錄及修正佈建錯誤的指示，請參閱＜疑難排解＞一節。

5. 在初始同步完成之後，它會在 [佈建] 索引標籤中寫入稽核摘要報告，如下所示。

   ![Azure 入口網站](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>常見問題集 (FAQ)

* **解決方案功能問題**
  * [處理來自 Workday 的新雇用人員時，解決方案如何在 Active Directory 中為新使用者帳戶設定密碼？](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [解決方案是否支援在佈建作業完成後傳送電子郵件通知？](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [如何管理新雇用人員的密碼交付，並安全地提供重設其密碼的機制？](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [解決方案是否會將 Workday 使用者設定檔快取在 Azure AD 雲端中，或快取在佈建代理程式層？](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [解決方案是否支援將內部部署 AD 群組指派給使用者？](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [解決方案使用哪些 Workday API 來查詢和更新 Workday 人員設定檔？](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [是否可以搭配兩個 Azure AD 租用戶來設定我的 Workday HCM 租用戶？](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [為什麼在已部署 Azure AD Connect 的情況下不支援「Workday 至 Azure AD」使用者佈建應用程式？](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [如何提出與 Workday 和 Azure AD 整合相關的改進建議或新功能要求？](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **佈建代理程式問題**
  * [佈建代理程式的 GA 版本是哪一版？](#what-is-the-ga-version-of-the-provisioning-agent)
  * [如何得知我的佈建代理程式版本？](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft 是否會自動推送佈建代理程式更新？](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [我是否可以將佈建代理程式安裝在執行 AAD Connect 的相同伺服器上？](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [如何將佈建代理程式設定成使用 Proxy 伺服器進行連出 HTTP 通訊？](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [如何確保佈建代理程式能夠與 Azure AD 租用戶進行通訊，而沒有任何防火牆封鎖代理程式所需的連接埠？](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [如何將與佈建代理程式相關的網域取消註冊？](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [如何將佈建代理程式解除安裝？](#how-do-i-uninstall-the-provisioning-agent)
  
* **Workday 至 AD 的屬性對應和設定問題**
  * [如何備份或匯出 Workday 佈建屬性對應和結構描述的工作複本？](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [我在 Workday 和 Active Directory 中有自訂屬性。如何設定讓解決方案使用我的自訂屬性？](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [我是否可以將使用者的照片從 Workday 佈建至 Active Directory？](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [如何根據使用者對公共用途的同意，從 Workday 同步行動電話號碼？](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [如何根據使用者的部門/國家 (地區)/縣市屬性設定 AD 中的顯示名稱格式，以及處理區域差異？](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [如何使用 SelectUniqueValue 來為 samAccountName 屬性產生唯一值？](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [如何移除含有變音符號的字元，並將其轉換成一般英文字母？](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>解決方案功能問題

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>處理來自 Workday 的新雇用人員時，解決方案如何在 Active Directory 中為新使用者帳戶設定密碼？

當內部部署佈建代理程式收到建立新 AD 帳戶的要求時，它會自動產生一個為滿足 AD 伺服器所定義之密碼複雜性需求而設計的複雜隨機密碼，並在使用者物件上設定此密碼。 任何地方都不會記錄此密碼。

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>解決方案是否支援在佈建作業完成後傳送電子郵件通知？

否，目前的版本不支援在完成佈建作業後傳送電子郵件通知。

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>如何管理新雇用人員的密碼交付，並安全地提供重設其密碼的機制？

新 AD 帳戶佈建所牽涉到的其中一個最後步驟，就是交付指派給使用者 AD 帳戶的暫時密碼。 許多企業仍使用傳統方法將暫時密碼交付給使用者的管理員，然後再由其將密碼交給新雇用/約聘人員。 此程序有固有的安全性缺陷，而您可以選擇使用 Azure AD 功能來實作更佳的方法。

在雇用程序中，HR 小組通常會執行背景檢查，並審查新雇用人員的行動電話號碼。 藉由「Workday 至 AD 使用者佈建」整合，您便可以在此事實基礎上進行建置，然後在第 1 天為使用者推出自助式密碼重設功能。 做法是將新雇用人員的「行動電話號碼」屬性從 Workday 傳播至 AD，然後再使用 AAD Connect 將該屬性從 AD 傳播至 Azure AD。 在 Azure AD 中有「行動電話號碼」之後，您可以為使用者帳戶啟用[自助式密碼重設 (SSPR)](../authentication/howto-sspr-authenticationdata.md)，讓新雇用人員能夠在第 1 天使用已註冊且已驗證的行動電話號碼來進行驗證。

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>解決方案是否會將 Workday 使用者設定檔快取在 Azure AD 雲端中，或快取在佈建代理程式層？

否，解決方案不會保有使用者設定檔快取。 Azure AD 佈建服務僅用來作為資料處理器，負責從 Workday 讀取資料並寫入至目標 Active Directory 或 Azure AD。 如需有關使用者隱私權和資料保留的詳細資料，請參閱[管理個人資料](#managing-personal-data)一節。

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>解決方案是否支援將內部部署 AD 群組指派給使用者？

目前不支援此功能。 建議的因應措施是部署 PowerShell 指令碼來查詢 Azure AD Graph API 端點是否有稽核記錄資料，然後使用該資料來觸發群組指派之類的案例。 此 PowerShell 指令碼可以連結至工作排程器，並部署在執行佈建代理程式的相同主機上。  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>解決方案使用哪些 Workday API 來查詢和更新 Workday 人員設定檔？

解決方案目前使用下列 Workday API：

* Get_Workers (v21.1)：用於擷取人員資訊
* Maintain_Contact_Information (v26.1)：用於「工作電子郵件回寫」功能

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>是否可以搭配兩個 Azure AD 租用戶來設定我的 Workday HCM 租用戶？

是，支援此設定。 以下是設定此案例的概要步驟：

* 部署佈建代理程式 #1，並向 Azure AD 租用戶 #1 註冊此代理程式。
* 部署佈建代理程式 #2，並向 Azure AD 租用戶 #2 註冊此代理程式。
* 根據每個「佈建代理程式」將管理的「子網域」，為每個代理程式設定網域。 一個代理程式可以處理多個網域。
* 在 Azure 入口網站中，於每個租用戶中安裝「Workday 至 AD 使用者佈建應用程式」，然後為其設定個別的網域。

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>為什麼在已部署 Azure AD Connect 的情況下不支援「Workday 至 Azure AD」使用者佈建應用程式？

以混合模式使用 Azure AD 時 (其中所含的使用者混合了雲端與內部部署使用者)，有明確的「授權單位來源」定義相當重要。 通常混合式案例會要求部署 Azure AD Connect。 部署 Azure AD Connect 時，內部部署 AD 是授權單位來源。 若將「Workday 至 Azure AD」連接器導入此組合，會造成 Workday 屬性值可能覆寫 Azure AD Connect 所設定的值。 因此，在已啟用 Azure AD Connect 的情況下不支援「Workday 至 Azure AD」佈建應用程式。 在這類情況下，建議您使用「Workday 至 AD 使用者」佈建應用程式將使用者傳送至內部部署 AD，然後再使用 Azure AD Connect 將他們同步至 Azure AD。

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>如何提出與 Workday 和 Azure AD 整合相關的改進建議或新功能要求？

您的意見反應極具價值，因為它可協助我們設定未來版本和增強功能的方向。 我們歡迎所有意見反應，並鼓勵您在 [Azure AD 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory)中提交您的構想或改進建議。 針對與 Workday 整合相關的特定意見反應，請選取 [SaaS Applications] \(SaaS 應用程式\)，然後使用關鍵字 *Workday* 來搜尋，以尋找與 Workday 相關的現有意見反應。

![UserVoice SaaS 應用程式](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

提出新構想建議時，請檢查看看是否已有其他人提出類似的功能建議。 在該情況下，您可以投票給該功能或增強功能要求。 您也可以留下有關您特定使用案例的評論，以表示您對該構想的支持，以及示範該功能為何也對您極具價值。

### <a name="provisioning-agent-questions"></a>佈建代理程式問題

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>佈建代理程式的 GA 版本是哪一版？

* 「佈建代理程式」的 GA 版本是 1.1.30 和更新版本。
* 如果您的代理程式版本低於 1.1.30，即表示您執行的是公開預覽版，如果裝載代理程式的伺服器具有 .NET 4.7.1 執行階段，它就會自動更新至 GA 版本。
  * 您可以[檢查伺服器上安裝的 .NET 版本](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 如果伺服器並未執行 .NET 4.7.1，您可以[下載並安裝 .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)。 在安裝 .NET 4.7.1 之後，您的佈建代理程式將自動更新至 GA 版本。

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>如何得知我的佈建代理程式版本？

* 登入安裝「佈建代理程式」的 Windows Server。
* 移至 [控制台] -> [解除安裝或變更程式] 功能表
* 尋找與 [Microsoft Azure AD Connect 佈建代理程式] 項目對應的版本

  ![Azure 入口網站](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft 是否會自動推送佈建代理程式更新？

是，Microsoft 會自動更新佈建代理程式。 您可以透過停止 [Microsoft Azure AD Connect 代理程式更新程式] Windows 服務來停用自動更新。

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>我是否可以將佈建代理程式安裝在執行 AAD Connect 的相同伺服器上？

是，您可以將「佈建代理程式」安裝在執行 AAD Connect 的相同伺服器上。

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>進行設定時，「佈建代理程式」會提示您輸入 Azure AD 系統管理員認證。 代理程式是否會將認證儲存在本機伺服器上？

在設定期間，「佈建代理程式」提示您輸入 Azure AD 系統管理員認證只是為了連線到 Azure AD 租用戶。 它不會將認證儲存在本機伺服器上。 不過，它的確會將用來連線到「內部部署 Active Directory 網域」的認證儲存在本機 Windows 密碼保存庫中。

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>如何將佈建代理程式設定成使用 Proxy 伺服器進行連出 HTTP 通訊？

「佈建代理程式」支援使用連出 Proxy。 您可以透過編輯代理程式設定檔 **C:\Program Files\Microsoft Azure AD Connect 佈建代理程式\AADConnectProvisioningAgent.exe.config** 來設定它。請將下列各行新增至該檔案中靠近檔案結尾處的 `</configuration>` 結尾標記之前。
以您的 Proxy 伺服器名稱和連接埠值取代 [proxy-server] 和 [proxy-port] 變數。

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>如何確保佈建代理程式能夠與 Azure AD 租用戶進行通訊，而沒有任何防火牆封鎖代理程式所需的連接埠？

您也可以藉由從內部部署網路開啟[連接器連接埠測試工具 (英文)](https://aadap-portcheck.connectorporttest.msappproxy.net/) 來檢查是否已開啟所有必要的連接埠。 越多的綠色勾選記號，表示越佳的復原功能。

為了確定工具可提供您正確的結果，請務必：

* 從您已安裝「佈建代理程式」之伺服器的瀏覽器上開啟工具。
* 確定所有適用於您「佈建代理程式」的 Proxy 或防火牆也已套用至這個頁面。 您可以在 Internet Explorer 中移至 [設定] -> [網際網路選項] -> [連線] -> [LAN 設定] 來完成此動作。 在此頁面上，您會看到 [為您的 LAN 使用 Proxy 伺服器] 欄位。 請選取此方塊，然後在 [位址] 欄位中輸入 Proxy 位址。

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>是否可以設定讓一個佈建代理程式佈建多個 AD 網域？

是，可以設定讓一個「佈建代理程式」處理多個 AD 網域，只要代理程式能夠監控個別網域控制站即可。 Microsoft 建議您設定一個由 3 個佈建代理程式組成的群組來服務同一組 AD 網域，以確保能夠提供高可用性及提供容錯移轉支援。

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>如何將與佈建代理程式相關的網域取消註冊？

* 從 Azure 入口網站中，取得您 Azure AD 租用戶的「租用戶識別碼」。
* 登入執行「佈建代理程式」的 Windows Server。
* 以「Windows 系統管理員」身分開啟 PowerShell。
* 變更至包含註冊指令碼的目錄，然後執行下列命令，此命令會以您租用戶識別碼的值取代 \[tenant ID\] 參數。

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* 從顯示的代理程式清單中，從 *resourceName* 與您 AD 網域名稱相等的資源中，複製 [識別碼] 欄位的值。
* 將識別碼貼到此命令中，然後在 PowerShell 中執行它。

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* 重新執行代理程式設定精靈。
* 任何其他先前指派給此網域的代理程式都將需要重新設定。

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>如何將佈建代理程式解除安裝？

* 登入安裝「佈建代理程式」的 Windows Server。
* 移至 [控制台] -> [解除安裝或變更程式] 功能表
* 將下列程式解除安裝：
  * Microsoft Azure AD Connect 佈建代理程式
  * Microsoft Azure AD Connect 代理程式更新程式
  * Microsoft Azure AD Connect 佈建代理程式套件

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Workday 至 AD 的屬性對應和設定問題

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>如何備份或匯出 Workday 佈建屬性對應和結構描述的工作複本？

您可以使用 Microsoft Graph API 來匯出您的「Workday 使用者佈建」設定。 如需詳細資料，請參閱[匯出及匯入您的 Workday 使用者佈建屬性對應設定](#exporting-and-importing-your-configuration)。

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>我在 Workday 和 Active Directory 中有自訂屬性。 如何設定讓解決方案使用我的自訂屬性？

解決方案支援自訂的 Workday 和 Active Directory 屬性。 若要將您的自訂屬性新增至對應結構描述，請開啟 [屬性對應] 刀鋒視窗，然後向下捲動以展開 [顯示進階選項] 區段。 

![編輯屬性清單](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

若要新增您的自訂 Workday 屬性，請選取 [Edit attribute list for Workday] \(編輯 Workday 的屬性清單\) 選項，然後若要新增自訂 AD 屬性，請選取 [Edit attribute list for On Premises Active Directory] \(編輯內部部署 Active Directory 的屬性清單\) 選項。

另請參閱：

* [自訂 Workday 使用者屬性的清單](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>如何設定讓解決方案根據 Workday 變更，只更新 AD 中的屬性而不建立任何新的 AD 帳戶？

您可以藉由設定 [屬性對應] 刀鋒視窗中的 [目標物件動作] 來完成此設定，如下所示：

![更新動作](./media/workday-inbound-tutorial/wd_target_update_only.png)

請只針對要從 Workday 流向 AD 的更新作業選取 [更新] 核取方塊。 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>我是否可以將使用者的照片從 Workday 佈建至 Active Directory？

解決方案目前不支援在 Active Directory 中設定 *thumbnailPhoto* 和 *jpegPhoto* 等二進位屬性。

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>如何根據使用者對公共用途的同意，從 Workday 同步行動電話號碼？

* 請移至您「Workday 佈建應用程式」的 [佈建] 刀鋒視窗。
* 按一下 [屬性對應] 
* 在 [對應] 底下，選取 [Synchronize Workday Workers to On Premises Active Directory] \(將 Workday 人員同步至內部部署 Active Directory\) (或 [Synchronize Workday Workers to Azure AD] \(將 Workday 人員同步至 Azure AD\))。
* 在 [屬性對應] 頁面上，向下捲動並勾選 [顯示進階選項] 方塊。  按一下 [Edit attribute list for Workday] \(編輯 Workday 的屬性清單\)
* 在開啟的刀鋒視窗中，找出 "Mobile" 屬性，然後按一下該資料列，以便編輯 **API 運算式** ![行動裝置 GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* 以下列新運算式取代 **API 運算式**，新運算式將只有在 Workday 中工作行動電話號碼的「公共用途旗標」設定為 "True" 時，才會擷取該電話號碼。

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* 儲存「屬性清單」。
* 儲存「屬性對應」。
* 清除目前狀態，然後重新啟動完整同步處理。

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>如何根據使用者的部門/國家 (地區)/縣市屬性設定 AD 中的顯示名稱格式，以及處理區域差異？

在 AD 中，設定 *displayName* 屬性以便同時提供有關使用者部門和國家/地區的資訊，是常見的需求。 例如，如果 John Smith 在美國的行銷部門工作，您可以將其 *displayName* 顯示為 *Smith, John (Marketing-US)*。

以下說明如何處理這類需求，以將 *CN* 或 *displayName* 建構成包含公司、業務單位、縣市或國家/地區等屬性。

* 擷取每個 Workday 屬性時，都是使用基礎 XPATH API 運算式來擷取，在 [屬性對應] -> [進階] 區段 -> [Edit attribute list for Workday] \(編輯 Workday 的屬性清單\) 即可設定此運算式。 以下是 Workday *PreferredFirstName*, *PreferredLastName*、*Company* 及 *SupervisoryOrganization* 屬性的預設 XPATH API 運算式。

     [!div class="mx-tdCol2BreakAll"]
     | Workday 屬性 | API XPath 運算式 |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | 公司 | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   請向您的 Workday 小組確認上述 API 運算式是否適用於您的 Workday 租用戶設定。 必要時，您可以依照[自訂 Workday 使用者屬性的清單](#customizing-the-list-of-workday-user-attributes)一節所述，編輯這些運算式。

* 同樣地，擷取 Workday 中的國家/地區資訊時是使用以下 XPATH：*wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Workday 屬性清單區段中有 5 個國家/地區相關屬性。

     | Workday 屬性 | API XPath 運算式 |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  請向您的 Workday 小組確認上述 API 運算式是否適用於您的 Workday 租用戶設定。 必要時，您可以依照[自訂 Workday 使用者屬性的清單](#customizing-the-list-of-workday-user-attributes)一節所述，編輯這些運算式。

* 若要建置正確的屬性對應運算式，請識別哪個 Workday 屬性「可靠地」代表使用者的名字、姓氏、國家/地區及部門。 假設這些屬性分別是 *PreferredFirstName*、*PreferredLastName*、*CountryReferenceTwoLetter* 及 *SupervisoryOrganization*。 您可以使用這個來建置 AD *displayName* 屬性的運算式，以取得像是 *Smith, John (Marketing-US)* 的顯示名稱，如下所示。

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    有了正確的運算式之後，請編輯 [屬性對應] 資料表，然後修改 *displayName* 屬性對應，如下所示： ![DisplayName 對應](./media/workday-inbound-tutorial/wd_displayname_map.png)

* 延伸上述範例，假設您想要將來自 Workday 的縣市名稱轉換成速記值，然後使用它來建置顯示名稱 (例如 *Smith, John (CHI)* 或 *Doe, Jane (NYC)*)，則您可以使用 Switch 運算式搭配 Workday *Municipality* 屬性作為決定性變數，來獲得此結果。

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    另請參閱：
  * [Switch 函式語法](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Join 函式語法](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Append 函式語法](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>如何使用 SelectUniqueValue 來為 samAccountName 屬性產生唯一值？

假設您想要使用來自 Workday 的 *FirstName* 和 *LastName* 屬性組合，為 *samAccountName* 屬性產生唯一值。 以下是可供您開始著手的運算式：

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
)
```

上述運算式如何運作：如果使用者是 John Smith，它會先嘗試產生 JSmith，如果 JSmith 已經存在，則會產生 JoSmith，如果此值也存在，則會產生 JohSmith。 此運算式也會確保產生的值符合與 *samAccountName* 相關的長度限制和特殊字元限制。

另請參閱：

* [Mid 函式語法](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Replace 函式語法](../manage-apps/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue 函式語法](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>如何移除含有變音符號的字元，並將其轉換成一般英文字母？

建構使用者的電子郵件地址或 CN 值時，請使用 [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) 函式來移除使用者名字和姓氏中的特殊字元。

## <a name="troubleshooting-tips"></a>疑難排解秘訣

本節提供有關如何使用「Azure AD 稽核記錄」和「Windows Server 事件檢視器」記錄，針對 Workday 整合的相關佈建問題進行疑難排解的特定指引。 本節以在下列文章中所擷取的一般疑難排解步驟和概念為基礎：[教學課程：關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)

本節涵蓋下列疑難排解層面：

* [設定 Windows 事件檢視器以進行代理程式疑難排解](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [設定 Azure 入口網站稽核記錄以進行服務疑難排解](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [了解 AD 使用者帳戶建立作業的記錄](#understanding-logs-for-ad-user-account-create-operations)
* [了解管理員更新作業的記錄](#understanding-logs-for-manager-update-operations)
* [解決常見的錯誤](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>設定 Windows 事件檢視器以進行代理程式疑難排解

* 登入部署「佈建代理程式」的 Windows Server 機器
* 開啟「Windows Server 事件檢視器」傳統型應用程式。
* 選取 [Windows 記錄檔 > 應用程式]。
* 使用 [篩選目前的記錄] 選項來檢視記錄在 [AAD.Connect.ProvisioningAgent] 來源底下的所有事件，並指定 "-5" 篩選條件來排除「事件識別碼」為 "5" 的事件，如下所示。

  ![Windows 事件檢視器](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* 按一下 [確定]，然後依 [日期和時間] 資料行來排序結果檢視。

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>設定 Azure 入口網站稽核記錄以進行服務疑難排解

* 啟動 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 Workday 佈建應用程式的 [稽核記錄] 區段。
* 使用 [稽核記錄] 頁面上的 [資料行] 按鈕，以在檢視中只顯示下列資料行 ([日期]、[活動]、[狀態]、[原因])。 此設定可確保您只將焦點放在與疑難排解相關的資料上。

  ![稽核記錄資料行](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* 使用 [目標] 和 [日期範圍] 查詢參數來篩選檢視。 
  * 將 [目標] 查詢參數設定為 Workday 人員物件的「人員識別碼」或「員工識別碼」。
  * 將 [日期範圍] 設定為您要調查佈建相關錯誤或問題的適當期間。

  ![稽核記錄篩選](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>了解 AD 使用者帳戶建立作業的記錄

當偵測到 Workday 中的新雇用人員 (假設「員工識別碼」為 *21023*) 時，Azure AD 佈建服務會嘗試為該人員建立新的 AD 使用者帳戶，然後在該程序中建立 4 個稽核記錄，如下所述：

  [ ![稽核記錄建立作業](media/workday-inbound-tutorial/wd_audit_logs_02.png) ](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

當您按一下任何稽核記錄時，[活動詳細資料] 頁面隨即開啟。 以下是 [活動詳細資料] 頁面針對每個記錄類型顯示的內容。

* [Workday Import] \(Workday 匯入\) 記錄：此記錄會顯示從 Workday 擷取的人員資訊。 請使用記錄之 [其他詳細資料] 區段中的資訊，針對有關從 Workday 擷取資料的問題進行疑難排解。 以下顯示一個範例記錄，以及有關如何解譯每個欄位的指標。

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* [AD Import] \(AD 匯入\) 記錄：此記錄會顯示從 AD 擷取之帳戶的資訊。 由於一開始建立使用者時並沒有 AD 帳戶，因此 [Activity Status Reason] \(活動狀態原因\) 會指出在 Active Directory 中找不到任何具有 [比對識別碼] 屬性值的帳戶。 請使用記錄之 [其他詳細資料] 區段中的資訊，針對有關從 Workday 擷取資料的問題進行疑難排解。 以下顯示一個範例記錄，以及有關如何解譯每個欄位的指標。

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  若要尋找與此 AD 匯入作業對應的「佈建代理程式」記錄，請開啟「Windows 事件檢視器」記錄，然後使用 [尋找] 功能表選項來尋找包含 [比對識別碼]/[聯結屬性] 屬性值 (在此案例中為 *21023*) 的記錄項目。

  ![尋找](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  尋找 [事件識別碼] 為 *9* 的項目，這會為您提供代理程式用來擷取 AD 帳戶的 LDAP 搜尋篩選。 您可以確認這是否是擷取唯一使用者項目的適當搜尋篩選。

  ![LDAP 搜尋](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  緊接其後且 [事件識別碼] 為*2* 的記錄會擷取搜尋作業的結果，以及它是否有傳回任何結果。

  ![LDAP 結果](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* [Synchronization rule action] \(同步處理規則動作\) 記錄：此記錄會顯示屬性對應規則和所設定範圍篩選的結果，以及將採取來處理連入 Workday 事件的佈建動作。 請使用記錄之 [其他詳細資料] 區段中的資訊，針對同步處理動作的相關問題進行疑難排解。 以下顯示一個範例記錄，以及有關如何解譯每個欄位的指標。

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  如果屬性對應運算式有問題或連入 Workday 資料有問題 (例如：必要屬性的值是空的或為 Null)，則您會在此階段看到失敗，其中會有提供失敗詳細資料的 ErrorCode。

* [AD Export] \(AD 匯出\) 記錄：此記錄會顯示 AD 帳戶建立作業的結果，以及在該程序中設定的屬性值。 請使用記錄之 [其他詳細資料] 區段中的資訊，針對帳戶建立作業的相關問題進行疑難排解。 以下顯示一個範例記錄，以及有關如何解譯每個欄位的指標。 在 [其他詳細資料] 區段中，“EventName” 是設定為 “EntryExportAdd”、“JoiningProperty” 是設定為 [比對識別碼] 屬性的值、“SourceAnchor” 是設定為與記錄相關的 WorkdayID (WID)，而 “TargetAnchor” 則是設定為新建立之使用者的 AD “ObjectGuid” 屬性值。 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  若要尋找與此 AD 匯出作業對應的「佈建代理程式」記錄，請開啟「Windows 事件檢視器」記錄，然後使用 [尋找] 功能表選項來尋找包含 [比對識別碼]/[聯結屬性] 屬性值 (在此案例中為 *21023*) 的記錄項目。  

  尋找與匯出作業之時間戳記對應且 [事件識別碼] 為 *2* 的 HTTP POST 記錄。 此記錄將包含佈建服務傳送給佈建代理程式的屬性值。

  [![SCIM 新增](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  緊接在上述事件之後，應該有另一個事件，此事件會擷取建立 AD 帳戶作業的回應。 此事件會傳回 AD 中建立的新 objectGuid，且它會設定為佈建服務中的 TargetAnchor 屬性。

  [![SCIM 新增](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>了解管理員更新作業的記錄

manager 屬性是 AD 中的參考屬性。 佈建服務不會在進行使用者建立作業的過程中設定 manager 屬性。 而是在為使用者建立 AD 帳戶之後，於進行「更新」作業的過程中，才會設定 manager 屬性。 擴充上述案例，假設在 Workday 中已啟用「員工識別碼」為 "21451" 的新雇用人員，而該新雇用人員的管理員 (*21023*) 已經有 AD 帳戶。 在此案例中，於稽核記錄中搜尋使用者 21451 會顯示 5 個項目。

  [ ![管理員更新](media/workday-inbound-tutorial/wd_audit_logs_03.png) ](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

前 4 個記錄與我們在使用者建立作業中探索的記錄類似。 第 5 個記錄是與 manager 屬性更新相關的匯出。 此記錄會顯示 AD 帳戶管理員更新作業 的結果，這是使用管理員的 *objectGuid* 屬性來執行的作業。

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>解決常見的錯誤

本節涵蓋 Workday 使用者佈建的相關常見錯誤及其解決方式。 錯誤依下列方式分組：

* [佈建代理程式錯誤](#provisioning-agent-errors)
* [連線錯誤](#connectivity-errors)
* [AD 使用者帳戶建立錯誤](#ad-user-account-creation-errors)
* [AD 使用者帳戶更新錯誤](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>佈建代理程式錯誤

|#|錯誤案例 |可能的原因|建議的解決方式|
|--|---|---|---|
|1.| 安裝佈建代理程式時發生錯誤，錯誤訊息：*服務「Microsoft Azure AD Connect 佈建代理程式」(AADConnectProvisioningAgent) 無法啟動。請確認您有足夠的權限可以啟動系統。* | 通常，如果您嘗試在網域控制站上安裝佈建代理程式，而群組原則導致服務無法啟動，就會出現此錯誤。  如果您有舊版代理程式正在執行，而您尚未將其解除安裝就開始進行新的安裝，則也會看到此錯誤。| 在非 DC 伺服器上安裝佈建代理程式。 確定先將舊版代理程式解除安裝，然後才安裝新的代理程式。|
|2.| Windows 服務「Microsoft Azure AD Connect 佈建代理程式」處於「正在啟動」狀態，而未切換成「正在執行」狀態。 | 在安裝過程中，代理程式精靈會在伺服器上建立一個本機帳戶 (**NT 服務\\AADConnectProvisioningAgent**)，這是用於啟動服務的**登入**帳戶。 如果您 Windows Server 上的安全性原則會導致本機帳戶無法執行服務，您就會遇到此錯誤。 | 開啟 [服務] 主控台。 在 Windows 服務 [Microsoft Azure AD Connect 佈建代理程式] 上按一下滑鼠右鍵，然後在 [登入] 索引標籤中指定某個網域系統管理員的帳戶來執行此服務。 重新啟動服務。 |
|3.| 在「連線 Active Directory」步驟中以您的 AD 網域設定佈建代理程式時，精靈花費很長的時間嘗試載入 AD 結構描述，並且最終逾時。 | 通常，如果因防火牆問題而導致精靈無法聯繫 AD 網域控制站伺服器，就會出現此錯誤。 | 在 [Connect Active Directory] \(連線 Active Directory\) 精靈畫面上，於提供您 AD 網域的認證時，有一個稱為 [Select domain controller priority] \(選取網域控制站優先順序\) 的選項。 請使用此選項來選取與代理程式伺服器位於相同網站中的網域控制站，並確保沒有任何會封鎖通訊的防火牆規則。 |

#### <a name="connectivity-errors"></a>連線錯誤

如果佈建服務無法連線至 Workday 或 Active Directory，可能會導致佈建進入隔離狀態。 請使用下表來針對連線問題進行疑難排解。

|#|錯誤案例 |可能的原因|建議的解決方式|
|--|---|---|---|
|1.| 當您按一下 [測試連線] 時，收到錯誤訊息：*連線到 Active Directory 時發生錯誤。請確認內部部署佈建代理程式正在執行，且設有正確的 Active Directory 網域。* | 通常，如果佈建代理程式並未執行，或在 Azure AD 與佈建代理程式之間有封鎖通訊的防火牆，就會出現此錯誤。 如果未在「代理程式精靈」中設定網域，您也會看到此錯誤。 | 開啟 Windows Server 上的 [服務] 主控台，以確認代理程式正在執行。 開啟佈建代理程式精靈，並確認已向代理程式註冊正確的網域。  |
|2.| 佈建作業在週末 (星期五到星期六) 進入隔離狀態，而我們收到指出同步處理發生問題的電子郵件通知。 | 此錯誤的其中一個常見原因是計劃性 Workday 停機。 如果您目前使用 Workday 實作租用戶，請注意，Workday 針對它的實作租用戶在週末有排定的停機時間 (通常是從星期五傍晚到星期六早上)，而在這段期間，Workday 佈建應用程式可能會進入隔離狀態，因為它無法連線到 Workday。 一旦 Workday 實作租用戶恢復上線，它就會回到正常狀態。 在罕見的情況下，如果「整合系統使用者」的密碼因租用戶重新整理而發生變更，或帳戶處於鎖定或過期狀態，您也可能看到此錯誤。 | 請洽詢您的 Workday 系統管理員或整合合作夥伴，以了解 Workday 排定於何時停機，以在停機期間忽略警示訊息，並在 Workday 執行個體恢復上線後確認可用性。  |


#### <a name="ad-user-account-creation-errors"></a>AD 使用者帳戶建立錯誤

|#|錯誤案例 |可能的原因|建議的解決方式|
|--|---|---|---|
|1.| 稽核記錄中有匯出作業失敗，訊息為：*錯誤:OperationsError-SvcErr:發生作業錯誤。尚未為目錄服務設定上層參照。目錄服務因此無法發行轉介給這個樹系外的物件。* | 通常，如果未正確設定 [Active Directory 容器] OU，或用於 *parentDistinguishedName* 的「運算式對應」有問題，就會出現此錯誤。 | 檢查 [Active Directory 容器] OU 參數是否有錯字。 如果您在屬性對應中使用 *parentDistinguishedName*，請確定它一律會評估為 AD 網域內的已知容器。 請檢查稽核記錄中的「匯出」事件，以查看產生的值。 |
|2.| 稽核記錄中有匯出作業失敗，錯誤碼為：*SystemForCrossDomainIdentityManagementBadResponse*，訊息為：*錯誤:ConstraintViolation-AtrErr:要求中的值無效。屬性的數值不在可接受的數值範圍內。\n錯誤詳細資料:CONSTRAINT_ATT_TYPE - company*。 | 雖然此錯誤是 *company* 屬性特定的錯誤，但針對其他屬性 (例如 *CN*)，您也可能看到此錯誤。 之所以會出現此錯誤，是因為 AD 的強制執行結構描述條件約束緣故。 AD 中的 *company* 和 *CN* 等屬性預設有 64 個字元的上限。 如果來自 Workday 的值超過 64 個字元，您就會看到此錯誤訊息。 | 檢查稽核記錄中的「匯出」事件，以查看錯誤訊息中所回報屬性的值。 請考慮使用 [Mid](../manage-apps/functions-for-customizing-application-data.md#mid) 函式以截斷來自 Workday 的值，或將對應變更至沒有類似之長度條件約束的 AD 屬性。  |

#### <a name="ad-user-account-update-errors"></a>AD 使用者帳戶更新錯誤

在進行 AD 使用者帳戶更新程序的期間，佈建服務會從 Workday 和 AD 讀取資訊、執行屬性對應規則，然後判斷是否有任何變更需要生效。 因而相應地觸發更新事件。 如果這其中任何步驟發生失敗，就會在稽核記錄中記錄下來。 請使用下表來針對常見的更新錯誤進行疑難排解。

|#|錯誤案例 |可能的原因|建議的解決方式|
|--|---|---|---|
|1.| 稽核記錄中有同步處理規則動作失敗，訊息為：*EventName = EntrySynchronizationError 和 ErrorCode = EndpointUnavailable*。 | 當因為內部部署佈建代理程式發生處理錯誤，而導致佈建服務無法從 Active Directory 擷取使用者設定檔資料時，就會出現此錯誤。 | 查看「佈建代理程式事件檢視器」記錄中是否有指出讀取作業相關問題的錯誤事件 (依「事件識別碼」#2 來進行篩選)。 |
|2.| 針對 AD 中的某些使用者，AD 中的 manager 屬性未更新。 | 此錯誤的最可能原因就是您使用範圍規則，而使用者的管理員不在該範圍內。 如果在目標 AD 網域中找不到管理員的比對識別碼屬性 (例如 EmployeeID)，或該屬性未設定為正確的值，您也可能遇到此錯誤。 | 檢閱範圍篩選，然後將管理員使用者新增到範圍中。 檢查管理員在 AD 中的設定檔，以確定比對識別碼屬性有值。 |

## <a name="managing-your-configuration"></a>管理您的設定

本節說明如何進一步延伸、自訂及管理您的 Workday 導向使用者佈建設定。 其中涵蓋下列主題：

* [自訂 Workday 使用者屬性的清單](#customizing-the-list-of-workday-user-attributes)  
* [匯出及匯入您的設定](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>自訂 Workday 使用者屬性的清單

Active Directory 和 Azure AD 的 Workday 佈建應用程式都包含您可以從中選取的預設 Workday 使用者屬性清單。 不過，這些清單並不完整。 Workday 支援數百個可能的使用者屬性，這些屬性可以是 Workday 租用戶的標準或唯一屬性。

Azure AD 佈建服務支援自訂清單或 Workday 屬性的功能，以包含任何在人力資源 API 的 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 作業中公開的屬性。

若要進行此變更，您必須使用 [Workday Studio](https://community.workday.com/studio-download) 來擷取代表您要使用之屬性的 XPath 運算式，然後在 Azure 入口網站中使用進階屬性編輯器，將這些運算式新增至您的佈建設定。

**若要擷取 Workday 使用者屬性的 XPath 運算式：**

1. 下載並安裝 [Workday Studio](https://community.workday.com/studio-download)。 您需要 Workday 社群帳戶才能存取安裝程式。

2. 從下列 URL 下載 Workday Human_Resources WSDL 檔案： https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. 啟動 Workday Studio。

4. 從命令列中，選取 [Workday] > [在測試器中測試 Web 服務] 選項。

5. 選取 [外部]，然後選取您在步驟 2 下載的 Human_Resources WSDL 檔案。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. 將 [位置] 欄位設定為 `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`，但是以您實際的執行個體類型取代 "IMPL-CC"，並以您實際的租用戶名稱取代 "TENANT"。

7. 將 [作業] 設定為 [Get_Workers]

8.  按一下 [要求]/[回應] 窗格下方的小型**設定**連結，以設定您的 Workday 認證。 勾選 [驗證]，然後輸入 Workday 整合系統帳戶的使用者名稱和密碼。 請務必將使用者名稱的格式設為 name@tenant，並讓 [WS-Security 使用者名稱權杖] 選項繼續保持已選取狀態。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. 選取 [確定] 。

10. 在 [Request] \(要求\) 窗格中，貼上以下的 XML 並將 **Employee_ID** 設定為您 Workday 租用戶中真實使用者的員工識別碼。 選取已填入您想擷取之屬性的使用者。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. 按一下 [傳送要求] (綠色箭頭) 來執行命令。 如果成功，回應應會出現在 [回應] 窗格中。 請檢查回應以確定它具有您所輸入的使用者識別碼資料，而非錯誤。

12. 如果成功，請從 [回應] 窗格複製 XML 並將它儲存為 XML 檔案。

13. 在 Workday Studio 的命令列中，選取 [File] \(檔案\) > [Open File...] \(開啟檔案...\) 並開啟您剛儲存的 XML 檔案。 此動作會在 Workday Studio XML 編輯器中開啟該檔案。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. 在檔案樹狀目錄中，一一瀏覽 **[/env:Envelope] > [env:Body] > [wd:Get_Workers_Response] > [wd:Response_Data] > [wd:Worker]** 以尋找使用者的資料。

15. 在 [wd:Worker] 底下，尋找您要新增的屬性並加以選取。

16. 從 [文件路徑] 欄位複製您所選屬性的 XPath 運算式。

17. 從複製的運算式中移除 **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** 前置詞。

18. 如果所複製運算式中的最後一個項目是節點 (例如："/wd:Birth_Date")，則請在運算式的結尾附加 **/text()**。 如果最後一個項目是屬性 (例如："/@wd: type")，則不一定要這麼做。

19. 結果應該類似 `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`。 這是您將複製到 Azure 入口網站中的值。

**若要將自訂 Workday 使用者屬性新增至佈建組態：**

1. 如本教學課程稍早所述，啟動 [Azure 入口網站](https://portal.azure.com)，並瀏覽至 Workday 佈建應用程式的 [佈建] 區段。

2. 將 [佈建狀態] 設定為 [關閉]，然後選取 [儲存]。 此步驟有助於確保您的變更只會在您準備好時生效。

3. 在 [對應] 底下，選取 [Synchronize Workday Workers to On Premises Active Directory] \(將 Workday 人員同步至內部部署 Active Directory\) (或 [Synchronize Workday Workers to Azure AD] \(將 Workday 人員同步至 Azure AD\))。

4. 捲動到下一個畫面的底部，然後選取 [顯示進階選項]。

5. 選取 [編輯 Workday 的屬性清單]。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. 捲動到輸入欄位所在屬性清單的底部。

7. 針對 [名稱]，輸入屬性的顯示名稱。

8. 針對 [類型]，選取屬性的適當對應類型 ([字串] 最常見)。

9. 針對 [API 運算式]，輸入您從 Workday Studio 複製的 XPath 運算式。 範例： `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. 選取 [新增屬性]。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. 選取上方的 [儲存]，然後在對話方塊中選取 [是]。 如果 [屬性對應] 畫面仍然開啟，請將它關閉。

12. 回到主要 [佈建] 索引標籤，再次選取 [Synchronize Workday Workers to On Premises Active Directory] \(將 Workday 人員同步至內部部署 Active Directory\) (或 [Synchronize Workers to Azure AD] \(將人員同步至 Azure AD\))。

13. 選取 [新增對應]。

14. 您的新屬性現在應出現在 [來源屬性] 清單中。

15. 視需要為您的新屬性新增對應。

16. 完成後，請記得將 [佈建狀態] 設回 [開啟] 並儲存。

### <a name="exporting-and-importing-your-configuration"></a>匯出及匯入您的設定

本節說明如何使用 Microsoft Graph API 和「Graph 總管」將您的「Workday 佈建」屬性對應和結構描述匯出成 JSON 檔案，然後將其匯入回 Azure AD 中。

#### <a name="step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id"></a>步驟 1：擷取您的 Workday 佈建應用程式服務主體識別碼 (物件識別碼)

1. 啟動 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 Workday 佈建應用程式的 [屬性] 區段。
1. 在佈建應用程式的 [屬性] 區段中，複製與 [物件識別碼] 欄位相關的 GUID 值。 此值也稱為您應用程式的 **ServicePrincipalId**，並且會在「Graph 總管」作業中用到。

   ![Workday 應用程式服務主體識別碼](./media/workday-inbound-tutorial/wd_export_01.png)

#### <a name="step-2-sign-into-microsoft-graph-explorer"></a>步驟 2：登入 Microsoft Graph 總管

1. 啟動 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)
1. 按一下 [使用 Microsoft 登入] 按鈕，然後使用「Azure AD 全域管理員」或「應用程式管理員」認證來登入。

    ![Graph 登入](./media/workday-inbound-tutorial/wd_export_02.png)

1. 成功登入時，您會在左側窗格中看到使用者帳戶詳細資料。

#### <a name="step-3-retrieve-the-provisioning-job-id-of-the-workday-provisioning-app"></a>步驟 3：擷取 Workday 佈建應用程式的佈建作業識別碼

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以從[步驟 1](#step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id) 擷取的 **ServicePrincipalId** 取代 [servicePrincipalId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

您會得到如下所示的回應。 請複製回應中的「id 屬性」。 此值是 **ProvisioningJobId**，且將會用來擷取基礎結構描述中繼資料。

   [![佈建作業識別碼](./media/workday-inbound-tutorial/wd_export_03.png)](./media/workday-inbound-tutorial/wd_export_03.png#lightbox)

#### <a name="step-4-download-the-provisioning-schema"></a>步驟 4：下載佈建結構描述

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以在先前步驟中擷取的 ServicePrincipalId 和 ProvisioningJobId 取代 [servicePrincipalId] 和 [ProvisioningJobId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

從回應中複製 JSON 物件，然後將它儲存成檔案以建立結構描述備份。

#### <a name="step-5-import-the-provisioning-schema"></a>步驟 5：匯入佈建結構描述

> [!CAUTION]
> 請只有在您需要為無法使用 Azure 入口網站來變更的設定修改結構描述，或您需要從含有效且可運作之結構描述的先前備份檔案還原設定時，才執行此步驟。

在「Microsoft Graph 總管」中，設定下列 PUT 查詢，其中以在先前步驟中擷取的 ServicePrincipalId 和 ProvisioningJobId 取代 [servicePrincipalId] 和 [ProvisioningJobId]。

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

在 [要求本文] 索引標籤中，複製 JSON 結構描述檔案的內容。

   [![要求本文](./media/workday-inbound-tutorial/wd_export_04.png)](./media/workday-inbound-tutorial/wd_export_04.png#lightbox)

在 [要求標頭] 索引標籤中，新增值為 “application/json” 的 Content-Type 標頭屬性

   [![要求標頭](./media/workday-inbound-tutorial/wd_export_05.png)](./media/workday-inbound-tutorial/wd_export_05.png#lightbox)

按一下 [執行查詢] 按鈕以匯入新的結構描述。

## <a name="managing-personal-data"></a>管理個人資料

適用於 Active Directory 的 Workday 佈建解決方案會要求在內部部署 Windows Server 上安裝佈建代理程式，而此代理程式會在 Windows 事件記錄檔中建立可能包含個人資料 (視您的 Workday 至 AD 屬性對應而定) 的記錄。 為了符合使用者隱私權義務，您可以藉由設定 Windows 排程工作來清除事件記錄檔，確保沒有任何資料會在事件記錄檔中保留超過 48 小時。

Azure AD 佈建服務會歸類到 GDPR 分類的**資料處理器**類別。 作為資料處理器管線，服務會對重要合作夥伴和終端取用者提供資料處理服務。 Azure AD 佈建服務不會產生使用者資料，也不會自行控制要收集哪些個人資料以及如何使用這些資料。 在 Azure AD 佈建服務中，資料的擷取、彙總、分析和報告都會以現有的企業資料為基礎。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

在資料保留方面，Azure AD 佈建服務不會針對超過 30 天的資料產生報告、執行分析或提供見解。 因此，Azure AD 佈建服務不會儲存、處理或保留任何資料達 30 天以上。 這項設計符合 GDPR 法規、Microsoft 隱私權合規性規定和 Azure AD 資料保留原則。

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
* [了解如何設定 Workday 與 Azure Active Directory 之間的單一登入](workday-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [了解如何使用 Microsoft Graph API 來管理佈建設定](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) \(英文\)
