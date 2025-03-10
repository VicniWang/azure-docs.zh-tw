---
title: 私人 SKU 和方案 | Microsoft Docs
description: 如何使用私人 SKU 來管理供應項目可用性。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 519f0354f2a19e106ca1072170721b27357d173e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849202"
---
<a name="private-skus-and-plans"></a>私人 SKU 和方案
============

私人 SKU 讓您可以限制 SKU 僅供特定客戶使用。 當 SKU 標記為私人時，代表該 SKU 不在包含 [Azure Marketplace](https://azuremarketplace.microsoft.com) 及 [Azure 入口網站](http://portal.azure.com)在內的任何公開目錄上開放使用。 在 Azure 入口網站上，只有擁有 SKU 存取權的客戶可以看見它。 此外，系統會提示這些客戶，他們擁有私人供應項目的存取權。

>[!NOTE]
>私人 SKU 必須有新的唯一 SKU/方案識別碼，來避免與您的公用 SKU 發生任何衝突。

您可以使用私人 SKU 來處理以下案例：

1.  發佈您只想開放給特定客戶使用，而不想公開的軟體。
2.  以提供給特定客戶的自訂價格，發佈公開軟體的變化。
3.  發佈含有自訂描述及條款的公開軟體變化 (透過新的供應項目)。

如果您只想要變更價格，您可以重複使用同一個供應項目中其他 SKU 的磁碟。 使用私人 SKU，您就不必重新提交各個 SKU 的磁碟。

<a name="mark-a-sku-private"></a>將 SKU 標記為私人
---------------------

若要將 SKU 標記為私人，請切換詢問 SKU 是否為私人的選項：

![將 SKU 標記為私人](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

您可以重複使用其他 SKU 的磁碟，並修改定價或描述。 若要重複使用磁碟，請在回覆「這個 SKU 要重複使用公開 SKU 中的映像嗎」提示時選取 [是]。

如果將 SKU 標記為私人，且供應項目有其他可重複使用磁碟的 SKU，您必須指出 SKU 會使用其他 SKU 中的磁碟。 您也必須指定私人 SKU 的目標對象。

>[!NOTE]
>發佈之後，公開 SKU 便不能再設為私人。

<a name="select-an-image"></a>選取映像
------------------

您可以為私人 SKU 提供新的磁碟，或是重複使用已在其他 SKU 中提供的相同磁碟，而只修改定價或描述。 若要重複使用磁碟，請在回覆「這個 SKU 要重複使用公開 SKU 中的映像嗎」提示時選取 [是]。

![指出映像重複使用](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

在您確認 SKU 會重複使用公開 SKU 中的映像後，即表明屬於映像來源的 SKU。

下一個擷取畫面的提示顯示如何識別私人 SKU 會重複使用所選 SKU 的映像：

![選取映像](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

當您發佈供應項目時，所選 SKU 的映像會在私人 SKU 識別碼下以自訂費率/條款開放使用。 私人 SKU 只有目標對象可以看見。

如需映像更新，您只需要更新基礎的 SKU 映像。 在幕後，私人 SKU 的映像也會自動更新。 同樣地，如果您從基礎 SKU 刪除了映像，這個映像也會從私人 SKU 中移除。

<a name="restricting-the-audience"></a>限制對象
------------------------

只有目標使用者能夠找到和部署私人供應項目。
我們目前支援使用訂用帳戶識別碼來設定目標使用者。

如果輸入這些訂用帳戶的方式是手動輸入表單，**最多可以輸入 10 個訂用帳戶**，若透過上傳 CSV 檔案，則允許**最多 20,000 個訂用帳戶**。

有限對象的手動輸入：

![手動限制對象](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

有限對象的 CSV 上傳：

![使用 CSV 來限制對象](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

範例 CSV 檔案內容：

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

當您從手動輸入切換至 CSV 上傳檢視，或是從 CSV 切換至手動輸入時，系統不會保留具有 SKU 存取權的訂用帳戶識別碼舊清單。 系統會顯示警告，而清單只會在儲存供應項目時覆寫。

<a name="sync-private-subscriptions"></a>同步處理私人訂用帳戶
-------------------------

將訂用帳戶新增至具有私人 SKU 或方案的已發佈供應項目時，您不必重新發佈供應項目即可新增對象資訊。 只要使用 Azure 訂用帳戶識別碼 (方案和 SKU) 或租用戶識別碼 (僅限方案) 即可新增對象。

<a name="previewing-private-offers"></a>預覽私人供應項目
-------------------------

在預覽/預備步驟期間，只有供應項目層級的預覽訂用帳戶能夠存取 SKU。 這是測試階段，此時您可以驗證供應項目向目標客戶呈現的方式，而且對所有發佈類型都是標準的。

存取預備供應項目的供應項目層級預覽訂用帳戶：

![預覽訂用帳戶識別碼](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

在供應項目上線後，只有有限的目標訂用帳戶 (透過手動輸入或 CSV 輸入) 能夠檢視和部署私人 SKU。 為了進行驗證，建議您**一律將您自己的訂用帳戶包含在私人 SKU 的有限目標中**。

>[!NOTE]
>為了進行偵錯，Microsoft 支援和工程小組也會有這些私人供應項目的存取權。
