---
title: 透過 Azure Migrate 使用機器相依性分組機器 | Microsoft Docs
description: 說明如何透過 Azure Migrate 服務使用機器相依性來建立評量。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: e62a792e7503e65ebe008a52430f86f1f3a00006
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55456012"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>使用機器相依性對應分組機器

本文說明如何將機器相依性視覺化，建立機器群組以進行 [Azure Migrate](migrate-overview.md) 評量。 在執行評量前，若想要透過交叉檢查機器相依性的方式，對信心等級較高的虛擬機器群組進行評估，通常會使用此方法。 相依性視覺效果可協助您有效地規劃移轉至 Azure。 它可協助您確保不會遺留任何動作，且在您要移轉至 Azure 時，不會發生意外的中斷。 您可以探索所有需要一起移轉的交互相依系統，以及識別執行中的系統是否仍為使用者提供服務，還是應該解除委任而非移轉。

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

## <a name="prepare-for-dependency-visualization"></a>為相依性視覺效果做準備
Azure Migrate 運用 Log Analytics 中的「服務對應」解決方案，來呈現機器的相依性視覺效果。

### <a name="associate-a-log-analytics-workspace"></a>與 Log Analytics 工作區建立關聯
若要利用相依性視覺效果，您需要將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案建立關聯。 您只能在建立移轉專案的相同訂用帳戶中，建立或連結工作區。

- 若要將 Log Analytics 工作區連結至專案，請在 [概觀] 中，移至專案的 [基本資訊] 區段，然後按一下 [需要設定]

    ![與 Log Analytics 工作區建立關聯](./media/concepts-dependency-visualization/associate-workspace.png)

