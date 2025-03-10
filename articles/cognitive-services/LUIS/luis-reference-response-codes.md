---
title: API HTTP 回應碼
titleSuffix: Azure
description: 了解哪些 HTTP 回應碼會從 LUIS 撰寫和 API 端點傳回
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: e55d0e1566edf8f9d969a16d0ae558b9831920e7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867693"
---
# <a name="common-api-response-codes-and-their-meaning"></a>常見的 API 回應碼及其意義

[撰寫](https://aka.ms/luis-authoring-apis)和[端點](https://aka.ms/luis-endpoint-apis) API 會傳回 HTTP 回應碼。 當回應訊息包含專屬於要求的資訊時，HTTP 回應狀態碼為一般。 

## <a name="common-status-codes"></a>常見狀態碼
下表針對[撰寫](https://aka.ms/luis-authoring-apis)和[端點](https://aka.ms/luis-endpoint-apis) API 列出一些最常見的 HTTP 回應狀態碼：

|代碼|API|說明|
|:--|--|--|
|400|撰寫、端點|要求的參數不正確，表示必要參數遺失、格式不正確或太大|
|400|撰寫、端點|要求的本文不正確，表示 JSON 遺失、格式不正確或太大|
|401|編寫|使用端點訂用帳戶金鑰，而不是撰寫金鑰|
|401|撰寫、端點|無效、格式不正確或空的金鑰|
|401|撰寫、端點| 金鑰不符合區域|
|401|編寫|您不是擁有者或共同作業者|
|401|編寫|無效的 API 呼叫順序|
|403|撰寫、端點|超過每月的金鑰總配額限制|
|409|端點|應用程式仍在載入中|
|410|端點|應用程式需要重新訓練並重新發行|
|414|端點|查詢超過最大字元限制|
|429|撰寫、端點|超出速率限制 (要求/秒)|

## <a name="next-steps"></a>後續步驟

* REST API [撰寫](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)和[端點](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)文件
