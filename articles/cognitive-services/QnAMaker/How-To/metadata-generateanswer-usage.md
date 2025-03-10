---
title: 中繼資料與 GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓您將索引鍵/值組形式的中繼資料新增至問答集。 這項資訊可用來篩選使用者查詢的結果，並儲存待處理的交談中可用的其他資訊。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim88
ms.openlocfilehash: 1294b714c217178d53ed69cc886cd89f23620274
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859482"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>搭配使用中繼資料與 GenerateAnswer API

QnA Maker 可讓您將索引鍵和值組形式的中繼資料新增至問答集。 這項資訊可用來篩選使用者查詢的結果，並儲存待處理的交談中可用的其他資訊。 如需詳細資訊，請參閱[知識庫](../Concepts/knowledge-base.md)。

## <a name="qna-entity"></a>QnA 實體

了解 QnA Maker 如何儲存問題/解答資料，是第一要務。 下圖說明某個 QnA 實體：

![QnA 實體](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每個 QnA 實體都有唯一且持續性的識別碼。 此識別碼可以用來對特定 QnA 實體進行更新。

## <a name="generateanswer-api"></a>GenerateAnswer API

您可以在聊天機器人或應用程式中使用 GenerateAnswer API 來查詢使用者問題的知識庫，而從問答集中取得最佳相符項目。

### <a name="generateanswer-endpoint"></a>GenerateAnswer 端點

在您從 [QnA Maker 入口網站](https://www.qnamaker.ai)或使用 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) 發佈知識庫之後，您可以取得 GenerateAnswer 端點的詳細資料。

若要取得端點詳細資料：
1. 登入 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
2. 在 [我的知識庫] 中，對您的知識庫按一下 [檢視程式碼]。
![我的知識庫](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. 取得 GenerateAnswer 端點詳細資料。

![端點詳細資料](../media/qnamaker-how-to-metadata-usage/view-code.png)

您也可以從知識庫的 [設定] 索引標籤取得端點詳細資料。

### <a name="generateanswer-request"></a>GenerateAnswer 要求

您可以透過 HTTP POST 要求來呼叫 GenerateAnswer。 如需示範如何呼叫 GenerateAnswer 的範例程式碼，請參閱[快速入門](../quickstarts/csharp.md)。

- **要求 URL**： https://{QnA Maker endpoint}/knowledgebases/{knowledge base ID}/generateAnswer

- **要求參數**： 
    - **新增知識庫識別碼** (字串)：測試您知識庫的 GUID。
    - **QnAMaker 端點** (字串)：您的 Azure 訂用帳戶中，部署端點的主機名稱。
- **要求標頭**
    - **Content-Type** (字串)：傳送至 API 的本文媒體類型。
    - **授權** (字串)：您的端點金鑰 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。
- **要求本文**
    - **問題** (字串)：要對您的知識庫查詢的使用者問題。
    - **頂端** (選擇性，整數)：要包含在輸出中的排名結果數目。 預設值為 1。
    - **使用者識別碼** (選擇性，字串)：用來識別使用者的唯一識別碼。 此識別碼會記錄在交談記錄中。
    - **strictFilters** (選擇性，字串)：如果指定，將會指示 QnA Maker 僅傳回含有指定中繼資料的解答。 如需詳細資訊，請參閱下列內容。
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>GenerateAnswer 回應

- **回應 200** - 成功的呼叫會傳回問題的結果。 此回應包含下列欄位：
    - **解答** - 使用者查詢的解答清單，依排名分數的遞減順序排序。
        - **分數**：0 和 100 之間的排名分數。
        - **問題**：使用者所提供的問題。
        - **回答**：問題的答案。
        - **來源**：從中擷取解答或將其儲存在知識庫中的來源名稱。
        - **中繼資料**：與解答相關聯的中繼資料。
            - 名稱：中繼資料名稱。 (字串，最大長度：100，必要)
            - 值：中繼資料值。 (字串，最大長度：100，必要)
        - **識別碼**：指派給解答的唯一識別碼。
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
                "metadata": [
                    {
                        "name": "category",
                        "value": "api"
                    }
                ]
            }
        ]
    }
    ```

## <a name="metadata-example"></a>中繼資料範例

請考慮下面的常見問題集資料。 請按一下中繼資料圖示，將中繼資料新增至至您的知識庫。

![新增中繼資料](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用中繼資料標記的 strictFilters 篩選結果

請考量使用者針對 "Paradise" 餐廳而提出的問題 「飯店何時會打烊」。

由於只需要 "Paradise" 餐廳的結果，因此您可以在 GenerateAnswer 呼叫中設定「餐廳名稱」中繼資料的篩選條件，如下所示。

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>保存內容
GenerateAnswer 的回應會包含相符問答集的對應中繼資料資訊，如下所示。

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

這項資訊可用來記錄先前的交談內容，以用於後續的交談。 

## <a name="next-steps"></a>後續步驟

發佈頁面也會提供資訊，搭配 [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) 和 [cURL](../Quickstarts/get-answer-from-kb-using-curl.md) 來產生答案。 

> [!div class="nextstepaction"]
> [建立知識庫](./create-knowledge-base.md)