- 與工作區建立關聯時，您可以選擇建立新的工作區，或連結現有的工作區：
  - 建立新工作區時，您必須指定工作區的名稱。 然後會在和移轉專案相同之 [Azure 地理區](https://azure.microsoft.com/global-infrastructure/geographies/)的區域中建立工作區。
  - 當您連結現有的工作區時，您能以和移轉專案相同的方式，從相同訂用帳戶中所有的可用工作區中挑選。 請注意，系統只會列出那些在[支援服務對應](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)的區域中建立的工作區。 若要能夠連結工作區，請確定您有該工作區的「讀取者」存取權。

> [!NOTE]
> 您無法變更與移轉專案相關聯的工作區。

### <a name="download-and-install-the-vm-agents"></a>下載並安裝虛擬機器代理程式
在您設定工作區之後，您必須在待評估的每個內部部署機器上，下載及安裝代理程式。 此外，如果您的機器沒有網際網路連線，則需要下載並安裝 [Log Analytics 閘道](../azure-monitor/platform/gateway.md)。

1. 在 [概觀] 中，按一下 [管理] > [機器]，並選取所需的機器。
2. 在 [相依性] 資料行中，按一下 [安裝代理程式]。
3. 至 [相依性] 頁面，下載 Microsoft Monitoring Agent (MMA) 及相依性代理程式，安裝到要進行評估的每個虛擬機器上。
4. 複製工作區識別碼與金鑰。 在內部部署機器上安裝 MMA 時，需要用到這些識別碼與金鑰。

> [!NOTE]
> 若要自動安裝代理程式，可使用任何部署工具 (例如 System Center Configuration Manager) 或使用我們的合作夥伴工具 [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)，其具有 Azure Migrate 的代理程式部署解決方案。

### <a name="install-the-mma"></a>安裝 MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>在 Windows 電腦上安裝代理程式

在 Windows 電腦上安裝代理程式：

1. 按兩下下載的代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。 在 [授權條款] 頁面上，按一下 [我同意] 以接受授權。
3. 在 [目的地資料夾] 中，保留或修改預設的安裝資料夾 > [下一步]。
4. 在 [代理程式安裝選項] 中，選取 [Azure Log Analytics] > [下一步]。
5. 按一下 [新增] 以新增 Log Analytics 工作區。 貼上您從入口網站複製的工作區識別碼和金鑰。 按 [下一步] 。

您可以從命令列或使用自動化方法 (例如 Azure 自動化 DSC、System Center Configuration Manager) 來安裝代理程式，或者如果您已經在您的資料中心部署 Microsoft Azure Stack，請使用 Azure Resource Manager 範本。 [了解更多](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)有關如何使用這些方法來安裝 MMA 代理程式。

#### <a name="install-the-agent-on-a-linux-machine"></a>在 Linux 電腦上安裝代理程式

在 Linux 電腦上安裝代理程式：

1. 使用 scp/sftp 將適當的套件組合 (x86 或 x64) 傳輸到 Linux 電腦。
2. 使用 --安裝引數安裝套件組合。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[了解更多](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) MMA 支援的 Linux 作業系統清單。

#### <a name="install-the-agent-on-a-machine-monitored-by-scom"></a>在 SCOM 監視的電腦上安裝代理程式

對於受 System Center Operations Manager 2012 R2 或更新版本監視的電腦，不需要安裝 MMA 代理程式。 服務對應已與 SCOM 整合，此整合可利用 SCOM MMA 收集必要的相依性資料。 您可以使用[此處](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)的指導方針來啟用整合。 但請注意，在這些電腦上必須安裝相依性代理程式。


### <a name="install-the-dependency-agent"></a>安裝相依性代理程式
1. 若要在 Windows 電腦上安裝相依性代理程式，請按兩下安裝檔案，並遵循精靈的指示。
2. 若要在 Linux 電腦上安裝相依性代理程式，請使用下列命令以 root 身分安裝：

    ```sh InstallDependencyAgent-Linux64.bin```

深入了解 [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) 與 [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) 作業系統的相依性代理程式支援。

[深入了解](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)如何使用指令碼安裝 Dependency 代理程式。


## <a name="create-a-group"></a>建立群組

1. 安裝代理程式之後，請移至入口網站，然後按一下[管理] > [機器]。
2. 搜尋安裝代理程式的機器。
3. 機器的 [相依性] 資料行現在應會顯示為**檢視相依性**。 按一下資料行以檢視機器的相依性。
4. 機器的相依性對應會顯示下列詳細資料：
    - 往返機器的輸入 (用戶端) 和輸出 (伺服器) TCP 連線
        - 未安裝 MMA 和相依性代理程式的相依機器會依連接埠號碼分組
        - 已安裝 MMA 和相依性代理程式的相依機器會以不同的方塊顯示
    - 在機器內執行的處理序，您可以展開每個機器方塊，以檢視處理序
    - 每部機器的完整網域名稱、作業系統、MAC 位址等屬性，您可以按一下每個機器方塊來檢視這些詳細資料

      ![檢視電腦相依性](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. 您可以按一下時間範圍標籤中的持續時間，以查看不同持續時間的相依性。 根據預設，範圍是一小時。 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。

    > [!NOTE]
      目前，相依性視覺效果 UI 不支援選取超過一小時的時間範圍。 使用 Log Analytics 可在較長的持續期間內[查詢相依性資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#query-dependency-data-from-log-analytics)。

5. 找出您需要劃為同一群組的相依機器後，請使用 Ctrl + 按一下來選取對應中的多部機器，然後按一下 [群組機器]。
6. 指定群組名稱。 確認 Azure Migrate 已探索到相依機器。

    > [!NOTE]
    > 如果 Azure Migrate 未探索到相依機器，您就無法將它新增至群組。 若要將這類機器新增至群組，您必須使用 vCenter Server 中的正確範圍，再次執行探索程序，並確定 Azure Migrate 已探索到機器。  

7. 如果您需要建立此群組的評估，請選取核取方塊來建立新的群組評估。
8. 按一下 [確定] 以儲存群組。

一旦建立群組之後，建議您在群組的所有機器上安裝代理程式，並將整個群組的相依性視覺化來調整群組。

## <a name="query-dependency-data-from-log-analytics"></a>從 Log Analytics 查詢相依性資料

服務對應擷取的相依性資料可供在與 Azure Migrate 專案相關聯的 Log Analytics 工作區中查詢。 [深入了解](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)可在 Log Analytics 中查詢的服務對應資料表。 

若要執行 Log Analytics 查詢：

1. 安裝代理程式之後，請移至入口網站，然後按一下 [概觀]。
2. 在 [概觀] 中，移至專案的 [Essentials] 區域，然後按一下 [OMS 工作區] 旁提供的工作區名稱。
3. 在 Log Analytics 工作區頁面上，按一下 [一般]  >  [記錄]。
4. 撰寫查詢以使用 Log Analytics 蒐集相依性資料。 [此處](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)提供收集相依性資料的範例查詢。
5. 按一下 [執行] 以執行查詢。 

[深入了解](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)如何撰寫 Log Analytics 查詢。 

## <a name="next-steps"></a>後續步驟

- [深入了解](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization)相依性視覺效果的常見問題集。
- [了解如何](how-to-create-group-dependencies.md)將群組相依性視覺化來調整群組。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
