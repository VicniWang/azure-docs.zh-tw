---
title: (已淘汰) 監視 Azure DC/OS 叢集 - Operations Management
description: 使用 Log Analytics 監視 Azure Container Service DC/OS 叢集。
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1278c788c0d36b2cadf860e379791ecd823a510b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113836"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(已淘汰) 使用 Log Analytics 監視 Azure Container Service DC/OS 叢集

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。 容器解決方案是 Log Analytics 中的解決方案，可協助您在單一位置檢視容器詳細目錄、效能和記錄檔。 您可以在集中式位置檢視記錄檔以稽核、對容器進行疑難排解，並尋找壟斷及佔用過量主機資源的容器。

![](media/container-service-monitoring-oms/image1.png)

如需容器解決方案的詳細資訊，請參閱[容器解決方案 Log Analytics](../../azure-monitor/insights/containers.md)。

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>從 DC/OS Universe 設定 Log Analytics


本文假設您已在叢集上設定 DC/OS，並已部署簡單的 Web 容器應用程式。

### <a name="pre-requisite"></a>必要條件
- [Microsoft Azure 訂用帳戶](https://azure.microsoft.com/free/)：您可以免費取得訂用帳戶。  
- Log Analytics 工作區設定 - 請參閱下方的「步驟 3」
- 已安裝 [DC/OS CLI](http://docs.mesosphere.com/1.12/cli)。

1. 在 DC/OS 儀表板中，按一下 [Universe] 並搜尋 'OMS'，如下所示。

   >[!NOTE]
   >OMS 現在稱為「Log Analytics」。

 ![](media/container-service-monitoring-oms/image2.png)

2. 按一下 [Install] 。 您將會看到含有版本資訊和一個 [安裝套件] 或 [進階安裝] 按鈕的快顯。 按一下 [Advanced Installation]\(進階安裝)，就會跳轉至 [OMS specific configuration properties]\(OMS 特定組態屬性) 頁面。

 ![](media/container-service-monitoring-oms/image3.png)

 ![](media/container-service-monitoring-oms/image4.png)

3. 這裡將要求您輸入 `wsid` (Log Analytics 工作區識別碼) 和 `wskey` (工作區識別碼的主索引鍵)。 若要取得 `wsid` 和 `wskey`，您需要在 <https://mms.microsoft.com> 建立帳戶。
請遵循步驟來建立帳戶。 建立帳戶之後，您必須依序按一下 [Settings]\(設定)、[Connected Sources]\(連接的來源)、[Linux Servers]\(Linux 伺服器)，以取得您的 `wsid` 和 `wskey`，如下所示。

 ![](media/container-service-monitoring-oms/image5.png)

4. 選取您想要的執行個體數目，然後按一下 [Review and Install]\(檢閱並安裝) 按鈕。 一般而言，執行個體數目應等於您代理程式叢集中的 VM 數目。 適用於 Linux 的 Log Analytics 代理程式會在其想要收集資訊的個別 VM 上安裝為獨立容器，以監視和記錄資訊。

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>設定簡單的 Log Analytics 儀表板

在 VM 上安裝適用於 Linux 的 Log Analytics 代理程式之後，下一個步驟是設定 Log Analytics 儀表板。 您可以透過 Azure 入口網站設定儀表板。

### <a name="azure-portal"></a>Azure 入口網站 

登入 Azure 入口網站，網址為：<https://portal.microsoft.com/>。 移至 [Marketplace]，選取 [監視 + 管理]，然後按一下 [查看全部]。 接著在搜尋中輸入 `containers` (容器)。 您會在搜尋結果中看到 "containers" (容器)。 選取 [Containers]\(容器)，然後按一下 [建立]。

![](media/container-service-monitoring-oms/image9.png)

按一下 [建立] 後，它會要求您提供您的工作區。 選取您的工作區，或是如果您沒有工作區，請建立新的工作區。

![](media/container-service-monitoring-oms/image10.PNG)

選取您的工作區後，按一下 [建立]。

![](media/container-service-monitoring-oms/image11.png)

如需 Log Analytics 容器解決方案的詳細資訊，請參閱[容器解決方案 Log Analytics](../../azure-monitor/insights/containers.md)。

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>如何使用 ACS DC/OS 調整 Log Analytics 代理程式 

如果您需要的已安裝 Log Analytics 代理程式少於實際的節點數，或是您正透過新增更多 VM 來相應增加虛擬機器擴展集，則可透過調整 `msoms` 服務來達成。

您可以移至 [Marathon] 或 [DC/OS UI Services]\(DC/OS UI 服務) 索引標籤來擴充節點數。

![](media/container-service-monitoring-oms/image12.PNG)

這樣將會部署到尚未部署 Log Analytics 代理程式的其他節點。

## <a name="uninstall-ms-oms"></a>解除安裝 MS OMS

若要解除安裝 MS OMS，請輸入以下命令：

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>請告訴我們！
哪些資訊是有用的？ 缺少哪些資訊？ 我們還能提供您什麼樣的實用資訊？ 請透過 <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a> 告訴我們。

## <a name="next-steps"></a>後續步驟

 由於您已設定 Log Analytics 來監視您的容器，[請檢視您的容器儀表板](../../azure-monitor/insights/containers.md)。
