---
title: 在 Azure 自動化混合式 Runbook 背景工作角色上執行 Runbook
description: 本文章提供在您的本機資料中心的電腦上執行 Runbook，或使用混合式 Runbook 背景工作角色之雲端提供者的相關資訊。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 35367a9ebc9ff09f40defd444f6ceb8ff54efe07
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430279"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作角色上執行 Runbook

在 Azure 自動化和混合式 Runbook 背景工作上執行的 Runbook 結構中沒有任何差異。 您使用的每個 Runbook 可能會有大幅差異。 此差異是因為以混合式 Runbook 背景工作角色為目標的 Runbook 通常會管理本機電腦上的資源，或管理部署所在本機環境中的資源。 Azure 自動化中的 Runbook 通常會管理 Azure 雲端中的資源。

當您撰寫要在混合式 Runbook 背景工作角色上執行的 Runbook 時，應該在裝載混合式背景工作角色的機器內編輯並測試 Runbook。 主機電腦具有您管理及存取本機資源所需要的所有 PowerShell 模組和網路存取。 一旦在混合式背景工作角色機器上測試 Runbook 後，接著可以將其上傳至 Azure 自動化環境，其可供您在混合式背景工作角色中執行。 請務必知道，作業會以本機系統帳戶在 Windows 上執行，或以特殊使用者帳戶 `nxautomation` 在 Linux 上執行。 針對混合式 Runbook 背景工作角色撰寫 Runbook 時，此行為可能會造成些微的差異。 當您撰寫 Runbook 時，應該檢閱這些變更。

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作角色上啟動 Runbook

