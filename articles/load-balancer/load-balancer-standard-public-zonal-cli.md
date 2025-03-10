---
title: 使用 Azure CLI 來建立具有區域前端的 Standard Load Balancer
titlesuffix: Azure Load Balancer
description: 了解如何使用 Azure CLI 來建立具有區域公用 IP 位址前端的公用 Standard Load Balancer
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 18e5e9ff299cb645e2b5b47d327ee93e27da82df
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699984"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>使用 Azure CLI 來建立具有區域前端的 Standard Load Balancer

本文會逐步說明如何使用標準公用 IP 位址，來建立具有區域前端的公用 [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard)。 在此案例中，您需為前端和後端執行個體指定特定區域，以讓您的資料路徑和資源與特定區域搭配運作。

如需關於搭配使用可用性區域和標準 Load Balancer 的詳細資訊，請參閱[標準 Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝並使用 CLI，請確定您已安裝最新的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，並使用 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) 來登入 Azure 帳戶。

> [!NOTE]
 針對精選的 Azure 資源和區域及 VM 大小系列有提供「可用性區域」支援。 如需如何開始使用，以及有哪些 Azure 資源、區域和 VM 大小系列可供用來試用可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。 如需支援，您可以透過 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 與我們聯繫或[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。  

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#az-group-create) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 *westeurope* 位置建立名為 *myResourceGroupLB* 的資源群組：

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>建立區域標準公用 IP
若要存取網際網路上您的應用程式，您需要負載平衡器的公用 IP 位址。 在特定區域中建立的「公用 IP」位址一律只存在於該區域中。 「公用 IP」位址的區域無法變更。

使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 建立公用 IP 位址。 下列範例會在區域 1 的 *myResourceGroupLoadBalancer* 資源群組中建立名為 *myPublicIP* 的區域公用 IP 位址。

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
```

## <a name="create-azure-standard-load-balancer"></a>建立 Azure Standard Load Balancer
本節將詳細說明如何建立及設定下列負載平衡器元件：
- 前端 IP 集區，可接收負載平衡器上的連入網路流量。
- 後端 IP 集區，前端集區在其中傳送負載平衡網路流量。
- 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
- 負載平衡器規則，可定義如何將流量分散至 VM。

### <a name="create-the-load-balancer"></a>建立負載平衡器
使用 [az network lb create](/cli/azure/network/lb#az-network-lb-create) 來建立 Standard Load Balancer。 下列範例會建立名為 *myLoadBalancer* 的負載平衡器，並將 *myPublicIP* 位址指派給前端 IP 設定。

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>在連接埠 80 上建立健康狀態探查

健全狀況探查會檢查所有虛擬機器執行個體，確認它們可以傳送網路流量。 探查檢查失敗的虛擬機器執行個體會從負載平衡器上移除，直到其恢復正常運作且探查判斷其健全狀況良好為止。 請使用 az network lb probe create 來建立健康狀態探查，以監視虛擬機器的健康狀態。 若要建立 TCP 健康狀態探查，請使用 [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create)。 下列範例會建立名為 myHealthProbe 的健康狀態探查：

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>建立用於連接埠 80 的負載平衡器規則
負載平衡器規則可定義連入流量的前端 IP 組態及接收流量的後端 IP 集區，以及所需的來源和目的地連接埠。 使用 [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) 建立負載平衡器規則 *myLoadBalancerRuleWeb*，用來接聽前端集區 *myFrontEndPool* 中的連接埠 80，以及用來將負載平衡的網路流量傳送到後端位址集區 *myBackEndPool* (也是使用連接埠 80)。

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>設定虛擬網路
請先建立支援的虛擬網路資源，才可部署一些 VM 及測試您的負載平衡器。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)，在 myResourceGroup 中建立名為 *myVnet*且子網路名為 *mySubnet* 的虛擬網路。


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>建立網路安全性群組

使用 [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).來建立名為 *myNetworkSecurityGroup* 的網路安全性群組，以定義對您虛擬網路的輸入連線。

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) 來針對連接埠 80 建立名為 *myNetworkSecurityGroupRule* 的安全性群組規則。

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>建立 NIC
使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 來建立三個網路 NIC，並將它們與公用 IP 位址和網路安全性群組建立關聯。 下列範例會建立三個虛擬 NIC。 (您在下列步驟中針對應用程式建立的每部 VM 都有一個虛擬 NIC)。 您可以隨時建立其他虛擬 NIC 和 VM，並將它們新增至負載平衡器︰

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>建立後端伺服器
在此範例中，您會建立三個位於區域 1 的虛擬機器，用來作為負載平衡器的後端伺服器。 此外，您還會在虛擬機器上安裝 NGINX 來確認已成功建立該負載平衡器。

### <a name="create-cloud-init-config"></a>建立 Cloud-init 組態

您可以使用 cloud-init 組態檔來安裝 NGINX，並在 Linux 虛擬機器上執行 'Hello World' Node.js 應用程式。 您目前的殼層中，建立名為 cloud-init.txt 的檔案，並將下列設定複製及貼到殼層中。 請確定您正確複製整個 cloud-init 檔案，特別是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-the-zonal-virtual-machines"></a>建立區域虛擬機器
使用 [az vm create](/cli/azure/vm#az-vm-create) 建立 VM。 下列範例會在區域 1 中建立三部 VM 並產生 SSH 金鑰 (如果尚未存在的話)︰

```azurecli-interactive
for i in `seq 1 3`; do
 az vm create \
--resource-group myResourceGroupLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone 1 \
--custom-data cloud-init.txt
done
```

## <a name="test-the-load-balancer"></a>測試負載平衡器
使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) 來取得負載平衡器的公用 IP 位址。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 

接著，您可以在 Web 瀏覽器中輸入公用 IP 位址。 請記住，負載平衡器開始將流量分散給 VM 之前，VM 需要花幾分鐘的時間才會就緒。 應用程式隨即顯示，包括負載平衡器分散流量之 VM 的主機名稱，如下列範例所示：

![執行 Node.js 應用程式](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

若要查看負載平衡器如何將流量分散到區域 1 內執行您應用程式的 VM，您可以強制重新整理 Web 瀏覽器。

## <a name="next-steps"></a>後續步驟
- 深入了解[標準負載平衡器](./load-balancer-standard-overview.md)。



