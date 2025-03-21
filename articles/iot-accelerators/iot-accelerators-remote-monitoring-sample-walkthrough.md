---
title: 遠端監視解決方案加速器概觀 - Azure | Microsoft Docs
description: 遠端監視解決方案加速器的概觀。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: dobett
ms.openlocfilehash: f2d3f8bf0f853afc144545cdaa8a217b0e5075a8
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100612"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>遠端監視解決方案加速器概觀

遠端監視[解決方案加速器](../iot-accelerators/about-iot-accelerators.md)會在遠端位置實作多部機器的端對端監視解決方案。 此解決方案結合了主要的 Azure 服務以提供一般的商務案例實作。 您可以將此解決方案作為自己實作的起點並加以[自訂](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)，以符合自己特有的商務需求。

本文將逐步介紹遠端監視解決方案的一些重要元素，讓您瞭解它的運作方式。 這項知識能協助您︰

* 在解決方案中進行疑難排解。
* 規劃如何自訂解決方案以滿足您的特定需求。
* 設計使用 Azure 服務的 IoT 解決方案。

GitHub 提供遠端監視解決方案加速器程式碼：

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>邏輯架構

下圖概述 [IoT 架構](../iot-fundamentals/iot-introduction.md)上重疊的遠端監視解決方案加速器的邏輯元件：

