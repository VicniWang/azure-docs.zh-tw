---
title: 快速入門：相應放大 Azure SQL 資料倉儲中的計算 - Azure 入口網站 | Microsoft Docs
description: 在 Azure 入口網站中調整 Azure SQL 資料倉儲中的計算。 相應放大計算以提升效能，或將計算調整回來以節省成本。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 2e3bb00cee679671ed382ee46690fc4dff8bddfb
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453880"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中調整 Azure SQL 資料倉儲中的計算

在 Azure 入口網站中調整 Azure SQL 資料倉儲中的計算。 [相應放大計算](sql-data-warehouse-manage-compute-overview.md)以提升效能，或將計算調整回來以節省成本。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="before-you-begin"></a>開始之前

您可以調整您已擁有的資料倉儲，也可以使用[快速入門：建立與連線 - 入口網站](create-data-warehouse-portal.md)來建立名為 **mySampleDataWarehouse** 的資料倉儲。  本快速入門會調整 **mySampleDataWarehouse**。

>[!Note]
>您的資料倉儲必須連線才能調整規模。 

## <a name="scale-compute"></a>調整計算

您可以藉由增加或減少資料倉儲單位來調整 SQL 資料倉儲計算資源。 [建立與連線 - 入口網站] 快速入門 (create-data-warehouse-portal.md) 已建立 **mySampleDataWarehouse**，並使用 400 DWU 加以初始化。 下列步驟會調整 **mySampleDataWarehouse** 的 DWU。

若要變更資料倉儲單位：

1. 在 Azure 入口網站的左側頁面中，按一下 [SQL 資料倉儲]。
2. 從 [SQL 資料倉儲] 頁面中，選取 [mySampleDataWarehouse]。 資料倉儲隨即開啟。
3. 按一下 [調整] 。

    ![按一下 [調整]](media/quickstart-scale-compute-portal/click-scale.png)

2. 在 [調整] 面板中，將滑桿向左或右移動來變更 DWU 設定。

    ![移動滑桿](media/quickstart-scale-compute-portal/scale-dwu.png)

3. 按一下 [檔案] 。 確認訊息隨即出現。 按一下 [是] 以確認或 [否] 以取消。

    ![按一下 [Save] \(儲存)。](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>後續步驟
您現已了解如何調整資料倉儲的計算。 若要深入了解 Azure SQL 資料倉儲，請繼續進行載入資料的教學課程。

> [!div class="nextstepaction"]
>[將資料載入 SQL 資料倉儲](load-data-from-azure-blob-storage-using-polybase.md)
