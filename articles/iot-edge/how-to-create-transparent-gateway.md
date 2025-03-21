---
title: 建立透明閘道裝置 - 使用 Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 裝置作為可處理來自下游裝置之資訊的透明閘道
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a42f4ce85214ad2a8c5692736b7d36101ccb62ed
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556215"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>設定 IoT Edge 裝置作為透明閘道

本文提供詳細的指示來設定 IoT Edge 裝置作為透明閘道，讓其他裝置與 IoT 中樞進行通訊。 在本文中，*IoT Edge 閘道*一詞是指當作透明閘道使用的 IoT Edge 裝置。 如需詳細資訊，請參閱[如何使用 IoT Edge 裝置作為閘道](./iot-edge-as-gateway.md)，其中提供概念性的概觀。

>[!NOTE]
>目前狀況：
> * 如果閘道與 IoT 中樞中斷連線，則下游裝置無法透過閘道進行驗證。
> * 已啟用 Edge 的裝置無法連線到 IoT Edge 閘道。 
> * 下游裝置無法使用檔案上傳。

對於要當作閘道運作的裝置，它必須能夠安全地連線到下游裝置。 Azure IoT Edge 可讓您使用公開金鑰基礎結構 (PKI)，設定這些裝置之間的安全連線。 在此案例中，我們將允許下游裝置連線至作為透明閘道的 IoT Edge 裝置。 為了維護適當的安全性，下游裝置應確認 Edge 裝置的身分識別，因為您應該只希望讓裝置連線至自己的閘道，而不是可能的惡意閘道。

下游裝置可以是使用 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)雲端服務建立身分識別的任何應用程式或平台。 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 就所有實際用途而言，下游裝置甚至可以是在 IoT Edge 閘道裝置本身執行的應用程式。 

您可以建立任何憑證基礎結構，來啟用裝置閘道拓撲所需的信任。 在本文中，我們假設您會使用在 IoT 中樞內用來啟用 [X.509 CA 安全性](../iot-hub/iot-hub-x509ca-overview.md)的相同憑證安裝程式，其中包含與特定 IoT 中樞相關聯的 X.509 CA 憑證 (IoT 中樞擁有者 CA)，以及使用此 CA 和 Edge 裝置的 CA 簽署的一系列憑證。

