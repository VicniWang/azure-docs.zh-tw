---
title: Azure Data Factory 對應資料流程排序轉換
description: Azure Data Factory 對應資料流程排序聯結轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 55cd303399d34eecd8f787e1e5af09c5d904fb44
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271032"
---
# <a name="azure-data-factory-data-sort-transformations"></a>Azure Data Factory 對應資料排序轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![排序設定](media/data-flow/sort.png "排序")

「排序」轉換可讓您排序目前資料流的內送資料列。 「排序」轉換的輸出資料列後續會遵循您設定的排序規則。 您可以選擇個別的資料行，並使用每個欄位旁邊的箭號指標按照 ASC 或 DEC 加以排序。 套用排序之前，如果您需要修改資料行，請按一下「計算資料行」啟動運算式編輯器。 此時能夠建立排序作業的運算式，而不只是直接對於排序套用資料行。

如果您想要在字串或文字欄位排序時忽略大小寫，您可以開啟「不區分大小寫」。

「只在資料分割內排序」會使用 Spark 資料分割。 藉由只將每個分割區內的內送資料排序，資料流程可以將資料分割的資料排序，而不會將整個資料流排序。

排序轉換的每個排序條件都可以重新排列。 因此如果您需要在排序優先順序中調整資料行的優先順序，請使用滑鼠按住該資料列，並且在排序清單中向上或向下移動。

排序的資料分割效果

對於資料分散於多個節點和資料分割的巨量資料 Spark 叢集，可執行 ADF 資料流程。 在架構您的資料流時，如果您需要排序轉換以相同的順序保留資料，請務必記住這一點。 如果您選擇在後續的轉換中重新分割您的資料，您可能會由於重新輪換資料而失去資料排序。
