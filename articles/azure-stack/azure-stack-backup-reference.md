---
title: Azure Stack 基礎結構備份服務的參考 | Microsoft Docs
description: 本文包含 Azure Stack 基礎結構備份服務的參考資料。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: a7930ea86f7972a6e4abb939fb148d519ca924e9
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416712"
---
# <a name="infrastructure-backup-service-reference"></a>基礎結構備份服務的參考

## <a name="azure-backup-infrastructure"></a>Azure 備份基礎結構

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

Azure Stack 是由許多服務所組成，包含入口網站、Azure Resource Manager 和基礎結構管理體驗。 Azure Stack 類似設備的管理體驗，著重於降低解決方案操作員所需要面對的操作複雜性。

「基礎結構備份」旨在將基礎結構服務在備份與還原資料上的複雜性內部化，以確保操作員可以專注在管理解決方案及維持對使用者的 SLA 上。

需要將備份資料匯出到外部共用，才能避免將備份儲存在相同系統上。 需要外部共用這點，可讓系統管理員有根據現有的公司 BC/DR 政策，來決定資料儲存位置的彈性。 

### <a name="infrastructure-backup-components"></a>基礎結構備份的元件

基礎結構備份包括下列元件：

 - **基礎結構備份控制器**  
 Azure Stack 雲端會具現化基礎結構備份控制器，且每個 Azure Stack 雲端中都有基礎結構備份控制器。
 - **備份資源提供者**  
 備份資源提供者 (備份 RP) 是由使用者介面，以及會公開 Azure Stack 基礎結構基本備份功能的應用程式介面 (API) 所組成。

#### <a name="infrastructure-backup-controller"></a>基礎結構備份控制器

基礎結構備份控制器是種具現化的 Azure Stack 雲端 Service Fabric 服務。 備份資源是在區域層級建立的，且會從 AD、CA、Azure Resource Manager、CRP、SRP、NRP、KeyVault、RBAC 擷取區域特定的服務資料。 

### <a name="backup-resource-provider"></a>備份資源提供者

備份資源提供者代表 Azure Stack 入口網站中，基本設定和備份資源清單的使用者介面。 操作員可以在使用者介面中執行下列作業：

 - 藉由提供外部儲存位置、認證和加密金鑰，來啟用第一次的備份
 - 檢視已完成建立的備份資源，和正在建立的狀態資源
 - 修改備份控制器存放備份資料的儲存位置
 - 修改備份控制器用來存取外部儲存位置的認證
 - 修改備份控制器用來加密備份的加密金鑰 


## <a name="backup-controller-requirements"></a>備份控制器的需求

本節描述基礎結構備份的重要需求。 建議您先仔細檢閱資訊，再啟用 Azure Stack 執行個體的備份，然後在部署和後續作業期間，必要時回顧參考。

這些需求包括：

  - **軟體需求** – 說明支援的儲存位置和大小調整指導方針。 
  - **網路需求** – 說明不同儲存位置的網路需求。  

### <a name="software-requirements"></a>軟體需求

#### <a name="supported-storage-locations"></a>支援的儲存位置

| 儲存位置                                                                 | 詳細資料                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| 裝載在信任的網路環境中儲存裝置上的 SMB 檔案共用 | 位於已部署 Azure Stack 的同一個資料中心，或位於不同資料中心的 SMB 共用。 多個 Azure Stack 執行個體可以使用相同的檔案共用。 |
| 在 Azure 上的 SMB 檔案共用                                                          | 目前不支援。                                                                                                                                 |
| 在 Azure 上的 Blob 儲存體                                                            | 目前不支援。                                                                                                                                 |

#### <a name="supported-smb-versions"></a>支援的 SMB 版本

| SMB | 版本 |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>儲存位置大小調整 

基礎結構備份控制器會隨選備份資料。 建議一天至少備份兩次，並保留最多七天份的備份。 

**1811 和更新版本**
| 環境規模 | 預估的備份大小 | 所需的空間總容量 |
|-------------------|--------------------------|--------------------------------|
| 4-16 個節點        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**1811 之前的版本**
| 環境規模 | 預估的備份大小 | 所需的空間總容量 |
|-------------------|--------------------------|--------------------------------|
| 4-16 個節點，ASDK  | 10 GB                     | 140 GB                        |

