---
title: 如何使用適用於 .NET 的 Azure 媒體服務 SDK 建立媒體處理器 | Microsoft Docs
description: 了解如何建立媒體處理器元件，為 Azure 媒體服務的媒體內容進行編碼、格式轉換、加密或解密。 程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 0a8cb1178ec70d4e50f2a45834f9592c4708c5af
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998632"
---
# <a name="how-to-get-a-media-processor-instance"></a>作法：取得媒體處理器執行個體
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>概觀
在媒體服務中，媒體處理器是可處理特定處理工作的元件，例如編碼、格式轉換、加密或解密媒體內容。 您通常會在建立媒體內容的編碼、加密或格式轉換工作時建立媒體處理器。

## <a name="azure-media-processors"></a>Azure 媒體處理器 

下列主題提供媒體處理器的清單：

* [編碼媒體處理器](scenarios-and-availability.md#encoding-media-processors)
* [分析媒體處理器](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>取得媒體處理器

下列方法將說明如何取得媒體處理器執行個體。 此程式碼範例假設會使用名為 **_context** 的模組層級變數來參考伺服器內容，如[做法：以程式設計方式連接到媒體服務](media-services-use-aad-auth-to-access-ams-api.md)一節所述。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>後續步驟
既然您已了解如何取得媒體處理器執行個體，請移至 [如何為資產編碼](media-services-dotnet-encode-with-media-encoder-standard.md) 主題，以了解如何使用媒體編碼器標準將資產編碼。

