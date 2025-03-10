---
title: 如何選擇 Bing 語音辨識模式 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 如何在 Bing 語音中選擇最佳辨識模式。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: ed270ed19959240bc1b90ba6171792cf4369e273
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221585"
---
# <a name="bing-speech-recognition-modes"></a>Bing 語音辨識模式

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing 語音轉換文字 API 支援多種語音辨識模式。 請為您的應用程式選擇會產生最佳辨識結果的模式。

| Mode | 說明 |
|---|---|
| *互動* | 適用於互動使用者應用程式案例的「命令和控制」辨識。 使用者說出簡短片語以作為應用程式的命令。 |
| 聽寫 | 適用於聽寫案例的連續辨識。 使用者說出顯示為文字的較長句子。 使用者使用較正式的說話風格。 |
| 交談 | 用於將人類交談記錄成文字的連續辨識。 使用者使用較不正式的說話風格，並且可能交替使用較長句子和較短片語。

> [!NOTE]
> 當您使用 [REST API](../GetStarted/GetStartedREST.md) 時，才適用這些模式。 [用戶端程式庫](../GetStarted/GetStartedClientLibraries.md)會使用不同的參數來指定辨識模式。 如需詳細資訊，請參閱您所選擇的用戶端程式庫。

如需詳細資訊，請參閱[辨識模式](../concepts.md#recognition-modes)頁面。
