---
title: Azure Service Fabric Mesh 應用程式的延展性 | Microsoft Docs
description: 了解如何調整 Azure Service Fabric Mesh 中的服務。
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c58961011231f74b09abdf313b8624ddc8ad52c1
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892940"
---
# <a name="scaling-service-fabric-mesh-applications"></a>調整 Service Fabric Mesh 應用程式

將應用程式部署到 Service Fabric Mesh 的其中一個主要優點是可讓您輕鬆相應縮小或放大服務。這應用於處理您服務上不同數量的負載，或是改善可用性。 您可以手動相應縮小或相應放大您的服務，或設定自動調整原則。

## <a name="manual-scaling-instances"></a>手動調整執行個體

在應用程式資源的部署範本中，每個服務都有一個 *replicaCount* 屬性，可用於設定您希望部署該服務的次數。 應用程式可由多個服務組成，每個服務都具有唯一的 *replicaCount* 數，這些服務會一同部署及管理。 為了調整服務複本數，請在部署範本或參數檔案中修改您欲調整的每個服務 *replicaCount* 值。 然後，升級應用程式。

如需以手動方式調整服務執行個體的範例，請參閱[手動相應縮小或相應放大您的服務](service-fabric-mesh-tutorial-template-scale-services.md)。

## <a name="autoscaling-service-instances"></a>自動調整服務執行個體
自動調整是 Service Fabric 的額外功能，可讓您以動態方式調整服務執行個體的數目 (水平調整)。 自動調整提供絕佳的彈性，並且可以根據 CPU 或記憶體使用率來佈建或移除服務執行個體。  自動調整可讓您針對工作負載執行正確的服務執行個體數目，並進行成本最佳化。

自動調整原則會根據服務資源檔案中的每個服務進行定義。 每個調整原則都由兩個部分組成：

- 調整觸發程序，用於描述何時將執行服務調整。 決定服務調整時機的因素有三個。 「負載閾值下限」是一個決定何時應該相應縮小服務規模的值。 如果分割區的所有執行個體平均負載低於此值，就會相應縮小服務規模。 「負載閾值上限」是一個決定何時應該相應放大服務規模的值。如果分割區的所有執行個體平均負載高於此值，就會相應放大服務規模。「調整間隔」會決定檢查觸發程序的頻率 (以秒為單位)。 檢查觸發程序之後，如果需要調整，就會套用此機制。 如果不需要調整，則不會執行任何動作。 在這兩種情況下，在調整間隔再次到期之前，都不會再次檢查觸發程序。

- 調整機制，用於描述觸發調整時，將如何執行調整。 「調整增量」會決定當觸發機制時，要新增或移除多少個執行個體。 「最大執行個體計數」會定義調整的上限。 如果執行個體數目達到此限制，則不論負載情況為何，服務都不會相應放大規模。 「最小執行個體計數」會定義調整的下限。 如果分割區的執行個體數目達到此限制，則不論負載情況為何，服務都不會相應縮小規模。

若要了解如何設定您服務的自動調整原則，請閱讀[自動調整服務](service-fabric-mesh-howto-auto-scale-services.md)。

## <a name="next-steps"></a>後續步驟

如需應用程式模型的資訊，請參閱 [Service Fabric 資源](service-fabric-mesh-service-fabric-resources.md)
