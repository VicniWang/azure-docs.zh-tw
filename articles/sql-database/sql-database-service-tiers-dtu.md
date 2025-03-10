---
title: Azure SQL Database 服務層 - 以 DTU 為基礎的購買模式 | Microsoft Docs
description: 對於提供計算大小和儲存體大小的單一和集區資料庫，了解以 DTU 為基礎的購買模式的服務層。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: b960e0f670b66ea1759da441e7b1cf53151de7f6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993593"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>以 DTU 為基礎的購買模式的服務層

以 DTU 為基礎的購買模式的服務層是以一系列計算大小來做區分，這些等級各有一定數量的內含儲存體、一定的備份保留期和一定的價格。 以 DTU 為基礎的購買模式的所有服務層皆可彈性變更計算大小而不需停機。 單一資料庫和彈性集區會根據服務層和計算大小，以每小時為單位來計費。

> [!IMPORTANT]
> SQL Database 受控執行個體不支援以 DTU 為基礎的購買模式。 如需詳細資訊，請參閱 [Azure SQL Database 受控執行個體](sql-database-managed-instance.md)。
> [!NOTE]
> 如需以虛擬核心為基礎之服務層的相關資訊，請參閱[以虛擬核心為基礎的服務層](sql-database-service-tiers-vcore.md)。 如需如何區分以 DTU 為基礎及以虛擬核心為基礎之服務層的相關資訊，請參閱 [Azure SQL Database 購買模型](sql-database-purchase-models.md)。

## <a name="compare-the-dtu-based-service-tiers"></a>比較以 DTU 為基礎的服務層

服務層的選擇主要視業務持續性、儲存體和效能需求而定。

||基本|標準|進階|
| :-- | --: |--:| --:| --:|
|目標工作負載|開發與生產|開發與生產|開發與生產|
|執行時間 SLA|99.99%|99.99%|99.99%|
|備份保留期|7 天|35 天|35 天|
|CPU|低|低、中、高|中、高|
|IO 輸送量 (大約) |每一 DTU 2.5 IOPS| 每一 DTU 2.5 IOPS | 每一 DTU 48 IOPS|
|IO 延遲 (大約)|5 毫秒 (讀取)，10 毫秒 (寫入)|5 毫秒 (讀取)，10 毫秒 (寫入)|2 毫秒 (讀取/寫入)|
|資料行存放區索引 |N/A|S3 和更新版本|支援|
|記憶體內部 OLTP|N/A|N/A|支援|
|||||

