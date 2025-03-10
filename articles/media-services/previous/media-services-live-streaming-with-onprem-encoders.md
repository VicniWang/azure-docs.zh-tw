---
title: 使用會建立多位元速率串流的內部部署編碼器執行即時串流 | Microsoft Docs
description: 本主題描述如何讓通道接收內部部署編碼器的多位元速率即時串流。 串流可以隨即透過一或多個串流端點傳遞給用戶端播放應用程式，使用下列其中一個自動調整串流通訊協定：HLS、Smooth Streaming、DASH。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/10/2019
ms.author: cenkd;juliako
ms.openlocfilehash: 67d86ca7ed79f431bf762d4a3679e18a7b4bc373
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990210"
---
# <a name="live-streaming-with-on-premises-encoders-that-create-multi-bitrate-streams"></a>使用會建立多位元速率串流的內部部署編碼器執行即時串流

> [!NOTE]
> 從 2018 年 5 月 12 日開始，即時通道將不再支援 RTP/MPEG-2 傳輸串流內嵌通訊協定。 請從 RTP/MPEG-2 移轉到 RTMP 或分散式 MP4 (Smooth Streaming) 內嵌通訊協定。

## <a name="overview"></a>概觀
在 Azure 媒體服務中，通道代表處理即時串流內容的管線。 通道會以兩種方式之一收到即時輸入串流：

* 內部部署即時編碼器會將多位元速率 RTMP 或 Smooth Streaming (分散式 MP4) 串流傳送到未啟用執行媒體服務即時編碼的通道。 內嵌的串流會通過通道，而不需任何進一步處理。 此方法稱為 *傳遞*。 即時編碼器也會將單一位元速率串流傳送至無法用於即時編碼的通道，但是不建議您使用此方法。 媒體服務會將串流傳遞給要求的客戶。

  > [!NOTE]
  > 使用傳遞方法是進行即時串流的最經濟實惠方式。


* 內部部署即時編碼器會將單一位元速率串流傳送至通道，可以使用下列格式之一，以媒體服務執行即時編碼：RTMP 或 Smooth Streaming (分散的 MP4)。 通道接著會執行即時編碼，將連入的單一位元速率串流編碼成多位元速率 (自動調整) 視訊串流。 媒體服務會將串流傳遞給要求的客戶。

從媒體服務 2.10 版起，當您建立通道時，可以指定您的通道接收輸入串流的方式。 您也可以指定是否要讓通道執行您串流的即時編碼。 您有兩個選擇：

* **傳遞**：如果您想要使用以多位元速率串流 (傳遞串流) 作為輸出的內部部署即時編碼器，請指定這個值。 在此情況下，連入的串流會傳遞至輸出，無須任何編碼。 這是在 2.10 版以前的通道行為。 本文提供有關使用此類型通道的詳細資訊。
* **即時編碼**：如果您打算使用媒體服務將單一位元速率即時串流編碼成多位元速率串流，請選擇這個值。 在**執行**狀態中離開即時編碼通道會產生費用。 建議您在即時串流事件完成之後立即停止執行的通道，以避免額外的每小時費用。 媒體服務會將串流傳遞給要求的客戶。

> [!NOTE]
> 本文討論未啟用而無法執行即時編碼的通道屬性。 如需使用已啟用執行即時編碼通道的相關資訊，請參閱 [使用 Azure 媒體服務建立多位元速率串流的即時串流](media-services-manage-live-encoder-enabled-channels.md)。
>
>如需建議的內部部署編碼器相關資訊，請參閱[建議的內部部署編碼器](media-services-recommended-encoders.md)。

下圖顯示一個即時串流工作流程，這個流程利用內部部署即時編碼器以多位元速率 RTMP 或 Fragmented MP4 (Smooth Streaming) 串流做為輸出。

![即時工作流程][live-overview]

