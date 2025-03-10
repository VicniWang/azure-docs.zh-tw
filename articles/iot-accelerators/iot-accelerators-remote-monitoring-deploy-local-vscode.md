---
title: 本機部署遠端監視解決方案 (Visual Studio Code) - Azure | Microsoft Docs
description: 此操作指南會示範如何使用 Visual Studio Code，將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6e2fafa398b09d0822c4582e196345b812e6fc52
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392499"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>本機部署遠端監視解決方案加速器 - Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文會示範如何將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。 您可了解如何在 Visual Studio Code 中執行微服務。 本機微服務部署會使用下列雲端服務：IoT 中樞、Cosmos DB、Azure 串流分析，以及 Azure 時間序列深入解析。

## <a name="prerequisites"></a>必要條件

若要部署遠端監視解決方案加速器所使用的 Azure 服務，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>電腦設定

若要完成本機部署，您必須在本機開發機器上安裝下列工具：

* [Git](https://git-scm.com/)
* [.Net Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](http://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code 的 C# 擴充功能](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) - 此軟體是指令碼用來建立 Azure 資源之 PCS CLI 的先決條件。 不要使用 Node.js v10

> [!NOTE]
> Visual Studio Code 適用於 Windows、Mac 和 Ubuntu。

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>執行微服務

在本節中，您會執行遠端監視微服務。 原生執行 Web UI、Docker 中的裝置模擬服務，以及 Visual Studio Code 中的微服務。

### <a name="build-the-code"></a>建置程式碼

在命令提示字元中瀏覽至 azure-iot-pcs-remote-monitoring-dotnet\services，並執行下列命令來建置程式碼。

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>在本機電腦上部署所有其他微服務

下列步驟示範如何在 Visual Studio 2017 中執行遠端監視微服務：

1. 啟動 Visual Studio Code。
1. 從您在 VS Code 中的本機複本開啟 **azure-iot-pcs-remote-monitoring-dotnet** 模組。
1. 從 scripts\local\launch\idesettings\vscode\. 複製 **launch.json** 和 **tasks.json** 檔案。 建立新的資料夾 **azure-iot-pcs-remote-monitoring-dotnet\.vscode** 並在其中貼上檔案。
1. 在 VS Code 中開啟偵錯面板，然後執行 [執行所有微服務] 組態。 此組態會在 Docker 中執行裝置模擬微服務，並且在偵錯工具中執行其他微服務。

例如，[偵錯主控台] 中 [驗證] 服務的輸出如下所示：

[![Deploy-Local-Auth-Service](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>執行 Web UI

在此步驟中，您會啟動 Web UI。 瀏覽至您本機複本中的 **azure-iot-pcs-remote-monitoring-dotnet\webui** 資料夾，然後執行下列命令：

```cmd
npm install
npm start
```

啟動完成時，您的瀏覽器會顯示頁面 **http://localhost:3000/dashboard**。 此頁面上的錯誤一如預期。 若要檢視沒有錯誤的應用程式，請完成下列步驟。

### <a name="configure-and-run-nginx"></a>設定和執行 NGINX

設定反向 Proxy 伺服器，以連結 Web 應用程式與在本機電腦上執行的微服務：

* 將 **webui\scripts\localhost** 資料夾中的 **nginx.conf** 檔案複製到 **nginx\conf** 安裝目錄。
* 執行 **nginx**。

如需執行 **nginx** 的詳細資訊，請參閱[適用於 Windows 的 nginx](http://nginx.org/en/docs/windows.html) \(英文\)。

### <a name="connect-to-the-dashboard"></a>連線至儀表板

若要存取遠端監視解決方案儀表板，請在瀏覽器中瀏覽至 [http://localhost:9000](http://localhost:9000)。

## <a name="clean-up"></a>清除

為避免不必要的費用，當您完成測試後，請從您的 Azure 訂用帳戶中移除雲端服務。 若要移除服務，請瀏覽到 [Azure 入口網站](https://ms.portal.azure.com)，然後刪除 **start.cmd** 指令碼建立的資源群組。

您也可以將您從 GitHub 複製原始程式碼時，所建立的遠端監視存放庫本機副本刪除。

## <a name="next-steps"></a>後續步驟

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](quickstart-remote-monitoring-deploy.md)。