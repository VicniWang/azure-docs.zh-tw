---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: f8122f35ac6d604908fc31dcece7dfb53dd50286
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985385"
---
共用映像庫服務可協助您圍繞自訂受控虛擬機器映像來建置結構和組織。 使用共用映像庫，即可在組織內對於不同的使用者、服務主體或 AD 群組共用您的映像。 共用映像可以複寫至多個區域，以更快速調整您的部署。

根據映像的建立方式，受控映像可能是完整 VM (包括任何連結的資料磁碟) 或僅含 OS 磁碟的複本。 當您從映像建立 VM 時，映像中 VHD 的複本可用來建立新 VM 的磁碟。 受控映像會保留在儲存體中，而且可以一再用來建立新的 VM。

如果您有大量需要維護的受控映像，而且想要提供給全公司使用時，便可使用共用映像庫作為存放庫，以便輕鬆地更新及共用映像。 使用共用映像庫所需的費用，就只有映像所用儲存體的成本，加上任何網路輸出成本 (用於將映像從來源區域複寫到已發行的區域)。

共用映像庫具有多個資源類型：

| 資源 | 說明|
|----------|------------|
| **受控映像** | 這是基本映像，既可單獨使用，也可用來在映像庫中建立個**映像版本**。 受控映像是從一般化 VM 建立的。 受控映像是一種特殊的 VHD 類型，可用來產生多個 VM，現在可以用來建立共用映像版本。 |
| **映像庫** | 和 Azure Marketplace 一樣，**映像庫**是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 映像會在資源庫內定義，並帶有映像資訊以及在內部使用時所需滿足的需求。 這包括映像是 Windows 還是 Linux、版本資訊以及最小和最大的記憶體需求。 這是映像類型的定義。 |
| **映像版本** | **映像版本**是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用**映像版本**來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |

<br>


