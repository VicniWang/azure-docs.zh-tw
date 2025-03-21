---
title: 在 Azure 入口網站中設定適用於 MySQL 的 Azure 資料庫的計量警示
description: 本文描述如何從 Azure 入口網站，設定和存取「適用於 MySQL 的 Azure 資料庫」的計量警示。
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: 1287df97f10033472c9717309688cf89887a66ed
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54242959"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>使用 Azure 入口網站來設定適用於 MySQL 之 Azure 資料庫的計量警示 

本文說明如何使用 Azure 入口網站來設定「適用於 MySQL 的 Azure 資料庫」警示。 您可以收到以 Azure 服務的監視計量為基礎的警示。

當所指定計量的值超出您指派的臨界值時，就會觸發警示。 不論是一開始符合條件時，還是之後不再符合條件時，都會觸發警示。 

您可以設定讓警示在觸發時執行下列動作︰
* 傳送電子郵件通知給服務管理員和共同管理員
* 傳送電子郵件給您指定的其他電子郵件。
* 呼叫 Webhook

您可以透過下列方式，來設定及取得警示規則的相關資訊：
* [Azure 入口網站](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>從 Azure 入口網站建立計量的警示規則
1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您想要監視的「適用於 MySQL 的 Azure 資料庫」伺服器。

2. 在資訊看板的 [監視] 區段底下，選取 [警示規則]，如下所示：

   ![選取 [警示規則]](./media/howto-alert-on-metric/2-alert-rules.png)

3. 選取 [新增計量警示] (+ 圖示)。

4. [建立規則] 頁面隨即開啟，如下所示。 填寫必要資訊：

   ![[新增計量警示] 表單](./media/howto-alert-on-metric/4-add-rule-form.png)

5. 在 [條件] 區段中，選取 [新增條件]。

6. 從要提醒的訊號清單中選擇一個計量。 在此範例中，選取 "Storage percent"。
   
   ![選取計量](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. 設定警示邏輯，包括**條件** (例如， "Greater than")、**閾值** (例如， 85 percent)、**時間彙總**，觸發警示之前，必須滿足計量規則的**期間** (例如， "Over the last 30 minutes")，以及**頻率**。
   
   完成時選取 [完成]。

   ![選取計量](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. 在 [動作群組] 區段中，選取 [建立] 建立新的群組，以接收警示通知。

9. 使用名稱、簡短名稱、訂用帳戶和資源群組填寫 [新增動作群組] 表單。

10. 設定 [電子郵件/簡訊/推播/語音] 動作類型。
    
   選擇 [電子郵件 Azure 資源管理員角色] 來選取訂用帳戶擁有者、參與者和讀者，以接收通知。
   
   如果您想要在警示引發時呼叫 Webhook，可選擇在 [Webhook] 欄位中提供有效的 URI。

   完成時選取 [確定]。

   ![動作群組](./media/howto-alert-on-metric/10-action-group-type.png)

11. 指定 [警示規則名稱]、[描述] 與 [嚴重性]。

   ![動作群組](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. 選取 [建立警示規則] 以建立警示。

   在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="manage-your-alerts"></a>管理警示
建立警示之後，您可以選取它，然後進行下列動作：

* 檢視圖表，其中顯示與此警示相關的計量臨界值及前一天的實際值。
* **編輯**或**刪除**警示規則。
* 如果您想要暫時停止或恢復接收通知，可以將警示**停用**或**啟用**。


## <a name="next-steps"></a>後續步驟
* 深入了解 [在警示中設定 webhook](../azure-monitor/platform/alerts-webhooks.md)。
* 依照 [計量集合概觀](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
