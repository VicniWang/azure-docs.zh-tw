---
title: 疑難排解：已下載的 Azure Active Directory 活動記錄中遺漏資料 | Microsoft Docs
description: 提供您已下載的 Azure Active Directory 活動記錄中遺漏資料的解決方案。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ba5f803f59263f9bfebfd4ec8635d5cdd6d90a0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171769"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>我在下載的 Azure Active Directory 活動記錄中無法找到所有資料

## <a name="symptoms"></a>徵兆

我已下載活動記錄 (稽核或登入)，卻沒看到我所選擇時間的所有記錄。 原因為何？ 

 ![報告](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>原因

當您在 Azure 入口網站中下載活動記錄時，我們會將級別限制為 5000 筆記錄，依最新記錄先排序。 

## <a name="resolution"></a>解決方案

您可以利用 [Azure AD 報告 API](concept-reporting-api.md) 在任何指定時間點擷取最多一萬筆記錄。 我們建議的方法是[定期執行指令碼](tutorial-signin-logs-download-script.md)，呼叫報告 API 以增量方式來擷取一段時間的記錄 (例如，每天或每週)。 

## <a name="next-steps"></a>後續步驟

* [Azure Active Directory 報告常見問題集](reports-faq.md)

