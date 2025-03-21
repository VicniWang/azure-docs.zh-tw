---
title: 選擇 Azure 搜尋服務的定價層或 SKU - Azure 搜尋服務
description: Azure 搜尋服務可以在這些 SKU 佈建：免費、基本及標準，其中「標準」在各種資源組態和容量層級都有提供。
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: cf2359834aa79b1d3fef8b65e4ef4191eb6ff867
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467436"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>選擇 Azure 搜尋服務的定價層

在 Azure 搜尋服務中，[資源是建立](search-create-service-portal.md)在定價層或該服務之存留期內固定的 SKU。 層包括**免費**、**基本**或**標準**，其中**標準**在數個組態和容量都有提供。 大部分客戶一開始都會使用**免費**層來進行評估，然後再晉升到**標準**層以進行開發與生產環境部署。 您可以完成**免費**層上的所有快速入門和教學課程，包括耗用大量資料之認知搜尋的快速入門和教學課程。 

層級會反映裝載服務之硬體的特性 (而非功能)，並會依下列方式來加以區分：

+ 您可以建立的索引數目
+ 分割區的大小和速度 (實體儲存體)

雖然所有層 (包括**免費**層) 通常都提供功能同位，但較大的工作負載可以指定用於較高層級的需求。 例如，除非資料集剛好很小，否則[認知搜尋](cognitive-search-concept-intro.md)索引所擁有的長時間執行技能會在免費服務上逾時。

> [!NOTE] 
> 功能同位的例外狀況是[索引子](search-indexer-overview.md)，它們並不適用於 S3HD。
>

在層中，您可以[調整複本和分割區的資源](search-capacity-planning.md)以進行效能調整。 您可能從每個層中的兩或三個開始，然後可以暫時提高運算能力以應對繁重的索引工作負載。 調整層中資源層級的能力增加了彈性，但也使分析稍微變得複雜。 您可能需要嘗試實驗以查看具有較高資源/副本的較低層級，是否提供比具有較低資源的較高層級較佳的值和效能。 若要深入了解容量會進行調整的時機和原因，請參閱[效能和最佳化考量](search-performance-optimization.md)。

