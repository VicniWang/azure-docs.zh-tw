---
title: Azure SQL Database 以虛擬核心為基礎的資源限制 - 彈性集區 | Microsoft Docs
description: 此頁面將針對 Azure SQL Database 中的彈性集區，說明一些以虛擬核心為基礎的常見資源限制。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 02/15/2019
ms.openlocfilehash: b0bd5c0ba8641bd287951168485be715a0b46224
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328668"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>使用以 vCore 為基礎的購買模型的彈性集區資源限制

本文使用以虛擬核心為基礎的購買模型，來提供 Azure SQL Database 彈性集區和集區資料庫的詳細資源限制。

如需以 DTU 為基礎的購買模型限制，請參閱 [SQL Database 以 DTU 為基礎的資源限制 - 彈性集區](sql-database-dtu-resource-limits-elastic-pools.md)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

您可以使用 [Azure 入口網站](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases)、[Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) 或 [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases)，來設定服務層、計算大小與儲存體數量。

> [!NOTE]
> 彈性集區中個別資料庫的資源限制通常與集區外部具有相同計算大小的單一資料庫資源限制相同。 例如，GP_Gen4_1 資料庫的並行背景工作上限是 200 個背景工作。 因此，GP_Gen4_1 集區中資料庫的並行背景工作上限也是 200 個背景工作。 請注意，GP_Gen4_1 集區中的並行背景工作總數為 210。

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>一般目的服務層：儲存體大小和計算大小

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>一般目的服務層：第 4 代計算平台 (第 1 部分)

