---
title: 教學課程：分析來自 Azure Digital Twins 設定的事件 | Microsoft Docs
description: 了解如何透過本教學課程中的步驟，使用 Azure 時間序列深入解析對來自 Azure Digital Twins 空間的事件進行視覺化檢視和分析。
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/18/2018
ms.author: dkshir
ms.openlocfilehash: 8f8bdb4d601e35cf489c1c4c1db3574fb8c8f18f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730780"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>教學課程：使用時間序列深入解析對來自 Azure Digital Twins 空間的事件進行視覺化檢視和分析

在部署 Azure Digital Twins 執行個體、佈建空間並實作自訂函式來監視特定情況後，您便可以對來自空間的事件和資料進行視覺化檢視，以找出其中的趨勢和異常。

在[第一個教學課程](tutorial-facilities-setup.md)中，您已設定虛構建築物的空間圖形，該建築物中有一個空間含有可監控運動、二氧化碳和溫度的感應器。 在[第二個教學課程](tutorial-facilities-udf.md)中，您佈建了圖形和使用者定義的函式。 該函式會監視這些感應器的數值，並觸發正確情況的通知。 也就是，房間是空的，而且溫度和二氧化碳濃度正常。

本教學課程示範如何將來自 Azure Digital Twins 的通知和資料與 Azure Time Series Insights 進行整合。 然後，您便可將一段時間的感應器數值視覺化。 您可以尋找其中的趨勢，例如，哪個房間最常使用，以及一天當中的哪個時間最忙碌。 您也可以偵測異常，例如哪些房間感覺變得更悶熱，或是建築物中的某個區域是否一直傳送高溫度值，而表示空調故障。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 使用 Azure 事件中樞來串流資料。
> * 使用時間序列深入解析來進行分析。

## <a name="prerequisites"></a>必要條件

本教學課程假設您已經[設定](tutorial-facilities-setup.md)並[佈建](tutorial-facilities-udf.md) Azure Digital Twins 設定。 繼續之前，請確定您有：

