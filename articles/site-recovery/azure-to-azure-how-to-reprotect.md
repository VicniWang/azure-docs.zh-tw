---
title: 使用 Azure Site Recovery 重新保護已容錯移轉回到主要 Azure 區域的 Azure VM | Microsoft Docs
description: 說明在從主要區域進行容錯移轉之後，如何使用 Azure Site Recovery，在次要區域中重新保護 Azure VM。
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0b5fe1b1df306bf0930715bc6cfb586e5445f85c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839954"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>重新保護已容錯移轉到主要區域的 Azure VM


當您使用 [Azure Site Recovery](site-recovery-overview.md) 來將 Azure VM 從一個區域[容錯移轉](site-recovery-failover.md)到另一個區域時，VM 會以未受保護狀態在次要區域中開機。 如果要將 VM 容錯回復到主要區域，您需要執行下列作業：

- 重新保護次要區域中的 VM，好讓它們開始複寫到主要區域。
- 當重新保護完成且 VM 正在複寫之後，您就能將它們從次要區域容錯移轉到主要區域。

> [!WARNING]
> 如果您已經將機器從主要區域[移轉](migrate-overview.md#what-do-we-mean-by-migration)到次要區域、已將 VM 移至另一個資源群組或已刪除 Azure VM，則無法重新保護該 VM 或對其進行容錯回復。


## <a name="prerequisites"></a>必要條件
1. 必須認可從主要到次要區域的 VM 容錯移轉。
2. 主要目標網站應該可供使用，而且您應該能夠存取或建立該區域中的資源。

## <a name="reprotect-a-vm"></a>重新保護 VM

1. 在 [保存庫] > [已複寫的項目] 中，以滑鼠右鍵按一下已容錯移轉的 VM，然後選取 [重新保護]。 重新保護的顯示方向應該是從次要到主要。

  ![重新保護](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. 檢閱資源群組、網路、儲存體和可用性設定組。 然後按一下 [確定] 。 如果已將任何資源標記為新資源，即會在重新保護過程中建立它們。
3. 重新保護作業會在目標網站植入最新資料。 完成該作業之後，即會進行差異複寫。 然後，您可以容錯移轉回到主要網站。 您可以使用自訂選項，來選取您想要在重新保護期間使用的儲存體帳戶或網路。

  ![自訂選項](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>自訂重新保護設定

您可以在重新保護期間自訂目標 VM 的下列屬性。

![自訂](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|屬性 |注意  |
|---------|---------|
|目標資源群組     | 修改要在其中建立 VM 的目標資源群組。 目標 VM 會在重新保護過程中加以刪除。 您可以選擇容錯移轉之後要在其中建立 VM 的新資源群組。        |
|目標虛擬網路     | 目標網路無法在重新保護作業期間進行變更。 若要變更網路，請重新進行網路對應。         |
|目標儲存體 (次要 VM 不會使用受控磁碟)     | 您可以變更 VM 在容錯移轉之後所使用的儲存體帳戶。         |
|複本受控磁碟 (次要 VM 使用受控磁碟)    | 站台復原會在主要區域中建立複本受控磁碟，以便製作次要 VM 受控磁碟的鏡像。         |
|快取儲存體     | 您可以指定要在複寫期間使用的快取儲存體帳戶。 預設會建立新的快取儲存體帳戶 (如果不存在)。         |
|可用性設定組     |如果次要區域中的 VM 是可用性設定組的一部分，您就可以針對主要區域中的目標 VM 選擇可用性設定組。 根據預設，Site Recovery 會在主要區域中嘗試尋找現有的可用性設定組並加以使用。 進行自訂時，您可以指定新的可用性設定組。         |


### <a name="what-happens-during-reprotection"></a>重新保護期間會發生什麼情況？

預設會發生下列情況：

1. 在容錯移轉之 VM 的執行區域中建立一個快取儲存體帳戶。
2. 如果目標儲存體帳戶 (主要區域中的原始儲存體帳戶) 不存在，即會建立新的帳戶。 指派的儲存體帳戶名稱是由次要 VM 所使用且尾碼為 "asr" 的儲存體帳戶名稱。
3. 如果 VM 使用受控磁碟，即會在主要區域中建立複本受控磁碟，以儲存從次要 VM 磁碟複寫的資料。
4. 如果目標可用性設定組不存在，請視需要建立一個以作為重新保護作業的一部分。 如果您已自訂重新保護設定，則會使用選取的 設定組。

當您觸發重新保護作業且目標 VM 存在時，會發生下列情況：

1. 如果目標端 VM 正在執行，請加以關閉。
2. 如果 VM 使用受控磁碟，就會建立具有 '-ASRReplica' 尾碼的原始磁碟複本。 原始磁碟則會被刪除。 '-ASRReplica' 複本會用於複寫。
3. 如果 VM 使用非受控磁碟，就會將目標 VM 的資料磁碟中斷連結並用於複寫。 將會建立 OS 磁碟的複本並連結至 VM。 原始 OS 磁碟則會中斷連結並用於複寫。
4. 只會針對來源磁碟與目標磁碟之間的變更進行同步處理。 系統會藉由比較這兩個磁碟來計算出差異，然後進行傳輸。 這將需要幾個小時的時間才能完成。
5. 在同步處理完成之後，差異複寫就會開始，並依據複寫原則建立復原點。

當您觸發重新保護作業，而目標 VM 和磁碟不存在時，會發生下列情況：
1. 如果 VM 使用受控磁碟，就會建立具有 '-ASRReplica' 尾碼的複本磁碟。 '-ASRReplica' 複本會用於複寫。
2. 如果 VM 使用非受控磁碟，就會在目標儲存體帳戶中建立複本磁碟。
3. 將會把全部磁碟從容錯移轉的區域複製到新的目標區域。
4. 在同步處理完成之後，差異複寫就會開始，並依據複寫原則建立復原點。

## <a name="next-steps"></a>後續步驟

當 VM 受到保護之後，您就能起始容錯移轉。 容錯移轉會在次要區域中關閉 VM，然後在主要區域中，以少許停機時間建立 VM 並加以開機。 建議您據以選擇一個時間，並執行測試容錯移轉，再將完整容錯移轉起始到主要網站。 [深入了解](site-recovery-failover.md)容錯移轉。