|計算大小|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|1|2|3|4|5|6|
|記憶體 (GB)|7|14|21|28|35|42|
|資料行存放區支援|yes|是|是|是|是|yes|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|512|756|756|1536|1536|1536|
|記錄大小上限|154|227|227|461|461|461|
|TempDB 大小 (GB)|32|64|96|128|160|192|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|目標 IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|每個集區的並行背景工作角色 (要求) 數上限 * |210|420|630|840|1050|1260|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|每個集區的最大 DB 數|100|200|300|500|500|500|
|每個資料庫最小/最大彈性集區虛擬核心選項|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|複本數目|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\* 關於任何個別資料庫的最大並行背景工作角色 (要求)，請參閱[單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果彈性集區是使用 Gen5 而且其最大 V 核心數是每個資料庫 2 個，則最大並行背景工作角色數是 200 個。  如果每個資料庫的最大 V 核心數是 0.5 個，則最大並行背景工作角色數是 50 個，因為 Gen5 上的最大並行背景工作角色數是每個 V 核心 100 個。  對於少於 1 個 V 核心的每個資料庫 V 核心最大數量的其他設定，並行背景工作角色的最大數目也是同樣地重新調整。

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>一般目的服務層：第 4 代計算平台 (第 2 部分)

|計算大小|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|7|8|9|10|16|24|
|記憶體 (GB)|49|56|63|70|112|168|
|資料行存放區支援|yes|是|是|是|是|yes|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|1536|2048|2048|2048|3584|4096|
|記錄大小上限 (GB)|461|614|614|614|1075|1229|
|TempDB 大小 (GB)|224|256|288|320|384|384|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|目標 IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|每個集區的並行背景工作角色 (要求) 數上限 *|1470|1680|1890|2100|3360|5040|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|每個集區的最大 DB 數|200|500|500|500|500|500|
|每個資料庫最小/最大彈性集區虛擬核心選項|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|複本數目|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\* 關於任何個別資料庫的最大並行背景工作角色 (要求)，請參閱[單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果彈性集區是使用 Gen5 而且其最大 V 核心數是每個資料庫 2 個，則最大並行背景工作角色數是 200 個。  如果每個資料庫的最大 V 核心數是 0.5 個，則最大並行背景工作角色數是 50 個，因為 Gen5 上的最大並行背景工作角色數是每個 V 核心 100 個。  對於少於 1 個 V 核心的每個資料庫 V 核心最大數量的其他設定，並行背景工作角色的最大數目也是同樣地重新調整。

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>一般目的服務層：第 5 代計算平台 (第 1 部分)

|計算大小|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|
|虛擬核心|2|4|6|8|10|12|14|
|記憶體 (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|資料行存放區支援|yes|是|是|是|是|是|yes|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|512|756|756|1536|1536|1536|
|記錄大小上限 (GB)|154|227|227|461|461|461|461|
|TempDB 大小 (GB)|64|128|192|256|320|384|384|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|目標 IOPS (64 KB)|500|1000|1500|2000|2500|3000|3500|
|每個集區的並行背景工作角色 (要求) 數上限 *|210|420|630|840|1050|1260|1470|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|30000|
|每個集區的最大 DB 數|200|500|500|500|500|500|500|
|每個資料庫最小/最大彈性集區虛擬核心選項|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|複本數目|1|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\* 關於任何個別資料庫的最大並行背景工作角色 (要求)，請參閱[單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果彈性集區是使用 Gen5 而且其最大 V 核心數是每個資料庫 2 個，則最大並行背景工作角色數是 200 個。  如果每個資料庫的最大 V 核心數是 0.5 個，則最大並行背景工作角色數是 50 個，因為 Gen5 上的最大並行背景工作角色數是每個 V 核心 100 個。  對於少於 1 個 V 核心的每個資料庫 V 核心最大數量的其他設定，並行背景工作角色的最大數目也是同樣地重新調整。

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>一般目的服務層：第 5 代計算平台 (第 2 部分)

|計算大小|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|
|虛擬核心|16|18|20|24|32|40|80|
|記憶體 (GB)|81.6|91.8|102|122.4|163.2|204|408|
|資料行存放區支援|yes|是|是|是|是|是|yes|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|2048|2048|3072|3072|4096|4096|4096|
|記錄大小上限 (GB)|614|614|922|922|1229|1229|1229|
|TempDB 大小 (GB)|384|384|384|384|384|384|384|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|目標 IOPS (64 KB)|4000|4500|5000|6000|7000|7000|7000|
|每個集區的並行背景工作角色 (要求) 數上限 *|1680|1890|2100|2520|33600|4200|8400|
|每個集區的最大 DB 數|500|500|500|500|500|500|500|
|每個資料庫最小/最大彈性集區虛擬核心選項|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|複本數目|1|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\* 關於任何個別資料庫的最大並行背景工作角色 (要求)，請參閱[單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果彈性集區是使用 Gen5 而且其最大 V 核心數是每個資料庫 2 個，則最大並行背景工作角色數是 200 個。  如果每個資料庫的最大 V 核心數是 0.5 個，則最大並行背景工作角色數是 50 個，因為 Gen5 上的最大並行背景工作角色數是每個 V 核心 100 個。  對於少於 1 個 V 核心的每個資料庫 V 核心最大數量的其他設定，並行背景工作角色的最大數目也是同樣地重新調整。

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>業務關鍵服務層：儲存體大小和計算大小

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>業務關鍵服務層：第 4 代計算平台 (第 1 部分)

|計算大小|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|1|2|3|4|5|6|
|記憶體 (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|資料行存放區支援|N/A|N/A|N/A|N/A|N/A|N/A|
|OLTP 記憶體內部儲存體 (GB)|1|2|3|4|5|6|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|
|記錄大小上限 (GB)|307|307|307|307|307|307|
|TempDB 大小 (GB)|32|64|96|128|160|192|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|目標 IOPS (64 KB)|5000|10000|15000|20000|25000|30000|
|每個集區的並行背景工作角色 (要求) 數上限 *|210|420|630|840|1050|1260|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|每個集區的最大 DB 數|只有單一 DB 才支援此計算大小|50|100|100|100|100|
|每個資料庫最小/最大彈性集區虛擬核心選項|N/A|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|複本數目|4|4|4|4|4|4|
|多重 AZ|yes|是|是|是|是|yes|
|讀取向外延展|yes|是|是|是|是|yes|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\* 關於任何個別資料庫的最大並行背景工作角色 (要求)，請參閱[單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果彈性集區是使用 Gen5 而且其最大 V 核心數是每個資料庫 2 個，則最大並行背景工作角色數是 200 個。  如果每個資料庫的最大 V 核心數是 0.5 個，則最大並行背景工作角色數是 50 個，因為 Gen5 上的最大並行背景工作角色數是每個 V 核心 100 個。  對於少於 1 個 V 核心的每個資料庫 V 核心最大數量的其他設定，並行背景工作角色的最大數目也是同樣地重新調整。

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>業務關鍵服務層：第 4 代計算平台 (第 2 部分)

|計算大小|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|7|8|9|10|16|24|
|記憶體 (GB)|81.6|91.8|102|122.4|163.2|204|408|
|資料行存放區支援|N/A|N/A|N/A|N/A|N/A|N/A|
|OLTP 記憶體內部儲存體 (GB)|7|8|9.5|11|20|36|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|
|記錄大小上限 (GB)|307|307|307|307|307|307|
|TempDB 大小 (GB)|224|256|288|320|384|384|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|目標 IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|每個集區的並行背景工作角色 (要求) 數上限 *|1470|1680|1890|2100|3360|5040|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|每個集區的最大 DB 數|100|100|100|100|100|100|
|每個資料庫最小/最大彈性集區虛擬核心選項|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|複本數目|4|4|4|4|4|4|
|多重 AZ|yes|是|是|是|是|yes|
|讀取向外延展|yes|是|是|是|是|yes|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\* 關於任何個別資料庫的最大並行背景工作角色 (要求)，請參閱[單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果彈性集區是使用 Gen5 而且其最大 V 核心數是每個資料庫 2 個，則最大並行背景工作角色數是 200 個。  如果每個資料庫的最大 V 核心數是 0.5 個，則最大並行背景工作角色數是 50 個，因為 Gen5 上的最大並行背景工作角色數是每個 V 核心 100 個。  對於少於 1 個 V 核心的每個資料庫 V 核心最大數量的其他設定，並行背景工作角色的最大數目也是同樣地重新調整。

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>業務關鍵服務層：第 5 代計算平台 (第 1 部分)

|計算大小|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|
|虛擬核心|2|4|6|8|10|12|14|
|記憶體 (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|資料行存放區支援|yes|是|是|是|是|是|yes|
|OLTP 記憶體內部儲存體 (GB)|1.571|3.142|4.713|6.284|8.655|11.026|13.397|
|資料大小上限 (GB)|1024|1024|1024|1536|1536|1536|1536|
|記錄大小上限 (GB)|307|307|307|461|461|461|461|
|TempDB 大小 (GB)|64|128|192|256|320|384|384|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|目標 IOPS (64 KB)|5000|10000|15000|20000|25000|30000|35000|
|每個集區的並行背景工作角色 (要求) 數上限 *|210|420|630|840|1050|1260|1470|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|30000|
|每個集區的最大 DB 數|只有單一 DB 才支援此計算大小|50|100|100|100|100|100|
|每個資料庫最小/最大彈性集區虛擬核心選項|N/A|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|複本數目|4|4|4|4|4|4|4|
|多重 AZ|yes|是|是|是|是|yes|
|讀取向外延展|yes|是|是|是|是|是|yes|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\* 關於任何個別資料庫的最大並行背景工作角色 (要求)，請參閱[單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果彈性集區是使用 Gen5 而且其最大 V 核心數是每個資料庫 2 個，則最大並行背景工作角色數是 200 個。  如果每個資料庫的最大 V 核心數是 0.5 個，則最大並行背景工作角色數是 50 個，因為 Gen5 上的最大並行背景工作角色數是每個 V 核心 100 個。  對於少於 1 個 V 核心的每個資料庫 V 核心最大數量的其他設定，並行背景工作角色的最大數目也是同樣地重新調整。

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>業務關鍵服務層：第 5 代計算平台 (第 2 部分)

|計算大小|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|
|虛擬核心|16|18|20|24|32|40|80|
|記憶體 (GB)|81.6|91.8|102|122.4|163.2|204|408|
|資料行存放區支援|yes|是|是|是|是|是|yes|
|OLTP 記憶體內部儲存體 (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|資料大小上限 (GB)|3072|3072|3072|4096|4096|4096|4096|
|記錄大小上限 (GB)|922|922|922|1229|1229|1229|1229|
|TempDB 大小 (GB)|384|384|384|384|384|384|384|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|目標 IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|每個集區的並行背景工作角色 (要求) 數上限 *|1680|1890|2100|2520|3360|4200|8400|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|30000|
|每個集區的最大 DB 數|100|100|100|100|100|100|100|
|每個資料庫最小/最大彈性集區虛擬核心選項|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|複本數目|4|4|4|4|4|4|4|
|多重 AZ|yes|是|是|是|是|yes|
|讀取向外延展|yes|是|是|是|是|是|yes|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\* 關於任何個別資料庫的最大並行背景工作角色 (要求)，請參閱[單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果彈性集區是使用 Gen5 而且其最大 V 核心數是每個資料庫 2 個，則最大並行背景工作角色數是 200 個。  如果每個資料庫的最大 V 核心數是 0.5 個，則最大並行背景工作角色數是 50 個，因為 Gen5 上的最大並行背景工作角色數是每個 V 核心 100 個。  對於少於 1 個 V 核心的每個資料庫 V 核心最大數量的其他設定，並行背景工作角色的最大數目也是同樣地重新調整。

如果彈性集區的所有虛擬核心都是忙碌中，則集區中的每個資料庫會收到等量的計算資源以處理查詢。 SQL Database 服務藉由確保運算時間的均等配量，提供資料庫之間的資源共用公平性。 彈性集區資源共用公平性不包括任何資源數量，否則當每個資料庫的最小虛擬核心數設為非零的值時，便會對每個資料庫保證資源數量。

## <a name="database-properties-for-pooled-databases"></a>集區資料庫的資料庫屬性

下表描述集區資料表的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 每個資料庫的虛擬核心上限 |集區中任何資料庫可以使用的虛擬核心數目上限，是否可用則是根據集區中其他資料庫的使用量而定。 每個資料庫的虛擬核心數目上限不等於資料庫的資源保證。 這個設定是全域設定，會套用至集區中的所有資料庫。 將每個資料庫的虛擬核心設定為最上限，以處理資料庫使用率的尖峰。 某種程度的過量使用是可預期的情況，因為集區通常會假設資料庫的熱門和冷門使用模式；在這些模式中，所有資料庫不會同時處於尖峰期。|
| 每個資料庫的虛擬核心下限 |集區中單一資料庫能夠保證的虛擬核心數下限。 這個設定是全域設定，會套用至集區中的所有資料庫。 每個資料庫最小虛擬核心建議設定為 0，同時也是預設值。 此屬性會設為 0 到每一資料庫的虛擬核心使用量平均值之間的任意數。 集區中資料庫數目和每個資料庫虛擬核心數目下限的乘積不能超過每個集區的虛擬核心。|
| 每個資料庫的儲存體上限 |使用者所設定集區資料庫的資料庫大小上限。 集區資料庫會共用配置的集區儲存體，所以資料庫可以觸達的大小會限制為較小的剩餘集區儲存體和資料庫大小。 資料庫大小上限是指資料檔案的大小上限，並不包含記錄檔所使用的空間。 |
|||

## <a name="next-steps"></a>後續步驟

- 如需單一資料庫的 vCore 資源限制，請參閱[使用以 vCore 為基礎之購買模型的單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)
- 如需單一資料庫的 DTU 資源限制，請參閱[使用以 DTU 為基礎之購買模型的單一資料庫資源限制](sql-database-dtu-resource-limits-single-databases.md)
- 如需適用於彈性集區的 DTU 資源限制，請參閱[使用以 DTU 為基礎之購買模型的彈性集區資源限制](sql-database-dtu-resource-limits-elastic-pools.md)
- 如需受控執行個體的資源限制，請參閱[受控執行個體資源限制](sql-database-managed-instance-resource-limits.md)。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
- 如需資料庫伺服器資源限制的相關資訊，請參閱 [SQL Database 伺服器上的資源限制概觀](sql-database-resource-limits-database-server.md)，以了解伺服器和訂用帳戶層級的限制。
