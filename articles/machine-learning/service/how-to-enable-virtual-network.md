---
title: 在虛擬網路中執行實驗和推斷
titleSuffix: Azure Machine Learning service
description: 在 Azure 虛擬網路中安全地執行機器學習實驗和推斷。 了解如何為模型定型建立計算目標，和如何在虛擬網路中推斷。 了解受保護虛擬網路的需求，例如需要輸入和輸出連接埠。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 60a76df6360ca66e8f55b03d5914283f669eb402
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118100"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>在 Azure 虛擬網路內安全地執行實驗與推斷

在本文中，您會學習如何在虛擬網路內執行實驗和推斷。 虛擬網路充當安全邊界，將 Azure 資源與公用網際網路隔離。 您也可以將 Azure 虛擬網路加入到您的內部部署網路， 讓您能夠安全地將模型定型，並可存取所部署的模型進行推斷。

Azure Machine Learning 服務依賴其他 Azure 服務來處理計算資源。 計算資源 (計算目標) 用於定型和部署模型。 這些計算目標可建立在虛擬網路內部。 例如，您可以使用 Microsoft 資料科學虛擬機器來定型模型，然後將模型部署到 Azure Kubernetes Service (AKS)。 如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

## <a name="storage-account-for-your-workspace"></a>您工作區的儲存體帳戶

當您建立 Azure Machine Learning 服務工作區時，需要 Azure 儲存體帳戶。 請勿開啟此儲存體帳戶的防火牆規則。 Azure Machine Learning 服務需要無限制存取儲存體帳戶。

如果您不確定是否修改了這些設定，請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)中的__預設網路存取規則__。 使用這些步驟允許從 [所有網路] 存取。

## <a name="use-machine-learning-compute"></a>使用 Machine Learning Compute

若要在虛擬網路中使用 Azure Machine Learning Compute，請使用下列關於網路需求的資訊：

- 虛擬網路必須在與 Azure Machine Learning 服務工作區相同的訂用帳戶和區域中。

- 為 Machine Learning Compute 叢集指定的子網路必須具有足夠的未指派 IP 位址，才能容納以叢集為目標的 VM 數目。 如果子網路沒有足夠的未指派 IP 位址，則將局部配置叢集。