> [!NOTE]
> 您可以在基本服務層取得免費的 Azure SQL Database，並搭配 Azure 免費帳戶來探索 Azure。 如需相關資訊，請參閱[使用您的免費 Azure 免費帳戶，建立受管理的雲端資料庫](https://azure.microsoft.com/free/services/sql-database/)。

## <a name="single-database-dtu-and-storage-limits"></a>單一資料庫 DTU 和儲存空間限制

單一資料庫的計算大小會以資料庫交易單位 (DTU) 表示，而彈性集區的計算大小則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[以 DTU 為基礎的購買模型](sql-database-purchase-models.md#dtu-based-purchasing-model)。

||基本|標準|進階|
| :-- | --: | --: | --: | --: |
| 儲存體大小上限 | 2 GB | 1 TB | 4 TB  |
| DTU 上限 | 5 | 3000 | 4000 | 
||||||

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>彈性集區 eDTU、儲存體及集區資料庫限制

| | **基本** | **標準** | **高級** |
| :-- | --: | --: | --: | --: |
| 每個資料庫的儲存體大小上限  | 2 GB | 1 TB | 1 TB |
| 每個集區的儲存體大小上限 | 156 GB | 4 TB | 4 TB |
| 每個資料庫的 eDTU 上限 | 5 | 3000 | 4000 |
| 每個集區的 eDTU 上限 | 1600 | 3000 | 4000 |
| 每個集區的資料庫數目上限 | 500  | 500 | 100 |
||||||

> [!IMPORTANT]
> 所有區域目前均可取得進階層中超過 1 TB 的儲存體，下列區域除外：美國中西部、中國東部、USDoDCentral、德國中部、USDoDEast、US Gov (西南部)、US Gov (愛荷華)、德國東北部、中國北部。 在其他區域，進階層中的儲存空間上限為 1 TB。 請參閱 [P11-P15 目前限制](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="dtu-benchmark"></a>DTU 基準測試

我們會使用模擬現實資料庫工作負載的基準，來校準與每個 DTU 量值相關聯的實體特性 (CPU、記憶體、IO)。

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>建立基準測試結果與實際案例資料庫效能之間的關聯

請務必了解，所有基準測試都只具備代表性與指標性。 利用基準測試應用程式達成的交易速率與利用其他應用程式可能達成的交易速率不同。 基準測試是由不同的交易類型集合所組成，這些類型是針對包含某個範圍的資料表和資料類型的結構描述來執行。 雖然基準測試會執行所有 OLTP 工作負載常見的相同基本作業，但是它不代表任何特定類別的資料庫或應用程式。 基準測試的目標是提供資料庫相對效能的合理指南，在計算大小之間向上或向下調整時可預期此目標。 實際上，資料庫的大小和複雜度都不一樣，會遭遇到不同的工作負載混合，並以不同的方式回應。 例如，IO 密集的應用程式可能會更快達到 IO 臨界值，或者 CPU 密集應用程式可能會更快達到 CPU 限制。 任何特定的資料庫並不保證能夠在增加負載的情況下使用和基準測試相同的方式調整。

基準測試和其方法會在以下詳細描述。

### <a name="benchmark-summary"></a>基準測試摘要

基準測試可測量基本資料庫作業混合的效能，這些作業最常發生在線上交易處理 (OLTP) 工作負載中。 雖然基準測試在設計時考量到雲端運算，但是資料庫結構描述、資料母體和交易的設計都廣泛代表 OLTP 工作負載中最常使用的基本元素。

### <a name="schema"></a>結構描述

結構描述的設計具有足夠的多樣性和複雜性才能支援廣泛的作業。 對包含六個資料表的資料庫執行基準測試。 資料表分成三個類別：固定大小、調整和成長。 有兩個固定大小資料表、三個調整資料表，和一個成長資料表。 固定大小資料表有固定數目的資料列。 調整資料表有與資料庫效能成正比但不會在基準測試期間變更的基數。 成長資料表的大小在初始載入時類似調整資料表，但是在執行基準測試做為資料列的過程中，會插入並刪除基數變更。

結構描述包含資料類型的混合，包括整數、數值、字元和日期/時間。 結構描述包含主要和次要索引鍵，但不含任何外部索引鍵 - 也就是資料表之間沒有參考完整性條件約束。

資料產生程式會產生初始資料庫的資料。 運用各種策略產生整數和數值資料。 在某些情況下，會在某範圍中隨機分佈值。 在其他情況下，會隨機排列一組值以確保維護特定的分佈。 從文字的加權清單產生文字欄位以產生實際的查看資料。

資料庫的大小是根據「縮放比例」來設定。 縮放比例 (簡寫為 SF) 可決定調整和成長資料表的基數。 如以下的＜使用者與步調＞一節所述，資料庫大小、使用者數目和最大效能都會根據彼此的比例進行調整。

### <a name="transactions"></a>交易

工作負載包含九種交易類型，如下表所示。 每一筆交易都設計為反白顯示資料庫引擎和系統硬體中特定的一組系統特性，與其他交易呈現高度對比。 此方法可讓您更容易評估不同元件對整體效能的影響。 例如，「頻繁讀取」交易會從磁碟產生大量的讀取作業。

| 交易類型 | 說明 |
| --- | --- |
| 輕度讀取 |SELECT；記憶體中；唯讀 |
| 中度讀取 |SELECT；大部分記憶體中；唯讀 |
| 重度讀取 |SELECT；大部分非記憶體中；唯讀 |
| 輕度更新 |UPDATE；記憶體中；讀寫 |
| 重度更新 |UPDATE；大部分非記憶體中；讀寫 |
| 輕度插入 |INSERT；記憶體中；讀寫 |
| 重度插入 |INSERT；大部分非記憶體中；讀寫 |
| 刪除 |DELETE；記憶體中與非記憶體中的混合；讀寫 |
| 重度 CPU |SELECT；記憶體中；非常重度的 CPU 負載；唯讀 |

### <a name="workload-mix"></a>工作負載混合

利用下列整體混合從加權分佈中隨機選取交易。 整體混合具有大約 2:1 的讀/寫比率。

| 交易類型 | 混合 % |
| --- | --- |
| 輕度讀取 |35 |
| 中度讀取 |20 |
| 重度讀取 |5 |
| 輕度更新 |20 |
| 重度更新 |3 |
| 輕度插入 |3 |
| 重度插入 |2 |
| 刪除 |2 |
| 重度 CPU |10 |

### <a name="users-and-pacing"></a>使用者與步調

基準測試工作負載是由一個工具觸發，它會跨一組連接提交交易，以模擬許多並行使用者的行為。 雖然所有的連接和交易都是由電腦產生，為了簡單起見，我們將這些連接視為「使用者」。 雖然每一位使用者都獨立於其他所有使用者操作，但是所有使用者都執行相同的步驟循環，如下所示：

1. 建立資料庫連接。
2. 發出結束訊號之前請重複：
   - 隨機選取交易 (從加權分佈中)。
   - 執行選取的交易及測量回應時間。
   - 等候步調延遲。
3. 關閉資料庫連接。
4. [結束]。

步調延遲 (在步驟 2c) 為隨機選取，但其分佈具有 1.0 秒的平均值。 因此每位使用者平均每秒可以產生最多一筆交易。

### <a name="scaling-rules"></a>調整規則

使用者數目取決於資料庫大小 (以縮放比例單位表示)。 每五個縮放比例單位有一名使用者。 由於步調延遲，一位使用者平均每秒可以產生最多一筆交易。

例如，縮放比例為 500 (SF = 500) 的資料庫會有 100 位使用者，並可達到最大速率 100 TPS。 若要觸發更高的 TPS 速率，需要更多使用者和更大的資料庫。

### <a name="measurement-duration"></a>測量持續時間

有效的基準測試執行需要至少一個小時的穩定狀態測量持續時間。

### <a name="metrics"></a>度量

基準測試中的關鍵度量是輸送量和回應時間。

- 輸送量是基準測試中的基礎效能測量。 每個單位時間都會在交易中報告輸送量，計算所有交易類型。
- 回應時間是效能可預測性的測量。 回應時間條件約束會隨服務類別而有所不同，較高等級的服務類別有更嚴格的回應時間需求，如下所示。

| 服務類別 | 輸送量測量 | 回應時間需求 |
| --- | --- | --- |
| 進階 |每秒交易 |0.5 秒時第 95 個百分位數 |
| 標準 |每分鐘交易 |1.0 秒時第 90 個百分位數 |
| 基本 |每小時交易 |2.0 秒時第 80 個百分位數 |

## <a name="next-steps"></a>後續步驟

- 如需特定計算大小的詳細資訊和單一資料庫可用的儲存體大小選項，請參閱[單一資料庫 SQL Database 以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 如需特定計算大小的詳細資訊和彈性集區可用的儲存體大小選項，請參閱 [SQL Database 以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)。
