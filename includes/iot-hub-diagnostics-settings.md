---
title: 包含檔案
description: 包含檔案
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 150b800bfa6bfa20f10dbba7e8d55981ecb9df34
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56422865"
---
### <a name="enable-logging-with-diagnostics-settings"></a>利用診斷設定啟用記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

2. 選取 [診斷設定]。

3. 選取 [開啟診斷]。

   ![開啟診斷](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. 指定診斷設定的名稱。

5. 選擇您要傳送記錄檔的位置。 您可以選取這三個選項的任意組合：

   * 封存至儲存體帳戶
   * 串流至事件中樞
   * 傳送至 Log Analytics

6. 選擇您想要監視哪些作業，並啟用這些作業的記錄。 診斷設定可以報告的作業為：

   * 連線
   * 裝置遙測
   * 雲端到裝置的訊息
   * 裝置身分識別作業
   * 檔案上傳
   * 訊息路由
   * 雲端到裝置對應項作業
   * 裝置到雲端對應項作業
   * 對應項作業
   * 工作的作業
   * 直接方法  
   * 分散式追蹤 (預覽)

6. 儲存新設定。 

如果您想要使用 PowerShell 開啟診斷設定，請使用下列程式碼：

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

新的設定大約會在 10 分鐘內生效。 之後，記錄就會顯示在 [診斷設定] 刀鋒視窗上已設定的封存目標中。 如需有關設定診斷功能的詳細資訊，請參閱[收集並取用來自 Azure 資源的記錄資料](../articles/azure-monitor/platform/diagnostic-logs-overview.md)。