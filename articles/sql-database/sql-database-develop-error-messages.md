---
title: SQL 錯誤碼 - 資料庫連線錯誤 | Microsoft Docs
description: '了解 SQL Database 用戶端應用程式的 SQL 錯誤碼，例如常見的資料庫連線錯誤、資料庫副本問題，以及一般錯誤。 '
keywords: sql error code,access sql,database connection error,sql error codes,sql 錯誤碼,存取 sql,資料庫連線錯誤,sql 錯誤碼
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 4da18fffc98367f24ec95bd27617e7638e3d5705
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003664"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>請參閱 SQL Database 用戶端應用程式的 SQL 錯誤碼：資料庫連線錯誤和其他問題

本文列出 SQL Database 用戶端應用程式的 SQL 錯誤碼，包括資料庫連線錯誤、暫時性錯誤、資源管理錯誤、資料庫副本問題、彈性集區，以及其他錯誤。 大多數分類是專門針對 Azure SQL Database，並不適用 Microsoft SQL Server。 另請參閱[系統錯誤訊息](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx)。

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>資料庫連線錯誤、暫時性錯誤，以及其他暫時的錯誤

下表涵蓋您的應用程式嘗試存取 SQL Database 可能發生之連線遺失錯誤和其他暫時性錯誤的 SQL 錯誤碼。 如需連線到 Azure SQL Database 的快速入門教學課程，請參閱 [連線到 Azure SQL Database](sql-database-libraries.md)。

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>最常見的資料庫連線錯誤和暫時性錯誤

Azure 基礎結構能夠在 SQL Database 服務出現繁重的工作負載時動態重新設定伺服器。  此動態行為可能會導致您的用戶端程式遺失其 SQL Database 連接。 此類錯誤情況稱為「暫時性錯誤」 。

強烈建議您的用戶端程式具有重試邏輯，以便在給予暫時性錯誤一些時間自行修正後，可以嘗試重新建立連線。  我們建議您在您第一次重試前延遲 5 秒鐘。 在少於 5 秒的延遲後重試，雲端服務會有超過負荷的風險。 對於後續每次重試，延遲應以指數方式成長，最大值為 60 秒。

暫時性錯誤通常是資訊清單，在您用戶端程式中的下列錯誤訊息之一：

* 伺服器 &lt;Azure_instance&gt; 上的資料庫 &lt;db_name&gt; 目前無法使用。 請稍後重試連接。 如果問題持續發生，請連絡客戶支援服務，並提供工作階段追蹤識別碼 &lt;session_id&gt;
* 伺服器 &lt;Azure_instance&gt; 上的資料庫 &lt;db_name&gt; 目前無法使用。 請稍後重試連接。 如果問題持續發生，請連絡客戶支援服務，並提供工作階段追蹤識別碼 &lt;session_id&gt;。 (Microsoft SQL Server，錯誤：40613)
* 遠端主機已強制關閉現有的連接。
* System.Data.Entity.Core.EntityCommandExecutionException:執行命令定義時發生錯誤。 詳細資訊請參閱內部例外狀況。 ---> System.Data.SqlClient.SqlException:從伺服器接收結果時發生傳輸層級錯誤。 (提供者：工作階段提供者，錯誤：19 - 無法使用實體連線)
* 嘗試連線到次要資料庫失敗，因為資料庫正在重新設定，且在主要資料庫上的使用中交易期間忙於套用新的頁面。 

如需重試邏輯的程式碼範例，請參閱：

* [SQL Database 和 SQL Server 的連接庫](sql-database-libraries.md) 
* [修正 SQL Database 中連線錯誤和暫時性錯誤的動作](sql-database-connectivity-issues.md)

