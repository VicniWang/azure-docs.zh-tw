---
title: 新增語言分析器 - Azure 搜尋服務
description: Azure 搜尋服務中適用於非英文查詢和索引的多語言語彙文字分析。
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 20a8d9f5b575fca5471916af0183257f2a43d5cb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328283"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>將語言分析器新增至 Azure 搜尋服務索引

「語言分析器」是特定類型的[文字分析器](search-analyzers.md)，能使用目標語言的語言規則來執行語彙分析。 每個可搜尋的欄位都會有 **analyzer** 屬性。 如果您的索引包含已翻譯的字串 (例如適用於英文和中文文字的個別欄位)，您可以在每個欄位上指定語言分析器，以存取由那些分析器所提供的豐富語言功能。  

Azure 搜尋服務支援由 Lucene 所支援的 35 種分析器，以及由專屬的 Microsoft 自然語言處理技術所支援的 50 種分析器 (此技術同樣用於 Office 和 Bing 中)。

## <a name="comparing-analyzers"></a>比較分析器

一些開發人員可能偏好使用更熟悉、簡單且開放原始碼的 Lucene 解決方案。 Lucene 語言分析器速度較快，但 Microsoft 分析器具備進階功能，例如詞形歸併還原、複合字詞拆分 (適用於德文、丹麥文、荷蘭文、瑞典文、挪威文、愛沙尼亞文、芬蘭文、匈牙利文、斯洛伐克文等語言) 和實體辨識 (URL、電子郵件、日期、數字)。 如果可以，您應該進行 Microsoft 和 Lucene 分析器的比較，以決定哪一個比較適合。 

使用 Microsoft 分析器編製索引，平均速度約比其 Lucene 對等項目慢兩到三倍，視語言而定。 平均大小的查詢應不至大幅影響搜尋效能。 

### <a name="english-analyzers"></a>英文分析器

預設分析器是標準 Lucene，其可用於英文，但效果較不如 Lucene 的英文分析器或 Microsoft 的英文分析器。 
 
