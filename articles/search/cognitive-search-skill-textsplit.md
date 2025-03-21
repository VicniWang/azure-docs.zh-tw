---
title: 文字分割認知搜尋技能 - Azure 搜尋服務
description: 您可以根據 Azure 搜尋服務擴充管線中的長度，將文字分成文字之區塊或頁面。
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
ms.openlocfilehash: d5a8fa61307c278a77b33868a711970850a06f3e
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410042"
---
#   <a name="text-split-cognitive-skill"></a>文字分割認知技能

**文字分割**技能將文字分成文字區塊。 您可以指定是否想要將文字分成句子或特定長度的頁面。 如果其他技能下游有最大文字長度需求，此技能特別有用。 

> [!NOTE]
> 這項技能不受限於認知服務 API，您不需支付其使用費用。 不過，您仍然應該[連結認知服務資源](cognitive-search-attach-cognitive-services.md)，以覆寫限制您每天少量擴充的**免費**資源選項。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 參數名稱     | 說明 |
|--------------------|-------------|
| textSplitMode      | 「頁面」或「句子」 | 
| maximumPageLength | 如果 textSplitMode 設為「頁面」，這是指由 `String.Length` 測量的最大頁面長度。 最小值為 100。  如果 textSplitMode 設定為 "pages"，演算法會嘗試將文字分割為大小和 "maximumPageLength" 差不多的區塊。 在這種情況下，演算法會盡量在例句邊界斷句，以讓區塊大小稍微小於 "maximumPageLength"。 | 
| defaultLanguageCode   | (選用) 以下其中一個語言代碼：`da, de, en, es, fi, fr, it, ko, pt`。 預設值是英文 (en)。 幾點考量事項：<ul><li>如果您傳遞的是 languagecode-countrycode 格式，則只會使用該格式的 languagecode 部分。</li><li>如果語言不在前面的清單中，分割技能會在字元界限拆分文字。</li><li>提供語言代碼有助於避免將無空格語言 (例如中文、日文和韓文) 的字組切成一半。</li></ul>  |


## <a name="skill-inputs"></a>技能輸入

| 參數名稱       | 說明      |
|----------------------|------------------|
| text  | 要分割成子字串的文字。 |
| languageCode  | (選用) 文件的語言代碼。  |

## <a name="skill-outputs"></a>技能輸出 

| 參數名稱     | 說明 |
|--------------------|-------------|
| textItems | 已擷取的子字串陣列。 |


##  <a name="sample-definition"></a>範例定義

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
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
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>錯誤案例
如果不支援某語言，則會產生警告，而且會在字元界限分割文字。

## <a name="see-also"></a>另請參閱

+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
