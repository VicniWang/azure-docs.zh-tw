---
title: Azure PowerShell 指令碼範例 - 計算 Blob 容器大小 | Microsoft Docs
description: 您可以加總 Azure Blob 儲存體中每個 Blob 的大小，計算出 Azure Blob 儲存體的容器大小。
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: ec8254478786825d0333e8be24b0dfbea8c18e60
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245145"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>計算 Blob 儲存體容器的大小

此指令碼會加總容器中 Blob 的大小，計算出 Azure Blob 儲存體的容器大小。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> 此 PowerShell 指令碼提供容器的估計大小，不應用於計費計算。 如需針對帳單目的計算容器大小的指令碼，請參閱[針對帳單目的計算 Blob 儲存體容器的大小](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、容器和所有相關資源。

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來計算 Blob 儲存體容器的大小。 下表中的每個項目都會連結至特定命令的文件。

| 命令 | 注意 |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | 取得資源群組中或訂用帳戶中指定的儲存體帳戶或所有儲存體帳戶。 |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | 列出容器中的 Blob。 ||

## <a name="next-steps"></a>後續步驟

如需針對帳單目的計算容器大小的指令碼，請參閱[針對帳單目的計算 Blob 儲存體容器的大小](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)。

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

如需其他的儲存體 PowerShell 指令碼範例，可參閱 [Azure 儲存體的 PowerShell 範例](../blobs/storage-samples-blobs-powershell.md)。