- 如果您打算透過限制流量來保護虛擬網路，請針對 Machine Learning Compute 服務開放一些連接埠。 如需詳細資訊，請參閱[必要連接埠](#mlcports)。

- 確認您對虛擬網路其訂用帳戶或資源群組的安全性原則或鎖定是否限制了管理虛擬網路的權限。

- 如果您要將多個的 Machine Learning Compute 叢集放在一個虛擬網路，您可能必須要求一或多個資源的配額增加。

    Machine Learning Compute 會自動將額外的網路資源配置在包含虛擬網路的資源群組中。 對於每個 Machine Learning Compute 叢集，Azure Machine Learning 服務會配置以下資源：

    - 一個網路安全性群組 (NSG)

    - 一個公用 IP 位址

    - 一個負載平衡器

   這些資源會被訂用帳戶的[資源配額](https://docs.microsoft.com/azure/azure-subscription-service-limits)所限制。

### <a id="mlcports"></a> 所需連接埠

Machine Learning Compute 目前使用 Azure Batch 服務將 VM 佈建在指定的虛擬網路中。 子網路必須允許來自 Batch 服務的輸入通訊。 此通訊用於排程在 Machine Learning Compute 節點上的執行，並與 Azure 儲存體和其他資源進行通訊。 Batch 會在 VM 連結的網路介面 (NIC) 層級新增 NSG。 這些 NSG 會自動設定輸入和輸出規則，以允許下列流量：

- 連接埠 29876 和 29877 上來自 Batch 服務角色 IP 位址的輸入 TCP 流量。
 
- 連接埠 22 上的輸入 TCP 流量，以允許遠端存取。
 
- 任何連接埠上傳至虛擬網路的輸出流量。

- 任何連接埠上傳至網際網路的輸出流量。

如果您要在 Batch 設定的 NSG 中修改或新增輸入/輸出規則，請謹慎操作。 如果 NSG 封鎖對計算節點的通訊，則 Machine Learning Compute 服務會將計算節點的狀態設定為 [無法使用]。

您不需要在子網路層級指定 NSG，因為 Batch 會設定其本身的 NSG。 不過，如果指定的子網路有相關聯的 NSG 和 (或) 防火牆，請設定輸入和輸出安全性規則，如上所述。 下列螢幕擷取畫面會顯示規則組態在 Azure 入口網站中的外觀：

![Machine Learning Compute 的輸入 NSG 規則螢幕擷取畫面](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Machine Learning Compute 的輸出 NSG 規則螢幕擷取畫面](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>在虛擬網路中建立 Machine Learning Compute

若要透過使用 Azure 入口網站建立 Machine Learning Compute 叢集，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 Azure Machine Learning 服務工作區。

1. 在 [應用程式] 區段中，選取 [計算]。 然後，選取 [新增計算]。 

    ![如何在 Azure Machine Learning 服務中新增計算](./media/how-to-enable-virtual-network/add-compute.png)

1. 若要設定此計算資源以使用虛擬網路，使用下列選項：

    - __網路組態__：選取 [進階]。

    - __資源群組__：選取包含虛擬網路的資源群組。

    - __虛擬網路__：選取包含子網路的虛擬網路。

    - __子網路__：選取要使用的子網路。

   ![顯示機器學習計算其虛擬網路設定的螢幕擷取畫面](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

您也可以透過使用 Azure Machine Learning SDK 建立 Machine Learning Compute 叢集。 下列程式碼會在名為 `mynetwork` 的虛擬網路其 `default` 子網路中建立新的 Machine Learning Compute 叢集：

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")
    
    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

建立程序完成後，您可以使用叢集將模型定型。 如需詳細資訊，請參閱[選取與使用定型的計算目標](how-to-set-up-training-targets.md)。

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>使用虛擬機器或 HDInsight 叢集

若要虛擬網路中的虛擬機器或 Azure HDInsight 叢集搭配您的工作區使用，請遵循這些步驟：

> [!IMPORTANT]
> Azure Machine Learning 服務僅支援執行 Ubuntu 的虛擬機器。

1. 透過使用 Azure 入口網站 或 Azure CLI 建立 VM 或 HDInsight 叢集，並將它放在 Azure 虛擬網路中。 如需詳細資訊，請參閱下列文件：
    * [建立和管理適用於 Linux VM 的 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虛擬網路延伸 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. 若要允許 Azure Machine Learning 服務與 VM 或叢集上的 SSH 連接埠通訊，您必須設定 NSG 的來源項目。 SSH 連接埠通常是連接埠 22。 若要允許來自此來源的流量，請使用以下資訊：

    * __來源__：選取 [服務標記]。

    * __來源服務標記__：選取 [AzureMachineLearning]。

    * __來源連接埠範圍__:選取 [*]。

    * __目的地__:選取 [任何]。

    * __目的地連接埠範圍__：選取 [22]。

    * __通訊協定__：選取 [任何]。

    * __動作__：選取 [允許]。

   ![用於對虛擬網路內的 VM 或 HDInsight 叢集進行實驗的輸入規則螢幕擷取畫面](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    為 NSG 保留預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。
    
1. 將 VM 或 HDInsight 叢集附加至 Azure Machine Learning 服務工作區。 如需詳細資訊，請參閱[為模型定型設定計算目標](how-to-set-up-training-targets.md)。

## <a name="use-azure-kubernetes-service"></a>使用 Azure Kubernetes Service

> [!IMPORTANT]
> 先檢查先決條件，並為您的叢集規劃 IP 位址，再繼續進行這些步驟。 如需詳細資訊，請參閱[在 Azure Kubernetes Service 中設定進階網路](https://docs.microsoft.com/azure/aks/configure-advanced-networking)。
> 
> 為 NSG 保留預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。
>
> Azure Kubernetes Service 與 Azure 虛擬網路應在相同的區域中。

若要將虛擬網路中的 Azure Kubernetes Service 新增至您的工作區，請從 Azure 入口網站使用下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 Azure Machine Learning 服務工作區。

1. 在 [應用程式] 區段中，選取 [計算]。 然後，選取 [新增計算]。 

    ![如何在 Azure Machine Learning 服務中新增計算](./media/how-to-enable-virtual-network/add-compute.png)

1. 若要設定此計算資源以使用虛擬網路，使用下列選項：

    - __網路組態__：選取 [進階]。

    - __資源群組__：選取包含虛擬網路的資源群組。

    - __虛擬網路__：選取包含子網路的虛擬網路。

    - __子網路__：選取子網路。

    - __Kubernetes 服務位址範圍__：選取 Kubernetes Service 位址範圍。 此位址範圍使用 CIDR 標記法 IP 範圍，來定義可用於叢集的 IP 位址。 此範圍不得與任何子網路 IP 範圍重疊。 例如︰10.0.0.0/16。

    - __Kubernetes DNS 服務 IP 位址__：選取 Kubernetes DNS 服務 IP 位址。 此 IP 位址會指派給 Kubernetes DNS 服務。 它必須在 Kubernetes Service 位址範圍內。 例如︰10.0.0.10.

    - __Docker 橋接器位址__：選取 Docker 橋接器位址。 此 IP 位址會指派給 Docker 橋接器， 不得位於任何子網路 IP 範圍或 Kubernetes Service 位址範圍中。 例如︰172.17.0.1/16。

   ![Azure Machine Learning 服務：Machine Learning Compute 虛擬網路設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > 如果您在虛擬網路中已經有 AKS 叢集，您可以將它附加到工作區。 如需詳細資訊，請參閱[部署至 AKS 的方式](how-to-deploy-to-aks.md)。

您在虛擬網路中也可以使用 **Azure Machine Learning SDK** 新增 Azure Kubernetes 服務。 下列程式碼會在名為 `mynetwork` 的虛擬網路其 `default` 子網路中建立新的 Azure Kubernetes Service 執行個體：

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

建立程序完成之後，您可以在虛擬網路背後的 AKS 叢集進行推斷。 如需詳細資訊，請參閱[部署至 AKS 的方式](how-to-deploy-to-aks.md)。

## <a name="next-steps"></a>後續步驟

* [設定定型環境](how-to-set-up-training-targets.md)
* [部署模型的位置](how-to-deploy-and-where.md)
* [使用 SSL 安全地部署模型](how-to-secure-web-service.md)

