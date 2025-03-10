---
title: 將本機 Jupyter Notebook 移轉至 Azure Notebooks
description: 快速地從本機電腦或 Web URL，將 Jupyter Notebook 移轉至 Azure Notebooks，並共用 Notebook 以進行共同作業。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 3bd7f895e3cc30c4c5a0496977d65d68c220931f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084497"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>快速入門：遷移本機 Jupyter 筆記本

您在個人電腦本機建立的 Jupyter Notebook，只有您自己可以存取。 您可以透過各種方式共用您的檔案，但其後接收者將會有其本身的本機 Notebook 複本，而您將難以併入他們所做的任何變更。 您也可以將 Notebook 儲存在共用的線上存放庫 (例如 GitHub) 中，但若要這麼做，每個共同作業者仍需在本機安裝其本身的 Jupyter，且必須使用與您相同的組態。

藉由將您的本機或以存放庫為基礎的 Notebook 移轉至 Azure Notebooks，您即可將其儲存在能夠立即與共同作業者共用的雲端位置。 這些共同作業者只需瀏覽器即可檢視和執行您的 Notebook，且在[登入](quickstart-sign-in-azure-notebooks.md) Azure Notebooks 後也將能夠進行變更。

本快速入門示範從您的本機電腦或另一個可存取的檔案 URL 移轉 Notebook 的程序。 若要從 GitHub 存放庫移轉 Notebook，請參閱[快速入門：複製 Notebook](quickstart-clone-jupyter-notebook.md)。

## <a name="create-a-project-on-azure-notebooks"></a>在 Azure Notebooks 上建立專案

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入。 (如需詳細資訊，請參閱[快速入門 - 登入 Azure Notebooks](quickstart-sign-in-azure-notebooks.md))。

1. 從您的公用設定檔頁面中，選取頁面頂端的 [我的專案]：

    ![瀏覽器視窗頂端的 [我的專案] 連結](media/quickstarts/my-projects-link.png)

1. 在 [我的專案] 頁面上，選取 [+ 新增專案] (鍵盤快速鍵：n)；只有在瀏覽器視窗很窄時，該按鈕才會顯示為 **+**：

    ![[我的專案] 頁面上的 [新增專案] 命令](media/quickstarts/new-project-command.png)

1. 在顯示的 [建立新的專案] 快顯視窗中，在 [專案名稱] 和 [專案識別碼] 欄位中為您要移轉的 Notebook 輸入適當的值，並清除 [公用專案] 和 [建立 README.md] 的選項，然後選取 [建立]。

## <a name="upload-the-local-notebook"></a>上傳本機 Notebook

1. 在 [專案] 頁面上選取 [上傳] (如果您的瀏覽器視窗較小，可能會顯示為向上箭號)，然後選取 1。 在顯示的快顯視窗中，選取 [從電腦] (如果您的 Notebook 位於本機檔案系統) 或 [從 URL] (如果 Notebook 位於線上)：

    ![從 URL 或本機電腦上傳 Notebook 的命令](media/quickstarts/upload-from-computer-url-command.png)

   (同樣地，如果您的 Notebook 位於 GitHub 存放庫中，請改為遵循[快速入門：複製 Notebook](quickstart-clone-jupyter-notebook.md) 中的步驟。)

    - 如果您使用 [從電腦]，請將 *.ipynb* 檔案拖放到快顯視窗中，或選取 [選擇檔案]，然後瀏覽至您要匯入的檔案，並加以選取。 然後選取 [上傳]。 上傳的檔案會獲得與本機檔案相同的名稱。 (您不需要上傳 *.ipynb_checkpoints* 資料夾的任何內容。)

    ![從電腦上傳的快顯視窗](media/quickstarts/upload-from-computer-popup.png)

    - 如果您使用 [從 URL]，請在 [檔案 URL] 欄位中輸入來源位址，然後在 [檔案名稱] 欄位中，輸入要指派給專案中 Notebook 的檔案名稱。 然後選取 [上傳]。 如果您有多個具有不同 URL 的檔案，請使用 **+ 新增檔案**命令來檢查您輸入的第一個 URL，其後快顯視窗將會提供另一個檔案的新欄位。

    ![從 URL 上傳的快顯視窗](media/quickstarts/upload-from-url-popup.png)

1. 開啟並執行您剛上傳的 Notebook，以確認其內容和作業。 完成之後，請選取 [檔案] > [終止並關閉] 以關閉 Notebook。

1. 若要共用已上傳 Notebook 的連結，請以滑鼠右鍵按一下專案中的檔案，並選取 [複製連結] (鍵盤快速鍵：y)，然後將該連結貼到適當的訊息中。 或者，您可以使用專案頁面上的 [共用] 控制項共用整個專案。

1. 若要編輯 Notebook 以外的檔案，請以滑鼠右鍵按一下專案中的檔案，然後選取 [編輯檔案] (鍵盤快速鍵：i)。 預設動作 [執行] (鍵盤快速鍵：r) 只會顯示檔案內容，不允許編輯。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：建立和執行 Jupyter Notebook 來執行線性迴歸](tutorial-create-run-jupyter-notebook.md)
