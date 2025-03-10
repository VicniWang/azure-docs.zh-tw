---
title: 在 Windows 上安裝 Azure IoT Edge | Microsoft Docs
description: 在 Windows 10、Windows Server 和 Windows IoT 核心版上安裝 Azure IoT Edge 的指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 27478de68cde9a097dcc160a4553839aef9a018c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902800"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>在 Windows 上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。 

若要深入了解 IoT Edge 執行階段，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出在 Windows x64 (AMD/Intel) 系統上安裝 Azure IoT Edge 執行階段的步驟。 Windows 支援目前為預覽版。

>[!NOTE]
就 Azure IoT Edge 而言，在 Windows 系統上使用 Linux 容器不是建議或支援的生產環境設定。 不過，這很適合用於開發與測試用途。

## <a name="prerequisites"></a>必要條件

使用此區段可以檢閱 Windows 裝置是否可支援 IoT Edge，以及在安裝之前為容器引擎做好準備。 

### <a name="supported-windows-versions"></a>支援的 Windows 版本

Azure IoT Edge 支援不同版本的 Windows，取決於您執行 Windows 容器或 Linux 容器而定。 

具有 Windows 容器之最新版本的 Azure IoT Edge 可以在下列版本的 Windows 上執行：
* 具有 2018 年 10 月更新的 Windows 10 或 IoT 核心版 (組建 17763)
* Windows Server 2019 (組建 17763)

具有 Linux 容器之最新版本的 Azure IoT Edge 可以在下列版本的 Windows 上執行： 
* Windows 10 年度更新版 (組建 14393) 或較新版本
* Windows Server 2016 或更新版本

