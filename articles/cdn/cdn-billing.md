---
title: 了解 Azure CDN 計費 | Microsoft Docs
description: 本常見問題集描述 Azure CDN 的計費方式。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: af8e57f39b5b83b1d1be09c29d8b6eb5d49c7b6c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309260"
---
# <a name="understanding-azure-cdn-billing"></a>了解 Azure CDN 計費

本常見問題集描述 Azure 內容傳遞網路 (CDN) 主控之內容的計費結構。

## <a name="what-is-a-billing-region"></a>什麼是計費區域？
計費區域是地理區域，用來判斷從 Azure CDN 傳遞物件時該收取多高的費率。 目前的計費區域與所屬地理區如下：

- 區域 1：北美洲、歐洲、中東與非洲

- 區域 2：亞太地區 (包含日本)

- 區域 3：南美洲

- 區域 4：澳大利亞與紐西蘭

- 區域 5：印度

如需存在點 (POP) 區域的詳細資訊，請參閱[依地區的 Azure CDN POP 位置](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)。 例如，位在墨西哥的 POP 落在北美洲區域，因此屬於區域 1 的涵蓋範圍。 

如需 Azure CDN 價格的詳細資訊，請參閱[內容傳遞網路的價格](https://azure.microsoft.com/pricing/details/cdn/)。

## <a name="how-are-delivery-charges-calculated-by-region"></a>如何依照區域計算傳遞費用？
Azure CDN 計費區域的判定，取決於將內容傳遞給終端使用者的來源伺服器所在位置。 用戶端的目的地 (實體位置) 不在計費區域的考量範圍內。

例如，假設位在墨西哥的使用者發出要求，然而因對等互連或流量等情況，該要求由位在美國 POP 的伺服器提供服務，則計費區域將會是美國。

## <a name="what-is-a-billable-azure-cdn-transaction"></a>什麼是可計費的 Azure CDN 交易？
所有在 CDN 終止的 HTTP(S) 要求都算是可計費事件，包括所有回應類型：成功、失敗或其他。 然而，不同的回應可能會產生不同的流量。 例如，「304 未修改」和其他只含標頭的回應只會產生少量流量，因為它們是小型的標頭回應。同樣地，錯誤回應 (如「404 找不到」) 雖然可計費，不過因為回應承載很小，只會產生少量費用。

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>使用 Azure CDN 會產生其他哪些相關聯的 Azure 成本？
使用 Azure CDN 時，用來當做物件來源的服務也會產生一些使用量費用。 這些成本通常只佔整體 CDN 使用量成本的一小部分。

如果您將 Azure Blob 儲存體當做內容來源，還會因快取填滿而產生以下儲存空間費用：

- 實際使用的 GB：您的來源物件之實際儲存空間。

- 傳輸 (以 GB 為單位)：傳輸以填入 CDN 快取的資料量。

- 交易：如填入快取所需。

如需 Azure 儲存體計費的詳細資訊，請參閱[了解 Azure 儲存體計費 - 頻寬、交易和容量](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)。

如果您使用*託管服務傳遞*，可能會產生以下費用：

- Azure 計算時間：當作來源的計算執行個體。

- Azure 計算傳輸：傳輸自計算執行個體以填入 Azure CDN 快取的資料。

如果您的用戶端使用位元組範圍要求 (不考慮來源服務)，請考慮以下事項：

- *位元組範圍要求*是發生在 CDN 的可計費交易。 當用戶端發出位元組範圍要求時，會要求物件的子集 (範圍)。 CDN 只會回應所要求內容的一部分。 這部分的回應是可計費交易，而傳輸量只限於範圍回應的大小 (加上標頭)。

- 當索取部分物件的要求 (透過指定位元組範圍標頭) 抵達時，CDN 會將整個物件擷取到快取。 因此，儘管來自 CDN 的可計費交易只有部分回應，不過來自來源的可計費交易卻可能涉及物件的完整大小。

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>會發生多少傳輸活動以支援快取？
每當 CDN POP 需要填滿快取時，會向來源發出要求，索取即將加入快取的物件。 因此，來源的每次快取遺漏都會產生可計費交易。 快取遺漏的次數取決於幾個因素：

- 可快取內容的型態：對於高 TTL (存留時間)/到期值，而且經常存取的內容，因為它在快取中的熱門程度高，所以大多數的負載都由 CDN 負責處理。 良好的快取命中率通常是指超過 90% 的命中率，代表不論是因為快取遺漏或物件重新整理，只有不到 10% 的用戶端要求必須傳回來源。

- 載入物件需要幾個節點：每當節點從來源載入物件，就會引發計費交易。 因此，比較通用的內容 (從比較多節點存取)，產生的可計費交易也會比較多。

- TTL 影響：物件的 TTL 越高，表示需要從來源擷取它的頻率越低。 這也代表用戶端 (如瀏覽器) 將物件留在快取中的時間較長，因此能減少 CDN 的交易數。

## <a name="how-do-i-manage-my-costs-most-effectively"></a>管理成本最有效的方法是什麼？
盡可能為內容設定最長的 TTL。 
