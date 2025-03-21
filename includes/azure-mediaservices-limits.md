---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 9cd5a63b5b1d7000605f90752be755356d40bb4a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212973"
---
>[!NOTE]
>如果是不固定的資源，您可以建立支援票證，要求增加配額。 **請勿**為了嘗試取得更高的限制而建立其他 Azure 媒體服務。

| 資源 | 預設限制 | 
| --- | --- | 
| 在單一訂用帳戶的 Azure 媒體服務 (AMS) 帳戶 | 25 (固定) |
| 每個 AMS 帳戶的媒體保留單元 (RU) |25 (S1)<br/>10 (S2, S3) <sup>(1)</sup> | 
| 每個 AMS 帳戶的作業 | 50,000<sup>(2)</sup> |
| 每個作業的鏈結工作 | 30 (固定) |
| 每個 AMS 帳戶的資產 | 1,000,000|
| 每個工作的資產 | 50 |
| 每個作業的資產 | 100 |
| 一次與資產相關聯的唯一定位器 | 5<sup>(4)</sup> |
| 每個 AMS 帳戶的即時通道  |5|
| 每個通道中已停止狀態的程式  |50|
| 每個通道中執行中的程式  |3|
| 每個媒體服務帳戶的串流端點 (已停止或執行中)|2|
| 每個串流端點的資料流單位  |10 |
| 儲存體帳戶 | 1,000<sup>(5)</sup> (固定) |
| 原則 | 1,000,000<sup>(6)</sup> |
| 檔案大小| 在某些情況下，對於媒體服務中支援處理的檔案大小上限有所限制。 <sup>7</sup> |
  
<sup>1</sup> 如果您變更類型 (例如，從 S2 到 S1)，就會重設 RU 上限。

<sup>2</sup> 這個數目包括佇列、已完成、作用中和已取消的工作。 不包含已刪除的工作。 您可以使用 **IJob.Delete** 或 **DELETE** HTTP 要求刪除舊工作。

自 2017 年 4 月 1 日起，您的帳戶中任何超過 90 天的作業記錄以及其相關工作記錄都會自動刪除，即使記錄總數低於配額上限亦然。 如果您需要封存作業/工作資訊，您可以使用[這裡](../articles/media-services/previous/media-services-dotnet-manage-entities.md)所述的程式碼。

<sup>3</sup> 提出要求來列出作業實體時，每個要求會傳回最多 1000 個作業。 如果您需要追蹤所有已送出的工作，您可以如 [OData 系統查詢選項](https://msdn.microsoft.com/library/gg309461.aspx)中所述，使用 top/skip。

<sup>4</sup> 定位器並非設計來管理每個使用者的存取控制。 若要給予個別使用者不同的存取權限，請使用數位版權管理 (DRM) 方案。 如需詳細資訊，請參閱 [本節](../articles/media-services/previous/media-services-content-protection-overview.md) 。

<sup>5</sup> 儲存體帳戶必須來自相同的 Azure 訂用帳戶。

<sup>6</sup> 對於不同的 AMS 原則 (例如 Locator 原則或 ContentKeyAuthorizationPolicy) 有 1,000,000 個原則的限制。 

>[!NOTE]
> 如果您總是使用相同的天數 / 存取權限 / 等等，您應該使用相同的原則識別碼。如需詳細資訊與範例，請參閱[本節](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)。

<sup>7</sup> 如果您要將內容上傳至 Azure 媒體服務中的資產，以在服務中透過其中一種媒體處理器加以處理 (也就是像媒體編碼器標準和媒體編碼器高階工作流程之類的編碼器，或像臉部偵測器這類分析引擎)，則您應該注意支援的檔案上小上限的條件約束。 

單一 blob 支援的大小上限目前在 Azure Blob 儲存體是最多 5 TB。 不過，其他的限制會以服務所使用的 VM 大小作為基礎套用在 Azure 媒體服務中。 下表顯示每個媒體保留單元 (S1、S2、S3) 上的限制。如果來源檔案大於資料表中定義的限制，編碼作業就會失敗。 如果您要編碼長時間的 4k 解析來源，就必須使用 S3 媒體保留單位來達成所需的效能。 如果您的 4k 內容超過 S3 媒體保留單元上的 260 GB 限制，請以 amshelp@microsoft.com 與我們連絡，以取得潛在防護功能來支援您的情節。

| 媒體保留單元類型 | 檔案輸入大小 (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
