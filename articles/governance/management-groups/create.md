---
title: 建立管理群組來組織 Azure 資源 - Azure Governance
description: 了解如何使用入口網站、Azure PowerShell 和 Azure CLI 建立 Azure 管理群組來管理多個資源。
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 01bfd10b2f37a7990ab9a1badfcb09422baa391a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342196"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>建立用於資源組織及管理的管理群組

管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../policy/overview.md)和 [Azure 角色型存取控制](../../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。

## <a name="create-a-management-group"></a>建立管理群組

您可以使用入口網站、PowerShell 或 Azure CLI 來建立管理群組。 目前，您無法使用 Resource Manager 範本來建立管理群組。

### <a name="create-in-portal"></a>在入口網站中建立

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務] > [管理群組]。

1. 在主頁面上，選取 [新增管理群組]。

   ![主要群組](./media/main.png)

1. 填寫管理群組識別碼欄位。

   - [管理群組識別碼] 是用來在此管理群組上提交命令的目錄唯一識別碼。 在建立之後無法編輯這個識別碼，因為整個 Azure 系統會使用它來識別此群組。
   - [顯示名稱] 欄位是顯示在 Azure 入口網站內的名稱。 在建立管理群組時，不同的顯示名稱是選擇性欄位，並且可以隨時進行變更。  

   ![建立](./media/create_context_menu.png)  

1. 選取 [ **儲存**]。

### <a name="create-in-powershell"></a>在 PowerShell 中建立

在 PowerShell 內，您需使用 New-AzureRmManagementGroup Cmdlet：

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso'
```

**GroupName** 是要建立的唯一識別碼。 其他命令會使用此識別碼來參考這個群組，且之後無法變更。

如果您需要管理群組在 Azure 入口網站中顯示不同的名稱，可以新增包含字串的 **DisplayName** 參數。 例如，如果您需要建立 GroupName 為 Contoso，以及顯示名稱為 "Contoso Group" 的管理群組，可以使用下列 Cmdlet：

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId 'ContosoTenant'
```

使用 **ParentId** 參數，讓此管理群組在不同的管理下建立。

### <a name="create-in-azure-cli"></a>在 Azure CLI 中建立

您可以在 Azure CLI 上使用 az account management-group create 命令。

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>後續步驟

若要深入了解管理群組，請參閱：

- [建立管理群組以組織 Azure 資源](create.md)
- [如何變更、刪除或管理您的管理群組](manage.md)
- [檢閱 Azure PowerShell 資源模組中的管理群組](https://aka.ms/mgPSdocs)
- [檢閱 REST API 中的管理群組](https://aka.ms/mgAPIdocs)
- [檢閱 Azure CLI 中的管理群組](https://aka.ms/mgclidoc)