## <a id="scenario"></a>常見即時串流案例
下列步驟描述當我們建立一般即時串流應用程式時，會涉及到的各種工作。

1. 將攝影機連接到電腦。 啟動並設定內部部署即時編碼器，讓它以多位元速率 RTMP 或 Fragmented MP4 (Smooth Streaming) 串流做為輸出。 如需詳細資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](https://go.microsoft.com/fwlink/?LinkId=532824)。

    您也可以在建立通道之後執行此步驟。
2. 建立並啟動通道。

3. 擷取通道內嵌 URL。

    即時編碼器會使用內嵌 URL 來傳送串流到通道。
4. 擷取通道預覽 URL。

    使用此 URL 來確認您的通道會正確接收即時串流。
5. 建立程式。

    使用 Azure 入口網站時，建立程式也會建立資產。

    使用 .NET SDK 或 REST 時，您必須建立資產並指定要在建立程式時使用此資產。
6. 發行與程式相關聯的資產。   

    >[!NOTE]
    >建立 Azure 媒體服務帳戶時，**預設**串流端點會新增至 [已停止] 狀態的帳戶。 您想要串流內容的串流端點必須處於 [執行中] 狀態。

7. 當您準備好開始串流和封存時，請啟動程式。

8. 即時編碼器會收到啟動公告的信號 (選擇性)。 公告會插入輸出串流中。

9. 每當您想要停止串流處理和封存事件時，請停止程式。

10. 刪除程式 (並選擇性地刪除資產)。     

## <a id="channel"></a>通道和其相關元件的說明
### <a id="channel_input"></a>頻道輸入 (內嵌) 組態
#### <a id="ingest_protocols"></a>嵌入串流通訊協定
媒體服務會使用多位元速率分散 MP4 和多位元速率 RTMP 做為串流通訊協定來支援內嵌即時摘要。 選取 RTMP 內嵌串流通訊協定時，會為通道建立兩個 ingest(input) 端點：

* **主要 URL**：指定通道主要 RTMP 內嵌端點的完整 URL。
* **次要 URL** (選擇性)：指定通道次要 RTMP 內嵌端點的完整 URL。

如果您想要改善內嵌串流的持久性、容錯 (以及編碼器容錯移轉和容錯)，特別是針對下列案例，請使用次要 URL：

- 單一編碼器雙重推送至主要和次要 URL：

    這個案例的主要目的是為網路變動起伏和快速變換提供恢復功能。 部分 RTMP 編碼器無法妥善處理網路中斷連線。 當發生網路中斷連線時，編碼器可能會停止編碼，然後在重新連線時不會傳送緩衝處理過的資料。 這會導致不連續和資料遺失。 網路中斷連線會因為網路不佳或 Azure 端正在進行維護而發生。 主要/次要 URL 會減少網路問題，並提供受控制的升級程序。 每次已排程的網路發生中斷連線時，媒體服務會管理主要和次要中斷連線，並提供兩者之間的延遲中斷連線。 接著，編碼器會有時間持續傳送資料，並再次重新連線。 中斷連線的順序可以是隨機的，但是主要/次要或次要/主要 URL 之間一定會有延遲。 在這種情況下，編碼器仍是單一失敗點。

- 多個編碼器的情況下，每個編碼器會推送到專用點：

    這種情況下會提供兩個編碼器和擷取備援。 在這個案例中，encoder1 會推送至主要 URL，而 encoder2 會推送至次要 URL。 當編碼器失敗時，其他編碼器可以持續傳送資料。 可以維持資料備援，因為媒體服務不會同時中斷主要和次要 URL 的連線。 此案例假設編碼器是時間同步處理，並提供完全相同的資料。  

- 多個編碼器雙重推送至主要和次要 URL：

    在此案例中，兩個編碼器會將資料推送至主要和次要 URL。 這提供最佳的可靠性及容錯功能，以及資料備援。 此案例可容許兩個編碼器皆失敗並且中斷連線，即使一個編碼器停止運作。 它假設編碼器是時間同步處理，並提供完全相同的資料。  

如需 RTMP 即時編碼器的詳細資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](https://go.microsoft.com/fwlink/?LinkId=532824)。

#### <a name="ingest-urls-endpoints"></a>內嵌 URL (端點)
通道提供一個輸入端點 (內嵌 URL)，您可以在即時編碼器中指定這個端點，這樣編碼器就可以將資料流推播到您的通道。   

當您建立通道時，您可以取得內嵌 URL。 若您要取得這些 URL，通道不一定要在 **執行** 狀態。 當您準備好開始將資料推入通道，通道必須處於 **執行** 狀態。 通道開始內嵌資料後，您可以透過預覽 URL 來預覽資料流。

您可以選擇透過 SSL 連線來內嵌 Fragmented MP4 (Smooth Streaming) 即時資料流。 若要透過 SSL 擷取，請務必將擷取 URL 更新為 HTTPS。 目前，您無法內嵌 RTMP over SSL。

#### <a id="keyframe_interval"></a>主要畫面格間隔
當您使用內部部署即時編碼器來產生多位元速率資料流時，主要畫面格間隔會指定圖片群組 (GOP) 持續期間以供該外部編碼器使用。 當通道收到此內送串流之後，您能以下列任一格式將您的即時串流傳遞到用戶端播放應用程式：Smooth Streaming、HTTP (DASH) 上的動態彈性資料流，以及 HTTP 即時串流 (HLS)。 在執行即時資料流時，會一律動態封裝 HLS。 依預設，媒體服務會根據從即時編碼器收到的主要畫面格間隔，自動計算 HLS 區段封裝比例 (每一個區段的片段)。

下表顯示如何計算區段持續時間：

| 主要畫面格間隔 | HLS 區段封裝比例 (FragmentsPerSegment) | 範例 |
| --- | --- | --- |
| 小於或等於 3 秒 |3:1 |如果 KeyFrameInterval (或 GOP) 為 2 秒鐘，則預設 HLS 區段封裝比率會是 3 比 1。 這會建立 6 秒 HLS 區段。 |
| 3 到 5 秒 |2:1 |如果 KeyFrameInterval (或 GOP) 為 4 秒鐘，則預設 HLS 區段封裝比率會是 2 比 1。 這會建立 8 秒 HLS 區段。 |
| 大於 5 秒 |1:1 |如果 KeyFrameInterval (或 GOP) 為 6 秒鐘，則預設 HLS 區段封裝比率會是 1 比 1。 這會建立 6 秒 HLS 區段。 |

您可以設定通道的輸出並在 ChannelOutputHls 上設定 FragmentsPerSegment，即可變更每個區段比例的片段。

您也可以透過在 ChannelInput 上設定 KeyFrameInterval 屬性，以變更主要畫面格間隔值。 如果您明確設定 KeyFrameInterval，就會透過先前所述的規則計算 FragmentsPerSegment HLS 區段封裝比率。  

如果您明確設定 KeyFrameInterval 和 FragmentsPerSegment，媒體服務會使用您所設定的值。

#### <a name="allowed-ip-addresses"></a>允許的 IP 位址
您可以定義允許將視訊發行到這個通道的 IP 位址。 可以將允許的 IP 位址指定為下列其中一項︰

* 單一 IP 位址 (例如 10.0.0.1)
* 使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 10.0.0.1/22)
* 使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 10.0.0.1(255.255.252.0))

