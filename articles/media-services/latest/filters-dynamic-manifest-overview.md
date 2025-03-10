---
title: 篩選器和 Azure 媒體服務動態資訊清單 | Microsoft Docs
description: 本主題說明如何建立篩選器，讓您的用戶端可以使用篩選器來串流特定的資料流區段。 媒體服務會建立動態資訊清單來完成此選擇性資料流。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: 3a496aa5dc08ac59fb51f8bf3010bd1edf1e605d
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447934"
---
# <a name="dynamic-manifests"></a>動態資訊清單

媒體服務根據預先定義的篩選器提供**動態資訊清單**。 定義篩選器 (請參閱[定義篩選器](filters-concept.md)) 之後，您的用戶端便可使用篩選器來串流處理您視訊的特定轉譯或子剪輯。 用戶端會在資料流 URL 中指定篩選器。 篩選器可以套用至自適性串流通訊協定：Apple HTTP 即時串流 (HLS)、MPEG-DASH 和 Smooth Streaming。 

下表顯示包含篩選器之 URL 的一些範例：

|通訊協定|範例|
|---|---|
|HLS|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|


> [!NOTE]
> 動態資訊清單不會變更資產和該資產的預設資訊清單。 您的用戶端可以選擇要求包含或不含篩選器的資料流。 
> 

本主題說明**動態資訊清單**相關的概念，並提供您可能會想使用此功能的案例範例。

## <a name="manifest-files-overview"></a>資訊清單檔概觀

當您將資產編碼以進行彈性位元速率資料流時，會建立 **資訊清單** (播放清單) 檔案 (此檔案是以文字或 XML 為基礎)。 **資訊清單** 檔案包含資料流中繼資料，例如：資料軌類型 (音訊、視訊或文字)、資料軌名稱、開始和結束時間、位元速率 (品質)、資料軌語言、簡報視窗 (持續時間固定的滑動視窗)，視訊轉碼器 (FourCC)。 此檔案也會透過提供下一個可播放視訊片段及其位置的相關資訊，來指示播放程式擷取下一個片段。 片段 (或區段) 實際上是視訊內容的「區塊」。

以下是 HLS 資訊清單檔案的範例： 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>取得並檢查資訊清單檔案

根據應將您資料流的哪個資料軌 (即時或點播視訊) 包含至動態建立的資訊清單中，來指定篩選器資料軌屬性條件清單。 您必須先載入 Smooth Streaming 資訊清單，才能取得並檢查資料軌的屬性。

