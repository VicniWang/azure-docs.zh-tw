---
title: 常見問題集 - Avere vFXT for Azure
description: 關於 Avere vFXT for Azure 的常見問題集
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: dbd9eaf531dcad230c23d1b41110036102faf3df
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652659"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT for Azure 常見問題集

本文中回答的問題可協助您判斷 Avere vFXT for Azure 是否適合您的需求。 文中提供 Avere vFXT 的基本資訊，並說明如何搭配其他 Azure 元件和外部廠商的產品使用。 

## <a name="general"></a>一般 

### <a name="what-is-avere-vfxt-for-azure"></a>什麼是 Avere vFXT for Azure？

Avere vFXT for Azure 是高效能的檔案系統，可快取 Azure 計算中的作用中資料，以有效處理關鍵工作負載。

### <a name="is-avere-vfxt-a-storage-solution"></a>Avere vFXT 是儲存體解決方案嗎？

沒有。 Avere vFXT 是連接至儲存體環境的檔案系統「快取」，例如 EMC 或 NetApp NAS 或 Azure Blob 容器。 Avere vFXT 可簡化用戶端的資料要求，並快取提供的資料，長時間大規模地改善效能。 Avere vFXT 本身不會儲存資料。 它沒有背後所儲存資料量的相關資訊。

### <a name="is-avere-vfxt-a-tiering-solution"></a>Avere vFXT 是階層處理解決方案嗎？

Avere vFXT 不會自動處理經常性儲存層與非經常性儲存層之間的階層資料。  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>如何得知環境是否適合使用 Avere vFXT？

這個問題的最佳判別方式是，自問「可快取工作負載嗎？」 也就是工作負載是否有高讀寫比例？ 例如 80/20 或 70/30 的讀取/寫入比例。

如果以檔案為基礎的分析管線在大量 Azure 虛擬機器執行，並符合下列一或多項條件，就可以考慮 Avere vFXT for Azure：

* 因為檔案存取時間很長 (達數十毫秒或秒，視需求而定)，使得整體效能很慢或不一致。 客戶無法接受此延遲。

* 需要處理的資料位於 WAN 環境的遠端，而永久移動該資料並不切實際。 該資料可能位於不同的 Azure 區域或客戶資料中心。

* 大量用戶端都在要求資料，例如在高效能運算 (HPC) 叢集中。 龐大的並行要求數會增加延遲。

* 客戶想要在 Azure 虛擬機器中依「現況」執行目前的管線，且需要 POSIX 型共用儲存體 (或快取) 解決方案以獲得延展性。 使用 Avere vFXT for Azure，您就不必重新架構工作管線來對 Azure Blob 儲存體進行原生呼叫。

* HPC 應用程式以 NFSv3 用戶端為基礎。 (在某些情況下，可以使用 SMB 2.1 用戶端，但效能會受到限制。)

下圖精簡說明此問題的解答。 您的工作流程越接近右上方，Avere 快取解決方案就越可能適用於您的環境。

