---
title: 設定以 Azure Site Recovery 從 VMware 複寫到 Azure 的來源環境 | Microsoft Docs
description: 本文說明如何設定您的內部部署環境，以利用 Azure Site Recovery 將 VMware 虛擬機器複寫到 Azure。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: b4ed878b19b2eebe01c9888388949600e85c56e3
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792969"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>設定從 VMware 複寫到 Azure 的來源環境

本文說明如何設定您的來源內部部署環境，將 VMware 虛擬機器複寫到 Azure。 這包括選取您的複寫案例、將內部部署機器設定為 Site Recovery 組態伺服器以及自動探索內部部署 VM 等步驟。 

## <a name="prerequisites"></a>必要條件

本文假設您已經：

- 透過 [Azure Site Recovery 部署規劃工具](site-recovery-deployment-planner.md)的協助完成部署規劃。 這可協助您根據每日資料變化率來配置足夠的頻寬，以符合您所需的復原點目標 (RPO)。
- 在 [Azure 入口網站](http://portal.azure.com)中[設定資源](tutorial-prepare-azure.md)。
- [設定內部部署 VMware](vmware-azure-tutorial-prepare-on-premises.md)，包括進行自動探索的專用帳戶。

## <a name="choose-your-protection-goals"></a>選擇您的保護目標

1. 在 [復原服務保存庫] 中，選取保存庫名稱。 在此案例中，我們會使用 **ContosoVMVault**。
2. 在 [使用者入門] 中，選取 Site Recovery。 然後選取 [準備基礎結構]。
3. 在 [保護目標] > [您的電腦位於何處] 中，選取 [內部部署]。
4. 在 [您要將電腦複寫到何處] 中，選取 [複製到 Azure]。
5. 在 [您的電腦虛擬化了嗎] 中，選取 [是，使用 VMware vSphere Hyperviso]。 然後選取 [確定]。

## <a name="set-up-the-configuration-server"></a>設定組態伺服器

您可以透過開放式虛擬化應用程式 (OVF) 範本，將組態伺服器設定為內部部署 VMware VM。 [深入了解](concepts-vmware-to-azure-architecture.md)要安裝在 VMware VM 上的元件。

1. 深入了解組態伺服器部署的[必要條件](vmware-azure-deploy-configuration-server.md#prerequisites)。
2. [檢查部署的容量數字](vmware-azure-deploy-configuration-server.md#capacity-planning)。
3. [下載](vmware-azure-deploy-configuration-server.md#download-the-template)和[匯入](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) OVF 範本來設定執行組態伺服器的內部部署 VMware VM。 範本所提供的授權屬於評估授權，有效期為 180 天。 在此期間過後，客戶就必須使用所購買的授權來啟用時段。
4. 開啟 VMware VM，並在復原服務保存庫中[註冊它](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services)。

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>將 Azure Site Recovery 的資料夾從防毒程式中排除

### <a name="if-antivirus-software-is-active-on-source-machine"></a>如果來源機器上啟用防毒軟體

如果來源機器已啟用防毒軟體，請排除安裝資料夾。 因此，請排除資料夾 C:\ProgramData\ASR\agent 以便順暢地進行複寫。

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>如果組態伺服器上啟用防毒軟體

請從防毒軟體排除下列資料夾，以便順暢地進行複寫，以及避免發生連線問題

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool 
 - C:\thirdparty
 - C:\Temp
 - C:\strawberry
 - C:\ProgramData\MySQL
 - C:\Program Files (x86)\MySQL
 - C:\ProgramData\ASR
 - C:\ProgramData\Microsoft Azure Site Recovery
 - C:\ProgramData\ASRLogs
 - C:\ProgramData\ASRSetupLogs
 - C:\ProgramData\LogUploadServiceLogs
 - C:\inetpub
 - ASR 伺服器安裝目錄。 例如︰E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>如果相應放大處理序伺服器/主要目標啟用防毒軟體

請從防毒軟體排除下列資料夾

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. ASR 負載平衡的處理序伺服器安裝目錄，範例：C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>後續步驟
[設定您的目標環境](./vmware-azure-set-up-target.md) 