- [Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 執行中的 Digital Twins 執行個體。
- 在工作電腦上下載並解壓縮 [Digital Twins C# 範例](https://github.com/Azure-Samples/digital-twins-samples-csharp)。
- 在開發電腦上安裝 [.NET Core SDK 2.1.403 版或更新版本](https://www.microsoft.com/net/download)，以執行範例。 執行 `dotnet --version` 來確認是否已安裝正確版本。

## <a name="stream-data-by-using-event-hubs"></a>使用事件中樞來串流資料

您可以使用[事件中樞](../event-hubs/event-hubs-about.md)服務來建立管線以串流資料。 本節會說明如何建立事件中樞來作為 Azure Digital Twins 和時間序列深入解析執行個體之間的連接器。

### <a name="create-an-event-hub"></a>建立事件中樞

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左窗格中選取 [建立資源]。

1. 搜尋並選取 [事件中樞]。 選取 [建立] 。

1. 輸入事件中樞命名空間的 [名稱]。 選擇 [標準] **定價層**、[訂用帳戶]、用於 Digital Twins 執行個體的 [資源群組]，以及 [位置]。 選取 [建立] 。

1. 在事件中樞命名空間部署中，選取 [資源] 底下的命名空間。

    ![部署後的事件中樞命名空間](./media/tutorial-facilities-analyze/open-event-hub-ns.png)

1. 在事件中樞命名空間的 [概觀] 窗格中，選取頂端的 [事件中樞] 按鈕。
    ![事件中樞按鈕](./media/tutorial-facilities-analyze/create-event-hub.png)

1. 輸入事件中樞的 [名稱]，然後選取 [建立]。

   事件中樞部署好之後，便會出現在事件中樞命名空間的 [事件中樞] 窗格中，且狀態為 [作用中]。 選取此事件中樞以開啟其 [概觀] 窗格。

1. 選取頂端的 [取用者群組] 按鈕，然後為取用者群組輸入名稱，例如 **tsievents**。 選取 [建立] 。

    ![事件中樞取用者群組](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   取用者群組建立好之後，便會出現在事件中樞 [概觀] 窗格底部的清單中。

1. 開啟事件中樞的 [共用存取原則] 窗格，然後選取 [新增] 按鈕。 輸入 **ManageSend** 作為原則名稱，確定所有核取方塊皆已選取，然後選取 [建立]。

    ![事件中樞連接字串](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. 開啟所建立的 ManageSend 原則，然後將 [連接字串 - 主要金鑰] 和 [連接字串 - 次要金鑰] 的值複製到暫存檔。 下一節在建立事件中樞的端點時需要用到這些值。

### <a name="create-an-endpoint-for-the-event-hub"></a>建立事件中樞的端點

1. 在 [命令] 視窗中，確定您位於 Azure Digital Twins 範例的 **occupancy-quickstart\src** 資料夾中。

1. 在編輯器中開啟 **actions\createEndpoints.yaml** 檔案。 將其中的內容替換為下列程式碼：

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. 將預留位置 `Primary_connection_string_for_your_event_hub` 替換為事件中樞的 [連接字串 - 主要金鑰] 值。 確定此連接字串的格式如下：

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. 將預留位置 `Secondary_connection_string_for_your_event_hub` 替換為事件中樞的 [連接字串 - 次要金鑰] 值。 確定此連接字串的格式如下： 

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. 將預留位置 `Name_of_your_Event_Hub` 取代為事件中樞的名稱。

    > [!IMPORTANT]
    > 所有值在輸入時不得有任何引號。 請確定 YAML 檔案中的所有冒號後面都至少有一個空格字元。 您也可以使用任何線上的 YAML 驗證器 (例如[此工具](https://onlineyamltools.com/validate-yaml)) 來驗證 YAML 檔案的內容。

1. 儲存並關閉檔案。 在 [命令] 視窗中執行下列命令，並在出現提示時使用 Azure 帳戶登入。

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   此命令會為事件中樞建立兩個端點。

   ![事件中樞的端點](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>使用時間序列深入解析來進行分析

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [建立資源]。 

1. 搜尋並選取新的**時間序列深入解析**資源。 選取 [建立] 。

1. 輸入時間序列深入解析執行個體的**名稱**，然後選取 [訂用帳戶]。 選取用於 Digital Twins 執行個體的 [資源群組]，以及您的 [位置]。 選取 [建立] 。

    ![用於建立時間序列深入解析執行個體的選取項目](./media/tutorial-facilities-analyze/create-tsi.png)

1. 執行個體部署好之後，開啟時間序列深入解析環境，然後開啟其 [事件來源] 窗格。 選取頂端的 [新增] 按鈕來新增取用者群組。

1. 在 [新增事件來源] 窗格中，輸入 [名稱]，並確定所選取的其他值是否正確。 選取 [ManageSend] 作為 [事件中樞原則名稱]，然後選取您在上一節建立的取用者群組來作為 [事件中樞取用者群組]。 選取 [建立] 。

    ![用於建立事件來源的選取項目](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. 開啟時間序列深入解析環境的 [概觀] 窗格，然後選取頂端的 [移至環境] 按鈕。 如果您收到資料存取警告，則請開啟時間序列深入解析執行個體的 [資料存取原則] 窗格，選取 [新增]，選取 [參與者] 角色，然後選取適當的使用者。

1. [移至環境] 按鈕會開啟[時間序列深入解析總管](../time-series-insights/time-series-insights-explorer.md)。 如果總管中未顯示任何事件，請藉由瀏覽至 Digital Twins 範例的 **device-connectivity** 專案並執行 `dotnet run`，以模擬裝置事件。

1. 產生幾個模擬事件後，返回時間序列深入解析總管，並選取頂端的 [重新整理] 按鈕。 您應該會看到系統已針對模擬感應器資料建立了分析圖表。 

    ![時間序列深入解析總管中的圖表](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. 在時間序列深入解析總管中，您接著可以針對來自房間、感應器和其他資源的不同事件和資料，產生圖表和熱度圖。 使用左側的 [量值] 和 [分割依據] 下拉式方塊，來建立您自己的視覺效果。 

   例如，選取 [事件] 作為 [量值] 並選取 [DigitalTwins-SensorHardwareId] 作為 [分割依據]，來為每個感應器產生熱度圖。 熱度圖將類似下圖：

   ![時間序列深入解析總管中的熱度圖](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>清除資源

如果您想要停止探索超過此時間點的 Azure Digital Twins，請放心地刪除在本教學課程中建立的資源：

1. 從 [Azure 入口網站](http://portal.azure.com)的左側功能表中，選取 [所有資源]，選取您的 Digital Twins 資源群組，然後選取 [刪除]。

    > [!TIP]
    > 如果您在刪除 Digital Twins 執行個體時遇到問題，已推出的服務更新中具有修正程式。 請重試刪除執行個體。

2. 如有必要，請刪除工作電腦上的應用程式範例。

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以深入了解 Azure Digital Twins 中的空間智慧圖形和物件模型。

> [!div class="nextstepaction"]
> [了解 Digital Twins 物件模型和空間智慧圖形](concepts-objectmodel-spatialgraph.md)
