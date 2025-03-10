---
title: 使用 Ansible 在 Azure 中建立並設定 Azure Kubernetes Service 叢集
description: 了解如何使用 Ansible 在 Azure 中建立並管理 Azure Kubernetes Service 叢集
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, 劇本, aks, 容器, Kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/23/2018
ms.openlocfilehash: f4541bb9516855c4391188fb57e5ab64bc03c76e
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890479"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>使用 Ansible 在 Azure 中建立並設定 Azure Kubernetes Service 叢集
Ansible 可讓您將環境中的資源部署和設定自動化。 您可以使用 Ansible 來管理 Azure Kubernetes Service (AKS)。 本文說明如何使用 Ansible 建立並設定 Azure Kubernetes Service 叢集。

## <a name="prerequisites"></a>必要條件
- **Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- **Azure 服務主體** - 在[建立服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)時，請記下下列值：**appId**、**displayName**、**password** 和 **tenant**。

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 必須使用 Ansible 2.6，才能執行本教學課程中的下列範例腳本。

## <a name="create-a-managed-aks-cluster"></a>建立受控 AKS 叢集
本節中的程式碼會呈現用來建立資源群組的 Ansible 劇本範例，以及位於資源群組中的 AKS 叢集。

> [!Tip]
> 針對 `your_ssh_key` 預留位置，以單行格式且開頭為 "ssh-rsa" (不含引號) 的方式輸入 RSA 公開金鑰。

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

下面幾點有助於解釋上述 Ansible 劇本的程式碼：
- **tasks** 內的第一個區段會定義名為 **myResourceGroup** 且位於 **eastus** 位置內的資源群組。
- **tasks** 內的第二個區段會定義名為 **myAKSCluster** 且位於 **myResourceGroup** 資源群組內的 AKS 叢集。

若要使用 Ansible 建立 AKS 叢集，請將上述劇本範例儲存為 `azure_create_aks.yml`，並使用下列命令執行劇本：

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

*ansible-playbook 命令的輸出與下列顯示已成功建立 AKS 叢集的內容類似：

  ```Output
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>調整 AKS 節點

上一節中的劇本範例會定義兩個節點。 如果叢集上需要較少或較多的容器工作負載，您可以輕鬆地調整節點數目。 本節中的劇本範例會將節點數從兩個增加為三個。 變更 **agent_pool_profiles** 區塊中的 **count** 值，即可修改節點計數。

> [!Tip]
> 針對 `your_ssh_key` 預留位置，以單行格式且開頭為 "ssh-rsa" (不含引號) 的方式輸入 RSA 公開金鑰。

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

若要使用 Ansible 調整 Azure Kubernetes Service 叢集，請將上述劇本儲存為 azure_configure_aks.yml，並如下所示執行劇本：

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

下列輸出顯示您已成功建立 AKS 叢集：

  ```Output
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>刪除受控 AKS 叢集

下列範例 Ansible 腳本區段會示範如何刪除 AKS 叢集：

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name:
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

若要使用 Ansible 刪除 Azure Kubernetes Service 叢集，請將上述腳本儲存為 *azure_delete_aks.yml*，並依照下列方式執行腳本：

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

下列輸出顯示您已成功刪除 AKS 叢集：
  ```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [教學課程：調整 Azure Kubernetes Service (AKS) 中的應用程式](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
