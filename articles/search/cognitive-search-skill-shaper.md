---
title: 增強認知搜尋技能 - Azure 搜尋服務
description: 從非結構化資料中擷取中繼資料和結構化資訊，並在 Azure 搜尋服務擴充管線中塑形為複雜類型。
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
ms.openlocfilehash: a1f5a698ee76ebd0561bd19ff1a23d0f04be0771
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410110"
---
#   <a name="shaper-cognitive-skill"></a>塑形器認知技能

**塑形器**技能會建立複雜類型，以支援複合欄位 (也稱為多部分欄位)。 複雜類型欄位有多個部分，但在 Azure 搜尋服務索引中會被視為單一項目。 在搜尋案例中有用的合併欄位範例，包括將名字和姓氏合併成單一欄位、將縣/市和區/鄉/鎮/市合併成單一欄位，或將姓名和出生日期合併成單一欄位來建立唯一的身分識別。

**塑形器**技能可讓您實質上建立結構、定義該結構成員的名稱，並將值指派給每個成員。

根據預設，此技術會支援單一層級深度的物件。 對於更複雜的物件，則可以鏈結數個**塑形器**步驟。

在回應中，輸出名稱一律為 "output"。 就內部而言，管線可對應不同的名稱，例如以下範例中的 "analyzedText" 對應為 "output"，但**塑形器**技能本身會在回應中傳回 "output"。 如果您正在對擴充文件進行偵錯，並發現命名差異，或如果您建置自訂技能並自行建構回應，這可能十分重要。

> [!NOTE]
> 這項技能不受限於認知服務 API，您不需支付其使用費用。 不過，您仍然應該[連結認知服務資源](cognitive-search-attach-cognitive-services.md)，以覆寫限制您每天少量擴充的**免費**資源選項。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>範例 1：複雜類型

假設您要建立一個稱為 *analyzedText* 的結構，此結構有兩個成員，分別為：*text* 和 *sentiment*。 在 Azure 搜尋服務中，多部分的可搜尋欄位稱為*複雜類型*，目前預設尚未支援。 在此預覽中，**塑形器**技能可用來在索引中產生複雜類型的欄位。 

下列範例提供成員名稱做為輸入。 輸出結構 (您在 Azure 搜尋服務中的複雜欄位) 是透過 *targetName* 指定的。 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>範例輸入
提供此**塑形器**技能有用輸入的 JSON 文件可以是：

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="sample-output"></a>範例輸出
**塑形器**技能會產生一個稱為 *analyzedText* 的新元素。此新元素結合了 *text* 和 *sentiment* 的元素。 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="sample-2-input-consolidation"></a>範例 2：輸入合併

在另一個範例中，假設在不同的管線處理階段，您已經擷取書名和書本不同頁面上的章節標題。 您現在可以建立由這些各種輸入所組成的單一結構。

此案例的塑形器技能定義看起來像下列範例：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>範例輸出
在此案例中，塑形器會將所有章節標題壓平合併，以建立單一陣列。 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>另請參閱

+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)