+ Lucene 的英文分析器能擴充標準分析器。 它會從字詞中移除所有格 (結尾的 's)、為每個 Porter 詞幹演算法套用詞幹，然後移除英文停用字詞。  

+ Microsoft 的英文分析器會執行詞形的歸併還原，而不是詞幹分析。 這表示它可以把變形和不規則的字詞形式處理得更好，以得到相關性更強的搜尋結果 

 > [!Tip]
 > [搜尋分析器示範](https://alice.unearth.ai/) \(英文\) 會顯示由標準 Lucene 分析器、Lucene 的英文語言分析器，以及 Microsoft 的英文版自然語言處理器所產生之結果的並排比較。 針對您所提供的每個搜尋輸入，每個分析器的結果都會顯示在相鄰的窗格中。

## <a name="configuring-analyzers"></a>設定分析器

語言分析器是以現況使用。 針對索引定義中的每一個欄位，您可以將 **analyzer** 屬性設為能指定語言和語言堆疊 (Microsoft 或 Lucene) 的分析器名稱。 搜尋與編製該欄位索引時會套用相同的分析器。 例如，您可以有個別適用於英文、法文及西班牙文旅館說明的欄位，這些欄位會在相同的索引中並列存在。 或者，與其使用 **analyzer**，您可以使用 **indexAnalyzer** 和 **searchAnalyzer** 來在編製索引和查詢時間時使用不同的分析規則。 

使用 **searchFields** 查詢參數來指定針對查詢所要搜尋的語言特定欄位。 您可以在[搜尋文件](https://docs.microsoft.com/rest/api/searchservice/search-documents) \(英文\) 中檢閱包含分析器屬性的查詢範例。 

如需索引屬性的詳細資訊，請參閱[建立索引 &#40;Azure 搜尋服務 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\)。 如需 Azure 搜尋服務中分析的詳細資訊，請參閱 [Azure 搜尋服務中的分析器](https://docs.microsoft.com/azure/search/search-analyzers)。

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>語言分析器清單 
 以下是支援的語言清單與 Lucene 和 Microsoft 的分析器名稱。  

|語言|Microsoft 分析器名稱|Lucene 分析器名稱|  
|--------------|-----------------------------|--------------------------|  
|阿拉伯文|ar.microsoft|ar.lucene|  
|亞美尼亞文||hy.lucene|  
|孟加拉文|bn.microsoft||  
|巴斯克文||eu.lucene|  
|保加利亞文|bg.microsoft|bg.lucene|  
|卡達隆尼亞文|ca.microsoft|ca.lucene|  
|簡體中文|zh-Hans.microsoft|zh-Hans.lucene|  
|繁體中文|zh-Hant.microsoft|zh-Hant.lucene|  
|克羅埃西亞文|hr.microsoft||  
|捷克文|cs.microsoft|cs.lucene|  
|丹麥文|da.microsoft|da.lucene|  
|荷蘭文|nl.microsoft|nl.lucene|  
|English|en.microsoft|en.lucene|  
|愛沙尼亞文|et.microsoft||  
|芬蘭文|fi.microsoft|fi.lucene|  
|法文|fr.microsoft|fr.lucene|  
|加利西亞文||gl.lucene|  
|德文|de.microsoft|de.lucene|  
|希臘文|el.microsoft|el.lucene|  
|古吉拉特文|gu.microsoft||  
|希伯來文|he.microsoft||  
|北印度文|hi.microsoft|hi.lucene|  
|匈牙利文|hu.microsoft|hu.lucene|  
|冰島文|is.microsoft||  
|印尼文 (Bahasa)|id.microsoft|id.lucene|  
|愛爾蘭文||ga.lucene|  
|義大利文|it.microsoft|it.lucene|  
|日文|ja.microsoft|ja.lucene|  
|坎那達文|ka.microsoft||  
|韓文|ko.microsoft|ko.lucene|  
|拉脫維亞文|lv.microsoft|lv.lucene|  
|立陶宛文|lt.microsoft||  
|馬來亞拉姆文|ml.microsoft||  
|馬來文 (拉丁)|ms.microsoft||  
|馬拉地文|mr.microsoft||  
|挪威文|nb.microsoft|no.lucene|  
|波斯文||fa.lucene|  
|波蘭文|pl.microsoft|pl.lucene|  
|葡萄牙文 (巴西)|pt-Br.microsoft|pt-Br.lucene|  
|葡萄牙文 (葡萄牙)|pt-Pt.microsoft|pt-Pt.lucene|  
|旁遮普文|pa.microsoft||  
|羅馬尼亞文|ro.microsoft|ro.lucene|  
|俄文|ru.microsoft|ru.lucene|  
|塞爾維亞文 (斯拉夫)|sr-cyrillic.microsoft||  
|塞爾維亞文 (拉丁)|sr-latin.microsoft||  
|斯洛伐克文|sk.microsoft||  
|斯洛維尼亞文|sl.microsoft||  
|西班牙文|es.microsoft|es.lucene|  
|瑞典文|sv.microsoft|sv.lucene|  
|坦米爾文|ta.microsoft||  
|泰盧固文|te.microsoft||  
|泰文|th.microsoft|th.lucene|  
|土耳其文|tr.microsoft|tr.lucene|  
|烏克蘭文|uk.microsoft||  
|烏都文|ur.microsoft||  
|越南文|vi.microsoft||  

 所有名稱加上 **Lucene** 註解的分析器都是由 [Apache Lucene 的語言分析器](https://lucene.apache.org/core/4_9_0/core/overview-summary.html ) \(英文\) 所提供。

## <a name="see-also"></a>另請參閱  
 [建立索引 &#40;Azure 搜尋服務 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\)  
 [AnalyzerName 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername) \(英文\)  
 [影片：Azure 搜尋服務 MVA 簡報的課程模組 7](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07) \(英文\)。  

