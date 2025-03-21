---
title: 使用 Azure Site Recovery 進行從 VMware VM 和實體伺服器至 Azure 之災害復原的支援矩陣 | Microsoft Docs
description: 摘要說明可支援使用 Azure Site Recovery 進行從 VMware VM 和實體伺服器至 Azure 之災害復原的作業系統和元件。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: raynew
ms.openlocfilehash: 8115065afcbd81da1527e09c07ca89ce89100d7d
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236986"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>從 VMware VM 和實體伺服器至 Azure 之災害復原的支援矩陣

本文摘要說明使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 災害復原至 Azure 所支援的元件和設定。

若要開始使用 Azure Site Recovery 搭配簡單的部署案例，請瀏覽[教學課程](tutorial-prepare-azure.md)。 您可以在[這裡](vmware-azure-architecture.md)深入了解 Azure Site Recovery 架構。

## <a name="replication-scenario"></a>複寫案例

**案例** | **詳細資料**
--- | ---
VMware VM | 將內部部署 VMware 虛擬機器複寫至 Azure。 您可以在 Azure 入口網站中或使用 [PowerShell](vmware-azure-disaster-recovery-powershell.md) 來部署此案例。
實體伺服器 | 將內部部署 Windows/Linux 實體伺服器複寫至 Azure。 您可以在 Azure 入口網站中部署此案例。

## <a name="on-premises-virtualization-servers"></a>內部部署虛擬化伺服器

**伺服器** | **需求** | **詳細資料**
--- | --- | ---
VMware | vCenter Server 6.5、6.7、6.0 或 5.5 或 vSphere 6.7、6.5、6.0 或 5.5 | 我們建議使用 vCenter 伺服器。<br/><br/> 我們建議 vSphere 主機與 vCenter 伺服器應位於和處理序伺服器相同的網路中。 根據預設，處理序伺服器元件會在組態伺服器上執行，因此這是您在其中設定組態伺服器的網路，除非您設定專用處理序伺服器。
實體 | N/A

## <a name="site-recovery-configuration-server"></a>Site Recovery 組態伺服器

組態伺服器屬於內部部署機器，可執行 Site Recovery 元件，包括組態伺服器、處理序伺服器和主要目標伺服器。 針對以所有需求來設定組態伺服器的 VMware 複寫，請使用 OVF 範本來建立 VMware 虛擬機器。 針對實體伺服器複寫，請手動設定組態伺服器機器。

