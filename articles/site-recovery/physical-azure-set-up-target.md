---
title: 設定目標環境以從內部部署實體伺服器災害復原至 Azure | Microsoft Docs
description: 本文說明如何設定目標 Azure 環境，以便使用 Azure Site Recovery 進行實體伺服器的災害復原。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 43276aad26bc06400c1bc4b5feaace0d5646c213
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849224"
---
# <a name="prepare-target-vmware-to-azure"></a>準備目標 (VMware 至 Azure)

本文將說明如何準備您的 Azure 環境，以便開始將執行 Windows 或 Linux 的實體伺服器 (x64) 複寫至 Azure。

## <a name="prerequisites"></a>必要條件

本文假設：
- 您已建立復原服務保存庫，用以保護實體伺服器。 您可以從 [Azure 入口網站] (http://portal.azure.com "Azure 入口網站")建立復原服務保存庫。
- 您已[設定內部部署環境](physical-azure-disaster-recovery.md)以將實體伺服器複寫至 Azure。

## <a name="prepare-target"></a>準備目標

完成**步驟 1：選取保護目標**和**步驟 2︰準備來源**之後，即會進入**步驟 3︰目標**

![準備目標](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **訂用帳戶︰** 從下拉式功能表中選取您想要的訂用帳戶，以將實體伺服器複寫至該訂用帳戶。
2. **部署模型︰** 選取部署模型 (傳統或資源管理員)

根據所選的部署模型，系統會執行驗證以確保您的目標訂用帳戶中有至少一個相容的儲存體帳戶和虛擬網路，以便將您的實體伺服器複寫和容錯移轉至其中。

驗證成功完成後，按一下 [確定] 以移至下一個步驟。

如果您沒有相容的資源管理員儲存體帳戶或虛擬網路，您可以按一下頁面頂端的 [+ 儲存體帳戶] 或 [+ 網路] 按鈕來建立一個。

## <a name="next-steps"></a>後續步驟
[設定複寫設定](vmware-azure-set-up-replication.md)。