如需目前支援哪些作業系統的詳細資訊，請參閱 [Azure IoT Edge 支援](support.md#operating-systems)。 

如需最新版本 IoT Edge 中包含哪些內容的詳細資訊，請參閱 [Azure IoT Edge 版次](https://github.com/Azure/azure-iotedge/releases)。

### <a name="prepare-for-a-container-engine"></a>準備容器引擎 

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器引擎。 對於生產案例，請使用安裝指令碼中包含的 Moby 引擎，在 Windows 裝置上執行 Windows 容器。 對於開發和測試，您可以在您的 Windows 裝置上執行 Linux 容器，但您需要先安裝及設定容器引擎，然後再安裝 IoT Edge。 對於上述任一案例，請參閱下列各節，了解準備您的裝置的必要條件。 

#### <a name="moby-engine-for-windows-containers"></a>適用於 Windows 容器的 Moby 引擎

對於在生產案例中執行 IoT Edge 的 Windows 裝置，Moby 是唯一正式支援的容器引擎。 安裝指令碼會先在您的裝置上自動安裝 Moby 引擎，然後再安裝 IoT Edge。 藉由開啟容器功能，以準備您的裝置。 

1. 在啟動列中，搜尋 [開啟或關閉 Windows 功能]，並開啟控制台程式。
2. 尋找並選取 [容器]。
3. 選取 [確定] 。 

#### <a name="docker-for-linux-containers"></a>適用於 Linux 容器的 Docker

如果您使用 Windows 以開發並測試 Linux 裝置的容器，您可以使用[適用於 Windows 的 Docker 用戶端](https://www.docker.com/docker-windows)作為您的容器引擎。 Docker 可設定為[使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。 您需要先安裝 Docker，並加以設定，然後再安裝 IoT Edge。 在生產環境中的 Windows 裝置上不支援 Linux 容器。 

如果您的 IoT Edge 裝置是 Windows 電腦，請檢查其是否符合 Hyper-V 的[系統需求](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)。 如果是虛擬機器，請啟用[巢狀虛擬化](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)，並配置至少 2GB 的記憶體。

## <a name="install-iot-edge-on-a-new-device"></a>在新的裝置上安裝 IoT Edge

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用封裝即表示您接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

PowerShell 指令碼會下載並安裝 Azure IoT Edge 安全性精靈。 接著，安全性精靈會開始第一個執行階段模組 (總共兩個)，也就是可遠端部署其他模組的 IoT Edge 代理程式。 

當您第一次在裝置上安裝 IoT Edge 執行階段時，需要使用來自 IoT 中樞的身分識別以佈建裝置。 單一 IoT Edge 裝置可以使用 IoT 中樞所提供的裝置連接字串以手動方式佈建。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要設定時將很有幫助。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。 

下列各節說明新裝置上 IoT Edge 安裝指令碼的常見使用案例和參數。 

### <a name="option-1-install-and-manually-provision"></a>選項 1：安裝並手動佈建

在第一個選項中，您提供由 IoT 中樞產生的裝置連接字串以佈建裝置。 

依照[註冊新的 Azure IoT Edge 裝置](how-to-register-device-portal.md)中的步驟註冊您的裝置，並擷取裝置連接字串。 

此範例顯示使用 Windows 容器的手動安裝：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

當您手動安裝及佈建裝置時，可以使用其他參數修改安裝，包括：
* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證
* 跳過 Moby CLI 安裝

如需這些安裝選項的詳細資訊，請繼續閱讀本文，或跳過以了解[所有安裝參數](#all-installation-parameters)。

### <a name="option-2-install-and-automatically-provision"></a>選項 2：安裝並自動佈建

在第二個選項中，使用 IoT 中樞裝置佈建服務來佈建裝置。 提供來自「裝置佈建服務」執行個體的**範圍識別碼**，以及來自您裝置的**註冊識別碼**。

依照[在 Windows 上建立及佈建模擬 TPM Edge 裝置](how-to-auto-provision-simulated-device-windows.md)中的步驟設定裝置佈建服務，並擷取其 [範圍識別碼]、模擬 TPM 裝置並擷取其 [註冊識別碼]，然後建立個別註冊。 在您的 IoT 中樞註冊裝置之後，請繼續進行安裝。  

   >[!TIP]
   >在安裝和測試期間，請將執行 TPM 模擬器的視窗保持開啟。 

下列範例顯示使用 Windows 容器自動安裝：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

當您手動安裝及佈建裝置時，可以使用其他參數修改安裝，包括：
* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證
* 跳過 Moby CLI 安裝

如需這些安裝選項的詳細資訊，請繼續閱讀本文，或跳過以了解[所有安裝參數](#all-installation-parameters)。

## <a name="update-an-existing-installation"></a>更新現有的安裝

如果您已先在裝置上安裝 IoT Edge 執行階段，並使用來自 IoT 中樞的身分識別加以佈建，您可以使用簡化的安裝指令碼。 旗標 `-ExistingConfig` 宣告 IoT Edge 組態檔已存在於裝置上。 組態檔包含裝置身分識別，以及憑證和網路設定的相關資訊。 無論您的裝置是最初是手動佈建還是自動佈建，都可以使用此安裝選項。 

如需更多資訊，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。

此範例顯示指向現有組態檔的安裝，並使用 Windows 容器： 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

當您從現有組態檔安裝 IoT Edge 時，可以使用其他參數修改安裝，包括：
* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄，或 
* 跳過 Moby CLI 安裝。 

您無法使用指令碼參數宣告 IoT Edge 代理程式容器，因為已在先前安裝的組態檔中設定該資訊。 如果您想要修改代理程式容器映像，請在 config.yaml 檔案中執行此操作。 

如需這些安裝選項的詳細資訊，請繼續閱讀本文，或跳過以了解[所有安裝參數](#all-installation-parameters)。

## <a name="offline-installation"></a>離線安裝

在安裝期間會下載四個檔案： 
* IoT Edge 安全性精靈 (iotedgd) zip 
* Moby 引擎 zip
* Moby CLI zip
* Visual C++ 可轉散發套件 (VC 執行階段) msi

您可以提前將一個或所有檔案下載到裝置，然後在包含這些檔案的目錄中，指向安裝指令碼。 安裝程式會先檢查目錄，然後僅下載找不到的元件。 如果四個檔案皆可離線使用，您可以在沒有網際網路連線的情況下進行安裝。 您也可以使用這項功能，來覆寫一或多個線上版本的元件。  

如需最新的安裝檔案以及先前版本的相關資訊，請參閱 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)

若要使用離線元件安裝，請使用 `-OfflineInstallationPath` 參數，並提供檔案目錄的絕對路徑。 例如，

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>所有安裝參數

前幾節介紹常見的安裝案例，示範如何使用參數來修改安裝指令碼。 本節提供可用來安裝 IoT Edge 之有效參數的參考資料表。 如需詳細資訊，請在 PowerShell 視窗中執行 `get-help Install-SecurityDaemon -full`。 

若要使用現有設定來安裝 IoT Edge，您的安裝命令可以使用這些常見參數： 

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **手動** | None | **切換參數**。 必須將每個安裝宣告為手動、DPS 或 existingconfig。<br><br>宣告您將提供裝置連接字串以手動佈建裝置 |
| **Dps** | None | **切換參數**。 必須將每個安裝宣告為手動、DPS 或 existingconfig。<br><br>宣告您將提供裝置佈建服務 (DPS) 的範圍識別碼，以及透過 DPS 佈建的裝置註冊識別碼。  |
| **ExistingConfig** | None | **切換參數**。 必須將每個安裝宣告為手動、DPS 或 existingconfig。<br><br>宣告 config.yaml 檔案已存在於裝置，包含其佈建資訊。 |
| **DeviceConnectionString** | 此連接字串來自 IoT 中樞註冊的 IoT Edge 裝置，以單引號括住 | 此為手動安裝的**必要項目**。 如果您未提供指令碼參數中的連接字串，在安裝期間會提示您輸入一個連接字串。 |
| **ScopeId** | 此範圍識別碼來自與您 IoT 中樞相關聯之裝置佈建服務的執行個體。 | 此為 DPS 安裝的**必要項目**。 如果您未提供指令碼參數中的範圍識別碼，在安裝期間會提示您輸入範圍識別碼。 |
| **RegistrationId** | 由您裝置產生的註冊識別碼 | 此為 DPS 安裝的**必要項目**。 如果您未提供指令碼參數中的註冊識別碼，在安裝期間會提示您輸入註冊識別碼。 |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器作業系統，Linux 則為預設值。 對於 Windows 容器，安裝中將包含容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 在 Windows 上執行 Linux 容器是很有用的開發案例，但是在生產中並不支援。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數，安裝程式會檢查安裝所需之 iotedged zip、Moby 引擎 zip、Moby CLI zip 和 VC 執行階段 MSI 檔案的目錄。 如果所有四個檔案皆在目錄中，您可以在離線狀態下安裝 IoT Edge。 您也可以使用此參數覆寫特定元件的線上版本。 |
| **AgentImage** | IoT Edge 代理程式映像 URI | 根據預設，新的 IoT Edge 安裝會為 IoT Edge 代理程式映像使用最新的輪替標籤。 使用此參數，為映像版本設定特定標籤，或提供您自己的代理程式映像。 如需詳細資訊，請參閱[了解 IoT Edge 標籤](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **使用者名稱** | 容器登錄使用者名稱 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的使用者名稱。 |
| **密碼** | 安全密碼字串 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的密碼。 | 
| **SkipMobyCli** | None | 僅適用於 -ContainerOS 設定為 Windows 的情況。 請勿將 Moby CLI (docker.exe) 安裝至 $MobyInstallDirectory。 |

## <a name="verify-successful-installation"></a>確認安裝成功

透過以下命令，檢查 IoT Edge 服務的狀態： 

```powershell
Get-Service iotedge
```

使用以下命令，檢查最後 5 分鐘的服務記錄檔：

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

並使用以下項目列出執行中的模組：

```powershell
iotedge list
```

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從 Windows 裝置中移除 IoT Edge 安裝，請從管理 PowerShell 視窗中使用下列命令。 此命令會移除 IoT Edge 執行階段，以及您現有的設定和 Moby 引擎資料。 

```PowerShell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

如果您想要在裝置上重新安裝 IoT Edge，請忽略 `-DeleteConfig` 和 `-DeleteMobyDataRoot` 參數，以便您可以在日後使用現有設定資訊，重新安裝安全性精靈。 

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您對於 IoT Edge 是否正確安裝有任何問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
