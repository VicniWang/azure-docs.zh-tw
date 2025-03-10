---
title: 實體類型
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在 Language Understanding Intelligent Service (LUIS) 應用程式中新增實體 (您應用程式定義域中的關鍵資料)。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 61211db72928bebf0e1ea50cad6362337f66f0eb
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859604"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>實體類型和其在 LUIS 中的目的

實體是語句中作為您應用程式定義域關鍵資料的單字或片語。

## <a name="entity-compared-to-intent"></a>實體與意圖的比較

實體代表語句中您想要擷取的單字或片語。 一個語句可以包含許多實體，也可以完全不包含實體。 一個實體代表一個類別，其中包含相似物件 (地點、物件、人員、事件或概念) 的集合。 實體描述與意圖有關的資訊，有時是您應用程式執行其工作的必備要素。 例如，「新聞搜尋」應用程式可能包含「主題」、「來源」、「關鍵字」及「發佈日期」等實體，這些都是搜尋新聞的關鍵資料。 在旅遊預訂應用程式中，「地點」、「日期」、「航空公司」、「艙等」及「機票」則是航班預訂 (與「預訂航班」意圖相關) 的關鍵資訊。

相較之下，意圖代表了整個語句的預測。 

## <a name="entities-help-with-data-extraction-only"></a>實體僅有助於資料擷取

在實體上加上標籤或標記只能用於實體擷取，無助於意圖預測。

## <a name="entities-represent-data"></a>實體代表資料

實體是您想要從語句中提取的資料。 這可以是名稱、日期、產品名稱或任何單字的群組。 

|語句|實體|資料|
|--|--|--|
|購買 3 張到紐約的機票|預先建置的號碼<br>Location.Destination|3<br>紐約|
|購買 1 張 3 月 5 日從紐約到倫敦的機票|Location.Origin<br>Location.Destination<br>預先建置的 datetimeV2|紐約<br>倫敦<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>實體是選擇性的，但強烈建議使用

意圖是必要的，實體則為選擇性。 您不需要為應用程式中的每個概念建立實體，只需針對用戶端應用程式執行動作所需的部分建立實體即可。 

如果您的語句中沒有 Bot 繼續工作所需的詳細資料，就無須新增這些資料。 您可以隨著應用程式趨於成熟，稍後再新增這些資料。 

如果您不確定會如何使用該資訊，可以新增一些常見的預先建置實體，例如 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序數](luis-reference-prebuilt-ordinal.md)、[電子郵件](luis-reference-prebuilt-email.md)及[電話號碼](luis-reference-prebuilt-phonenumber.md)。

## <a name="label-for-word-meaning"></a>單字意義的標籤

如果單字選擇或單字排列相同，但意義不同，請勿以實體標記它。 

下列語句中的 `fair` 一字是一個同形異義字。 其拼字相同，但意義不同：

|語句|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

如果您想要讓事件實體尋找所有事件資料，請標記第一個語句中的 `fair` 一字，但不要標記第二個語句中的該字。

## <a name="entities-are-shared-across-intents"></a>實體會跨意圖共用

實體會在意圖間共用。 它們不屬於任何單一意圖。 意圖和實體可以在語意上相關，但並不具有獨佔關聯性。

在「為我預訂到巴黎的機票」語句中，「巴黎」是代表地點的實體。 LUIS 可透過辨識使用者語句中提到的實體，協助用戶端應用程式選擇要執行以滿足使用者要求的特定動作。

## <a name="mark-entities-in-none-intent"></a>標記 None 意圖中的實體

如果可能，所有意圖 (包括 **None** 意圖) 都應有標記的實體。 這可協助 LUIS 進一步了解實體在語句中的哪裡，以及實體周圍有哪些單字。 

## <a name="entity-status-for-predictions"></a>用於預測的實體狀態

LUIS 入口網站會在範例語句中的實體與標記實體不同時，或太靠近另一個實體以至於不清楚時，讓您知道有此狀況。 這會在範例語句中以紅色底線表示。 

