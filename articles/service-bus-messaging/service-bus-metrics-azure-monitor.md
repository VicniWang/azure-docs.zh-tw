---
title: Azure 監視器中的 Azure 服務匯流排計量 (預覽) | Microsoft Docs
description: 使用 Azure 監視器監視服務匯流實體
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: fda6ca26c39d49c0d16c9933cc0a14f0bfea90a9
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54857109"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Azure 監視器中的 Azure 服務匯流排計量 (預覽)

服務匯流排計量會提供您 Azure 訂用帳戶中的資源狀態。 您可以使用豐富的計量資料集，評估服務匯流排資源的整體健康狀態，不只在命名空間層級，還有實體層級。 這些統計資料相當重要，因為它們可協助您監視服務匯流排的狀態。 計量也可協助針對問題的根本原因進行疑難排解，而不需要連絡 Azure 支援。

「Azure 監視器」提供統一的使用者介面，可供您監視各個不同的 Azure 服務。 如需詳細資訊，請參閱 [Microsoft Azure 中的監視](../monitoring-and-diagnostics/monitoring-overview.md)和 GitHub 上的 [Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) (使用 .NET 擷取 Azure 監視計量) 範例。

> [!IMPORTANT]
> 在未與實體互動的時間達 2 小時之後，計量將會開始顯示 "0" 作為值，直到該實體不再閒置為止。

## <a name="access-metrics"></a>存取計量