![圖表顯示有成千上萬的用戶端大量讀取負載，會比較適合使用 Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>哪種用戶端規模最適合使用 Avere vFXT 解決方案？

Avere vFXT 快取解決方案的建置可處理數百、數千或成千上萬個計算核心。 如果您的幾個機器都在執行輕鬆的工作，就不適合 Avere vFXT 解決方案。

一般 Avere vFXT 客戶執行的工作負載需求約從 1,000 個 CPU 核心起。 這些環境的核心數目可達 50,000 個或以上。 隨著這些工作負載成長，會要求更多的輸送量或更多的 IOPS，而 Avere vFXT 可以調整，因此您可以新增節點以提供支援。

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Avere vFXT 環境可以儲存多少資料？

Avere vFXT 算是一種快取， 因此不會特別儲存資料。 而是結合使用 RAM 與 SSD 來儲存快取的資料。 資料會永久儲存在後端儲存體系統 (例如，NetApp NAS 系統或 Blob 容器)。 Avere vFXT 系統並沒有背後所儲存資料量的相關資訊。 Avere vFXT 只會快取用戶端所要求資料的子集。  

### <a name="what-regions-are-supported"></a>支援哪些區域？

自 2018 年 11 月 1 日起，除了主權區域 (中國、德國) 地區和政府區域，所有區域都支援 Avere vFXT for Azure。 請確定您所要使用的區域可支援數量龐大的計算核心，以及建立 Avere vFXT 叢集所需的 VM 執行個體。

### <a name="how-do-i-get-help-with-avere-vfxt"></a>如何取得 Avere vFXT 的相關協助？

專門支援小組會提供 Avere vFXT for Azure 的相關協助。 請依照[取得有關系統的協助](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)中的指示，從 Azure 入口網站開啟支援票證。 

### <a name="is-avere-vfxt-highly-available"></a>Avere vFXT 可用性高嗎？

高，Avere vFXT 可當作專屬 HA 解決方案來執行。

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Avere vFXT for Azure 是否也支援其他雲端服務？

是，客戶可使用一個以上的雲端提供者搭配 Avere vFXT 叢集。 它支援 AWS S3 標準貯體、Google 雲端服務標準貯體和 Azure Blob 容器。 

> [!NOTE] 
> 在 AWS 或 Google 雲端中使用 Avere vFXT 會收取軟體費用，但搭配 Azure 則否。

## <a name="technical-compute"></a>技術：計算

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>您能夠描述 Avere vFXT 環境的「面貌」嗎？

Avere vFXT 是由多個 Azure 虛擬機器構成的叢集設備。 Python 程式庫可處理叢集建立、刪除及修改。 請閱讀[什麼是 Avere vFXT for Azure？](avere-vfxt-overview.md)以深入了解。 

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Avere vFXT 可在哪種 Azure 虛擬機器上執行？  

Avere vFXT for Azure 叢集使用 Microsoft Azure E32s_v3 或 D16s_v3 虛擬機器。 

### <a name="can-i-mix-and-match-virtual-machine-types-for-my-cluster"></a>我能混搭叢集的虛擬機器類型嗎？

否，您必須從虛擬機器類型之中擇一使用。
    
### <a name="can-i-move-between-virtual-machine-types"></a>我可以改用不同的虛擬機器類型嗎？

是，有從一種 VM 類型改用其他類型的移轉路徑。 請[開啟支援票證](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)以了解如何進行。

### <a name="does-the-avere-vfxt-environment-scale"></a>Avere vFXT 環境規模可以調整嗎？

Avere vFXT 叢集可以小至三個虛擬機器，或大至 24 個節點。 如果您認為叢集需要九個以上的節點，請連絡 Azure 支援小組來協助規劃。 節點數目越多，需要的部署架構越大。

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Avere vFXT 環境可以自動調整規模嗎？

沒有。 您可以上下調整叢集大小，但新增或移除叢集節點的步驟須手動進行。

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Avere vFXT 叢集可以執行為虛擬機器擴展集嗎？

Avere vFXT 不支援部署虛擬機器擴展集。 數個內建的可用性支援機制是專為參與叢集的不可部分完成 VM 而設計。  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>可以在低優先順序的 VM 上執行 Avere vFXT 叢集嗎？

否，系統要求基礎的虛擬機器擴展集。

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>可以在容器中執行 Avere vFXT 叢集嗎？

不可以，Avere vFXT 必須部署為獨立的應用程式。

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Avere vFXT VM 計數會佔用我的計算配額嗎？

是。 請確定要支援叢集的區域中有足夠的配額。  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>可以在不同的可用性區域中執行 Avere vFXT 叢集機器嗎？

沒有。 Avere vFXT 中之高可用性模型目前不支援位於不同可用性區域中的個別 Avere vFXT 叢集成員。

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>可以複製 Avere vFXT 虛擬機器嗎？

否，您必須使用支援的 Python 指令碼，在 Avere vFXT 叢集中新增或移除節點。 如需 的詳細資訊，請閱讀[管理 Avere vFXT 叢集](avere-vfxt-manage-cluster.md)。  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>是否有 "VM" 版的軟體可在我自己的本機環境中執行？

否，提供的系統就是叢集設備，並已在特定的虛擬機器類型上經過測試。 這項限制可協助客戶，避免其所建立系統不支援一般 Avere vFXT 工作流程的高效能需求。 

## <a name="technical-disks"></a>技術：磁碟

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Azure VM 支援哪種磁碟？

Avere vFXT for Azure 可使用 1 TB 或 4 TB 進階 SSD 組態。 進階 SSD 組態能部署為多個受控磁碟。

### <a name="does-the-cluster-support-unmanaged-disks"></a>叢集是否支援非受控磁碟？

否，叢集需要受控磁碟。

### <a name="does-the-system-support-local-attached-ssds"></a>系統是否支援本機 (連接的) SSD？

Avere vFXT for Azure 目前不支援本機 SSD。 用於 Avere vFXT 的磁碟必須要能關閉和重新啟動，但此設定中的本機連接 SSD 只能終止。

### <a name="does-the-system-support-ultra-ssds"></a>系統是否支援 Ultra SSD？

否，系統只支援進階 SSD 組態。

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>可以卸離進階 SSD，稍後再重新連接以保留切換使用之間的快取內容嗎？

不支援卸離後重新連接 SSD。 在切換使用之間，來源上的中繼資料或檔案內容可能已經變更，這樣做可能會造成資料完整性問題。

### <a name="does-the-system-encrypt-the-cache"></a>系統會將快取加密嗎？

資料會等量分散在磁碟，但不會加密。 不過，您可以將磁碟本身加密。 如需詳細資訊，請參閱[在 Azure 中的虛擬機器上保護及使用原則](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption)。

## <a name="technical-networking"></a>技術：網路功能

### <a name="what-network-is-recommended"></a>建議使用哪種網路？

如果使用內部部署儲存體搭配 Avere vFXT，您使用的網路連線應為 1 Gbps 或更快。 如果您有少量的資料，並願意在執行作業之前，將資料複製到雲端，VPN 連線可能夠快。 

> [!TIP] 
> 網路連結越慢，初始的冷讀取速度會愈慢。 讀取速度緩慢會增加工作管線的延遲情況。 

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>可以在計算叢集以外的虛擬網路中執行 Avere vFXT 嗎？

是，您可以在不同的虛擬網路中建立 Avere vFXT 系統。 如需詳細資訊，請閱讀[規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md)。

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Avere vFXT 需要自己的子網路嗎？

是。 Avere vFXT 必須執行為 HA 叢集，且需要多個 IP 位址才能運作。 如果叢集位於自己的子網路中，您要避免 IP 位址發生衝突，這可能會造成安裝和一般操作發生問題。 只要 IP 位址沒有重疊，叢集的子網路可以位於現有的虛擬網路內。

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>可以在 InfiniBand 上執行 Avere vFXT 嗎？

不可以，Avere vFXT 只能使用乙太網路/IP。

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>如何從 Avere vFXT 存取我的內部部署 NAS 環境？

Avere vFXT 環境類似任何其他的 Azure VM，它需要透過網路閘道或 VPN 路由存取客戶資料中心 (反之亦然)。 如果 Azure ExpressRoute 連線可用於您的環境，請考慮使用。

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Avere vFXT 有哪些頻寬需求？

整體頻寬需求取決於兩個因素： 

* 向來源要求的資料數量 
* 用戶端系統在初始資料載入期間對延遲的容限程度  

對於在意延遲的環境，您應該使用光纖解決方案與至少 1 Gbps 的連結速度。 如果有的話，請使用 ExpressRoute。  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>可以使用公用 IP 位址執行 Avere vFXT 嗎？

不可以，用來運作 Avere vFXT 的網路環境應該要透過最佳做法加以保護。  

## <a name="technical-back-end-storage-core-filers"></a>技術：後端儲存體 (核心檔案管理工具)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>單一 Avere vFXT 環境可支援多少核心檔案管理工具？

Avere vFXT 叢集支援最多 20 個核心檔案管理工具。 

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Avere vFXT 環境如何儲存資料？

Avere vFXT 不是儲存體。 它是快取，會從稱為核心檔案管理工具的多個儲存體目標讀取和寫入資料。 Avere vFXT 進階 SSD 磁碟上儲存的資料為暫時性，最後都會排清到後端核心檔案管理工具儲存體。

### <a name="which-core-filers-does-avere-vfxt-support"></a>Avere vFXT 支援哪些核心檔案管理工具？

一般而言，Avere vFXT for Azure 支援下列系統作為核心檔案管理工具： 

* Dell EMC Isilon (OneFS 7.1、7.2、8.0 及 8.1) 
* NetApp ONTAP (Clustered Mode 9.4、9.3、9.2、9.1P1、8.0-8.3) 與 (7-Mode 7.*、8.0-8.3) 
* Azure Blob 容器 (僅限本地備援儲存體) 
* AWS S3 貯體 
* Google 雲端貯體

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Avere vFXT 為什麼不支援所有 NFS 檔案管理工具？

雖然所有 NFS 平台都符合相同的 IETF 標準，但在實務上，每個實作都有自己的行為模式。 這些細項會影響 Avere vFXT 與儲存體系統之間的互動方式。 市面上最廣泛使用的平台都是支援的系統。

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Avere vFXT 是否支援私用物件儲存體 (例如 SwiftStack)？

Avere vFXT 不支援私用物件儲存體。

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>如何取得受支援的特定儲存體產品？

支援根據現場的需求量而定。 如果要求支援 NAS 解決方案可產生足夠的收益，就會列入考慮。 請透過 Azure 支援小組提出要求。

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>可以使用 Azure Blob 儲存體作為核心檔案管理工具嗎？

是，Avere vFXT for Azure 可以使用區塊 Blob 容器作為雲端核心檔案管理工具。  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Blob 核心檔案管理工具有哪些儲存體帳戶需求？

您的儲存體帳戶必須是一般用途 v2 (GPv2) 帳戶，並設定為只使用本地備援儲存體。 不支援異地備援儲存體和區域備援儲存體。

### <a name="can-i-use-archive-blob-storage"></a>可以使用封存 Blob 儲存體嗎？

沒有。 封存儲存體的服務等級協定 (SLA) 與 Avere vFXT 系統的即時目錄和檔案存取需求不相容。 

### <a name="can-i-use-cool-blob-storage"></a>可以使用非經常性儲存層的 Blob 儲存體嗎？

您可以使用非經常性儲存層，但請注意，作業率會高出許多。 

### <a name="how-do-i-encrypt-the-blob-container"></a>如何加密 Blob 容器？

您可以在 Azure (慣用) 或 Avere vFXT 核心檔案管理工具層級中設定 Blob 加密。  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>可以對 Blob 核心檔案管理工具使用我自己的加密金鑰嗎？

根據預設，資料是透過適用於 Azure Blob、表格和佇列儲存體加上 Azure 檔案儲存體的 Microsoft 受控金鑰來加密。 您可以攜帶自己的金鑰來加密 Blob 儲存體和 Azure 檔案儲存體。 如果您選擇使用 Avere vFXT 加密，就必須使用 Avere 產生的金鑰並將它儲存在本機。 

## <a name="purchasing"></a>購買

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>如何取得 Avere vFXT for Azure 授權？

透過 Azure Marketplace 即可輕鬆取得 Avere vFXT for Azure 授權。 註冊 Azure 帳戶，然後遵循[部署 Avere vFXT 叢集](avere-vfxt-deploy.md)中的指示來建立 Avere vFXT 叢集。 

### <a name="how-much-does-avere-vfxt-cost"></a>Avere vFXT 的費用是多少？

在 Azure 中使用 Avere vFXT 叢集，不會產生任何額外的授權費用。 客戶須承擔儲存體和其他 Azure 使用量費用。

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>能以低優先順序執行 Avere vFXT VM 嗎？

否，Avere vFXT 叢集需要「永遠開啟」服務。 不需要時，可以關閉叢集。 

## <a name="next-steps"></a>後續步驟

若要開始使用 Avere vFXT for Azure，請閱讀以下文章，了解如何規劃和部署自己的系統：

* [規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md)
* [部署概觀](avere-vfxt-deploy-overview.md)
* [準備建立 Avere vFXT 叢集](avere-vfxt-prereqs.md)
* [部署 Avere vFXT 叢集](avere-vfxt-deploy.md)

若要深入了解 Avere vFXT 的功能與使用案例，請參閱 [Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/)。
