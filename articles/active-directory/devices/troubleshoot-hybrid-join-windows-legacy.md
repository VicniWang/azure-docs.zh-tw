---
title: 針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解 | Microsoft Docs
description: 針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7054946a15ca27f4b5a36d3d2ac712cba2fd68d8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185123"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解 

本文章僅適用於下列裝置： 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

對於 Windows 10 或 Windows Server 2016，請參閱[針對已加入混合式 Azure Active Directory 的 Windows 10 和 Windows Server 2016 裝置進行疑難排解](troubleshoot-hybrid-join-windows-current.md)。

本文章假設您[設定已加入混合式 Azure Active Directory 的裝置](hybrid-azuread-join-plan.md)來支援下列案例：

- 裝置型條件式存取


本文章提供有關如何解決潛在問題的疑難排解指引。  

**您應該知道的事情：** 

- 舊版 Windows 裝置的混合式 Azure AD 加入運作方式與在 Windows 10 中的運作方式略有不同。 許多客戶並不了解他們需要設定的是 AD FS (適用於同盟網域) 還是「無縫 SSO」(適用於受控網域)。

- 針對具有同盟網域的客戶，如果「服務連接點」(SCP) 已設定為指向受控網域名稱 (例如 contoso.onmicrosoft.com，而不是 contoso.com)，則舊版 Windows 裝置的混合式 Azure AD 加入將無法運作。

- 每一使用者的裝置數目上限目前也適用於已加入舊版混合式 Azure AD 的裝置。 

- 當多位網域使用者登入已加入舊版混合式 Azure AD 的裝置時，相同的實體裝置會在 Azure AD 中出現多次。  舉例來說：如果 *jdoe* 和 *jharnett* 登入裝置，在 [使用者] 資訊索引標籤中就會為每位使用者建立個別的註冊 (DeviceID)。 

- 在使用者資訊索引標籤上，也可能因重新安裝作業系統或手動重新註冊，而導致一個裝置有多個項目。

- 您可以將裝置的初始註冊/加入設定為在登入或鎖定/解除鎖定時嘗試執行。 工作排程器工作可能會觸發 5 分鐘的延遲。 

- 如果是 Windows 7 SP1 或 Windows Server 2008 R2 SP1，請確定已安裝 [KB4284842](https://support.microsoft.com/help/4284842)。 此更新可避免未來的驗證因客戶在變更密碼之後遺失存取受保護金鑰的權限而失敗。

## <a name="step-1-retrieve-the-registration-status"></a>步驟 1：擷取註冊狀態 

**確認註冊狀態：**  

1. 使用已執行混合式 Azure AD 加入的使用者帳戶登入。

2. 開啟命令提示字元 

3. 輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

此命令會顯示一個對話方塊，當中會提供有關加入狀態的詳細資料。

![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>步驟 2：評估混合式 Azure AD 加入狀態 

如果裝置未加入混合式 Azure AD，您可以按一下 [加入] 按鈕來嘗試執行混合式 Azure AD 加入。 如果執行混合式 Azure AD 加入的嘗試失敗，將會顯示有關該失敗的詳細資料。


**最常見的問題包括：**

- AD FS 或 Azure AD 設定不正確或網路問題

    ![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
    - Autoworkplace.exe 無法以無訊息方式使用 Azure AD 或 AD FS 進行驗證。 造成此情況的原因可能是 AD FS (適用於同盟網域) 遺漏或設定不正確、「Azure AD 無縫單一登入」(適用於受控網域) 遺漏或設定不正確，或網路問題。 
    
     - 也可能是已針對使用者啟用/設定多重要素驗證 (MFA)，但未在 AD FS 伺服器設定 WIAORMUTLIAUTHN。 
     
     - 另一個可能原因是主領域探索 (HRD) 頁面正在等候使用者互動，而導致 **autoworkplace.exe** 無法以無訊息方式要求權杖。
     
     - 可能是用戶端上 IE 的內部網路區域中遺漏 AD FS 和 Azure AD URL。
     
     - 網路連線能力問題可能會導致 **autoworkplace.exe** 無法連線至 AD FS 或 Azure AD URL。 
     
     - **Autoworkplace.exe** 會要求用戶端必須具有用戶端到組織內部部署 AD 網域控制站的直接視線，這意謂著混合式 Azure AD 加入只有在用戶端連線至組織的內部網路時才會成功。
     
     - 貴組織使用的是 Azure AD 無縫單一登入，`https://autologon.microsoftazuread-sso.com` 或 `https://aadg.windows.net.nsatc.net` 不在裝置的 IE 內部網路設定中，並且未針對內部網路區域啟用 [允許透過指令碼更新至狀態列]。

- 您不是以網域使用者身分登入

    ![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    這種情況可能是由幾種不同的原因所致︰
    
    - 登入的使用者不是網域使用者 (例如本機使用者)。 對下層裝置的混合式 Azure AD 聯結僅支援網域使用者。
    
    - 用戶端無法連線至網域控制站。    

- 已達到配額限制

    ![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- 服務沒有回應 

    ![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

您也可在事件記錄中找到此資訊，記錄位於：**應用程式和服務記錄\Microsoft-Workplace Join**
  
**混合式 Azure AD 加入失敗的最常見原因包括：** 

- 您的電腦未連線至組織的內部網路，或不在可連線到內部部署 AD 網域控制站的 VPN 上。

- 您是使用本機電腦帳戶來登入您的電腦。 

- 服務組態問題： 

  - AD FS 伺服器未設定為支援 **WIAORMULTIAUTHN**。 

  - 您電腦的樹系沒有「服務連接點」物件指向 Azure AD 中已驗證的網域名稱 
  
  - 或者，如果您的網域是受控網域，則是未設定「無縫 SSO」或其無法運作。

  - 使用者已達到裝置限制。 

## <a name="next-steps"></a>後續步驟

如有問題，請參閱[裝置管理常見問題集](faq.md)  
