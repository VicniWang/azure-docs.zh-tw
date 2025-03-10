---
title: 使用 Azure Resource Manager 範本匯入 SQL BACPAC 檔案 | Microsoft Docs
description: 了解如何使用 SQL Database 擴充功能透過 Azure Resource Manager 範本匯入 SQL BACPAC 檔案。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6c146a99be369f92a72717ad1ec4b79bddafe28c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819321"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>教學課程：使用 Azure Resource Manager 範本匯入 SQL BACPAC 檔案

了解如何使用 Azure SQL Database 擴充功能透過 Azure Resource Manager 範本匯入 BACPAC 檔案。 除了完成部署所需的主要範本檔案以外，部署成品可以是任何檔案。 BACPAC 檔案是成品。 在本教學課程中，您將建立一個範本，以部署 Azure SQL Server、SQL Database，以及匯入 BACPAC 檔案。 如需使用 Azure Resource Manager 範本來部署 Azure 虛擬機器擴充功能的相關資訊，請參閱 [# 教學課程：使用 Azure Resource Manager 範本部署虛擬機器擴充功能](./resource-manager-tutorial-deploy-vm-extensions.md)。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 準備 BACPAC 檔案
> * 開啟快速入門範本
> * 編輯範本
> * 部署範本
> * 驗證部署

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* [Visual Studio Code](https://code.visualstudio.com/) 搭配 Resource Manager Tools 擴充功能。 請參閱[安裝延伸模組](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。
* 為了提高安全性，請使用為 SQL Server 系統管理員帳戶產生的密碼。 以下是用於產生密碼的範例：

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault 的設計訴求是保護加密金鑰和其他祕密。 如需詳細資訊，請參閱[教學課程：在 Resource Manager 範本部署中整合 Azure Key Vault](./resource-manager-tutorial-use-key-vault.md)。 我們也建議您每三個月更新一次密碼。

## <a name="prepare-a-bacpac-file"></a>準備 BACPAC 檔案

BACPAC 檔案可在[具有公用存取權的 Azure 儲存體帳戶](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac)上共用。 若要自行建立，請參閱[將 Azure SQL 資料庫匯出到 BACPAC 檔案](../sql-database/sql-database-export.md)。 如果您選擇將檔案發佈到自己的位置，您稍後必須在本教學課程中更新範本。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

Azure 快速入門範本是 Resource Manager 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本教學課程中使用的範本名為[部署具有威脅偵測功能的 Azure SQL Server](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/)。

1. 在 Visual Studio Code 中，選取 [檔案]>[開啟檔案]。
2. 在 [檔案名稱] 中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json
    ```
3. 選取 [開啟] 以開啟檔案。

    範本中定義了三項資源：

    * `Microsoft.Sql/servers` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers)。
    * `Microsoft.SQL/servers/securityAlertPolicies` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies)。
    * `Microsoft.SQL.servers/databases` 。  請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)。

    自訂範本之前，最好能初步了解範本。
4. 選取 [檔案]>[另存新檔]，以名稱 **azuredeploy.json** 將檔案的複本儲存至您的本機電腦。

## <a name="edit-the-template"></a>編輯範本

將兩個額外的資源新增至範本。

* 若要讓 SQL 資料庫擴充功能匯入 BACPAC 檔案，您必須允許存取 Azure 服務。 將下列 JSON 新增至 SQL 伺服器定義：

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2015-05-01-preview",
        "dependsOn": [
            "[variables('databaseServerName')]"
        ],
        "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
        }
    }
    ```

    範本應顯示如下：

    ![Azure Resource Manager 部署 SQL 擴充功能 BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

* 使用下列 JSON，將 SQL Database 擴充功能資源新增至資料庫定義：

    ```json
    "resources": [
        {
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    範本應顯示如下：

    ![Azure Resource Manager 部署 SQL 擴充功能 BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    若要了解資源定義，請參閱 [SQL Database 擴充功能參考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions)。 以下是部分重要元素：

    * **dependsOn**：在 SQL 資料庫建立後，才可建立擴充功能資源。
    * **storageKeyType**：要使用的儲存體金鑰類型。 其值可以是 `StorageAccessKey` 或 `SharedAccessKey`。 由於 BACPAC 檔案可在具有公用存取權的 Azure 儲存體帳戶上共用，因此在此處會使用 `SharedAccessKey'。
    * **storageKey**：要使用的儲存體金鑰。 如果儲存體金鑰類型為 SharedAccessKey，則前面必須加上 "?"。
    * **storageUri**：要使用的儲存體 URI。 如果您選擇不使用提供的 BACPAC 檔案，則必須更新值。
    * **administratorLoginPassword**：SQL 系統管理員的密碼。 使用所產生的密碼。 請參閱[必要條件](#prerequisites)。

## <a name="deploy-the-template"></a>部署範本

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

請參閱[部署範本](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)一節，以了解部署程序。 改用下列 PowerShell 部署指令碼：

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json"
```

使用所產生的密碼。 請參閱[必要條件](#prerequisites)。

## <a name="verify-the-deployment"></a>驗證部署

在入口網站中，請從新部署的資源群組中選取 SQL 資料庫。 選取 [查詢編輯器 (預覽)]，然後輸入系統管理員認證。 您應該會看到兩個已匯入資料庫中的資料表：

![Azure Resource Manager 部署 SQL 擴充功能 BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已部署 Azure SQL Server、SQL Database 並匯入 BACPAC 檔案。 BACPAC 檔案會儲存在 Azure 儲存體帳戶中。 任何具有 URL 的人員都可以存取該檔案。 若要了解如何保護 BACPAC 檔案 (成品)，請參閱

> [!div class="nextstepaction"]
> [保護成品](./resource-manager-tutorial-secure-artifacts.md)
