---
title: 在 Azure Stack 中套用更新 | Microsoft Docs
description: 了解如何為 Azure Stack 整合系統匯入及安裝 Microsoft 更新套件。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 0c3f52c78bbfd3094324b74f3b66610fcebfa2f4
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099287"
---
# <a name="apply-updates-in-azure-stack"></a>在 Azure Stack 中套用更新

*適用範圍：Azure Stack 整合式系統*

您可以使用系統管理員入口網站中的 [更新] 圖格來為 Azure Stack 套用 Microsoft 或 OEM 更新套件。

如果您使用的是整合系統版本 1807 或更早的版本，您必須下載更新套件、將套件檔案匯入至 Azure Stack，然後安裝該更新套件。 如需指示，請參閱[透過下載套件更新 Azure Stack](#update-azure-stack-by-downloading-the-package)

這些升級指示適用於 Azure Stack 整合系統。 如果您使用的是 Azure Stack 開發系統，則必須下載最新版本的安裝套件。 如需指示，請參閱[安裝 Azure Stack 開發套件](.\asdk\asdk-install.md)

## <a name="update-azure-stack"></a>更新 Azure Stack

### <a name="select-and-apply-an-update-package"></a>選取並套用更新套件

1. 開啟系統管理入口網站。

2. 選取 [儀表板]。 選取 [更新] 圖格。

    ![Azure Stack 有可用的更新](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. 記下 Azure Stack 的目前版本。 您可以更新至下一個完整的版本。 例如，如果您執行 Azure Stack 1811，則下一個發行版本是 1901。

    ![Azure Stack 更新套用](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. 在 [更新] 清單中選取下一個可用的版本。 如果要檢閱版本變更，可以選取版本資訊欄中的 [檢視] 以開啟版本的版本資訊主題。

5. 選取 [立即更新]。 將會開始更新。

### <a name="review-update-history"></a>檢閱更新歷程記錄

1. 開啟系統管理入口網站。

2. 選取 [儀表板]。 選取 [更新] 圖格。

3. 選取 [更新歷程記錄]。

![Azure Stack 更新歷程記錄](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>透過下載套件來更新 Azure Stack

如果您使用的是整合系統版本 1807 或更早的版本，您必須下載更新套件、將套件檔案匯入至 Azure Stack，然後安裝該更新套件。

## <a name="download-the-update-package"></a>下載更新套件

當有適用於 Azure Stack 的 Microsoft 或 OEM 更新套件可用時，請將該套件下載到可從 Azure Stack 存取的位置，然後檢閱套件內容。 更新套件通常是由下列檔案所組成：

- 自我解壓縮的 `<PackageName>.exe` 檔案。 此檔案包含更新的承載，例如 Windows Server 的最新累積更新。

- 對應 `<PackageName>.bin` 檔案。 這些檔案提供與 *PackageName*.exe 檔案相關的承載壓縮。

- `Metadata.xml` 檔案。 此檔案包含更新的相關基本資訊，例如發行者、名稱、先決條件、大小及支援路徑 URL。

> [!IMPORTANT]  
> 套用 Azure Stack 1901 更新套件之後，Azure Stack 更新套件的封裝格式將會從 .exe、.bin(s) 和 .xml 格式移至 .zip(s) 和 .xml 格式。 已連線戳記的 Azure Stack 操作員不受影響。 已中斷連線的 Azure Stack 操作員只會用使如下所述的相同程序匯入 .xml 和 .zip 檔。

## <a name="import-and-install-updates"></a>匯入及安裝更新

下列程序說明如何在系統管理員入口網站中匯入及安裝更新套件。

> [!IMPORTANT]  
> 強烈建議您向使用者通知任何維護工作，並且盡可能將一般的維護期間安排在非上班時間。 維護作業會影響使用者工作負載和入口網站作業。

1. 在系統管理員入口網站中，選取 [所有服務]。 然後，在 [資料 + 儲存體] 類別底下，選取 [儲存體帳戶]。 (或者，在篩選方塊中，開始輸入**儲存體帳戶**，然後選取它)。

    ![顯示要在入口網站中的哪裡尋找儲存體帳戶](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. 在篩選方塊中，輸入 **update**，然後選取 [updateadminaccount] 儲存體帳戶。

3. 在儲存體帳戶詳細資料的 [服務] 底下，選取 [Blob]。
 
    ![顯示如何移至儲存體帳戶的 Blob](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. 在 [Blob 服務] 底下，選取 [容器] 來建立容器。 輸入名稱 (例如 *Update-1811*)，然後選取 [確定]。
 
     ![顯示如何在儲存體帳戶中新增容器](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. 建立容器之後，按一下容器名稱，然後按一下 [上傳] 以將套件檔案上傳到容器中。
 
    ![顯示如何上傳套件檔案](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. 在 [上傳 Blob] 底下，按一下資料夾圖示，瀏覽至更新套件的 .exe 檔，然後按一下檔案總管視窗中的 [開啟]。
  
7. 在 [上傳 Blob] 底下，按一下 [上傳]。
  
    ![顯示要在哪裡上傳每個套件檔案](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. 針對 *PackageName*.bin 和 Metadata.xml 檔案重複步驟 6 和 7。 請勿匯入 Supplemental Notice.txt 檔案 (如果包含)。
9. 完成時，您可以檢閱通知 (入口網站右上角中的鈴鐺圖示)。 通知應該會指出已完成上傳。
10. 瀏覽回儀表板上的 [更新] 圖格。 此圖格應該會指出有可用的更新。 按一下此圖格即可檢視新加入的更新套件。
11. 若要安裝更新，請選取標示為 [就緒] 的套件，然後在該套件上按一下滑鼠右鍵並選取 [立即更新]，或按一下靠近頂端的 [立即更新] 動作。
12. 當您按一下安裝更新套件時，可以在 [更新執行詳細資料] 區域中檢視狀態。 您也可以從這裡按一下 [下載完整記錄] 來下載記錄檔。
13. 當更新完成時，[更新] 圖格會顯示已更新的 Azure Stack 版本。

您可以在將更新安裝到 Azure Stack 後，從儲存體帳戶手動刪除這些更新。 Azure Stack 會定期檢查是否有較舊的更新套件，並將它們從儲存體中移除。 Azure Stack 可能需要兩週的時間才能移除舊套件。

## <a name="next-steps"></a>後續步驟

- [在 Azure Stack 中管理更新概觀](azure-stack-updates.md)
- [Azure Stack 服務原則](azure-stack-servicing-policy.md)
