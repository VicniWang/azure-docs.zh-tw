---
title: 在 Azure 上建立和共用 Jupyter Notebook
description: 在 Azure Notebooks 上快速建立和執行 Jupyter Notebook，並與他人共用。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 96eb6a823299521a071edc87b5bce95409405ffb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53096268"
---
# <a name="quickstart-create-and-share-a-notebook"></a>快速入門：建立及共用筆記本

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入。 (如需詳細資訊，請參閱[快速入門 - 登入 Azure Notebooks](quickstart-sign-in-azure-notebooks.md))。

1. 從您的公用設定檔頁面中，選取頁面頂端的 [我的專案]：

    ![瀏覽器視窗頂端的 [我的專案] 連結](media/quickstarts/my-projects-link.png)

1. 在 [我的專案] 頁面上，選取 [+ 新增專案] (鍵盤快速鍵：n)；只有在瀏覽器視窗很窄時，該按鈕才會顯示為 **+**：

    ![[我的專案] 頁面上的 [新增專案] 命令](media/quickstarts/new-project-command.png)

1. 在顯示的 [建立新專案] 快顯視窗中，輸入或設定下列詳細資料，然後選取 [建立]：

    - **專案名稱**：Python 中的 Hello World
    - **專案識別碼**：hello-world-python
    - **公用專案**：(已清除)
    - **建立 README.md**：(已清除)

    ![已填入詳細資料的新增專案快顯視窗](media/quickstarts/new-project-popup.png)

1. 幾分鐘後，Azure Notebooks 會帶您瀏覽至新的專案。 選取 [+ 新增] 下拉式清單 (可能只顯示為 **+**)，然後選取 [Notebook]，將 Notebook 新增至專案：

    [![](media/quickstarts/empty-project-new-notebook-button.png "新的空專案和新增 Notebook 命令")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. 在顯示的 [建立新的 Notebook] 快顯視窗中輸入 Notebook 的檔案名稱，例如 *HelloWorldInPython.ipynb* (*.ipynb* 表示 IronPython (Jupyter) Notebook)，然後選取 [Python 3.6] 作為語言 (也稱為*核心*)：

    ![建立新的 Notebook 快顯視窗](media/quickstarts/new-notebook-popup.png)

1. 選取 [新增] 以完成建立 Notebook 的作業；該 Notebook 會隨即出現在您專案的檔案清單中：

    ![出現在專案檔案清單中的新 Notebook](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>執行 Notebook

1. 選取新的 Notebook 並在編輯器中加以執行；系統會為此 Notebook 自動啟動您所選取的核心 (自採範例中為 Python 3.6)：

    ![Azure Notebooks 中新 Notebook 的檢視](media/quickstarts/create-notebook-first-open.png)

1. 根據預設，Notebook 會有一個空的程式碼資料格。 若要將資料格類型變更為 **Markdown**，請使用資料格類型下拉式清單選取 **Markdown**：

    ![在新的 Notebook 中變更資料格類型](media/quickstarts/create-notebook-cell-type.png)

1. 在資料格中輸入或貼上下列標題文字：

    ```markdown
    # Hello World in Python
    ```

1. 由於您正在編輯 Markdown，因此文字會顯示為帶有 "#" 的標頭。 若要將 Markdown 轉譯成 HTML，請選取 [執行] 按鈕。 Azure Notebooks 會隨即自動建立新的程式碼資料格：

    ![資料格的 [執行] 按鈕和轉譯的 Markdown](media/quickstarts/run-cell-markdown-render.png)

1. 在程式碼資料格中，輸入下列 Python 程式碼：

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. 選取 [執行] (鍵盤快速鍵：Shift+Enter) 以執行程式碼。 在資料格下方，您應該會看到類似以下文字的成功輸出：

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. 選取儲存圖示以儲存您的工作：

    ![Jupyter Notebook 工具列上的 [儲存] 圖示](media/quickstarts/hello-results-save-icon.png)

1. 選取 [檔案] > [終止並關閉] 功能表命令，以停止伺服器並關閉瀏覽器視窗。

## <a name="share-the-notebook"></a>共用 Notebook

若要共用您的 Notebook，請視需要切換回專案頁面、以滑鼠右鍵按一下 Notebook 檔案並選取 [複製連結] (鍵盤快速鍵：y)，然後將該連結貼到適當的訊息 (電子郵件、IM 等) 中。

在 [專案] 頁面上，您也可以使用 [共用] 功能表取得連結、建立包含連結的電子郵件訊息，或取得 HTML 和 Markdown 內嵌程式碼：

![專案共用命令](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：建立和執行 Jupyter Notebook 來執行線性迴歸](tutorial-create-run-jupyter-notebook.md)
