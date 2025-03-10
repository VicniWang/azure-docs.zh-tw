---
title: 用於 Azure Notebooks 的使用者設定檔和識別碼
description: 如何使用 Azure Notebooks 建立及管理您的使用者設定檔和使用者識別碼。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 18b698861a791174f2e254543d5d3b7a54052574
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54854156"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>您 Azure Notebooks 的設定檔和使用者識別碼

在 Azure Notebooks 功能強大的共同作業空間內，在您的使用者設定檔可將您的公共形象呈現給他人：

[![Azure Notebooks 設定檔頁面](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

您的使用者識別碼會包含在您用來共用專案和 Notebook 的 URL 中。 下列清單將說明不同的 URL 模式：

- `https://notebooks.azure.com/<user_id>`：您的設定檔頁面。
- `https://notebooks.azure.com/<user_id>/projects`：您的專案。 您會看到所有專案；其他使用者只會看到您的公用專案。
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`：專案檔。
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`：特定專案的複製品。
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`：特定 Notebook 或檔案的 HTML 預覽。

## <a name="your-user-id"></a>您的使用者識別碼

第一次登入 Azure Notebooks 時，系統會自動為您的帳戶指派暫時的使用者識別碼，例如 "anon-idr3ca"。 只要您的使用者識別碼開頭是 "anon-"，每當您登入時，Azure Notebooks 就會提示您變更該識別碼：

![在登入 Azure Notebooks 時發出建立使用者識別碼的提示](media/accounts/create-user-id.png)

暫時使用者名稱旁也會出現**設定使用者識別碼**命令：

![在您使用暫時識別碼時顯示的設定使用者識別碼命令](media/accounts/configure-user-id-command.png)

您也可以隨時在設定檔頁面上變更使用者識別碼。

使用者識別碼必須由至少四個字母、數字和連字號組成。 不允許使用任何其他字元，且使用者識別碼不得以連字號開始或結尾，或是連續使用多個連字號。

> [!Important]
> 變更您的識別碼後，您已使用先前的識別碼共用的任何 URL 就會失效。 您可以將識別碼重新變更為原先的識別碼，以重新驗證連結。 但在此同時，可能會有其他使用者宣告未使用的識別碼。

## <a name="your-profile"></a>您的設定檔

您的設定檔由 URL `https://notebooks.azure.com/<user_id>` 中可公開檢視的資訊所組成。 您的設定檔頁面也會顯示您最近使用的專案和任何加上星號的專案。

若要編輯您的設定檔，請在您的設定檔頁面上使用**編輯設定檔資訊**命令。 設定檔的各區段如下所示：

| 區段 | 內容 |
| --- | --- |
| 設定檔相片 | 您的設定檔頁面上顯示的影像。 |
| 帳戶資訊 | 您的顯示名稱、使用者識別碼和公用電子郵件帳戶。 此處的電子郵件帳戶可提供其他使用者與您連絡的管道，且此帳戶可與您用來登入 Azure Notebook 本身的[帳戶](azure-notebooks-user-account.md)不同。 |
| 設定檔資訊 | 您的所在位置、公司、工作職稱、網站和簡短描述。 |
| 社交設定檔 | 您的 GItHub、Twitter 和 Facebook 識別碼 (如果您想要加以共用)。 |
| 隱私權設定 | 提供兩個命令：<ul><li>**匯出我的設定檔**：建立及下載 *.zip* 檔案，其中包含 Azure Notebook 在您的設定檔中儲存的所有資訊，包括您的相片、設定檔資訊和安全性記錄。</li><li>**刪除我的帳戶**：永久刪除您儲存在 Azure Notebook 中的所有個人資訊。</li></ul> |
| 啟用網站功能 | 可讓您控制 Azure Notebooks 各方面的行為：<ul><li>**Notebook 的整合前端**：可提升 Notebook 的啟動速度和持續性。</li><li>**依預設會以 JupyterLab 執行**：根據預設，Azure Notebook 會提供適用於多數使用者的簡易使用者介面。 JupyterLab 可提供更豐富、但也較複雜的介面，適用於有經驗的使用者。</li><li>**VNext 網站**：可啟用這份文件中顯示的現代化 Web 版面配置。</li></ul> |

## <a name="next-steps"></a>後續步驟  

> [!div class="nextstepaction"]
> [教學課程：建立和執行 Jupyter Notebook 來執行線性迴歸](tutorial-create-run-jupyter-notebook.md)
