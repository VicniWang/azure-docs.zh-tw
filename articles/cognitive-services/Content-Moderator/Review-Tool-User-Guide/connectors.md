---
title: 審核內容時連接至其他服務 - 內容仲裁
titlesuffix: Azure Cognitive Services
description: 了解如何使用連接器來存取 Content Moderator 工作流程的其他 API。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 5e56579a856f7b6259acddcbe34f2e5361505cb5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217606"
---
# <a name="connect-to-other-cognitive-services"></a>連接到其他認知服務

除了 Content Moderator API 外，Azure Content Moderator 工作流程還可以使用其他 API。 您可以使用 Content Moderator 中的連接器來存取其他 API。 連接器會提供通往其他 API 的連結。

Content Moderator 包含下列預設連接器：

* Emotion API
* 人臉識別 API
* PhotoDNA Cloud Service

![Content Moderator 的可用連接器](images/connectors-1.png)

## <a name="verify-your-credentials"></a>確認認證 

在定義工作流程之前，請確定您擁有所要使用連接器 API 的有效認證：

1.  在檢閱工具儀表板上，選取 [設定] > [連接器]。

  ![Content Moderator 選取連接器](images/connectors-2.png)

2.  針對要確認認證的連接器，選取其旁邊的 [編輯] 符號。

  ![Content Moderator 選取編輯符號](images/connectors-3.png)

3.  訂用帳戶金鑰隨即出現。 如果您進行任何編輯，請於完成時選取 [儲存]。

  ![Content Moderator 編輯連接器頁面](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>新增連接器

1.  新增連接器之前，需要有訂用帳戶金鑰。 在檢閱工具儀表板上，選取 [設定] > [認證]。 選取並複製 [Ocp-Admin-Subscription-Key] 方塊中的值。

2.  選取 [連接器]。 選取檢閱工具儀表板上所顯示的其中一個可用連接器。 然後，選取 [連線]。 

  ![Content Moderator 新增連接器頁面](images/connectors-5.png)

3.  在 [Ocp-Admin-Subscription-Key] 方塊中，貼上您複製的金鑰。 然後選取 [儲存]。

## <a name="next-steps"></a>後續步驟

* 了解如何使用連接器來[定義自訂工作流程](workflows.md)。