### <a name="network-requirements"></a>網路需求
| 儲存位置                                                                 | 詳細資料                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 裝載在信任的網路環境中儲存裝置上的 SMB 檔案共用 | 若 Azure Stack 執行個體位於防火牆保護的環境中，則需要使用連接埠 445。 基礎結構備份控制器會透過連接埠 445，起始對 SMB 檔案伺服器的連線。 |
| 若要使用檔案伺服器的 FQDN，名稱必須可從 PEP 進行解析             |                                                                                                                                                                                         |

> [!Note]  
> 不需要開啟任何輸入連接埠。

### <a name="encryption-requirements"></a>加密需求

自 1901 版起，基礎結構備份服務在雲端復原時，會使用具有公開金鑰 (.CER) 的憑證來加密備份資料，使用具有私密金鑰 (.PFX) 的憑證以解密備份資料。   
 - 憑證用於傳輸金鑰，不會用來建立經驗證的安全通訊。 有基於此，憑證會是自我簽署的憑證。 Azure Stack 不需要驗證此憑證的根或信任，因此不需要外部的網際網路存取權。
 
自我簽署的憑證有兩個部分，一個是具有公開公鑰，另一個是具有私密金鑰：
 - 加密備份資料：具有公開金鑰 (匯出至 .CER 檔案) 的憑證用於加密備份資料
 - 解密備份資料：具有私密金鑰 (匯出至 .PFX 檔案) 的憑證用於解密備份資料

內部密碼輪替不會管理具有公開金鑰 (.CER) 的憑證。 若要輪替憑證，您必須建立新的自我簽署憑證，並使用新的檔案 (.CER) 更新備份設定。  
 - 所有現存的備份將使用過去的公開金鑰保持加密狀態。 新的備份將會使用新的公開金鑰。 
 
基於安全理由，Azure Stack 不會保留雲端復原期間所使用具有私密金鑰 (.PFX) 的憑證。 這個檔案在雲端復原期間必須明確提供。  

**回溯相容性模式**自 1901 版起，加密金鑰支援已被取代，將在未來的版本中移除。 如果您是從 1811 版更新的，而且是透過已使用加密金鑰啟用的備份更新，則 Azure Stack 將會繼續使用該加密金鑰。 將會支援至少 3 個版本的回溯相容性模式。 這此之後，將會需要憑證。 
 * 從加密金鑰更新到憑證是單向作業。  
 * 所有現有的備份將使用加密金鑰維持加密狀態。 新的備份將會使用新的憑證。 

## <a name="infrastructure-backup-limits"></a>基礎結構備份限制

當您規劃、部署和操作 Microsoft Azure Stack 執行個體時，請考慮這些限制。 下表描述了這些限制。

### <a name="infrastructure-backup-limits"></a>基礎結構備份限制
| 限制識別碼                                                 | 限制        | 註解                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| 備份類型                                                      | 僅限完整備份    | 基礎結構備份控制器只支援完整備份。 不支援支援增量備份。                                          |
| 排定的備份                                                | 已排定和手動  | 備份控制器支援已排定的備份和隨選備份                                                                                 |
| 並行備份作業的數目上限                                   | 1            | 備份控制器的每個執行個體只支援一個作用中的備份作業。                                                                  |
| 網路交換器設定                                     | 不在範圍中 | 系統管理員必須使用 OEM 工具來備份網路交換器設定。 請參閱各 OEM 廠商所提供的 Azure Stack 文件。 |
| 硬體生命週期主機                                          | 不在範圍中 | 系統管理員必須使用 OEM 工具來備份硬體生命週期主機。 請參閱各 OEM 廠商所提供的 Azure Stack 文件。      |
| 檔案共用的數目上限                                    | 1            | 只有一個檔案共用可以用來儲存備份資料                                                                                        |
| 備份應用程式服務、函式、SQL、MySQL 資源提供者資料 | 不在範圍中 | 如需部署和管理由 Microsoft 所建立的加值 RP，請參閱發行的指導方針。                                                  |
| 備份第三方資源提供者                              | 不在範圍中 | 如需部署和管理由第三方所建立的加值 RP，請參閱發行的指導方針。                                          |

## <a name="next-steps"></a>後續步驟

 - 若要深入了解基礎結構備份服務，請參閱[使用基礎結構備份服務進行 Azure Stack 的備份和資料復原](azure-stack-backup-infrastructure-backup.md)。
