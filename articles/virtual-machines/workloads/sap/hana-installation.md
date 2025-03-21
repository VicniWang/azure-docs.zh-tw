---
title: 在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA | Microsoft Docs
description: 如何在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc63eb792e58d960ae67138b5e58e6b705945030
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446387"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>如何在 Azure 上安裝和設定 SAP HANA (大型執行個體)

閱讀本文章之前，請熟悉 [HANA 大型執行個體的常見術語](hana-know-terms.md)和 [HANA 大型執行個體 SKU](hana-available-skus.md)。

安裝 SAP HANA 是由您負責。 您可以在 Azure 虛擬網路與「HANA 大型執行個體」單位之間建立連線之後，開始安裝新的「Azure 上的 SAP HANA (大型執行個體)」伺服器。 

> [!Note]
> 依據 SAP 原則，SAP HANA 的安裝必須由已經通過「認證 SAP 技術協會」測驗 (SAP HANA 安裝認證測驗) 的人員，或由身為 SAP 認證系統整合者 (SI) 的人員執行。

當您打算安裝 HANA 2.0 時，請參閱 [SAP 支援附註 #2235581 - SAP HANA：支援的作業系統](https://launchpad.support.sap.com/#/notes/2235581/E)，以確保 OS 受您所要安裝的 SAP HANA 版本支援。 HANA 2.0 支援的 OS 所受到的限制比 HANA 1.0 支援的 OS 更多。 

> [!IMPORTANT] 
> 針對類型 II 單位，目前只支援 SLES 12 SP2 OS 版本。 

在開始安裝 HANA 之前，您必須驗證下列項目：
- [HLI 單位](#validate-the-hana-large-instance-units)
- [作業系統設定](#operating-system)
- [網路組態](#networking)
- [儲存體組態](#storage)


## <a name="validate-the-hana-large-instance-units"></a>驗證 HANA 大型執行個體單位

當您從 Microsoft 收到 HANA 大型執行個體單位之後，請驗證下列設定，並視需要進行調整。

在收到 HANA 大型執行個體並建立對執行個體的存取和連線之後，**第一步**是向 OS 提供者註冊執行個體的 OS。 這個步驟包括「在部署於 Azure VM 的 SUSE SMT 執行個體中註冊 SUSE Linux OS」。 

HANA 大型執行個體單位可以連接到這個 SMT 執行個體。 (如需詳細資訊，請參閱[如何設定 SUSE Linux 的 SMT 伺服器](hana-setup-smt.md))。 或者，需要向您必須連線的 Red Hat Subscription Manager 註冊 Red Hat OS。 如需詳細資訊，請參閱[什麼是 Azure 上的 SAP HANA (大型執行個體)？](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的備註。 

客戶也必須負責執行這個步驟，以便修補 OS。 若為 SUSE，請尋找文件上有關 [SMT 安裝](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)的這個頁面，以了解如何安裝和設定 SMT。

**第二步**是檢查是否有特定 OS 版次/版本的新修補程式和修正。 請確認「HANA 大型執行個體」的修補程式等級是否為最新狀態。 在某些情況下，可能不會包含最新的修補程式。 在接管 HANA 大型執行個體單位之後，必須檢查是否需要套用修補程式。

**第三步**是查看相關的「SAP 附註」，以在特定的 OS 版次/版本上安裝及設定 SAP HANA。 因為依據個別的安裝情況，建議事項會有所變更或是對「SAP 附註」或組態所做的變更會有所不同，所以 Microsoft 無法總是完美地設定「HANA 大型執行個體」單位。 

因此，身為客戶的您必須閱讀與 SAP HANA 有關的 SAP 附註，以確定實際的 Linux 版本。 亦請檢查作業系統版次/版本的組態，並套用尚未完成的組態設定 (若有的話)。

具體而言，請檢查下列參數並最後調整成：

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

從 SLES12 SP1 和 RHEL 7.2 開始，必須在 /etc/sysctl.d 目錄的組態檔中設定這些參數。 例如，必須建立名為 91-NetApp-HANA.conf 的組態檔。 如果是舊版 SLES 和 RHEL，則必須 /etc/sysctl.conf 中設定這些參數。

針對所有 RHEL 6.3 開始的 RHEL 版本，請記住下列事項： 
- sunrpc.tcp_slot_table_entries = 128 參數必須在 /etc/modprobe.d/sunrpc-local.conf 中設定。 如果該檔案不存在，則必須先透過新增下列項目來建立它： 
    - options sunrpc tcp_max_slot_table_entries=128

**第四步**是檢查您「HANA 大型執行個體單位」的系統時間。 系統會使用系統時區來部署執行個體。 此時區代表 HANA 大型執行個體戳記所在的 Azure 區域位置。 您可以變更您所擁有執行個體的系統時間或時區。 

如果您為租用戶訂購更多執行個體，則必須為新交付的執行個體調整其時區。 在移交執行個體之後，Microsoft 即無法洞察您為執行個體設定的系統時區。 因此，為新部署的執行個體設定的時區可能不會與您變更後的時區相同。 身為客戶，您必須負責視需要調整所移交執行個體的時區。 

**第五步**是檢查 etc/hosts。 在移交刀鋒伺服器時，會針對不同的用途為它們指派不同的 IP 位址。 檢查 etc/hosts 檔案。 將單位新增到現有的租用戶時，請勿期望系統會使用先前交付之系統的 IP 位址來正確維護新部署之系統的 etc/hosts。 身為客戶，您必須負責確定新部署的執行個體可以互動並解析您租用戶中先前所部署單位的名稱。 

## <a name="operating-system"></a>作業系統

> [!IMPORTANT] 
> 針對類型 II 單位，目前只支援 SLES 12 SP2 OS 版本。 

根據 [SAP 支援附註 #1999997 - 常見問題集：SAP HANA 記憶體](https://launchpad.support.sap.com/#/notes/1999997/E)，已傳遞作業系統映像的交換空間會設為 2 GB。 身為客戶，如果您想要使用不同的設定，則必須自行設定。

[SUSE Linux Enterprise Server 12 SP1 for SAP 應用程式](https://www.suse.com/products/sles-for-sap/download/)是針對 Azure 上的 SAP HANA (大型執行個體) 安裝的 Linux 的分佈。 這個特定的分佈提供現成 SAP 特有功能，(包括預先設定的參數，有效地在 SLES 上執行 SAP)。

請參閱 SUSE 網站上的[資源程式庫/技術白皮書](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)和 SAP Community Network (SCN) 上的 [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)，以取得與在 SLES 上部署 SAP HANA 相關的有用資源 (包括高可用性的設定、SAP 作業特定的安全性強化等等)。

以下是額外和有用的 SAP on SUSE 相關連結︰

- [SUSE Linux 網站上的 SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP 的最佳做法：加入佇列複寫 – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (包括 SLES 12 for SAP 應用程式)

以下是適用於在 SLES 12 上實作 SAP HANA 的 SAP 支援附註︰

- [SAP 支援附註 #1944799 – SLES 作業系統安裝的 SAP HANA 指南](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP 支援附註 #2205917 – 針對 SLES 12 for SAP 應用程式的 SAP HANA DB 建議作業系統設定](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP 支援附註 #1984787 – SUSE Linux Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP 支援附註 #171356 – 在 Linux 上的 SAP 軟體︰一般資訊](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP 支援附註 #1391070 – Linux UUID 解決方案](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) 是可供在 HANA Large Instances 上執行 SAP HANA 的另一個供應項目。 版本 RHEL 6.7 和 7.2 均可使用。 請注意，與僅支援 RHEL 7.2 和更新版本的原生 Azure VM 不同，HANA 大型執行個體也支援 RHEL 6.7。 不過，我們建議使用 RHEL 7.x 版本。

以下是其他有用的 SAP on Red Hat 相關連結︰
- [Red Hat Linux 網站上的 SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)。

以下是適用於在 Red Hat 上實作 SAP HANA 的 SAP 支援附註︰

- [SAP 支援附註 #2009879 - 適用於 Red Hat Enterprise Linux (RHEL) 作業系統的 SAP HANA 指南](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP 支援附註 #2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP 支援附註 #2247020 - SAP HANA DB：適用於 RHEL 6.7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2247020)
- [SAP 支援附註 #1391070 – Linux UUID 解決方案](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP 支援附註 #2228351 - Linux：RHEL 6 或 SLES 11 上的 SAP HANA Database SPS 11 修訂版 110 (或更新版本)](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP 支援附註 #2397039 - 常見問題集：RHEL 上的 SAP](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP 支援附註 #1496410 - Red Hat Enterprise Linux 6.x：安裝與升級](https://launchpad.support.sap.com/#/notes/1496410)
- [SAP 支援附註 #2002167 - Red Hat Enterprise Linux 7.x：安裝與升級](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>時間同步處理

建立在 SAP NetWeaver 架構上的 SAP 應用程式，對構成 SAP 系統的各種元件時間差異很敏感。 具有錯誤標題 ZDATE\_LARGE\_TIME\_DIFF 的 SAP ABAP 簡短傾印可能很眼熟。 這是因為這些簡短傾印會在不同伺服器或 VM 的系統時間離開太遠時顯示。

針對 SAP HANA on Azure (大型執行個體)，在 Azure 中完成的時間同步處理不適用於大型執行個體戳記中的計算單位。 這項同步處理並不適用於在原生 Azure VM 中執行 SAP 應用程式，因為 Azure 可確保正確同步處理系統時間。 

因此，您必須設定不同時間伺服器，以供 Azure VM 上執行的 SAP 應用程式伺服器與 HANA 大型執行個體上執行的 SAP HANA 資料庫執行個體使用。 大型執行個體戳記中的儲存體基礎結構與 NTP 伺服器進行時間同步處理。


## <a name="networking"></a>網路功能
我們假設您已如下列文件所述，依照建議設計 Azure 虛擬網路，並將這些虛擬網路連接到 HANA 大型執行個體：

- [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 上的 SAP HANA (大型執行個體) 的基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

關於單一單位的網路功能，有一些值得一提的細節。 每個「HANA 大型執行個體」單位都隨附兩個或三個 IP 位址，這些位址會指派給兩個或三個 NIC 連接埠。 在 HANA 向外延展組態與「HANA 系統複寫」案例中會使用三個 IP 位址。 其中一個指派給單位之 NIC 的 IP 位址是來自 [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中所述的伺服器 IP 集區。

若要了解架構所適用的乙太網路詳細資料，請參閱 [HLI 支援案例](hana-supported-scenario.md)。

## <a name="storage"></a>儲存體

SAP HANA on Azure (大型執行個體) 的儲存體配置是由 Azure服務管理透過 SAP 建議的指引進行設定。 這些指引列載於 [SAP HANA 儲存體需求](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)白皮書中。 

有不同 HANA 大型執行個體 SKU 的不同磁碟區的約略大小記載於 [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

下表列出的存放磁碟區的命名慣例：

| 儲存體使用量 | 掛接名稱 | 磁碟區名稱 | 
| --- | --- | ---|
| HANA 資料 | /hana/data/SID/mnt0000<m> | 儲存體 IP：/hana_data_SID_mnt00001_tenant_vol |
| HANA 記錄檔 | /hana/log/SID/mnt0000<m> | 儲存體 IP：/hana_log_SID_mnt00001_tenant_vol |
| HANA 記錄備份 | /hana/log/backups | 儲存體 IP：/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA 共用 | /hana/shared/SID | 儲存體 IP：/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | 儲存體 IP：/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

SID 是 HANA 執行個體的系統識別碼。 

Tenant 是部署租用戶時的內部作業列舉。

HANA usr/sap 會共用相同的磁碟區。 掛接點的專門用語包含 HANA 執行個體的系統識別碼及掛接編號。 在相應增加部署中，只有一個掛接，例如 mnt00001。 另一方面，在向外延展部署中，您會看到許多掛接，因為有背景工作角色和主要節點。 

在向外延展環境中，資料、記錄和記錄備份磁碟區是共用的，且會附加至向外延展組態中的每個節點上。 若是多個 SAP 執行個體的組態，會建立一組不同的磁碟區，並附加到 HANA 大型執行個體單位。 如需您的案例所適用的儲存配置詳細資料，請參閱 [HLI 支援案例](hana-supported-scenario.md)。

當您查看 HANA 大型執行個體單位時，您會發現這些單位有著相當大的 HANA/data 磁碟區，而且還有一個 HANA/log/backup 磁碟區。 我們之所以將 HANA/data 的大小設定成如此大，是因為我們為身為客戶的您提供的儲存體快照集就是使用同一個磁碟區。 您執行的儲存體快照集愈多，您在指派存放磁碟區中的快照集所使用的空間也愈多。 

HANA/log/backup 磁碟區不支援作為資料庫備份的磁碟區。 它會調整大小以用為 HANA 交易記錄備份的備份磁碟區。 如須詳細資訊，請參閱 [Azure 上的 SAP Hana (大型執行個體) 高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

除了所提供的儲存體之外，您還可以購買額外的儲存體容量 (增量單位為 1 TB)。 此額外儲存體可以做為新的磁碟區新增至 HANA 大型執行個體。

在 Azure 服務管理的 SAP HANA 上架期間，客戶會為 sidadm 使用者和 sapsys 群組指定使用者識別碼 (UID) 和群組識別碼 (GID) (例如：1000,500)。 安裝 SAP HANA 系統時，您必須使用這些相同的值。 因為您想要在一個單元上部署多個 HANA 執行個體，所以您會取得多個磁碟區集合 (每個執行個體一組)。 如此一來，在部署期間您需要定義下列項目：

- 不同的 (sidadm 衍生來源的) HANA 執行個體 SID。
- 不同 HANA 執行個體的記憶體大小。 每個執行個體的記憶體大小都會定義每個個別磁碟區集合中的磁碟區大小。

根據儲存提供者建議，已針對所有掛接的磁碟區設定下列掛接選項 (不包括開機 LUN)：

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

這些掛接點設定在類似 /etc/fstab 的位置，如下圖所示：

![HANA 大型執行個體單位中掛接磁碟區的 fstab](./media/hana-installation/image1_fstab.PNG)

S72m HANA 大型執行個體單元的 df -h 命令輸出應該像這樣：

![HANA 大型執行個體單位中掛接磁碟區的 fstab](./media/hana-installation/image2_df_output.PNG)


儲存體控制器和大型執行個體戳記中的節點會同步處理至 NTP 伺服器。 當您針對 NTP 伺服器同步處理 SAP HANA on Azure (大型執行個體) 單位和 Azure VM 時，Azure 或大型執行個體戳記中的基礎結構與計算單位之間應該不會耗費太多時間。

若要將 SAP HANA 到底層所使用的儲存體都最佳化，請設定下列 SAP HANA 組態參數：

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
針對從 SAP HANA 1.0 到 SPS12 的版本，您可以在安裝 SAP HANA 資料庫時設定這些參數，如 [SAP 附註 #2267798 - SAP HANA 資料庫組態 (英文)](https://launchpad.support.sap.com/#/notes/2267798) 所述。

您也可以透過使用 hdbparam 架構，在安裝 SAP HANA 資料庫之後設定這些參數。 

在 SAP HANA 2.0，hdbparam 架構已被取代。 因此，必須使用 SQL 命令來設定參數。 如需詳細資訊，請參閱 [SAP 附註 #2399079：在 HANA 2 中刪除 hdbparam](https://launchpad.support.sap.com/#/notes/2399079)。

若要深入了解適用於您的架構的儲存配置，請參閱 [HLI 支援案例](hana-supported-scenario.md)。


**後續步驟**

- 請參閱 [HLI 上的 HANA 安裝](hana-example-installation.md)










































 







 




