---
title: 發佈知識庫
titleSuffix: QnA Maker - Azure Cognitive Services
description: 發佈您的知識庫是讓知識庫成為問題回答端點的最後一個步驟。 您發佈知識庫時，知識庫的 QnA 內容會從測試索引移到 Azure 搜尋服務中的生產索引。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 585e5f8f955a405b08bef42588ab5caff71c7bba
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865908"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-portal"></a>使用 QnA Maker 入口網站發佈知識庫

發佈知識庫是讓知識庫成為用戶端應用程式問題回答端點的最後一個步驟。 

發佈知識庫時，知識庫的問題與答案內容會從測試索引移到 Azure 搜尋服務中的生產索引。

![發佈生產測試索引](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

## <a name="publish-a-knowledge-base"></a>發佈知識庫

1. 變更知識庫之後，請選取上方導覽列中的 [發佈]。 您可以發佈知識庫直到達到 Azure 搜尋服務的分配數目為止。 

    ![發佈知識庫](../media/qnamaker-how-to-publish-kb/publish.png)

2. 再次選取 [發佈]，查看應用程式或聊天機器人中可用的端點詳細資料。

    ![成功發佈知識庫](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>清除資源

當知識庫完成時，則會從 QnA Maker 入口網站中移除。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得知識庫的分析](./get-analytics-knowledge-base.md)
