---
title: 如何將適用於容器的 Azure 監視器上線 | Microsoft Docs
description: 本文說明您如何將適用於容器的 Azure 監視器上線並加以設定，讓您可以了解容器的執行方式，以及已識別出哪些效能相關問題。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: 13da9e0d731e87b6cdd5830c9295847511c301ef
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567293"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>如何將適用於容器的 Azure 監視器上線  
本文說明如何設定適用於容器的 Azure 監視器，來監視部署至 Kubernetes 環境並裝載於 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) 上之工作負載的效能。

使用下列受支援的方法，即可為全新、一或多個現有的 AKS 部署啟用適用於容器的 Azure 監視器：

* 從 Azure 入口網站或使用 Azure CLI
* 使用 [Terraform 和 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

## <a name="prerequisites"></a>必要條件 
開始之前，請確定您有下列項目：

- Log Analytics 工作區。 您可以在啟用新 AKS 叢集的監視時建立它，或是讓上線體驗在 AKS 叢集訂用帳戶的預設資源群組中建立預設工作區。 若選擇自行建立它 ，您可以透過 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)、透過 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../../azure-monitor/learn/quick-create-workspace.md)中建立它。
- 您是用於啟用容器監視的 Log Analytics 參與者角色成員。 如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../../azure-monitor/platform/manage-access.md)。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>元件 

監視效能的能力依賴適用於 Linux 的容器化 Log Analytics 代理程式，此代理程式特別為適用於容器的 Azure 監視器所開發。 這個特製化的代理程式會收集叢集中所有節點的效能和事件資料，且會自動部署並在部署期間向指定的 Log Analytics 工作區進行註冊。 代理程式版本是 microsoft/oms:ciprod04202018 或更新版本，且會以下列格式的日期呈現：*mmddyyyy*。 

發行代理程式的新版本時，代理程式會在您裝載於 Azure Kubernetes Service (AKS) 的受控 Kubernetes 叢集上自動升級。 若要遵循所發行的版本，請參閱[代理程式發行公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) (英文)。 

>[!NOTE] 
>如果您已部署 AKS 叢集，則可以使用 Azure CLI 或提供的 Azure Resource Manager 範本來啟用監視，如此文章稍後所示。 您無法使用 `kubectl` 來生集、刪除、重新部署或部署此代理程式。 範本必須部署在叢集所在的資源群組。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com)。 

## <a name="enable-monitoring-for-a-new-cluster"></a>針對新的叢集啟用監視
在部署期間，您可以在 Azure 入口網站中、使用 Azure CLI 或使用 Terraform 來啟用對新 AKS 叢集的監視。  若要從入口網站啟用，請依照快速入門文章[部署 Azure Kubernetes Service (AKS) 叢集](../../aks/kubernetes-walkthrough-portal.md)中的步驟執行。 在 [監視] 頁面上，針對 [啟用監視] 選項，請選取 [是]，然後選取現有的 Log Analytics 工作區，或是建立新的 Log Analytics 工作區。 

### <a name="enable-using-azure-cli"></a>啟用使用 Azure CLI
若要使用 Azure CLI 針對建立的新 AKS 叢集啟用監視，請依照快速入門文章中[建立 AKS 叢集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)一節下的步驟執行。  

>[!NOTE]
>如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 2.0.43 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 
>

### <a name="enable-using-terraform"></a>啟用使用 Terraform
如果您[使用 Terraform 部署新的 AKS 叢集](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)，若不選擇指定現有的引數，則可以指定設定檔中所需的引數[以建立 Log Analytics 工作區](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html)。 

>[!NOTE]
>如果您選擇使用 Terraform，則必須執行 Terraform Azure RM Provider 1.17.0 版或以上版本。

若要將適用於容器的 Azure 監視器新增至工作區，請參閱：[azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)，並透過包含 [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) 和指定 **oms_agent** 來完成設定檔。 

啟用監視並順利完成所有設定工作之後，您可以透過兩種方式來監視叢集效能：

