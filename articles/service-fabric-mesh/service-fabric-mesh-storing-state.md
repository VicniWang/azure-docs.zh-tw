---
title: Azure Service Fabric Mesh 的狀態儲存體選項 | Microsoft Docs
description: 深入了解如何在 Azure Service Fabric Mesh 上執行的 Service Fabric Mesh 應用程式中可靠地儲存狀態。
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 81d9fc7babef7c1d14a0acc420d0c16c05b83215
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354321"
---
# <a name="state-management-with-service-fabric"></a>使用 Service Fabric 的狀態管理

Service Fabric 支援許多不同的狀態儲存體選項。 如需狀態管理模式和 Service Fabric 的概念性概觀，請參閱 [Service Fabric 概念：狀態](/azure/service-fabric/service-fabric-concepts-state)。 不論您的服務在 Service Fabric Mesh 內部或外部執行，這些相同的概念都適用。 

使用 Service Fabric Mesh，您可以輕鬆地部署新應用程式，並將它連線到 Azure 中裝載的現有資料存放區。 除了使用任何遠端資料庫，有數個選項可供儲存資料，其取決於服務需要本機或遠端儲存體。 

## <a name="volumes"></a>磁碟區

容器通常會利用暫存磁碟。 但暫存磁碟是暫時的物件，因此您會得到新的暫存磁碟，當容器損毀時則會遺失資訊。 您也很難與其他容器共用暫存磁碟上的資訊。 磁碟區是容器裡面掛接的目錄，可用來保存狀態。 磁碟區會提供一般用途的檔案儲存體，並可讓您使用一般磁碟 I/O 檔案 API 來讀取/寫入檔案。 磁碟區資源說明如何掛接目錄，以及要使用哪個支援儲存體。 您可以選擇 Azure 檔案儲存體或 Service Fabric 磁碟區磁碟來儲存資料。

![磁碟區][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric 可靠磁碟區

Service Fabric 可靠磁碟區是用來將本機磁碟區掛接至容器的 Docker 磁碟區驅動程式。 讀取和寫入是本機作業且速度很快。 資料會複寫至外部的次要節點，以便具有高度可用性。 容錯移轉也很快速。 容器損毀時，便會容錯移轉至已有資料複本的節點。 如需範例，請參閱[如何使用 Service Fabric 可靠磁碟區來部署應用程式。](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md)

### <a name="azure-files-volume"></a>檔案儲存體磁碟區

檔案儲存體磁碟區是用來將檔案儲存體共用掛接至容器的 Docker 磁碟區驅動程式。 Azure 檔案儲存體會使用網路儲存體，因此讀取和寫入會透過網路進行。 相較於 Service Fabric 可靠磁碟區，Azure 檔案儲存體的效能較差，但會提供較便宜且完全可靠的資料選項。 如需範例，請參閱[如何使用檔案儲存體磁碟區來部署應用程式](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)。

## <a name="next-steps"></a>後續步驟

如需應用程式模型的資訊，請參閱 [Service Fabric 資源](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