[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 描述啟動 Runbook 的不同方法。 混合式 Runbook 背景工作加入了 **RunOn** 選項，您可以在其中指定混合式 Runbook 背景工作群組的名稱。 若已指定群組，則會擷取 Runbook，且由該群組中的其中一個背景工作角色執行。 如果未指定此選項，則會在 Azure 自動化中正常執行。

在 Azure 入口網站中啟動 Runbook 時，您會看到 [執行於] 選項，您可以在此選取 [Azure] 或 [Hybrid Worker]。 如果選取 [Hybrid 背景工作角色]，則您可以從下拉式清單中選取群組。

使用 **RunOn** 參數。 您可以使用 Windows PowerShell 以下列命令在 Hybrid Runbook Worker 群組上啟動名為 Test-Runbook 的 Runbook。

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> 0.9.1 版 Microsoft Azure PowerShell 的 **Start-AzureAutomationRunbook** Cmdlet 已新增 **RunOn** 參數。 如果您安裝較早的版本，您應該 [下載最新版本](https://azure.microsoft.com/downloads/) 。 您只需要在從 PowerShell 啟動 Runbook 的工作站上安裝此版本。 您不需要將它安裝在背景工作電腦上，除非您想要從該電腦啟動 Runbook。

## <a name="runbook-permissions"></a>Runbook 權限

在混合式 Runbook 背景工作角色上執行的 Runbook 不能使用通常用於 Runbook 對 Azure 資源進行驗證的相同方法，因為它們會存取 Azure 外部的資源。 Runbook 可以提供自己的驗證給本機資源，或使用 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
)來設定驗證。 您也可以指定 RunAs 帳戶以對所有 Runbook 提供使用者內容。

### <a name="runbook-authentication"></a>Runbook 驗證

根據預設，內部部署電腦上的 Runbook 是在 Windows 的本機系統帳戶內容中或是 Linux 的特殊使用者帳戶 `nxautomation` 內容中執行，因此，它們必須對要存取的資源提供自己的驗證。

您可以在您的 Runbook 中使用[認證](automation-credentials.md)和[憑證](automation-certificates.md)資產，搭配可讓您指定認證的 Cmdlet，您就能夠向不同的資源驗證。 下列範例顯示會重新啟動電腦的 Runbook 的一部分。 它會從認證資產擷取認證和從變數資產擷取電腦的名稱，然後使用這些值搭配 Restart-Computer Cmdlet。

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

您也可以使用 [InlineScript](automation-powershell-workflow.md#inlinescript)，它可讓您利用 [PSCredential 一般參數](/powershell/module/psworkflow/about/about_workflowcommonparameters)指定的認證在另一部電腦上執行程式碼區塊。

### <a name="runas-account"></a>RunAs 帳戶

根據預設，混合式 Runbook 背景工作角色會使用 Windows 的本機系統和 Linux 的特殊使用者帳戶 `nxautomation` 來執行 Runbook。 您不需要讓 Runbook 提供自己的驗證給本機資源，您可以針對混合式背景工作角色群組指定 **RunAs** 帳戶。 您指定具有本機資源存取權的 [認證資產](automation-credentials.md)，在群組中的 Hybrid Runbook Worker 執行時，所有 Runbook 會在這些認證下執行。

認證的使用者名稱必須是下列格式之一：

* 網域\使用者名稱
* username@domain
* 使用者名稱 (適用於內部部署機器的本機帳戶)

使用下列程序以針對混合式背景工作角色群組指定 RunAs 帳戶：

1. 建立具有本機資源存取權的 [認證資產](automation-credentials.md) 。
2. 在 Azure 入口網站中，開啟自動化帳戶。
3. 選取 [Hybrid Worker 群組]  圖格，然後選取群組。
4. 選取 [所有設定]，然後選取 [Hybrid 背景工作角色群組設定]。
5. 將 [執行身分] 從 [預設] 變更為 [自訂]。
6. 選取認證，然後按一下 [儲存] 。

### <a name="managed-identities-for-azure-resources"></a>適用於 Azure 資源的受控識別

在 Azure 虛擬機器上執行的混合式 Runbook 背景工作角色，可以使用 Azure 資源的受控識別來向 Azure 資源進行驗證。 透過執行身分帳戶使用 Azure 資源的受控識別有許多優點。

* 不需要匯出執行身分憑證，然後將其匯入混合式 Runbook 背景工作角色
* 不需要更新執行身分帳戶所使用的憑證
* 不需要處理 Runbook 程式碼中的執行身分連線物件

若要在混合式 Runbook 背景工作角色上使用 Azure 資源的受控識別，您需要完成下列步驟：

1. 建立 Azure VM
2. [在 VM 上設定 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [在 Resource Manager 中將您的 VM 存取權授與資源群組](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [使用 VM 系統指派的受控識別來取得存取權杖](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. 在虛擬機器上[安裝 Windows 混合式 Runbook 背景工作角色](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker)。

完成上述步驟後，您可以使用 Runbook 中的 `Connect-AzureRmAccount -Identity` 對 Azure 資源進行驗證。 此組態可減少使用執行身分帳戶及管理執行身分帳戶憑證的需求。

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>自動化執行身分帳戶

在 Azure 中部署資源的自動化建置流程中，您可能需要存取內部部署系統來支援部署序列中的工作或一組步驟。 若要支援使用「執行身分」帳戶向 Azure 驗證，您需要安裝「執行身分」帳戶憑證。

下列 PowerShell Runbook **Export-RunAsCertificateToHybridWorker** 會從 Azure 自動化帳戶匯出「執行身分」憑證，並將它下載和匯入到混合式背景工作角色 (連線至相同帳戶) 上的本機電腦憑證存放區。 完成該步驟後，它會確認背景工作角色可以使用執行身分帳戶成功向 Azure 驗證。

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** 現在是 **Connect-AzureRMAccount** 的別名。 搜尋您的程式庫項目時，如果沒有看到 **Connect-AzureRMAccount**，便可以使用 **Add-AzureRmAccount**，或是在自動化帳戶中更新模組。

將 *Export-RunAsCertificateToHybridWorker* Runbook 以 `.ps1` 副檔名儲存至您的電腦。 將它匯入您的自動化帳戶，並編輯 Runbook，將變數 `$Password` 的值變更為您自己的密碼。 發佈而後執行 Runbook。 以使用「執行身分」帳戶執行和驗證 Runbook 的混合式背景工作角色群組為目標。 作業資料流會報告將憑證匯入本機電腦存放區的嘗試，後面還會出現幾行。 此行為根據訂用帳戶中定義多少自動化帳戶和驗證是否成功而定。

## <a name="job-behavior"></a>作業行為

作業在混合式 Runbook 背景工作角色上與在 Azure 沙箱上執行時的處理方式會有些許不同。 其中一個主要差異，在於混合式 Runbook 背景工作角色上並沒有限制作業持續時間。 由於[公平共用](automation-runbook-execution.md#fair-share)，在 Azure 沙箱中執行的 Runbook 會限制為 3 小時。 對於長時間執行的 Runbook，您要確定能夠接受可能重新啟動。 例如，若裝載混合式背景工作角色的電腦重新開機。 如果混合式背景工作角色主機電腦重新開機，則任何執行中的 Runbook 作業都會從頭重新啟動，或是從 PowerShell 工作流程 Runbook 的最後一個檢查點重新啟動。 在 Runbook 作業重新啟動超過 3 次之後，它就會暫止。

## <a name="run-only-signed-runbooks"></a>僅執行已簽署的 Runbook

混合式 Runbook 背景工作角色可使用某些設定來設定為只能執行已簽署的 Runbook。 下一節將說明如何設定混合式 Runbook 背景工作角色來執行已簽署的 [Windows 混合式 Runbook 背景工作角色](#windows-hybrid-runbook-worker)和 [Linux 混合式 Runbook 背景工作角色](#linux-hybrid-runbook-worker)。

> [!NOTE]
> 一旦將混合式 Runbook 背景工作角色設定為只能執行已簽署的 Runbook，尚**未**經過簽署的 Runbook 將無法在背景工作角色上執行。

### <a name="windows-hybrid-runbook-worker"></a>Windows 混合式 Runbook 背景工作角色

#### <a name="create-signing-certificate"></a>建立簽署憑證

下列範例會建立可用於簽署 Runbook 的自我簽署憑證。 此範例會建立憑證並將它匯出。 稍後會將該憑證匯入到混合式 Runbook 背景工作角色。 同時也會傳回指紋，此值稍後將用來參考憑證。

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

#### <a name="configure-the-hybrid-runbook-workers"></a>設定混合式 Runbook 背景工作

將建立的憑證複製到群組中的每個混合式 Runbook 背景工作角色。 執行下列指令碼來匯入憑證並設定混合式背景工作角色，以便在 Runbook 中使用簽章驗證。

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>使用憑證簽署您的 Runbook

將混合式 Runbook 背景工作角色設定為僅使用已簽署的 Runbook，您就必須簽署要在混合式 Runbook 背景工作角色上使用的 Runbook。 請使用下列範例 PowerShell 來簽署您的 Runbook。

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

簽署 Runbook 後，就必須將它匯入到您的自動化帳戶，並與簽章區塊一起發佈。 若要了解如何匯入 Runbook，請參閱[將 Runbook 從檔案匯入 Azure 自動化](manage-runbooks.md#import-a-runbook)。

### <a name="linux-hybrid-runbook-worker"></a>Linux 混合式 Runbook 背景工作角色

若要登入 Linux 混合式 Runbook 背景工作角色上的 Runbook，您的混合式 Runbook 背景工作角色在該機器上必須有 [GPG](https://gnupg.org/index.html) \(英文\) 可執行檔。

#### <a name="create-a-gpg-keyring-and-keypair"></a>建立 GPG Keyring 和金鑰組

若要建立 Keyring 和金鑰組，您將需使用混合式 Runbook 背景工作角色帳戶 `nxautomation`。

使用 `sudo` 登入為 `nxautomation` 帳戶。

```bash
sudo su – nxautomation
```

使用 `nxautomation` 帳戶之後，請產生 GPG 金鑰組。

```bash
sudo gpg --generate-key
```

GPG 將引導您完成建立金鑰組的步驟。 您將需針對要產生的金鑰提供名稱、電子郵件地址、到期時間、複雜密碼，以及在該機器上等候足夠的熵。

由於 GPG 目錄是使用 sudo 所產生的，因此，您需要將其擁有者變更為 `nxautomation`。 

執行下列命令以變更擁有者。

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>使 Keyring 可供混合式 Runbook 背景工作角色使用

建立 Keyring 之後，您必須使它可供混合式 Runbook 背景工作角色使用。 修改設定檔 `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf`，在 `[worker-optional]` 區段下方包含下列範例。

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>確認簽章驗證已開啟

如果已在機器上停用簽章驗證，您必須將它開啟。 執行下列命令以開啟簽章驗證。 使用您的工作區識別碼來取代 `<LogAnalyticsworkspaceId>`。

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>簽署 Runbook

一旦設定簽章驗證之後，就能使用下列命令來登入 Runbook：

```bash
gpg –-clear-sign <runbook name>
```

已簽署的 Runbook 將具有 `<runbook name>.asc` 名稱。

已簽署的 Runbook 可立即上傳至 Azure 自動化，而且可以像一般 Runbook 一樣執行。

## <a name="troubleshoot"></a>疑難排解

如果您的 Runbook 未順利完成，請檢閱 [Runbook 執行失敗](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)中的疑難排解指南。

## <a name="next-steps"></a>後續步驟

* 若要深入了解可用來啟動 Runbook 的不同方法，請參閱[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)。
* 若要了解使用文字式編輯器在 Azure 自動化中處理 PowerShell Runbook 的不同方式，請參閱[在 Azure 自動化中編輯 Runbook](automation-edit-textual-runbook.md)