在 [SQL Server 連接集區 (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx) 中可找使用 ADO.NET 之用戶端的封鎖期間討論。

### <a name="transient-fault-error-codes"></a>暫時性錯誤錯誤碼

下列錯誤為暫時性錯誤，並且應該在應用程式邏輯中重試： 

| 錯誤碼 | 嚴重性 | 說明 |
| ---:| ---:|:--- |
| 4060 |16 |無法開啟登入所要求的資料庫 "%.&#x2a;ls"。 登入失敗。 |
| 40197 |17 |服務處理您的要求時發生錯誤。 請再試一次。 錯誤代碼 %d。<br/><br/>當服務因為軟體或硬體升級、硬體故障或任何其他容錯移轉問題而關閉時，您會收到這個錯誤。 內嵌在錯誤 40197 訊息中的錯誤代碼 (%d) 提供發生的失敗或容錯移轉種類的其他資訊。 錯誤代碼會內嵌在錯誤 40197 的訊息錯誤範例包括 40020、40143、40166 和 40540。<br/><br/>重新連線到您的 SQL Database 伺服器時，會自動連線至健康情況良好的資料庫複本。 您的應用程式必須攔截錯誤 40197、記錄訊息中內嵌的錯誤碼 (%d) 以進行疑難排解，並嘗試重新連接到 SQL Database 直到資源可供使用，並再次建立您的連線。 |
| 40501 |20 |服務目前忙碌中。 在 10 秒後重試要求。 事件識別碼：%ls。 程式碼：%d。<br/><br/>如需詳細資訊，請參閱<br/>• [Azure SQL Database 資源限制](sql-database-service-tiers-dtu.md)。 |
| 40613 |17 |資料庫 '%.&#x2a;ls' (在伺服器 '%.&#x2a;ls' 上) 目前無法使用。 請稍後重試連接。 如果問題持續發生，請連絡客戶支援服務，並提供工作階段追蹤識別碼 '%.&#x2a;ls'。 |
| 49918 |16 |無法處理要求。 資源不足，無法處理要求。<br/><br/>服務目前忙碌中。 請稍後再重試要求。 |
| 49919 |16 |無法處理建立或更新要求。 訂用帳戶 "%ld" 太多建立或更新作業進行中。<br/><br/>服務正忙於處理多個建立或更新您訂用帳戶或伺服器的要求。 要求目前已封鎖以求資源最佳化。 查詢 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以查看暫止的作業。 等待直到暫止的建立或更新要求完成，或刪除您的其中一個暫止要求，稍後再重試您的要求。 |
| 49920 |16 |無法處理要求。 訂用帳戶 "%ld" 太多作業進行中。<br/><br/>服務正忙於處理這個訂用帳戶的多個要求。 要求目前已封鎖以求資源最佳化。 查詢 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以查看作業狀態。 等候直到暫止的要求已完成，或刪除您其中一個暫止要求，稍後再重試您的要求。 |
| 4221 |16 |登入 read-secondary 失敗，因為 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING' 上的等候時間很長。 無法使用複本進行登入，因為在複本回收時執行中之交易的資料列版本已遺失。 將主要複本上的使用中交易復原或認可，就可以解決問題。 避免在主要伺服器上的冗長寫入交易，就可讓此條件發生率降至最低。 |

## <a name="database-copy-errors"></a>資料庫複製錯誤

在 Azure SQL Database 中複製資料庫時，可能會發生下列錯誤。 如需詳細資訊，請參閱 [複製 Azure SQL Database](sql-database-copy.md)。

| 錯誤碼 | 嚴重性 | 說明 |
| ---:| ---:|:--- |
| 40635 |16 |IP 位址 '%.&#x2a;ls' 的用戶端已暫時停用。 |
| 40637 |16 |建立資料庫副本目前已停用。 |
| 40561 |16 |資料庫複製失敗。 來源或目標資料庫不存在。 |
| 40562 |16 |資料庫複製失敗。 已經卸除來源資料庫。 |
| 40563 |16 |資料庫複製失敗。 已經卸除目標資料庫。 |
| 40564 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並再試一次。 |
| 40565 |16 |資料庫複製失敗。 不允許從相同來源進行超過 1 個並行資料庫複製。 請卸除目標資料庫並稍後再試一次。 |
| 40566 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並再試一次。 |
| 40567 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並再試一次。 |
| 40568 |16 |資料庫複製失敗。 來源資料庫已變成無法使用。 請卸除目標資料庫並再試一次。 |
| 40569 |16 |資料庫複製失敗。 目標資料庫已變成無法使用。 請卸除目標資料庫並再試一次。 |
| 40570 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並稍後再試一次。 |
| 40571 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並稍後再試一次。 |

## <a name="resource-governance-errors"></a>資源管理錯誤

使用 Azure SQL Database 時過度使用資源會造成下列錯誤。 例如︰

* 交易已開啟太長時間。
* 交易持有太多鎖定。
* 應用程式耗用太多記憶體。
* 應用程式耗用太多 `TempDb` 空間。

相關主題：

* 此處可取得更詳細資訊：[Azure SQL Database 資源限制](sql-database-service-tiers-dtu.md)。

| 錯誤碼 | 嚴重性 | 說明 |
| ---:| ---:|:--- |
| 10928 |20 |資源識別碼：%d。 資料庫的 %s 限制是 %d，且已達到。 如需詳細資訊，請參閱[單一和集區資料庫的 SQL Database 資源限制](sql-database-resource-limits-database-server.md)。<br/><br/>資源識別碼可指出已達到限制的資源。 對於背景工作執行緒，資源識別碼 = 1。 對於工作階段，資源識別碼 = 2。<br/><br/>如需有關此錯誤以及其解決方法的詳細資訊，請參閱：<br/>• [Azure SQL Database 資源限制](sql-database-service-tiers-dtu.md)。 |
| 10929 |20 |資源識別碼：%d。 %s 最小保證是 %d，最大限制是 %d，而資料庫的目前使用量是 %d。 但伺服器目前太忙碌，無法針對此資料庫支援大於 %d 的要求。 如需詳細資訊，請參閱[單一和集區資料庫的 SQL Database 資源限制](sql-database-resource-limits-database-server.md)。 或者，請稍後再試一次。<br/><br/>資源識別碼可指出已達到限制的資源。 對於背景工作執行緒，資源識別碼 = 1。 對於工作階段，資源識別碼 = 2。<br/><br/>如需有關此錯誤以及其解決方法的詳細資訊，請參閱：<br/>• [Azure SQL Database 資源限制](sql-database-service-tiers-dtu.md)。 |
| 40544 |20 |資料庫已達到大小配額。 資料分割或刪除資料、卸除索引，或參閱可能解決方式的文件。 |
| 40549 |16 |工作階段已終止，因為您有長時間執行的交易。 請嘗試縮短您的交易時間。 |
| 40550 |16 |工作階段已終止，因為它取得太多鎖定。 嘗試在單一交易中讀取或修改較少的資料列。 |
| 40551 |16 |已終止工作階段，因為它過度使用 `TEMPDB`。 請嘗試修改查詢，以減少使用暫存資料表空間。<br/><br/>如果您是使用暫存物件，請在工作階段不再需要暫存物件時予以卸除，藉此節省 `TEMPDB` 資料庫的空間。 |
| 40552 |16 |已終止工作階段，因為過度使用交易記錄檔空間。 請嘗試在單一交易中修改較少的資料列。<br/><br/>如果您使用 `bcp.exe` 公用程式或 `System.Data.SqlClient.SqlBulkCopy` 類別執行大量插入，請嘗試使用 `-b batchsize` 或 `BatchSize` 選項來限制每一筆交易中要複製到伺服器的資料列數目。 如果您要使用 `ALTER INDEX` 陳述式重建索引，請嘗試使用 `REBUILD WITH ONLINE = ON` 選項。 |
| 40553 |16 |已終止工作階段，因為過度使用記憶體。 請嘗試修改查詢以處理較少的資料列。<br/><br/>減少 Transact-SQL 程式碼中的 `ORDER BY` 和 `GROUP BY` 作業數目，從而減少查詢的記憶體需求。 |

## <a name="elastic-pool-errors"></a>彈性集區錯誤

下列錯誤與建立及使用彈性集區有關：

| 錯誤碼 | 嚴重性 | 說明 | 更正措施 |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |彈性集區已達到其儲存體限制。 彈性集區的儲存體使用量不能超過 (%d) MB。 當彈性集區達到儲存體限制時，嘗試將資料寫入資料庫。 |請考慮盡可能增加彈性集區的 DTU 及/或儲存體，以提高其儲存體限制、減少彈性集區中個別資料庫所使用的儲存體量，或是從彈性集區移除資料庫。 |
| 10929 | 16 |%s 最小保證是 %d，最大限制是 %d，而資料庫的目前使用量是 %d。 但伺服器目前太忙碌，無法針對此資料庫支援大於 %d 的要求。 請參閱[適用於單一和集區資料庫的 SQL Database 資源限制](sql-database-resource-limits-database-server.md)以取得協助。 或者，請稍後再試一次。 每個資料庫的最小 DTU / vCore；每個資料庫的最大 DTU / vCore。 彈性集區中的所有資料庫並行背景工作 (要求) 總數試圖超過集區限制。 |請考慮盡可能增加彈性集區的 DTU 或 vCore，以提高其背景工作數的限制，或是從彈性集區移除資料庫。 |
| 40844 | 16 |伺服器 '%ls' 上的資料庫 '%ls' 是彈性集區中的 '%ls' 版資料庫，且無法有連續複製關聯性。  |N/A |
| 40857 | 16 |找不到伺服器 '%ls' 的彈性集區，彈性集區名稱: '%ls'。 指定的彈性集區不存在於指定的伺服器中。 | 請提供有效的彈性集區名稱。 |
| 40858 | 16 |彈性集區 '%ls' 已存在於伺服器 '%ls' 中。 指定的彈性集區已存在於指定的 SQL Database 伺服器中。 | 請提供新的彈性集區名稱。 |
| 40859 | 16 |彈性集區不支援服務層 '%ls'。 指定的服務層不支援彈性集區佈建。 |請提供正確的版本，或者將服務層保留空白，以使用預設的服務層。 |
| 40860 | 16 |彈性集區 '%ls' 和服務目標 '%ls' 的組合無效。 如果資源類型指定為 'ElasticPool'，才能同時指定彈性集區和服務層。 |請指定正確的彈性集區和服務層組合。 |
| 40861 | 16 |資料庫版本 '%.ls' 不能和彈性集區服務層 ('%.ls') 不同。 資料庫版本和彈性集區服務層不同。 |請勿指定與彈性集區服務層不同的資料庫版本。  請注意，不需要指定資料庫版本。 |
| 40862 | 16 |指定彈性集區服務目標時，必須指定彈性集區名稱。 彈性集區服務目標不是彈性集區的唯一識別依據。 |使用彈性集區服務目標時，請指定彈性集區名稱。 |
| 40864 | 16 |服務層 '%.*ls' 的彈性集區 DTU 數必須至少為 (%d) 個 DTU。 試圖將彈性集區的 DTU 數設為低於最小限制。 |請重試將彈性集區的 DTU 數至少設為等於最小限制。 |
| 40865 | 16 |服務層 '%.*ls' 的彈性集區 DTU 數不可超過 (%d) 個 DTU。 試圖將彈性集區的 DTU 數設為高於最大限制。 |請重試將彈性集區的 DTU 數設為低於最大限制。 |
| 40867 | 16 |服務層 '%.*ls' 的每個資料庫最大 DTU 必須至少為 (%d)。 試圖將每個資料庫的最大 DTU 設為低於支援的限制。 | 請考慮使用可支援所需設定的彈性集區服務層。 |
| 40868 | 16 |服務層 '%.*ls' 的每個資料庫最大 DTU 不可超過 (%d)。 試圖將每個資料庫的最大 DTU 設為超過支援的限制。 | 請考慮使用可支援所需設定的彈性集區服務層。 |
| 40870 | 16 |服務層 '%.*ls' 的每個資料庫最小 DTU 不可超過 (%d)。 試圖將每個資料庫的最小 DTU 設為超過支援的限制。 | 請考慮使用可支援所需設定的彈性集區服務層。 |
| 40873 | 16 |資料庫數目 (%d) 和每個資料庫的最小 DTU (%d) 不能超過彈性集區的 DTU 數 (%d)。 試圖針對彈性集區中的資料庫指定最小 DTU 而超過彈性集區的 DTU 數。 | 請考慮增加彈性集區的 DTU 數，或減少每個資料庫的最小 DTU，或是減少彈性集區中的資料庫數目。 |
| 40877 | 16 |除非不包含任何資料庫，否則無法刪除彈性集區。 彈性集區包含一或多個資料庫，因此無法刪除。 |若要刪除彈性集區，請移除其中的資料庫。 |
| 40881 | 16 |彈性集區 '%.*ls' 已達到其資料庫計數上限。  如果彈性集區的 DTU 為 (%d) 個，則彈性集區的資料庫計數上限不能超過 (%d)。 當達到彈性集區的資料庫計數上限時，試圖建立資料庫，或將資料庫加入至彈性集區。 | 請考慮盡可能增加彈性集區的 DTU 數，以提高其資料庫限制，或是從彈性集區移除資料庫。 |
| 40889 | 16 |不能減少彈性集區 '%.*ls' 的 DTU 數或儲存體限制，因為這麼做會無法提供足夠的儲存空間給位於其中的資料庫。 試圖將彈性集區的儲存體限制減少為低於其儲存體使用量。 | 請考慮減少彈性集區中之個別資料庫的儲存體使用量，或從集區中移除資料庫，以便減少集區的 DTU 數或儲存體限制。 |
| 40891 | 16 |每個資料庫的最小 DTU (%d) 不能超過每個資料庫的最大 DTU (%d)。 試圖將每個資料庫的最小 DTU 設為超過每個資料庫的最大 DTU。 |請確定每個資料庫的最小 DTU 並未超過每個資料庫的最大 DTU。 |
| TBD | 16 |彈性集區中個別資料庫的儲存體大小，不能超過 '%.*ls' 服務層彈性集區所允許的大小上限。 資料庫的大小上限超過彈性集區服務層所允許的大小上限。 |請將資料庫的大小上限設定在彈性集區服務層所允許的大小上限內。 |

相關主題：

* [建立彈性集區 (C#)](sql-database-elastic-pool-manage-csharp.md)
* [管理彈性集區 (C#)](sql-database-elastic-pool-manage-csharp.md)
* [建立彈性集區 (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [監視和管理彈性集區 (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>一般錯誤

下列錯誤不屬於任何先前的類別。

| 錯誤碼 | 嚴重性 | 說明 |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) 不是有效的名稱，因為它包含無效字元。 |
| 18452 |14 |登入失敗。 登入是來自不受信任的網域並且無法用於 Windows 驗證。%.&#x2a;ls (這個版本的 SQL Server 中不支援 Windows 登入)。 |
| 18456 |14 |使用者 '%.&#x2a;ls' 登入失敗。%.&#x2a;ls%.&#x2a;ls (使用者 "%.&#x2a;ls" 登入失敗)。 |
| 18470 |14 |使用者 '%.&#x2a;ls' 登入失敗。 原因：帳戶已停用。%.&#x2a;ls |
| 40014 |16 |無法在相同交易中使用多個資料庫。 |
| 40054 |16 |在這個版本的 SQL Server 中不支援沒有叢集索引的資料表。 建立叢集的索引並再試一次。 |
| 40133 |15 |此作業在這個版本的 SQL Server 中不受支援。 |
| 40506 |16 |指定的 SID 對這個版本的 SQL Server 無效。 |
| 40507 |16 |'%.&#x2a;ls' 無法在這個版本的 SQL Server 中使用參數叫用。 |
| 40508 |16 |USE 陳述式不支援在資料庫之間切換。 使用新連接來連接到不同的資料庫。 |
| 40510 |16 |陳述式 '%.&#x2a;ls' 在這個版本的 SQL Server 中不受支援 |
| 40511 |16 |內建函數 '%.&#x2a;ls' 在這個版本的 SQL Server 中不受支援。 |
| 40512 |16 |被取代的功能 '%ls' 在這個版本的 SQL Server 中不受支援。 |
| 40513 |16 |伺服器變數 '%.&#x2a;ls' 在這個版本的 SQL Server 中不受支援。 |
| 40514 |16 |'%ls' 在這個版本的 SQL Server 中不受支援。 |
| 40515 |16 |'%.&#x2a;ls' 中資料庫和/或伺服器名稱的參考在這個版本的 SQL Server 中不受支援。 |
| 40516 |16 |全域暫存物件在這個版本的 SQL Server 中不受支援。 |
| 40517 |16 |關鍵字或陳述式選項 '%.&#x2a;ls' 在這個版本的 SQL Server 中不受支援。 |
| 40518 |16 |DBCC 命令 '%.&#x2a;ls' 在這個版本的 SQL Server 中不受支援。 |
| 40520 |16 |安全性實體類別 '%S_MSG' 在這個版本的 SQL Server 中不受支援。 |
| 40521 |16 |安全性實體類別 '%S_MSG' 在這個版本的 SQL Server 的伺服器範圍中不受支援。 |
| 40522 |16 |資料庫主體 '%.&#x2a;ls' 類型在這個版本的 SQL Server 中不受支援。 |
| 40523 |16 |隱含使用者 '%.&#x2a;ls' 建立在這個版本的 SQL Server 中不受支援。 明確建立使用者之後再使用它。 |
| 40524 |16 |資料型別 '%.&#x2a;ls' 在這個版本的 SQL Server 中不受支援。 |
| 40525 |16 |WITH '%.ls' 在這個版本的 SQL Server 中不受支援。 |
| 40526 |16 |'%.&#x2a;ls' 資料列集提供者在這個版本的 SQL Server 中不受支援。 |
| 40527 |16 |連結的伺服器在這個版本的 SQL Server 中不受支援。 |
| 40528 |16 |無法在這個版本的 SQL Server 中將使用者對應到憑證、非對稱金鑰或 Windows 登入。 |
| 40529 |16 |模擬內容中的內建函數 '%.&#x2a;ls' 在這個版本的 SQL Server 中不受支援。 |
| 40532 |11 |無法開啟登入所要求的伺服器 "%.&#x2a;ls"。 登入失敗。 |
| 40553 |16 |已終止工作階段，因為過度使用記憶體。 請嘗試修改查詢以處理較少的資料列。<br/><br/> 減少 Transact-SQL 程式碼中的 `ORDER BY` 和 `GROUP BY` 作業數目有助於減少查詢的記憶體需求。 |
| 40604 |16 |無法 CREATE/ALTER DATABASE，因為它會超過伺服器的配額。 |
| 40606 |16 |附加資料庫在這個版本的 SQL Server 中不受支援。 |
| 40607 |16 |Windows 登入在這個版本的 SQL Server 中不受支援。 |
| 40611 |16 |伺服器最多可以定義 128 個防火牆規則。 |
| 40614 |16 |防火牆規則的起始 IP 位址不能超過結束 IP 位址。 |
| 40615 |16 |無法開啟登入所要求的伺服器 '{0}'。 不允許 IP 位址為 '{1}' 的用戶端存取伺服器。<br /><br />若要啟用存取，請使用 SQL Database 入口網站，或在 master 資料庫上執行 sp\_set\_firewall\_rule，為此 IP 位址或位址範圍建立防火牆規則。 可能需要五分鐘的時間，這項變更才會生效。 |
| 40617 |16 |以 (規則名稱) 開頭的防火牆規則名稱太長。 最大長度為 128。 |
| 40618 |16 |防火牆規則名稱不可為空白。 |
| 40620 |16 |使用者 "%.&#x2a;ls" 登入失敗。 密碼變更失敗。 在登入期間變更密碼在這個版本的 SQL Server 中不受支援。 |
| 40627 |20 |伺服器 '{0}' 和資料庫 '{1}' 上的作業正在進行中。 請稍候幾分鐘後再試一次。 |
| 40630 |16 |密碼驗證失敗。 因為密碼長度太短，密碼不符合原則需求。 |
| 40631 |16 |您指定的密碼太長。 密碼必須不超過 128 個字元。 |
| 40632 |16 |密碼驗證失敗。 因為密碼不夠複雜，密碼不符合原則需求。 |
| 40636 |16 |無法在此作業中使用保留的資料庫名稱 '%.&#x2a;ls'。 |
| 40638 |16 |無效的訂用帳戶識別碼 (subscription-id)。 訂用帳戶不存在。 |
| 40639 |16 |要求不符合結構描述：(結構描述錯誤)。 |
| 40640 |20 |伺服器發生非預期的例外狀況。 |
| 40641 |16 |指定的位置無效。 |
| 40642 |17 |伺服器目前太過忙碌。 請稍後再試一次。 |
| 40643 |16 |指定的 x-ms-version 標頭值無效。 |
| 40644 |14 |無法授權存取指定的訂用帳戶。 |
| 40645 |16 |Servername (servername) 不能是空白或 Null。 它可以只由小寫字母 'a'-'z'、數字 0-9 和連字號組成。 連字號不得在名稱的開頭或結尾。 |
| 40646 |16 |訂用帳戶 ID 不能空白。 |
| 40647 |16 |訂用帳戶 (subscription-id) 沒有伺服器 (servername)。 |
| 40648 |17 |已經執行太多要求。 請稍後重試。 |
| 40649 |16 |指定了無效的 Content-Type。 僅支援 application/xml。 |
| 40650 |16 |訂用帳戶 (subscription-id) 不存在或尚未準備進行作業。 |
| 40651 |16 |無法建立伺服器，因為訂用帳戶 (subscription-id) 已停用。 |
| 40652 |16 |無法移動或建立伺服器。 訂用帳戶 (subscription-id) 會超出伺服器配額。 |
| 40671 |17 |閘道器與管理服務之間的通訊失敗。 請稍後重試。 |
| 40852 |16 |無法在登入所要求的伺服器 '%.\*ls' 上開啟資料庫 '%.\*ls'。 只允許使用已啟用安全性的連接字串存取資料庫。 若要存取此資料庫，請將連接字串修改成在伺服器 FQDN 中包含 'secure'。也就是說  -  'server name'.database.windows.net 應修改為 'server name'.database`secure`.windows.net。 |
| 40914 | 16 | 無法開啟登入所要求的伺服器 '*[server-name]*'。 用戶端不得存取該伺服器。<br /><br />若要修正，請考慮新增[虛擬網路規則](sql-database-vnet-service-endpoint-rule-overview.md)。 |
| 45168 |16 |SQL Azure 系統未達負載，並正在對單一 SQL Database 伺服器的並行 DB CRUD 作業 (例如，建立資料庫) 放置上限。 錯誤訊息中指定的伺服器已超過最大並行連接數目。 請稍後再試。 |
| 45169 |16 |SQL Azure 系統未達負載，並正在對單一訂用帳戶的並行伺服器 CRUD 作業 (例如，建立資料庫) 的數量放置上限。 錯誤訊息中指定的訂用帳戶已超出最大並行連接數目，因此要求已遭到拒絕。 請稍後再試。 |

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure SQL Database 功能](sql-database-features.md)。
* 深入了解[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)。
* 深入了解[虛擬核心形式的購買模型](sql-database-service-tiers-vcore.md)。