<!---
The purpose of this article is to help you choose a tier. It supplements the [pricing page](https://azure.microsoft.com/pricing/details/search/) and [Service Limits](search-limits-quotas-capacity.md) page with a digest of billing concepts and consumption patterns associated with various tiers. It also recommends an iterative approach for understanding which tier best meets your needs. 
--->

## <a name="how-billing-works"></a>計費的運作方式

在 Azure 搜尋服務中，當您在入口網站裡建立搜尋資源時有四種方式會產生成本：

* 新增用於一般編製索引和查詢工作的複本和分割區。 您會使用這兩者，但您可以選擇其他資源層級並付費來增加其中之一或兩者以增加容量。 
* 編製索引期間的資料輸出費用。 從 Azure SQL Database 或 Cosmos DB 資料來源提取資料時，您會在帳單中看到那些資源的交易費用。
* 僅針對[認知搜尋](cognitive-search-concept-intro.md)，在文件萃取期間的影像擷取，會根據從文件擷取的影像數目計費。 文字擷取目前是免費的。
* 僅針對[認知搜尋](cognitive-search-concept-intro.md)，以[內建認知技能](cognitive-search-predefined-skills.md)為基礎的擴充會按照認知服務資源進行計費。 擴充的計費費率如同您直接使用認知服務執行工作。

如果您不是使用[認知服務](cognitive-search-concept-intro.md)或 [Azure 搜尋服務索引子](search-indexer-overview.md)，您的成本只會與使用中的複本和分割區相關聯，用於一般編製索引和查詢工作負載。

### <a name="billing-for-general-purpose-indexing-and-queries"></a>一般用途編製索引和查詢的計費

針對 Azure 搜尋服務作業，所需了解的最重要計費概念是「搜尋單位」(SU)。 因為 Azure 搜尋服務需要複本和分割區才能編製索引和查詢，因此僅依據其中之一來計費沒有意義。 相反地，會將兩者合併來計費。 

SU 是服務所用複本和分割區的乘積：**`(R X P = SU)`**

每個服務都以一個 SU (一個複本乘以一個分割區) 作為最小值。 任何服務的最大值均為 36 個 SU，其可透過多種方式達成：6 個資料分割 x 6 複本，或 3 個分割區 x 12 個複本等等。 使用少於總容量的情況很常見。 例如，3 個複本，3 個分割區，以 9 個 SU 計費。 

計費的費率是**每小時每 SU**，而且每個定價層會有漸進式較高的費率。 較高層級具有更大型且更快速的分割區，從而有助於該層級整體較高的每小時費率。 如需每一層的費率，請參閱 [定價詳細資料](https://azure.microsoft.com/pricing/details/search/)。 

大多數客戶只能線上提供總容量的一部分，其餘部分則保留。 就計費來說，所上線的分割區和複本數目 (使用 SU 公式來計算) 會決定您實際上每小時要支付多少金額。

### <a name="billing-for-image-extraction-in-cognitive-search"></a>認知搜尋服務中的影像擷取計費

如果您在認知服務編製索引管線中從檔案擷取影像，在您的 Azure 搜尋服務帳單中會針對該作業收費。 觸發影像擷取的參數是[索引子設定](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)中的 **imageAction**。 如果 **imageAction** 設為 none (預設值)，則不會有影像擷取的費用。

價格有可能會變更，但一律會在 Azure 搜尋服務的[價格詳細資料](https://azure.microsoft.com/pricing/details/search/)頁面記載。 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>認知搜尋服務中的內建技能收費

當您設定擴充管線後，在管線中使用的任何[內建技能](cognitive-search-predefined-skills.md)都是以機器學習模型為基礎。 那些模型是由認知服務提供。 在編製索引期間使用那些模型，其計費費率與您直接要求資源相同。

例如，假設有一個針對掃描的影像 JPEG 檔案進行光學字元辨識 (OCR) 所組成的管線，其產生的文字會推送到 Azure 搜尋服務索引以供自由格式檢索查詢。 您的編製索引管線會包含具有 [OCR 技能](cognitive-search-skill-ocr.md)的索引子，而該技能將會[附加到認知服務資源](cognitive-search-attach-cognitive-services.md)。 當您執行索引子時，執行 OCR 的費用會出現在認知資源帳單。

## <a name="tips-for-reducing-costs"></a>降低成本的秘訣

您無法關閉服務以降低費用。 專用資源在您服務的整個生命週期內是 24 小時運行，專供您使用。 降低費用的唯一方法，是將複本和分割區減少到仍然提供可接受的效能和 [SLA 合規性](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的低層級。 

降低成本的一個手段，是選擇每小時費率較低的層。 S1 的每小時費率低於 S2 或 S3 的費率。 假設您針對負載預測的下限佈建服務，如果您超出服務範圍，您可以建立第二個較大的階層式服務，在第二個服務上重建索引，然後刪除第一個服務。 

如果您已完成內部部署伺服器的容量規劃，則您知道「購買」是常見的，這樣您就能夠處理預估的成長。 但是透過雲端服務，您可以更積極地追求成本節約，因為您沒有鎖定特定的購買。 如果目前服務不足，您可以隨時切換至較高層的服務。

### <a name="capacity-drill-down"></a>容量向下鑽研

在 Azure 搜尋服務中，容量是由「複本」和「分割區」所構成。 

+ 複本是搜尋服務的執行個體，每個複本都會裝載一份經過負載平衡的索引。 例如，具有 6 個複本的服務，會有 6 份已在服務中載入的每個索引。 

+ 資料分割會儲存索引並自動將可搜尋的資料分割：兩個資料分割會將索引對半分割、三個資料分割會將索引分割成三等份，依此類推。 就容量來說，「資料分割大小」是定價層的主要區分功能。

> [!NOTE]
> 所有**標準**層都支援[彈性組合複本和資料分割](search-capacity-planning.md#chart)，讓您可以藉由變更平衡來[讓系統偏向速度或儲存取向](search-performance-optimization.md)。 **Basic** 層最多可提供三個複本以實現高可用性，但只有一個資料分割。 **免費**層不會提供專用資源：其計算資源是由多個訂閱者共用。

### <a name="more-about-service-limits"></a>深入了解服務限制

服務會裝載資源，例如索引、索引子等等。 每一個定價層都會對可以建立的資源數量施加[服務限制](search-limits-quotas-capacity.md)。 因此，索引 (和其他物件) 數目上限是定價層的第二個區分功能。 當您在入口網站中檢閱各個選項時，請注意索引數目限制。 索引子、資料來源和技能等其他資源則會與索引限制掛勾。

## <a name="consumption-patterns"></a>耗用量模式

大部分客戶一開始會使用**免費**服務，並且會無限期地保留下來，然後選擇其中一個**標準**層來供重要的開發或生產工作負載使用。 

![Azure 搜尋服務層](./media/search-sku-tier/tiers.png "Azure 搜尋服務定價層")

在任一端都有**基本**和 **S3 HD** 可供重要但非典型的耗用量模式使用。 **基本**適用於小型生產工作負載：其提供 SLA、專用資源、高可用性，但適量的儲存體，最多總共可達 2 GB。 此定價層是設計給一直未充分使用可用容量的客戶來使用。 針對 ISV、合作夥伴、[多租用戶解決方案](search-modeling-multitenant-saas-applications.md)或任何會呼叫大量小型索引的任何組態等典型工作負載，則適合使用另一端的 **S3 HD**。 如果**基本**或 **S3 HD** 層合用，情形通常很明顯，但如果您想要確認，請在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) 張貼文章或[連絡 Azure支援](https://azure.microsoft.com/support/options/)以尋求進一步的指引。

讓我們將焦點轉到更常見的標準層，**S1-S3** 是逐漸增加的容量層級，具有資料分割大小轉折點以及索引、索引子和推論資源數目上限：

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| 資料分割大小|  25 GB | 100 GB | 200 GB |  |  |  |  |
| 索引和索引子限制| 50 | 200 | 200 |  |  |  |  |

如果必須有專用資源和多個資料分割，**S1** 是常見的選擇。 **S1** 具有 25 GB 的資料分割，可供最多 12 個資料分割使用，如果您透過複本來獲得最多個資料分割，則 S1 的每一服務限制是 300 GB (如需更多已經過平衡的組合，請參閱[配置資料分割和複本](search-capacity-planning.md#chart))。

入口網站和定價頁面將焦點放在資料分割大小和儲存體，但對於每個定價層來說，所有計算能力 (磁碟容量、速度、CPU) 都會隨價格線性增加。 **S2** 複本的速度快過 **S1**，**S3** 的速度快過 **S2**。 **S3** 層打破了一般的線性計算定價模式，其 I/O 速度的增加率超過線性。 如果您預期 I/O 會成為瓶頸，**S3** 所能提供的 IOPS 遠非較低定價層可比。

**S3** 和 **S3 HD** 都由相同的高容量基礎結構支援，但各自會以不同方式達到最大限制。 **S3** 的目標是較少量的極大型索引。 因此，其最大限制是資源限制 (每項服務 2.4 TB)。 **S3 HD** 的目標是大量極小型索引。 到達 1,000 個索引時，**S3 HD** 就達到其索引條件約束形式的限制。 如果您是需要 1,000 個以上索引的 **S3 HD** 客戶，請連絡 Microsoft 支援以了解如何繼續進行。

> [!NOTE]
> 以前必須擔心文件限制，但新的服務再也沒有這方面的顧慮。 如需哪些情況仍適用文件限制的詳細資訊，請參閱[服務限制：文件限制](search-limits-quotas-capacity.md#document-limits)。
>

## <a name="evaluate-capacity"></a>評估容量

容量與執行服務的費用密切相關。 定價層會對兩個層級 (儲存體和資源) 施加限制，因此兩者都應考慮到，原因是兩者之間先到達者，就是有效的限制。 

商務需求通常會指定您需要的索引數目。 例如，大型的文件存放庫全域索引，或可能是以區域、應用程式或商務利基為基礎的多個索引。

若要判斷索引的大小，您必須[建置一個](search-create-index-portal.md)索引。 Azure 搜尋服務中的資料結構主要是[反向索引](https://en.wikipedia.org/wiki/Inverted_index)，這種索引的特性不同於來源資料。 若為反向索引，其大小和複雜性取決於內容，而不一定是您送入的資料量。 具有大量重複內容的大型資料來源所產生的索引，可能會比內容變化極大的資料集所產生的索引還小。 因此，不太可能根據原始資料集的大小來推斷出索引大小。

> [!NOTE] 
> 雖然預估未來對索引和儲存空間的需求可能感覺像是猜測，但值得這麼做。 如果就結果來說某個定價層的容量太低，您就必須在更高的定價層佈建新服務，然後[重新載入您的索引](search-howto-reindex.md)。 無法從某個 SKU 就地升級到另一個 SKU 的相同服務。
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>步驟 1：使用免費層進行粗略預估

其中一個預估容量的方法是從**免費**層開始。 請回想一下，**免費**服務最多可提供 3 個索引，50 MB 的儲存體，和 2 分鐘的索引時間。 使用這些條件約束來預估預計的索引大小並不容易，但下列範例會示範一個方法：

+ [建立免費服務](search-create-service-portal.md)
+ 準備具代表性的小型資料集 (假設有 5000 份文件，取樣大小為 10%)
+ [建置初始索引](search-create-index-portal.md)，並注意它在入口網站中的大小 (假設有 30 MB)

假設這次取樣佔整個資料來源的 10% 且具有代表性，且如果所有文件都編製索引，則 30 MB 的索引會變成大約 300 MB。 有了這個初步的數字，您或許可以將該數量翻倍，來編列兩個索引 (開發和生產) 的預算，所以總共的儲存體需求是 600 MB。 **基本**層就能輕鬆滿足此需求，因此您可以從這一個定價層開始。

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>步驟 2：使用可計費層進行精簡預估

有些客戶會想從使用專用資源來開始，以便因應較大的取樣和處理時間，然後在開發期間實際預估索引數量、大小和查詢量。 一開始，客戶會依據最可能的預估值來佈建服務，然後在開發專案成熟時，小組通常就會知道現有服務容量是超過預計的生產工作負載容量還是不足。 

1. [檢閱每一個定價層的服務限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits)，以判斷較低定價層是否能支援您需要的索引數量。 橫跨**基本**-**S1**- **S2** 層的索引限制分別為 15-50-200。

1. [在可計費的定價層建立服務](search-create-service-portal.md)：

    + 如果您才剛開始學習，請從較低的定價層開始，例如**基本**或 **S1**。
    + 如果明顯需要大規模的索引和查詢負載，請從較高的定價層開始，例如 **S2** 或甚至 **S3**。

1. [建置初始索引](search-create-index-portal.md)，以判斷來源資料轉譯為索引的情形。 這是唯一能預估索引大小的方式。

1. 在入口網站中[監視儲存體、服務限制、查詢量和延遲](search-monitor-usage.md)。 入口網站會顯示每秒查詢數、節流的查詢和搜尋延遲；這幾點全都能協助您決定所使用的定價層是否合適。 除了入口網站的計量外，您還可以啟用[搜尋流量分析](search-traffic-analytics.md)來設定深層監視，例如點選連結分析。 

對於分析來說，索引數目和大小有同樣的價值，原因是達到上限的條件是完全用盡儲存體 (資料分割) 或資源 (索引、索引子等等)，視何者先達到。 入口網站可協助您追蹤這兩項，在 [概觀] 頁面上並排顯示目前的使用量和最大限制。

> [!NOTE]
> 如果文件包含無關的資料，儲存體需求可能會過度膨脹。 理想情況是，文件只包含搜尋體驗所需的資料。 二進位資料不可搜尋，應該分開存放 (或許存放在 Azure 資料表或 Blob 儲存體中)，並且在索引中要有一個欄位用來保存外部資料的 URL 參考。 個別文件的大小上限是 16 MB (如果您在單一要求中大量上傳多個文件，則會小於 16 MB)。 [Azure 搜尋服務中的服務限制](search-limits-quotas-capacity.md)有更多資訊。
>

**查詢量的考量**

在微調效能時，每秒查詢數 (QPS) 計量會顯得重要，但除非您一開始就預期會有大量的查詢，否則此計量通常不是定價層的考量事項。

標準層能提供平衡的複本與分割區，透過額外的複本來達到負載平衡以及額外的分割區來協助平行處理，以支援更快速的查詢往返。 佈建服務之後，就能對效能進行微調。

一開始就預期會有不斷大量查詢的客戶，應該考慮使用更高的定價層，以獲得更強大硬體的支援。 如果未能發生這些查詢量，您便可以將資料分割和複本離線，或甚至改用較低定價層的服務。 如需如何計算查詢輸送量的詳細資訊，請參閱 [Azure 搜尋服務效能和最佳化](search-performance-optimization.md)。


**服務等級協定**

**免費**層和預覽功能並未隨附[服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。 在所有可計費層中，SLA 會在您為您的服務佈建足夠的備援性時生效。 查詢 (讀取) SLA 時需要兩個 (含) 以上的複本。 查詢和檢索 (讀寫) SLA 時需要三個 (含) 以上的複本。 分割區數目不是 SLA 考量。 

## <a name="tips-for-tier-evaluation"></a>定價層評估秘訣

+ 了解如何建置有效率的索引，以及哪種重新整理方法的影響最少。 我們建議使用[搜尋流量分析](search-traffic-analytics.md)來獲得查詢活動見解。

+ 允許圍繞查詢來建置計量，並收集使用模式的相關資料 (在營業時間內查詢、在離峰時間編製索引)，並使用此資料來通知未來的服務佈建決策。 雖然對每小時或每日的頻率來說不實用，但您可以動態調整分割區和資源以因應計劃中的查詢量變化，而如果這些層級已保持夠久而必定會採取相應行動，則可因應雖非計劃中但已獲認可的變化。

+ 請記住，佈建量不足的唯一缺點是，您可能必須在實際需求大於預估需求時終止服務。 為避免服務中斷，建議您在相同訂用帳戶中使用更高的定價層建立一個新服務，並讓此服務並行執行，直到所有應用程式和要求都以新端點為目標才停止。

## <a name="next-steps"></a>後續步驟

請從**免費**層開始，並使用資料子集建置初始索引來了解其特性。 Azure 搜尋服務中的資料結構是反向索引，其中，反向索引的大小和複雜性取決於內容。 請記住，重複性高的內容所產生的索引，往往會比差異性高的內容所產生的索引小。 因此，決定索引儲存體需求的是內容特性，而不是資料集大小。

對於索引大小有了初步了解後，請在本文所討論的其中一個定價層 (**基本**或**標準**層) [佈建可計費服務](search-create-service-portal.md)。 放寬資料子集的任何人為條件約束，並[重建索引](search-howto-reindex.md)使其包含所有您實際想要搜尋的資料。

視需要[配置資料分割和複本](search-capacity-planning.md)，以取得所需的效能和規模。

如果效能和容量合適，您的工作就完成了。 否則，請在更符合需求的不同定價層重新建立搜尋服務。

> [!NOTE]
> 您的問題如果需要更多協助，請張貼到 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) 或[連絡 Azure 支援](https://azure.microsoft.com/support/options/)。
