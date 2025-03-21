---
title: 針對生產環境準備裝置和部署 - Azure IoT Edge | Microsoft Docs
description: 了解如何將 Azure IoT Edge 解決方案從開發層面轉移到生產環境，包括使用適當憑證設定您的裝置，以及針對日後的程式碼更新來研擬部署計畫。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 86b33bfa0f5383ac68080e2f8f7f9a004a1364a0
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652595"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>準備在生產環境中部署 IoT Edge 解決方案

當你準備將 IoT Edge 解決方案從開發層面轉移到生產環境時，請確保其設定可以提供持續性效能。

本文提供的資訊並非完全相同。 為了協助您設定優先權，每個區段都會以清單開始，並將工作分成兩個部分：生產前需完成的**重要**事項，或是您需要知道的**實用**事項。

## <a name="device-configuration"></a>裝置設定

IoT Edge 裝置可以是任何項目，包括 Raspberry Pi、膝上型電腦或伺服器上執行的虛擬機器。 您可以實際或透過虛擬連線存取裝置，或者裝置可能長時間受到隔離。 無論是哪種方式，您都需要確保其設定能夠妥善執行。 

* **重要**
    * 安裝生產憑證
    * 研擬裝置管理計畫
    * 使用 Moby 作為容器引擎

* **實用**
    * 選擇上游通訊協定

### <a name="install-production-certificates"></a>安裝生產憑證

生產環境中的每個 IoT Edge 裝置都要安裝裝置憑證授權單位 (CA) 的憑證。 此 CA 憑證接著會在 config.yaml 檔案的 IoT Edge 執行階段中宣告。 為了簡化開發和測試流程，如果 config.yaml 檔案中沒有任何宣告的憑證，IoT Edge 執行階段會建立暫時憑證。 不過，這些暫時憑證三個月後就會過期，而且對於生產情節並不安全。 

