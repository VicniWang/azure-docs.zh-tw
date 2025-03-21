---
title: 建立 NetApp 帳戶以存取 Azure NetApp Files | Microsoft Docs
description: 說明如何存取 Azure NetApp Files 及建立 NetApp 帳戶，以便設定容量集區及建立磁碟區。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 47b9d25f8db2241bb578528780e28f43d56371e5
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963075"
---
# <a name="create-a-netapp-account"></a>建立 NetApp 帳戶
建立 NetApp 帳戶可讓您設定容量集區，並接著建立磁碟區。 您可以使用 [Azure NetApp Files] 刀鋒視窗來建立新的 NetApp 帳戶。

## <a name="before-you-begin"></a>開始之前
您必須註冊您的訂用帳戶，以使用 NetApp 資源提供者和公開預覽功能。

[註冊 Azure NetApp Files](azure-netapp-files-register.md)

## <a name="steps"></a>步驟 

1. 登入 Azure 入口網站。 
2. 使用下列其中一個方法存取 [Azure NetApp Files] 刀鋒視窗：  
  * 在 Azure 入口網站搜尋方塊中搜尋 **Azure NetApp Files**。  
  * 按一下導覽中的 [所有服務]，然後篩選為 Azure NetApp Files。  

  您可以藉由按一下 [Azure NetApp Files] 刀鋒視窗旁邊的星號，將其加入「我的最愛」。 

3. 按一下 [+ 新增] 以建立新的 NetApp 帳戶。  
  [新增 NetApp 帳戶] 視窗隨即出現。  

4. 請提供 NetApp 帳戶的下列資訊： 
  * **帳戶名稱**  
    指定訂用帳戶的唯一名稱。
  *  **訂用帳戶**  
    從現有訂用帳戶選取訂用帳戶。
  * **資源群組**   
    使用現有的資源群組或建立新群組。
  * **位置**  
    選取您想要帳戶及其子資源位在哪個區域。  

    ![新增 NetApp 帳戶](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. 按一下頁面底部的 [新增] 。     
  您剛建立的 NetApp 帳戶現在會出現在 [Azure NetApp Files] 刀鋒視窗中。 

## <a name="next-steps"></a>後續步驟  

[設定容量集區](azure-netapp-files-set-up-capacity-pool.md)

