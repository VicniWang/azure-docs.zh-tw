---
title: 實作異地分散的 Azure SQL Database 解決方案| Microsoft Docs
description: 了解如何設定您的 Azure SQL Database 和應用程式來容錯移轉至複寫資料庫，並測試容錯移轉。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/10/2019
ms.openlocfilehash: 11c1f34176e7852806464781e80d6dc0fd5345a4
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750336"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>教學課程：實作異地分散資料庫

設定 Azure SQL Database 和應用程式來容錯移轉到遠端區域，並測試您的容錯移轉計畫。 您會了解如何：

> [!div class="checklist"]
> - 建立[容錯移轉群組](sql-database-auto-failover-group.md)
> - 執行 Java 應用程式以查詢 Azure SQL Database
> - 測試容錯移轉

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您已安裝下列項目：

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Azure SQL Database。 若要建立一次使用，
  - [入口網站](sql-database-single-database-get-started.md)
  - [CLI](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > 教學課程使用 *AdventureWorksLT* 範例資料庫。

- Java 和 Maven，請參閱[使用 SQL Server 建立應用程式](https://www.microsoft.com/sql-server/developer-get-started/)，反白顯示 **Java** 並選取您的環境，然後遵循步驟進行。

> [!IMPORTANT]
> 務必設定防火牆規則，使用您在本教學課程中執行步驟的電腦所用的公用 IP 位址。 資料庫層級防火牆規則將會自動複寫到次要伺服器。
>
> 如需資訊，請參閱[建立資料庫層級防火牆規則](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)，或者，若要判斷對於電腦的伺服器層級防火牆規則所用的 IP 位址，請參閱[建立伺服器層級防火牆](sql-database-server-level-firewall-rule.md)。  

## <a name="create-a-failover-group"></a>建立容錯移轉群組

使用 Azure PowerShell，在現有的 Azure SQL 伺服器與另一個區域的新 Azure SQL 伺服器之間建立[容錯移轉群組](sql-database-auto-failover-group.md)。 然後將範例資料庫新增到容錯移轉群組。

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

若要建立容錯移轉群組，請執行下列指令碼：

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzureRMSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzureRmSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzureRmSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

在 Azure 入口網站中也可以變更異地複寫設定，方法是選取您的資料庫，然後選取 [設定] > [異地複寫]。

![異地複寫設定](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>執行範例專案

1. 在主控台中，使用下列命令建立 Maven 專案：

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. 輸入 **Y**，然後按 **ENTER**。

1. 將目錄變更為新的專案。

   ```bash
   cd SqlDbSample
   ```

1. 使用您慣用的編輯器，開啟專案資料夾中的 *pom.xml* 檔案。

1. 新增下列 `dependency` 區段，以便新增 Microsoft JDBC Driver for SQL Server 相依性。 相依性必須貼入較大的 `dependencies` 區段內。

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. 在 `dependencies` 區段之後新增 `properties` 區段來指定 Java 版本：

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 在 `properties` 區段之後新增 `build` 區段來支援資訊清單檔：

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. 儲存並關閉 *pom.xml* 檔案。

1. 開啟位於 ..\SqlDbSample\src\main\java\com\sqldbsamples 中的 *App.java* 檔案，並將內容取代為下列內容：

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. 儲存並關閉 *App.java* 檔案。

1. 在命令主控台中，執行下列命令：

   ```bash
   mvn package
   ```

1. 啟動應用程式執行大約 1 小時，直到手動停止為止，以便您有時間執行容錯移轉測試。

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>測試容錯移轉

執行下列指令碼，以模擬容錯移轉，並觀察應用程式結果。 請注意，在資料庫移轉期間，一些插入和選取會失敗。

您也可以使用下列命令，在測試期間檢查災害復原伺服器的角色：

   ```powershell
   (Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

若要測試容錯移轉：

1. 啟動容錯移轉群組的手動容錯移轉：

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. 將容錯移轉群組還原至主要伺服器：

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您要設定 Azure SQL Database 和應用程式來容錯移轉到遠端區域，然後測試容錯移轉計畫。 您已了解如何︰

> [!div class="checklist"]
> - 建立異地複寫容錯移轉群組
> - 執行 Java 應用程式以查詢 Azure SQL Database
> - 測試容錯移轉

請前進到下一個關於如何使用 DMS 進行移轉的教學課程。

> [!div class="nextstepaction"]
> [使用 DMS 將 SQL Server 遷移至 Azure SQL Database 受控執行個體](../dms/tutorial-sql-server-to-managed-instance.md)
