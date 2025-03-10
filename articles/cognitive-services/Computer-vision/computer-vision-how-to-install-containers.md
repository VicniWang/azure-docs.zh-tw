---
title: 如何安裝及執行容器 - 電腦視覺
titlesuffix: Azure Cognitive Services
description: 本逐步解說教學課程的內容包含如何下載、安裝及執行適用於電腦視覺的容器。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 02/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2a786d383d103f9b45ea7b13de24b8de9c9e9f5e
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445367"
---
# <a name="install-and-run-recognize-text-containers"></a>安裝及執行辨識文字容器

電腦視覺的辨識文字部分也能以 Docker 容器的形式取得。 您可以使用它來從不同表面和背景的各種物件影像 (如收據、海報和名片)，偵測及擷取印刷文字。  
> [!IMPORTANT]
> 辨識文字容器目前只適用於英文。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

使用辨識文字容器之前，您必須符合下列必要條件：

|必要|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|辨識文字資源 |若要使用此容器，您必須具備：<br><br>[_辨識文字_](vision-api-how-to-topics/howtosubscribe.md) Azure 資源，用來取得相關聯的計費金鑰和計費端點 URI。 這兩個值可在 Azure 入口網站的 [辨識文字概觀和金鑰] 頁面上取得，需要這些值才能啟動容器。<br><br>**{BILLING_KEY}**：資源金鑰<br><br>**{BILLING_ENDPOINT_URI}**：端點 URI 範例為：`https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明每個「辨識文字」容器的最低和建議的 CPU 核心與記憶體配置。

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
|辨識文字|1 核心，8 GB 記憶體，0.5 TPS|2 核心，8 GB 記憶體，1 TPS|

每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。


## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

「辨識文字」適用的容器映像可供使用。 

| 容器 | 存放庫 |
|-----------|------------|
|辨識文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下載容器映像。


### <a name="docker-pull-for-the-recognize-text-container"></a>辨識文字容器的 Docker Pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](computer-vision-resource-container-config.md)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 此命令會使用下列參數：

| Placeholder | 值 |
|-------------|-------|
|{BILLING_KEY} | 此金鑰用來啟動容器，您可以在 Azure 入口網站的 [辨識文字金鑰] 頁面上取得。  |
|{BILLING_ENDPOINT_URI} | 計費端點 URI 值。|

請以您自己的值取代下列範例 `docker run` 命令中的參數。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 從容器映像執行辨識容器
* 配置一個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

`docker run` 命令有相關[範例](./computer-vision-resource-container-config.md#example-docker-run-commands)可供參考。 

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

針對容器 API 請使用主機 https://localhost:5000。

### <a name="asynchronous-text-recognition"></a>非同步文字辨識

您可以將 `POST /vision/v2.0/recognizeText` 和 `GET /vision/v2.0/textOperations/*{id}*` 作業一同使用，以非同步地辨識影像中的印刷文字，類似電腦視覺服務使用那些相對應的 REST 作業的方式。 辨識文字容器目前只辨識印刷文字，而非手寫文字，因此辨識文字容器會忽略為電腦視覺服務作業通常會指派的 `mode` 參數。

### <a name="synchronous-text-recognition"></a>同步文字辨識

您可以使用 `POST /vision/v2.0/recognizeTextDirect` 作業同步地辨識影像中的印刷文字。 因為此作業是同步的，所以此作業的要求本文和 `POST /vision/v2.0/recognizeText` 作業的相同，但此作業的回應主體本文和 `GET /vision/v2.0/textOperations/*{id}*` 作業傳回的相同。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](./computer-vision-resource-container-config.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。 

## <a name="containers-api-documentation"></a>容器的 API 文件

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>計費

辨識文字容器會使用您 Azure 帳戶上的_辨識文字_資源傳送計費資訊至 Azure。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](./computer-vision-resource-container-config.md)。

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行辨識文字容器的概念和工作流程。 摘要說明：

* 辨識文字提供 Docker 適用的 Linux 容器，以封裝辨識文字。
* 容器映像可從 Azure 中的 Microsoft Container Registry (MCR) 下載取得。
* 容器映像是在 Docker 中執行。
* 您可以指定容器的主機 URI，來使用 REST API 或 SDK 呼叫辨識文字容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](computer-vision-resource-container-config.md)以了解組態設定
* 檢閱[電腦視覺概觀](Home.md)，以深入了解辨識印刷和手寫的文字  
* 參閱[電腦視覺 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)，以取得容器支援的方法之詳細資訊。
* 參閱[常見問題集 (FAQ)](FAQ.md) 來解決與電腦視覺功能相關的問題。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
