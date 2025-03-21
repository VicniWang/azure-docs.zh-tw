---
title: 實體辨識認知搜尋技能 - Azure 搜尋服務
description: 從 Azure 搜尋服務認知搜尋管線中的文字，擷取不同類型的實體。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7a7fe9603716575c241ca78ebdc9b674888ca835
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452202"
---
#    <a name="entity-recognition-cognitive-skill"></a>實體辨識認知技能

**實體辨識**技能會從文字擷取實體。 這項技能會使用認知服務中[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)所提供的機器學習模型。

> [!NOTE]
> 從 2018 年 12 月 21 日開始，您可以[連結認知服務資源](cognitive-search-attach-cognitive-services.md)與 Azure 搜尋服務技能集。 據此，我們將開始收取執行技術集的費用。 自這個日期起，我們也會開始收取文件萃取階段中影像擷取的費用。 從文件中擷取文字的功能則繼續免費提供。
>
> [內建認知技能](cognitive-search-predefined-skills.md)執行會以[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services)收費，費率與直接執行工作時一樣。 影像擷取是 Azure 搜尋服務費用，目前是以預覽版定價提供。 如需詳細資訊，請參閱 [Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)或[計費方式](search-sku-tier.md#how-billing-works)。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>資料限制
記錄的大小上限應該是 50,000 個字元 (以 `String.Length` 為測量單位)。 如果您需要先分割資料，然後再將該資料傳送至關鍵片語擷取器，請考慮使用 [文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

參數區分大小寫，且均為選擇性。

| 參數名稱     | 說明 |
|--------------------|-------------|
| 類別    | 應擷取的類別陣列。  可能的類別類型：`"Person"`、`"Location"`、`"Organization"`、`"Quantity"`、`"Datetime"`、`"URL"`、`"Email"`。 如果未提供任何類別，則會傳回所有類型。|
|defaultLanguageCode |  輸入文字的語言代碼。 支援下列語言：`de, en, es, fr, it`|
|minimumPrecision | 未使用。 保留供未來使用。 |
|includeTypelessEntities | 設定為 True 時，如果文字包含已知的實體，但無法分類為其中一個受支援的類別，則會作為 `"entities"` 複雜輸出欄位的一部分傳回。 預設值為 `false` |


## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | 說明                   |
|---------------|-------------------------------|
| languageCode  | 選用。 預設值為 `"en"`。  |
| text          | 要分析的文字。          |

## <a name="skill-outputs"></a>技能輸出

**附註**：並非所有的實體類別都支援所有語言。
只有 _en_、_es_ 支援 `"Quantity"`、`"Datetime"`、`"URL"`、`"Email"` 類型的擷取。

| 輸出名稱     | 說明                   |
|---------------|-------------------------------|
| 人員      | 字串陣列，其中每個字串代表人員名稱。 |
| 位置  | 字串陣列，其中每個字串代表位置。 |
| 組織  | 字串陣列，其中每個字串代表組織。 |
| 數量  | 字串陣列，其中每個字串代表數量。 |
| 日期時間  | 字串陣列，其中每個字串代表日期時間 (如其在文字中所示) 值。 |
| URL | 字串陣列，其中每個字串代表 URL |
| 電子郵件 | 字串陣列，其中每個字串代表電子郵件 |
| namedEntities | 複雜類型的陣列，包含下列欄位： <ul><li>category</li> <li>值 (實際實體名稱)</li><li>位移 (在文字中找到的位置)</li><li>信賴 (目前未使用。 將會設定為 -1 的值)</li></ul> |
| 實體 | 複雜類型陣列，其中包含從文字中擷取的實體相關豐富資訊，包含下列欄位 <ul><li> 名稱 (實際實體名稱。 這代表「標準化」格式)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (實體的維基百科頁面連結)</li><li>bingId</li><li>類型 (已辨識實體的類別)</li><li>子類型 (僅適用於特定類別，可提供實體類型的漸進式檢視)</li><li> 符合 (包含的複雜集合)<ul><li>文字 (實體的原始文字)</li><li>位移 (找到的位置)</li><li>長度 (原始實體文字的長度)</li></ul></li></ul> |

##  <a name="sample-definition"></a>範例定義

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>範例輸入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>範例輸出

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>錯誤案例
如果文件的語言程式碼不受支援，則會傳回錯誤，且不會擷取任何實體。

## <a name="see-also"></a>另請參閱

+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)