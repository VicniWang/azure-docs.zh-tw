---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e2dc82ee49b240fe562f02b38c4991c644c010d3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333831"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure 進階儲存體：專為高效能而設計

這篇文章提供使用 Azure 進階儲存體來建置高效能應用程式的指導方針。 您可以使用這份文件所提供的指示，並根據您的應用程式所採用的技術，結合適合的效能最佳作法。 為了說明指導方針，在這整份文件中，我們以進階儲存體上執行的 SQL Server 為範例。

雖然我們在本文中說明儲存體層的效能案例，但您必須將應用程式層最佳化。 例如，如果您在 Azure 進階儲存體上裝載 SharePoint 伺服器陣列，您可以使用本文件的 SQL Server 範例將資料庫伺服器最佳化。 此外，也要將 SharePoint 伺服器陣列的 Web 伺服器和應用程式伺服器最佳化，才能發揮最高效能。

關於在 Azure 進階儲存體上將應用程式效能最佳化方面，本文有助於回答以下常見的問題。

* 如何衡量應用程式效能？  
* 為什麼看不到預期的高效能？  
* 哪些因素會影響進階儲存體上的應用程式效能？  
* 這些因素如何影響進階儲存體上的應用程式效能？  
* 如何最佳化 IOPS、頻寬和延遲？  

我們特別針對進階儲存體提供這些指導方針，因為進階儲存體上執行的工作負載非常重視效能。 我們在適當的地方都提供範例。 針對在具有標準儲存體磁碟的 IaaS VM 上執行的應用程式，您也可以運用這些指導方針。