![閘道憑證設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

閘道在連線起始期間，會向下游裝置出具其 Edge 裝置 CA 憑證。 下游裝置會檢查以確認 Edge 裝置 CA 憑證是由擁有者 CA 憑證所簽署。 此程序可讓下游裝置確認閘道是來自信任的來源。

下列步驟會逐步引導您在正確的位置建立憑證並且加以安裝。

## <a name="prerequisites"></a>必要條件

設定為閘道的 Azure IoT Edge 裝置。 您可以透過下列作業系統的步驟，使用開發電腦或虛擬機器作為 IoT Edge 裝置：
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

您可以使用任何電腦產生憑證，然後再將其複製到您的 IoT Edge 裝置。 

## <a name="generate-certificates-with-windows"></a>使用 Windows 產生憑證

使用本節中的步驟，在 Windows 裝置上產生測試憑證。 您可以在 IoT Edge 裝置本身產生憑證，或使用不同的電腦，並將最終的憑證複製到執行任何支援的作業系統的任何 IoT Edge 裝置。 

本節中所產生的憑證是僅供測試用途。 

### <a name="install-openssl"></a>安裝 OpenSSL

在您用來產生憑證的電腦上，安裝 Windows 適用的 OpenSSL。 您可以透過數種方式來安裝 OpenSSL：

   >[!NOTE]
   >如果您已經在 Windows 裝置上安裝 OpenSSL，則可以略過此步驟，但請確定您的 PATH 環境變數中有 openssl.exe。

* **較簡便：** 下載並安裝任何第三方 OpenSSL 二進位檔，例如，從 [SourceForge 上的這個專案](https://sourceforge.net/projects/openssl/)下載並安裝。 將 openssl.exe 的完整路徑加入至您的 PATH 環境變數。 
   
* **建議配備：** 自行下載 OpenSSL 原始程式碼並在您的電腦上組建二進位檔，或是透過 [vcpkg](https://github.com/Microsoft/vcpkg) 來執行。 下列指示會使用 vcpkg 下載原始程式碼，並在 Windows 電腦上編譯並安裝 OpenSSL，且各項作業都能以輕鬆的步驟完成。

   1. 瀏覽至要安裝 vcpkg 的目錄。 我們將此目錄稱之為 *\<VCPKGDIR>*。 依照指示下載並安裝 [vcpkg](https://github.com/Microsoft/vcpkg)。
   
   2. vcpkg 安裝後，請從 Powershell 提示字元執行下列命令，以安裝 Windows x64 的 OpenSSL 套件。 安裝通常需要約 5 分鐘的時間。

      ```PowerShell
      .\vcpkg install openssl:x64-windows
      ```
   3. 將 `<VCPKGDIR>\installed\x64-windows\tools\openssl` 新增至您的 PATH 環境變數，讓 openssl.exe 檔案可供叫用。

### <a name="prepare-creation-scripts"></a>準備建立指令碼

適用於 C 的 Azure IoT 裝置 SDK 包含您可以用來產生測試憑證的指令碼。 在本節中，您可以複製 SDK 以及設定 PowerShell。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 

2. 複製包含指令碼的 Git 存放庫來產生非生產環境憑證。 這些指令碼可協助您建立用來設定透明閘道的所需憑證。 使用 `git clone` 命令或[下載 ZIP](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip)。 

   ```PowerShell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. 瀏覽至您要使用的目錄。 我們將此目錄稱之為 *\<WRKDIR>*。  所有檔案都會在此目錄中建立。

4. 將組態和指令碼檔案複製到您的工作目錄。 

   ```PowerShell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   如果您已下載 SDK 的 ZIP 檔，則資料夾名稱為 `azure-iot-sdk-c-master`，且路徑的其餘部分會相同。 

5. 設定環境變數 OPENSSL_CONF 使用 openssl_root_ca.cnf 組態檔。

    ```PowerShell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. 啟用 PowerShell 執行指令碼。

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. 將指令碼所使用的函式導入 PowerShell 的全域命名空間中。
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

8. 確認 OpenSSL 已正確安裝，並確定名稱不會與現有的憑證名稱衝突。 如果有問題，指令碼應會說明如何在系統上加以修正。

   ```PowerShell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>建立憑證

在本節中，您要建立三個憑證，然後將它們以鏈結方式連接。 將憑證放入鏈結檔案可讓您輕鬆地在您的 IoT Edge 閘道裝置和任何下游裝置上安裝它們。  

1. 建立擁有者 CA 憑證，並讓其簽署一個中繼憑證。 這些憑證全部放置在 *\<WRKDIR>* 中。

      ```PowerShell
      New-CACertsCertChain rsa
      ```

2. 使用下列命令建立 Edge 裝置 CA 憑證和私密金鑰。 提供閘道裝置的名稱，這將在憑證產生期間，用於命名檔案。 

   ```PowerShell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. 使用下列命令，從擁有者 CA 憑證、中繼憑證和 Edge 裝置 CA 憑證建立憑證鏈結。 

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   指令碼會建立下列憑證和金鑰：
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>使用 Linux 產生憑證

使用本節中的步驟，在 Linux 裝置上產生測試憑證。 您可以在 IoT Edge 裝置本身產生憑證，或使用不同的電腦，並將最終的憑證複製到執行任何支援的作業系統的任何 IoT Edge 裝置。 

### <a name="prepare-creation-scripts"></a>準備建立指令碼

1. 複製包含指令碼的 Git 存放庫來產生非生產環境憑證。 這些指令碼可協助您建立用來設定透明閘道的所需憑證。 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. 瀏覽至您要使用的目錄。 我們將此目錄稱之為 *\<WRKDIR>*。  所有檔案都會在此目錄中建立。
  
3. 將組態和指令碼檔案複製到您的工作目錄。

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. 設定 OpenSSL 使用提供的指令碼產生憑證。 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>建立憑證

在本節中，您要建立三個憑證，然後將它們以鏈結方式連接。 將憑證放入鏈結檔案可讓您輕鬆地在您的 IoT Edge 閘道裝置和任何下游裝置上安裝它們。  

1.  建立擁有者 CA 憑證和一個中繼憑證。 這些憑證放置在 *\<WRKDIR>* 中。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   指令碼會建立下列憑證和金鑰：
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2.  使用下列命令建立 Edge 裝置 CA 憑證和私密金鑰。 提供閘道裝置的名稱，這將在憑證產生期間，用於命名檔案。 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   指令碼會建立下列憑證和金鑰：
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. 從擁有者 CA 憑證、中繼憑證和 Edge 裝置 CA 憑證建立稱為 **new-edge-device-full-chain.cert.pem** 的憑證鏈結。

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>在閘道上安裝憑證

既然您已經建立憑證鏈結，您需要將它安裝在 IoT Edge 閘道裝置上，並將 IoT Edge 執行階段設定為參考新的憑證。 

1. 從 *\<WRKDIR>* 複製下列檔案。 將其儲存在您 IoT Edge 裝置上的任何位置。 我們將您 IoT Edge 裝置上的目的地目錄稱之為 *\<CERTDIR>*。 

   如果您在 Edge 裝置本身產生憑證，可以略過此步驟，並使用工作目錄的路徑。

   * 裝置 CA 憑證 -  `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * 裝置 CA 私密金鑰 - `<WRKDIR>\private\new-edge-device.key.pem`
   * 擁有者 CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 開啟 IoT Edge 安全性精靈組態檔。 

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 將 config.yaml 檔案中的**憑證**屬性設定為您在 IoT Edge 裝置上放置憑證和金鑰檔案所在的路徑。

```yaml
certificates:
  device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>將 Edge 中樞部署至閘道

當您第一次在裝置上安裝 IoT Edge 時，只有一個系統模組會自動啟動：Edge 代理程式。 若要讓您的裝置當作閘道運作，您需要兩個系統模組。 如果您之前還未將任何模組部署至您的閘道裝置，請建立您裝置的部署來啟動第二個系統模組，也就是 Edge 中樞。 部署看起來是空的，因為您沒有在精靈中新增任何模組，但它將會部署兩個系統模組。 

您可以使用 `iotedge list` 命令，檢查哪些模組是在裝置上執行。

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。

2. 移至 [IoT Edge]，並選取要作為閘道的 IoT Edge 裝置。

3. 選取 [設定模組]。

4. 選取 [下一步] 。

5. 在 [指定路由] 頁面中，您應該會有可將所有模組中的所有訊息傳送至 IoT 中樞的預設路由。 如果沒有，請新增下列程式碼，然後選取 [下一步]。

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. 在 [檢閱範本] 頁面中，選取 [提交]。

## <a name="route-messages-from-downstream-devices"></a>從下游裝置路由傳送訊息
IoT Edge 執行階段可以路由傳送從下游裝置送來的訊息，就像路由傳送從模組送來的訊息一樣。 這可讓您在將任何資料傳送到雲端之前，在執行於閘道的模組中執行分析。 

目前，針對下游裝置所傳送的訊息，您進行路由傳送的方式是將它們與模組所傳送的訊息做區分。 模組所傳送的訊息全都包含名為 **connectionModuleId** 系統屬性，但下游裝置所傳送的訊息則無此屬性。 您可以使用路由的 WHERE 子句來排除任何包含該系統屬性的訊息。 

以下路由會用來將訊息從任何下游裝置傳送給名為 `ai_insights` 的模組。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

如需有關訊息路由的詳細資訊，請參閱[部署模組及建立路由](./module-composition.md#declare-routes)。

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>後續步驟

既然您讓 IoT Edge 裝置當作透明閘道運作，您需要將下游裝置設定為信任閘道，並將訊息傳送到該裝置。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。
