---
title: 使用 Azure Data Factory 將資料複製到 Azure 資料總管或從該處複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料複製到 Azure 資料總管或從該處複製資料。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: orspod
ms.openlocfilehash: 8f2a7a953ce2964645c281d9454a73b0cf1a8ff6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747183"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure 資料總管或從該處複製資料

本文概述如何使用 Azure Data Factory 中的複製活動，將資料複製到 [Azure 資料總管](../data-explorer/data-explorer-overview.md)或從該處複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從任何支援的來源資料存放區複製到 Azure 資料總管。 您也可以將資料從 Azure 資料總管複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md)表格。

>[!NOTE]
>目前，尚未支援使用自我裝載整合執行階段，將資料從 Azure 資料總管複製到內部部署資料存放區，或從內部部署資料存放區複製到 Azure 資料總管。

Azure 資料總管連接器可讓您執行下列作業：

* 使用 Azure Active Directory (Azure AD) 應用程式權杖驗證搭配**服務主體**來複製資料。
* 作為來源時，請使用 KQL (Kusto) 查詢擷取資料。
* 作為接收時，請將資料附加至目的地資料表。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供有關屬性的詳細資料，這些屬性會用來定義 Azure 資料總管連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure 資料總管連結服務支援以下屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設定為 **AzureDataExplorer** | yes |
| endpoint | Azure 資料總管叢集的端點 URL，格式為 `https://<clusterName>.<regionName>.kusto.windows.net `。 | yes |
| 資料庫 | 資料庫名稱。 | yes |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | yes |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | yes |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | yes |

**連結服務屬性範例：**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Azure 資料總管資料集所支援的屬性清單。

若要將資料複製到 Azure 資料總管，請將資料集的 type 屬性設定為 **AzureDataExplorerTable**。

以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設定為 **AzureDataExplorerTable** | yes |
| 資料表 | 連結服務所參考的資料表名稱。 | Yes (接收)：No (來源) |

**資料集屬性範例**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Azure 資料總管來源和接收所支援的屬性清單。

### <a name="azure-data-explorer-as-source"></a>作為來源的 Azure 資料總管

若要從 Azure 資料總管複製資料，請將複製活動來源中的 **type** 屬性設定為 **AzureDataExplorerSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設定為：**AzureDataExplorerSource** | yes |
| query | [KQL 格式](/azure/kusto/query/)中指定的唯讀要求。 使用自訂的 KQL 查詢作為參考。 | yes |
| queryTimeout | 查詢要求逾時之前的等待時間。預設值是 10 分鐘 (00:10:00)；允許的最大值為 1 小時 (01:00:00)。 | 否 |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>作為接收的 Azure 資料總管

若要將資料複製到 Azure 資料總管，請將複製活動接收中的 type 屬性設定為 **AzureDataExplorerSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收的 **type** 屬性必須設定為：**AzureDataExplorerSink** | yes |
| ingestionMappingName | Kusto 資料表上預先建立之 [csv 對應](/azure/kusto/management/mappings#csv-mapping)的名稱。 若要從來源將資料行對應至 Azure 資料總管，您也可以使用複製活動[資料行對應](copy-activity-schema-and-type-mapping.md)。 | 否 |

**範例：**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional csv mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。