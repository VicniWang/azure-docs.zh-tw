---
title: 從您的 Microsoft Azure Marketplace VHD 部署 VM | Microsoft Docs
description: 說明如何從部署的 Azure VHD 註冊 VM。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 3ab98669e01c9cfb2d4f46b8ddd83ff69653337b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434167"
---
# <a name="deploy-a-vm-from-your-vhds"></a>從您的 VHD 部署 VM

本節說明如何從 Azure 部署的虛擬硬碟 (VHD) 部署虛擬機器 (VM)。  文中會列出必要的工具，並說明如何使用這些工具建立使用者 VM 映像，然後再使用 PowerShell 指令碼將該映像部署至 Azure。

在您將虛擬硬碟 (VHD)，即一般化作業系統 VHD 和零或更多個資料磁碟 VHD，上傳至 Azure 儲存體帳戶之後，您就可以將它們註冊為使用者 VM 映像。 您可以接著測試該映像。 請注意，因為您的作業系統 VHD 已一般化，所以您無法藉由提供 VHD URL 來直接部署 VM。

若要深入了解 VM 映像，請參閱下列部落格文章：

- [VM 映像](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM 映像 PowerShell 使用說明](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/) (英文)


## <a name="prerequisite-install-the-necessary-tools"></a>必要條件：安裝所需的工具

如果尚未設定，請依照下列指示安裝 Azure PowerShell 和 Azure CLI：

- [使用 PowerShellGet 在 Windows 上安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)
- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>部署步驟

您將使用下列步驟建立及部署使用者 VM 映像：

1. 建立負責擷取映像並將其一般化的使用者 VM 映像。 
2. 建立憑證，並將其儲存在新的 Azure Key Vault 中。 與 VM 建立安全 WinRM 連線所需的憑證。  系統會提供 Azure Resource Manager 範本和 Azure PowerShell 指令碼。 
3. 使用提供的範本和指令碼，從使用者 VM 映像部署 VM。

VM 部署完成後，您便可準備開始[認證您的 VM 映像](./cpp-certify-vm.md)。

2.  按一下 [新增] 並搜尋**範本部署**，然後選取 [在編輯器中建置自己的範本]。  <br/>
  ![在 Microsoft Azure 入口網站建置 VHD 部署範本](./media/publishvm_021.png)

3. 複製並將此 [JSON 範本](./cpp-deploy-json-template.md)貼上至編輯器，然後按一下 [儲存]。 <br/>
  ![將 VHD 部署範本儲存於 Microsoft Azure 入口網站](./media/publishvm_022.png)

4. 為顯示的**自訂部署**屬性頁面提供參數值。

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **參數**              |   **說明**                                                            |
   |  -------------              |   ---------------                                                            |
   | 儲存體帳戶名稱   | 一般化 VHD 所在的儲存體帳戶名稱                    |
   | 輸入儲存體容器名稱 | 一般化 VHD 所在的容器名稱                          |
   | 公用 IP 的 DNS 名稱      | 公用 IP 的 DNS 名稱                                                           |
   | 管理員使用者名稱             | 新 VM 系統管理員帳戶的使用者名稱                                  |
   | 管理員密碼              | 新 VM 管理員帳戶密碼                                  |
   | OS 類型                     | VM 作業系統：`Windows` \| `Linux`                                    |
   | 訂用帳戶識別碼             | 讓選取的訂用帳戶取得安全性建議                                      |
   | 位置                    | 部署的地理位置                                        |
   | VM 大小                     | [Azure VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)，例如`Standard_A2` |
   | 公用 IP 位址      | 您的公用 IP 位址名稱                                               |
   | 虛擬機器名稱                     | 新 VM 名稱                                                           |
   | 虛擬網路名稱        | VM 使用的虛擬網路名稱                                   |
   | NIC 名稱                    | 正在執行虛擬網路的網路介面卡名稱               |
   | VHD URL                     | 完整的 OS 磁碟 VHD URL                                                     |
   |  |  |
            
5. 在您提供這些數值後，請按一下 [採購]。 

Azure 會開始部署： 它會建立新的 VM，附上指定的非受控 VHD，存放於指定的儲存體帳戶路徑中。  您可以在 Microsoft Azure 入口網站中按一下左側的 [虛擬機器] 來追蹤進度。  建立 VM 後，狀態會從`Starting`變更為`Running`。 


### <a name="deploy-a-vm-from-powershell"></a>從 PowerShell 部署 VM

若要從剛才建立的一般化 VM 映像部署大型 VM，請使用下列 Cmdlet。

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>後續步驟

接下來，您將為您的解決方案[建立使用者 VM 映像](cpp-create-user-image.md)。

