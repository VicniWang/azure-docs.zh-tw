---
title: 了解部署順序
description: 了解藍圖經過的生命週期與每個階段的詳細資料。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: bd12aabf0ca8f82261e6b3c677d7306ee46c4171
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308612"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>了解 Azure 藍圖中的部署順序

Azure 藍圖會在處理藍圖指派時使用**排序順序**來判斷資源的建立順序。 本文說明下列概念：

- 使用的預設排序順序
- 如何自訂順序
- 如何處理自訂的順序

在 JSON 範例中有一些變數。您需要使用自己的值取代它們：

- `{YourMG}` - 以您的管理群組名稱取代

## <a name="default-sequencing-order"></a>預設排序順序

若藍圖沒有包含用於部署成品的順序指示詞，或是指示詞為 Null，則會使用下列順序：

- 訂用帳戶層級**角色指派**成品，依成品名稱排序
- 訂用帳戶層級**原則指派**成品，依成品名稱排序
- 訂用帳戶層級 **Azure Resource Manager 範本**成品，依成品名稱排序
- **資源群組**成品 (包含子成品)，依預留位置名稱排序

在每個**資源群組**成品中，下列排序順序會適用於要在該資源群組內建立的成品：

- 資源群組子**角色指派**成品，依成品名稱排序
- 資源群組子**原則指派**成品，依成品名稱排序
- 資源群組子 **Azure Resource Manager 範本**成品，依成品名稱排序

## <a name="customizing-the-sequencing-order"></a>自訂排序順序

撰寫大型藍圖時，可能需要依照特定順序建立資源。 此案例最常見的使用模式便是藍圖包含數個 Azure Resource Manager 範本時。 藍圖會藉由允許定義排序順序來處理此模式。

排序可透過在 JSON 中定義 `dependsOn` 屬性來完成。 只有藍圖 (適用於資源群組) 及成品物件支援此屬性。 `dependsOn` 為成品名稱字串陣列，代表在其建立前必須建立的特定成品。

> [!NOTE]
> **資源群組**成品支援 `dependsOn` 屬性，但不能是任何成品類型的 `dependsOn` 目標。

### <a name="example---blueprint-with-ordered-resource-group"></a>範例 - 使用已排序資源群組

此範例藍圖具備透過宣告 `dependsOn` 值來定義自訂排序順序的資源群組，以及一個標準資源群組。 在此範例中，名為 **assignPolicyTags** 的成品會在 **ordered-rg** 資源群組之前進行處理。 **standard-rg** 會根據預設排序順序進行處理。

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>範例 - 使用自訂順序的成品

此範例是相依於 Azure Resource Manager 範本的原則成品。 根據預設順序，原則成品會在 Azure Resource Manager 範本之前建立。 這順序可讓原則成品等待建立 Azure Resource Manager 範本。

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

## <a name="processing-the-customized-sequence"></a>處理自訂順序

在建立過程期間，會使用拓撲排序來建立藍圖成品的相依性關係圖。 此檢查可確保支援資源群組與成品之間的每個相依性層級。

如果成品相依性宣告為不會改變預設順序，則不會進行任何變更。 有個範例是相依於訂用帳戶層級原則的資源群組。 另一個範例則是相依於資源群組 ’standard-rg’ 子角色指派的資源群組 ’standard-rg’ 子原則指派。 在這兩個案例中，`dependsOn` 皆不會改變預設排序順序，因此不會產生任何變更。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](lifecycle.md) (英文)
- 了解如何使用[靜態和動態參數](parameters.md)
- 了解如何使用[藍圖資源鎖定](resource-locking.md)
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題