> [!NOTE]
> 有時候，看似磁碟效能問題的情況，其實是網路瓶頸。 在這些情況下，您應該將您的[網路效能](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)最佳化。
> 如果您的 VM 支援加速網路，您應確實加以啟用。 如果未啟用，您可以對 [Windows](../articles/virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 和 [Linux](../articles/virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 上已部署的 VM 加以啟用。

開始之前，如果您不熟悉進階儲存體，請先閱讀[為 IaaS 虛擬機器選取 Azure 磁碟類型](../articles/virtual-machines/windows/disks-types.md) \(英文\) 和[標準儲存體帳戶的 Azure 儲存體延展性和效能目標](../articles/storage/common/storage-scalability-targets.md)。

## <a name="application-performance-indicators"></a>應用程式效能指標

我們使用效能指標來評估應用程式執行是否順暢，例如，應用程式多快處理使用者要求、應用程式在每個要求中處理多少資料、應用程式在一段特定時間內處理多少要求、使用者提交要求之後必須等候少久才獲得回應。 這些效能指標的技術性術語包括 IOPS、輸送量或頻寬及延遲。

在本節中，我們將討論進階儲存體的一般效能指標。 在下一節「收集應用程式需求」中，您將學習如何對您的應用程式測量這些效能指標。 稍後在「最佳化應用程式效能」中，您將了解影響這些效能指標的因素，以及最佳化建議。

## <a name="iops"></a>IOPS

IOPS，亦即 Input/output Operations Per Second (每秒鐘輸入/輸出作業數)，是指應用程式在一秒內傳送到儲存體磁碟的要求數。 輸入/輸出作業可能是讀取或寫入、循序或隨機。 線上零售網站之類的「線上交易處理」(OLTP) 應用程式需要立即處理許多並行使用者要求。 使用者要求是頻繁插入和更新的資料庫交易，應用程式必須快速處理。 因此，OLTP 應用程式需要極高的 IOPS。 這類應用程式處理數百萬個小型和隨機的 IO 要求。 如果您有這類應用程式，您必須以 IOPS 最佳化為重點，設計應用程式基礎結構。 在稍後的 *最佳化應用程式效能*一節中，我們將詳細討論產生較高 IOPS 所必須考慮的一切因素。

當您將進階儲存體磁碟連接至高延展性 VM 時，Azure 會根據磁碟規格，保證佈建一定數量的 IOPS。 例如，P50 磁碟會佈建 7500 IOPS。 每個高延展性 VM 大小也有它可承受的特定 IOPS 限制。 例如，標準 GS5 VM 以 80,000 IOPS 為限。

## <a name="throughput"></a>Throughput

輸送量或頻寬是指應用程式在指定的間隔內傳送到儲存體磁碟的資料量。 如果應用程式以較大 IO 單位大小執行輸入/輸出作業，則需要較高輸送量。 資料倉儲應用程式通常會發出一次存取大量資料的頻繁掃描作業，而且經常執行大量作業。 換句話說，這類應用程式需要較高的輸送量。 如果您有這類應用程式，您必須以輸送量最佳化為重點來設計基礎結構。 下一節，我們將詳細討論必須調整哪些因素，才能達到此目的。

當您將進階儲存體磁碟連接至高延展性 VM 時，Azure 會根據該磁碟規格來佈建輸送量。 例如，P50 磁碟會佈建每秒 250 MB 的磁碟輸送量。 每個高延展性 VM 大小也有它可承受的特定輸送量限制。 例如，標準 GS5 VM 的輸送量上限為每秒 2,000 MB。

輸送量和 IOPS 之間存在某種關聯，如下列公式所示。

![IOPS 和輸送量的關係](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

因此，務必判斷應用程式所需的最佳輸送量和 IOPS 值。 嘗試最佳化其中一個時，另一個也會受影響。 在稍後的 *最佳化應用程式效能*一節中，我們將更詳細討論最佳化 IOPS 和輸送量。

## <a name="latency"></a>Latency

延遲是指應用程式收到單一要求、將它傳送至儲存體磁碟，然後將回應傳送給用戶端所花費的時間。 除了 IOPS 和輸送量，這也是應用程式效能的一個重要量值。 進階儲存體磁碟的延遲是指擷取要求的資訊並傳回給應用程式所花費的時間。 進階儲存體提供一致的低延遲。 進階磁碟是設計來為大部分的 IO 作業提供個位數的毫秒延遲。 如果在進階儲存體磁碟上啟用 ReadOnly 主機快取，讀取延遲會非常低。 在稍後的 *最佳化應用程式效能*一節中，我們將更詳細討論磁碟快取。

當您將應用程式最佳化以產生較高的 IOPS 和輸送量時，也會影響應用程式的延遲。 調整應用程式效能之後，務必評估應用程式的延遲，以避免發生非預期的高延遲現象。

# <a name="performance-application-checklist-for-disks"></a>磁碟的效能應用程式檢查清單

設計 Azure 進階儲存體上執行的高效能應用程式時，第一步是了解應用程式的效能需求。 已收集效能需求之後，您就可以將應用程式最佳化，以達到最佳效能。

在上一節，我們已說明一般效能指標：IOPS、輸送量和延遲。 為了讓應用程式提供理想的使用者體驗，您必須識別哪些是重要的效能指標。 例如，就每秒處理數百萬個交易的 OLTP 應用程式而言，高 IOPS 最重要。 然而，就每秒處理大量資料的資料倉儲應用程式而言，高輸送量很重要。 就即時視訊串流處理網站之類的即時應用程式而言，極低的延遲非常重要。

接下來，測量應用程式在整個存留期的最高效能需求。 使用下列檢查清單範例作為起點。 記錄正常、尖峰和離峰工作負載期間的最高效能需求。 只要識別所有工作負載層級的需求，您就可以判斷應用程式的整體效能需求。 例如，電子商務網站的正常工作負載是它的一年中大部分日子處理的交易。 網站的尖峰工作負載是它在節慶季節或特殊銷售活動期間所處理的交易。 尖峰工作負載的期間通常很短，但應用程式可能需要調升為正常作業的兩倍以上。 請找出 50 百分位數、90 百分位數和 99 百分位數的需求。 這有助於篩選掉效能需求的任何極端值，讓您專注於最佳化正確的值。

## <a name="application-performance-requirements-checklist"></a>應用程式效能需求檢查清單

| **效能需求** | **50 百分位數** | **90 百分位數** | **99 百分位數** |
| --- | --- | --- | --- |
| 最大 每秒交易 | | | |
| % 讀取作業 | | | |
| % 寫入作業 | | | |
| % 隨機作業 | | | |
| % 循序作業 | | | |
| IO 要求大小 | | | |
| 平均輸送量 | | | |
| 最大 Throughput | | | |
| 最小 Latency | | | |
| 平均延遲 | | | |
| 最大 CPU | | | |
| 平均 CPU | | | |
| 最大 記憶體 | | | |
| 平均記憶體 | | | |
| 佇列深度 | | | |

> [!NOTE]
> 您應該預期應用程式未來的成長，據以調整這些數字。 最好事先為成長預做規劃，因為以後再變更基礎結構來改善效能會較困難。

如果您有現有的應用程式，而且想要移到進階儲存體，請先為現有的應用程式建立上述檢查清單。 然後，在進階儲存體上建置應用程式的原型，並根據本文件稍後的 *最佳化應用程式效能* 一節所述的指導方針來設計應用程式。 下一篇文章說明可用來收集效能度量單位的工具。

### <a name="counters-to-measure-application-performance-requirements"></a>測量應用程式效能需求的計數器

如果要測量應用程式的效能需求，最佳方式就是使用伺服器的作業系統所提供的效能監視工具。 您可以使用 Windows 的 PerfMon 和 Linux 的 iostat。 這些工具可擷取與上一節所述的每個量值相對應的計數器。 當應用程式執行正常、尖峰和離峰工作負載時，您必須在擷取這些計數器的值。

伺服器的處理器、記憶體及每個邏輯磁碟和實體磁碟，都有可用的 PerfMon 計數器。 當您使用進階儲存體磁碟和 VM 時，實體磁碟計數器適用於每個進階儲存體磁碟，邏輯磁碟計數器適用於進階儲存體上建立的每個磁碟區。 您必須為裝載應用程式工作負載的磁碟擷取這些值。 如果邏輯與實體磁碟之間有一對一對應，您可以參考實體磁碟計數器，否則請參考邏輯磁碟計數器。 在 Linux 上，iostat 命令會產生 CPU 和磁碟使用率報告。 磁碟使用率報告提供每個實體裝置或分割區的統計資料。 如果資料庫伺服器的資料和記錄位於不同磁碟上，請同時從這兩個磁碟中收集這項資料。 下表說明磁碟、處理器和記憶體的計數器：

| 計數器 | 說明 | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS 或每秒交易數** |每秒發出給儲存體磁碟的 I/O 要求數。 |Disk Reads/sec  <br> Disk Writes/sec |tps  <br> r/s  <br>  w/s |
| **磁碟讀取和寫入** |磁碟上執行的讀取和寫入作業 %。 |% Disk Read Time  <br>  % Disk Write Time |r/s  <br>  w/s |
| **輸送量** |每秒讀取或寫入磁碟的資料量。 |Disk Read Bytes/sec  <br>  Disk Write Bytes/sec |kB_read/s <br> kB_wrtn/s |
| **延遲** |完成磁碟 IO 要求的總時間。 |Average Disk sec/Read  <br>  Average disk sec/Write |await  <br>  svctm |
| **IO 大小** |發出給儲存體磁碟的 I/O 要求大小。 |Average Disk Bytes/Read  <br>  Average Disk Bytes/Write |avgrq-sz |
| **佇列深度** |等候讀取或寫入儲存體磁碟的未完成 I/O 要求數。 |目前磁碟佇列長度 |avgqu-sz |
| **最大記憶體** |順暢執行應用程式所需的記憶體數量 |% Committed Bytes in Use |Use vmstat |
| **最大CPU** |順暢執行應用程式所需的 CPU 數量 |% Processor time |%util |

深入了解 [iostat](https://linux.die.net/man/1/iostat) 和 [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx)。



## <a name="optimize-application-performance"></a>最佳應用程式效能

對進階儲存體上執行的應用程式，影響效能的主要因素包括 IO 要求的本質、VM 大小、磁碟大小、磁碟數目、磁碟快取、多執行緒處理和佇列深度。 您可以使用系統提供的參數來控制這些因素。 大部分應用程式可能沒有選項讓您直接改變 IO 大小和佇列深度。 例如，如果您使用 SQL Server，您無法選擇 IO 大小和佇列深度。 SQL Server 會選擇最佳 IO 大小和佇列深度值，以獲得最高效能。 務必了解這兩種因素對應用程式效能的影響，才能佈建適當的資源來滿足效能需求。

在本節中，請參閱您建立的應用程式需求檢查清單，以識別您需要將應用程式效能最佳化到何種程度。 據此，您將能夠判斷需要調整本節中的哪些因素。 若要證明每個因素對應用程式效能的影響，請在應用程式安裝上執行效能評定工具。 有關在 Windows 和 Linux VM 上執行一般效能評定工具的步驟，請參閱本文最後的 [效能評定](#Benchmarking) 一節。

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>最佳化 IOPS、輸送量和延遲的速覽

下表摘要說明效能因素和最佳化 IOPS、輸送量和延遲所需的步驟。 這份摘要之後的幾節更深入說明每一個因素。

如需 VM 大小以及每種 VM 可用的 IOPS、輸送量和延遲的詳細資訊，請參閱 [Linux VM 大小](../articles/virtual-machines/linux/sizes.md)或 [Windows VM 大小](../articles/virtual-machines/windows/sizes.md)。

| &nbsp; | **IOPS** | **輸送量** | **延遲** |
| --- | --- | --- | --- |
| **範例案例** |需要極高每秒交易速率的企業 OLTP 應用程式。 |處理大量資料的企業資料倉儲應用程式。 |需要立即回應使用者要求的近乎即時的應用程式，例如線上遊戲。 |
| 效能因素 | &nbsp; | &nbsp; | &nbsp; |
| **IO 大小** |較小 IO 大小會產生較高的 IOPS。 |較大 IO 大小會產生較高的輸送量。 | &nbsp;|
| **VM 大小** |使用 IOPS 大於應用程式需求的 VM 大小。 |使用輸送量限制大於應用程式需求的 VM 大小。 |使用調整限制大於應用程式需求的 VM 大小。 |
| **磁碟大小** |使用 IOPS 大於應用程式需求的磁碟大小。 |使用輸送量限制大於應用程式需求的磁碟大小。 |使用調整限制大於應用程式需求的磁碟大小。 |
| **VM 和磁碟調整限制** |選擇的 VM 大小的 IOPS 限制，應該大於它連接的進階儲存體磁碟所推動的 IOPS 總數。 |選擇的 VM 大小的輸送量限制，應該大於它連接的進階儲存體磁碟所推動的輸送量總數。 |選擇的 VM 大小的調整限制，必須大於連接的進階儲存體磁碟的調整限制總數。 |
| **磁碟快取** |在讀取作業繁重的進階儲存體磁碟上，啟用唯讀快取可產生較高的讀取 IOPS。 | &nbsp; |在讀取作業繁重的進階儲存體磁碟上，啟用唯讀快取可產生極低的讀取延遲。 |
| **磁碟串接** |使用多個磁碟並串接在一起，可結合產生較高的 IOPS 和輸送量限制。 請注意，每個 VM 的結合限制應該高於連接的高階磁碟的結合限制。 | &nbsp; | &nbsp; |
| **等量大小** |在 OLTP 應用程式中，隨機小型 IO 模式使用較小的等量大小。 例如，SQL Server OLTP 應用程式使用等量大小 64KB。 |在資料倉儲應用程式中，循序大型 IO 模式使用較大的等量大小。 例如，SQL Server 資料倉儲應用程式使用 256KB 等量大小。 | &nbsp; |
| **多執行緒處理** |使用多執行緒處理將較多要求推送至進階儲存體，將會產生較高的 IOPS 和輸送量。 例如，在 SQL Server 上設定較高的 MAXDOP 值，可配置更多 CPU 給 SQL Server。 | &nbsp; | &nbsp; |
| **佇列深度** |較大佇列深度會產生較高的 IOPS。 |較大佇列深度會產生較高的輸送量。 |較小佇列深度會產生較低的延遲。 |

## <a name="nature-of-io-requests"></a>IO 要求的本質

IO 要求是指應用程式執行的輸入/輸出作業單位。 識別 IO 要求的本質、隨機或循序、讀取或寫入、小型或大型，可協助您判斷應用程式的效能需求。 設計應用程式基礎結構時，務必了解 IO 要求的本質，才能做出正確的決策。

IO 大小是其中一個很重要的因素。 IO 大小是指應用程式所產生的輸入/輸出作業要求的大小。 IO 大小對效能有很重大的影響，特別是應用程式可達到的 IOPS 和頻寬。 下列公式顯示 IOPS、IO 大小和頻寬/輸送量之間的關聯性。  
    ![](media/premium-storage-performance/image1.png)

有些應用程式可讓您改變 IO 大小，而有些應用程式則不能改變。 例如，SQL Server 會自行決定最佳的 IO 大小，不提供任何參數讓使用者變更 IO 大小。 相反地，Oracle 提供 [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) 參數，可用來設定資料庫的 I/O 要求大小。

如果您使用的應用程式不允許變更 IO 大小，請根據本文中的指導方針，將應用程式最相關的效能 KPI 最佳化。 例如，

* OLTP 應用程式會產生數百萬個小型和隨機的 IO 要求。 若要處理這種 IO 要求，您必須以提高 IOPS 為重點，設計應用程式基礎結構。  
* 資料倉儲應用程式會產生大型和循序的 IO 要求。 若要處理這種 IO 要求，您必須以提高頻寬和輸送量為重點，設計應用程式基礎結構。

如果您使用的應用程式可讓您變更 IO 大小，除了參考其他效能指導方針，請利用此基本原則來決定 IO 大小。

* 較小 IO 大小會產生較高的 IOPS。 例如，OLTP 應用程式使用 8 KB。  
* 較大 IO 大小會產生較高的頻寬/輸送量。 例如，資料倉儲應用程式使用 1024 KB。

以下是如何計算應用程式的 IOPS 和輸送量/頻寬的範例。 假設應用程式使用 P30 磁碟。 P30 磁碟可以達到的最大 IOPS 和輸送量/頻寬，分別為 5000 IOPS 和每秒 200 MB。 現在，如果應用程式需要 P30 磁碟的最大 IOPS，但您使用較小的 IO 大小，例如 8 KB，則能夠達到的頻寬為每秒 40 MB。 不過，如果應用程式需要 P30 磁碟的最大輪送量/頻寬，而且您使用較大的 IO 大小，例如 1024 KB，則產生的 IOPS 較小，只有 200 IOPS。 因此，請將 IO 大小調整為同時滿足應用程式的 IOPS 和輸送量/頻寬需求。 下表摘要說明 P30 磁碟的不同 IO 大小及其對應的 IOPS 和輸送量。

| 應用程式需求 | I/O 大小 | IOPS | 輸送量/頻寬 |
| --- | --- | --- | --- |
| 最大 IOPS |8 KB |5,000 |每秒 40 MB |
| 最大輸送量 |1024 KB |200 |每秒 200 MB |
| 最大輸送量 + 高 IOPS |64 KB |3,200 |每秒 200 MB |
| 最大 IOPS + 高輸送量 |32 KB |5,000 |每秒 160 MB |

若要讓 IOPS 和頻寬高於單一進階儲存體磁碟的最大值，請使用多個串接在一起的進階磁碟。 例如，串接兩個 P30 磁碟可產生 10,000 IOPS 的結合 IOPS，或每秒 400 MB 的結合輸送量。 如下一節所述，您使用的 VM 大小必須支援結合的磁碟 IOPS 和輸送量。

> [!NOTE]
> 因為提高 IOPS 或輸送量時，也會提高另一個因素，在提高任何一個因素時，請確保不會達到磁碟或 VM 的輸送量或 IOPS 限制。

若要證明 IO 大小對應用程式效能的影響，您可以在 VM 和磁碟上執行效能評定工具。 請建立多個測試回合，而且每個回合使用不同的 IO 大小，以查看影響。 如需詳細資料，請參閱本文最後的 [效能評定](#Benchmarking) 一節。

## <a name="high-scale-vm-sizes"></a>高延展性 VM 大小

開始設計應用程式時，首要工作之一是選擇 VM 來裝載應用程式。 進階儲存體提供高延展性 VM 大小，可以執行需要更高計算能力和較高本機磁碟 I/O 效能的應用程式。 這些 VM 為本機磁碟提供更快的處理器、較高的記憶體與核心比率，以及固態硬碟 (SSD)。 舉例來說，DS、DSv2 和 GS 系列 VM 就是支援進階儲存體的高延展性 VM。

高延展性 VM 有各種不同大小，以及不同數目的 CPU 核心、記憶體、作業系統和暫存磁碟大小。 每個 VM 大小也規定您可連接至 VM 的資料磁碟數目上限。 因此，選擇的 VM 大小會影響應用程式可用的處理、記憶體和儲存體容量。 也會影響計算和儲存體成本。 例如，以下為 DS 系列、DSv2 系列和 GS 系列中最大 VM 大小的規格：

| VM 大小 | CPU 核心 | 記憶體 | VM 磁碟大小 | 最大 資料磁碟 | 快取大小 | IOPS | 頻寬快取 IO 限制 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |作業系統 = 1023 GB  <br>  本機 SSD = 224 GB |32 |576 GB |50,000 IOPS  <br>  每秒 512 MB |4,000 IOPS 和每秒 33 MB |
| Standard_GS5 |32 |448 GB |作業系統 = 1023 GB  <br>  本機 SSD = 896 GB |64 |4224 GB |80,000 IOPS  <br>  每秒 2,000 MB |5,000 IOPS 和每秒 50 MB |

若要檢視所有可用的 Azure VM 大小的完整清單，請參閱 [Windows VM 大小](../articles/virtual-machines/windows/sizes.md)或 [Linux VM 大小](../articles/virtual-machines/linux/sizes.md)。 選擇可以符合並調整為期望的應用程式效能需求的 VM 大小。 此外，選擇 VM 大小時，請將下列重要因素納入考量。

*調整限制*  
 每個 VM 和每個磁碟的最大 IOPS 限制都不同且各自獨立。 請確定應用程式在 VM 及它連接的高階磁碟的限制內推動 IOPS。 否則，應用程式效能會發生節流現象。

例如，假設應用程式需求是最高 4,000 IOPS。 為了達到此目的，您在 DS1 VM 上佈建 P30 磁碟。 P30 磁碟最高可以達到 5,000 IOPS。 但是，DS1 VM 受限於 3,200 IOPS。 因此，應用程式效能會受到 VM 限制而降到 3,200 IOPS，效能將會降低。 為了避免這種情況，請選擇可同時滿足應用程式需求的 VM 和磁碟大小。

*作業成本*  
 在許多情況下，使用進階儲存體的整體作業成本，可能會低於使用標準儲存體。

例如，假設應用程式需要 16,000 IOPS。 為了達到這樣的效能，您需要使用 Standard\_D14 Azure IaaS VM，它在使用 32 個標準儲存體 1TB 磁碟時，最高可達到 16,000 的 IOPS。 每個 1TB 標準儲存體磁碟最高可達到 500 IOPS。 此 VM 的每月預估成本為 $1,570。 32 個標準儲存體磁碟的每月成本為 $1,638。 預估每月總成本為 $3,208。

不過，如果將相同的應用程式裝載於進階儲存體，則只需要較小的 VM 大小和較少的進階儲存體磁碟，因此可降低整體成本。 Standard\_DS13 VM 可以使用四個 P30 磁碟來滿足 16,000 IOPS 需求。 DS13 VM 的最大 IOPS 為 25,600，每個 P30 磁碟的最高 IOPS 為 5,000。 整體來說，這項設定可以達到 5,000 x 4 = 20,000 IOPS。 此 VM 的每月預估成本為 $1,003。 四個 P30 進階儲存體磁碟的每月成本為 $544.34。 預估每月總成本為 $1,544。

下表以標準和進階儲存體來摘要說明此案例的成本明細。

| &nbsp; | **標準** | **高級** |
| --- | --- | --- |
| **每月的 VM 成本** |$1,570.58 (Standard\_D14) |$1,003.66 (Standard\_DS13) |
| **每月的磁碟成本** |$1,638.40 (32 x 1 TB 磁碟) |$544.34 (4 x P30 磁碟) |
| **每月的整體成本** |$3,208.98 |$1,544.34 |

*Linux 散發版本*  

Azure 進階儲存體可讓執行 Windows 和 Linux 的 VM 達到相同層級的效能。 我們支援許多種 Linux 散發版本，您可以在 [這裡](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)看到完整清單。 務必注意，針對不同類型的工作負載，不同的散發版本會更適合。 根據執行工作負載的散發版本而定，您會看到不同層級的效能。 請以您的應用程式來測試 Linux 散發版本，選擇最適合的散發版本。

搭配進階儲存體執行 Linux 時，請檢查所需驅動程式的最新更新，以確保達到較高效能。

## <a name="premium-storage-disk-sizes"></a>進階儲存體磁碟大小

Azure 進階儲存體提供八種 GA 磁碟大小，以及目前處於預覽狀態的三種磁碟大小。 對於 IOPS、頻寬和儲存體，每個磁碟大小各有不同的調整限制。 請根據應用程式需求和高延展性 VM 大小，選擇正確的進階儲存體磁碟大小。 下表顯示八種磁碟大小及其功能。 目前只有針對「受控磁碟」才支援 P4、P6、P15、P60、P70 和 P80 磁碟大小。

| 進階磁碟類型  | P4    | P6    | P10   | P15 | P20   | P30   | P40   | P50   | P60   | P70   | P80   |
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| 磁碟大小           | 32 GiB | 64 GiB | 128 GB| 256 GiB| 512 GB            | 1,024 GiB (1 TiB)    | 2,048 GiB (2 TiB)    | 4,095 GiB (4 TiB)    | 8,192 GiB (8 TiB)    | 16,384 GiB (16 TiB)    | 32,767 GiB (32 GiB)    |
| 每一磁碟的 IOPS       | 120   | 240   | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12,500              | 15,000              | 20,000              |
| 每一磁碟的輸送量 | 每秒 25 MiB  | 每秒 50 MiB  | 每秒 100 MiB |每秒 125 MiB | 每秒 150 MiB | 每秒 200 MiB | 每秒 250 MiB | 每秒 250 MiB | 每秒 480 MiB | 每秒 750 MiB | 每秒 750 MiB |

您選擇的磁碟數量取決於已選擇的磁碟大小。 您可以使用單一 P50 磁碟或多個 P10 磁碟來滿足應用程式需求。 做決定時，請將下列因素納入考量。

*調整限制 (IOPS 和輸送量)*  
 每個高階磁碟大小的 IOPS 和輸送量限制不同，且無關於 VM 調整限制。 請確定磁碟的 IOPS 和輸送量合計，不超過所選擇的 VM 大小的調整限制。

例如，如果應用程式需求是輸送量上限 250 MB/秒，而且您使用 DS4 VM 搭配單一 P30 磁碟。 DS4 VM 最高可以達到 256 MB/秒的輸送量。 不過，單一 P30 磁碟的輸送量限制為 200 MB/秒。因此，由於磁碟的限制，應用程式會受限於 200 MB/秒。 為了克服這項限制，請將一個以上的資料磁碟佈建到 VM，或將您的磁碟大小調整為 P40 或 P50。

> [!NOTE]
> 由快取所服務的讀取不計入磁碟 IOPS 和輸送量內，因此不受磁碟限制。 對於每個 VM，快取有其個別的 IOPS 和輸送量限制。
>
> 例如，剛開始時，讀取和寫入分別為 60 MB/秒和 40 MB/秒。 經過一段時間，快取已活絡，可服務越來越多的讀取。 於是，來自磁碟的寫入輸送量就更高。

*磁碟數量*  
 評估應用程式需求，判斷您需要的磁碟數目。 每個 VM 大小也會限制您可連接至 VM 的磁碟數目。 這通常是核心數目的兩倍。 請確定您選擇的 VM 大小可以支援所需的磁碟數目。

請記住，相較於標準儲存體磁碟，進階儲存體磁碟有較高效能的功能。 因此，如果您將應用程式從使用標準儲存體的 Azure IaaS VM 移轉至進階儲存體，可能只需要較少的高階磁碟，應用程式就能達到相同或更高的效能。

## <a name="disk-caching"></a>磁碟快取

採用 Azure 進階儲存體的高延展性 VM 有一項稱為 BlobCache 的多層快取技術。 BlobCache 結合虛擬機器 RAM 和本機 SSD 進行快取。 此快取適用於進階儲存體永續性磁碟和 VM 本機磁碟。 根據預設，針對作業系統磁碟，此快取設定會設為讀取/寫入，而針對裝載於進階儲存體的資料磁碟，則設為唯讀。 在進階儲存體上啟用磁碟快取時，高延展性 VM 可以達到極高的效能層級，超過基礎磁碟效能。

> [!WARNING]
> 最多 4 TiB 的磁碟大小才支援磁碟快取。
> 變更 Azure 磁碟的快取設定會將目標磁碟中斷連接再重新連接。 如果它是作業系統磁碟，則會重新啟動 VM。 在變更磁碟快取設定之前，請先將可能受此中斷情況影響的所有應用程式/服務停止。

若要深入了解 BlobCache 的運作方式，請參閱 [深入 Azure 進階儲存體](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) 部落格文章。

務必在正確的一組磁碟上啟用快取。 高階磁碟上是否應該啟用磁碟快取，取決於磁碟將處理的工作負載模式。 下表顯示作業系統磁碟和資料磁碟的預設快取設定。

| **磁碟類型** | **預設快取設定** |
| --- | --- |
| 作業系統磁碟 |讀寫 |
| 資料磁碟 |唯讀 |

以下是針對資料磁碟建議的磁碟快取設定，

| **磁碟快取設定** | **使用這項設定的建議時機** |
| --- | --- |
| None |針對唯寫和大量寫入的磁碟，將主機快取設定為「無」。 |
| 唯讀 |針對唯讀和讀寫的磁碟，將主機快取設定為「唯讀」。 |
| 讀寫 |只有當應用程式在必要時可適當地將快取的資料寫入永續性磁碟時，才將主機快取設定為「讀寫」。 |

*唯讀*  
 您可以在進階儲存體資料磁碟上設定「唯讀」快取，讓應用程式達到較低的讀取延遲，以及非常高的讀取 IOPS 和輸送量。 這是基於兩個理由，

1. 從快取執行的讀取 (在 VM 記憶體和本機 SSD 上)，速度遠快於從資料磁碟讀取 (在 Azure Blob 儲存體上)。  
1. 進階儲存體不會將快取所服務的讀取計入磁碟 IOPS 和輸送量之內。 因此，應用程式能夠達到較高的 IOPS 和輸送量總計。

*讀寫*  
 根據預設，作業系統磁碟會啟用「讀寫」快取。 我們最近也已在資料磁碟上增加支援「讀寫」快取。 如果您使用「讀寫」快取，您必須有適當的方法將快取中的資料寫入永續性磁碟。 例如，SQL Server 會自行負責將快取的資料寫入永續性儲存體磁碟。 對於不負責保存必要資料的應用程式，如果使用「讀寫」快取，一旦 VM 損毀，可能會導致資料遺失。

舉例來說，您可以執行下列動作，將這些指導方針運用在進階儲存體上執行的 SQL Server，

1. 在裝載資料檔的進階儲存體磁碟上設定「唯讀」快取。  
   a.  從快取中快速讀取可縮短 SQL Server 查詢時間，因為從快取擷取資料頁，速度遠快於直接從資料磁碟擷取。  
   b.  由快取來服務讀取，表示高階資料磁碟會有額外的輸送量可用。 SQL Server 可以使用這個額外的輸送量來擷取更多資料頁和執行其他作業，例如備份/還原、批次載入和索引重建。  
1. 在裝載記錄檔的進階儲存體磁碟上設定「無」快取。  
   a.  記錄檔以大量寫入的作業為主。 因此，無法從「唯讀」快取中受惠。

### <a name="optimize-performance-on-linux-vms"></a>最佳化 Linux VM 的效能

對於所有快取設為 [唯讀] 或 [無] 的進階 SSD 或 Ultra 磁碟，當您掛接檔案系統時，您必須停用 "barriers" (阻礙)。 此案例中您不需要阻礙，因為對這些快取設定而言，寫入進階儲存體磁碟是持久的。 寫入要求成功完成時，資料就已寫入永久性的存放區。 若要停用 "barrier" (阻礙)，請使用下列其中一種方法。 請選擇適用您檔案系統的方法︰
  
* 若是 **reiserFS**，請使用 `barrier=none` 掛接選項停用阻礙。 (若要啟用阻礙，請使用 `barrier=flush`。)
* 若是 **ext3/ext4**，請使用 `barrier=0` 掛接選項停用阻礙。 (若要啟用阻礙，請使用 `barrier=1`。)
* 若是 **XFS**，請使用 `nobarrier` 掛接選項停用阻礙。 (若要啟用阻礙，請使用 `barrier`。)
* 對於快取設定為 **ReadWrite** 的進階儲存體，請啟用寫入持續性的障礙。
* 對於要在 VM 重新開機後保存的磁碟機標籤，您必須以參考磁碟的通用唯一識別碼 (UUID) 更新 /etc/fstab。 如需詳細資訊，請參閱[將受控磁碟新增至 Linux VM](../articles/virtual-machines/linux/add-disk.md)。

下列 Linux 散發套件經過驗證，可使用進階 SSD。 如需透過進階 SSD 提升效能與穩定性，建議您將虛擬機器升級成這些版本之一或更新的版本。 

部分版本需要適用於 Azure 的最新 Linux Integration Services 4.0 版。 若要下載並安裝散發套件，請遵循下表所列的連結。 完成驗證時，我們會將映像新增至清單中。 驗證結果顯示每個映像的效能各有差異。 效能取決於工作負載特性和映像上的設定。 不同的映像已針對不同種類的工作負載進行調整。

| 配送映像 | 版本 | 支援的核心 | 詳細資料 |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x、8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5、6.6、6.7、7.0 | &nbsp; | [LIS4 (必要)](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *請參閱下一節中的附註* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 (建議使用) ](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *請參閱下一節中的附註* |
| Red Hat Enterprise Linux (RHEL) | 6.8+、7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 或 RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |

## <a name="lis-drivers-for-openlogic-centos"></a>Openlogic CentOS 的 LIS 驅動程式

如果您執行 OpenLogic CentOS VM，請執行下列命令以安裝最新的驅動程式：

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

若要啟用新的驅動程式，請重新啟動 VM。

## <a name="disk-striping"></a>磁碟等量分割

當高延展性 VM 連接數個進階儲存體永續性磁碟時，磁碟可以串接在一起，以彙總其 IOPS、頻寬和儲存體容量。

在 Windows 中，您可以使用「儲存空間」將磁碟串接在一起。 您必須為集區中的每個磁碟設定一欄。 否則，由於磁碟之間的流量分配不平均，等量磁碟區的整體效能可能會低於預期。

重要：您可以使用伺服器管理員 UI，將一個等量磁碟區的總欄數最多設定為 8 個。 當連接 8 個以上的磁碟時，請使用 PowerShell 來建立磁碟區。 您可以使用 PowerShell 將欄數設定為等於磁碟數量。 例如，如果單一等量磁碟區中有 16 個磁碟，請在 *New-VirtualDisk* PowerShell Cmdlet 的 *NumberOfColumns* 參數中指定 16 欄。

在 Linux 上，請使用 MDADM 公用程式將磁碟串接在一起。 如需有關在 Linux 上等量分割磁碟的詳細步驟，請參閱 [在 Linux 上設定軟體 RAID](../articles/virtual-machines/linux/configure-raid.md)。

*等量大小*  
 磁碟串接時一項重要設定是等量大小。 等量大小或區塊大小是指應用程式在等量磁碟區上可定址的最小資料區塊。 您設定的等量大小取決於應用程式的類型及其要求模式。 如果您選擇錯誤的等量大小，可能會導致 IO 對齊錯錯，進而導致應用程式的效能降低。

比方說，如果應用程式所產生的 IO 要求大於磁碟等量大小，儲存體系統會跨越等量單位界限，將此要求寫入多個磁碟上。 在需要存取該資料時，必須跨越一個以上的等量單位來搜尋，才能完成要求。 這種行為的累積效果可能會導致效能大幅降低。 相反地，如果 IO 要求大小比等量大小更小，而且是隨機性質，IO 要求可能聚集在相同的磁碟上而造成瓶頸，最終導致 IO 效能降低。

請根據應用程式執行的工作負載類型，選擇適當的等量大小。 對於隨機小型 IO 要求，請使用較小的等量大小。 對於大型循序 IO 要求，請使用較大的等量大小。 針對您要在進階儲存體上執行的應用程式，請參考等量大小建議。 對於 SQL Server 而言，請將 OLTP 工作負載的等量大小設定為 64KB，而資料倉儲工作負載則設定為 256KB。 若要深入了解，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳作法](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) 。

> [!NOTE]
> 在 DS 系列 VM 上，最多可以串接 32 個進階儲存體磁碟，而在 GS 系列 VM 上，最多可以串接 64 個進階儲存體磁碟。

## <a name="multi-threading"></a>多執行緒處理

Azure 已將進階儲存體平台設計為大規模平行。 因此，多執行緒應用程式可達到的效能遠高於單一執行緒應用程式。 多執行緒應用程式會將工作分割成多個執行緒，並竭盡所能利用 VM 和磁碟資源，以提高執行效率。

例如，如果應用程式在單一核心 VM 上使用兩個執行緒執行，CPU 可以在兩個執行緒之間切換以提高效率。 當一個執行緒正在等候磁碟 IO 完成時，CPU 可以切換到另一個執行緒。 如此一來，兩個執行緒可以完成比單一執行緒更多的工作。 如果 VM 有一個以上的核心，則可進一步縮短執行時間，因為每個核心可以平行地執行工作。

您可能無法變更現成應用程式實作單一執行緒或多執行緒的方式。 例如，SQL Server 能夠處理多 CPU 與多核心。 不過，SQL Server 會決定在哪些情況下利用一或多個執行緒來處理查詢。 它可以使用多執行緒來執行查詢和建立索引。 如果查詢需要聯結大型資料表，而且將資料傳回給使用者之前需要排序資料，SQL Server 可能會使用多個執行緒。 但是，使用者無法控制 SQL Server 使用單一執行緒或多個執行緒來執行查詢。

您可以變更組態設定，以影響應用程式的這種多執行緒或平行處理方式。 例如，以 SQL Server 而言，這就是「平行處理原則的最大程度」組態。 這項設定稱為 MAXDOP，可讓您設定 SQL Server 在平行處理時可使用的處理器數目上限。 您可以為個別的查詢或索引作業設定 MAXDOP。 針對重視效能的應用程式，這有益於平衡系統的資源。

例如，假設使用 SQL Server 的應用程式正在同時執行大型查詢和索引作業。 假設您希望索引作業的效能高於大型查詢。 在這種情況下，您可以將索引作業的 MAXDOP 值設為高於查詢的 MAXDOP 值。 如此一來，可供 SQL Server 利用來執行索引作業的處理器數目，就比專用於大型查詢的處理器數目更多。 請記住，您不能控制 SQL Server 用於每個作業的執行緒數目。 您可以控制專用於多執行緒處理的處理器數目上限。

深入了解 SQL Server 中的 [平行處理原則的程度](https://technet.microsoft.com/library/ms188611.aspx) 。 在應用程式及其組態中找出會影響多執行緒處理的設定，以最佳化效能。

## <a name="queue-depth"></a>佇列深度

「佇列深度」或「佇列長度」或「佇列大小」是指系統中暫止的 IO 要求數目。 佇列深度的值決定應用程式可以排隊的 IO 作業數目，將由儲存體磁碟來處理。 它會影響我們在本文已討論過的所有三個應用程式效能指標，也就是 IOPS、輸送量和延遲。

佇列深度與多執行緒處理密切相關。 佇列深度值指出應用程式可以達到多高的多執行緒處理程度。 如果佇列深度較大，應用程式可以同時執行較多作業，也就是多執行緒處理程度較高。 如果佇列深度較小，即使是多執行緒應用程式，也不會有足夠的要求排隊來同時執行。

通常，現成應用程式不允許您變更佇列深度，因為如果設定不正確，將會弊多於利。 應用程式會設定正確的佇列深度值，以獲得最佳效能。 但是，務必了解此概念，才能對應用程式的效能問題進行疑難排解。 您也可以在系統上執行效能評定工具，以觀察佇列深度的效果。

某些應用程式提供設定來影響佇列深度。 例如，上一節所述 SQL Server 中的 MAXDOP (平行處理原則的最大程度) 設定。 MAXDOP 並不直接變更 SQL Server 的佇列深度值，但卻是影響佇列深度和多執行緒處理的一種方法。

*高佇列深度*  
 高佇列深度可讓較多作業在磁碟上排隊。 磁碟會事先知道其佇列中的下一個要求。 因此，磁碟可以事先排定這些作業，並以最佳順序來處理作業。 因為應用程式將更多要求傳送到磁碟，磁碟可以處理更多平行 IO。 最後，應用程式將能夠達到更高的 IOPS。 因為應用程式處理更多的要求，所以應用程式的總輸送量也會提高。

通常，當每個連接的磁碟上有 8-16+ 個未完成的 IO 時，應用程式可以達到最大輸送量。 如果佇列深度是一，則應用程式不會將足夠的 IO 推送至系統，因此在指定期間內處理的數量會較少。 換句話說，輸送量較少。

例如，在 SQL Server 中，將查詢的 MAXDOP 值設為 "4"，會向 SQL Server 表示它最多可以使用四個核心來執行查詢。 SQL Server 會決定查詢執行的最佳佇列深度值和核心數目。

*最佳佇列深度*  
 佇列深度值太高也有其缺點。 如果佇列深度值太高，應用程式會嘗試推動非常高的 IOPS。 除非應用程式的永續性磁碟已佈建足夠的 IOPS，否則這可能對應用程式延遲造成負面影響。 下列公式顯示 IOPS、延遲和佇列深度之間的關聯性。  
    ![](media/premium-storage-performance/image6.png)

不應該只想將佇列深度設定為較高的值，而是應該設定為最佳值，以提供足夠的 IOPS 給應用程式，但又不影響延遲。 例如，如果應用程式延遲必須是 1 毫秒，則達成 5,000 IOPS 所需的佇列深度為 QD = 5000 x 0.001 = 5。

*等量磁碟區的佇列深度*  
 對於等量磁碟區，應該維持夠高的佇列深度，讓每個磁碟有各自的尖峰佇列深度。 例如，假設應用程式推送的佇列深度為 2，而等量磁碟區中有 4 個磁碟。 兩個 IO 要求會流向兩個磁碟，而剩餘的兩個磁碟會處於閒置狀態。 因此，請將佇列深度設定為讓所有磁碟都有工作執行。 下列公式顯示如何決定等量磁碟區的佇列深度。  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>節流

Azure 進階儲存體會根據您選擇的 VM 大小和磁碟大小，佈建指定數目的 IOPS 和輸送量。 每當應用程式嘗試推動的 IOPS 或輸送量超過 VM 或磁碟可處理的這些限制時，進階儲存體便會進行節流。 這會造成應用程式的效能降低。 這可能表示延遲較高、輸送量較低或 IOPS 較低。 如果進階儲存體不會節流，應用程式可能會超出其資源的能力負荷而完全失敗。 因此，為了避免因為節流而造成效能問題，一定要為應用程式佈建足夠的資源。 請考量先前在 VM 大小和磁碟大小各節已討論過的因素。 為了駕馭應用程式，效能評定是找出所需資源的最佳方式。

## <a name="next-steps"></a>後續步驟

深入了解可用的磁碟類型：

* [選取磁碟類型](../articles/virtual-machines/windows/disks-types.md)  

若為 SQL Server 使用者，請參閱「SQL Server 的效能最佳作法」文章：

* [Azure 虛擬機器中的 SQL Server 效能最佳作法](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure 進階儲存體為 Azure VM 中的 SQL Server 提供最高效能](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
