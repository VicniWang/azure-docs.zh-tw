---
title: 如何規劃 Azure Active Directory (Azure AD) Join 實作 | Microsoft Docs
description: 說明在您的環境中實作已加入 Azure AD 的裝置所需執行的步驟。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a299c5cf59a3c5b7a994303c9d85783ecfbb424
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201885"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>作法：規劃 Azure AD 聯結實作


Azure AD Join 可讓您直接將裝置加入 Azure AD，而不需要加入內部部署 Active Directory，同時讓您的使用者保有生產力和安全性。 Azure AD Join 在大規模和限域的部署中都符合企業需求。   

本文將為您提供規劃 Azure AD Join 實作所需的資訊。

 
## <a name="prerequisites"></a>必要條件

本文假設您熟悉 [Azure Active Directory 中的裝置管理簡介](../device-management-introduction.md)。



## <a name="plan-your-implementation"></a>計劃您的實作

若要規劃您的混合式 Azure AD 實作，您應該熟悉：

|   |   |
|---|---|
|![勾選][1]|檢閱您的案例|
|![勾選][1]|檢閱您的身分識別基礎結構|
|![勾選][1]|存取您的裝置管理|
|![勾選][1]|了解應用程式和資源的考量|
|![勾選][1]|了解您的佈建選項|
|![勾選][1]|設定企業狀態漫遊|
|![勾選][1]|設定條件式存取|







## <a name="review-your-scenarios"></a>檢閱您的案例 

混合式 Azure AD Join 可能較適用於特定案例，而 Azure AD Join 則可讓您轉換至以雲端優先的 Windows 模型。 如果您打算現代化裝置管理，並降低裝置相關 IT 成本，Azure AD Join 所提供的絕佳基礎將可讓您達到這些目標。  

 
如果您的目標符合下列準則，則應考慮使用 Azure AD Join：

- 您想要採用 Microsoft 365 作為使用者的生產力套件。

- 您想要以雲端裝置管理解決方案來管理裝置。

- 您想要為分散於各處的使用者簡化裝置佈建程序。

- 您想要現代化應用程式基礎結構。
 

 

## <a name="review-your-identity-infrastructure"></a>檢閱您的身分識別基礎結構  

Azure AD Join 在受控和同盟環境中均可運作。  


### <a name="managed-environment"></a>受控環境

受控環境可使用無縫單一登入透過[密碼雜湊同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)或[傳遞驗證](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)進行部署。

在這些案例中，您不需要設定同盟伺服器以進行驗證。


### <a name="federated-environment"></a>同盟環境

同盟環境應具有支援 WS-Trust 和 WS-Fed 通訊協定的識別提供者：

- **WS-Fed：** 必須使用此通訊協定，才能將裝置加入 Azure AD。

- **WS-Trust：** 必須使用此通訊協定，才能登入已加入 Azure AD 的裝置。 

如果您的識別提供者不支援這些通訊協定，則 Azure AD Join 無法原生運作。 從 Windows 10 1809 開始，您的使用者即可經由 SAML 型識別提供者透過 [Windows 10 的 Web 登入](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10)來登入已加入 Azure AD 的裝置。 Web 登入目前為僅供預覽的功能。


### <a name="smartcards-and-certificate-based-authentication"></a>智慧卡和憑證型驗證

您無法使用智慧卡或憑證型驗證將裝置加入 Azure AD。 不過，如果您已設定 AD FS，則可以使用智慧卡來登入已加入 Azure AD 的裝置。

**建議：** 實作 Windows Hello 企業版，向 Windows 10 裝置進行強式無密碼驗證。


### <a name="user-configuration"></a>使用者組態

如果您將使用者建立於：

- **內部部署 Active Directory**，您必須使用 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 將其同步至 Azure AD。 

- **Azure AD**，則不需另行設定。

內部部署 UPN 與 Azure AD UPN 不同，加入 Azure AD 的裝置上並不支援它。 如果您使用內部部署 UPN，您應規劃改為使用它們在 Azure AD 中的主要 UPN。



## <a name="assess-your-device-management"></a>存取您的裝置管理

