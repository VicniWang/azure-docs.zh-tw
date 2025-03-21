---
title: 教學課程：使用 Azure CLI 部署到現有的虛擬網路中 - Azure 專用 HSM | Microsoft Docs
description: 示範如何使用 CLI 將專用 HSM 部署到現有虛擬網路中的教學課程
services: dedicated-hsm
documentationcenter: na
author: barclayn
manager: barbkess
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: c8713c4d3f6bd7835fed8e668407ea9ee55cc712
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111827"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>教學課程：使用 CLI 將 HSM 部署至現有的虛擬網路

Azure 專用 HSM 提供實體裝置以供單獨客戶使用，其具有完整的系統管理控制權和完整的管理責任。 使用實體裝置創造了 Microsoft 控制裝置配置的需求，以確保有效率地管理容量。 因此，在 Azure 訂用帳戶內，通常看不見可供佈建資源的專用 HSM 服務。 任何需要存取專用 HSM 服務的 Azure 客戶都必須先連絡其 Microsoft 客戶代表，要求註冊專用 HSM 服務。 唯有順利完成此程序，才可能進行佈建。 

本教學課程示範典型的佈建程序，其中：

- 客戶已經有虛擬網路
- 他們有虛擬機器
- 他們必須將 HSM 資源新增到該現有的環境中。

高可用性、多區域的典型部署架構可能如下所示：

