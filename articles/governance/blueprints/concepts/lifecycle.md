---
title: 了解藍圖生命週期
description: 了解藍圖所會歷經的生命週期以及各階段的詳細資訊。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2dbf63e745af102de05ec6dc3e3bcb3e98cd3a32
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563706"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>了解 Azure 藍圖生命週期

如同 Azure 中的許多資源，Azure 藍圖中的藍圖也具有典型的自然生命週期。 它們會建立、部署，最後當它們不再需要或相關時即會刪除。
藍圖支援標準生命週期作業。 藍圖會接著以這些作業為基礎來提供其他層級的狀態，以對管理其基礎結構即程式碼 (DevOps 中的重要元素) 的組織，支援常見的持續整合和持續部署管線。

以下會討論藍圖的標準生命週期，以完整了解藍圖與各個階段：

> [!div class="checklist"]
> - 建立及編輯藍圖
> - 發佈藍圖
> - 建立及編輯藍圖的新版本
> - 發佈藍圖的新版本
> - 刪除藍圖的特定版本
> - 刪除藍圖

## <a name="creating-and-editing-a-blueprint"></a>建立及編輯藍圖

建立藍圖時，將成品新增至其中、儲存至管理群組或訂用帳戶，並提供唯一名稱與唯一版本。 藍圖現在處於**草稿**模式，尚無法指派。 然而在**草稿**模式中，它可以繼續更新及變更。

處於**草稿**模式且未曾發佈的藍圖會在 [藍圖定義] 頁面上，顯示與**已發佈**藍圖不同的圖示。 **最新版本**也會針對這些未曾發佈的藍圖顯示為**草稿**。

使用 [Azure 入口網站](../create-blueprint-portal.md#create-a-blueprint)或 [REST API](../create-blueprint-rest-api.md#create-a-blueprint) 來建立及編輯藍圖。

## <a name="publishing-a-blueprint"></a>發佈藍圖

對**草稿**模式的藍圖進行所有計劃性變更之後，該藍圖會處於**已發佈**狀態且可供指派。 藍圖的**已發佈**版本是無法改變的。
一旦處於**已發佈**狀態，系統就會以與**草稿**藍圖不同的圖示來顯示藍圖，並在 [最新版本] 欄中顯示所提供的版本號碼。

使用 [Azure 入口網站](../create-blueprint-portal.md#publish-a-blueprint)或 [REST API](../create-blueprint-rest-api.md#publish-a-blueprint) 來發佈藍圖。

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>建立及編輯藍圖的新版本

藍圖的**已發佈**版本是無法改變的。 不過，可以將藍圖的新版本新增至現有藍圖，並且視需要進行修改。 藉由編輯現有藍圖來進行變更。 儲存新的變更後，藍圖現在會有**未發佈的變更**。 這些變更是藍圖的新**草稿**版本。

使用 [Azure 入口網站](../create-blueprint-portal.md#edit-a-blueprint)來編輯藍圖。

## <a name="publishing-a-new-version-of-the-blueprint"></a>發佈藍圖的新版本

藍圖的每個編輯後版本必須**已發佈**，才能加以指派。 若已對藍圖進行**未發佈的變更**，但尚未**發佈**，編輯藍圖頁面上就會顯示 [發佈藍圖] 按鈕。 如果您看不到此按鈕，則表示藍圖**已發佈**且沒有**未發佈的變更**。

> [!NOTE]
> 單一藍圖可以有多個**已發佈**版本，每個都可指派給訂用帳戶。

若要發佈具有**未發佈的變更**的藍圖，請使用用於發佈新藍圖的相同步驟。

## <a name="deleting-a-specific-version-of-the-blueprint"></a>刪除藍圖的特定版本

藍圖的每個版本都是唯一物件，可個別**發佈**。 因此，可以刪除藍圖的每個版本。 刪除藍圖的版本不會對該藍圖的其他版本產生任何影響。

> [!NOTE]
> 您無法刪除擁有作用中指派的藍圖。 先刪除指派，然後再刪除您要移除的版本。

1. 在左窗格中按一下 [所有服務]，然後搜尋並選取 [原則]。 在 [原則] 頁面上，按一下 [藍圖]。

1. 從頁面左邊選取 [藍圖定義]，然後使用篩選選項來找出您要刪除版本的藍圖。 按一下該藍圖以開啟編輯頁面。

1. 按一下 [已發佈的版本] 索引標籤，然後找出您要刪除的版本。

1. 以滑鼠右鍵按一下要刪除的版本，然後選取 [刪除這個版本]。

## <a name="deleting-the-blueprint"></a>刪除藍圖

您也可以刪除核心藍圖。 刪除核心藍圖也會刪除該藍圖的所有藍圖版本，包括**草稿**和**已發佈**的藍圖。 刪除藍圖的版本時，刪除核心藍圖不會移除任何藍圖版本的現有指派。

> [!NOTE]
> 您無法刪除擁有作用中指派的藍圖。 先刪除指派，然後再刪除您要移除的版本。

使用 [Azure 入口網站](../create-blueprint-portal.md#delete-a-blueprint)或 [REST API](../create-blueprint-rest-api.md#delete-a-blueprint) 來刪除藍圖。

## <a name="assignments"></a>指派

在生命週期中，有數個時間點可將藍圖指派給訂用帳戶。 當藍圖版本的模式為**已發佈**時，即將將該版本指派給訂用帳戶。 此生命週期在新版本尚在開發時，就可使用並主動指派藍圖版本。

指派藍圖的版本時，請務必了解指派它們的位置，以及使用哪些參數來指派它們。 參數可以是靜態或動態的。 若要深入了解，請參閱[靜態與動態參數](parameters.md)。

### <a name="updating-assignments"></a>更新指派

指派藍圖時，可以更新指派。 更新現有指派的原因有數個，包括：

- 新增或移除[資源鎖定](resource-locking.md)
- 變更[動態參數](parameters.md#dynamic-parameters)的值
- 將指派升級至藍圖的較新**已發佈**版本

若要了解作法，請參閱[更新現有的指派](../how-to/update-existing-assignments.md)。

## <a name="next-steps"></a>後續步驟

- 了解如何使用[靜態與動態參數](parameters.md)
- 了解如何自訂[藍圖排序順序](sequencing-order.md) (英文)
- 了解如何使用[藍圖資源鎖定](resource-locking.md)
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題