* 選取左窗格中的 [健康情況]，直接在 AKS 叢集中進行。
* 在所選取叢集的 AKS 叢集頁面中，選取 [監視器容器深入解析] 圖格。 在 Azure 監視器中，選取左窗格中的 [健康情況]。 

  ![在 AKS 中選取適用於容器的 Azure 監視器選項](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>針對現有的受控叢集啟用監視
您可以使用 Azure CLI、從入口網站或是透過使用 PowerShell Cmdlet `New-AzureRmResourceGroupDeployment` 所提供的 Azure Resource Manager 範本，來對已部署的 AKS 叢集啟用監視。 

### <a name="enable-monitoring-using-azure-cli"></a>使用 Azure CLI 來啟用監視
下列步驟會使用 Azure CLI 來啟用對 AKS 叢集的監視。 在此範例中，您不需要預先建立或指定現有的工作區。 此命令透過在 AKS 叢集預設資源群組中建立預設工作區 (若該區域中沒有預設工作區) 來為您簡化程序。  所建立的預設工作區格式類似 *DefaultWorkspace-\<GUID>-\<Region>*。  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

輸出看起來會像下面這樣：

```azurecli
provisioningState       : Succeeded
```

如果您希望與現有的工作區整合，請使用下列命令來指定該工作區。

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

輸出看起來會像下面這樣：

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-using-terraform"></a>使用 Terraform 來啟用監視
1. 將 **oms_agent** 附加元件設定檔新增至現有的 [azurerm_kubernetes_cluster 資源](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. 按照 Terraform 文件中的步驟新增 [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)。

### <a name="enable-monitoring-from-azure-monitor"></a>從 Azure 監視器啟用監視
若要從 Azure 監視器在 Azure 入口網站中啟用 AKS 叢集的監視，請執行下列步驟：

1. 在 Azure 入口網站中，選取 [監視]。 
2. 從清單中選取 [容器]。
3. 在 [監視器 - 容器] 頁面上，選取 [不受監視的叢集]。
4. 從不受監視的叢集清單，在清單中尋找容器，然後按一下 [啟用]。   
5. 在 [適用於容器的 Azure 監視器上線] 頁面上，如果相同訂用帳戶中有現有 Log Analytics 工作區可作為叢集，請從下拉式清單中加以選取。  
    清單會預先選取訂用帳戶中已部署 AKS 容器的預設工作區和位置。 

    ![啟用 AKS 容器深入解析監視](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >如果您想要建立新的 Log Analytics 工作區以儲存來自叢集的監視資料，請遵循[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)中的指示。 請務必在和部署 AKS 容器相同的訂用帳戶中建立工作區。 
 
啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。 

### <a name="enable-monitoring-from-aks-cluster-in-the-portal"></a>從入口網站中的 AKS 叢集啟用監視
若要在 Azure 入口網站中啟用 AKS 容器的監視，請執行下列步驟：

1. 在 Azure 入口網站中，選取 [所有服務]。 
2. 在資源清單中，開始輸入**容器**。  
    清單會根據您輸入的文字進行篩選。 
3. 選取 [Kubernetes 服務]。  

    ![[Kubernetes 服務] 連結](./media/container-insights-onboard/portal-search-containers-01.png)

4. 在容器清單中，選取容器。
5. 在容器概觀頁面上，選取 [監視容器]。  
6. 在 [適用於容器的 Azure 監視器上線] 頁面上，如果相同訂用帳戶中有現有 Log Analytics 工作區可作為叢集，請在下拉式清單中選取。  
    清單會預先選取訂用帳戶中已部署 AKS 容器的預設工作區和位置。 

    ![啟用 AKS 容器健康情況監視](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >如果您想要建立新的 Log Analytics 工作區以儲存來自叢集的監視資料，請遵循[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)中的指示。 請務必在和部署 AKS 容器相同的訂用帳戶中建立工作區。 
 
在啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的作業資料。 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本啟用監視
此方法包含兩個 JSON 範本。 一個範本會指定啟用監視的設定，另一個範本則包含可設定以指定下列各項的參數值：

* AKS 容器資源識別碼。 
* 叢集部署所在的資源群組。
* 要在其中建立工作區的 Log Analytics 工作區與區域。 

>[!NOTE]
>範本必須部署在叢集所在的資源群組。
>

Log Analytics 工作區必須手動建立。 若要建立工作區，您可以透過 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)、透過 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../../azure-monitor/learn/quick-create-workspace.md)中設定它。

若您不熟悉使用範本來部署資源的概念，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/resource-group-template-deploy.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/resource-group-template-deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

#### <a name="create-and-execute-a-template"></a>建立並執行範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]", 
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. 將此檔案儲存為本機資料夾的 **existingClusterOnboarding.json**。
3. 將下列 JSON 語法貼到您的檔案中：

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "<workspaceLocation>"
       }
     }
    }
    ```

4. 使用 AKS 叢集之 [AKS 概觀] 頁面上的值，來編輯 **aksResourceId** 和 **aksResourceLocation** 的值。 **workspaceResourceId** 值是您 Log Analytics 工作區的完整資源識別碼，其中包含工作區名稱。 同時針對 **workspaceRegion** 指定工作區的所在位置。 
5. 將此檔案儲存為本機資料夾的 **existingClusterParam.json**。
6. 您已準備好部署此範本。 

    * 在包含範本的資料夾中使用下列 PowerShell 命令：

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ClusterResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

        ```powershell
        provisioningState       : Succeeded
        ```

    * 使用 Azure CLI 來執行下列命令：
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        可能需要幾分鐘的時間才能完成設定變更。 完成之後，將會顯示如下訊息並包含結果：

        ```azurecli
        provisioningState       : Succeeded
        ```
啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。 

## <a name="verify-agent-and-solution-deployment"></a>驗證代理程式和解決方案部署
使用代理程式 *06072018* 版或更新版本時，您可以確認代理程式和方案皆已成功部署。 使用舊版的代理程式時，您只能確認代理程式部署。

### <a name="agent-version-06072018-or-later"></a>代理程式 06072018 版或更新版本
執行下列命令以確認代理程式已成功部署。 

```
kubectl get ds omsagent --namespace=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

若要確認解決方案的部署，請執行下列命令：

```
kubectl get deployment omsagent-rs -n=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>早於 06072018 的代理程式版本

若要確認已正確部署 06072018 版以前發行的 Log Analytics 代理程式，請執行下列命令：  

```
kubectl get ds omsagent --namespace=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>使用 CLI 來檢視設定
使用 `aks show` 命令來取得詳細資料，例如解決方案是否已啟用、Log Analytics 工作區 resourceID 為何，以及有關叢集的詳細資料。  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。  此命令的結果應該會顯示監視附加元件設定檔，而且應該會類似下列命令輸出：

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```
## <a name="next-steps"></a>後續步驟

* 如果您在試著將解決方案上線時遇到問題，請檢閱[疑難排解指南](container-insights-troubleshoot.md)

* 藉由啟用監視來擷取 AKS 叢集節點和 Pod 的健康情況計量，這些健康情況計量都可在 Azure 入口網站中取得。 若要了解如何使用適用於容器的 Azure 監視器，請參閱[檢視 Azure Kubernetes Service 健康情況](container-insights-analyze.md)。
