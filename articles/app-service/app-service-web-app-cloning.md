---
title: 使用 PowerShell 複製應用程式 - Azure App Service
description: 了解如何使用 PowerShell，將您的 App Service 應用程式複製到新的應用程式。
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 2a28409120bac13ea7d288c7fc41f7154c003388
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106251"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>使用 PowerShell 複製 Azure App Service App

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

隨著 Microsoft Azure PowerShell 1.1.0 版的發行，`New-AzWebApp` 中新增了一個新選項，可讓您將現有的 App Service 應用程式複製到不同區域或相同區域中新建立的應用程式。 此選項可讓客戶輕鬆且快速地跨不同區域部署許多應用程式。

應用程式複製目前僅支援 Premium 層 App Service 方案。 新功能會使用與 App Service 備份功能相同的限制，請參閱[在 Azure App Service 中備份應用程式](manage-backup.md)。

## <a name="cloning-an-existing-app"></a>複製現有的應用程式
案例：您想要將位於美國中南部區域的現有應用程式內容，複製到位於美國中北部區域的新應用程式。 使用 Azure Resource Manager 版本的 PowerShell Cmdlet 並搭配 `-SourceWebApp` 選項來建立新的應用程式，即可實現此目的。

若知道包含來源應用程式的資源群組名稱，您就能使用下列 PowerShell 命令來取得來源應用程式的資訊 (在此例中名為 `source-webapp`)：

```PowerShell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

若要建立新的 App Service 方案，您可以使用 `New-AzAppServicePlan` 命令，如下列範例所示。

```PowerShell
New-AzAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

使用 `New-AzWebApp` 命令，就能在美國中北部區域建立新的應用程式，並將它繫結到現有的進階層 App Service 方案。 此外，您可以使用與來源應用程式相同的資源群組，也可以定義新的資源群組，如下列命令所示：

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

若要複製現有的應用程式 (包括所有相關聯的部署位置)，您必須使用 `IncludeSourceWebAppSlots` 參數。 下列 PowerShell 命令示範如何搭配 `New-AzWebApp` 命令使用該參數：

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

若要複製相同區域內的現有應用程式，您必須在相同區域中建立新的資源群組和新的 App Service 方案，然後使用下列 PowerShell 命令來複製應用程式：

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>複製現有應用程式至 App Service 環境
案例：您想要將位於美國中南部區域的現有應用程式內容，複製到現有 App Service 環境 (ASE) 的新應用程式中。

若知道包含來源應用程式的資源群組名稱，您就能使用下列 PowerShell 命令來取得來源應用程式的資訊 (在此例中名為 `source-webapp`)：

```PowerShell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

若知道 ASE 的名稱和 ASE 所屬的資源群組名稱，您就能在現有 ASE 中建立新的應用程式，如下列命令所示：

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

由於舊版因素，因此需有 `Location` 參數，但在 ASE 中建立應用程式時，則會忽略此參數。 

## <a name="cloning-an-existing-app-slot"></a>複製現有的應用程式位置
案例：您想要將應用程式的現有部署位置複製到新的應用程式或新位置。 新的應用程式可以位於與原始應用程式位置相同的區域或不同區域中。

若知道包含來源應用程式的資源群組名稱，您就能使用下列 PowerShell 命令，來取得繫結至 `source-app` 之來源應用程式位置的資訊 (在此例中名為 `source-appslot`)：

```PowerShell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

下列命令示範如何建立從來源應用程式至新應用程式的複製作業：

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>在複製應用程式時設定流量管理員
建立多區域的應用程式並將 Azure 流量管理員設定為將流量路由傳送到這些所有應用程式，是確保客戶的應用程式具有高可用性的重要案例。 複製現有應用程式時，您可以選擇將這兩個應用程式連線到新的流量管理員設定檔或現有設定檔。 只支援 Azure Resource Manager 版本的流量管理員。

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>在複製應用程式時建立新流量管理員設定檔
案例：您想要將應用程式複製到另一個區域，同時設定包含這兩個應用程式的 Azure Resource Manager 流量管理員設定檔。 下列命令示範如何建立從來源應用程式至新應用程式的複製作業，同時設定新的流量管理員設定檔：

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>將新複製的應用程式新增至現有的流量管理員設定檔
案例：您已經擁有 Azure Resource Manager 流量管理員設定檔，如今想要將這兩個應用程式新增為端點。 若要這麼做，您必須先組合現有的流量管理員設定檔識別碼。 您需要訂用帳戶識別碼、資源群組名稱和現有流量管理員設定檔名稱。

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

擁有流量管理員識別碼之後，下列命令示範如何建立從來源應用程式至新應用程式的複製作業，同時將它們新增至現有的流量管理員設定檔：

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>目前的限制
以下是已知的應用程式複製限制：

* 不會複製自動調整設定
* 不會複製備份排程設定
* 不會複製 VNET 設定
* 不會自動在目的地應用程式上設定 App Insights
* 不會複製簡單驗證設定
* 不會複製 Kudu 延伸模組
* 不會複製 TiP 規則
* 不會複製資料庫內容
* 如果複製到不同縮放單位，輸出 IP 位址將會變更

### <a name="references"></a>參考
* [App Service 複製](app-service-web-app-cloning.md)
* [在 Azure App Service 中備份應用程式](manage-backup.md)
* [Azure Resource Manager 的 Azure 流量管理員支援預覽](../traffic-manager/traffic-manager-powershell-arm.md)
* [App Service 環境簡介](environment/intro.md)
* [搭配使用 Azure PowerShell 與 Azure 資源管理員](../azure-resource-manager/powershell-azure-resource-manager.md)

