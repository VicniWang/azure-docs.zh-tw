---
title: 如何使用 Azure Active Directory 報告對登入錯誤進行疑難排解 | Microsoft Docs
description: 了解如何在 Azure 入口網站中使用 Azure Active Directory 報告對登入錯誤進行疑難排解
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22126114f2d4fcb865485d9cebc69f0e35f70201
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198468"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>作法：使用 Azure Active Directory 報告對登入錯誤進行疑難排解

Azure Active Directory (Azure AD) 中的[登入報告](concept-sign-ins.md)可讓您找到組織中有關應用程式存取管理問題的答案，包括：

- 使用者的登入模式為何？
- 一週內有多少使用者登入？
- 這些登入的狀態為何？


此外，登入報告也可協助您對組織中使用者的登入失敗進行疑難排解。 在本指南中，您會了解如何從登入報告中將失敗的登入隔離出來，並藉此了解失敗的根本原因。

## <a name="prerequisites"></a>必要條件

您需要：

* 具有進階 (P1/P2) 授權的 Azure AD 租用戶。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。
* 所擔任的租用戶角色為**全域系統管理員**、**安全性系統管理員**、**安全性讀取者**或**報告讀取者** 的使用者。 此外，任何使用者都可以存取自己的登入資訊。 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>使用登入報告對登入錯誤進行疑難排解

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)，然後選取您的目錄。
2. 選取 [Azure Active Directory]，然後從 [監視] 區段選取 [登入]。 
3. 使用提供的篩選條件來縮小失敗範圍，像是使用者名稱或物件識別碼、應用程式名稱或日期。 此外，從 [狀態] 下拉式清單中選取 [失敗] 只會顯示失敗的登入。 

    ![篩選結果](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. 找出您想要調查的失敗登入並加以選取。 這會開啟其他詳細資料視窗，並顯示有關失敗登入的詳細資訊。 請記下**登入錯誤碼**和**失敗原因**。 

    ![選取記錄](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. 您也可以在詳細資料視窗的 [疑難排解和支援] 索引標籤中找到這項資訊。

    ![疑難排解和支援](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. 失敗原因會說明錯誤。 例如，在上述案例中，失敗原因是**無效的使用者名稱或密碼，或無效的內部部署使用者名稱或密碼**。 這表示使用者輸入了不正確的使用者名稱或密碼來登入 Azure 入口網站。 若要修正，只需使用正確的使用者名稱和密碼再登入一次。

7. 您可以在[登入錯誤碼參考](reference-sign-ins-error-codes.md)中搜尋錯誤碼 (在此範例中為 **50126**)，以取得其他詳細資訊和修復意見。 

8. 如果所有解決方案均失敗，或採取建議動作後問題仍持續發生，請遵循 [疑難排解和支援] 索引標籤中的步驟來[提出支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)。 

## <a name="next-steps"></a>後續步驟

* [登入錯誤碼參考](reference-sign-ins-error-codes.md)
* [登入報告概觀](concept-sign-ins.md)