![圖形：顯示如何在資源庫中備有多個映像版本](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>區域支援

共用映像庫的區域支援目前處於有限預覽狀態，但會不斷擴充。 針對有限預覽版，以下是您可建立資源庫的區域清單，以及可在其中複寫任何資源庫映像的區域清單： 

| 資源庫建立區域  | 版本複寫目的地 |
|--------------------|----------------------|
| 美國中西部    |所有公用區域 &#42;|
| 美國東部 2          ||
| 美國中南部   ||
| 東南亞     ||
| 西歐        ||
| 美國西部            ||
| 美國東部            ||
| 加拿大中部     ||
|                    ||



&#42; 若要複寫至澳大利亞中部和澳大利亞中部 2，您需要將您的訂用帳戶列入允許清單。 若欲要求加入允許清單，請移至： https://www.microsoft.com/en-au/central-regions-eligibility/

## <a name="scaling"></a>調整大小
共用映像庫可讓您指定要讓 Azure 保留的映像複本數目。 這對於有多個 VM 的部署案例很有幫助，因為 VM 部署可以分散到不同複本，減少執行個體建立程序由於單一複本多載而遭到節流的機會。

![圖形：顯示如何調整映像](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>複寫
共用映像庫也可讓您自動地將映像複寫到其他 Azure 區域。 每個共用映像版本皆可複寫到不同區域，實際情形取決於何者對組織有利。 舉例來說，一律將最新映像複寫到多個區域，而讓所有較舊的版本只留在 1 個區域中。 這有助於節省共用映像版本的儲存體成本。 

作為共用映像版本複寫目的地的區域可在共用映像版本建立之後更新。 複寫到不同區域所需的時間取決於所複製的資料量，以及作為版本複寫目的地的區域數目。 在某些情況下，這可能需要幾小時的時間。 複寫進行期間，您可以檢視每個區域的複寫狀態。 某個區域完成映像複寫後，您就可以在該區域使用該映像版本部署 VM 或 VMSS。

![圖形：顯示如何複寫映像](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access
由於共用映像庫、共用映像和共用映像版本都是資源，所以您可以使用內建的原生 Azure RBAC 控制來共用。 使用 RBAC 時，您可以將這些資源共用給組織內的其他使用者、服務主體和群組。 這些資源的共用範圍侷限在相同 Azure AD 租用戶內。 使用者擁有共用映像版本的存取權後，就可以在和共用映像版本相同的 Azure AD 租用戶內，於其有權存取的任何訂用帳戶中部署 VM 或虛擬機器擴展集。  以下共用矩陣可協助您了解使用者有權存取的項目：

| 與使用者共用     | 共用映像庫 | 共用映像 | 共用映像版本 |
|----------------------|----------------------|--------------|----------------------|
| 共用映像庫 | yes                  | 是          | yes                  |
| 共用映像         | 否                   | yes          | yes                  |
| 共用映像版本 | 否                   | 否           | yes                  |



## <a name="billing"></a>計費
使用共用映像庫服務不會有額外費用。 您只會支付下列資源的費用：
- 儲存共用映像版本的儲存體成本。 這取決於版本的複本數目以及作為版本複寫目的地的區域數目。
- 從版本的來源區域複寫至要複寫之區域的網路輸出費用。

## <a name="sdk-support"></a>SDK 支援

下列 SDK 支援建立共用映像資源庫：

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>範本

您可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像資源庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>常見問題集 

**問：** 如何註冊共用映像庫公開預覽版？
 
 A. 若要註冊共用映像庫公開預覽版，您必須從每個想要在其中建立共用映像庫、映像定義或映像版本資源的訂用帳戶，以及想要使用映像版本部署虛擬機器的位置執行下列命令，來註冊該功能。

**CLI**： 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register --name Microsoft.Compute
```

**PowerShell**: 

```powershell
Register-AzProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

**問：** 如何列出訂用帳戶之間的所有共用映像庫資源？ 
 
 A. 若要針對您可在 Azure 入口網站上存取的訂用帳戶，列出其彼此之間的所有共用映像庫資源，請遵循下列步驟：

 1. 開啟 [Azure 入口網站](https://portal.azure.com)。
 1. 移至 [所有資源]。
 1. 選取所有要列出其內含全部資源的訂用帳戶。
 1. 尋找類型為 [私用資源庫] 的資源。
 
 若要查看映像定義和映像版本，您還應該選取 [顯示隱藏的類型]。
 
 若要列出您有權存取之所有訂用帳戶的共用映像庫資源，請在 Azure CLI 中使用下列命令：

 ```bash
 az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
 ```


**問：** 如何跨訂用帳戶共用映像？
 
 A. 您可以使用角色型存取控制 (RBAC) 來跨訂用帳戶共用映像。 具有映像版本 (甚至可跨訂用帳戶) 讀取權限的使用者，都能夠使用映像版本來部署虛擬機器。


**問：** 是否可以將現有映像移至共用映像庫？
 
 A. 是。 根據您可能擁有的映像類型，案例共有 3 種。

 案例 1：如果您擁有受控映像，則可以透過該映像建立映像定義和映像版本。

 案例 2：如果您擁有非受控的一般化映像，則可以透過該映像建立受控映像，繼而建立映像定義和映像版本。 

 案例 3：如果您在本機檔案系統擁有 VHD，則需要上傳 VHD、建立受控映像，然後就可以從中建立映像定義和映像版本。 
    - 如果是 Windows VM 的 VHD，請參閱[上傳一般化 VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)。
    - 如果是 Linux VM 的 VHD，請參閱[上傳 VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**問：** 是否可以從特製化磁碟建立映像版本？

 A. 否，目前不支援將特製化磁碟作為映像。 如果您有特製化磁碟，則需要將特製化磁碟連結至新的 VM 以[從 VHD 建立 VM](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk)。 擁有執行中的 VM 後，您必須依照指示從 [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) 或 [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) 建立受控映像。 擁有一般化受控映像後，就可以開始建立共用映像描述和映像版本的程序。


**問：** 是否可以透過 Azure 入口網站建立共用映像庫、映像定義和映像版本？

 A. 否，目前不支援透過 Azure 入口網站建立任何共用映像庫資源。 不過，有支援透過 CLI、範本和 SDK 來建立共用映像庫資源。 PowerShell 也即將發行。

 
**問：** 映像定義或映像版本在建立之後是否可以更新？ 可以修改哪種詳細資料？

 A. 每個資源可更新的詳細資料如下所述：
 
共用映像庫：
- 說明

映像定義：
- 建議的 vCPU
- 記憶體
- 說明
- 生命週期結束日期

映像版本：
- 區域複本計數
- 目標區域
- 從最新版排除
- 生命週期結束日期


**問：** 共用映像庫資源是否可以在建立後移至不同訂用帳戶？

 A. 否，無法將共用映像庫資源移至不同訂用帳戶。 不過如有需要，可將資源庫中的映像版本複寫到其他區域。

**問：** 是否可以跨雲端 (Azure China 21Vianet、Azure 德國和 Azure Government 雲端) 複寫映像版本？ 

 A. 否，無法跨雲端複寫映像版本。

**問：** 是否可以跨訂用帳戶複寫映像版本？ 

 A. 否，但可以在訂用帳戶中跨區域複寫映像版本，然後透過 RBAC 在其他訂用帳戶中使用。

**問：** 是否可以跨 Azure AD 租用戶共用映像版本？ 

 A. 否，共用映像庫目前不支援跨 Azure AD 租用戶共用映像版本。 但是，您可以使用 Azure Marketplace 上的 [私人供應項目] 功能來達到此目的。


**問：** 跨目標區域複寫映像版本需要多久時間？

 A. 映像版本的複寫時間完全取決於映像大小以及作為其複寫目的地的區域數目。 不過，建議您最好讓映像小一點，並讓來源和目標區域近一點以獲得最佳結果。 您可以使用 -ReplicationStatus 旗標檢查複寫的狀態。


**問：** 訂用帳戶中可以建立幾個共用映像庫？

 A. 預設配額是： 
- 每個區域中每個訂用帳戶 10 個共用映像庫
- 每個區域中每個訂用帳戶 200 個映像定義
- 每個區域中每個訂用帳戶 2000 個映像版本


**問：** 來源區域和目標區域有何差異？

 A. 來源區域是要建立映像版本的區域，目標區域則是要儲存映像版本複本的區域。 每個映像版本只能有一個來源區域。 此外，在建立映像版本時，請務必將來源區域位置傳遞作為其中一個目標區域。  


**問：** 如何在建立映像版本時指定來源區域？

 A. 在建立映像版本時，您可以使用 **--location** 標籤 (在 CLI 中) 以及 **-Location** 標籤 (在 PowerShell 中) 來指定來源區域。 請確定用來作為基底映像以供建立映像版本的受控映像所在位置，與您想要在其中建立映像版本的位置相同。 此外，在建立映像版本時，請務必將來源區域位置傳遞作為其中一個目標區域。  


**問：** 如何指定要在每個區域中建立的映像版本複本數目？

 A. 有兩種方式可供指定要在每個區域中建立的映像版本複本數目：
 
1. 區域複本計數，用以指定要在每個區域建立的複本數目。 
2. 一般複本計數，這是未指定區域複本計數時，每個區域計數的預設值。 

若要指定區域複本計數，請傳遞位置以及要在該區域建立的複本數目，例如：“South Central US=2”。 

如果未對每個位置指定區域複本計數，則複本的預設數目會是所指定的一般複本計數。 

若要在 CLI 中指定一般複本計數，請在 `az sig image-version create` 命令中使用 **--replica-count** 引數。


**問：** 是否可以在想要用來建立映像定義和映像版本的地方以外的位置建立共用映像庫？

 A. 是的，可以。 但建議您最好讓資源群組、共用映像庫、映像定義和映像版本位於相同位置。


**問：** 使用共用映像庫需要哪些費用？

 A. 除了用於儲存映像版本的儲存體費用，以及用於將映像版本從來源區域複寫到目標區域的網路輸出費用外，使用共用映像庫服務不會再有任何費用。

**問：** 我應該使用哪些 API 版本來建立共用映像庫、映像定義、映像版本，以及從映像版本建立 VM/VMSS？

 A. 對於使用映像版本來部署 VM 和虛擬機器擴展集，建議您使用 API 版本 2018-04-01 或更高版本。 若要處理共用映像庫、映像定義及映像版本，建議您使用 API 版本 2018-06-01。 