**元件** | **需求**
--- |---
CPU 核心 | 8
RAM | 16 GB
磁碟數量 | 3 個磁碟<br/><br/> 磁碟包括作業系統磁碟、處理序伺服器快取磁碟和用於容錯回復的保留磁碟機。
磁碟可用空間 | 處理序伺服器快取需要 600 GB 的空間。
磁碟可用空間 | 保留磁碟機需要 600 GB 的空間。
作業系統  | Windows Server 2012 R2 或 Windows Server 2016 |
作業系統地區設定 | 英文 (en-us)
PowerCLI | 應該安裝 [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")。
Windows Server 角色 | 未啟用： <br/> - Active Directory Domain Services <br/>- 網際網路資訊服務 <br/> - Hyper-V |
群組原則| 未啟用： <br/> - 防止存取命令提示字元。 <br/> - 防止存取登錄編輯工具。 <br/> - 檔案附件的信任邏輯。 <br/> - 開啟指令碼執行。 <br/> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 請確定您已執行下列動作：<br/><br/> - 沒有預先存在的預設網站 <br/> - 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定  <br/> - 沒有預先存在的網站/應用程式接聽連接埠 443<br/>
NIC 類型 | VMXNET3 (部署為 VMware VM 時)
IP 位址類型 | 靜態
連接埠 | 443 (用於控制通道協調流程)<br/>9443 (用於資料傳輸)

## <a name="replicated-machines"></a>複寫的電腦

Site Recovery 支援複寫任何執行於所支援機器上的工作負載。

**元件** | **詳細資料**
--- | ---
機器設定 | 複寫到 Azure 的電腦必須符合 [Azure 需求](#azure-vm-requirements)。
機器工作負載 | Site Recovery 支援複寫任何執行於所支援機器上的工作負載 (如 Active Directory、SQL server 等)。 若要深入了解，請按一下[這裡](https://aka.ms/asr_workload)
Windows 作業系統 | 64 位元 Windows Server 2019、64 位元 Windows Server 2016 (Server Core，使用桌面體驗的伺服器)、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)。 </br></br>  [至少含 SP2 的 Windows Server 2008 -32 位元和 64 位元](migrate-tutorial-windows-server-2008.md) (僅限移轉)。 </br></br> 不支援 Windows 2016 Nano Server。
Linux 作業系統 | Red Hat Enterprise Linux：5.2 至 5.11<b>\*\*</b>、6.1 至 6.10<b>\*\*</b>、7.0 至 7.6 <br/><br/>CentOS：5.2 至 5.11<b>\*\*</b>、6.1 至 6.10<b>\*\*</b>、7.0 至 7.6 <br/><br/>Ubuntu 14.04 LTS 伺服器 [(支援的核心版本)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS 伺服器 [(支援的核心版本)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (支援的核心版本)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1、SP2、SP3 [ (支援的核心版本)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>、SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/></br>- 不支援將所複寫的機器從 SUSE Linux Enterprise Server 11 SP3 升級至 SP4。 若要升級，請停用複寫，然後在升級之後再次加以啟用。</br></br> - [深入了解](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)Azure 中對 Linux 及開放原始碼技術的支援。 Site Recovery 會協調容錯移轉以在 Azure 中執行 Linux 伺服器。 不過，Linux 廠商可能會將支援僅限於生命週期尚未結束的發行版本。<br/><br/> - 在 Linux 發行版本上，僅支援屬於發行版本之次要版本發行/更新的原生核心。<br/><br/> - 不支援跨主要 Linux 發行版本升級受保護的機器。 若要升級，請停用複寫、升級作業系統，然後再次啟用複寫。<br/><br/> - 執行 Red Hat Enterprise Linux 5.2-5.11 或 CentOS 5.2-5.11 的伺服器應該安裝 [Linux Integration Services (LIS) 元件](https://www.microsoft.com/download/details.aspx?id=55106)，如此機器才能在 Azure 中啟動。

### <a name="ubuntu-kernel-versions"></a>Ubuntu 核心版本


**支援的版本** | **Azure Site Recovery 行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-generic 至 3.13.0-164-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.15.0-1023-azure 至 4.15.0-1036-azure |
14.04 LTS | [9.21][9.21 UR] | 3.13.0-24-generic 至 3.13.0-163-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.15.0-1023-azure 至 4.15.0-1035-azure |
14.04 LTS | [9.20][9.20 UR] | 3.13.0-24-generic 至 3.13.0-153-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-138-generic、<br/>4.15.0-1023-azure 至 4.15.0-1025-azure |
14.04 LTS | [9.19][9.19 UR] | 3.13.0-24-generic 至 3.13.0-153-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-131-generic |
|||
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-43-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1036-azure|
16.04 LTS | [9.21][9.21 UR] | 4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-42-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1035-azure|
16.04 LTS | [9.20][9.20 UR] | 4.4.0-21-generic 至 4.4.0-138-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-38-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1025-azure|
16.04 LTS | [9.19][9.19 UR] | 4.4.0-21-generic 至 4.4.0-131-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-30-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1019-azure|

### <a name="debian-kernel-versions"></a>Debian 核心版本


**支援的版本** | **Azure Site Recovery 行動服務版本** | **核心版本** |
--- | --- | --- |
Debian 7 | [9.19][9.19 UR]、[9.20][9.20 UR]、[9.21][9.21 UR]、[9.22][9.22 UR]| 3.2.0-4-amd64 至 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.20][9.20 UR],[9.21][9.21 UR],[9.22][9.22 UR] | 3.16.0-4-amd64 至 3.16.0-7-amd64、4.9.0-0.bpo.4-amd64 至 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.19][9.19 UR] | 3.16.0-4-amd64 至 3.16.0-6-amd64、4.9.0-0.bpo.4-amd64 至 4.9.0-0.bpo.7-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 支援的核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.22][9.22 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 至 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default 至 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.21][9.21 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 至 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default 至 4.4.156-94.72-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.20][9.20 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 至 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default 至 4.4.156-94.64-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.19][9.19 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.96-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 至 4.4.121-92.85-default</br></br>SP3 4.4.73-5-default 至 4.4.140-94.42-default |

## <a name="linux-file-systemsguest-storage"></a>Linux 檔案系統/客體儲存體

**元件** | **支援**
--- | ---
檔案系統 | ext3、ext4、XFS
磁碟區管理員 | [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)之前， <br/> 1.支援 LVM2。 <br/> 2.資料磁碟才支援 LVM。 <br/> 3.Azure VM 只有單一 OS 磁碟。<br/><br/>自 [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)起，即支援 LVM 和 LVM2。
並行虛擬存放裝置 | 不支援並行虛擬驅動程式所匯出的裝置。
多佇列區塊 IO 裝置 | 不支援。
使用 HP CCISS 儲存體控制器的實體伺服器 | 不支援。
裝置/掛接點的命名慣例 | 裝置名稱或掛接點名稱應該是唯一名稱。 請確定沒有任何兩個裝置/掛接點的名稱僅有大小寫之別。 </br> 範例：不允許將相同虛擬機器的兩個裝置命名為 *device1* 和 *Device1*。
目錄 | [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)之前， <br/> 1.下列目錄 (若設為獨立磁碟分割/檔案系統) 必須都位於來源伺服器的同一個 OS 磁碟：/(root)、/boot、/usr、/usr/local、/var、/etc。</br>2. /boot 應該位於磁碟分割區，而不是 LVM 磁碟區。<br/><br/> 自 [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)及更新版本起，即不適用上述限制。 不支援在跨多個磁碟的 LVM 磁碟區上執行 /boot。
開機目錄 | 不支援在虛擬機器上有多個開機磁碟 <br/><br/> 無法保護不含開機磁碟的機器

可用空間需求 | /root 分割區上為 2 GB <br/><br/> 安裝資料夾 XFSv5 上為 250 MB | 自行動服務 9.10 版開始，支援 XFS 檔案系統上的 XFSv5 功能，例如中繼資料總和檢查碼。 使用 xfs_info 公用程式來檢查磁碟分割的 XFS 超級區塊。 若 ftype 設為 1，則代表使用了 XFSv5 功能。

## <a name="vmdisk-management"></a>VM/磁碟管理

**Action** | **詳細資料**
--- | ---
在複寫的 VM 上調整磁碟大小 | 支援。
在複寫的 VM 上新增磁碟 | 停用 VM 的複寫、新增磁碟，然後重新啟用複寫。 目前不支援在複寫的 VM 上新增磁碟。

## <a name="network"></a>網路

**元件** | **支援**
--- | ---
主機網路 NIC 小組 | 支援 VMware VM。 <br/><br/>不支援實體機器複寫。
主機網路 VLAN | 是。
主機網路 IPv4 | 是。
主機網路 IPv6 | 沒有。
客體/伺服器網路 NIC 小組 | 沒有。
客體/伺服器網路 IPv4 | 是。
客體/伺服器網路 IPv6 | 沒有。
客體/伺服器網路靜態 IP (Windows) | 是。
客體/伺服器網路靜態 IP (Linux) | 是。 <br/><br/>VM 設定為在容錯回復時使用 DHCP。
客體/伺服器網路多重 NIC | 是。


## <a name="azure-vm-network-after-failover"></a>Azure VM 網路 (容錯移轉後)

**元件** | **支援**
--- | ---
Azure ExpressRoute | yes
ILB | yes
ELB | yes
Azure 流量管理員 | yes
多個 NIC | yes
保留的 IP 位址 | yes
IPv4 | yes
保留來源 IP 位址 | yes
Azure 虛擬網路服務端點<br/> (不含 Azure 儲存體防火牆) | yes
加速網路 | 否

## <a name="storage"></a>儲存體
**元件** | **支援**
--- | ---
動態磁碟 | 作業系統磁碟必須是基本磁碟。 <br/><br/>資料磁碟可以是動態磁碟
Docker 磁碟設定 | 否
主機 NFS | VMware 為是<br/><br/> 實體伺服器為否
主機 SAN (iSCSI/FC) | yes
主機 vSAN | VMware 為是<br/><br/> 實體伺服器為 N/A
主機多重路徑 (MPIO) | 是，通過 Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON 測試
主機虛擬磁碟區 (VVol) | VMware 為是<br/><br/> 實體伺服器為 N/A
客體/伺服器 VMDK | yes
客體/伺服器 EFI/UEFI| 部分 (適用於 Windows Server 2012 和更新版本移轉到 Azure) <br/><br/> 請參閱表格結尾處的附註
客體/伺服器共用叢集磁碟 | 否
客體/伺服器加密磁碟 | 否
客體/伺服器 NFS | 否
客體/伺服器 SMB 3.0 | 否
客體/伺服器 RDM | yes<br/><br/> 實體伺服器為 N/A
客體/伺服器磁碟 > 1 TB | yes<br/><br/>最多 4,095 GB<br/><br/> 磁碟必須大於 1024 MB。
客體/伺服器磁碟使用 4K 邏輯與 4k 實體磁區大小 | yes
客體/伺服器磁碟使用 4K 邏輯與 512 位元組實體磁區大小 | yes
客體/伺服器磁碟區使用等量磁碟 > 4 TB <br/><br/>邏輯磁碟區管理 (LVM)| yes
客體/伺服器 - 儲存體空間 | 否
客體/伺服器 熱新增/移除磁碟 | 否
客體/伺服器 - 排除磁碟 | yes
客體/伺服器多重路徑 (MPIO) | 否

> [!NOTE]
> 將執行 Windows Server 2012 或更新版本的 VMware 虛擬機器進行 UEFI 開機，可移轉到 Azure。 適用以下限制：

> - 僅支援移轉到 Azure。 不支援容錯回復至內部部署 VMware 網站。
> - 伺服器的 OS 磁碟上不應有超過 4 個磁碟分割。
> - 需要行動服務 9.13 版或更新版本。

## <a name="azure-storage"></a>Azure 儲存體

**元件** | **支援**
--- | ---
本地備援儲存體 | yes
異地備援儲存體 | yes
讀取權限異地備援儲存體 | yes
非經常性儲存體 | 否
經常性存取儲存體| 否
區塊 Blob | 否
待用資料加密 (儲存體服務加密)| yes
進階儲存體 | yes
匯入/匯出服務 | 否
設定在目標儲存體/快取儲存體帳戶 (用於儲存複寫資料) 上適用於虛擬網路的 Azure 儲存體防火牆 | 否
一般用途 v2 儲存體帳戶 (經常性存取層和非經常性存取層) | 否

## <a name="azure-compute"></a>Azure 計算

**功能** | **支援**
--- | ---
可用性設定組 | yes
中樞 | yes
受控磁碟 | yes

## <a name="azure-vm-requirements"></a>Azure VM 需求

您複寫到 Azure 的內部部署 VM 必須符合此表中摘要說明的 VM 需求。 Site Recovery 在執行先決條件檢查時，會於有一些需求不符合時失敗。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | 驗證複寫的機器[所支援的作業系統](#replicated-machines)。 | 若不支援，則檢查會失敗。
客體作業系統架構 | 64 位元。 | 若不支援，則檢查會失敗。
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則檢查會失敗。
資料磁碟計數 | 64 或以下。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 最多 4,095 GB | 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。 |
VM 名稱 | 從 1 到 63 個字元。<br/><br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。


## <a name="vault-tasks"></a>保存庫工作

**Action** | **支援**
--- | ---
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否


## <a name="download-latest-azure-site-recovery-components"></a>下載最新的 Azure Site Recovery 元件

**名稱** | **說明** | **最新版本下載指示**
--- | --- | --- | --- | ---
組態伺服器 | 協調內部部署 VMware 伺服器與 Azure 之間的通訊  <br/><br/> 安裝在內部部署 VMware 伺服器上 | 若為全新安裝，請按一下[這裡](vmware-azure-deploy-configuration-server.md)。 若要將現有元件升級為最新版本，請按一下[這裡](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。
處理序伺服器|預設會安裝在組態伺服器上。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。 隨著部署規模擴大，您可以新增額外的個別處理序伺服器，以處理日較大的複寫流量。| 若為全新安裝，請按一下[這裡](vmware-azure-set-up-process-server-scale.md)。 若要將現有元件升級為最新版本，請按一下[這裡](vmware-azure-manage-process-server.md#upgrade-a-process-server)。
行動服務 | 協調內部部署 VMware 伺服器/實體伺服器和 Azure/次要站台間複寫<br/><br/> 安裝於 VMware VM 上或您想要複寫的實體伺服器上 | 若為全新安裝，請按一下[這裡](vmware-azure-install-mobility-service.md)。 若要將現有元件升級為最新版本，請按一下[這裡](vmware-physical-mobility-service-overview.md#update-the-mobility-service)。

若要深入了解最新功能和修正程式，請按一下[這裡](https://aka.ms/ASR_latest_release_notes)。


## <a name="next-steps"></a>後續步驟
[了解如何](tutorial-prepare-azure.md)準備 Azure 的 VMware VM 災害復原。

[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
