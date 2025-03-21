---
title: 在 Node 中開始使用 Azure 轉送混合式連線 Websocket | Microsoft Docs
description: 為 Azure 轉送混合式連線 Websocket 撰寫 Node.js 主控台應用程式
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 9bdf3d319735d8d4ca85235dfb949d440bba9a02
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615070"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>在 Node.js 中開始使用轉送混合式連線 WebSocket

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

在本快速入門中，您將建立 Node.js 傳送者與接收者應用程式，以使用 Azure 轉送中的混合式連線 WebSocket 來傳送和接收訊息。 若要對 Azure 轉送有整體上的了解，請參閱 [Azure 轉送](relay-what-is-it.md)。 

在本快速入門中，您會執行下列步驟： 

1. 使用 Azure 入口網站建立轉送命名空間。
2. 使用 Azure 入口網站，在該命名空間中建立混合式連線。
3. 撰寫伺服器 (接聽端) 主控台應用程式來接收訊息。
4. 撰寫用戶端 (傳送端) 主控台應用程式來傳送訊息。
5. 執行應用程式。 

## <a name="prerequisites"></a>必要條件

- [Node.js](https://nodejs.org/en/).
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-a-namespace"></a>建立命名空間
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Create a hybrid connection
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>建立伺服器應用程式 (接聽程式)
為了接聽並接收來自轉送的訊息，請撰寫 Node.js 主控台應用程式。

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>建立用戶端應用程式 (傳送者)
為了傳送訊息到轉送，請撰寫 Node.js 主控台應用程式。

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>執行應用程式

1. 執行伺服器應用程式：從 Node.js 命令提示字元中，鍵入 `node listener.js`。
2. 執行用戶端應用程式：從 Node.js 命令提示字元中，鍵入 `node sender.js`，然後輸入一些文字。
3. 確定伺服器應用程式主控台有將用戶端應用程式中所輸入的文字輸出。

    ![執行應用程式](./media/relay-hybrid-connections-node-get-started/running-applications.png)

恭喜您，您已經使用 Node.js 建立端對端混合式連線應用程式！

## <a name="next-steps"></a>後續步驟
在本快速入門中，您已建立 Node.js 用戶端和使用 WebSocket 來傳送和接收訊息的伺服器應用程式。 Azure 轉送的混合式連線功能也支援使用 HTTP 來傳送和接收訊息。 若要了解如何搭配使用 HTTP 和 Azure 轉送混合式連線，請參閱 [Node.js HTTP 快速入門](relay-hybrid-connections-http-requests-node-get-started.md)。

在本快速入門中，您已使用 Node.js 來建立用戶端和伺服器應用程式。 若要了解如何使用 .NET Framework 撰寫用戶端和伺服器應用程式，請參閱 [.NET WebSocket 快速入門](relay-hybrid-connections-dotnet-get-started.md)或 [.NET HTTP 快速入門](relay-hybrid-connections-http-requests-dotnet-get-started.md)。


