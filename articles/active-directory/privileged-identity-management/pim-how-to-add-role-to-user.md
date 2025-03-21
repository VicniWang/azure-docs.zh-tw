---
title: 在 PIM 中指派 Azure AD 目錄角色 | Microsoft Docs
description: 如何在 Azure AD Privileged Identity Management (PIM) 中指派 Azure AD 目錄角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4acabd4b583766ac730558fd07c424ce97a1299a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192502"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>在 PIM 中指派 Azure AD 目錄角色

透過 Azure Active Directory (Azure AD)，全域系統管理員可設定**永久的**目錄角色指派。 您可以使用 [Azure 入口網站](../users-groups-roles/directory-assign-admin-roles.md)或使用 [PowerShell 命令](/powershell/module/azuread#directory_roles)來建立這些角色指派。

Azure AD Privileged Identity Management (PIM) 服務也允許特殊權限角色管理員指派永久的目錄角色。 此外，特殊權限角色系統管理員可以讓使用者**有資格**獲派目錄角色。 合格系統管理員可在需要時啟用角色，而在完成工作之後，其權限就隨即失效。

## <a name="make-a-user-eligible-for-a-role"></a>讓使用者有資格獲派角色

請遵循下列步驟來讓使用者有資格獲派 Azure AD 目錄角色。

1. 以屬於[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色一員的使用者身分登入 [Azure 入口網站](https://portal.azure.com/)。

    如需如何授與其他系統管理員存取權以管理 PIM 的資訊，請參閱[授與其他系統管理員存取權以管理 PIM](pim-how-to-give-access-to-pim.md)。

1. 開啟 **Azure AD Privileged Identity Management**。

    如果您尚未在 Azure 入口網站中起始 PIM，請移至[開始使用 PIM](pim-getting-started.md)。

1. 按一下 [Azure AD 目錄角色]。

1. 按一下 [角色] 或 [成員]。

    ![Azure AD 目錄角色](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 按一下 [新增成員] 以開啟 [新增受控成員]。

1. 按一下 [選取角色]，按一下您想要管理的角色，然後按一下 [選取]。

    ![選取角色](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. 按一下 [選取成員]，選取您想要指派給角色的使用者，然後按一下 [選取]。

    ![選取角色](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 在 [新增受控成員] 中，按一下 [確定] 以將使用者新增至角色。

1. 在角色清單中，按一下您剛才指派的角色，以查看成員清單。

     指派角色之後，您選取的使用者將會在成員清單中顯示為該角色的**合格**使用者。

    ![角色的合格使用者](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 既然使用者有資格扮演某個角色，請讓他們知道他們可以根據[在 PIM 中啟用我的 Azure AD 目錄角色](pim-how-to-activate-role.md)中的指示來啟用角色。

    啟用期間，系統會要求合格的系統管理員註冊 Azure Multi-factor Authentication (MFA)。 如果使用者無法註冊MFA，或使用者使用的是 Microsoft 帳戶 (通常是 @outlook.com)，您就需要將他們的所有角色設為永久。

## <a name="make-a-role-assignment-permanent"></a>設定永久角色指派

根據預設，新的使用者只有獲派目錄角色的資格。 如果您想要設定永久角色指派，請遵循下列步驟。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 目錄角色]。

1. 按一下 [成員]。

    ![成員的清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 按一下您想要設為永久的 [合格] 角色。

1. 按一下 [更多]，然後按一下 [設為永久]。

    ![設定永久角色指派](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    角色現在會列為**永久**。

    ![永久變更的成員清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>從角色移除使用者

您可以將使用者從角色指派中移除，但請務必一律至少保留一個永久全域系統管理員使用者。 如果您不確定哪些使用者仍然需要其角色指派，您可以[開始進行角色的存取權檢閱](pim-how-to-start-security-review.md)。

請遵循下列步驟移除目錄角色中的特定使用者。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 目錄角色]。

1. 按一下 [成員]。

    ![成員的清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 按一下您想要移除的角色指派。

1. 按一下 [更多]，然後按一下 [移除]。

    ![移除角色](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 在要求您確認的訊息中，按一下 [是]。

    ![移除角色](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    已移除角色指派。

## <a name="authorization-error-when-assigning-roles"></a>指派角色時的授權錯誤

如果您最近已針對訂用帳戶啟用 PIM，而且在嘗試將使用者設為合格的目錄角色時發生授權錯誤，則可能是因為 MS-PIM 服務主體尚未具備適當的權限。 MS-PIM 服務主體必須擁有[使用者存取系統管理員](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色，才能將角色指派給其他人。 您不需等到系統為 MS-PIM 指派使用者存取系統管理員角色，可以改為手動指派。

遵循下列步驟，將使用者存取系統管理員角色指派給訂用帳戶的 MS-PIM 服務主體。

1. 以全域系統管理員身分登入 Azure 入口網站。

1. 選擇 [所有服務]，然後選擇 [訂用帳戶]。

1. 選擇您的訂用帳戶。

1. 選擇 [存取控制 (IAM)]。

1. 選擇 [角色指派]，以查看訂用帳戶範圍中目前的角色指派清單。

   ![訂用帳戶的存取控制 (IAM) 刀鋒視窗](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. 檢查是否已為 **MS-PIM** 服務主體指派**使用者存取系統管理員**角色。

1. 若未指派，請選擇 [新增角色指派] 以開啟 [新增角色指派] 窗格。

1. 在 [角色] 下拉式清單中，選取 [使用者存取系統管理員] 角色。

1. 在 [選取] 清單中，尋找並選取 [MS-PIM] 服務主體。

   ![新增適用於 MS-PIM 的權限](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. 選擇 [儲存] 以指派角色。

   稍後片刻，即會在訂用帳戶範圍中，為 MS-PIM 服務主體指派使用者存取系統管理員角色。

   ![適用於 MS-PIM 的使用者存取系統管理員角色](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>後續步驟

- [在 PIM 中設定 Azure AD 目錄角色設定](pim-how-to-change-default-settings.md)
- [在 PIM 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
