---
title: 所有預先建置的實體
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中所包含預先建置的實體清單。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/12/2019
ms.author: diberry
ms.openlocfilehash: 880c825ab8923dbdf17277a51378b6f4b624e091
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179891"
---
# <a name="entities-per-culture-in-your-luis-model"></a>在您 LUIS 模型中，每個文化特性的實體

Language Understanding (LUIS) 提供預先建置的實體。 當您的應用程式中包含預先建置的實體時，LUIS 就會在端點回應中包含對應的實體預測。 所有範例語句也都會標示該實體。 預先建置實體的行為**無法**修改。 除非另有註明，否則預先建置的實體在所有 LUIS 應用程式地區設定 (文化特性) 中均適用。 下表列出支援各種文化特性的預先建置實體。

|文化特性|次文化特性|
|--|--|
|中文|[zh-CN](#chinese-entity-support)|
|荷蘭文|[nl-NL](#dutch-entity-support)|
|English|[en-US (美式)](#english-american-entity-support)|
|法文|[fr-CA (加拿大)](#french-canadian-entity-support), [fr-FR (法國)](#french-france-entity-support), |
|德文|[de-DE](#german-entity-support)|
|義大利文|[it-IT](#italian-entity-support)|
|日文|[ja-JP](#japanese-entity-support)|
|韓文|[ko-KR](#korean-entity-support)|
|葡萄牙文|[pt-BR (巴西)](#portuguese-brazil-entity-support)|
|西班牙文|[es-ES (西班牙)](#spanish-spain-entity-support), [es-MX (墨西哥)](#spanish-mexico-entity-support)|
|土耳其文|[turkish](#turkish-entity-support)|

## <a name="chinese-entity-support"></a>中文實體支援

支援下列實體：

|預建實體|```zh-CN``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    ✔   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>荷蘭文實體支援

支援下列實體：

|預建實體|```nl-NL``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    -   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>英文 (美式) 實體支援

支援下列實體：

|預建實體|```en-US``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    ✔   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>法文 (法國) 實體支援

支援下列實體：

|預建實體|```fr-FR``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    ✔   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>法文 (加拿大) 實體支援

支援下列實體：

|預建實體|```fr-CA``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    ✔   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>德文實體支援

支援下列實體：

|預建實體|```de-DE``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    ✔   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>義大利文實體支援

支援下列實體：

|預建實體|```it-IT``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    -   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>日文實體支援

支援下列實體：

|預建實體|```ja-JP``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    -   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>韓文實體支援

支援下列實體：

|預建實體|```ko-KR``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    -   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    -   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    -   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    -   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>葡萄牙文 (巴西) 實體支援

支援下列實體：

|預建實體|```pt-BR``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    ✔   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>西班牙文 (西班牙) 實體支援

支援下列實體：

|預建實體|```es-ES``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    ✔   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    ✔   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    ✔   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>西班牙文 (墨西哥) 實體支援

支援下列實體：

|預建實體|```es-MX``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    -   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    -   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    -   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    -   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

請參閱[已被取代的預先建置實體](luis-reference-prebuilt-deprecated.md)的注意事項

KeyPhrase 不適用於葡萄牙文 (巴西) 的所有子文化特性 - ```pt-BR```。

## <a name="turkish-entity-support"></a>土耳其文實體支援

支援下列實體：

|預建實體|```tr-tr``` |
------|:------:|
[年齡](luis-reference-prebuilt-age.md)：<br>年<br>月<br>week<br>day   |    -   |
[貨幣 (錢幣)](luis-reference-prebuilt-currency.md)：<br>貨幣單位<br>小數單位 (例如：貨幣單位)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md)：<br>日期<br>daterange<br>分析<br>時間範圍   |    -   | 
[維度](luis-reference-prebuilt-dimension.md)：<br>磁碟區<br>區域<br>重量<br>資訊 (例如：位元/位元組)<br>長度 (例如：公尺)<br>速度 (例如：英哩/小時)  |    -   | 
[電子郵件](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[序數](luis-reference-prebuilt-ordinal.md)   |    -   |  
[百分比](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[溫度](luis-reference-prebuilt-temperature.md)：<br>華氏<br>克式<br>蘭氏<br>德氏<br>攝氏   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

請參閱[已被取代的預先建置實體](luis-reference-prebuilt-deprecated.md)的注意事項

KeyPhrase 無法使用。


## <a name="contribute-to-prebuilt-entity-cultures"></a>參與預先建置的實體文化特性
預先建置的實體是在 Recognizers-Text 開放原始碼專案中開發的。 [參與](https://github.com/Microsoft/Recognizers-Text)此專案。 此專案包含每個文化特性的貨幣適用的範例。 

GeographyV2 和 PersonName 不包含在 Recognizers-Text 專案中。 對於這些預先建置實體的問題，請開啟[支援要求](../../azure-supportability/how-to-create-azure-support-request.md)。 

## <a name="next-steps"></a>後續步驟

了解 [number](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md) 和 [currency](luis-reference-prebuilt-currency.md) 實體相關資訊。 
