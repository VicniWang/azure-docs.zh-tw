---
title: Azure 備份常見問題集
description: '有關以下常見問題的解答：包括復原服務保存庫、可以備份的項目、其運作方式、加密和限制等 Azure 備份功能。 '
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: bfc1c419d5d58b4528b76dbed6fd0060f6b2833d
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446659"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure 備份 - 常見問題集
本文提供「Azure 備份」服務的常見問題解答。

## <a name="recovery-services-vault"></a>復原服務保存庫

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>每個 Azure 訂用帳戶中可以建立的保存庫數目是否有任何限制？
是。 您可以為每個訂用帳戶的 Azure 備份支援區域，最多建立 500 個復原服務保存庫。 如果您需要其他保存庫，請建立其他訂用帳戶。

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>針對每個保存庫註冊的伺服器/電腦具有數目限制嗎？
每個保存庫可以註冊最多 1000 個 Azure 虛擬機器。 如果您使用的是 Microsoft Azure 備份代理程式，則每個保存庫最多可以註冊 50 個 MAB 代理程式。 而且您可以將 50 部 MAB 伺服器/DPM 伺服器註冊至保存庫。

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>如果我的組織有一個保存庫，如何在還原資料時，將來自不同伺服器的資料隔離？
當您設定備份時，要一起復原的伺服器資料應該使用相同的複雜密碼。 如果您只想要復原特定的伺服器，請使用該伺服器專用的複雜密碼。 例如，人力資源伺服器可能使用一組加密複雜密碼，而會計伺服器使用另一組，並且儲存體伺服器使用第三組。

