---
title: 語音服務訓練的轉譯指導方針
titleSuffix: Azure Cognitive Services
description: 了解如何準備文字，針對語音服務自訂原音和語言模型以及語音字型。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 31813cbbe4bdb647d43e99e7585d1eb3bb6e8a5c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857170"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>使用語音服務的轉譯指導方針

若要自訂 [語音轉換文字] 或 [文字轉換語音]，您必須提供文字以及語音。 每一行文字都會對應至單一語句。 文字應該盡可能完全符合語音。 此文字稱為「文字記錄」，您必須以特定格式建立它。

語音服務會將輸入正規化，以保持文字一致。 

本文說明兩種類型的正規化。 各種語言的指導方針稍有不同。

## <a name="us-english-en-us"></a>美式英文 (en-US)

文字資料應該只使用 ASCII 字元集以純文字撰寫，每行一個語句。

請避免使用擴充 (Latin-1) 或 Unicode 標點符號字元。 在文字處理程式中準備資料，或是從網頁抓取資料時，可能會不小心包含這些字元。 請以適當的 ASCII 替代字元取代這些字元。 例如︰

| 要避免的字元 | 替代字元 |
|----- | ----- |
| "Hello world" (開頭和結尾雙引號) | "Hello world" (雙引號) |
| John’s day (右單引號) | John's day (單引號) |
| it was good—no, it was great! (長破折號) | it was good--no, it was great! (連字號) |

### <a name="text-normalization-rules-for-english"></a>適用於英文的文字正規化規則

語音服務會執行下列正規化規則：

* 對於所有文字使用小寫字母
* 移除文字內部單引號以外的所有標點符號
* 將數字擴充成口語形式，包括美元金額

這裡有一些範例：

| 原始文字 | 正規化之後 |
|----- | ----- |
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

將下列正規化套用至您的文字記錄：

* 縮寫應該以單字寫出。
* 非標準數值字串 (例如一些日期或會計表單) 應該以字組寫出。
* 含非字母字元或混合英數字字元的字組應該轉譯如發音。
* 保留發音為字組不變的縮寫 (例如，"radar"、"laser"、"RAM" 或 "NATO")。
* 寫出發音如同個別字母的縮寫，並以空格分隔字母 (例如，"IBM"、"CPU"、"FBI"、"TBD" 或 "NaN")。 

這裡有一些範例：

| 原始文字 | 正規化之後 |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr.Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>中文 (zh-cn)

上傳到「自訂語音服務」的文字資料應使用 UTF-8 編碼搭配位元組順序標記。 檔案應該以每行一個語句撰寫。

請避免使用半形標點符號字元。 在文字處理程式中準備資料，或是從網頁抓取資料時，可能會不小心包含這些字元。 請以適當的全形替代字元取代這些字元。 例如︰

| 要避免的字元 | 替代字元 |
|----- | ----- |
| "你好" (開頭和結尾雙引號) | "你好" (雙引號) |
| 需要什么帮助? (問號) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>適用於中文的文字正規化規則

語音服務會執行下列正規化規則：

* 移除所有標點符號
* 將數字擴充成口語形式
* 將全形字母轉換成半形字母
* 對於所有的英文字組使用大寫字母

這裡有一些範例：

| 原始文字 | 正規化之後 |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

在匯入您的文字之前，將下列正規化套用至您的文字：

* 縮寫應該以單字寫出 (如同口語形式)。
* 將數值字串以口語形式寫出。

這裡有一些範例：

| 原始文字 | 正規化之後 |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>其他語言

上傳到**語音轉換文字**服務的文字資料必須使用 UTF-8 編碼搭配位元組順序標記。 檔案應該以每行一個語句撰寫。

> [!NOTE]
> 下列範例使用德文。 不過，這些指導方針適用於美式英文或中文以外的所有語言。

### <a name="text-normalization-rules-for-german"></a>適用於德文的文字正規化規則

語音服務會執行下列正規化規則：

* 對於所有文字使用小寫字母
* 移除所有標點符號，包括各種類型的引號 (可接受 "ttest"、'test'、"test„ 和 «test»)
* 捨棄包含任何特殊字元 (¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬) 的資料列
* 將數字擴充成字組形式，包括美元或歐元金額
* 只接受 a、o 和 u 的變母音；其他則會以 "th" 取代或捨棄

這裡有一些範例：

| 原始文字 | 正規化之後 |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

在匯入您的文字之前，將下列正規化套用至您的文字：

* 小數點應該是 "," 而非 "."。
* 小時與分鐘之間的時間分隔符號應該是 ":" 而非 "." (例如，12:00 Uhr)。
* "ca." 之類的縮寫 不會被取代。 我們建議您使用完整形式。
* 已移除四個主要的數學運算子 (+、-、\* 和 /)。 建議您以其文字形式加以取代："plus"、"minus"、"mal" 和 "geteilt"。
* 相同的規則適用於比較運算子 (=、<、和 >)。 建議並取代成 "gleich"、"kleiner als" 和 "grösser als"。
* 使用 3/4 這類分數時，採用文字形式 (例如，"drei viertel" 而不採用 ¾)。
* 以文字形式 "Euro" 取代 € 符號。

這裡有一些範例：

| 原始文字 | 在使用者的正規化之後 | 在系統的正規化之後
|--------  | ----- | -------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>後續步驟

- [取得您的語音服務試用訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
- [以 C# 辨識語音](quickstart-csharp-dotnet-windows.md)
