---
title: 使用 PowerShell 在 Azure Data Factory 中建立共用的自我裝載整合執行階段 | Microsoft Docs
description: 了解如何在 Azure Data Factory 中建立共用的自我裝載整合執行階段，讓多個資料處理站都能存取該整合執行階段。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: 76b0d1728b46834270e9a5b53709de62b4a8b3fa
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429373"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>使用 PowerShell 在 Azure Data Factory 中建立共用的自我裝載整合執行階段

本逐步指南說明如何使用 Azure PowerShell 在 Azure Data Factory 中建立共用的自我裝載整合執行階段。 然後您可以在其他資料處理站中，使用該共用的自我裝載整合執行階段。 在本教學課程中，您會執行下列步驟： 

1. 建立資料處理站。 
1. 建立自我裝載整合執行階段。
1. 與其他資料處理站共用該自我裝載整合執行階段。
1. 建立連結的整合執行階段。
1. 撤銷共用。

## <a name="prerequisites"></a>必要條件 

- **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。 

- **Azure PowerShell**(英文)。 請依照[使用 PowerShellGet 在 Windows 上安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.11.0) 中的指示操作。 您會使用 PowerShell 來執行指令碼，以建立可與其他資料處理站共用的自我裝載整合執行階段。 

> [!NOTE]  
> 如需目前可取得 Data Factory 的 Azure 區域，請在 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory) 上選取您感興趣的區域。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 Windows PowerShell 整合式指令碼環境 (ISE)。

1. 建立變數。 複製並貼上下列指令碼。 請將變數 (例如 **SubscriptionName** 和 **ResourceGroupName**) 取代為實際值： 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. 登入並選取訂用帳戶。 將下列程式碼新增至指令碼，以登入並選取 Azure 訂用帳戶：

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. 建立資源群組和資料處理站。

    > [!NOTE]  
    > 此為選用步驟。 如果您已經有資料處理站，請略過此步驟。 

    使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.11.0) 命令建立 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 下列範例會在 WestEurope 位置建立名為 `myResourceGroup` 的資源群組： 

    ```powershell
    New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    執行下列命令來建立資料處理站： 

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>建立自我裝載整合執行階段

> [!NOTE]  
> 此為選用步驟。 如果您已經有要與其他資料處理站共用的自我裝載整合執行階段，請略過此步驟。

執行下列命令來建立自我裝載整合執行階段：

```powershell
$SharedIR = Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>取得整合執行階段驗證金鑰並註冊節點

執行下列命令，以取得自我裝載整合執行階段的驗證金鑰：

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

回應會包含此自我裝載整合執行階段的驗證金鑰。 當您註冊整合執行階段節點時，會使用此金鑰。

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>安裝和註冊自我裝載整合執行階段

1. 從 [Azure Data Factory Integration Runtime](https://aka.ms/dmg) 下載自我裝載整合執行階段安裝程式。

2. 執行安裝程式，以在本機電腦上安裝自我裝載整合。

3. 使用您在上一步中擷取的驗證金鑰，註冊新的自我裝載整合。

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>與另一個資料處理站共用該自我裝載整合執行階段

### <a name="create-another-data-factory"></a>建立另一個資料處理站

> [!NOTE]  
> 此為選用步驟。 如果您已有要共用的資料處理站，請略過此步驟。

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>授與權限

將存取您建立並註冊的自我裝載整合執行階段所需的權限授與資料處理站。

> [!IMPORTANT]  
> 請勿略過此步驟！

```powershell
New-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>建立連結的自我裝載的整合執行階段

執行下列命令來建立連結的自我裝載整合執行階段：

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

現在，您可以在任何已連結的服務中，使用此連結的整合執行階段。 連結的整合執行階段是使用共用的整合執行階段來執行活動。

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>將整合執行階段共用從資料處理站撤銷

若要撤銷資料處理站對共用整合執行階段的存取權，請執行下列命令：

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

若要移除現有連結的整合執行階段，請對共用整合執行階段執行下列命令：

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>後續步驟

- 檢閱 [Azure Data Factory 中的整合執行階段概念](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)。

- 了解如何[在 Azure 入口網站中建立自我裝載的整合執行階段](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)。
