---
title: Azure 中的 Load Balancer 閒置 TCP 重設
titlesuffix: Azure Load Balancer
description: 具有閒置逾時雙向 TCP RST 封包的 Load Balancer
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/29/2019
ms.author: kumud
ms.openlocfilehash: 18e4a7ae5010730054dd110828c63e8418b93f39
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296914"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>具有閒置 TCP 重設的負載平衡器 (公開預覽)

您可以使用 [Standard Load Balancer](load-balancer-standard-overview.md)，藉由對某個指定規則啟用「閒置時重設 TCP」，來為您的案例建立更容易預測的應用程式行為。 Load Balancer 的預設行為是在達到流程的閒置逾時時，以無訊息模式卸除流程。  啟用此功能會讓 Load Balancer 在閒置逾時的時候，傳送雙向的 TCP 重設 (TCP RST 封包)。  這會讓您的應用程式端點知道，連線已逾時且無法再供使用。  如有需要，端點可以立即建立新的連線。

![負載平衡器 TCP 重設](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>具有閒置逾時 TCP 重設的 Load Balancer 功能目前為公開預覽版，而且只有有限的一組[區域](#regions)時才提供。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
您變更此預設行為，以及對輸入 NAT 規則、負載平衡規則和[輸出規則](https://aka.ms/lboutboundrules)啟用傳送閒置逾時 TCP 重設。  根據規則啟用時，Load Balancer 會在達到所有相符流程的閒置逾時時，將雙向 TCP 重設 (TCP RST 封包) 傳送至用戶端和伺服器端點。

收到 TCP RST 封包的端點會立即關閉對應的通訊端。 這會立即通知端點有關已經發生連線的版本，而且相同 TCP 連線上的任何未來通訊都會失敗。  如果通訊端視需要關閉並重新建立連線，而不需要等到 TCP 連線最後逾時，則應用程式可以清除連線。

在許多情況下，這可能不需要傳送 TCP (或應用程式層) 保持運作，即可重新整理流程的閒置逾時。 

如果閒置持續時間超過設定所允許的值，或您的應用程式顯示已啟用 TCP 重設的非預期行為，您可能仍然需要使用 TCP Keepalive (或應用程式層 Keepalive) 來監視 TCP 連線的作用性。  此外，Keepalive 仍然適用於在路徑的某處 Proxy 處理連線時，特別是應用程式層 Keepalive。  

請仔細檢查整個完整情節，決定您是否受益於啟用 TCP 重設、調整閒置逾時，以及可能需要額外的步驟，以確保所需的應用程式行為。

## <a name="enabling-tcp-reset-on-idle-timeout"></a>啟用閒置逾時的 TCP 重設

使用 API 版本 2018-07-01，您可以啟用傳送每個規則的閒置逾時雙向 TCP 重設：

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a> 區域可用性

所有區域都有提供。

## <a name="limitations"></a>限制

- 入口網站無法用來設定或檢視 TCP 重設。  請改為使用範本、REST API、Az CLI 2.0 或 PowerShell。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Standard Load Balancer](load-balancer-standard-overview.md)。
- 深入了解[輸出規則](load-balancer-outbound-rules-overview.md)。