### <a name="supported-devices"></a>支援的裝置

Azure AD Join：

- 僅適用於 Windows 10 裝置。 

- 不適用舊版的 Windows 或其他作業系統。 如果您有 Windows 7/8.1 裝置，您必須升級為 Windows 10，以部署 Azure AD Join。

- 不支援 FIPS 模式中搭配 TPM 的裝置。
 
**建議：** 一律使用最新的 Windows 10 版本，以享有更新的功能。


### <a name="management-platform"></a>管理平台

已加入 Azure AD 的裝置以 MDM 平台 (例如 Intune 和 MDM CSP) 作為其裝置管理的基礎。 Windows 10 有內建的 MDM 代理程式，可與所有相容的 MDM 解決方案搭配運作。

有兩種方法可用來管理已加入 Azure AD 的裝置：

- **僅限 MDM** - 裝置由 Intune 之類的 MDM 提供者以獨佔方式管理。 所有原則都會在 MDM 註冊的過程中提供。 對 Azure AD Premium 或 EMS 客戶而言，MDM 註冊會是包含在 Azure AD Join 中的自動化步驟。

- **共同管理** - 裝置由 MDM 提供者和 SCCM 所管理。 使用此方法時，SCCM 代理程式會安裝在 MDM 管理的裝置上，以管理特定層面。

由於已加入 Azure AD 的裝置並未連線至內部部署 Active Directory，因此不支援群組原則。