![多區域部署](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

本教學課程著重於一對已整合到現有虛擬網路 (請參閱上述的 VNET 1) 的 HSM 和必要的 ExpressRoute 閘道 (請參閱上述的子網路 1)。  所有其他資源都是標準 Azure 資源。 相同的整合程序可以用於上述 VNET 3 上子網路 4 中的 HSM。

## <a name="prerequisites"></a>必要條件

Azure 入口網站中目前尚未提供 Azure 專用 HSM。 所有與服務的互動都會透過命令列或使用 PowerShell 進行。 本教學課程會使用 Azure Cloud Shell 中的命令列 (CLI) 介面。 如果您不熟悉 Azure CLI，請遵循以下的入門指示：[Azure CLI 2.0 使用者入門](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

假設：

- 您已完成 Azure 專用 HSM 註冊程序
- 您已獲准使用服務。 若非如此，請連絡 Microsoft 客戶代表以取得詳細資料。
- 您已建立這些資源的資源群組，而本教學課程中部署的新資訊將會加入該群組。
- 您已經按照上圖建立所需的虛擬網路、子網路和虛擬機器，而現在想要將 2 個 HSM 整合到該部署中。

以下所有指示都假設您已經導覽至 Azure 入口網站，並已開啟 Cloud Shell (選取靠近入口網站右上方的 “\>\_”)。

## <a name="provisioning-a-dedicated-hsm"></a>佈建專用 HSM

透過 ExpressRoute 閘道佈建 HSM 並將其整合到現有的虛擬網路時，會使用 ssh 來驗證。 此驗證有助於確保 HSM 裝置的連線性和基本可用性，以便進行任何進一步的設定活動。 下列命令會使用 Azure Resource Manager 範本來建立 HSM 資源和相關聯的網路資源。

### <a name="validating-feature-registration"></a>驗證功能註冊

如上所述，任何佈建活動都要求向您的訂用帳戶註冊專用 HSM 服務。 若要加以驗證，請在 Azure 入口網站 Cloud Shell 中執行下列命令。

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

下列命令會驗證專用 HSM 服務所需的網路功能。

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

兩個命令都應該會傳回 “Registered” 狀態 (如下所示)。 如果命令並未傳回 “Registered”，則必須註冊此服務，請連絡您的 Microsoft 帳戶代表。

![訂用帳戶狀態](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>建立 HSM 資源

HSM 會佈建到客戶的虛擬網路中，所以需要虛擬網路和子網路。 ExpressRoute 閘道是可供 HSM 啟用虛擬網路與實體裝置之間通訊的相依性，而虛擬機器最終需要使用 Gemalto 用戶端軟體存取 HSM 裝置。 這些資源已收集到範本檔案中 (具有對應的參數檔案)，以便使用。 直接以 HSMrequest@Microsoft.com 連絡 Microsoft 即可取得檔案。

擁有檔案後，您必須編輯參數檔案，以插入慣用的資源名稱。 使用 “value”: “” 編輯程式碼行。

- `namingInfix` HSM 資源名稱的首碼
- `ExistingVirtualNetworkName` 用於 HSM 的虛擬網路名稱
- `DedicatedHsmResourceName1` 資料中心戳記 1 中的 HSM 資源名稱
- `DedicatedHsmResourceName2` 資料中心戳記 2 中的 HSM 資源名稱
- `hsmSubnetRange` HSM 的子網路 IP 位址範圍
- `ERSubnetRange` VNET 閘道的子網路 IP 位址範圍

這些變更的範例如下所示：

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

相關聯的 Azure Resource Manager 範本檔案會以此資訊建立 6 個資源：

- 指定的 VNET 中 HSM 的子網路
- 虛擬網路閘道的子網路
- 可將 VNET 連線到 HSM 裝置的虛擬網路閘道
- 閘道的公用 IP 位址
- 戳記 1 中的 HSM
- 戳記 2 中的 HSM

設定參數值後，必須將檔案上傳至 Azure 入口網站 Cloud Shell 檔案共用，以便使用。 在 Azure 入口網站中，按一下右上方的 “\>\_” Cloud Shell 符號，這會讓畫面的底部成為命令環境。 其選項包含 BASH 和 PowerShell，而您應該選取 BASH (如果尚未設定)。

命令殼層在工具列上有上傳/下載選項，您應該選取此選項以將範本和參數檔案上載至您的檔案共用：

![檔案共用](media/tutorial-deploy-hsm-cli/file-share.png)

上傳檔案後，您就準備好建立資源。 建立新的 HSM 資源之前，有一些您應確保已備妥的必要資源。 您必須具有虛擬網路，內含適用於計算、HSM 和閘道的子網路範圍。 下列命令可作為建立這類虛擬網路的範例。

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
--vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>請注意，對於虛擬網路而言最重要的組態是 HSM 裝置的子網路必須將委派設定為 “Microsoft.HardwareSecurityModules/dedicatedHSMs”。  若未設定此選項，HSM 佈建將無法運作。

一旦備妥所有必要條件，請執行下列命令以使用 Azure Resource Manager 範本，確保您已使用您唯一的名稱來更新值 (最少資源群組名稱)：

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

此部署應該需要大約 25 到 30 分鐘才能完成，其中大量時間用於 HSM 裝置

![佈建狀態](media/tutorial-deploy-hsm-cli/progress-status.png)

當部署順利完成時，會顯示 “provisioningState”:“Succeeded”。 您可以連線到現有的虛擬機器，並使用 SSH 來確保 HSM 裝置的可用性。

## <a name="verifying-the-deployment"></a>驗證部署

若要驗證是否已佈建裝置以及查看裝置屬性，請執行下列命令集。 請確定已適當設定資源群組，且資源名稱完全與您在參數檔案中擁有的資源名稱相同。

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![佈建輸出](media/tutorial-deploy-hsm-cli/progress-status2.png)

您現在也可以使用 [Azure 資源總管](https://resources.azure.com/)來查看資源。   在總管中，依序展開左側的 [訂用帳戶]、專用 HSM 的特定訂用帳戶、資源群組、您所使用的資源群組，最後選取 [資源] 項目。

## <a name="testing-the-deployment"></a>測試部署

測試部署時，請連線到可存取 HSM 的虛擬機器，然後直接連線到 HSM 裝置。 這些動作會確認 HSM 可連線。
SSH 工具用於連線至虛擬機器。 此命令將如下所示，但採用您在參數中指定的系統管理員名稱和 dns 名稱。

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

VM 的 IP 位址也用來取代上述命令中的 DNS 名稱。 如果命令成功，它會提示您輸入密碼，而您應該加以輸入。 登入虛擬機器後，即可使用在與 HSM 相關聯的網路介面資源的入口網站中找到的私人 IP 位址登入 HSM。

![元件清單](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>請注意，若已選取 [顯示隱藏的類型] 核取方塊，將會顯示 HSM 資源。

在上面的螢幕擷取畫面，按一下 “HSM1_HSMnic” 或 “HSM2_HSMnic” 會顯示適當的私人 IP 位址。 否則，以上使用的 `az resource show` 命令是識別正確 IP 位址的方法。 

當您擁有正確的 IP 位址時，請執行下列命令來替代該位址：

`ssh tenantadmin@10.0.2.4`

如果成功，系統會提示您輸入密碼。 預設密碼是 PASSWORD，而 HSM 會先要求您變更您的密碼，以設定強式密碼，然後使用貴組織所偏好的機制來儲存密碼並防止遺失。

>[!IMPORTANT]
>如果您遺失此密碼，HSM 必須重設，這表示您的金鑰會遺失。

當您使用 ssh 連線到 HSM 時，請執行下列命令，以確保 HSM 運作正常。

`hsm show`

其輸出應類似下圖所示：

![元件清單](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

此時，您已配置所有資源，以提供高可用性、兩個 HSM 部署和已驗證的存取和操作狀態。 任何進一步的設定或測試都牽涉到更多 HSM 裝置本身的工作。 為此，您應該遵循 Gemalto Luna Network HSM 7 Administration Guide 第 7 章的指示，來初始化 HSM 及建立分割區。 您在 Gemalto 客戶支援入口網站中註冊且具有客戶識別碼後，可以直接從 Gemalto 下載所有文件和軟體。 下載用戶端軟體 7.2 版，以取得所有必要的元件。

## <a name="delete-or-clean-up-resources"></a>刪除或清除資源

如果您已處理完 HSM 裝置，即可將它當作資源刪除並傳回可用的集區。 執行此作業時的顯著考量就是裝置上的任何敏感性客戶資料。 若要移除敏感性客戶資料，裝置應使用 Gemalto 用戶端來恢復出廠預設值。 請參閱 Gemalto 系統管理員指南中的 SafeNet Network Luna 7 裝置，並考慮依序執行下列命令。

1. `hsm factoryReset -f`
2. `sysconf config factoryReset -f -service all`
3. `network interface delete -device eth0`
4. `network interface delete -device eth1`
5. `network interface delete -device eth2`
6. `network interface delete -device eth3`
7. `my file clear -f`
8. `my public-key clear -f`
9. `syslog rotate`


>[!NOTE]
如果您有關於任何 Gemalto 裝置組態的問題，您應該連絡 [Gemalto 客戶支援](https://safenet.gemalto.com/technical-support/)。


如果您已處理完此資源群組中的資源，即可透過下列命令來移除它們：

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>後續步驟

完成本教學課程中的步驟之後，就會佈建專用 HSM 資源，而您具有包含必要 HSM 和進一步網路元件的虛擬網路，能夠與 HSM 進行通訊。  您現在可以慣用部署架構所需的更多資源，讓此部署更加完善。 如需有關協助規劃部署的詳細資訊，請參閱「概念」文件。
建議採用以下設計：主要區域中有兩個 HSM 可處理機架層級的可用性，而次要區域中有兩個 HSM 可處理區域可用性。 本教學課程中所用的範本檔案可輕鬆地作為兩個 HSM 部署的基礎，但必須修改其參數以能夠以符合您的需求。

* [高可用性](high-availability.md)
* [實體安全性](physical-security.md)
* [網路功能](networking.md)
* [支援能力](supportability.md)
* [監視](monitoring.md)
