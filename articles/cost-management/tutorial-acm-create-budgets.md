---
title: 教學課程 - 建立和管理 Azure 預算 | Microsoft Docs
description: 此教學課程可協助規劃和說明您取用之 Azure 服務的成本。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: b41d086c092f3b18715d8fb70cd1a487a97c6869
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814039"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>教學課程：建立和管理 Azure 預算

成本管理中的預算可協助您進行規劃並促進組織責任歸屬。 透過預算，您可以說明您在特定期間所取用或訂閱的 Azure 服務。 它們可協助您通知其他人其費用的相關資訊以主動管理成本，並監視費用在經過一段時間的進展方式。 當超過您所建立的預算閾值時，只會觸發通知。 您的資源都會不受到影響，而您的使用量並不會停止。 當您分析成本時，您可以使用預算來比較及追蹤費用。

每月預算將根據每四個小時的支出進行評估。 但是，已耗用的資源的資料和通知可在八小時內提供。  

當您在未來選取到期日時，預算會在期間 (每月、每季或每年) 結束時自動重設相同的預算金額。 因為它們會使用相同的預算金額重設，所以，在預算的貨幣金額與未來期間不同時您需要另外建立預算。

此教學課程的範例會引導您針對 Azure Enterprise 合約 (EA) 訂用帳戶建立和編輯預算。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中建立預算
> * 編輯預算

## <a name="prerequisites"></a>必要條件

預算功能支援各種不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。 若要檢視預算，您至少需要 Azure 帳戶的讀取存取。

 針對 Azure EA 訂用帳戶，您必須具備檢視預算的讀取存取權。 若要建立及管理預算，您必須具有參與者權限。 您可以個別為 EA 訂用帳戶和資源群組建立預算。 不過，您無法為 EA 帳單帳戶建立預算。

使用者和群組針對預算的每個訂用帳戶支援下列 Azure 權限：

- 擁有者：可以建立、修改或刪除訂用帳戶的預算。
- 參與者和成本管理參與者 – 可以建立、修改或刪除他們自己的預算。 可以修改其他人所建立之預算的預算金額。
- 讀者和成本管理讀者 – 可以檢視他們有權限的預算。

如需成本管理資料的指派權限詳細資訊，請參閱[指派成本管理資料的存取權](assign-access-acm-data.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

- 在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-budget-in-the-azure-portal"></a>在 Azure 入口網站中建立預算

您可以針對每月、每季或每年期間建立 Azure 訂用帳戶預算。 您在 Azure 入口網站中的瀏覽內容會決定您是否要針對訂用帳戶或資源群組建立預算。 例如，在 Azure 入口網站中，瀏覽至 [訂用帳戶] &gt; 選取訂用帳戶 &gt; [預算]。 在此範例中，您建立的預算適用於您所選取的訂用帳戶。 如果您想要建立資源群組的預算，請瀏覽至 [資源群組] > 選取資源群組 > [預算]。

建立預算之後，會針對它們顯示您目前費用的簡單檢視。

按一下 [新增] 。

![在 Azure 入口網站中顯示的成本管理預算](./media/tutorial-acm-create-budgets/budgets01.png)

在 [建立預算] 視窗中，輸入預算名稱和預算金額。 然後選擇每月、每季或每年持續期間。 接下來，選取結束日期。 預算需要至少一個成本閾值 (預算的百分比) 與對應的電子郵件地址。 您可以選擇性地在單一預算中最多包含五個閾值與五個電子郵件地址。 當達到預算閾值時，通常會在 8 小時內收到電子郵件通知。 如需有關通知的詳細資訊，請參閱[使用成本警示](cost-mgt-alerts-monitor-usage-spending.md)。

以下是建立每月美金 $4,500 的預算範例。 達到預算的 90% 時，就會產生電子郵件警示。

![[建立預算] 方塊中顯示的範例資訊](./media/tutorial-acm-create-budgets/monthly-budget01.png)

當您建立每季預算時，它的運作方式與與每月預算相同。 差異在於當季預算金額會平均分配於當季的三個月。 如您所預期的，每年預算金額會平均分配於該日曆年度的所有 12 個月。

每當成本管理接收到更新的計費資料時，即會對照預算來更新目前支出。 通常是每日進行。

![顯示目前預算支出的範例資訊](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

建立預算之後，即會在成本分析中顯示它。 當您開始[分析成本和費用](quick-acm-cost-analysis.md)時，首要步驟之一就是檢視與您費用趨勢相關的預算。

![成本分析中顯示的預算和支出範例](./media/tutorial-acm-create-budgets/cost-analysis.png)

在上述範例中，您已針對訂用帳戶建立預算。 不過，您也可以針對資源群組建立預算。 如果您想要針對資源群組建立預算，請瀏覽至[成本管理 + 計費] &gt;[訂用帳戶] &gt; 選取訂用帳戶 > [資源群組] > 選取資源群組 > [預算] > 然後**新增**預算。

## <a name="edit-a-budget"></a>編輯預算

根據您擁有的存取層級，您可以編輯預算來變更其屬性。 在下列範例中，部分屬性是唯讀的，因為使用者只具備訂用帳戶的參與者權限。 目前已停用 [到期日]，而且一旦設定之後就無法修改。

![編輯預算以變更各種屬性的範例](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中建立預算
> * 編輯預算

請前進到下一個教學課程，以建立成本管理資料的週期性匯出。

> [!div class="nextstepaction"]
> [建立和管理匯出的資料](tutorial-export-acm-data.md)
