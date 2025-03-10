---
title: 在 Azure SQL Database 受控執行個體資料庫中設定複寫 | Microsoft Docs
description: 了解如何在 Azure SQL Database 受控執行個體資料庫中設定異動複寫
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 038d8c919e68e68f886525a6c78139496edef8e1
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893006"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>在 Azure SQL Database 受控執行個體資料庫中設定複寫

異動複寫可讓您將資料從 SQL Server 資料庫或其他執行個體資料庫複寫到 Azure SQL Database 受控執行個體資料庫。 您也可以使用異動複寫，將 Azure SQL Database 受控執行個體中執行個體資料庫內的變更推送至 SQL Server 資料庫、Azure SQL Database 中的單一資料庫，以及 Azure SQL Database 彈性集區中的集區資料庫。 異動複寫是 [Azure SQL Database 受控執行個體](sql-database-managed-instance.md)的公開預覽功能。 受控執行個體可以裝載發行者、散發者和訂閱者資料庫。 如需可用設定的相關資訊，請參閱[異動複寫設定](sql-database-managed-instance-transactional-replication.md#common-configurations)。

## <a name="requirements"></a>需求

將受控執行個體設定作為發行者或散發者需要：

- 受控執行個體目前未參與異地複寫關聯性。

   >[!NOTE]
   >Azure SQL Database 中的單一資料庫與集區資料庫只能是訂閱者。

- 所有受控執行個體都必須位於相同的 vNet 上。

- 連線會在複寫參與者之間使用 SQL 驗證。

- 複寫工作目錄的 Azure 儲存體帳戶共用。

- 必須在受控執行個體子網路的安全性規則中開啟連接埠 445 (TCP 輸出) 才能存取 Azure 檔案共用

## <a name="features"></a>特性

支援：

- 混用 SQL Server 內部部署和 Azure SQL Database 中受控執行個體的異動與快照式複寫。
- 訂閱者可以是內部部署 SQL Server 資料庫、Azure SQL Database 中的單一資料庫，或 Azure SQL Database 彈性集區中的集區資料庫。
- 單向或雙向複寫。

Azure SQL Database 的受控執行個體中不支援下列功能：

- 可更新的訂用帳戶。
- 作用中的異地複寫。

## <a name="configure-publishing-and-distribution-example"></a>設定發行與散發範例

1. 在入口網站中[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-create-tutorial-portal.md)。
2. 針為工作目錄[建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)。

   請務必複製儲存體金鑰。 請參閱[檢視並複製儲存體存取金鑰](../storage/common/storage-account-manage.md#access-keys
)。
3. 建立發行者的執行個體資料庫。

   在下列範例指令碼中，以執行個體資料庫的名稱取代 `<Publishing_DB>`。

4. 使用散發者的 SQL 驗證建立資料庫使用者。 使用安全的密碼。

   在下列範例指令碼中，搭配此 SQL Server 帳戶資料庫使用者名稱和密碼使用 `<SQL_USER>` 和 `<PASSWORD>`。

5. [連線到 SQL Database 受控執行個體](sql-database-connect-query-ssms.md)。

6. 執行下列查詢，以新增散發者和散發資料庫。

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. 若要將發行者設定為使用指定的散發資料庫，請更新並執行下列查詢。

   以 SQL Server 帳戶和密碼取代 `<SQL_USER>` 和 `<PASSWORD>`。

   以您的儲存體帳戶值取代 `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>`。  

   以來自 Microsoft Azure 儲存體帳戶的 [存取金鑰] 索引標籤連接字串取代 `<STORAGE_CONNECTION_STRING>`。

   更新下列查詢之後，請執行它。

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. 設定發行者以進行複寫。

    在下列查詢中，以發行者資料庫的名稱取代 `<Publishing_DB>`。

    以發行集的名稱取代 `<Publication_Name>`。

    以 SQL Server 帳戶和密碼取代 `<SQL_USER>` 和 `<PASSWORD>`。

    更新查詢之後，請執行它來建立發行集。

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. 新增發行項、訂用帳戶和發送訂閱代理程式。

   若要新增這些物件，請更新下列指令碼。

   - 以發行集物件的名稱取代 `<Object_Name>`。
   - 以來源結構描述的名稱取代 `<Object_Schema>`。
   - 取代用角括弧 `<>` 括住的其他參數以符合先前指令碼中的值。

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>另請參閱

- [異動複寫](sql-database-managed-instance-transactional-replication.md)
- [什麼是受控執行個體？](sql-database-managed-instance.md)
