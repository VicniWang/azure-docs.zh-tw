---
title: Azure Analysis Services 中支援的資料來源 | Microsoft Docs
description: 說明 Azure Analysis Services 中資料模型支援的資料來源。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c7b3695e5cb43a9c8f659a3ac1baddab51526b2d
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893726"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services 中支援的資料來源

在 Visual Studio 「取得資料」或「匯入精靈」中顯示的資料來源和連接器，也會在 Azure Analysis Services 和 SQL Server Analysis Services 中顯示。 不過，這不表示 Azure Analysis Services 支援所有顯示的資料來源和連接器。 您可以連線的資料來源類型取決於多個因素，例如模型相容性層級、可用的資料連接器、驗證類型、提供者，以及內部部署資料閘道支援。 

## <a name="azure-data-sources"></a>Azure 資料來源

|資料來源  |記憶體內  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   yes      |    yes      |
|Azure SQL 資料倉儲     |   yes      |   yes       |
|Azure Blob 儲存體<sup>[1](#tab1400a)</sup>     |   yes       |    否      |
|Azure 資料表儲存體<sup>[1](#tab1400a)</sup>    |   yes       |    否      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  yes        |  否        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   yes       |    否      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     yes     |   否       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>、<sup>[3](#databricks)</sup>     |   yes       |   否       |
||||

<a name="tab1400a">1</a> - 僅限 Tabular 1400 和更高模型。   
<a name="azsqlmanaged">2</a> - 支援 Azure SQL Database 受控執行個體。 因為受控執行個體在具有私人 IP 位址的 Azure VNet 中執行，所以需要內部部署資料閘道。   
<a name="databricks">3</a> - 目前不支援使用 Spark 連接器的 Azure Databricks。   
<a name="gen2">4</a> - 目前不支援 ADLS Gen2。


**提供者**   
連線至 Azure 資料來源的記憶體內部和 DirectQuery 模型會使用 .NET Framework Data Provider for SQL Server。

## <a name="on-premises-data-sources"></a>內部部署資料來源

從 Azure AS 伺服器連線至內部部署資料來源需要內部部署閘道。 使用閘道時，需要 64 位元的提供者。

### <a name="in-memory-and-directquery"></a>記憶體內部和 DirectQuery

|資料來源 | 記憶體內部提供者 | DirectQuery 提供者 |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0、Microsoft OLE DB Provider for SQL Server、.NET Framework Data Provider for SQL Server | .NET Framework Data Provider for SQL Server |
| SQL Server 資料倉儲 |SQL Server Native Client 11.0、Microsoft OLE DB Provider for SQL Server、.NET Framework Data Provider for SQL Server | .NET Framework Data Provider for SQL Server |
| Oracle |Microsoft OLE DB Provider for Oracle、Oracle Data Provider for .NET |Oracle Data Provider for .NET | |
| Teradata |OLE DB Provider for Teradata、Teradata Data Provider for .NET |Teradata Data Provider for .NET | |
| | | |

### <a name="in-memory-only"></a>僅限記憶體內部

|資料來源  |  
|---------|---------|
|Access 資料庫     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|分析平台系統     |  
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excel 活頁簿     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|資料夾<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (搶鮮版 (Beta)) |
|JSON 文件<sup>[1](#tab1400b)</sup>     |  
|二進位檔中的程式行<sup>[1](#tab1400b)</sup>     | 
|MySQL Database     | 
|OData 摘要<sup>[1](#tab1400b)</sup>     |  
|ODBC 查詢     | 
|OLE DB     |   
|Postgre SQL Database<sup>[1](#tab1400b)</sup>    | 
|Salesforce 物件<sup>[1](#tab1400b)</sup> |  
|Salesforce 報告<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint<sup>[1](#tab1400b)</sup>     |   
|Sybase 資料庫     |  
|XML 表格<sup>[1](#tab1400b)</sup>    |  
|||
 
<a name="tab1400b">1</a> 僅限 Tabular 1400 和更高模型。

## <a name="specifying-a-different-provider"></a>指定不同提供者

Azure Analysis Services 中的資料模型連線至某些資料來源時，可能需要不同的資料提供者。 在某些情況下，使用原生提供者 (例如 SQL Server Native Client (SQLNCLI11)) 連接到資料來源的表格式模型可能會傳回錯誤。 如果使用 SQLOLEDB 以外的原生提供者，您可能會看到錯誤訊息：**未註冊 'SQLNCLI11.1' 提供者**。 或者，如果您的 DirectQuery 模型已與內部部署資料來源連線，而您使用了原生提供者，則可能會看見錯誤訊息：**Error creating OLE DB row set.Incorrect syntax near 'LIMIT'”** (建立 OLE DB 列集時發生錯誤。'LIMIT' 附近的語法錯誤)。

將內部部署 SQL Server Analysis Services 表格式模型移轉至 Azure Analysis Services 時，可能需要變更提供者。

**指定提供者**

1. 在 SSDT > **Tabular Model Explorer** (表格式模型總管)  >  [資料來源] 中，以滑鼠右鍵按一下資料來源連線，然後按一下 [編輯資料來源]。
2. 在 [編輯連線] 中按一下 [進階]，以開啟 [進階屬性] 視窗。
3. 在[設定進階屬性]  >  [提供者] 中，然後選取適當的提供者。

## <a name="impersonation"></a>模擬
在某些情況下，可能需要指定不同的模擬帳戶。 模擬帳戶可以在 Visual Studio SSDT (SSDT) 或 SSMS 中指定。

對於內部部署資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。
* 如果使用 Windows 驗證，請設定 Windows 使用者/密碼。 對於 SQL Server，In-Memory 資料模型僅支援具有特定模擬帳戶的 Windows 驗證。

對於雲端資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。

## <a name="next-steps"></a>後續步驟
[內部部署閘道](analysis-services-gateway.md)   
[管理您的伺服器](analysis-services-manage.md)   