如需詳細資訊，請參閱[實體狀態預測](luis-how-to-add-example-utterances.md#entity-status-predictions)。 

## <a name="types-of-entities"></a>實體類型

LUIS 會提供許多類型的實體。 您可以根據擷取資料的方式和資料在擷取之後的呈現方式，來選擇實體。

實體可以透過機器學習來擷取，如此可讓 LUIS 繼續了解實體在語句中出現的方式。 實體也可不使用機器學習來進行擷取，而是比對確切文字或規則運算式。 模式中的實體可以透過混合式實作來擷取。 

擷取實體後，實體資料就可以表示為單一的資訊單位，或與其他實體結合，以形成用戶端應用程式可使用的資訊單位。

|機器學習|可標記|教學課程|範例<br>Response|實體類型|目的|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**複合**](#composite-entity)|實體群組，無論何種實體類型。|
|✔|✔|[✔](luis-quickstart-intent-and-hier-entity.md)|[✔](luis-concept-data-extraction.md#hierarchical-entity-data)|[**階層式**](#hierarchical-entity)|簡單實體的群組。|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**清單**](#list-entity)|透過比對確切文字來擷取的項目和其同義字清單。|
|混合||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|難以判斷實體結尾的實體。|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**預建**](#prebuilt-entity)|已訓練為擷取各種資料類型。|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**規則運算式**](#regular-expression-entity)|要比對文字的使用者規則運算式。|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**簡單**](#simple-entity)|在字組或片語中包含單一概念。|

只有機器學習的實體需要針對範例語句中的每個意圖進行標記。 機器學習實體在透過[端點查詢](luis-concept-test.md#endpoint-testing)及[檢閱端點語句](luis-how-to-review-endoint-utt.md)來進行測試時，效果最佳。 

Pattern.any 實體需要在[模式](luis-how-to-model-intent-pattern.md)範本裡的範例 (而不是意圖裡使用者所提供的範例) 中進行標記。 

混合實體會使用實體偵測方法的組合。

## <a name="composite-entity"></a>複合實體

複合實體是由預建實體、簡單、規則運算式、清單和階層式等其他實體所組成的。 個別實體會構成一個完整的提體。 

當資料有下列特性時，最適用此實體：

* 彼此相關。 
* 在語句的內容中彼此相關。
* 使用各種實體類型。
* 需要由用戶端應用程式當作一個資訊單位進行分組和處理。
* 有各種使用者語句需要使用機器學習。

![複合實體](./media/luis-concept-entities/composite-entity.png)

[教學課程](luis-tutorial-composite-entity.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="hierarchical-entity"></a>階層式實體

階層式實體是一個依內容學習的簡單實體 (稱為子系) 類別。

當資料有下列特性時，最適用此實體：

* 簡單的實體。
* 在語句的內容中彼此相關。
* 使用特定文字來表示每個子系實體。 這些字的範例包括：from/to、leaving/headed to、away from/toward。
* 子系通常會在相同的語句中。 
* 需要由用戶端應用程式當作一個資訊單位進行分組和處理。

不應使用此實體的情況：

* 不論內容為何，您需要實體有完全相符的子系文字。 可改為使用[清單實體](#list-entity)。 
* 您需要與其他實體類型有父子關聯性的實體。 可使用[複合實體](#composite-entity)。

![階層式實體](./media/luis-concept-entities/hierarchical-entity.png)

[教學課程](luis-quickstart-intent-and-hier-entity.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#hierarchical-entity-data)<br>

### <a name="roles-versus-hierarchical-entities"></a>角色與階層式實體

模式的[角色](luis-concept-roles.md#roles-versus-hierarchical-entities)可解決與階層式實體相同的問題，但適用所有實體類型。 角色目前僅可在模式中使用。 角色無法在意圖的範例語句中使用。  

## <a name="list-entity"></a>清單實體

清單實體代表一組固定的封閉式相關字組及其同義字。 LUIS 並不會探索清單實體的額外值。 使用**建議**功能，以根據目前的清單查看適用於新字組的建議。 如果有多個清單實體具有相同的值，則在端點查詢中會傳回每個實體。 

當文字資料有下列特性時，最適用此實體：

* 是已知的組合。
* 此組合不會超過此實體類型的最大 LUIS [界限](luis-boundaries.md)。
* 語句中的文字是與同義字或正式名稱完全相符的項目。 LUIS 不會將清單用於完全相符之文字項目以外的範圍。 詞幹分析、複數及其他變化無法透過清單實體來解析。 若要管理變化，請考慮使用[模式](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)並搭配選擇性的文字語法。

![清單實體](./media/luis-concept-entities/list-entity.png)

[教學課程](luis-quickstart-intent-and-list-entity.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Pattern.any 實體

Pattern.any 是僅用於模式範本語句的可變長度預留位置，用來標記實體開始及結束的位置。  

以下時機適用此實體：

* 實體的結尾可能會與語句的其餘文字混淆。 
[教學課程](luis-tutorial-pattern.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#patternany-entity-data)

**範例**  
如果用戶端應用程式要根據書名來搜尋書籍，pattern.any 會擷取完整的書名。 為搜尋此書而使用 pattern.any 的範本語句是 `Was {BookTitle} written by an American this year[?]`。 

下表中，每個資料列都有兩個版本的語句。 上層語句是 LUIS 一開始看到語句的樣子，其中書名的開始處和結尾處並不清楚。 下層語句是使用模式進行擷取後，LUIS 了解書名為何的樣子。 

|語句|
|--|
|Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?<br>Was **The Man Who Mistook His Wife for a Hat and Other Clinical Tales** written by an American this year?|
|Was Half Asleep in Frog Pajamas written by an American this year?<br>Was **Half Asleep in Frog Pajamas** written by an American this year?|
|Was The Particular Sadness of Lemon Cake:A Novel written by an American this year?<br>Was **The Particular Sadness of Lemon Cake:A Novel** written by an American this year?|
|Was There's A Wocket In My Pocket! written by an American this year?<br>Was **There's A Wocket In My Pocket!** written by an American this year?|

## <a name="prebuilt-entity"></a>預建實體

預建實體是代表一般概念 (例如電子郵件、URL 和電話號碼) 的內建類型。 預先建置的實體名稱為保留名稱。 已新增至應用程式的[所有預建實體](luis-prebuilt-entities.md)都會在端點預測查詢中傳回 (如果有在語句中找到的話)。 

以下時機適用此實體：

* 資料符合預建實體所支援的常見使用案例 (以您的語言文化為基礎)。 

可以隨時新增或移除預建實體。 如果您發現範例語句中偵測到預建實體，而這使得您無法標記自訂實體，請移除應用程式中的預建實體，然後標記您的實體，最後再將預建實體新增回去。 

![Number 預先建置的實體](./media/luis-concept-entities/number-entity.png)

[教學課程](luis-tutorial-prebuilt-intents-entities.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#prebuilt-entity-data)

在開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案中已定義部分這些預建實體。 如果目前不支援您的特定文化特性或實體，請向專案提出。 

## <a name="regular-expression-entity"></a>規則運算式實體 

規則運算式最適用於未經處理的語句文字。 這會忽略大小寫並忽略文化特性變體。  在字元等級而非權杖等級的拼字檢查修改之後，才會套用規則運算式比對。 如果規則運算式太複雜 (例如使用許多方括號)，您便無法將運算式新增到模型中。 使用組件，但不是整個 [.Net Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) 程式庫。 

以下時機適用此實體：

* 以任何一致的變化來一致地格式化的資料。
* 規則運算式不需要 2 個層級以上的巢狀結構。 

![規則運算式實體](./media/luis-concept-entities/regex-entity.png)

[教學課程](luis-quickstart-intents-regex-entity.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

## <a name="simple-entity"></a>簡單實體 

簡單實體是描述單一概念並從機器學習內容學習到的一般實體。 由於簡單實體通常是名稱，例如公司名稱、產品名稱或其他類別的名稱，因此，使用簡單實體時可加入[片語清單](luis-concept-feature.md)，以提升所用名稱所代表的信號。 

以下時機適用此實體：

* 格式不一致但表示相同內容的資料。 

![簡單實體](./media/luis-concept-entities/simple-entity.png)

[教學課程](luis-quickstart-primary-and-secondary-data.md)<br/>
[實體的範例回應](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>實體限制

請檢閱[限制](luis-boundaries.md#model-boundaries)，以了解您可以將多少個每一種類型的實體新增到模型中。

## <a name="composite-vs-hierarchical-entities"></a>複合實體與階層式實體的比較

複合實體和階層式實體都有父子關係，且都是機器學習實體。 機器學習可讓 LUIS 根據不同的內容 (單字的排列) 理解實體。 複合實體較具彈性，因為它們允許以不同的實體類型作為子系。 階層實體的子系僅限簡單實體。 

|類型|目的|範例|
|--|--|--|
|階層式|簡單實體的父子|Location.Origin=New York<br>Location.Destination=London|
|複合|父子實體：預先建置、清單、簡單、階層式| number=3<br>list=first class<br>prebuilt.datetimeV2=March 5|

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果您所需的實體超出實體數目上限 

您可能需要使用階層式和複合實體。 階層式實體會反映共用特性或具有某個分類成員身分之實體間的關係。 子實體全都是其父系分類的成員。 例如，名為 PlaneTicketClass 的階層式實體可能會有 EconomyClass 和 FirstClass 子實體。 階層所跨的深度只有一層。  

複合實體代表一個整體的多個部分。 例如，名為 PlaneTicketOrder 的複合實體可能會有 Airline、Destination、DepartureCity、DepartureDate 及 PlaneTicketClass 子實體。 您可以從既有的簡單實體、階層式實體的子系或預先建置的實體，建置複合實體。  

LUIS 也提供非機器學習但可讓您的 LUIS 應用程式指定固定值清單的清單實體類型。 請參閱 [LUIS 界限](luis-boundaries.md)參考，以檢閱「清單」實體類型的限制。 

如果您在考慮階層式、複合及清單實體之後，所需的實體仍然超出限制，請與支援人員連絡。 若要這樣做，請收集關於您系統的詳細資訊、前往 [LUIS](luis-reference-regions.md#luis-website) 網站，然後選取 [支援]。 如果您的 Azure 訂用帳戶包含支援服務，請與 [Azure 技術支援人員](https://azure.microsoft.com/support/options/)連絡。 

## <a name="next-steps"></a>後續步驟

了解良好[語句](luis-concept-utterance.md)的相關概念。 

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
