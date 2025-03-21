---
title: 內部部署 Azure AD 密碼保護代理程式版本發行記錄
description: 文件版本發行和行為變更歷程記錄
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8897651c963b0036bc2ac3d8cdb06a52d6f52ba1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188030"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>預覽：Azure AD 密碼保護代理程式版本記錄

|     |
| --- |
| Azure AD 密碼保護是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12650"></a>1.2.65.0

發行日期：2/1/2019

變更：

* Server Core 上現在支援 DC 代理程式和 Proxy 服務。 最低 OS 需求則與之前相同：適用於 DC 代理程式的 Windows Server 2012 和適用於 Proxy 的 Windows Server 2012 R2。
* Register-AzureADPasswordProtectionProxy 與 Register-AzureADPasswordProtectionForest Cmdlet 現在均支援以裝置代碼為基礎的 Azure 驗證模式。
* Get-AzureADPasswordProtectionDCAgent Cmdlet 將會忽略已損害和/或無效的服務連接點。 這會修正網域控制站有時在輸出中多次出現的錯誤 (bug)。
* Get-AzureADPasswordProtectionSummaryReport Cmdlet 將會忽略已損害和/或無效的服務連接點。 這會修正網域控制站有時在輸出中多次出現的錯誤 (bug)。
* 現在已從 %ProgramFiles%\WindowsPowerShell\Modules 註冊 Proxy PowerShell 模組。 機器的 PSModulePath 環境變數已不再修改。
* 已新增新的 Get-AzureADPasswordProtectionProxy Cmdlet，以協助在樹系或網域中探索已註冊的 Proxy。
* DC 代理程式會在 sysvol 共用中使用新的資料夾，來複寫密碼原則和其他檔案。

   舊的資料夾位置：

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   新的資料夾位置：

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (進行此變更是為了避免誤判「孤立的 GPO」警告。)

   > [!NOTE]
   > 系統將不會在舊的資料夾和新的資料夾之間完成任何資料的移轉或共用。 舊版 DC 代理程式將繼續使用舊的位置，直到升級至此版本或更新版本。 一旦所有 DC 代理程式都執行版本 1.2.65.0 或更新版本之後，就能手動刪除舊的 sysvol 資料夾。

* DC 代理程式和 Proxy 服務現在將會偵測並刪除其個別服務連接點的已損害複本。
* 每個 DC 代理程式都將在其網域中，針對 DC 代理程式和 Proxy 服務連接點，定期刪除已損害和過時的服務連接點。 如果它的活動訊號時間戳記超過七天，則會將 DC 代理程式和 Proxy 服務連接點視為過時。
* DC 代理程式現在將視需要來更新樹系憑證。
* Proxy 服務現在將視需要來更新 Proxy 憑證。
* 密碼驗證演算法的更新：會先將全域禁用密碼清單和客戶專屬的禁用密碼清單 (若已設定) 組合在一起，然後再進行密碼驗證。 如果指定的密碼包含來自全域和客戶專屬清單的權杖，則該密碼現在可能會遭到拒絕 (失敗或僅限稽核)。 事件記錄檔文件已更新以反映此資訊；請參閱[監視 Azure AD 密碼保護](howto-password-ban-bad-on-premises-monitor.md)。
* 效能和穩健性修正
* 改進記錄功能

> [!WARNING]
> 限時功能：此版本 (1.2.65.0) 中的 DC 代理程式服務將自 2019 年 9 月 1 日起停止處理密碼驗證要求。  舊版 (請參閱下列清單) 中的 DC 代理程式服務將自 2019 年 7 月 1 日起停止處理。 所有版本中的 DC 代理程式服務都會在到達這些截止日期之前的兩個月內，將 10021 事件記錄至系統管理事件記錄檔。 所有限時的限制都將從即將發行的 GA 版本中移除。 Proxy 代理程式服務在所有版本中都不是限時的，但仍應升級至最新版本，以利用所有後續的錯誤 (bug) 修正和其他改良。

## <a name="12250"></a>1.2.25.0

發行日期：2018/11/01

修正：

* DC 代理程式和 Proxy 服務應該不會再因為憑證信任失敗而失敗。
* DC 代理程式和 Proxy 服務有適用於 FIPS 合規電腦的其他修正程式。
* Proxy 服務現在將可在只有 TLS 1.2 的網路環境中正常運作。
* 次要的效能和穩健性修正
* 改進記錄功能

變更：

* 現在 Proxy 服務的最低必要作業系統層級是 Windows Server 2012 R2。 DC 代理程式服務的最低必要作業系統層級維持為 Windows Server 2012。
* Proxy 服務現在需要 .NET 版本 4.6.2。
* 密碼驗證演算法採用擴充字元正規化資料表。 這可能會導致在舊版中接受的密碼遭到拒絕。

## <a name="12100"></a>1.2.10.0

發行日期：2018/8/17

修正：

* Register-AzureADPasswordProtectionProxy 與 Register-AzureADPasswordProtectionForest 現在支援多重要素驗證
* Register-AzureADPasswordProtectionProxy 需要網域中有 WS2012 或更新版本的網域控制站，避免發生加密錯誤。
* 啟動時向 Azure 要求新的密碼原則，使用 DC 代理程式服務較可靠。
* DC 代理程式服務會在必要時，每小時向 Azure 要求新的密碼原則，但現在能在隨機選取的開始時間進行。
* 在升階之前於伺服器上安裝為複本時，DC 代理程式服務不會再會導致新的 DC 公告無限期延遲。
* DC 代理程式服務現在允許 [啟用 Windows Server Active Directory 上的密碼保護] 組態設定
* 當升級至未來的版本時，DC 代理程式與 Proxy 安裝程式現在支援就地升級。

> [!WARNING]
> 不支援從 1.1.10.3 版就地升級，而且會造成安裝錯誤。 若要升級至 1.2.10 版或更新版本，您必須先將 DC 代理程式與 Proxy 服務軟體完全解決安裝，再從頭開始安裝新版本。 需要重新註冊 Azure AD 密碼保護 Proxy 服務。  不需要重新註冊樹系。

> [!NOTE]
> 就地升級 DC 代理程式軟體需要重新開機。

* DC 代理程式和 Proxy 服務現在支援在設定為僅使用 FIPS 相容演算法的伺服器上執行。
* 次要的效能和穩健性修正
* 改進記錄功能

## <a name="11103"></a>1.1.10.3

發行日期：2018/6/15

初始公開預覽版本

## <a name="next-steps"></a>後續步驟

[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)
