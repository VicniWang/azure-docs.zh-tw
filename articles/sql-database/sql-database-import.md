---
title: 匯入 BACPAC 檔案以建立 Azure SQL Database | Microsoft Docs
description: 匯入 BACPAC 檔案以建立新的 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: c5f90776cb0e8617f0e524bd6b1701f4bf20d0a1
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415695"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>快速入門：在 Azure SQL Database 中將 BACPAC 檔案匯入資料庫

您可以使用 [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 檔案將 SQL Server 資料庫匯入 Azure SQL Database 中的資料庫。 您可以從儲存在 Azure Blob 儲存體 (僅標準儲存體) 中，或內部部署中的本機儲存體中的 `BACPAC` 檔案匯入資料。 若要藉由提供更多且更快速的資源來最大化匯入速度，請在匯入程序期間，將您的資料庫調整為較高的服務層與計算大小。 然後，您可以在匯入成功後相應減少。

> [!NOTE]
> 資料庫匯入後的相容性層級會以來源資料庫的相容性層級為基礎。
> [!IMPORTANT]
> 匯入資料庫後，您可以選擇於目前的相容性層級 (針對 AdventureWorks2008R2 資料庫為層級 100) 或更高層級運作資料庫。 如需於特定相容性層級操作資料庫的含意與選項詳細資訊，請參閱 [ALTER DATABASE 相容性層級 (ALTER DATABASE Compatibility Level)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。 如需相容性層級其他相關資料庫等級設定的資訊，另請參閱 [ALTER DATABASE 範圍組態 (ALTER DATABASE SCOPED CONFIGURATION)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)。

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>在 Azure 入口網站中從 BACPAC 檔案匯入

[Azure 入口網站](https://portal.azure.com)「僅」支援在 Azure SQL Database 中建立單一資料庫，且「僅能」從儲存在 Azure Blob 儲存體中的 BACPAC 檔案建立。

> [!NOTE]
> [受控執行個體](sql-database-managed-instance.md)目前不支援使用 Azure 入口網站從 BACPAC 檔案將資料庫移轉到執行個體資料庫中。 若要匯入至受控執行個體，請使用 SQL Server Management Studio 或 SQLPackage。

1. 若要使用 Azure 入口網站從 BACPAC 檔案匯入至單一資料庫，請開啟適當的資料庫伺服器頁面，然後在工具列上選取 [匯入資料庫]。  

   ![資料庫匯入1](./media/sql-database-import/import1.png)

2. 選取 BACPAC 檔案的儲存體帳戶及容器，然後選取要匯入的 BACPAC 檔案。
3. 指定新資料庫的大小 (通常與原始資料庫相同)，並提供目的地 SQL Server 認證。 如需新 Azure SQL 資料庫可能之值的清單，請參閱[建立資料庫](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)。

   ![資料庫匯入2](./media/sql-database-import/import2.png)

4. 按一下 [確定]。

5. 若要監視匯入進度，請開啟資料庫的伺服器頁面，然後在 [設定] 下選取 [匯入/匯出記錄]。 匯入成功時，會處於 [已完成] 狀態。

   ![資料庫匯入狀態](./media/sql-database-import/import-status.png)

6. 若要確認資料庫伺服器上的資料庫為線上狀態，請選取 [SQL 資料庫]，並確認新的資料庫為 [線上] 狀態。

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>使用 SqlPackage 從 BACPAC 檔案匯入

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令列公用程式匯入 SQL Server 資料庫，請參閱[匯入參數和屬性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 SqlPackage 具有最新的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和[適用於 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)。 您也可以從 Microsoft 下載中心下載最新的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

為了規模和效能，我們建議在大部分生產環境中使用 SqlPackage，而不使用 Azure 入口網站。 如需 SQL Server 客戶諮詢小組部落格中有關使用 `BACPAC` 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) \(英文\)。

下列 SqlPackage 命令會將 **AdventureWorks2008R2** 資料庫從本機儲存體匯入至名為 **mynewserver20170403** 的 Azure SQL Database 伺服器。 此命令會建立名為 **myMigratedDatabase**、且具有**進階**服務層和 **P6** 服務目標的新資料庫。 請針對您的環境適當變更這些值。

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 若要連線到從公司防火牆後方管理單一資料庫的 SQL Database 伺服器，該防火牆必須開啟連接埠 1433。 若要連線到受控執行個體，您必須有[點對站連線](sql-database-managed-instance-configure-p2s.md)或快速路由連線。
>

此範例說明如何使用 SqlPackage 與 Active Directory 通用驗證來匯入資料庫。

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>使用 PowerShell 從 BACPAC 檔案匯入至單一資料庫

> [!NOTE]
> [受控執行個體](sql-database-managed-instance.md)目前不支援使用 Azure PowerShell 從 BACPAC 檔案將資料庫移轉到執行個體資料庫中。 若要匯入至受控執行個體，請使用 SQL Server Management Studio 或 SQLPackage。


使用 [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) Cmdlet 來提交匯入資料庫要求至 Azure SQL Database 服務。 匯入可能需要一些時間才能完成，視資料庫大小而定。

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 您可以使用 [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) Cmdlet 來查看匯入的進度。 如果在要求後立即執行此 Cmdlet，通常會傳回 **Status:InProgress**。 當您看見 **Status:Succeeded** 時，即表示匯入已完成。

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
如需其他指令碼範例，請參閱[從 BACPAC 檔案匯入資料庫](scripts/sql-database-import-from-bacpac-powershell.md)。

## <a name="limitations"></a>限制

不支援匯入至彈性集區中的資料庫。 您可以將資料匯入到單一資料庫，然後將資料庫移到彈性集區。

## <a name="import-using-wizards"></a>使用精靈匯入

您也可以使用這些精靈。

- [SQL Server Management Studio 中的匯入資料層應用程式精靈](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server 匯入和匯出精靈](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何連線及查詢已匯入的 SQL Database，請參閱[快速入門：Azure SQL Database：使用 SQL Server Management Studio 連線及查詢資料](sql-database-connect-query-ssms.md)。
- 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)。
- 如需有關整個 SQL Server 資料庫移轉程序的討論，包括效能建議，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 若要了解如何管理和共用儲存體金鑰，以及安全地共用存取簽章，請參閱 [Azure 儲存體安全性指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
