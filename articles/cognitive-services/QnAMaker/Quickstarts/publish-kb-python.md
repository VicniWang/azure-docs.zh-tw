---
title: 發佈知識庫，REST (Python)
titleSuffix: QnA Maker - Azure Cognitive Services
description: 這個以 Python REST 為基礎的快速入門會逐步引導您發佈知識庫，以將經過測試的最新版知識庫推送至代表已發佈知識庫的專用 Azure 搜尋服務索引。 它也會建立可在您的應用程式或聊天機器人中呼叫的端點。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: f4491a343d9d84f714c1ac21a46d495ebd4d40b9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868050"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>快速入門：使用 Python 在 QnA Maker 中發佈知識庫

本快速入門以 REST 為基礎，會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用 Azure 搜尋服務索引，並建立端點以供應用程式或聊天機器人呼叫。

本快速入門會呼叫 QnA Maker API：
* [發佈](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - 此 API 不需要在要求主體中有任何資訊。

## <a name="prerequisites"></a>必要條件

* [Python 3.7](https://www.python.org/downloads/)
* 您必須有 QnA Maker 服務。 若要擷取您的金鑰，請在儀表板中選取 [資源管理] 下方的 [金鑰]。
* QnA Maker 知識庫 (KB) 識別碼可以在 kbid 查詢字串參數中的 URL 找到，如下所示。

    ![QnA Maker 知識庫識別碼](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果您還沒有知識庫，可以建立要用於本快速入門的範例知識庫：[建立新的知識庫](create-new-kb-nodejs.md)。

> [!NOTE] 
> 從 [**Azure-Samples/cognitive-services-qnamaker-python** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base)可取得完整的解決方案檔。

## <a name="create-a-knowledge-base-python-file"></a>建立知識庫 Python 檔案

建立名為 `publish-kb-3x.py` 的檔案。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `publish-kb-3x.py` 的頂端，新增下列幾行以將必要的相依性新增至專案：

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>新增必要的常數

在上述必要的相依性後面，新增必要的常數以存取 QnA Maker。 使用您自己的值加以取代。

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>新增 POST 要求來發佈知識庫

在必要常數之後，新增下列程式碼來對 QnA Maker API 提出 HTTPS 要求，以便發佈知識庫並接收回應：

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

發佈成功時，API 呼叫會傳回 204 狀態，且回應主體中沒有任何內容。 程式碼會針對 204 回應新增內容。

對於任何其他回應，該回應則會原封不動地傳回。

## <a name="build-and-run-the-program"></a>建置並執行程式

在命令列中輸入下列命令，以執行程式。 它會傳送要求給 QnA Maker API 來發佈知識庫，然後印出代表成功的 204 或錯誤。

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>後續步驟

在發佈知識庫之後，您需要有[用來產生答案的端點 URL](../Tutorials/create-publish-answer.md#generating-an-answer)。 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

[QnA Maker 概觀](../Overview/overview.md)
