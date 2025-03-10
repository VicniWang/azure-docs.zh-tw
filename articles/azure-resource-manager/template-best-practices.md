---
title: Azure Resource Manager 範本最佳做法
description: 說明用於製作 Azure Resource Manager 範本的建議方法。 提供建議來避免使用範本時的常見問題。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: tomfitz
ms.openlocfilehash: 9b136c73afc08e05694aed99d57139f77466788d
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490374"
---
# <a name="azure-resource-manager-template-best-practices"></a>Azure Resource Manager 範本最佳做法

本文提供有關如何建構 Resource Manager 範本的建議。 這些建議可協助您避免使用範本來部署解決方案時的常見問題。

如需如何管理 Azure 訂用帳戶的建議，請參閱[Azure 企業 Scaffold：規定的訂用帳戶治理](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)。

如需相關建議以了解如何建置適用於所有 Azure 雲端環境的範本，請參閱[開發針對雲端一致性的 Azure Resource Manager 範本](templates-cloud-consistency.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="parameters"></a>參數
本節資訊對您在使用[參數](resource-manager-templates-parameters.md)時會有幫助。

### <a name="general-recommendations-for-parameters"></a>參數的一般建議

* 將參數的使用最小化。 相反地，請對不需要在部署期間指定的屬性使用變數或常值。

* 參數名稱使用駝峰式大小寫。

* 針對會隨著環境 (例如 SKU、大小或容量) 而有所不同的設定使用參數。

* 針對希望能方便識別而指定的資源名稱使用參數。

* 在中繼資料中提供每個參數的描述：

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* 針對不敏感的參數定義預設值。 藉由指定預設值，您可以更輕鬆地部署範本，且範本的使用者可以看到適當值的範例。 參數的預設值皆必須對預設部署組態中的所有使用者有效。 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* 若要指定選擇性參數，請不要使用空字串作為預設值。 相反地，請使用常值或語言運算式來建構值。

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* 針對資源類型的 API 版本，請勿使用參數。 資源屬性和值可能會隨版本號碼而不同。 將 API 版本設定為參數時，程式碼編輯器中的 Intellisense 會無法判斷正確的結構描述。 請改為將 API 版本硬式編碼在範本中。

* 請謹慎使用 `allowedValues`。 只有當您必須確保允許的選項中不會包含某些值時才可使用。 如果您廣泛使用 `allowedValues`，可能會因為未能讓清單保持最新狀態而導致有效的部署遭到封鎖。

* 當範本中的參數名稱與 PowerShell 部署命令中的參數相符時，Resource Manager 會在範本參數加上後置詞 **FromTemplate** 以避免命名衝突。 例如，如果您在範本中包含名為 **ResourceGroupName** 的參數，它會與 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 部署期間，系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。

### <a name="security-recommendations-for-parameters"></a>參數的安全性建議

* 使用者名稱和密碼 (或祕密) 請一律使用參數。

* 所有密碼和祕密都要使用 `securestring`。 如果您在 JSON 物件中傳遞敏感資料，請使用 `secureObject` 類型。 部署資源後，便無法讀取具有安全字串或安全物件類型的範本參數。 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* 針對需要 `secureString` 類型的使用者名稱、密碼或任何值，請勿提供預設值。

* 針對會增加應用程式受攻擊面的屬性，請勿提供預設值。

### <a name="location-recommendations-for-parameters"></a>參數的位置建議

* 請使用參數來指定資源的位置，並將預設值設定為 `resourceGroup().location`。 提供位置參數可讓範本的使用者指定其有權作為部署目的地的位置。

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* 請勿針對位置參數指定 `allowedValues`。 您指定的位置可能無法在所有雲端中使用。

* 針對可能位於相同位置的資源，請使用位置參數值。 此方法可以降低系統要求使用者提供位置資訊的次數。

* 針對無法在所有位置中使用的資源，請使用不同參數或指定常值的位置值。

## <a name="variables"></a>變數

當您使用[變數](resource-manager-templates-variables.md)時，下列資訊可能會很有幫助︰

* 您需要在範本中使用一次以上的值，才使用變數。 如果值只會使用一次，硬式編碼值會讓您的範本較容易看懂。

* 針對用複雜方式排列範本函式所建構的值，請使用變數。 當複雜的運算式只以變數形式顯示時，範本會較容易看懂。

* 針對資源上的 `apiVersion`，請勿使用變數。 API 版本會決定資源的結構描述。 變更版本往往就必須變更資源的屬性。

* 您不能在範本的 **variables** 區段使用 [reference](resource-group-template-functions-resource.md#reference) 函式。 **reference** 函式的值是從資源的執行階段狀態所衍生。 不過，將範本初始剖析時，會將變數加以解析。 請直接在範本的 **resources** 或 **outputs** 區段中，建構需要 **reference** 函式的值。

* 包含變數以用於必須是唯一的資源名稱。

* 請[在變數中使用複製迴圈](resource-group-create-multiple.md#variable-iteration)來建立重複的 JSON 物件模式。

* 請移除未使用的變數。

## <a name="resource-dependencies"></a>資源相依性

在決定要設定哪些[相依性](resource-group-define-dependencies.md)時，請使用下列指導方針︰

* 使用 **reference** 函式並傳入資源名稱，以便在必須共用屬性的資源之間設定隱含相依性。 當您已經定義隱含的相依性時，請勿新增明確的 `dependsOn` 元素。 這種方法可減少產生不必要相依性的風險。

* 設定子資源為相依於其父資源。

* 設定為[false 的資源](resource-manager-templates-resources.md#condition)，會自動從相依性順序中移除。 請在假定一律會部署該資源的情況下設定相依性。

* 讓相依性重疊顯示，而不需要明確設定它們。 例如，您的虛擬機器相依於虛擬網路介面，而虛擬網路介面相依於虛擬網路和公用 IP 位址。 因此，會在所有三個資源之後部署虛擬機器，但不會明確地將虛擬機器設定為相依於所有三個資源。 這種方法可釐清相依性順序，而且稍後可以比較容易變更範本。

* 如果部署之前可以決定值，請嘗試部署沒有相依性的資源。 例如，如果設定值需要另一個資源的名稱，您可能就不需要相依性。 本指引並非一律有效，因為某些資源會確認其他資源是否存在。 如果您收到錯誤，請加入相依性。

## <a name="resources"></a>資源

當您使用[資源](resource-manager-templates-resources.md)時，下列資訊可能會很有幫助︰

* 針對範本中的每個資源指定 **comments**，協助其他參與者了解資源的用途：
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* 如果您使用範本中的公用端點 (例如 Azure Blob 儲存體公用端點)，請勿將命名空間硬式編碼。 使用 **reference** 函式，動態擷取命名空間。 您可以使用此方法可將範本部署到不同的公用命名空間環境中，而不需要將範本中的端點手動變更。 將 API 版本設定成與您在範本中用於儲存體帳戶相同的版本：
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   如果將該儲存體帳戶部署在您要建立的相同範本中，當您參考資源時，即不需要指定提供者命名空間。 下列範例顯示簡化的語法：
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   如果您範本中其他的值是使用公用命名空間進行設定，請變更這些值以反映相同的 **reference** 函式。 例如，您可以設定虛擬機器診斷設定檔的 **storageUri** 屬性：
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   您也可以參考不同資源群組中現有的儲存體帳戶：

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* 只有在應用程式要求時，才將公用 IP 位址指派給虛擬機器。 若要連線至虛擬機器 (VM) 進行偵錯，或要進行管理或系統管理，請使用輸入 NAT 規則、虛擬網路閘道或 Jumpbox。
   
     如需有關如何連線至虛擬機器的詳細資訊，請參閱︰
   
   * [在 Azure 中執行多層式架構的 VM](../guidance/guidance-compute-n-tier-vm.md)
   * [在 Azure Resource Manager 中設定 VM 的 WinRM 存取](../virtual-machines/windows/winrm.md)
   * [允許使用 Azure 入口網站從外部存取您的 VM](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [允許使用 PowerShell 從外部存取您的 VM](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [允許使用 Azure CLI 從外部存取您的 Linux VM](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* 公用 IP 位址的 **domainNameLabel** 屬性必須是唯一的。 **domainNameLabel** 值的長度必須介於 3 到 63 個字元之間，還要遵循這個規則運算式指定的規則：`^[a-z][a-z0-9-]{1,61}[a-z0-9]$`。 **uniqueString** 函式會產生長度為 13 個字元的字串，因此 **dnsPrefixString** 參數會限制為 50 個字元：

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* 將密碼新增至自訂指令碼擴充功能時，使用 **protectedSettings** 屬性中的 **commandToExecute** 屬性。
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > 為了確保作為參數傳遞至 VM 和擴充功能的祕密會經過加密，請使用相關擴充功能的 **protectedSettings** 屬性。
   > 
   > 

## <a name="outputs"></a>輸出

如果您使用範本來建立公用 IP 位址，請包含 [outputs 區段](resource-manager-templates-outputs.md)，以傳回 IP 位址和完整網域名稱 (FQDN) 的詳細資料。 您可以使用輸出值，輕鬆在部署後擷取公用 IP 位址和 FQDN 的相關詳細資料。

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>後續步驟

* 如需 Resource Manager 範本檔案結構的相關資訊，請參閱[了解 Azure Resource Manager 範本的結構和語法](resource-group-authoring-templates.md)。
* 如需相關建議以了解如何建置適用於所有 Azure 雲端環境的範本，請參閱[開發針對雲端一致性的 Azure Resource Manager 範本](templates-cloud-consistency.md)。
