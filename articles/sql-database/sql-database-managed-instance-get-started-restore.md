---
title: 將備份還原至 Azure SQL Database 受控執行個體 | Microsoft Docs
description: 使用 SSMS 將資料庫備份還原至 Azure SQL Database 受控執行個體。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: 9459aa9df74efa500109d7c281daad28355bd0d0
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895719"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>快速入門：將資料庫還原到受控執行個體 

在本快速入門中，您將使用 SQL Server Management Studio (SSMS)，將資料庫 (Wide World Importers - Standard 備份檔案) 從 Azure Blob 儲存體還原至 Azure SQL Database [受控執行個體](sql-database-managed-instance.md)。 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> - 如需使用 Azure 資料庫移轉服務 (DMS) 進行移轉的詳細資訊，請參閱[使用 DMS 的受控執行個體移轉](../dms/tutorial-sql-server-to-managed-instance.md)。 
> - 如需各種移轉方法的詳細資訊，請參閱[將 SQL Server 執行個體移轉至 Azure SQL Database 受控執行個體](sql-database-managed-instance-migrate.md)。

## <a name="prerequisites"></a>必要條件

此快速入門：

- 使用[建立受控執行個體](sql-database-managed-instance-get-started.md)快速入門的資源。
- 需要您的電腦已安裝最新的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)。
- 需要使用 SSMS 以連線到您的受控執行個體。 請參閱這些快速入門以了解如何連線：
  - [從 Azure VM 連線到 Azure SQL Database 受控執行個體](sql-database-managed-instance-configure-vm.md)
  - [設定從內部部署連線至 Azure SQL Database 受控執行個體的點對站連線](sql-database-managed-instance-configure-p2s.md)。

> [!NOTE]
> 如需有關使用 Azure Blob 儲存體和[共用存取簽章 (SAS) 金鑰](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)來備份和還原 SQL Server 資料庫的詳細資訊，請參閱 [SQL Server 備份到 URL](sql-database-managed-instance-get-started-restore.md)。

## <a name="restore-the-database-from-a-backup-file"></a>從備份檔案還原資料庫

在 SSMS 中，遵循這些步驟以將 Wide World Importers 資料庫還原至您的受控執行個體。 資料庫備份檔案儲存在預先設定的 Azure Blob 儲存體帳戶中。

1. 開啟 SMSS 並連線到您的受控執行個體。
2. 從左側功能表中，以滑鼠右鍵按一下您的受控執行個體，並選取 [新增查詢] 以開啟新的查詢視窗。
3. 執行下列 SQL 指令碼，它會使用預先設定的儲存體帳戶和 SAS 金鑰，在您的受控執行個體中[建立認證](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql?view=sql-server-2017)。

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![建立認證](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. 若要檢查您的認證，請執行下列指令碼，它會使用[容器](https://azure.microsoft.com/services/container-instances/) URL 來取得備份檔案清單。

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![檔案清單](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. 執行下列程式碼以還原 Wide World Importers 資料庫。

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![還原](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. 執行下列程式碼來追蹤還原的狀態。

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. 當還原完成時，請在 [物件總管] 加以檢視。 

## <a name="next-steps"></a>後續步驟

- 若要對備份至 URL 進行疑難排解，請參閱 [SQL Server 備份至 URL 的最佳做法和疑難排解](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)。
- 如需有關應用程式連線選項的概觀，請參閱[將您的應用程式連線至受控執行個體](sql-database-managed-instance-connect-app.md)。
- 若要使用慣用工具或語言進行查詢，請參閱[快速入門：Azure SQL Database 連線和查詢](sql-database-connect-query.md)。
