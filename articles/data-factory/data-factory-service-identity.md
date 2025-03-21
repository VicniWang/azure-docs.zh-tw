---
title: Azure Data Factory 服務識別 | Microsoft Docs
description: 了解 Azure Data Factory 中的資料處理站服務識別。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 7937836daad5ad299f3e5b7b6b7994ae40a833fd
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446881"
---
# <a name="azure-data-factory-service-identity"></a>Azure Data Factory 服務識別

本文協助您了解資料處理站的服務識別及其運作方式。

## <a name="overview"></a>概觀

建立資料處理站時，服務識別可隨著處理站建立一起建立。 服務識別是向 Azure Active Directory 註冊的受控應用程式，代表這個特定資料處理站。

資料處理站的服務識別有助於下列功能：

- [在 Azure Key Vault 中存放認證](store-credentials-in-key-vault.md)，在此案例中，資料處理站服務識別是用於 Azure Key Vault 驗證。
- 連接器，包括 [Azure Blob 儲存體](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md)、[Azure SQL Database](connector-azure-sql-database.md)，以及 [Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md)。
- [網路活動](control-flow-web-activity.md)。

## <a name="generate-service-identity"></a>產生服務識別

資料處理站服務識別的產生過程如下：

- 透過 **Azure 入口網站或 PowerShell** 建立資料處理站時，一律會自動建立服務識別。
- 透過 **SDK** 建立資料處理站時，只有您在用於建立的處理站物件中指定 "Identity = new FactoryIdentity()" 時，才會建立服務識別。 請參閱 [.NET 快速入門 - 建立資料處理站](quickstart-create-data-factory-dot-net.md#create-a-data-factory)中的範例。
- 透過 **REST API** 建立資料處理站時，只有您在要求本文中指定 "identity" 區段時，才會建立服務識別。 請參閱 [REST 快速入門 - 建立資料處理站](quickstart-create-data-factory-rest-api.md#create-a-data-factory)中的範例。

如果您發現資料處理站沒有與下列[擷取服務識別](#retrieve-service-identity)指示相關聯的服務識別，您可藉由程式設計的方式透過身分識別啟動器更新資料處理站來明確產生一個：

- [使用 PowerShell 產生服務識別](#generate-service-identity-using-powershell)
- [使用 REST API 產生服務識別](#generate-service-identity-using-rest-api)
- 使用 Azure Resource Manager 範本產生服務識別
- [使用 SDK 產生服務識別](#generate-service-identity-using-sdk)

>[!NOTE]
>- 服務識別不能修改。 更新已有服務識別的資料處理站不會有任何影響，服務識別會維持不變。
>- 如果您更新已有服務識別的資料處理站，但未在處理站物件中指定 "identity" 參數，或未在 REST 要求本文中指定 "identity" 區段，您將會收到錯誤。
>- 當您刪除資料處理站，會一併刪除相關聯的服務識別。

### <a name="generate-service-identity-using-powershell"></a>使用 PowerShell 產生服務識別

再次呼叫 **Set-AzureRmDataFactoryV2** 命令，您就會看到新產生的 "Identity" 欄位：

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>使用 REST API 產生服務識別

在要求本文中以 "identity" 區段呼叫下面的 API：

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**要求本文**：新增 "identity": { "type":"SystemAssigned" }。

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**回應**：系統會自動建立服務識別，並據此填入 "identity" 區段。

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-service-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本產生服務識別

**範本**：新增 "identity": { "type":"SystemAssigned" }。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-service-identity-using-sdk"></a>使用 SDK 產生服務識別

以 Identity=new FactoryIdentity() 呼叫資料處理站 create_or_update 函式。 使用 .NET 的範例程式碼：

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>擷取服務識別

您可以從 Azure 入口網站或以程式設計的方式擷取服務識別。 下列各節會顯示一些範例。

>[!TIP]
> 如果您沒有看到服務識別，請藉由更新您的處理站來[產生服務識別](#generate-service-identity)。

### <a name="retrieve-service-identity-using-azure-portal"></a>使用 Azure 入口網站擷取服務識別

您可以從 Azure 入口網站 -> 您的資料處理站 -> 設定 -> 屬性，找到服務識別資訊：

- 服務識別識別碼
- 服務識別租用戶
- **服務識別應用程式識別碼** > 複製此值

![擷取服務識別](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>使用 PowerShell 擷取服務識別

當您取得特定的資料處理站時，將會傳回服務識別主體識別碼和租用戶識別碼，如下所示：

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

複製主體識別碼，然後以主體識別碼作為參數來執行下面的 Azure Active Directory 命令，以取得您可用於授與存取權的 **ApplicationId**：

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>後續步驟
請參閱下列介紹何時及如何使用資料處理站服務識別的相關主題：

- [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)
- [使用 Azure 資源的受控識別驗證，從 Azure Data Lake Store 來回複製資料](connector-azure-data-lake-store.md)

請參閱 [Azure 資源的受控識別概觀](/azure/active-directory/managed-identities-azure-resources/overview)，以了解關於 Azure 資源的受控識別的詳細背景，而資料處理站服務識別會以這類受控識別為基礎。 
