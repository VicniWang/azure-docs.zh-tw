---
title: 使用 Azure PowerShell 建立 Log Analytics 工作區 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 建立 Log Analytics 工作區，以從您的雲端和內部部署環境啟用管理解決方案和資料收集。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: 073ce3075f6c14230ad76f70feb8d01d17b4e2e0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005466"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>使用 Azure PowerShell 建立 Log Analytics 工作區

Azure PowerShell 模組用於從 PowerShell 命令列或在指令碼中建立和管理 Azure 資源。 本快速入門示範如何使用 Azure PowerShell 模組來部署「Azure 監視器」中的 Log Analytics 工作區。 Log Analytics 工作區是用於「Azure 監視器」記錄資料的獨特環境。 每個工作區都有自己的資料存放庫與設定，而且資料來源和解決方案會設定為將其資料儲存在特定的工作區中。 如果您想從下列來源收集資料，就必須要有 Log Analytics 工作區：

* 訂用帳戶中的 Azure 資源  
* System Center Operations Manager 監視的內部部署電腦  
* 來自 System Center Configuration Manager 的裝置集合  
* Azure 儲存體的診斷或記錄資料  
 
針對其他來源，例如環境中的 Azure VM 和 Windows 或 Linux VM，請參閱下列主題：

* [從 Azure 虛擬機器收集資料](../learn/quick-collect-azurevm.md)
* [從混合式 Linux 電腦收集資料](../learn/quick-collect-linux-computer.md)
* [從混合式 Windows 電腦收集資料](quick-collect-windows-computer.md)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，則在執行本教學課程時，必須使用 Azure PowerShell 模組 5.7.0 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/azurerm/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzureRmAccount` 以建立與 Azure 的連線。

## <a name="create-a-workspace"></a>建立工作區
使用 [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) 建立工作區。 下列範例會在 *eastus* 位置的資源群組 *Lab* 中，使用 Resource Manager 範本從您的本機電腦建立名為 *TestWorkspace* 的工作區。 JSON 範本會設定為只提示您輸入工作區的名稱，並針對您環境中可能作為標準組態使用的其他參數，指定預設值。 

下列參數會設定預設值：

* 位置 - 預設為美國東部
* SKU - 預設為在 2018 年 4 月定價模型中發行的全新每 GB 定價層

>[!WARNING]
>如果在已選擇加入 2018 年 4 月全新定價模型的訂用帳戶中建立或設定 Log Analytics 工作區，則唯一有效的 Log Analytics 定價層是 **PerGB2018**。 
>

### <a name="create-and-deploy-template"></a>建立和部署範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. 編輯範本以符合您的需求。  檢閱 [Microsoft.OperationalInsights/workspaces 範本](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces)參考，以了解支援哪些屬性和值。 
3. 將此檔案儲存為本機資料夾的 deploylaworkspacetemplate.json。   
4. 您已準備好部署此範本。 從包含範本的資料夾使用下列命令：

    ```powershell
        New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

部署需要幾分鐘的時間才能完成。 完成後，您會看到類似下列包含結果的訊息：

![部署完成時的範例結果](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>後續步驟
有了可用的工作區之後，您可以設定監視遙測的集合、執行記錄搜尋以分析該資料，並且新增管理解決方案，以提供額外的資料和分析深入解析。  

* 若要能夠使用「Azure 診斷」或 Azure 儲存體從 Azure 資源收集資料，請參閱[收集 Azure 服務的記錄和計量以在 Azure 監視器中使用](../platform/collect-azure-metrics-logs.md)。  
* [新增 System Center Operations Manager 作為資料來源](../platform/om-agents.md)，以從會報告 Operations Manager 管理群組的代理程式收集資料，並且將其儲存在 Log Analytics 工作區中。  
* 連線 [Configuration Manager](../platform/collect-sccm.md) 以匯入階層中集合成員的電腦。  
* 檢閱可用的[監視解決方案](../insights/solutions.md)，以及如何從您的工作區新增或移除解決方案。
