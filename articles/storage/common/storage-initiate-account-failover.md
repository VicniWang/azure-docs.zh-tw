---
title: 起始儲存體帳戶容錯移轉 (預覽) - Azure 儲存體
description: 了解如何在儲存體帳戶的主要端點無法使用時起始帳戶容錯移轉。 容錯移轉會更新次要區域，使其成為您儲存體帳戶的主要區域。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.component: common
ms.openlocfilehash: d1fb921f72af58eacde5f1ef35ee3aec80f767c8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55994671"
---
# <a name="initiate-a-storage-account-failover-preview"></a>起始儲存體帳戶容錯移轉 (預覽)

當您異地備援儲存體帳戶的主要端點因故無法使用時，您可以起始帳戶容錯移轉 (預覽)。 帳戶容錯移轉會更新次要端點，使其成為您儲存體帳戶的主要端點。 容錯移轉完成後，用戶端便可以開始寫入到新的主要區域。 強制容錯移轉可讓您維護應用程式的高可用性。

本文說明如何使用 Azure 入口網站、PowerShell 或 Azure CLI 為您的儲存體帳戶起始帳戶容錯移轉。 若要深入了解帳戶容錯移轉，請參閱 [Azure 儲存體中的災害復原和帳戶容錯移轉 (預覽)](storage-disaster-recovery-guidance.md)。

> [!WARNING]
> 帳戶容錯移轉通常會導致一些資料遺失。 若要了解帳戶容錯移轉的影響並預先因應資料遺失，請檢閱[了解帳戶容錯移轉程序](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)。

## <a name="prerequisites"></a>必要條件

您必須先確實執行下列步驟，才可開始對儲存體帳戶執行帳戶容錯移轉：

- 註冊帳戶容錯移轉預覽版。 如需註冊方式的相關資訊，請參閱[關於預覽版](storage-disaster-recovery-guidance.md#about-the-preview)。
- 確定您的儲存體帳戶已設定為使用異地備援儲存體 (GRS) 或讀取權限異地備援儲存體 (RA-GRS)。 如需異地備援儲存體的詳細資訊，請參閱[異地備援儲存體 (GRS)：適用於 Azure 儲存體的跨區域複寫](storage-redundancy-grs.md)。 

## <a name="important-implications-of-account-failover"></a>帳戶容錯移轉的重要影響

當您為儲存體帳戶起始帳戶容錯移轉時，次要端點的 DNS 記錄將會更新，使次要端點成為主要端點。 在起始容錯移轉之前，請確實了解儲存體帳戶可能會受到的影響。

若要在起始容錯移轉之前評估可能的資料遺失程度，請使用 `Get-AzureRmStorageAccount` PowerShell Cmdlet 並搭配 `-IncludeGeoReplicationStats` 參數，以查看 [上次同步時間] 屬性。 然後，查看帳戶的 `GeoReplicationStats` 屬性。 

容錯移轉之後，您的儲存體帳戶類型在新的主要區域中會自動轉換為本地備援儲存體 (LRS)。 您可以為帳戶重新啟用異地備援儲存體 (GRS) 或讀取權限異地備援儲存體 (RA-GRS)。 請注意，從 LRS 轉換為 GRS 或 RA-GRS 時，會產生額外的費用。 如需詳細資訊，請參閱[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。 

為您的儲存體帳戶重新啟用 GRS 之後，Microsoft 會開始將您帳戶中的資料複寫到新的次要區域。 複寫時間取決於複寫的資料量。  

## <a name="azure-portal"></a>Azure 入口網站

若要從 Azure 入口網站起始帳戶容錯移轉，請遵循下列步驟：

1. 瀏覽至儲存體帳戶。
2. 在 [設定] 下方，選取 [異地複寫]。 下圖顯示儲存體帳戶的異地複寫和容錯移轉狀態。

    ![顯示異地複寫和容錯移轉狀態的螢幕擷取畫面](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. 確認您的儲存體帳戶已進行異地備援儲存體 (GRS) 或讀取權限異地備援儲存體 (RA-GRS) 的設定。 若未設定，請選取 [設定] 下方的 [組態]，將您的帳戶更新為異地備援。 
4. [上次同步時間] 屬性會指出次要複本與主要複本相差了多久的時間。 [上次同步時間] 可讓您預估在容錯移轉完成後發生資料遺失的程度。
5. 選取 [準備容錯移轉 (預覽)]。 
6. 檢閱確認對話方塊。 在您準備就緒後，輸入**是**加以確認，並起始容錯移轉。

    ![顯示帳戶容錯移轉的確認對話方塊的螢幕擷取畫面](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>PowerShell

若要使用 PowerShell 起始帳戶容錯移轉，您必須先安裝 6.0.1 預覽模組。 請依照下列步驟安裝此模組：

1. 解除安裝任何先前安裝的 Azure PowerShell：

    - 使用 [設定] 底下的 [應用程式與功能]，從 Windows 移除任何先前安裝的 Azure PowerShell。
    - 從 `%Program Files%\WindowsPowerShell\Modules` 移除所有 **Azure*** 模組。
    
1. 確定您已安裝最新版的 PowerShellGet。 開啟 Windows PowerShell 視窗，然後執行下列命令來安裝最新版本：
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. 在安裝完 PowerShellGet 之後，關閉並重新開啟 PowerShell 視窗。 

1. 安裝最新版的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 安裝支援 Azure AD 的 Azure 儲存體預覽模組：
   
    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```
1. 關閉並重新開啟 PowerShell 視窗。
 

若要從 PowerShell 起始帳戶容錯移轉，請執行下列命令：

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 來起始帳戶容錯移轉，請執行下列命令：

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

## <a name="next-steps"></a>後續步驟

- [Azure 儲存體中的災害復原和帳戶容錯移轉 (預覽)](storage-disaster-recovery-guidance.md)
- [使用 RA-GRS 設計高可用性應用程式](storage-designing-ha-apps-with-ragrs.md)
- [教學課程：建置採用 Blob 儲存體的高可用性應用程式](../blobs/storage-create-geo-redundant-storage.md) 