### <a name="can-i-move-my-vault-between-subscriptions"></a>我是否可以在訂用帳戶之間移動保存庫？
是。 若要移動復原服務保存庫，請參閱此[文章](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>我是否可以將備份資料移至另一個保存庫？
沒有。 儲存在保存庫中的備份資料無法移至不同的保存庫。

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>是否可以在備份之後從 GRS 變更為 LRS？
沒有。 復原服務保存庫只能在儲存任何備份之前，變更儲存體選項。

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>我是否可以針對備份到「復原服務保存庫」的 VM 執行「項目層級還原」(ILR)？
- 由 Azure VM 備份所備份的 Azure VM 支援 Azure VM。 如需詳細資訊，請參閱[文章](backup-azure-restore-files-from-vm.md)
- 由 Azure 備份伺服器或 System Center DPM 所備份的內部部署 VM 線上復原點不支援 ILR。


## <a name="azure-backup-agent"></a>Azure 備份代理程式

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>哪裡可以找到有關適用於 Azure VM 備份之 Azure 備份代理程式的常見問題？

- 針對在 Azure VM 上執行的代理程式，請參閱這個[常見問題集](backup-azure-vm-backup-faq.md)。
- 針對用來備份 Azure 檔案資料夾的代理程式，請參閱這個[常見問題集](backup-azure-file-folder-backup-faq.md)。


## <a name="vmware-and-hyper-v-backup"></a>VMware 和 Hyper-V 備份

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>我可以將 VMware vCenter 伺服器備份到 Azure 嗎？
是。 您可以使用「Azure 備份伺服器」將 VMware vCenter Server 和 ESXi 主機備份至 Azure。

- [深入了解](backup-mabs-protection-matrix.md)支援的版本。
- [依照這些步驟](backup-azure-backup-server-vmware.md)來備份 VMware 伺服器。

### <a name="do-i-need-a-separate-license-to-recover-an-full-on-premises-vmwarehyper-v-cluster"></a>我是否需要個別授權才能復原完整的內部部署 VMware/Hyper-V 叢集？
您不需要針對 VMware/HYPER-V 保護進行個別授權。

- 如果您是 System Center 客戶，請使用 System Center Data Protection Manager (DPM) 來保護 VMware VM。
- 如果您不是 System Center 客戶，您可使用 Azure 備份伺服器 (預付型方案) 來保護 VMware VM。

## <a name="dpm-and-azure-backup-server-backup"></a>DPM 和 Azure 備份伺服器備份

### <a name="which-dpm-versions-are-supported"></a>支援哪些 DPM 版本？
支援的 DPM 版本彙總於[支援對照表](backup-azure-dpm-introduction.md#prerequisites-and-limitations)中。 建議您安裝最新的 DPM 更新，並在 DPM 伺服器上執行[最新版本](https://aka.ms/azurebackup_agent)的「Azure 備份」代理程式。

### <a name="can-i-register-the-server-to-multiple-vaults"></a>是否可以向多個保存庫註冊伺服器？
沒有。 只能向一個保存庫註冊 DPM 或「Azure 備份」伺服器。

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>可以使用 Azure 備份伺服器來為實體伺服器建立裸機復原 (BMR) 備份嗎？ <br/>
是。

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>可以使用 DPM 備份 Azure Stack 中的應用程式嗎？
沒有。 雖然您可以使用 Azure 備份來保護 Azure Stack，但 Azure 備份不支援使用 DPM 來備份 Azure Stack 中的應用程式。

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>如果我已安裝「Azure 備份」代理程式來保護檔案和資料夾，是否可以安裝 System Center DPM 將內部部署工作負載備份至 Azure？
是。 但您應該先安裝 DPM，然後再安裝「Azure 備份」代理程式。  依此順序安裝元件可確保「Azure 備份」代理程式能與 DPM 搭配運作。 不建議或不支援在安裝 DPM 之前先安裝代理程式。

## <a name="general-backup"></a>一般備份

### <a name="are-there-limits-on-backup-scheduling"></a>在備份排程方面是否有任何限制？
是。
- 您一天最多可以備份 Windows Server 或 Windows 機器三次。 您可以將排程原則設定為每天或每週排程。
- 您一天最多可以備份 DPM 兩次。 您可以將排程原則設定為每天、每週、每月及每年。
- 您一天可以備份 Azure VM 一次。

### <a name="what-operating-systems-are-supported-for-backup"></a>支援使用哪些作業系統來進行備份？
「Azure 備份」支援使用下列作業系統來備份檔案和資料夾，以及受「Azure 備份伺服器」和 DPM 保護的應用程式。

**作業系統**| **SKU** |**詳細資料**
--- | --- | ---
Workstation | |
Windows 10 64 位元 | 企業版、專業版、家用版 | 機器應該執行最新的服務套件和更新。
Windows 8.1 64 位元 | Enterprise、Pro | 機器應該執行最新的服務套件和更新。
Windows 8 64 位元 | Enterprise、Pro | 機器應該執行最新的服務套件和更新。
Windows 7 64 位元 | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter | 機器應該執行最新的服務套件和更新。
伺服器 | |
Windows Server 2016 64 位元 | Standard、Datacenter、Essentials | 含最新的服務套件/更新。
Windows Server 2012 R2 64 位元 | Standard、Datacenter、Foundation | 含最新的服務套件/更新。
Windows Server 2012 64 位元 | Datacenter、Foundation、Standard | 含最新的服務套件/更新。
Windows Storage Server 2016 64 位元 | Standard、Workgroup | 含最新的服務套件/更新。
Windows Storage Server 2012 R2 64 位元 | Standard、Workgroup、Essential | 含最新的服務套件/更新。
Windows Storage Server 2012 64 位元 | Standard、Workgroup | 含最新的服務套件/更新。
Windows Server 2008 R2 SP1 64 位元 | Standard、Enterprise、Datacenter、Foundation | 含最新的更新。
Windows Server 2008 64 位元 | Standard、Enterprise、Datacenter | 含最新的更新。

針對 Azure VM Linux 備份，Azure 備份支援 [Azure 所背書的散發套件清單](../virtual-machines/linux/endorsed-distros.md)，但 Core OS Linux 和 32 位元作業系統除外。 只要 VM 上有 VM 代理程式可用並可支援 Python，其他自備的 Linux 散發套件便可能可以運作。


### <a name="are-there-size-limits-for-data-backup"></a>資料備份是否有大小限制？
大小限制如下：

OS/機器 | 資料來源的大小限制
--- | --- | ---
Windows 8 或更新版本 | 54,400 GB
Windows 7 |1700 GB
Windows Server 2012 或更新版本 | 54,400 GB
Windows Server 2008、Windows Server 2008 R2 | 1700 GB
Azure VM | 16 個資料磁碟<br/><br/> 資料磁碟最大可達 4095 GB

### <a name="how-is-the-data-source-size-determined"></a>如何判斷資料來源大小？
下表說明如何決定每個資料來源大小。

**資料來源** | **詳細資料**
--- | ---
磁碟區 |從所要備份之單一磁碟區 VM 備份的資料量。
SQL Server 資料庫 |所要備份之單一 SQL 資料庫大小的大小。
SharePoint | 所要備份之 SharePoint 伺服陣列中內容和設定資料庫的總和。
Exchange |所要備份之 Exchange Server 中所有 Exchange 資料庫的總和。
BMR/系統狀態 |所要備份之機器的 BMR 或系統狀態的每個個別複本。

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>使用「復原服務保存庫」來備份的資料量是否有限制？
您可使用「復原服務保存庫」來備份的資料量並無任何限制。

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>為何傳輸到「復原服務保存庫」的資料大小會小於所選取要備份的資料？
從「Azure 備份代理程式」、DPM 或「Azure 備份伺服器」備份的資料會先經過壓縮和加密，再進行傳輸。 在套用壓縮和加密之後，保存庫中的資料會縮小 30-40%。

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>可以從保存庫中的復原點刪除個別的檔案嗎？
否，Azure 備份不支援從儲存的備份中刪除或清除個別項目。

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>如果我在備份作業開始後取消作業，是否會刪除已傳輸的備份資料？
沒有。 所有在備份作業取消前傳輸到保存庫的資料都會保留在保存庫中。

- Azure 備份會使用檢查點機制，在備份期間偶爾將檢查點加入至備份資料。
- 因為備份資料中有檢查點，所以下一個備份程序才可驗證檔案的完整性。
- 下一個備份工作將會增量到先前備份的資料。 增量備份只會傳輸新資料或變更的資料，相當於具有較佳的頻寬使用率。

如果您取消 Azure VM 的備份工作，則會忽略任何傳輸的資料。 下一個備份工作會傳輸自最後一個成功備份工作之後的增量資料。

## <a name="retention-and-recovery"></a>保留和復原

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>就 DPM 與沒有 DPM 的 Windows 機器而言，兩者的保留原則是否相同？
是，兩者都有每日、每週、每月及每年保留原則。

### <a name="can-i-customize-retention-policies"></a>我是否可以自訂保留原則？
是，您可以自訂原則。 例如，您可以設定每週和每天保留需求，但無法設定每年和每月保留需求。

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>我是否可以針對備份排程和保留原則使用不同的時間？
沒有。 保留原則僅能套用在復原點上。 例如，下圖顯示在上午 12:00 和下午 6:00 進行之備份的保留原則。

![排程備份和保留](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>如果備份保留了很長一段時間，是否需要較多時間才能復原較舊的資料點？ <br/>
否。 復原最舊或最新時間點所需的時間都相同。 每個復原點的功能就像一個完整的復原點。

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>若每個復原點就像一個完整的復原點，則其是否會影響可計費的備份儲存體總數？
典型的長期保留復原點產品會將備份資料儲存為完整的復原點。

    - 完整的復原點是「效率不佳的」  儲存體，但可以更輕鬆且更快速地進行還原。
    - 增量複本「符合儲存體效益」，但需要您還原一連串的資料，而這會影響復原時間

Azure 備份的儲存體架構透過最佳化儲存資料以進行快速還原，並降低儲存體成本支出，可讓您魚與熊掌兼得。 這可確保有效率地使用您的輸入和輸出頻寬。 資料儲存體數量及復原資料所需的時間都會維持在最低。 深入了解[增量備份](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/)。

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>可建立的復原點數目有限制嗎？
您可以為每個受保護的執行個體最多建立 9999 個復原點。 受保護的執行個體係指會備份至 Azure 的電腦、伺服器 (實體或虛擬) 或工作負載。

- 深入了解[備份和保留](./backup-introduction-to-azure-backup.md#backup-and-retention)。
- 了解[受保護的執行個體](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)。

### <a name="how-many-times-can-i-recovery-data-thats-backed-up-to-azure"></a>我可以將備份至 Azure 的資料復原幾次？
從 Azure 備份進行復原的次數沒有任何限制。

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>還原資料時，我需要支付來自 Azure 的輸出流量嗎？
沒有。 復原是免費的，不會向您收取輸出流量的費用。

### <a name="what-happens-when-i-change-my-backup-policy"></a>變更我的備份原則時會發生什麼狀況？
套用新原則後，就會遵循新原則的排程和保留期。

- 如果延長保留期，會標示現有的復原點，以依據新的原則加以保留。
- 如果縮短保留期，則會標示現有的復原點，以便在下次清除作業中剪除然後刪除。

## <a name="encryption"></a>加密

### <a name="is-the-data-sent-to-azure-encrypted"></a>傳送至 Azure 的資料會經過加密嗎？
是。 資料會在內部部署機器上以 AES256 加密。 資料會透過安全的 HTTPS 連結來傳送。 在雲端中傳輸的資料僅受到儲存體和復原服務之間的 HTTPS 連結保護。 iSCSI 通訊協定保護復原服務和使用者電腦之間傳輸的資料。 安全通道用於保護 iSCSI 通道。

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>位於 Azure 的備份資料也會經過加密嗎？
是。 在 Azure 中的資料會進行靜態加密。

- 針對內部部署備份，會使用您在備份至 Azure 時所提供的複雜密碼來提供靜態加密。
- 針對 Azure VM，會使用「儲存體服務加密」(SSE) 對資料進行靜態加密。

Microsoft 不會解密在任何時間點所備份的資料。

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>用來加密備份資料的加密金鑰最小長度是多少？
當您使用 Azure 備份代理程式時，加密金鑰應該至少包含 16 個字元。 在 Azure VM 中，Azure KeyVault 所使用的金鑰長度沒有限制。

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>如果我錯置加密金鑰，則會發生什麼情況？ 我是否可以復原資料？ Microsoft 是否可以復原資料？
用來加密備份資料的金鑰只存在於您的站台上。 Microsoft 不會維護 Azure 中的複本，且無法存取金鑰。 如果您忘記將金鑰放在哪裡，Microsoft 並無法復原該備份資料。

## <a name="next-steps"></a>後續步驟

閱讀其他常見問題集：

- Azure VM 的相關[常見問題](backup-azure-vm-backup-faq.md)。
- 「Azure 備份」代理程式的相關[常見問題](backup-azure-file-folder-backup-faq.md)
