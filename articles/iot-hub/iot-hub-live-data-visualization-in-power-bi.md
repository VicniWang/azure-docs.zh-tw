---
title: Azure IoT 中樞感應器資料的即時資料視覺效果 – Power BI | Microsoft Docs
description: 使用 Power BI 來視覺化收集自感應器並傳送至 Azure IoT 中樞的溫度和溼度資料。
author: rangv
keywords: 即時資料視覺效果, 即時資料視覺效果, 感應器資料視覺效果
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: a55e77853a1c9466892f686f34d17a5e84b11ba7
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411280"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化

![端對端圖表](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>您學到什麼

您可了解如何藉由使用 Power BI 將 Azure IoT 中樞收到的即時感應器資料視覺化。 如果您想嘗試使用 Web Apps 將 IoT 中樞的資料視覺化，請參閱[使用 Azure Web Apps 將 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-web-apps.md)。

## <a name="what-you-do"></a>您要做什麼

* 新增取用者群組，讓您的 IoT 中樞準備好進行資料存取。

* 建立、設定和執行串流分析作業，以便將資料從 IoT 中樞傳輸至 Power BI 帳戶。

* 建立及發佈 Power BI 報告，以將資料視覺化。

## <a name="what-you-need"></a>您需要什麼

 * 完成涵蓋下列需求的[設定裝置](iot-hub-raspberry-pi-kit-node-get-started.md)教學課程︰

   * 有效的 Azure 訂用帳戶。
   * 位於您訂用帳戶中的 Azure IoT 中樞。
   * 將訊息傳送到您 Azure IoT 中樞的用戶端應用程式。

* Power BI 帳戶。 ([免費試用 Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>設定、設定及執行串流分析作業

一開始先建立串流分析作業。 建立作業之後，定義輸入、輸出和用來擷取資料的查詢。

### <a name="create-a-stream-analytics-job"></a>建立串流分析作業

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [建立資源] > [物聯網] > [串流分析作業]。

2. 輸入作業的以下資訊。

   **作業名稱**：作業的名稱。 此名稱必須是全域唯一的。

   **資源群組**：使用 IoT 中樞所用的相同資源群組。

   **位置**：使用與資源群組相同的位置。

   **釘選到儀表板**：核取此選項可讓您從儀表板輕鬆地存取 IoT 中樞。

   ![在 Azure 中建立串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

3. 按一下頁面底部的 [新增] 。

### <a name="add-an-input-to-the-stream-analytics-job"></a>將輸入新增至串流分析作業

1. 開啟串流分析作業。

2. 在 [作業拓撲] 之下，按一下 [輸入]。

3. 在 [輸入] 窗格中，按一下 [新增資料流輸入]，然後輸入下列資訊︰

   **輸入別名**：輸入的唯一別名，然後選取下面的 [手動提供 IoT 中樞設定]。

   **來源**：選取 [IoT 中樞]。
   
   **端點**：按一下 [傳訊]。

   **取用者群組**：選取您剛建立的取用者群組。

4. 按一下頁面底部的 [新增] 。

   ![在 Azure 中將輸入新增至串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>將輸出新增至串流分析作業

1. 在 [作業拓撲] 之下，按一下 [輸出]。

2. 在 [輸出] 窗格中，按一下 [新增] 和 [Power BI]，然後輸入下列資訊︰

   **輸出別名**：輸出的唯一別名。

   **群組工作區**：選取您的目標群組工作區。

   **資料集名稱**：輸入資料集名稱。

   **資料表名稱**：輸入資料表名稱。

3. 按一下 [授權]，然後登入您的 Power BI 帳戶。

4. 按一下頁面底部的 [新增] 。

   ![在 Azure 中將輸出新增至串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>設定串流分析作業的查詢

1. 在 [作業拓撲] 之下，按一下 [查詢]。

2. 使用作業的輸入別名取代 `[YourInputAlias]`。

3. 使用作業的輸出別名取代 `[YourOutputAlias]`。

4. 按一下 [檔案] 。

   ![在 Azure 中將查詢新增至串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>執行串流分析作業

在串流分析作業中，按一下 [啟動] > [立即] > [啟動]。 成功啟動作業後，作業狀態會從 [已停止] 變更為 [執行中]。

![在 Azure 中執行串流分析作業](./media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>建立及發佈 Power BI 報告，以將資料視覺化。

1. 確保範例應用程式正在您的裝置上執行。 如果沒有，您可以參考[設定您的裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started)下的教學課程。

2. 登入您的 [Power BI](https://powerbi.microsoft.com/en-us/) 帳戶。

3. 按一下您所使用的工作區 [我的工作區]。

4. 按一下 [資料集]。

   您應該會看到在建立串流分析作業輸出時所指定的資料集。

5. 對於您所建立的資料集，按一下 [新增報告] (資料集名稱右邊的第一個圖示)。

   ![建立 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

6. 建立折線圖以顯示一段時間的即時溫度。

   1. 在報告建立頁面上，新增折線圖。

   2. 在 [欄位] 窗格上，展開您建立串流分析作業輸出時指定的資料表。
   
   3. 將 **EventEnqueuedUtcTime** 拖放至 [視覺效果] 窗格上的 [軸]。
   
   4. 將 **temperature** 拖放至 [值]。

      折線圖已建立。 x 軸會顯示 UTC 時區的日期和時間。 y 軸會顯示感應器的溫度。

      ![將溫度的折線圖新增至 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

7. 建立另一個折線圖以顯示一段時間的即時溼度。 若要這麼做，請遵循上述相同步驟，並將 **EventEnqueuedUtcTime** 放在 x 軸上，將 **humidity** 放在 y 軸上。

   ![將溼度的折線圖新增至 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

8. 按一下 [儲存] 以儲存報告。

9. 按一下左窗格中的 [報告]，然後按一下您剛建立的報告。

10. 按一下 [檔案] > [發佈到網站]。

11. 按一下 [建立內嵌程式碼]，然後按一下 [發佈]。

您會取得可與任何人共用以供存取報告的報告連結，以及取得程式碼片段以將報告整合到您的部落格或網站。

![發佈 Microsoft Power BI 報告](./media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft 也會提供 [Power BI 行動應用程式](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/)，以供檢視並與您行動裝置上的 Power BI 儀表板和報告互動。

## <a name="next-steps"></a>後續步驟

您已成功使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化。

有替代方法可將 Azure IoT 中樞的資料視覺化。 請參閱[使用 Azure Web Apps 將來自 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-web-apps.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
