---
title: Azure Key Vault - 如何以 PowerShell 使用虛刪除
description: 以 PowerShell 程式碼片段進行虛刪除的使用案例範例
author: bryanla
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: bryanla
ms.openlocfilehash: 70437403d3b78b7f8b9eef921c933a68793450da
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113578"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>如何使用 Key Vault 虛刪除與 PowerShell

Azure Key Vault 的虛刪除功能可復原已刪除的保存庫和保存庫物件。 具體而言，虛刪除解決下列案例：

- 可復原的 Key Vault 刪除支援
- 支援可復原的金鑰保存庫物件刪除；金鑰、密碼和憑證

## <a name="prerequisites"></a>先決條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 或更新版本 - 如果您尚未安裝，請安裝 Azure PowerShell，並將它與 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) \(英文\)。 

>[!NOTE]
> 載入您的環境的**可能**是過期版本的 Key Vault PowerShell 輸出格式檔案，而不是正確版本。 我們預計 PowerShell 的更新版本會包含輸出格式所需的更正，屆時將更新此主題。 如果您遇到此格式問題，目前的因應措施是：
> - 如果您注意到您看不到此主題中所述的已啟用虛刪除的屬性，請使用下列查詢：`$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`。


如需 PowerShell 的 Key Vault 特定參考資訊，請參閱 [Azure Key Vault PowerShell 參考](/powershell/module/az.keyvault)。

## <a name="required-permissions"></a>所需的權限

Key Vault 作業透過角色型存取控制 (RBAC) 權限來分別管理，如下所示：

| 作業 | 說明 | 使用者權限 |
|:--|:--|:--|
|列出|列出已刪除的金鑰保存庫。|Microsoft.KeyVault/deletedVaults/read|
|復原|還原已刪除的金鑰保存庫。|Microsoft.KeyVault/vaults/write|
|清除|永久移除已刪除的金鑰保存庫和其所有內容。|Microsoft.KeyVault/locations/deletedVaults/purge/action|

如需權限和存取控制的詳細資訊，請參閱[保護您的金鑰保存庫](key-vault-secure-your-key-vault.md)。

## <a name="enabling-soft-delete"></a>啟用虛刪除

您可啟用「虛刪除」來允許復原已刪除的金鑰保存庫，或金鑰保存庫中儲存的物件。

> [!IMPORTANT]
> 在金鑰保存庫上的啟用「虛刪除」是無法復原的動作。 將虛刪除屬性設定為 "true" 後，就無法加以變更或移除。  

### <a name="existing-key-vault"></a>現有的金鑰保存庫

對於名為 ContosoVault 的現有金鑰保存庫啟用虛刪除，如下所示。 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>新的金鑰保存庫

為新的金鑰保存庫啟用虛刪除是在建立時完成，方法是新增虛刪除啟用旗標到您的 create 命令。

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>驗證啟用虛刪除

若要驗證金鑰保存庫已啟用虛刪除，請執行 *show* 命令，並尋找「虛刪除已啟用?」 屬性：

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>刪除虛刪除所保護的金鑰保存庫

用以刪除金鑰保存庫行為變更的命令，取決於是否啟用虛刪除。

> [!IMPORTANT]
>如果您針對未啟用虛刪除的金鑰保存庫執行下列命令，您將永久刪除此金鑰保存庫及其所有的內容，並且沒有任何選項可供復原！

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>虛刪除如何保護您的金鑰保存庫

已啟用虛刪除：

- 已刪除的金鑰保存庫會從其資源群組中移除，並放置於與其建立所在位置相關聯的保留命名空間中。 
- 已刪除的物件 (例如金鑰、祕密和憑證) 都無法存取，而且只要其包含金鑰保存庫處於已刪除狀態便無法存取。 
- 系統會保留已刪除金鑰的 DNS 名稱，以免建立同名的金鑰保存庫。  

您可以檢視與您的訂用帳戶建立關聯的已刪除狀態金鑰保存庫，請使用下列命令：

```powershell
PS C:\> Get-AzKeyVault -InRemovedState 
```

- 「識別碼」可以用來在復原或清除時識別資源。 
- 「資源識別碼」是此保存庫的原始資源識別碼。 因為此金鑰保存庫目前處於已刪除狀態，所以沒有具有該資源識別碼的資源存在。 
- 如果不採取任何動作，「排定清除日期」就是永久刪除保存庫的時間。 用來計算「排定清除日期」的預設保留期間為 90 天。

## <a name="recovering-a-key-vault"></a>復原金鑰保存庫

若要復原金鑰保存庫，您可指定金鑰保存庫名稱、資源群組和位置。 請記下已刪除之金鑰保存庫的位置和資源群組，因為您需要這些才能進行復原程序。

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

復原金鑰保存庫後，則會使用使用金鑰保存庫的原始資源識別碼建立新的資源。 如果原始資源群組已遭移除，則必須會建立一個同名的資源群組，再嘗試復原。

## <a name="deleting-and-purging-key-vault-objects"></a>刪除並清除金鑰保存庫物件