如果未指定 IP 位址而且也未定義規則，則不允許任何 IP 位址。 若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。

### <a name="channel-preview"></a>通道預覽
#### <a name="preview-urls"></a>預覽 URL
通道會在進一步處理和傳遞之前，提供您用來預覽及驗證串流的預覽端點 (預覽 URL)。

當您建立通道時，您可以取得預覽 URL。 若您要取得此 URL，通道不一定要在 **執行** 狀態。 通道開始內嵌資料後，您就可以預覽您的資料流。

無論指定的輸入類型為何，目前預覽串流都只能以分散式 MP4 (Smooth Streaming) 格式傳遞。 您可以使用 [Smooth Streaming 健全狀況監視](http://playready.directtaps.net/smoothstreaming/)播放器測試 Smooth Stream。 您也可以使用裝載於 Azure 入口網站中的播放器來檢視您的串流。

#### <a name="allowed-ip-addresses"></a>允許的 IP 位址
您可以定義允許連接到預覽端點的 IP 位址。 如果沒有指定 IP 位址，將會允許任何 IP 位址。 可以將允許的 IP 位址指定為下列其中一項︰

* 單一 IP 位址 (例如 10.0.0.1)
* 使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 10.0.0.1/22)
* 使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>通道輸出
如需通道輸出的相關資訊，請參閱[主要畫面格間隔](#keyframe_interval)一節。

### <a name="channel-managed-programs"></a>通道管理程式
通道與程式相關聯，而您可以使用程式來控制即時串流中區段的發行和儲存。 通道會管理程式。 通道和程式的關聯性類似於傳統媒體，此處的通道有常數內容資料流，而程式的範圍是該通道上的某些計時事件。

設定 **封存時間範圍** 長度，即可指定您想要保留程式之錄製內容的時數。 此值最小可以設定為 5 分鐘，最大可以設定為 25 個小時。 封存時間範圍長度也會指出用戶端可以從目前即時位置及時往回搜尋的最大時間量。 程式在超過指定的時間量後還是可以執行，但是會持續捨棄落後時間範圍長度的內容。 此屬性的這個值也會決定用戶端資訊清單可以成長為多長的時間。

每個程式都與儲存串流內容的資產相關聯。 資產會對應到 Azure 儲存體帳戶中的區塊 Blob 容器，而資產中的檔案則會儲存為該容器中的 Blob。 若要發行程式讓您的客戶檢視串流，您必須建立相關聯資產的隨選定位器。 您可以使用此定位器來建置可提供給用戶端的串流 URL。

通道支援最多三個同時執行的程式，因此您可以建立相同內送串流的多個封存。 您可以視需要發行和封存事件的不同部分。 例如，假設您的商務需求是封存 6 小時的程式，但只廣播最後 10 分鐘。 為了達成此目的，您必須建立兩個同時執行的程式。 其中一個程式設定為封存 6 小時的事件，但是未發行該程式。 另一個程式則設定為封存 10 分鐘，並發行程式。

您不應該將現有程式重複用於新的事件。 而是針對每個事件建立新的程式。 當您準備好開始串流和封存時，請啟動程式。 每當您想要停止串流處理和封存事件時，請停止程式。

若要刪除封存的內容，請停止並刪除程式，然後刪除相關聯的資產。 如果程式使用資產，則無法刪除它。 必須先刪除程式。

即使在停止並刪除程式之後，使用者還是可以視需求將封存的內容串流為視訊，直到您未刪除資產。 如果想要保留封存的內容，但不要讓它可進行串流處理，請刪除串流定位器。

## <a id="states"></a>通道狀態和計費
通道目前狀態的可能值包括︰

* **已停止**：這是通道建立後的初始狀態。 在此狀態下，通道屬性可以更新，但是不允許串流。
* **正在啟動**：正在啟動通道。 在此狀態期間允許任何更新或串流。 如果發生錯誤，通道會回到**已停止**狀態。
* **Running**：通道可以處理即時串流。
* **正在停止**：正在停止通道。 在此狀態期間允許任何更新或串流。
* **正在刪除**：正在刪除通道。 在此狀態期間允許任何更新或串流。

下表顯示通道狀態如何對應至計費模式。

| 通道狀態 | 入口網站 UI 指標 | 是否計費？ |
| --- | --- | --- | --- |
| **啟動中** |**啟動中** |無 (暫時性狀態) |
| **執行中** |**就緒** (沒有執行中的程式)<p><p>或<p>**串流** (至少一個執行中的程式) |yes |
| **停止中** |**停止中** |無 (暫時性狀態) |
| **已停止** |**已停止** |否 |

## <a id="cc_and_ads"></a>隱藏式字幕和廣告插入
下表示範支援的隱藏式字幕和廣告插入標準。

| 標準 | 注意 |
| --- | --- |
| CEA-708 和 EIA-608 (708/608) |CEA-708 和 EIA-608 是美國和加拿大的隱藏式字幕標準。<p><p>目前只有編碼的輸入資料流附帶字幕時，才能播放字幕。 您使用的即時媒體編碼器，必須可以將 608 或 708 字幕插入至已傳送至媒體服務的已編碼資料流。 媒體服務會將內含字幕的內容傳遞給您的檢視器。 |
| .ismt 裡面附帶字幕 (Smooth Streaming 文字播放軌) |媒體服務動態封裝功能可讓您的用戶端以下列任一格式來串流內容：DASH、HLS 或 Smooth Streaming。 不過，如果您內嵌 Fragmented MP4 (Smooth Streaming) 而且在 .ismt 裡面附帶字幕 (Smooth Streaming 文字播放軌)，您就只能將資料流傳遞至 Smooth Streaming 用戶端。 |
| SCTE-35 |SCTE-35 是數位訊號系統，可用來提示廣告插入。 下游接收端會使用信號並根據分配的時間，將廣告切割成資料流。 SCTE 35 必須以鬆散播放軌的形式傳送至輸入資料流中。<p><p>目前，唯一支援附帶廣告訊號的輸入資料流格式是 Fragmented MP4 (Smooth Streaming)。 唯一支援的輸出格式也是 Smooth Streaming。 |

## <a id="considerations"></a>考量
使用內部部署即時編碼器並將多位元速率資料流傳送到通道時，請注意以下限制：

* 確定您的網際網路速度夠快，足以將資料傳送至內嵌點。
* 使用次要內嵌 URL 時會佔用額外的頻寬。
* 內送的多位元速率資料流最多可以有 10 個視訊品質等級 (亦稱為圖層)，以及最多 5 個音軌。
* 任何視訊品質等級的最高平均位元速率，應低於 10 Mbps。
* 所有視訊和音訊串流的平均位元速率彙總，應低於 25 Mbps。
* 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。 如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。
* 您可以在通道中內嵌單一位元速率。 但是，因為通道不會處理串流，用戶端應用程式也會收到單一位元速率資料流。 (不建議這個選項。)

以下是其他與通道和相關元件應用有關的注意事項：

* 每當您重新設定即時編碼器，請呼叫通道上的 **重設** 方法。 重設通道之前，您必須停止程式。 重設通道之後，請重新啟動程式。

  > [!NOTE]
  > 當您重新啟動程式時，您需要將它與新資產產生關聯並建立新的定位器。 
  
* 只有當通道處於**執行中**的狀態，且通道上的所有程式皆已停止時，才能停止通道。
* 依預設，您最多只能在媒體服務帳戶中新增 5 個通道。 如需詳細資訊，請參閱 [配額和限制](media-services-quotas-and-limitations.md)。
* 只有當您的通道處於 **執行中** 狀態時，才會向您計費。 如需詳細資訊，請參閱[通道狀態和帳單寄送](media-services-live-streaming-with-onprem-encoders.md#states)一節。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>相關主題
[建議的內部部署編碼器](media-services-recommended-encoders.md)

[Azure 媒體服務的分散 MP4 即時內嵌規格](media-services-fmp4-live-ingest-overview.md)

[Azure 媒體服務概觀和常見案例](media-services-overview.md)

[媒體服務概念](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
