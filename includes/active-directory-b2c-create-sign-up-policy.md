---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742440"
---
如果您只想在您的應用程式中啟用註冊功能，您可使用**註冊**使用者流程。 此使用者流程描述客戶在註冊期間將會經歷的體驗，以及應用程式在成功註冊時會收到的權杖內容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在 [管理] 底下選取 [使用者流程]。

按一下刀鋒視窗頂端的 [+新增使用者流程]。

在 [選取使用者流程類型] 底下，選取 [全部]，然後選取要使用的 [註冊] 版本。

名稱  決定您的應用程式所使用的註冊使用者流程名稱。 例如，輸入 **SiUp**。

在 [識別提供者] 底下，選取 [電子郵件註冊]。 (選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。

在 [使用者屬性和宣告] 底下，按一下 [顯示更多]。

在 [收集屬性] 欄中，選擇註冊期間要向取用者收集的屬性。 例如，選取 [國家/區域]、[顯示名稱] 和 [郵遞區號]。

在 傳回宣告 欄中，選擇成功註冊後，您要在權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱]、[身分識別提供者]、[郵遞區號]、[使用者是新的] 和 [使用者的物件識別碼]。

按一下 [確定]。

按一下頁面底部的 [新增] 。 已建立的使用者流程會顯示為 **B2C_1_SiUp** (自動新增 **B2C\_1\_** 部分)。

按一下 [執行使用者流程]。

在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL] 下拉式清單中選取 `https://localhost:44321/`。

按一下 [執行使用者流程]。 新的瀏覽器索引標籤隨即開啟，而您可以開始進行取用者體驗來註冊您的應用程式。

> [!NOTE]
> 建立和更新使用者流程後，需要經過一分鐘才會生效。
>