---
title: 使用 Log Analytics 監視 Azure Service Fabric 上的容器 | Microsoft Docs
description: 使用 Log Analytics 以監視在 Azure Service Fabric 叢集上執行的容器。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: d5fd55ec93ce07e30e4c6f123f9be8492581053c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972247"
---
# <a name="monitor-containers-with-log-analytics"></a>使用 Log Analytics 監視容器
 
本文涵蓋設定 Azure Log Analytics 容器監視解決方案以檢視容器事件所需的步驟。 若要設定您的叢集來收集容器事件，請參閱此[逐步教學課程](service-fabric-tutorial-monitoring-wincontainers.md)。 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

## <a name="set-up-the-container-monitoring-solution"></a>設定容器監視解決方案

> [!NOTE]
> 您需要為您的叢集設定 Log Analytics，並在您的節點上部署 Log Analytics 代理程式。 如果尚未完成，請先按照[設定 Log Analytics](service-fabric-diagnostics-oms-setup.md) 以及[將 Log Analytics 代理程式新增至叢集](service-fabric-diagnostics-oms-agent.md)中的步驟進行。

1. 使用 Log Analytics 與 Log Analytics 代理程式設定叢集之後，請部署您的容器。 移至下一步之前，請等待容器部署完成。

2. 在 Azure Marketplace 中，搜尋 [容器監視解決方案]，然後按一下顯示在 [監視 + 管理] 類別下的 [容器監視解決方案] 資源。

    ![新增容器解決方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 將解決方案建立在已經為叢集建立的相同工作區內。 此變更會自動觸發代理程式，以開始收集容器上的 Docker 資料。 大約 15 分鐘左右，您應該會看到帶有傳入記錄和統計資料的解決方案亮起，如以下圖像所示。

    ![基本 Log Analytics 儀表板](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

代理程式會收集數個容器專屬的記錄檔，您可以在 Log Analytics 中查詢這些記錄檔，或用來視覺化效能指標。 所收集的記錄類型有：

* ContainerInventory︰顯示容器位置、名稱和映像的相關資訊
* ContainerImageInventory︰已部署映像相關資訊，包括 ID 或大小
* ContainerLog︰特定的錯誤記錄檔、 docker 記錄檔 (stdout 等) 及其他項目
* ContainerServiceLog：已執行的 docker 精露命令
* 效能︰包括容器 CPU、記憶體、網路流量、磁碟 I/O，以及主機電腦的自訂計量



## <a name="next-steps"></a>後續步驟
* 深入了解 [Log Analytics 容器解決方案](../azure-monitor/insights/containers.md)。
* 深入了解 Service Fabric 上的容器協調流程 - [Service Fabric 與容器](service-fabric-containers-overview.md)
* 熟悉 Log Analytics 的[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)功能
* 設定 Log Analytics，以設定[自動化警示](../log-analytics/log-analytics-alerts.md)規則來協助偵測與診斷