[使用 .NET 上傳、編碼及串流處理檔案](stream-files-tutorial-with-api.md)教學課程說明如何使用 .NET 建置串流 URL (請參閱[建置 URL](stream-files-tutorial-with-api.md#get-streaming-urls) 一節)。 如果您執行應用程式，其中一個 URL 指向 Smooth Streaming 資訊清單：`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`。<br/>複製該 URL 並將它貼上到瀏覽器的網址列中。 瀏覽器會下載該檔案。 您可以在您選擇的文字編輯器中開啟它。

如需 REST 範例，請參閱[使用 REST 上傳、編碼及串流處理檔案](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>監視視訊資料流的位元速率

您可以使用 [Azure 媒體播放器示範頁面](http://aka.ms/amp) \(英文\) 來監視視訊資料流的位元速率。 示範頁面會在 [Diagnostics] (診斷) 索引標籤中顯示診斷資訊：

![Azure 媒體播放器診斷][amp_diagnostics]

## <a name="rendition-filtering"></a>轉譯篩選

您可以選擇將資產編碼成多個編碼設定檔 (H.264 Baseline、H.264 High、AACL、AACH、Dolby Digital Plus)，及多個高品質位元速率。 不過，並非所有的用戶端裝置都支援您所有的資產和位元速率。 例如，較舊的 Android 裝置只支援 H.264 Baseline+AACL。 將較高的位元速率傳送到無法獲益的裝置，會浪費頻寬和裝置計算。 這類裝置必須將所有指定的資訊解碼，才能縮小以顯示。

您可以使用動態資訊清單建立裝置設定檔，例如行動、主控台、HD/SD 等，並包括您想要納入設定檔中的資料軌和品質。

![轉譯篩選範例][renditions2]

下列範例使用編碼器將夾層資產編碼成七個 ISO MP4 視訊轉譯 (從 180p 到 1080p)。 編碼資產可以動態地封裝至下列任何一個資料流通訊協定：HLS、MPEG DASH 和 Smooth。  圖表頂端顯示不含篩選器的資產其 HLS 資訊清單 (包含全部七個轉譯)。  左下角顯示名為 "ott" 的篩選器已套用到 HLS 資訊清單。 "ott" 篩選器指定要移除所有不到 1 Mbps 的位元速率，因此將最差的兩個品質等級從回應中去除。 右下角顯示名為 "mobile" 的篩選器已套用到 HLS 資訊清單中。 "mobile" 篩選器指定要移除的解析度大於 720p 的轉譯，因此去除兩個 1080p 的轉譯。

![轉譯篩選][renditions1]

## <a name="removing-language-tracks"></a>移除語言資料軌
您的資產可能包含多個音訊語言，例如英文、西班牙文、法文等。通常，播放程式 SDK 管理員會預設音訊資料軌選取範圍，以及每個使用者可選擇的可用音訊資料軌。 開發這類的播放程式 SDK 相當有挑戰性，因為在各個裝置特有的播放程式架構之間有不同的實作方式。 此外播放程式 API 在某些平台上受到限制，且不包含音訊選擇功能，因此使用者無法選取或變更預設的音訊資料軌。有了資產篩選器，您可以藉由建立只包含所需音訊語言的篩選器來控制行為。

![語言資料軌篩選][language_filter]

## <a name="trimming-start-of-an-asset"></a>修剪資產開頭
在大部分的即時資料流事件中，操作人員必須在實際的事件前先進行測試。 例如，他們可以在事件開始前包含如下 slate 訊息：「程式將立刻開始」。 如果程式正在進行封存，則測試和靜態圖像資料也會一併封存並包含在簡報中。 但是此資訊不會對用戶端顯示。 透過動態資訊清單，您可以建立開始時間篩選器，並從資訊清單中移除不必要的資料。

![開始修剪][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>從即時封存中建立子剪輯 (檢視)
許多即時事件的執行時間長，且即時封存可能包括多個事件。 即時事件結束後，廣播者可能想要將即時封存分解成邏輯程式的啟動和停止序列。 接下來，個別發佈這些虛擬程式，但不後續處理即時封存，亦不建立個別的資產 (這無法運用 CDN 中現有快取片段的優點)。 例如足球或籃球比賽的球季、棒球賽局，或是任何體育節目的個別事件，都是此類虛擬程式的範例。

使用動態資訊清單，您便可以使用開始/結束時間建立篩選器，並在即時封存頂端建立虛擬檢視。 

![子剪輯篩選][subclip_filter]

已篩選的資產：

![滑動][skiing]

## <a name="adjusting-presentation-window-dvr"></a>調整簡報視窗 (DVR)
目前，Azure 媒體服務提供持續時間可設為 5 分鐘到 25 小時的循環封存。 資訊清單篩選可以在封存頂端建立循環 DVR 視窗封存，而不會刪除媒體。 在許多情況下，廣播者想要提供受限制的 DVR 時段以隨著即時邊緣移動，並同時保留更大的封存視窗。 廣播者可能會想要使用超出 DVR 視窗的資料來反白顯示剪輯，或者想要為不同的裝置提供不同的 DVR 視窗。 例如，大多數的行動裝置不處理大型 DVR 視窗 (您可以讓行動裝置有 2 分鐘長的 DVR 視窗，而桌上型用戶端的有一小時長)。

![DVR 視窗][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>調整 LiveBackoff (即時位置)
資訊清單篩選可用來移除即時程式其即時邊緣幾秒鐘的時間。 篩選可讓廣播者在檢視者收到資料流前 (倒退 30 秒) 先觀賞預覽發佈點的簡報，並建立廣告插入點。 廣播者接著可將這些廣告及時推送到其用戶端架構，讓廣播者能夠收到與處理資訊，才有機會播放廣告。

除了廣告支援外，LiveBackoff 設定可用來調整檢視者位置，讓用戶端移動並點閱即時邊緣時，仍然可以從伺服器取得片段，而非得到 404 或 412 HTTP 錯誤。

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>將多個規則結合成單一篩選器
您可以將多個篩選規則結合成單一篩選器。 例如，您可以定義一個「範圍規則」，將靜態圖像從即時封存中移除，並同時篩選出可用的位元速率。 套用多個篩選規則時，最終結果會是所有規則的交集。

![多個規則][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>結合多個篩選條件 (篩選器組合)

您也可以將多個篩選條件結合成單一 URL。 

下列案例示範您可能會想結合篩選條件的理由：

1. 您要篩選例如 Android 或 iPAD 等行動裝置的視訊品質 (以限制視訊品質)。 若要移除不必要的品質，您可以建立適用於裝置設定檔的帳戶篩選器。 帳戶篩選器可以用於相同媒體服務帳戶下所有的資產，而不需進一步的關聯。 
2. 您也想要修剪資產的開始和結束時間。 若要達到此目的，您可建立資產篩選器，並設定開始/結束時間。 
3. 您必須結合這兩個篩選器 (若不組合，您便需要將品質篩選加入修剪篩選，這會讓篩選器更難以使用)。

若要結合篩選條件，您必須設定資訊清單/播放清單 URL 的篩選條件名稱，並以分號分隔。 假設您擁有名為 *MyMobileDevice* 的篩選條件，可篩選品質，且您有另一個名為 *MyStartTime* 的篩選條件，用來設定特定的開始時間。 您可以將它們結合如下：

您可以結合最多三個篩選。 

如需詳細資訊，請參閱 [此部落格](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) 。

## <a name="considerations-and-limitations"></a>考量與限制

- 不應為 VoD 篩選器設定 **forceEndTimestamp**、**presentationWindowDuration** 和 **liveBackoffDuration** 的值。 它們僅適用於即時篩選器案例。 
- 動態資訊清單會在 GOP 界限 (主要畫面格) 中運作，因此修剪後能夠有精確的 GOP。 
- 您可以將相同的篩選器名稱用於帳戶和資產篩選器。 資產篩選器有較高的優先順序，因此會覆寫帳戶篩選器。
- 如果您更新篩選器，則資料流端點需要 2 分鐘的時間來重新整理規則。 如果內容是透過使用某些篩選器提供的 (並在 Proxy 和 CDN 快取中快取)，則更新這些篩選器會導致播放程式失敗。 建議您在更新篩選器之後清除快取。 如果這個選項無法執行，請考慮使用不同的篩選器。
- 客戶必須手動下載資訊清單，並剖析確切的 startTimestamp 和時間範圍。
    
    - 若要判斷 Asset 中資料軌的屬性，請[取得並檢查資訊清單檔案](#get-and-examine-manifest-files)。
    - 設定資產篩選器 timestamp 屬性的公式： <br/>startTimestamp = &lt;資訊清單中的開始時間&gt; +  &lt;預期的篩選器開始時間 (秒)&gt;*timescale


## <a name="next-steps"></a>後續步驟

下列文章說明如何以程式設計方式建立篩選器。  

- [使用 REST API 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