![邏輯架構](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>使用微服務的理由？

從 Microsoft 發行第一個解決方案加速器開始，就已發展雲端架構。 [微服務](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)問世以來都是經證實的做法，可達成延展及彈性，而不必犧牲開發速度。 多項在內部使用此架構模式的 Microsoft 服務，都獲得絕佳的可靠性與延展性結果。 更新的解決方案加速器已將這些知識付諸實現，因此您也可以從中獲益。

> [!TIP]
> 若要了解微服務架構的詳細資訊，請參閱 [.NET 應用程式架構](https://www.microsoft.com/net/learn/architecture)和[微服務︰採用雲端技術的應用程式革命](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)。

## <a name="device-connectivity"></a>裝置連線能力

解決方案在邏輯架構的裝置連線部分中包含下列元件：

### <a name="real-devices"></a>真實裝置

您可以將真實裝置連線到解決方案。 您可以使用 Azure IoT 裝置 SDK 來實作模擬裝置的行為。

您可以從解決方案入口網站的儀表板佈建真實裝置。

### <a name="device-simulation-microservice"></a>裝置模擬微服務

解決方案所包含的[裝置模擬微服務](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation)可讓您從解決方案入口網站管理模擬裝置的集區，從而測試解決方案中的端對端流程。 模擬的裝置：

* 產生裝置到雲端的遙測。
* 回應來自 IoT 中樞的雲端到裝置方法呼叫。

微服務會提供 RESTful 端點，讓您可建立、啟動及停止模擬。 每個模擬都是由一組不同類型的虛擬裝置所組成，可傳送遙測並回應方法呼叫。

您可以在解決方案入口網站的儀表板佈建模擬的裝置。

### <a name="iot-hub"></a>IoT 中樞

[IoT 中樞](../iot-hub/index.yml)會內嵌從真實裝置和模擬裝置傳送至雲端的遙測資料。 IoT 中樞會向 IoT 解決方案後端中的服務提供遙測資料進行處理。

解決方案中的 IoT 中樞也可︰

* 維護身分識別登錄，以儲存允許連線至入口網站之所有裝置的驗證金鑰。
* 代表解決方案加速器在您的裝置上叫用方法。
* 維護所有已註冊裝置的裝置對應項。 裝置對應項會儲存裝置所報告的屬性值。 裝置對應項也會儲存在解決方案入口網站中設定的所需屬性，以便裝置在後續連接時擷取。
* 排程作業以設定多個裝置的屬性，或在多個裝置上叫用方法。

## <a name="data-processing-and-analytics"></a>資料處理和分析

解決方案在邏輯架構的資料處理和分析部分中包含下列元件：

### <a name="iot-hub-manager-microservice"></a>IoT 中樞管理員微服務

解決方案所包含的 [IoT 中樞管理員微服務](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)可處理與 IoT 中樞的互動，例如：

* 建立及管理 IoT 裝置。
* 管理裝置對應項。
* 在裝置上叫用方法。
* 管理 IoT 認證。

此服務也會執行 IoT 中樞查詢，以擷取屬於使用者定義群組的裝置。

微服務會提供 RESTful 端點來管理裝置和裝置對應項、叫用方法，以及執行 IoT 中樞查詢。

### <a name="device-telemetry-microservice"></a>裝置遙測微服務

對於 Time Series Insights 中儲存的裝置遙測資料，[裝置遙測微服務](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry)提供 RESTful 端點以供讀取之用。 對於來自儲存體的警示定義，RESTful 端點也可啟用規則和讀取/寫入存取的 CRUD 作業。

### <a name="storage-adapter-microservice"></a>儲存體配接器微服務

[儲存體配接器的微服務](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter)管理機碼值組，以擷取儲存體服務語意，並提供使用 Azure Cosmos DB 儲存任何格式的資料所用的簡單介面。

值是排列在集合中。 您可以使用個別的值，也可以擷取整個集合。 複雜資料結構是由用戶端序列化，並以簡單的文字承載進行管理。

此服務會提供 RESTful 端點，以便進行機碼值組的 CRUD 作業。 值

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

解決方案加速器部署會使用 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) 來儲存規則、警示、組態設定，以及所有其他非經常性儲存體。

### <a name="azure-stream-analytics-manager-microservice"></a>Azure 串流分析管理員微服務

[Azure 串流分析管理員微服務](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager)可管理 Azure 串流分析 (ASA) 作業，包括設定作業的組態、啟動和停止作業，以及監視作業的狀態。

ASA 作業是由兩個參考資料集支援。 一個資料集會定義規則，另一個會定義裝置群組。 規則參考資料是從裝置遙測微服務所管理的資訊產生。 Azure 串流分析管理員微服務會將遙測資料的規則轉換成串流處理邏輯。

裝置群組的參考資料可用來識別要套用於內送遙測訊息的規則群組。 裝置群組是由設定微服務所管理，並使用 Azure IoT 中樞裝置對應項查詢。

ASA 作業從連線裝置將遙測資料傳送到 Time Series Insights 進行儲存和分析。

### <a name="azure-stream-analytics"></a>Azure 串流分析

[Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/)是事件處理引擎，可讓您檢查來自裝置的大量資料流。

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure 時間序列深入解析](https://docs.microsoft.com/azure/time-series-insights/)會儲存連接到解決方案加速器的裝置之中的遙測資料。 它也可透過解決方案 Web UI 以視覺效果呈現和查詢裝置遙測資料。

> [!NOTE]
> Azure China 雲端目前不提供時間序列見解。 Azure 中國雲端中的新遠端監視解決方案加速器部署將 Cosmos DB 使用於所有的儲存體。

### <a name="configuration-microservice"></a>設定微服務

對於解決方案加速器中的裝置群組、解決方案設定和使用者設定進行的 CRUD 作業，[組態的微服務](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)會提供 RESTful 端點。 它會使用儲存體配接器微服務保存組態資料。

### <a name="authentication-and-authorization-microservice"></a>驗證和授權微服務

[驗證和授權微服務](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth)可管理獲授權可存取解決方案加速器的使用者。 可以使用支援 [OpenId Connect](https://openid.net/connect/) 的任何識別服務提供者進行使用者管理。

### <a name="azure-active-directory"></a>Azure Active Directory

解決方案加速器部署使用 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) 作為 OpenID Connect 提供者。 Azure Active Directory 會儲存使用者資訊，並提供憑證來驗證 JWT 權杖簽章。

## <a name="presentation"></a>展示

解決方案在邏輯架構的展示部分中包含下列元件：

[Web 使用者介面是 React Javascript 應用程式](https://github.com/Azure/pcs-remote-monitoring-webui)。 應用程式：

* 只會使用 Javascript React，並會完全在瀏覽器中執行。
* CSS 樣式。
* 會透過 AJAX 呼叫與公眾對應的微服務互動。

使用者介面會呈現所有解決方案加速器功能，並與其他微服務互動，例如：

* 保護使用者資料的驗證和授權微服務。
* 列出和管理 IoT 裝置的 IoT 中樞管理員微服務。

使用者介面整合 Azure 時間序列深入解析總管來啟用裝置遙測資料的查詢和分析。

設定微服務可讓使用者介面儲存及擷取組態集。

## <a name="next-steps"></a>後續步驟

如果您需要探索來源程式碼和開發人員文件，請開始使用兩個 GitHub 存放庫的其中一個：

* [使用 Azure IoT 的遠端監視解決方案加速器 (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) \(英文\)。
* [使用 Azure IoT 的遠端監視解決方案加速器 (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) \(英文\)。

詳細的方案架構圖表：
* [遠端監視架構的解決方案加速器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture) \(英文\)。

如需關於遠端監視解決方案加速器的詳細概念資訊，請參閱[自訂解決方案加速器](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)。
