---
title: 資料移轉服務和工具矩陣 - Azure |Microsoft Docs
description: 深入了解可用來移轉資料庫以及支援移轉程序不同階段的服務和工具。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/13/2018
ms.openlocfilehash: 9887860470632c37a586cb3029e2ef7d455a3a9c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713277"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>資料移轉案例可用的服務和工具

本文會列舉多種 Microsoft 和協力廠商提供的服務，以及可協助您處理各種資料庫和資料移轉情節和特殊工作的工具。

下表所列的服務和工具可用來妥善規劃資料移轉以及完成各個不同階段。

> [!NOTE]
> 在下列資料表中，標示星號 (*) 的項目代表第三方工具。

## <a name="business-justification-phase"></a>商業論證階段

| **來源** | **目標** | **探索 /**<br/>**清查** | **目標和 SKU**<br/>**建議** | **TCO/投資報酬率和**<br/>**商業案例** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [對應工具組](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure SQL DB MI | [對應工具組](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure SQL VM | [對應工具組](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | SQL DW |  |  | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS SQL | Azure SQL DB、MI、VM |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Azure SQL DB、MI、VM | [對應工具組](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [MigVisor*](https://www.migvisor.com/) |  |
| Oracle | SQL DW | [對應工具組](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| Oracle | 適用於 PostgreSQL 的 Azure DB |  |  |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB、MI、VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/) | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | 適用於 MySQL 的 Azure DB | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | 適用於 MySQL 的 Azure DB |  |  | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | 適用於 PostgreSQL 的 Azure DB | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | 適用於 PostgreSQL 的 Azure DB |  |  | [TCO 計算機](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Azure SQL DB、MI、VM |  |  |  |
| Access | Azure SQL DB、MI、VM |  |  |  |
| Sybase | Azure SQL DB、MI、VM |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>移轉前階段

| **來源** | **目標** | **應用程式資料存取**<br/>**層級評估** | **資料庫**<br/>**評量** | **效能**<br/>**評量** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL VM |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW |  |  |  |
| RDS SQL | Azure SQL DB、MI、VM |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Azure SQL DB、MI、VM |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | SQL DW |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | 適用於 PostgreSQL 的 Azure DB |  |  |  |
| MongoDB | Cosmos DB |  | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB、MI、VM |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | 適用於 MySQL 的 Azure DB |  |  |  |
| RDS MySQL | 適用於 MySQL 的 Azure DB |  |  |  |
| PostgreSQL | 適用於 PostgreSQL 的 Azure DB |  |  |  |
| RDS PostgreSQL | 適用於 PostgreSQL 的 Azure DB |  |  |  |
| DB2 | Azure SQL DB、MI、VM |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Access | Azure SQL DB、MI、VM |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase | Azure SQL DB、MI、VM |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| | | | | |

## <a name="migration-phase"></a>移轉階段

| **來源** | **目標** | **結構描述** | **資料**<br/>**(離線)** | **資料**<br/>**(線上)** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL DB MI | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL VM | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | SQL DW |  |  |  |
| RDS SQL | Azure SQL DB、MI、VM | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure SQL DB、MI、VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL DW | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Oracle | 適用於 PostgreSQL 的 Azure DB |  |  |  |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis 資料*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis 資料*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis 資料*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis 資料*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis 資料*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis 資料*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Azure SQL DB、MI、VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | 適用於 MySQL 的 Azure DB | [MySQL dump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | 適用於 MySQL 的 Azure DB | [MySQL dump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | 適用於 PostgreSQL 的 Azure DB | [PG 傾印*](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | 適用於 PostgreSQL 的 Azure DB | [PG 傾印*](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Azure SQL DB、MI、VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | Azure SQL DB、MI、VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase | Azure SQL DB、MI、VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| | | | | |

## <a name="post-migration-phase"></a>移轉後階段

| **來源** | **目標** | **最佳化** |
| --- | --- | --- |
| SQL Server | Azure SQL DB | [雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI | [雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL VM | [雲端 Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW |  |
| RDS SQL | Azure SQL DB、MI、VM |  |
| Oracle | Azure SQL DB、MI、VM |  |
| Oracle | SQL DW |  |
| Oracle | 適用於 PostgreSQL 的 Azure DB |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Azure SQL DB、MI、VM |  |
| MySQL | 適用於 MySQL 的 Azure DB |  |
| RDS MySQL | 適用於 MySQL 的 Azure DB |  |
| PostgreSQL | 適用於 PostgreSQL 的 Azure DB |  |
| RDS PostgreSQL | 適用於 PostgreSQL 的 Azure DB |  |
| DB2 | Azure SQL DB、MI、VM |  |
| Access | Azure SQL DB、MI、VM |  |
| Sybase | Azure SQL DB、MI、VM |  |
| | | |

## <a name="next-steps"></a>後續步驟

如需 Azure 資料庫移轉服務的概觀，請參閱[什麼是 Azure 資料庫移轉服務](dms-overview.md)一文。