若要了解裝置 CA 憑證的角色，請參閱 [Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

如需如何在 IoT Edge 裝置上安裝憑證，並參考 config.yaml 檔案中的憑證等詳細資訊，請參閱[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)。 無論裝置是否作為閘道使用，設定憑證的步驟都相同。 本文為了建立範例憑證所提供的指令碼僅供測試之用。 請勿在生產環境中使用這些範例憑證。 

### <a name="have-a-device-management-plan"></a>研擬裝置管理計畫

在生產環境中設置任何裝置前，應先了解如何管理未來的更新作業。 若是 IoT Edge 裝置，要更新的元件清單可能包含：

* 裝置軔體
* 作業系統程式庫
* 容器引擎，例如 Moby
* IoT Edge 精靈
* CA 憑證

如需 IoT Edge 精靈的更新步驟，請參閱[更新 IoT Edge 執行階段](how-to-update-iot-edge.md)。 目前更新 IoT Edge 精靈的方法需要實際存取或透過 SSH 存取 IoT Edge 裝置。 如果您有多項裝置需要更新，請考慮將更新步驟新增至指令碼，或是使用 Ansible 等自動化工具執行大規模更新。

### <a name="use-moby-as-the-container-engine"></a>使用 Moby 作為容器引擎

在裝置上安裝容器引擎是任何 IoT Edge 裝置的必要條件。 生產環境僅支援 Moby 引擎。 Docker 等其他容器引擎可搭配 IoT Edge 使用，也可以使用這些引擎進行開發。 搭配 Azure IoT Edge 使用時可重新分配 Moby 引擎，而且 Microsoft 可提供此引擎的相關服務。 不支援在 IoT Edge 裝置上使用其他容器引擎。

### <a name="choose-upstream-protocol"></a>選擇上游通訊協定

可針對 Edge 代理程式和 Edge 中樞設定 IoT 中樞上游通訊用的通訊協定 (和使用的連接埠)。 預設的通訊協定是 AMQP，但您可以根據網路設定變更。 

這兩個執行階段模組都有 **UpstreamProtocol** 環境變數。 有效的變數值為： 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

為裝置本身中 config.yaml 檔案的 Edge 代理程式設定 UpstreamProtocol 變數。 舉例來說，如果您的 IoT Edge 裝置位於封鎖 AMQP 連接埠的 Proxy 伺服器後方，可能需要設定 Edge 代理程式透過 WebSocket (AMQPWS) 使用 AMQP，以建立 IoT 中樞的初始連線。 

一旦您的 IoT Edge 裝置連線，請務必在未來的部署中繼續設定這兩個執行階段模組的 UpstreamProtocol 變數。 [設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

## <a name="deployment"></a>部署

* **實用**
    * 與上游的通訊協定一致
    * 減少 Edge 中樞使用的記憶體空間
    * 請勿使用模組映像的偵錯版本

### <a name="be-consistent-with-upstream-protocol"></a>與上游的通訊協定一致

如果您要設定 IoT Edge 裝置上的 Edge 代理程式使用預設 AMQP 以外的其他通訊協定，就應該在所有後續部署中宣告相同的通訊協定。 舉例來說，如果您的 IoT Edge 裝置位於封鎖 AMQP 連接埠的 Proxy 伺服器後方，可能是設定該裝置透過 WebSocket (AMQPWS) 使用 AMQP 連線。 將模組部署到裝置時，如果未針對 Edge 代理程式和 Edge 中樞設定相同的 APQPWS 通訊協定，預設的 AMQP 會覆寫此設定並防止您重新連線。 

您只需要針對 Edge 代理程式和 Edge 中樞模組設定 UpstreamProtocol 環境變數。 任何其他模組會採用執行階段模組中設定的任何通訊協定。 

[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

### <a name="reduce-memory-space-used-by-edge-hub"></a>減少 Edge 中樞使用的記憶體空間

如果您正在部署可用記憶體有限的限制裝置，可以將 Edge 中樞設為採用更簡化的容量執行，並減少使用的磁碟空間。 不過這些設定會限制 Edge 中樞的效能，所以您可以找出解決方案適用的最佳平衡。 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>請勿在受限裝置上進行效能最佳化

根據預設，Edge 中樞已針對效能最佳化，而且會嘗試配置大量的記憶體。 這項設定會在 Raspberry Pi 等小型裝置上造成穩定性問題。 如果您正在部署資源有限的裝置，可以在 Edge 中樞上將 **OptimizeForPerformance** 環境變數設為 **false**。 

如需詳細資訊，請參閱[資源受限裝置的穩定性問題](troubleshoot.md#stability-issues-on-resource-constrained-devices)。

#### <a name="disable-unused-protocols"></a>停用未使用的通訊協定

最佳化 Edge 中樞效能並減少記憶體使用量的另一種方法是，針對解決方案中未使用的任何通訊協定關閉通訊協定標頭 (protocol head)。 

通訊協定標頭的設定方式是針對您部署資訊清單中的 Edge 中樞模組，設定布林環境變數設定。 這三個變數分別是：

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

這三個變數都有*兩條底線*，而且可以設為 true 或 false。 

#### <a name="reduce-storage-time-for-messages"></a>減少訊息的儲存時間

如果訊息因故無法傳遞到 IoT 中樞，Edge 中樞模組會暫時儲存訊息。 在未傳遞訊息過期之前，您可以設定 Edge 中樞保留訊息的時間。 如果您的裝置記憶體有問題，可以減少 Edge 中樞模組對應項中的 **timeToLiveSecs** 值。 

timeToLiveSecs 參數的預設值是 7200 秒，也就是兩小時。 

### <a name="do-not-use-debug-versions-of-module-images"></a>請勿使用模組映像的偵錯版本

從測試情節轉移至生產環境情節時，請務必移除部署資訊清單中的偵錯設定。 檢查部署資訊清單中的模組映像是否都沒有**\.偵錯**尾碼。 如果您已新增建立選項，以公開模組中的連接埠進行偵錯時，也請移除這些建立選項。 

## <a name="container-management"></a>容器管理

* **重要**
    * 管理您的容器登錄存取權
    * 使用標籤來管理版本

### <a name="manage-access-to-your-container-registry"></a>管理您的容器登錄存取權

將模組部署到生產 IoT Edge 裝置之前，請務必控管容器登錄的存取權，避免他人存取或變更您的容器映像。 您可以使用非公開的私人容器登錄來管理容器映像。 

在教學課程和其他說明文件中，我們會指示您在 IoT Edge 裝置使用與開發電腦上所用相同的容器登錄認證。 這些指示僅協助您更輕鬆地設定測試和開發環境，在生產情節中不應遵照這些指示。 當應用程式或服務以自動化或其他自動方式提取容器映像時，Azure Container Registry 建議[使用服務主體進行驗證](../container-registry/container-registry-auth-service-principal.md)，如同 IoT Edge 裝置一般。 為容器登錄建立具備唯讀存取權的服務主體，並且在部署資訊清單中提供該使用者名稱和密碼。

### <a name="use-tags-to-manage-versions"></a>使用標籤來管理版本

標籤是一種 Docker 概念，可用來區別 Docker 容器版本。 標籤是類似 **1.0** 的尾碼，位於容器存放庫的末端。 例如 **mcr.microsoft.com/azureiotedge-agent:1.0**。 標籤可以變動，而且可以隨時更改並指向另一個容器，因此當您更新模組映像時，您的團隊應同意要遵循的慣例。 

標籤也可協助您強制更新 IoT Edge 裝置。 當您將更新版模組推送至容器登錄時，請以遞增方式處理標記。 接著，將新部署和遞增的標籤一併推送到您的裝置。 容器引擎會將遞增的標籤辨識為新版本，並將最新的模組版本向下提取至您的裝置。 

如需標籤慣例的範例，請參閱[更新 IoT Edge 執行階段](how-to-update-iot-edge.md#understand-iot-edge-tags)，了解 IoT Edge 如何使用循環標籤 (rolling tag) 和特定標籤來追蹤版本。 

## <a name="networking"></a>網路功能

* **實用**
    * 檢閱輸出/輸入設定
    * 允許清單連線
    * 設定 Proxy 通訊

### <a name="review-outboundinbound-configuration"></a>檢閱輸出/輸入設定

Azure IoT 中樞和 IoT Edge 之間的通訊通道一律會設定為輸出。 在大部分的 IoT Edge 情節下，必要的連線只有三個。 容器引擎必須和容納模組映像的容器登錄連線。 IoT Edge 執行階段需要和 IoT 中樞連線才能擷取裝置組態資訊，以及傳送訊息和遙測。 如果您使用自動佈建，IoT Edge 精靈必須連線至裝置佈建服務。 如需詳細資訊，請參閱[防火牆和連接埠組態規則](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)。

### <a name="whitelist-connections"></a>允許清單連線

如果您的網路設定要求您明確地將 IoT Edge 裝置連線加入允許清單，請檢閱下列的 IoT Edge 元件清單：

* **IoT Edge 代理程式**可能會透過 WebSockets 持續以 AMQP/MQTT 連線方式連線到 IoT 中樞。 
* **IoT Edge 中樞**可能會透過 WebSockets 持續以 AMQP 連線或多種 MQTT 連線方式連線到 IoT 中樞。 
* **IoT Edge 精靈**對 IoT 中樞進行 HTTPS 間歇性呼叫。 

在這三個案例中，DNS 名稱會符合模式 \*.azure-devices.net。 

此外，**容器引擎**會透過 HTTPS 呼叫容器登錄。 若要擷取 IoT Edge 執行階段容器映像，DNS 名稱為 mcr.microsoft.com。 容器引擎會依部署中的設定，連線到其他登錄。 

此檢查清單是防火牆規則的起始點：

   | URL (\* = 萬用字元) | 輸出 TCP 連接埠 | 使用量 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft 容器登錄 |
   | global.azure-devices-provisioning.net  | 443 | DPS 存取 (選用) |
   | \*.azurecr.io | 443 | 個人和第三方容器登錄 |
   | \*.blob.core.windows.net | 443 | 映像差異的下載 | 
   | \*.azure-devices.net | 5671、8883、443 | IoT 中樞存取 |
   | \*.docker.io  | 443 | Docker 存取 (選用) |

### <a name="configure-communication-through-a-proxy"></a>設定 Proxy 通訊

如果要在使用 Proxy 伺服器的網路上部署您的裝置，裝置必須能夠透過 Proxy 通訊，以便觸達 IoT 中樞和容器登錄。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。

## <a name="solution-management"></a>解決方案管理

* **實用**
    * 設定記錄檔與診斷
    * 請考量測試和 CI/CD 管線

### <a name="set-up-logs-and-diagnostics"></a>設定記錄檔與診斷

在 Linux 中，IoT Edge 精靈會使用 journald 作為預設的記錄驅動程式。 您可以使用命令列工具 `journalctl` 查詢精靈記錄檔。 在 Windows 中，IoT Edge 精靈會使用 PowerShell 診斷。 使用 `Get-WinEvent` 查詢精靈記錄檔。 IoT Edge 模組會使用 JSON 驅動程式進行記錄，此為 Docker 預設值。  

正在測試 IoT Edge 部署時，通常可以存取您的裝置來擷取記錄檔並進行疑難排解。 在部署情節中，可能不提供該選項。 請考慮要如何收集生產環境中的裝置相關資訊。 其中一個選項是使用能夠收集其他模組資訊，並將資訊傳送至雲端的記錄模組。 其中一個記錄模組範例是 [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics)，您也可以設計自己的專用模組。 

如果您擔心資源有限裝置上的記錄檔變得太大，有幾個選項可以減少記憶體使用量。 

* 您可以特別限制 Docker 精靈本身中所有 Docker 記錄檔的 大小。 若是 Linux，可以在 `/etc/docker/daemon.json` 設定精靈。 若是 Windows，則是 `C:\ProgramData\docker\confige\daemon.json`。 
* 如果您想要調整每個容器的記錄檔大小，可以在每個模組的 CreateOptions 中進行。 
* 將 journald 設為 Docker 預設的記錄驅動程式，便可讓 Docker 自動管理記錄檔。 
* 安裝 Docker 適用的 logrotate 工具，即可定期移除舊的裝置記錄檔。 使用下列檔案規格： 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>請考量測試和 CI/CD 管線

若要提高 IoT Edge 部署情節的效率，請考量將您的生產環境部署整合至您的測試和 CI/CD 管線。 Azure IoT Edge 支援 Azure DevOps 等多個 CI/CD 平台。 如需詳細資訊，請參閱 [Azure IoT Edge 的持續整合與持續部署](how-to-ci-cd.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [IoT Edge 自動部署](module-deployment-monitoring.md)。
* 了解 IoT Edge 如何支援[持續整合與持續部署](how-to-ci-cd.md)。