如果您要使用群組原則，請使用 [MDM 移轉分析工具 (MMAT)](https://github.com/WindowsDeviceManagement/MMAT) 評估您的 MDM 原則同位檢查。 

請檢閱支援和不支援的原則，以判斷您是否可使用 MDM 解決方案，而非群組原則。 針對不支援的原則，請考量下列各項：

- 已加入Azure AD 的裝置或使用者是否需要不支援的原則？

- 不支援的原則是否適用於雲端驅動的部署中？

如果您的 MDM 解決方案無法透過 Azure AD 應用程式資源庫來啟用，您可以依照 [Azure Active Directory 與 MDM 的整合](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm)中所列的程序新增該解決方案。 

透過共同管理，您可以使用 SCCM 來管理裝置的特定層面，同時透過 MDM 平台來提供原則。 Microsoft Intune 可支援與 SCCM 搭配的共同管理。 如需詳細資訊，請參閱 [Windows 10 裝置的共同管理](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview)。 如果您使用 Intune 以外的 MDM 產品，請向 MDM 提供者洽詢適用的共同管理案例。

**建議：** 針對已加入 Azure AD 的裝置，請考慮使用僅限 MDM 的管理。



## <a name="understand-considerations-for-applications-and-resources"></a>了解應用程式和資源的考量

建議您將應用程式從內部部署移轉至雲端，以獲取較佳的使用者體驗和存取控制。 不過，已加入 Azure AD 的裝置可以順暢地提供對內部部署和雲端應用程式的存取。 如需詳細資訊，請參閱[內部部署資源的 SSO 如何在加入 Azure AD 的裝置上運作](azuread-join-sso.md)。

以下幾節將列出不同應用程式和資源類型的考量。

### <a name="cloud-based-applications"></a>雲端式應用程式

如果應用程式已新增至 Azure AD 應用程式資源庫，使用者將可透過已加入 Azure AD 的裝置進行 SSO。 他們不需進行其他設定。 使用者可在Microsoft Edge 和 Chrome 瀏覽器上進行 SSO。 若要使用 Chrome，您必須部署 [Windows 10 帳戶延伸模組](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。 

所有 Win32 應用程式只要：

- 使用 Web 帳戶管理員 (WAM) 提出權杖要求，則也可在已加入 Azure AD 的裝置上進行 SSO。 

- 不使用 WAM，系統可能會提示使用者進行驗證。 


### <a name="on-premises-web-applications"></a>內部部署 Web 應用程式

如果您的應用程式是以自訂方式建置，且 (或) 裝載於內部部署，您就必須將其新增至瀏覽器的信任網站，以便：

- 啟用 Windows 整合式驗證 
- 為使用者提供無提示的 SSO 體驗。 

如果您使用 AD FS，請參閱[驗證及管理使用 AD FS 的單一登入](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100))。 

**建議：** 考慮裝載於雲端 (例如 Azure) 並且與 Azure AD 整合，以提供更理想的體驗。

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>使用舊版通訊協定的內部部署應用程式

如果裝置可存取網域控制站，則使用者可從已加入 Azure AD 的裝置進行 SSO。 

**建議：** 部署 [Azure AD App Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)，以啟用這些應用程式的安全存取。


### <a name="on-premises-network-shares"></a>內部部署網路共用

如果裝置可存取內部部署網域控制站，則使用者可從已加入 Azure AD 的裝置進行 SSO。

### <a name="printers"></a>印表機

針對印表機，您必須部署[混合式雲端列印](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)，以在已加入 Azure AD 的裝置上探索印表機。 

雖然在僅限雲端的環境中無法自動探索印表機，您的使用者仍可使用印表機的 UNC 路徑直接加以新增。 


### <a name="on-premises-applications-relying-on-machine-authentication"></a>使用機器驗證的內部部署應用程式

已加入 Azure AD 的裝置不支援使用機器驗證的內部部署應用程式。 

**建議：** 考慮淘汰這些應用程式，並改用其最新的替代方案。

### <a name="remote-desktop-services"></a>遠端桌面服務問題

若要從遠端桌面連線至已加入 Azure AD 的裝置，主機電腦必須已加入 Azure AD 或已加入混合式 Azure AD。 不支援從未加入或非 Windows 的裝置進行遠端桌面連線。 如需詳細資訊，請參閱[連線至已加入 Azure AD 的遠端 PC](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)


## <a name="understand-your-provisioning-options"></a>了解您的佈建選項

您可以使用下列方法來佈建 Azure AD Join：

- **OOBE/設定中的自助模式** - 在自助模式中，使用者可在使用 Windows 全新體驗 (OOBE) 期間或從 [Windows 設定] 中進行 Azure AD Join 程序。 如需詳細資訊，請參閱[了解如何將您的工作裝置加入組織的網路](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)。 

- **Windows Autopilot** - Windows Autopilot 可讓您預先設定裝置，以提升在 OOBE 中執行 Azure AD Join 的體驗。 如需詳細資訊，請參閱 [Windows Autopilot 概觀](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)。 

- **大量註冊** - 大量註冊可讓系統管理員使用大量佈建工具設定裝置，以驅動 Azure AD Join。 如需詳細資訊，請參閱 [Windows 裝置的大量註冊](https://docs.microsoft.com/intune/windows-bulk-enroll)。
 
 


以下是這三種方法的比較 

 
||自助式設定|Windows Autopilot|大量註冊|
|---|---|---|---|
|需要使用者手動設定|yes|是|否|
|需要 IT 工作|否|yes|yes|
|適用的流程|OOBE 和設定|僅限 OOBE|僅限 OOBE|
|主要使用者的本機管理員權限|是，依照預設|可設定|否|
|需要裝置 OEM 支援|否|yes|否|
|支援的版本|1511+|1709+|1703+|
 
請檢閱上表，並檢閱下列有關於採用任一方法的考量，選擇您的一或多個部署方法：  

- 使用者是否有精熟的技術可獨自完成設定？ 

    - 這類使用者最適合使用自助模式。 請考慮以 Windows Autopilot 提升使用者體驗。  

- 使用者位於遠端還是公司內部？ 

    - 遠端使用者最適合使用自助模式或 Autopilot 輕鬆完成設定。 
 
- 您偏好由使用者驅動還是由管理員管理的設定？ 

    - 管理員驅動的部署較適合使用大量註冊來設定裝置並提供給使用者。     

- 您是向 1 到 2 個 OEM 購買裝置，還是向許多不同的 OEM 購買裝置？  

    - 如果您的裝置來自於少數幾個也支援 Autopilot 的 OEM，則可以較緊密地與 Autopilot 整合而獲得效益。 
 

## <a name="configure-your-device-settings"></a>設定您的裝置設定

Azure 入口網站可讓您控制如何將已加入 Azure AD 的裝置部署到您的組織中。 若要設定相關設定，請在 [Azure Active Directory ] 頁面上選取 `Devices > Device settings`。

### <a name="users-may-join-devices-to-azure-ad"></a>使用者可以將裝置加入 Azure AD

請根據您部署的範圍，和您要允許哪些人設定已加入 Azure AD 的裝置，將此選項設為 [全部] 或 [選取的]。 

![使用者可以將裝置加入 Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>已加入 Azure AD 的裝置上的其他本機系統管理員

選擇 [選取的]，然後選取您要在所有已加入 Azure AD 的裝置上新增至本機系統管理員群組的使用者。 

![已加入 Azure AD 的裝置上的其他本機系統管理員](./media/azureadjoin-plan/02.png)


### <a name="require-multi-factor-auth-to-join-devices"></a>需要多因素驗證才能加入裝置

如果您要求使用者在將裝置加入 Azure AD 時必須執行 MFA，請選取 [是]。 針對使用 MFA 將裝置加入 Azure AD 的使用者，裝置本身會成為第 2 個因素。

![需要多因素驗證才能加入裝置](./media/azureadjoin-plan/03.png)




## <a name="configure-your-mobility-settings"></a>設定您的行動性設定

您可能必須先新增 MDM 提供者，才能設定行動性設定。

**若要新增 MDM 提供者**：

1. 在 [Azure Active Directory] 頁面的 [管理] 區段中，按一下 `Mobility (MDM and MAM)`。 

2. 按一下 [新增應用程式]。

3. 從清單中選取您的 MDM 提供者。

    ![新增應用程式](./media/azureadjoin-plan/04.png)

選取您的 MDM 提供者以設定相關設定。 

### <a name="mdm-user-scope"></a>MDM 使用者範圍

根據您的部署範圍選取 [部分] 或 [全部]。 

![MDM 使用者範圍](./media/azureadjoin-plan/05.png)

根據您的範圍，會出現下列其中一種情況： 

- **使用者在 MDM 範圍內**：如果您具有 Azure AD Premium 訂用帳戶，則會隨著 Azure AD Join 自動執行 MDM 註冊。 範圍內的所有使用者皆必須具有適當的 MDM 授權。 在此情況下，如果 MDM 註冊失敗，Azure AD Join 也將回復。
    
- **使用者不在 MDM 範圍內**：如果使用者不在 MDM 範圍內，則 Azure AD Join 會逕自完成而不進行 MDM 註冊。 這會導致裝置未受管理。


### <a name="mdm-urls"></a>MDM URL

您的 MDM 組態會有三個相關的 URL：

- MDM 使用條款 URL

- MDM 探索 URL 

- MDM 合規性 URL


![新增應用程式](./media/azureadjoin-plan/06.png)


每個 URL 都有預先定義的預設值。 如果這些欄位是空的，請連絡您的 MDM 提供者以取得詳細資訊。

### <a name="mam-settings"></a>MAM 設定

MAM 不適用於 Azure AD Join。 


## <a name="configure-enterprise-state-roaming"></a>設定企業狀態漫遊

如果您想要啟用 Azure AD 的狀態漫遊，讓使用者可在裝置間同步其設定，請參閱[在 Azure Active Directory 中啟用企業狀態漫遊](https://docs.microsoft.com/azure/active-directory/devices/enterprise-state-roaming-enable)。 

**建議**：即使對混合式 Azure AD Join 裝置也可啟用此設定。


## <a name="configure-conditional-access"></a>設定條件式存取

如果您為已加入 Azure AD 的裝置設定了 MDM 提供者，只要裝置受到管理，提供者即會將裝置標示為符合規範。 

![符合規範的裝置](./media/azureadjoin-plan/46.png)

您可以使用此實作，[透過條件式存取要求必須從受控裝置存取雲端應用程式](../conditional-access/require-managed-devices.md)。




## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在初次執行期間向 Azure AD 加入新的 Windows 10 裝置](azuread-joined-devices-frx.md)
> [將您的工作裝置加入組織的網路](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)


<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