下列命令會在名為 'ContosoVault' 的金鑰保存庫中，刪除已啟用虛刪除的 'ContosoFirstKey' 金鑰：

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

在金鑰保存庫啟用虛刪除的情況下，已刪除的金鑰仍會看似已刪除，除非您明確地列出已刪除的金鑰。 對於已刪除狀態的金鑰，大部分作業會失敗，除了列出、復原或清除已刪除的金鑰時例外。 

例如，下列命令會列出 'ContosoVault' 金鑰保存庫中已刪除的金鑰：

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>轉換狀態 

當您刪除金鑰保存庫中的金鑰，並且已啟用虛刪除時，可能需要幾秒鐘的時間讓轉換完成。 在此轉換期間，可能會出現金鑰不在使用中狀態或已刪除狀態。 

### <a name="using-soft-delete-with-key-vault-objects"></a>對金鑰保存庫物件使用虛刪除

就像金鑰保存庫，已刪除的金鑰、祕密或憑證仍會維持已刪除狀態長達 90 天，除非加以復原或清除。 

#### <a name="keys"></a>金鑰

若要復原虛刪除的金鑰：

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

若要永久刪除 (也稱為清除) 虛刪除的金鑰：

> [!IMPORTANT]
> 清除金鑰會永久刪除它，而且無法復原！ 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

**復原**和**清除**動作在金鑰保存庫存取原則中有自己的相關聯權限。 若要讓使用者或服務主體能夠執行**復原**或**清除**動作，他們必須具有該金鑰或祕密的個別權限。 根據預設，當 'all' 捷徑用於授與所有權限時，**清除**不會新增到金鑰保存庫的存取原則。 您必須明確授與**清除**權限。 

#### <a name="set-a-key-vault-access-policy"></a>設定金鑰保存庫存取原則

下列命令授與 user@contoso.com 對 *ContosoVault* 中的金鑰執行數種作業的權限，這其中包括**清除**：

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> 如果您有現有的金鑰保存庫，且剛剛啟用虛刪除，您可能沒有**復原**和**清除**權限。

#### <a name="secrets"></a>密碼

就像金鑰，祕密會以其自己的命令進行管理：

- 刪除名為 SQLPassword 的密碼： 
```powershell
Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- 列出金鑰保存庫中的所有已刪除密碼： 
```powershell
Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- 復原已刪除狀態的密碼： 
```powershell
Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- 清除已刪除狀態的密碼： 

  > [!IMPORTANT]
  > 清除祕密會將它永久刪除，而且無法復原！

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>清除虛刪除所保護的金鑰保存庫

> [!IMPORTANT]
> 清除金鑰保存庫或其內含的物件之一，就會永久刪除它，這表示無法復原！

清除函式用來永久刪除金鑰保存庫物件或整個金鑰保存庫，也就是先前虛刪除的項目。 如上一節所示範，儲存在已啟用虛刪除功能的金鑰保存庫中的物件可能經歷多個狀態：
- **作用中**：刪除之前。
- **虛刪除**：刪除之後，能夠列出並復原回到作用中狀態。
- **永久刪除**：清除之後，無法復原。


同樣的作法也適用於金鑰保存庫。 若要永久刪除虛刪除的金鑰保存庫及其內容，您必須清除金鑰保存庫本身。

### <a name="purging-a-key-vault"></a>清除金鑰保存庫

清除金鑰保存庫後，它的整個內容 (包括金鑰、祕密和憑證) 都會永久刪除。 若要清除虛刪除的金鑰保存庫，請使用 `Remove-AzKeyVault` 命令搭配 `-InRemovedState` 選項，並使用 `-Location location` 引數指定已刪除金鑰保存庫的位置。 您可以使用命令 `Get-AzKeyVault -InRemovedState` 找到已刪除之保存庫的位置。

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>所需的清除權限
- 若要清除已刪除的金鑰保存庫，使用者需要執行 *Microsoft.KeyVault/locations/deletedVaults/purge/action* 作業的 RBAC 權限。 
- 若要列出已刪除的金鑰保存庫，使用者需要執行 *Microsoft.KeyVault/deletedVaults/read* 作業的 RBAC 權限。 
- 根據預設，只有訂用帳戶管理員擁有這些權限。 

### <a name="scheduled-purge"></a>排定的清除

列出已刪除的金鑰保存庫物件，也會顯示它們排定要由金鑰保存庫清除的時間。 如果不採取任何動作，「排定清除日期」指出何時會永久刪除金鑰保存庫物件。 根據預設，已刪除的金鑰保存庫物件的保留期限為 90 天。

>[!IMPORTANT]
>已清除的保存庫物件，由其「排定清除日期」欄位觸發，會永久刪除。 它無法復原！

## <a name="other-resources"></a>其他資源

- 如需 Key Vault 的虛刪除功能概觀，請參閱 [Azure Key Vault 虛刪除概觀](key-vault-ovw-soft-delete.md)。
- 如需 Azure Key Vault 使用方式的一般概觀，請參閱[什麼是 Azure Key Vault？](key-vault-overview.md)。

