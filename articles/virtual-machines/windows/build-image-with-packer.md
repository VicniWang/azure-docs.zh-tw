---
title: 如何使用 Packer 在 Azure 中建立 Windows VM 映像 | Microsoft Docs
description: 了解如何在 Azure 中使用 Packer 建立 Windows 虛擬機器的映像
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/29/2018
ms.author: cynthn
ms.openlocfilehash: 0ae4c883baa156276646755273547a17d23edc55
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982483"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>如何在 Azure 中使用 Packer 來建立 Windows 虛擬機器映像
Azure 中的每個虛擬機器 (VM) 都是透過映像所建立，而映像則會定義 Windows 散發套件和作業系統版本。 映像中可包含預先安裝的應用程式與組態。 Azure Marketplace 提供了許多第一方和第三方映像，這些映像適用於最常見的作業系統和應用程式環境，而您也可以建立自己自訂的映像，以符合您的需求。 本文詳述如何使用開放原始碼工具 [Packer](https://www.packer.io/) \(英文\)，在 Azure 中定義和建置自訂映像。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="create-azure-resource-group"></a>建立 Azure 資源群組
建置程序進行期間，Packer 會在建置來源 VM 時建立暫存的 Azure 資源。 若要擷取該來源 VM 以作為映像，您必須定義資源群組。 Packer 建置程序所產生的輸出會儲存在此資源群組中。

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>建立 Azure 認證
Packer 會使用服務主體來向 Azure 驗證。 Azure 服務主體是安全性識別，可供您與應用程式、服務及諸如 Packer 等自動化工具搭配使用。 您可以控制和定義對於服務主體可以在 Azure 中執行哪些作業的權限。

使用 [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) 建立服務主體，並為服務主體指派權限以便使用 [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment) 來建立和管理資源。 使用您自己的密碼取代範例中的 &lt;密碼&gt;。  

```powershell
$sp = New-AzADServicePrincipal -DisplayName "AzurePacker" `
    -Password (ConvertTo-SecureString "<password>" -AsPlainText -Force)
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

若要向 Azure 驗證，您還需要使用 [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) 取得 Azure 租用戶與訂用帳戶識別碼：

```powershell
$sub = Get-AzSubscription
$sub.TenantId[0]
$sub.SubscriptionId[0]
```

您可以在下一個步驟中使用這兩個識別碼 。


## <a name="define-packer-template"></a>定義 Packer 範本
若要建置映像，您可以將範本建立為 JSON 檔案。 在此範本中，您必須定義產生器和佈建程式，由它們執行實際的建置程序。 Packer 具有[適用於 Azure 的產生器](https://www.packer.io/docs/builders/azure.html)，以供您定義 Azure 資源，例如上述步驟所建立的服務主體認證。

建立名為 windows.json 的檔案，並貼入下列內容。 針對下列參數輸入您自己的值︰

| 參數                           | 取得位置 |
|-------------------------------------|----------------------------------------------------|
| client_id                         | 檢視具有 `$sp.applicationId` 的服務主體識別碼 |
| client_secret                     | 您在 `$securePassword` 中指定的密碼 |
| tenant_id                         | `$sub.TenantId` 命令所產生的輸出 |
| subscription_id                   | `$sub.SubscriptionId` 命令所產生的輸出 |
| managed_image_resource_group_name | 您在第一個步驟中建立的資源群組名稱 |
| managed_image_name                | 所建立之受控磁碟映像的名稱 |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

此範本會建置 Windows Server 2016 VM、安裝 IIS，然後使用 Sysprep 將 VM 一般化。 IIS 安裝會示範如何使用 PowerShell 佈建程式來執行其他命令。 最終的 Packer 映像則會包含必要的軟體安裝和設定。


## <a name="build-packer-image"></a>建置 Packer 映像
如果您尚未在本機電腦上安裝 Packer，請[遵循 Packer 安裝指示](https://www.packer.io/docs/install/index.html)。

請指定 Packer 範本檔案來建置映像，如下所示：

```bash
./packer build windows.json
```

上述命令的輸出範例如下所示：

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer 需要幾分鐘的時間來建置 VM、執行佈建程式並清除部署。


## <a name="create-a-vm-from-the-packer-image"></a>從 Packer 映像建立 VM
您現在可以使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 從您的映像建立 VM。 如果支援網路資源尚未存在，則會加以建立。 出現提示時，輸入要在 VM 上建立的系統管理使用者名稱和密碼。 下列範例會從 *myPackerImage* 建立名為 *myVM* 的 VM：

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

如果想要在不同於您 Packer 映像的資源群組或區域中建立 VM，請指定映像識別碼，而非映像名稱。 您可以使用 [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage) 取得映像識別碼。

從 Packer 映像建立虛擬機器需要幾分鐘的時間。


## <a name="test-vm-and-webserver"></a>測試 VM 和網頁伺服器
使用 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 取得 VM 的公用 IP 位址。 下列範例會取得稍早建立的 myPublicIP IP 位址︰

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

若要查看包含來自 Packer 佈建程式之 IIS 安裝的 VM，請在網頁瀏覽器中輸入公用 IP 位址。

![IIS 預設網站](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>後續步驟
在此範例中，您已使用 Packer 來建立已安裝 IIS 的 VM 映像。 您可以使用這個 VM 映像以及現有的部署工作流程，來進行「將應用程式部署至使用 Azure DevOps Services、Ansible、Chef 或 Puppet 從映像所建立的 VM」之類的作業。

如需其他 Windows 散發套件的其他 Packer 範本範例，請參閱[這個 GitHub 存放庫](https://github.com/hashicorp/packer/tree/master/examples/azure)。
