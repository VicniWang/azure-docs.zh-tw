---
title: 如何建立專案？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 如何在自訂翻譯工具中建立專案？
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 9886ba39846d9489a66c57bcc614f90e2bf70d4e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218099"
---
# <a name="create-a-project"></a>建立專案

專案是模型、文件和測試的容器。 每個專案都會自動包含上傳到該工作區且具有正確語言組的所有文件。 

建立專案是建置模型的首要步驟。 

## <a name="create-a-project"></a>建立專案：

1.  在[自訂翻譯工具](https://portal.customtranslator.azure.ai)入口網站中，按一下 [建立專案]。

    ![建立專案](media/how-to/how-to-create-project.png)

2.  在對話方塊中，輸入下列專案相關詳細資料：

    a.  專案名稱 (必要)：為您的專案提供唯一且有意義的名稱。 您不需要在標題中提到語言。

    b.  Description:專案的簡短摘要。 此描述並不會影響自訂翻譯工具或所產生自訂系統的行為，但可協助您區分不同專案。

    c.  語言組 (必要)：選取要翻譯的原始語言和目標語言。

    d.  類別 (必要)：選取最適合您專案的類別。 類別可描述您所要翻譯的文件術語和風格。

    e.  類別描述：使用此欄位來更明確地描述您要翻譯的特定領域或產業。 比方說，如果您的類別是醫學，您可能會新增手術或小兒科等方面的特定文件。 此描述並不會影響自訂翻譯工具或所產生自訂系統的行為。

    f.  專案標籤：[專案標籤](workspace-and-project.md#project-labels)可區分具有相同語言組和類別的專案。 最佳做法是「只」在您打算為相同語言組和相同類別建置多個專案，並且想以不同 CategoryID 來存取這些專案時，再使用標籤。 如果您只要為一個類別建置系統，請勿使用此欄位。 區別語言組不需使用專案標籤，而且也沒幫助。 您可以對多個專案使用相同標籤。

    ![建立專案對話方塊](media/how-to/how-to-create-project-dialog.png)

3.  按一下 [建立專案]。

## <a name="view-project-details"></a>檢視專案詳細資料

自訂翻譯工具登陸頁面會顯示您工作區中的前 10 個專案。 其中會顯示專案名稱、語言組、類別、狀態和 BLEU 分數。

選取專案之後，您會在專案頁面上看到下列項目：

- CategoryID：CategoryID 由 WorkspaceID、專案標籤和類別代碼所組成。 您會使用 CategoryID 和文字翻譯工具 API 來取得自訂翻譯。

- 訓練按鈕：使用此按鈕可啟動[訓練模型](how-to-train-model.md)。

- 新增文件按鈕：使用此按鈕可[上傳文件](how-to-upload-document.md)。

- 篩選文件按鈕：使用此按鈕可篩選及搜尋特定文件。

    ![檢視專案詳細資料](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>後續步驟

- 了解[如何搜尋、編輯、刪除專案](how-to-search-edit-delete-projects.md)。
- 了解[如何上傳文件](how-to-upload-document.md)以建立翻譯模型。
