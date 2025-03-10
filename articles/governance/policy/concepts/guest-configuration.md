---
title: 了解如何在虛擬裝置內執行稽核
description: 了解「Azure 原則」如何使用「來賓設定」來稽核 Azure 虛擬機器內的設定。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: ca8066caf77852c3ec1a8bd7cb534e8d74704bf2
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447271"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure 原則的來賓設定

除了稽核和[補救](../how-to/remediate-resources.md) Azure 資源之外，「Azure 原則」還能稽核虛擬機器內的設定。 此驗證會由「來賓設定」延伸模組和用戶端執行。 延伸模組會透過用戶端來驗證設定，例如作業系統設定、應用程式設定或目前狀態、環境設定等。

> [!IMPORTANT]
> 目前，使用「來賓設定」時，僅支援**內建**原則。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>延伸模組和用戶端

為了稽核虛擬機器內的設定，會啟用[虛擬機器延伸模組](../../../virtual-machines/extensions/overview.md)。 延伸模組會下載適用的原則指派及相對應的設定定義。

### <a name="register-guest-configuration-resource-provider"></a>註冊來賓設定資源提供者

您必須先註冊資源提供者，才能使用「來賓設定」。 您可以透過入口網站或透過 PowerShell 註冊。

#### <a name="registration---portal"></a>註冊 - 入口網站

若要透過 Azure 入口網站註冊「來賓設定」的資源提供者，請依照下列步驟進行操作：

1. 啟動 Azure 入口網站，然後按一下 [所有服務]。 搜尋並選取 [訂用帳戶]。

1. 尋找並按一下您想要啟用「來賓設定」的訂用帳戶。

1. 在 [訂用帳戶] 頁面的左側功能表中，按一下 [資源提供者]。

1. 篩選或捲動，直到找出 **Microsoft.GuestConfiguration** 為止，然後按一下同一列上的 [註冊]。

#### <a name="registration---powershell"></a>註冊 - PowerShell

若要透過 PowerShell 註冊「來賓設定」的資源提供者，請執行下列命令：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>驗證工具

在虛擬機器內，「來賓設定」會使用本機工具來執行稽核。

下表顯示每個支援的作業系統上所使用的本機工具清單：

|作業系統|驗證工具|注意|
|-|-|-|
| Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
| Linux|[Chef InSpec](https://www.chef.io/inspec/)| 「來賓設定」延伸模組會安裝 Ruby 和 Python。 |

### <a name="validation-frequency"></a>驗證頻率

「來賓設定」用戶端會每隔 5 分鐘檢查一次是否有新內容。
一旦收到來賓指派，系統便會每隔 15 分鐘檢查一次設定。
稽核完成後，系統會立即將結果傳送給來賓設定資源提供者。
發生原則[評估觸發程序](../how-to/get-compliance-data.md#evaluation-triggers)時，系統會將機器的狀態寫入到來賓設定資源提供者。
這會導致 Azure 原則評估 Azure Resource Manager 屬性。
隨選原則評估會從來賓設定資源提供者擷取最新的值。
不過，該評估不會對虛擬機器內的設定觸發新的稽核作業。

### <a name="supported-client-types"></a>支援的用戶端類型

下表顯示 Azure 映像上的支援作業系統清單：

|發行者|Name|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 Datacenter、2012 R2 Datacenter、2016 Datacenter|
|OpenLogic|CentOS|7.3、7.4、7.5|
|Red Hat|Red Hat Enterprise Linux|7.4、7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> 自訂虛擬機器映像上目前不支援「來賓設定」。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

下表列出不支援的作業系統：

|作業系統|注意|
|-|-|
|Windows 用戶端 | 不支援用戶端作業系統 (例如 Windows 7 和 Windows 10)。
|Windows Server 2016 Nano Server | 不支援。|

## <a name="guest-configuration-definition-requirements"></a>來賓設定定義需求

「來賓設定」所執行的每個稽核都需要兩個原則定義：**DeployIfNotExists** 和 **Audit**。 **DeployIfNotExists** 可用來為虛擬機器準備「來賓設定」代理程式及其他用以支援[驗證工具](#validation-tools)的元件。

**DeployIfNotExists** 原則定義會驗證並修正下列項目：

- 驗證虛擬機器已獲指派將要評估的設定。 如果目前沒有任何指派，請透過下列方式取得指派並備妥虛擬機器：
  - 使用[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)向虛擬機器進行驗證
  - 安裝最新版的 **Microsoft.GuestConfiguration** 延伸模組
  - 安裝[驗證工具](#validation-tools)和相依性 (如有需要)

在 **DeployIfNotExists** 符合規範之後，**Audit** 原則定義會使用本機驗證工具來判斷所指派的設定指派項目是否符合規範。 驗證工具會將結果提供給「來賓設定」用戶端。 用戶端會將結果轉送至「來賓延伸模組」，以便透過「來賓設定」資源提供者提供結果。

「Azure 原則」會使用「來賓設定」資源提供者 **complianceStatus** 屬性在 [合規性] 節點中回報合規性。 如需詳細資訊，請參閱[取得合規性資料](../how-to/getting-compliance-data.md)。

> [!NOTE]
> 每個「來賓設定」定義都必須有 **DeployIfNotExists** 和 **Audit** 原則定義。

「來賓設定」的所有內建原則都包含在一個方案中，以聚集要在指派中使用的定義。 名為 *[預覽] 的內建計劃：Linux 及 Windows 虛擬機器內的「稽核密碼」安全性設定*包含 18 項原則。 針對 Windows 有 6 組 **DeployIfNotExists** 和 **Audit**，針對 Linux 則有 3 組。 在每個案例中，定義內的邏輯僅會驗證依據[原則規則](definition-structure.md#policy-rule)定義進行評估的目標作業系統。

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例
- 檢閱[原則定義結構](definition-structure.md)
- 檢閱[了解原則效果](effects.md)
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)
- 了解如何[取得合規性資料](../how-to/getting-compliance-data.md)
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)
- 檢閱[使用 Azure 管理群組來組織資源](../../management-groups/index.md)，以了解何謂管理群組
