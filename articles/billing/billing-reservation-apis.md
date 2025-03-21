---
title: 適用於 Azure 保留自動化的 API | Microsoft Docs
description: 了解您可以用來以程式設計方式取得保留資訊的 Azure API。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: banders
ms.openlocfilehash: a71c456f017e80da85f27437738aa963ec967a3c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904146"
---
# <a name="apis-for-azure-reservation-automation"></a>適用於 Azure 保留自動化的 API

使用 Azure API 以程式設計方式為您的組織取得 Azure 服務或軟體保留的資訊。

## <a name="find-reservation-plans-to-buy"></a>尋找要購買的保留方案

如需根據您組織的使用量來購買保留方案，請使用「保留建議 API」來取得建議。 如需詳細資訊，請參閱[取得保留建議](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)。

您也可以使用「耗用量 API 使用量詳細資料」來分析您的資源使用量。 如需詳細資訊，請參閱[使用量詳細資料 - 依帳單帳戶列出的計費週期清單](/rest/api/consumption/usagedetails/listforbillingperiodbybillingaccount)。 您持續使用的 Azure 資源，通常是保留的最佳候選項目。

## <a name="buy-a-reservation"></a>購買保留項目

您目前無法以程式設計方式購買保留。 若要購買保留，請參閱下列文章：

服務方案：
- [虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

軟體方案：
- [SUSE Linux 軟體](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>取得保留

如果您是有 Enterprise 合約 (EA 客戶) 的 Azure 客戶，您可以使用[取得保留執行個體收費 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) 來取得您組織購買的保留。 對於其他訂用帳戶，請使用 API [保留訂單 - 清單](/rest/api/reserved-vm-instances/reservationorder/list)來取得您所購買且有權限檢視之保留的清單。 根據預設，帳戶擁有者或購買保留的人員，有權限檢視該保留。

## <a name="see-reservation-usage"></a>查看保留使用量

如果您是 EA 客戶，您可以程式設計方式檢視您組織中之保留的使用方式。 如需詳細資訊，請參閱[取得企業客戶的保留執行個體使用量](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)。 對於其他訂用帳戶，可以使用 API[保留項目摘要 - 依保留順序和保留列出的清單](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)。

如果您發現您組織的保留太少被使用：

- 請確認您組織建立的虛擬機器符合保留上的 VM 大小。
- 請確認執行個體大小彈性已開啟。 如需詳細資訊，請參閱[管理保留 - 變更保留的 VM 執行個體的最佳化設定](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)。
- 變更要共用的保留範圍，使它更廣泛地套用。 如需詳細資訊，請參閱[管理保留 - 變更保留範圍](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation)。
- 交換未使用的數量。 如需詳細資訊，請參閱[管理保留 - 取消和交換](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges)。

## <a name="give-access-to-reservations"></a>提供保留的存取權

使用[保留 - 作業 - 清單 API](/rest/api/reserved-vm-instances/reservationorder/list) 來取得使用者有權存取的所有保留的清單。 若要以程式設計方式提供保留的存取權，請參閱下列其中一篇文章：

- [使用 RBAC 和 REST API 來管理存取權](../role-based-access-control/role-assignments-rest.md)
- [使用 RBAC 和 Azure PowerShell 來管理存取權](../role-based-access-control/role-assignments-powershell.md)
- [使用 RBAC 和 Azure CLI 來管理存取權](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>分割或合併保留項目

購買保留內的多個資源執行個體之後，建議您將該保留中的執行個體指派至不同的訂用帳戶。 您可以變更保留範圍，使它套用至相同計費內容內的所有訂用帳戶。 但是，基於成本管理或預算考量，您可能想要保持範圍是「單一訂用帳戶」，並將保留執行個體指派至特定訂用帳戶。 

若要分割保留，請使用 API [保留 - 分割](/rest/api/reserved-vm-instances/reservation/split)。 您也可以使用 PowerShell 來分割保留。 如需詳細資訊，請參閱[管理保留 - 將保留分割成兩個保留](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations)。

若要將兩個保留合併成一個保留，請使用 API [保留 - 合併](/rest/api/reserved-vm-instances/reservation/merge)。

## <a name="change-scope-for-a-reservation"></a>變更保留範圍

保留的範圍可以是單一訂用帳戶或您的計費內容中的所有訂用帳戶。 如果您將範圍設定為單一訂用帳戶，此保留會對應至所選訂用帳戶中執行的資源。 如果您將範圍設定為共用，Azure 會將保留對應至計費內容中所有訂用帳戶上執行的資源。 計費內容取決於您購買此保留所用的訂用帳戶。 如需詳細資訊，請參閱[管理保留 - 變更範圍](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation)。

若要以程式設計方式變更範圍，請使用 API [保留 - 更新](/rest/api/reserved-vm-instances/reservation/update)。

## <a name="learn-more"></a>深入了解

- [什麼是 Azure 的保留](billing-save-compute-costs-reservations.md)
- [了解 VM 保留折扣的套用方式](billing-understand-vm-reservation-charges.md)
- [了解如何套用 SUSE Linux Enterprise 軟體方案折扣](billing-understand-suse-reservation-charges.md)
- [了解其他保留折扣的套用方式](billing-understand-reservation-charges.md)
- [了解隨用隨付方案訂用帳戶的保留項目使用量](billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
- [Windows 軟體的成本不包括在保留項目內](billing-reserved-instance-windows-software-costs.md)
- [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)