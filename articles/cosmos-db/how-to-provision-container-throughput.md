---
title: 在 Azure Cosmos DB 中佈建容器輸送量
description: 了解如何在 Azure Cosmos DB 中佈建資料庫層級的輸送量
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 4df8a12581b5d71a76964ca1e3d40c6c53185f67
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860315"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>在 Azure Cosmos 容器上佈建輸送量

本文說明如何在 Azure Cosmos DB 中佈建容器 (集合、圖表或資料表) 的輸送量。 您可以佈建單一容器的輸送量，或[為資料庫佈建](how-to-provision-database-throughput.md)輸送量，並在資料庫內的容器之間共用。 您可以使用 Azure 入口網站、Azure CLI 或 Azure CosmosDB SDK 來佈建容器的輸送量。

## <a name="provision-throughput-by-using-azure-portal"></a>使用 Azure 入口網站佈建輸送量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos DB 帳戶](create-sql-api-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增集合]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入集合識別碼 (或資料表或圖形)。
   * 輸入分割區索引鍵值，(例如 `/userid`)。
   * 輸入輸送量 (例如 1000 RU)。
   * 選取 [確定] 。

![醒目提示 [新增集合] 的資料總管螢幕擷取畫面](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-azure-cli"></a>使用 Azure CLI 佈建輸送量

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

如果您要針對使用適用於 MongoDB 的 Azure Cosmos DB API 設定的 Azure Cosmos DB 帳戶佈建輸送量，請使用分割區索引鍵路徑的 `/myShardKey`。 如果您要針對為 Cassandra API 設定的 Azure Cosmos DB 帳戶佈建輸送量，請使用分割區索引鍵路徑的 `/myPrimaryKey`。

## <a name="provision-throughput-by-using-net-sdk"></a>使用 .NET SDK 佈建輸送量

> [!Note]
> 使用 SQL API 為 Cassandra API 以外的所有 API 佈建輸送量。

### <a id="dotnet-most"></a>SQL、MongoDB、Gremlin 和資料表 API

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的輸送量佈建：

* [如何佈建資料庫的輸送量](how-to-provision-database-throughput.md)
* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
