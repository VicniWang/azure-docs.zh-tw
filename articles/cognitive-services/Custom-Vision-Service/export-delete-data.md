---
title: 匯出或刪除您的資料 - 自訂視覺服務
titlesuffix: Azure Cognitive Services
description: 了解如何匯出或刪除自訂視覺服務中的資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: 01273ca241769c5e3bb7b7222355d32b29fd51b9
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308497"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>匯出或刪除自訂視覺中的使用者資料

自訂視覺會收集使用者資料來操作本服務，但是客戶可以完整控制資料的檢視、匯出和刪除，並使用自訂視覺服務[訓練 API](https://go.microsoft.com/fwlink/?linkid=865446)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

如需如何匯出和刪除自訂視覺中使用者資料的詳細資訊，請參閱下表。

| 資料 | 匯出作業 | 刪除作業 |
| ---- | ---------------- | ---------------- |
| 帳戶資訊 (訂用帳戶金鑰) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | 使用 Azure 入口網站刪除 (Azure 訂用帳戶)。 或者，使用 CustomVision.ai 設定頁面中的 [Delete Your Account] \(刪除帳戶\) 按鈕 (Microsoft 帳戶訂用帳戶) | 
| 反覆項目詳細資料 | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 反覆項目效能詳細資料 | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 反覆項目清單 | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 專案和專案詳細資料 | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 影像標籤 | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 映像 | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (提供下載影像的 URI) 和 [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (提供下載影像的 URI) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 匯出的模型 | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | 刪除帳戶時刪除 | 
