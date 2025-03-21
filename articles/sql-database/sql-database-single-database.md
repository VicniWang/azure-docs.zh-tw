---
title: 什麼是 Azure SQL Database 單一資料庫 | Microsoft Docs
description: 了解 Azure SQL Database 中的單一資料庫
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: e8af73c955305c54efc478290608e627acb431ea
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099321"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>什麼是 Azure SQL Database 中的單一資料庫

單一資料庫部署選項會使用其本身的資源集在 Azure SQL Database 中建立資料庫，並透過 SQL Database 伺服器進行管理。 使用單一資料庫時，每個資料庫會彼此隔離並且可攜，且各自在[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)或[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)內有其本身的服務層和保證的計算大小。

> [!IMPORTANT]
> 單一資料庫是 Azure SQL Database 的三個部署選項之一。 另外兩個選項是[彈性集區](sql-database-elastic-pool.md)和[受控執行個體](sql-database-managed-instance.md)。
> [!NOTE]
> 如需 Azure SQL Database 的詞彙解釋，請參閱 [SQL 資料庫術語詞彙](sql-database-glossary-terms.md)

## <a name="dynamic-scalabilty"></a>動態延展性

您可以每個月在低性價比服務層支付少量費用，在小型單一資料庫中建置第一個應用程式，然後，隨時以手動或程式設計方式將[服務層變更為](sql-database-single-database-scale.md)較高性價比的服務層，以符合您的解決方案需求。 您的應用程式或客戶皆無須停機，即可調整效能。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求，並且讓您只需支付您所需的資源費用。

## <a name="single-databases-and-elastic-pools"></a>單一資料庫和彈性集區

單一資料庫可移入或移出[彈性集區](sql-database-elastic-pool.md)以進行資源共用。 對於許多企業和應用程式而言，只要能夠建立單一資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 彈性集區的設計可解決此問題。 概念很簡單。 您可將效能資源配置到集區，而非個別的資料庫，並支付該集區的集體效能資源，而非單一資料庫效能的費用。

## <a name="monitoring-and-alerting"></a>監視和警示

您可以使用[內建的效能監視](sql-database-performance.md)和[警示工具](sql-database-insights-alerts-portal.md)，並搭配使用效能分級。 使用這些工具，您可以根據目前或專案的效能需求快速評估相應增加或減少的影響。 此外，SQL Database 可以[發出計量和診斷記錄](sql-database-metrics-diag-logging.md)以便進行監視。

## <a name="availability-capabilities"></a>可用性功能

單一資料庫、彈性集區和受控執行個體均提供許多可用性特性。 如需相關資訊，請參閱[可用性特性](sql-database-technical-overview.md#availability-capabilities)。

## <a name="transact-sql-differences"></a>Transact-SQL 差異

Microsoft SQL Server 和 Azure SQL Database 都支援應用程式使用的大部分 Transact-SQL 功能。 例如，資料類型、運算子、字串、算術、邏輯及資料指標函式等核心 SQL 元件在 SQL Server 與 SQL Database 中都以相同的方式運作。 不過，DDL (資料定義語言) 和 DML (資料操作語言) 元素中有幾個 T-SQL 差異導致對 T-SQL 陳述式和查詢僅提供部分支援 (將在本文章中稍後探討)。
此外，還有一些功能和語法完全不受支援，因為 Azure SQL Database 的設計目的是要將功能隔離，讓它們不與 master 資料庫和作業系統相依。 因此，大多數伺服器層級活動都不適用於 SQL Database。 如果它們設定伺服器層級選項、作業系統元件或指定檔案系統組態，便無法使用 T-SQL 陳述式和選項。 當需要這類功能時，通常會從 SQL Database 或從另一個 Azure 功能或服務以其他方式提供適合的替代方案。

如需詳細資訊，請參閱[解決移轉至 SQL Database 期間的 Transact-SQL 差異](sql-database-transact-sql-information.md)。

## <a name="security"></a>安全性

SQL Database 提供了多種[內建安全性與合規性](sql-database-security-overview.md)功能，可協助您的應用程式符合各種安全性與合規需求。

## <a name="next-steps"></a>後續步驟

- 若要快速地開始使用單一資料庫，請從[單一資料庫快速入門 guide.md](sql-database-single-database-quickstart-guide.md) 著手。
- 若要深入了解如何將 SQL Server 資料庫移轉至 Azure，請參閱[移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 如需支援功能的相關資訊，請參閱「[功能](sql-database-features.md)」。
