---
title: Azure Event Grid 傳遞與重試
description: 說明 Azure Event Grid 如何傳遞事件，以及如何處理未傳遞的訊息。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: spelluru
ms.openlocfilehash: b69215a76b332db9b994827705d6bbc3b48af5c8
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465508"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 訊息傳遞與重試

本文章說明未確認傳遞時，Azure Event Grid 如何處理事件。

Event Grid 提供持久的傳遞。 它針對每個訂用帳戶傳遞每則訊息至少一次。 事件會立即傳送到每個訂用帳戶的已註冊端點。 如果端點未確認接收事件，事件方格會重新嘗試傳遞事件。

目前，事件方格會將每個事件個別傳送給訂閱者。 訂閱者會收到包含單一事件的陣列。

## <a name="retry-schedule-and-duration"></a>重試排程和持續時間

Event Grid 針對事件傳遞使用指數輪詢重試原則。 如果您的端點沒有回應或傳回失敗碼，事件方格會以下列排程重試傳遞：

1. 10 秒
2. 30 秒
3. 1 分鐘
4. 5 分鐘
5. 10 分鐘
6. 30 分鐘
7. 1 小時

事件方格會在所有重試步驟中加入些許隨機性。 一個小時之後，事件傳遞會每小時重試一次。

根據預設，事件格線會讓所有未在 24 小時內傳遞的事件變成過期事件。 您可以在建立事件訂用帳戶時[自訂重試原則](manage-event-delivery.md)。 您可以提供傳遞嘗試次數上限 (預設為 30 次) 和事件存留時間 (預設為 1440 分鐘)。

## <a name="dead-letter-events"></a>無效信件事件

當事件方格無法傳遞事件時，它可以將未傳遞事件傳送到儲存體帳戶。 這個程序稱為無效信件處理。 根據預設，事件方格不會開啟無效信件處理。 若要啟用它，您必須指定儲存體帳戶在建立事件訂用帳戶時保留未傳遞事件。 您可從這個儲存體帳戶提取事件，以解析傳遞項目。

事件方格在試過所有重試嘗試後，會將事件傳送至無效信件位置。 如果事件方格收到 400 (錯誤的要求) 或 413 (要求實體太大) 回應碼，則會立即將事件傳送至無效信件端點。 這些回應碼表示事件傳遞會永遠不會成功。

在系統最後一次嘗試傳遞事件，以及將它傳遞至無效信件位置之間，具有五分鐘的延遲。 此延遲的目的是為了減少 Blob 儲存體的作業次數。 如果無效信件位置無法使用的時間達到四個小時，系統便會捨棄該事件。

在設定無效信件位置之前，您必須先有具備容器的儲存體帳戶。 建立事件訂用帳戶時，您必須提供這個容器的端點。 端點的格式如下：`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

您可以選擇在事件已傳送至無效信件位置時接收通知。 若要使用事件方格來回應未傳遞事件，請為無效信件 Blob 儲存體[建立事件訂用帳戶](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 每當您的無效信件 Blob 儲存體收到未傳遞事件時，事件方格就會通知您的處理常式。 處理常式會以您希望採取的動作來回應，以便協調未傳遞事件。

如需無效信件位置的設定範例，請參閱[無效信件和重試原則](manage-event-delivery.md)。

## <a name="message-delivery-status"></a>訊息傳遞狀態

Event Grid 使用 HTTP 回應碼以確認接收事件。 

### <a name="success-codes"></a>成功碼

下列 HTTP 回應碼表示事件已成功傳遞到您的 Webhook。 Event Grid 會將傳遞視為完成。

- 200 確定
- 202 已接受

### <a name="failure-codes"></a>失敗碼

下列 HTTP 回應碼表示事件傳遞嘗試失敗。

- 400 不正確的要求
- 401 未經授權
- 404 找不到
- 408 要求逾時
- 413 要求實體太大
- 414 URI 太長
- 429 要求太多
- 500 內部伺服器錯誤
- 503 服務無法使用
- 504 閘道逾時

## <a name="next-steps"></a>後續步驟

* 若要檢視事件傳遞的狀態，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 若要自訂事件傳遞選項，請參閱[無效信件和重試原則](manage-event-delivery.md)。