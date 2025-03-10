---
title: 在 Azure 中建立 VHD 的快照集 | Microsoft Docs
description: 了解如何建立 Azure VHD 的複本作為備份，或用來針對問題進行疑難排解。
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 1015e6774dac1258820e3ca4b3d06786046a8554
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980851"
---
# <a name="create-a-snapshot"></a>建立快照集

快照集是完整的虛擬硬碟 (VHD) 唯讀複本。 您可以製作 OS 或資料磁碟 VHD 的快照集當作備份，或用來針對虛擬機器 (VM) 問題進行疑難排解。

如果您將使用快照集來建立新 VM，建議您完全關閉 VM 後再建立快照集，以清除所有進行中的處理序。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左側功能表中選取 [建立資源]，然後搜尋並選取 [快照集]。
3. 在 [快照集] 視窗中，選取 [建立]。 [建立快照集] 視窗隨即出現。
4. 輸入快照集的 [名稱]。
5. 選取現有的[資源群組](../../azure-resource-manager/resource-group-overview.md#resource-groups)，或輸入新資源群組的名稱。 
6. 選取 Azure 資料中心的 [位置]。  
7. 在 [來源磁碟] 中，選取要建立快照集的受控磁碟。
8. 選取用來儲存快照集的 [帳戶類型]。 除非需要將快照集存放在高效能磁碟上，否則選取 [Standard_HDD]。
9. 選取 [建立] 。

## <a name="use-powershell"></a>使用 PowerShell

下列步驟示範如何複製 VHD 磁碟、建立快照集組態，以及使用 [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) cmdlet 建立磁碟的快照集。 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

1. 設定一些參數： 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

2. 取得 VM：

 ```azurepowershell-interactive
$vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
```

3. 建立快照集組態。 在此範例中，快照集屬於 OS 磁碟：

 ```azurepowershell-interactive
$snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
```
   
   > [!NOTE]
   > 如果您想要將快照集儲存於區域復原的儲存體中，請在支援[可用性區域](../../availability-zones/az-overview.md)且包含 `-SkuName Standard_ZRS` 參數的區域中建立它。   
   
4. 製作快照集：

 ```azurepowershell-interactive
New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
```


## <a name="next-steps"></a>後續步驟

從快照集建立受控磁碟，然後連結新的受控磁碟作為 OS 磁碟，以從快照集建立虛擬機器。 如需詳細資訊，請參閱[使用 PowerShell 從快照集建立 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)。