Azure 監視器提供了多種方法供您存取計量。 您可以透過 [Azure 入口網站](https://portal.azure.com)來存取計量，或使用 Azure 監視器 API (REST 和 .NET) 和分析解決方案 (例如 Log Analytics 和事件中樞) 來存取計量。 如需詳細資訊，請參閱[監視 Azure 監視器所收集的資料](../azure-monitor/platform/data-collection.md)。

計量是預設啟用的功能，您可以存取最近 30 天的資料。 如果您需要延長這些資料的保留時間，您可以將計量資料封存到 Azure 儲存體帳戶。 在「Azure 監視器」的[診斷設定](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)中即可設定此值。

## <a name="access-metrics-in-the-portal"></a>在入口網站中存取計量

您可以在 [Azure 入口網站](https://portal.azure.com)中監視一段時間的計量。 下列範例示範如何檢視帳戶層級的成功要求及連入要求：

![][1]

您也可以直接透過命名空間來存取計量。 若要這樣做，請選取您的命名空間，然後按一下 [Metrics (Peview)] \(計量 (預覽))。 若要顯示篩選至實體範圍的計量，請選取實體，然後按一下[Metrics (Peview)] \(計量 (預覽))。

![][2]

如需支援維度的計量，您必須使用所需的維度值來進行篩選。

## <a name="billing"></a>計費

在預覽版「Azure 監視器」中使用計量是免費的。 不過，如果您使用內嵌計量資料的額外解決方案，可能就需支付這些解決方案的使用費。 例如，如果您將計量資料封存到 Azure 儲存體帳戶，就要支付 Azure 儲存體的使用費。 如果您將計量資料串流到 Log Analytics 進行進階分析，則也需支付 Log Analytics 的費用。

下列計量會提供您服務健康狀態的概觀。 

> [!NOTE]
> 我們正在取代許多計量，因為它們移至不同的名稱下。 這可能需要您更新參考。 此後不再支援標記「已取代」關鍵字的計量。

所有計量值都會每分鐘傳送至「Azure 監視器」。 時間細微性會定義用來呈現計量值的時間間隔。 所有的服務匯流排計量都支援 1 分鐘的時間間隔。

## <a name="request-metrics"></a>要求的計量

計算資料和管理作業要求的數目。

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
| 傳入的要求 (預覽) | 在指定時段內，向服務匯流排服務提出的要求數目。 <br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName|
|成功的要求 (預覽)|在指定時段內，向服務匯流排服務提出的成功要求數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName|
|伺服器錯誤 (預覽)|在指定時段內，服務匯流排服務中因錯誤而未處理的要求數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName|
|使用者錯誤 (預覽 - 請參閱以下子區段)|在指定的期間內，因使用者錯誤而未處理的要求數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName|
|節流的要求 (預覽)|因為超過使用量而節流的要求數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName|

### <a name="user-errors"></a>使用者錯誤

下列兩種錯誤類型會分類為使用者錯誤：

1. 用戶端錯誤 (在 HTTP 中是 400 錯誤)。
2. 處理訊息時發生的錯誤，例如 [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)。


## <a name="message-metrics"></a>訊息計量

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
|傳入訊息 (預覽)|在指定時段內，傳送至服務匯流排的事件或訊息數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName|
|傳出訊息 (預覽)|在指定時段內，從服務匯流排接收的事件或訊息數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName|
| 訊息 (預覽) | 佇列/主題中的訊息計數。 <br/><br/> 單位：Count <br/> 彙總類型：平均值 <br/> 維度：EntityName |
| ActiveMessages (預覽) | 佇列/主題中的作用中訊息計數。 <br/><br/> 單位：Count <br/> 彙總類型：平均值 <br/> 維度：EntityName |

## <a name="connection-metrics"></a>連接計量

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
|ActiveConnections (預覽)|命名空間及實體上的作用中連線數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName|
|已開啟的連線 (預覽)|已開啟的連線數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName|
|已關閉的連線 (預覽)|已關閉的連線數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：EntityName |

## <a name="resource-usage-metrics"></a>資源使用量計量

> [!NOTE] 
> 下列計量僅適用於**進階**層。 

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
|每個命名空間的 CPU 使用量 (預覽)|命名空間的 CPU 使用量百分比。<br/><br/> 單位：百分比 <br/> 彙總類型：最大值 <br/> 維度：EntityName|
|每個命名空間的記憶體大小使用量 (預覽)|命名空間的記憶體使用量百分比。<br/><br/> 單位：百分比 <br/> 彙總類型：最大值 <br/> 維度：EntityName|

## <a name="metrics-dimensions"></a>計量維度

Azure 服務匯流排支援下列的 Azure 監視器計量維度。 將維度新增至計量為選擇性。 如果不新增維度，會在命名空間層級指定計量。 

|維度名稱|說明|
| ------------------- | ----------------- |
|EntityName| 服務匯流排支援命名空間下的訊息實體。|

## <a name="set-up-alerts-on-metrics"></a>設定計量警示

1. 在 [服務匯流排命名空間] 頁面的 [計量] 索引標籤上，選取 [設定警示]。 

    ![[計量] 頁面 - [設定警示] 功能表](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. 選取 [選取目標]，然後在 [選取資源] 頁面上執行下列動作： 
    1. 針對 [依資源類型篩選] 欄位，選取 [服務匯流排命名空間]。 
    2. 針對 [依訂用帳戶篩選]  欄位，選取您的訂用帳戶。
    3. 從清單中選取 [服務匯流排命名空間]。 
    4. 選取 [完成] 。 
    
        ![選取命名空間](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. 選取 [新增準則]，然後在 [設定訊號邏輯] 頁面上執行下列動作：
    1. 針對 [訊號類型]，選取 [計量]。 
    2. 選取一個訊號。 例如︰[服務錯誤 (預覽)]。 

        ![選取伺服器錯誤](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. 針對 [條件]，選取 [大於]。
    2. 針對 [時間彙總]，選取 [總計]。 
    3. 針對 [閾值]，輸入 **5**。 
    4. 選取 [完成] 。    

        ![指定條件](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. 在 [建立規則] 頁面上，展開 [定義警示詳細資料]，然後執行下列動作：
    1. 輸入警示的**名稱**。 
    2. 輸入警示的**描述**。
    3. 選取警示規則的**嚴重性**。 

        ![警示詳細資料](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. 在 [建立規則] 頁面上，展開 [定義動作群組]，選取 [新增動作群組]，然後在 [新增動作群組] 頁面上執行下列動作。 
    1. 輸入動作群組的名稱。
    2. 輸入動作群組的簡短名稱。 
    3. 選取您的訂用帳戶。 
    4. 選取資源群組。 
    5. 針對此逐步解說，輸入 **Send email** 作為 [動作名稱]。
    6. 針對 [動作類型]，選取 [電子郵件/簡訊/推播/語音]。 
    7. 選取 [編輯詳細資料]。 
    8. 在 [電子郵件/簡訊/推播/語音] 頁面上，執行下列動作：
        1. 選取 [電子郵件]。 
        2. 輸入**電子郵件地址**。 
        3. 選取 [確定] 。

            ![警示詳細資料](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. 在 [新增動作群組] 頁面上，選取 [確定]。 
1. 在 [建立規則] 頁面上，選取 [建立警示規則]。 

    ![[建立警示規則] 按鈕](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>後續步驟

請參閱 [Azure 監視概觀](../monitoring-and-diagnostics/monitoring-overview.md)。

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


