---
title: 容錯移轉群組 - Azure SQL Database | Microsoft Docs
description: 自動容錯移轉群組是 SQL Database 功能，允許您管理 SQL Database 伺服器上的一組資料庫，或受控執行個體中的所有資料庫的複寫和自動/協調容錯移轉。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 0cffb4fdff4bddc33c6938e27425035c929808b7
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301922"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>使用自動容錯移轉群組可以啟用多個資料庫透明且協調的容錯移轉

自動容錯移轉群組是 SQL Database 功能，允許您管理 SQL Database 伺服器上的一組資料庫，或受控執行個體中所有資料庫到另一個區域 (當前處於受控執行個體的公開預覽狀態) 的複寫和容錯移轉。 它會使用與[主動式異地複寫](sql-database-active-geo-replication.md)相同的基礎技術。 您可以手動起始容錯移轉，也可以根據使用者定義的原則將其委派給 SQL Database 服務。 後項可讓您在導致主要區域中完全或部分喪失 SQL Database 服務可用性的嚴重失敗或其他非計劃事件之後，自動復原次要地區中的多個相關資料庫。 此外，您可以使用可讀取次要資料庫來卸載唯讀查詢工作負載。 因為自動容錯移轉群組牽涉到多個資料庫，所以這些資料庫必須在主要伺服器上進行設定。 在容錯移轉群組中，資料庫的主要和次要伺服器都必須位於相同的訂用帳戶。 自動容錯移轉群組支援將群組中的所有資料庫都只複寫到不同區域的一部次要伺服器。

> [!NOTE]
> 在 SQL Database 伺服器上使用單一或集區資料庫時，若您希望在相同或不同區域中有多個次要資料庫，請使用[主動式異地複寫](sql-database-active-geo-replication.md)。

當您使用自動容錯移轉群組與自動容錯移轉原則時，影響群組中一或多個資料庫的任何中斷，都會導致自動容錯移轉。 此外，自動容錯移轉群組還提供在容錯移轉期間仍保持不變的讀寫和唯讀接聽程式端點。 無論您使用手動或自動啟動容錯移轉，容錯移轉都會將群組中所有次要資料庫切換到主要資料庫。 資料庫容錯移轉完成後，DNS 記錄會自動更新以將端點重新導向至新的區域。 針對特定的 RPO 和 RTO 資料，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

當您使用自動容錯移轉群組與自動容錯移轉原則時，任何影響 SQL Database 伺服器或受控執行個體中資料庫的中斷，都會導致自動容錯移轉。 您可以使用下列方法管理自動容錯移轉群組：

- [Azure 入口網站](sql-database-implement-geo-distributed-database.md)
- [PowerShell：容錯移轉群組](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [REST API：容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups)。

容錯移轉之後，請確認已在新的主要資料庫上設定伺服器和資料庫的驗證需求。 如需詳細資訊，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。

若要達到真正的業務持續性，新增資料中心之間的資料庫備援只是解決方案的一部分。 在災難性失敗後要端對端復原應用程式 (服務) 需要復原構成服務的所有元件和任何相依的服務。 這些元件的範例包括用戶端軟體 (例如自訂 JavaScript 的瀏覽器)、web 前端、儲存體和 DNS。 所有元件都必須對相同的失敗具有恢復功能，並且在應用程式的復原時間目標 (RTO) 內可供使用。 因此，您需要識別所有相依服務並了解其提供的保證與功能。 然後，您必須採取適當步驟以確保服務功能在它所依賴的服務容錯移轉期間都正常。 如需有關設計災害復原解決方案的詳細資訊，請參閱[使用主動式異地複寫設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="auto-failover-group-terminology-and-capabilities"></a>自動容錯移轉群組術語和功能

- **容錯移轉群組**

  容錯移轉群組是由單一 SQL Database 伺服器，或單一受控執行個體所管理的一組資料庫，如果由於主要區域中斷而導致所有或某些主要資料庫無法使用，則可以將這些資料庫作為容錯移轉到另一個區域的一個單位。

  - **SQL Database 伺服器**

     使用 SQL Database 伺服器，可以將單一伺服器上的部分或所有使用者資料庫放在容錯移轉群組中。 此外，SQL Database 伺服器在單一 SQL Database 伺服器上可支援多個容錯移轉群組。

  - **受控執行個體**
  
     使用受控執行個體，容錯移轉群組包含受控執行個體中的所有使用者資料庫，因此受控執行個體僅支援單一的容錯移轉群組。

- **主要**

  容錯移轉群組中裝載主要資料庫的 SQL Database 伺服器或受控執行個體。

- **次要**

  容錯移轉群組中裝載次要資料庫的 SQL Database 伺服器或受控執行個體。 次要資料庫和主要資料庫不能位於相同區域。

- **將單一資料庫新增至容錯移轉群組**

  您可以將多個在相同 SQL Database 伺服器上的單一資料庫放入相同的容錯移轉群組。 如果您在容錯移轉群組中新增單一資料庫，它會使用相同的版本自動建立次要資料庫，並在次要伺服器上計算大小。  當建立容錯移轉群組時，您會指定該伺服器。 如果您新增在次要伺服器中已經有次要資料庫的資料庫，群組就會繼承該異地複寫連結。 當您新增在伺服器中已經有次要資料庫但不屬於容錯移轉群組一部分的資料庫時，會在次要伺服器中建立新的次要資料庫。
  
> [!IMPORTANT]
  > 在受控執行個體中，將會複寫所有使用者資料庫。 您無法在容錯移轉群組中選擇要複寫的使用者資料庫子集。

- **將彈性集區中的資料庫新增到容錯移轉群組**

  您可以將彈性集區中的所有或多個資料庫放入相同的容錯移轉群組。 如果主要資料庫在彈性集區中，則會使用相同名稱在該彈性集區中建立次要資料庫 (次要集區)。 您必須確保次要伺服器包含名稱完全相同的彈性集區，且有足夠的可用容量裝載容錯移轉群組將建立的次要資料庫。 如果您在集區中新增在次要集區中已經有次要資料庫的資料庫，群組就會繼承該異地複寫連結。 當您新增的資料庫在伺服器中已經有次要資料庫且不屬於容錯移轉群組一部分時，系統會在次要集區中建立新的次要資料庫。
  
  - **容錯移轉群組讀寫接聽程式**

  形成的 DNS CNAME 記錄指向目前的主要 URL。 它可讓讀寫 SQL 應用程式在容錯移轉之後，當主要資料庫變更時毫無障礙地重新連線至主要資料庫。

  - **讀寫接聽程式的 SQL Database 伺服器 DNS CNAME 記錄**

     在 SQL Database 伺服器上，指向目前的主要 URL 的容錯移轉群組的 DNS CNAME 記錄形成為 `failover-group-name.database.windows.net`。

  - **讀寫接聽程式的受控執行個體 DNS CNAME 記錄**

     在受控執行個體上，指向目前的主要 URL 的容錯移轉群組的 DNS CNAME 記錄形成為 `failover-group-name.zone_id.database.windows.net`。

- **容錯移轉群組唯讀接聽程式**

  形成的 DNS CNAME 記錄指向唯讀接聽程式 (指向次要 URL)。 它可讓唯讀 SQL 應用程式使用指定的負載平衡規則毫無障礙地連線到次要資料庫。

  - **唯讀接聽程式的 SQL Database 伺服器 DNS CNAME 記錄**

     在 SQL Database 伺服器上，指向次要 URL 的唯讀接聽程式的 DNS CNAME 記錄形成為 `failover-group-name.secondary.database.windows.net`。

  - **唯讀接聽程式的受控執行個體 DNS CNAME 記錄**

     在受控執行個體上，指向次要 URL 的唯讀接聽程式的 DNS CNAME 記錄形成為 `failover-group-name.zone_id.database.windows.net`。

- **自動容錯移轉原則**

  容錯移轉群組預設是利用自動容錯移轉原則設定。 SQL Database 服務在偵測到失敗且寬限期已過期之後，會觸發容錯移轉。 系統必須就影響級別，藉由 [SQL Database 服務的內建高可用性基礎結構](sql-database-high-availability.md)，來驗證中斷情況不會趨緩。 如果您想要控制應用程式的容錯移轉工作流程，可以關閉自動容錯移轉。

- **唯讀的容錯移轉原則**

  根據預設，唯讀接聽程式的容錯移轉已停用。 它可確保在次要伺服器離線時不會影響主要伺服器的性能。 不過，這也表示在次要伺服器恢復之前，唯讀的工作階段將無法連線。 如果您無法容忍唯讀工作階段的停機時間，並且可以暫時將主要伺服器用於唯讀和讀寫流量，但代價是主要伺服器潛在的效能降低，則可以為唯讀接聽程式啟用容錯移轉。 在這種情況下，如果次要伺服器無法使用時，唯讀流量將自動重新導向到主要伺服器。

- **計劃性容錯移轉**

   計劃性容錯移轉會在主要資料庫和次要資料庫之間執行完整同步處理，然後將次要資料庫切換為主要角色。 這可確保不會遺失資料。 計劃性容錯移轉會用於下列案例：

  - 在資料遺失不可接受時，在生產環境中執行災害復原 (DR) 演練
  - 將資料庫重新放置到不同的區域
  - 在中斷情況趨緩 (容錯回復) 後，將資料庫傳回到主要區域。

- **非計劃性容錯移轉**

   非計劃性或強制容錯移轉會立即將次要資料庫切換為主要角色，而不會與主要資料庫進行任何同步處理。 這項作業會導致資料遺失。 非計劃性容錯移轉是主要資料庫無法存取時，用來作為中斷期間的復原方法。 當原始的主要資料庫重新上線時，它會自動重新連線而不進行同步處理，並且變為新的次要資料庫。

- **手動容錯移轉**

  無論自動容錯移轉設定為何，您都可以在任何時候手動起始容錯移轉。 如果未設定自動容錯移轉原則，就需要手動容錯移轉才能將容錯移轉群組中的資料庫復原至次要資料庫。 您可以起始強制或易用容錯移轉 (具有完整的資料同步處理)。 後者可用來將主要資料庫重新放置到次要區域。 容錯移轉完成時，DNS 記錄會自動更新以確保能連線到新的主要伺服器

- **資料遺失的寬限期**

  因為主要和次要資料庫是使用非同步複寫進行同步處理，容錯移轉可能會導致資料遺失。 您可以自訂自動容錯移轉原則，以反映應用程式對資料遺失的容錯程度。 設定 **GracePeriodWithDataLossHours**，可以控制系統在起始可能造成資料遺失的容錯移轉之前等候的時間長度。

- **多個容錯移轉群組**

  您可以為相同的兩部伺服器設定多個容錯移轉群組來控制容錯移轉的規模。 每個群組分別進行容錯移轉。 如果您的多租用戶應用程式使用彈性集區，可以使用這項功能來混合每個集區中的主要和次要資料庫。 如此一來，可以讓中斷只影響一半的租用戶。

  > [!IMPORTANT]
  > 受控執行個體不支援多個容錯移轉群組。

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>將容錯移轉群組與單一資料庫和彈性集區一起使用的最佳做法

自動容錯移轉群組必須在主要 SQL Database 伺服器上設定，並將其連接至不同 Azure 區域中的次要 SQL Database 伺服器。  群組可以包含這些伺服器中的所有或部分資料庫。 下圖說明使用多個資料庫和自動容錯移轉群組之異地備援雲端應用程式的一般設定。

![自動容錯移轉](./media/sql-database-auto-failover-group/auto-failover-group.png)

在商務持續性前提下設計服務時，請遵循這些一般指導方針：

- **使用一或多個容錯移轉群組來管理多個資料庫的容錯移轉**

  可以在不同區域 (主要和次要伺服器) 的兩部伺服器之間建立一或多個容錯移轉群組。 每個群組可包含一或多個作為復原單位的資料庫，以防所有或部分的主要資料庫因為主要區域服務中斷而變成無法使用。 容錯移轉群組會使用和主要資料庫相同的服務目標，來建立異地次要資料庫。 如果您在容錯移轉群組中新增現有的異地複寫關聯性，請確定異地次要資料庫所設定的服務層與計算大小和主要資料庫相同。

- **針對 OLTP 工作負載使用讀取寫入接聽程式**

  在執行 OLTP 作業時使用 `failover-group-name.database.windows.net` 作為伺服器 URL，連線會自動導向至主要伺服器。 在容錯移轉之後，不會變更此 URL。 請注意，容錯移轉牽涉到更新 DNS 記錄，因此只有在重新整理用戶端 DNS 快取之後，才會將用戶端連線重新導向至新的主要伺服器。

- **針對唯讀工作負載使用唯讀接聽程式**

  如果您有容忍某些過時資料的邏輯隔離唯讀工作負載，則可以使用應用程式中的次要資料庫。 針對唯讀工作階段，請使用 `failover-group-name.secondary.database.windows.net` 作為伺服器 URL，連線會自動導向至次要伺服器。 也建議您使用 **ApplicationIntent=ReadOnly**，在連接字串中表示讀取意圖。

- **對效能降低做好心理準備**

  應用程式的其餘部分或所使用的其他服務並不會影響 SQL 的容錯移轉決策。 應用程式可能會「混用」某個區域的某些元件和另一個區域的某些元件。 若要避免效能降低，請務必要在 DR 區域中部署備援應用程式，並遵循[網路安全性指導方針](#failover-groups-and-network-security)。

  > [!NOTE]
  > DR 區域中的應用程式不一定非得使用不同的連接字串。  

- **對資料遺失做好心理準備**

  如果偵測到發生中斷，它會等候您透過 **GracePeriodWithDataLossHours** 所指定的這段時間。 預設值為 1 小時。 如果您無法承擔資料遺失情況，請務必將 **GracePeriodWithDataLossHours** 設定為足夠大的數字，例如 24 小時。 使用手動群組容錯移轉，從次要伺服器容錯回復到主要伺服器。

> [!IMPORTANT]
> 具有 800 個或更少 DTU 且使用異地複寫的資料庫超過 250 個的彈性集區，可能會遇到的問題包括規劃的容錯移轉時間較久與效能降低。  當地理複寫端點依地理位置廣泛相隔，或當每個資料庫使用多個次要端點時，較可能會發生這些寫入大量工作負載的問題。  異地複寫延遲隨時間而增加時，就可看出這些問題的徵兆。  您可以使用 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 來監視這個延遲。  如果發生這些問題，則風險降低方式包括增加集區 DTU 數目，或減少相同集區內異地複寫的資料庫數目。

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>使用容錯移轉叢集搭配受控執行個體的最佳做法

自動容錯移轉群組必須在主要執行個體上設定，並將其連接至不同 Azure 區域中的次要執行個體。  執行個體中的所有資料庫都將複寫至次要執行個體。 下圖說明使用受控執行個體和自動容錯移轉群組之異地備援雲端應用程式的一般設定。

![自動容錯移轉](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!IMPORTANT]
> 受控執行個體的自動容錯移轉群組處於公開預覽狀態。

如果您的應用程式使用受控執行個體做為資料層，請在設計業務持續性時遵循下列一般指導方針：

- **在與主要執行個體相同的 DNS 區域中建立次要執行個體**

  建立的新執行個體時，會自動產生唯一識別碼作為 DNS 區域，並包含在執行個體的 DNS 名稱中。 針對此執行個體的多網域 (SAN) 憑證佈建了 `zone_id.database.windows.net` 形式的 SAN 欄位。 此憑證可用來驗證與相同的 DNS 區域中的執行個體的用戶端連線。 若要確保容錯移轉之後與主要執行個體的連線不中斷，主要和次要執行個體必須位於相同的 DNS 區域。 當您的應用程式準備好進行生產環境部署時，請在不同區域中建立次要執行個體，並確保它與主要執行個體共用 DNS 區域。 這是透過使用 Azure 入口網站、PowerShell 或 REST API 指定 `DNS Zone Partner` 選擇性參數來完成的。

  如需在與主要執行個體相同的 DNS 區域中建立次要執行個體的詳細資訊，請參閱[使用受控執行個體管理容錯移轉群組 (預覽)](#powershell-managing-failover-groups-with-managed-instances-preview)。

- **在兩個執行個體之間啟用複寫流量**

  因為每個執行個體都在其自己的 VNet中隔離，因此必須允許這些 Vnet 之間的雙向流量。 請參閱 [Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **設定容錯移轉群組來管理整個執行個體的容錯移轉**

  容錯移轉群組將會管理執行個體中所有資料庫的容錯移轉。 建立群組時，執行個體中的每個資料庫將會自動異地複寫到次要執行個體。 您無法使用容錯移轉群組來起始資料庫子集的部分容錯移轉。

  > [!IMPORTANT]
  > 如果從主要執行個體中移除資料庫，則它也將自動在異地次要執行個體上卸除。

- **針對 OLTP 工作負載使用讀取寫入接聽程式**

  在執行 OLTP 作業時使用 `failover-group-name.zone_id.database.windows.net` 作為伺服器 URL，連線會自動導向至主要伺服器。 在容錯移轉之後，不會變更此 URL。 容錯移轉牽涉到更新 DNS 記錄，因此只有在重新整理用戶端 DNS 快取之後，才會將用戶端連線重新導向至新的主要伺服器。 因為次要執行個體與主要執行個體共用 DNS 區域，因此用戶端應用程式將能夠使用相同的 SAN 憑證重新連線到該區域。

- **直接連接到異地複寫的次要執行個體以進行唯讀查詢**

  如果您有容忍某些過時資料的邏輯隔離唯讀工作負載，則可以使用應用程式中的次要資料庫。 若要直接連接到異地複寫的次要執行個體，請使用 `server.secondary.zone_id.database.windows.net` 作為伺服器 URL，並直接連接到異地複寫的次要執行個體。

  > [!NOTE]
  > 在特定的服務層次，Azure SQL Database 支援使用[唯讀複本](sql-database-read-scale-out.md)來使用一個唯讀複本的容量，並使用連接字串中的 `ApplicationIntent=ReadOnly` 參數，對唯讀查詢工作負載進行負載平衡。 當您已設定異地複寫的次要執行個體時，可以使用此功能連接至主要位置或異地複寫位置中的唯讀複本。
  > - 若要連線至主要位置的唯讀複本，請使用 `failover-group-name.zone_id.database.windows.net`。
  > - 若要連線至主要位置的唯讀複本，請使用 `failover-group-name.secondary.zone_id.database.windows.net`。

- **對效能降低做好心理準備**

  應用程式的其餘部分或所使用的其他服務並不會影響 SQL 的容錯移轉決策。 應用程式可能會「混用」某個區域的某些元件和另一個區域的某些元件。 若要避免效能降低，請務必要在 DR 區域中部署備援應用程式，並遵循[網路安全性指導方針](#failover-groups-and-network-security)。

- **對資料遺失做好心理準備**

  如果系統偵測到服務中斷，SQL 會在就我們所知並無資料遺失時，自動觸發讀寫容錯移轉。 否則，它會等候您透過 `GracePeriodWithDataLossHours` 所指定的這段時間。 如果您指定 `GracePeriodWithDataLossHours`，請針對資料遺失做好準備。 服務中斷期間，Azure 一般會傾向維持可用性。 如果您無法承擔資料遺失情況，請務必將 GracePeriodWithDataLossHours 設定為足夠大的數字，例如 24 小時。

  在起始容錯移轉之後，將立即發生讀寫接聽程式的 DNS 更新。 這項作業不會導致資料遺失。 但是，在正常情況下，切換資料庫角色的程序最多可能需要 5 分鐘的時間。 在完成之前，新的主要執行個體中的某些資料庫仍會處於唯讀模式。 如果使用 PowerShell 起始容錯移轉，則整個作業是同步的。 如果使用 Azure 入口網站來起始，則 UI 將指示完成狀態。 如果使用 REST API 來起始，請使用標準 Azure Resource Manager 的輪詢機制來監視完成情況。

  > [!IMPORTANT]
  > 使用手動群組容錯移轉，將主要資料庫移回原始位置。 當造成容錯移轉的中斷情況趨緩時，您可以將主要資料庫移動到原始位置。 若要這樣做，您應該起始群組的手動容錯移轉。

## <a name="failover-groups-and-network-security"></a>容錯移轉群組和網路安全性

對於某些應用程式，安全性規則會要求對資料層的網路存取需限制為特定元件，例如 VM、Web 服務等等。這項需求會出現一些商務持續性設計、以及使用容錯移轉群組的挑戰。 實作這類限制的存取權時，您應該考慮下列選項。

### <a name="using-failover-groups-and-virtual-network-rules"></a>使用容錯移轉群組和虛擬網路規則

如果您使用[虛擬網路服務端點和規則](sql-database-vnet-service-endpoint-rule-overview.md)來限制存取您的 SQL 資料庫，請留意，每個虛擬網路服務端點只適用於一個 Azure 區域。 端點無法讓其他區域接受來自子網路的通訊。 因此，只有部署到相同區域中的用戶端應用程式可以連線到主要資料庫。 因為容錯移轉會導致 SQL 用戶端工作階段重新路由至不同 (次要) 區域中的伺服器，所以如果這些工作階段是來自該區域外的用戶端，就會失敗。 基於這個理由，如果參與的伺服器包含在虛擬網路規則中，則無法啟用自動容錯移轉原則。 若要支援手動容錯移轉，請遵循下列步驟：

1. 在次要地區中佈建您應用程式 (Web 服務、虛擬機器等) 前端元件的備援副本
2. 分別設定主要和次要伺服器的[虛擬網路規則](sql-database-vnet-service-endpoint-rule-overview.md)
3. 啟用[使用流量管理員設定的前端容錯移轉](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. 偵測到中斷情況時，起始手動容錯移轉。 這個選項最適合用於在前端和資料層之間需要一致延遲的應用程式，且支援當前端、資料層或兩者受到中斷影響時進行復原。

> [!NOTE]
> 如果您使用**唯讀接聽程式**對唯讀工作負載進行負載平衡，請確保此工作負載會在 VM 或是次要地區的其他資源中執行，使其得以連線到次要資料庫。

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>使用容錯移轉群組和 SQL 資料庫防火牆規則

如果您的商務持續性計劃需要使用具有自動容錯移轉的群組，可以使用傳統的防火牆規則，來限制對您 SQL 資料庫的存取。  若要支援自動容錯移轉，請遵循下列步驟：

1. [建立公用 IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [建立公用負載平衡器](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer)並為其指派公用 IP。
3. [建立虛擬網路和虛擬機器](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers)以用於前端元件
4. [建立網路安全性群組](../virtual-network/security-overview.md)並設定輸入連線。
5. 請使用 'Sql' [服務標籤](../virtual-network/security-overview.md#service-tags)確定會開啟到 Azure SQL 資料庫的輸出連線。
6. 建立 [SQL 資料庫防火牆規則](sql-database-firewall-configure.md)以允許您在步驟 1 中所建立公用 IP 位址的輸入流量。

如需如何設定輸出存取，以及在防火牆規則中使用哪些 IP 的相關詳細資訊，請參閱[負載平衡器連出連線](../load-balancer/load-balancer-outbound-connections.md)。

上述組態可確保自動容錯移轉不會封鎖來自前端元件的連線，並假設應用程式可以容忍前端與資料層之間較長的延遲。

> [!IMPORTANT]
> 若要保證區域性中斷時的商務持續性，您必須確定前端元件與資料庫的異地備援。

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>在受控執行個體與其 Vnet 之間啟用異地複寫

在兩個不同區域中的主要和次要受控執行個體之間設定容錯移轉群組時，每個執行個體都使用獨立的 VNet 隔離。 若要允許這些 Vnet 之間的複寫流量，請確保符合這些必要條件：

1. 兩個受控執行個體必須位於不同的 Azure 區域中。
2. 您的次要受控執行個體必須是空的 (沒有使用者資料庫)。
3. 主要和次要受控執行個體必須位於相同的資源群組中。
4. 受控執行個體所屬的 VNet 需要透過 [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)進行連線。 目前不支援轉移的全域 VNet 對等互連。
5. 兩個受控執行個體 Vnet 不能有重疊的 IP 位址。
6. 您需要設定網路安全性群組 (NSG)，以便連接埠 5022 和範圍 11000 ~ 12000 開啟來自其他受控執行個體子網路的輸入和輸出連線。 這是為了允許執行個體之間的複寫流量

    > [!IMPORTANT]
    > 設定錯誤的 NSG 安全性規則會導致資料庫複製作業停滯。

7. 您必須在次要執行個體上設定 DNS 區域夥伴。 DNS 區域是受控執行個體的屬性。 它表示接在受控執行個體名稱之後，並位於 `.database.windows.net` 前置詞之前的部分主機名稱。 在每個 VNet 中建立第一個受控執行個體期間，它將作為隨機字串產生。 建立受控執行個體後無法修改 DNS 區域，且相同子網路內的所有受控執行個體會共用相同的 DNS 區域值。 針對受控執行個體容錯移轉群組設定，主要受控執行個體和次要受控執行個體必須共用相同的 DNS 區域值。 您可以透過在建立次要受控執行個體時，指定 DnsZonePartner 參數來完成此操作。 DNS 區域夥伴屬性會定義要與之共用執行個體容錯移轉群組的受控執行個體。 透過將另一個受控執行個體的資源識別碼作為 DnsZonePartner 的輸入傳遞，目前正在建立的受控執行個體將繼承協力廠商受控執行個體的相同 DNS 區域值。

## <a name="upgrading-or-downgrading-a-primary-database"></a>升級或降級主要資料庫

您可以將主要資料庫升級或降級至不同的計算大小 (在相同的服務層內，而不是一般用途和業務關鍵之間)，而不需要將任何次要資料庫中斷連線。 升級時，我們建議您先升級次要資料庫，然後再升級主要資料庫。 降級時，順序相反︰先降級主要資料庫，然後再降級次要資料庫。 當您將資料庫升級或降級到不同的服務層時，會強制執行這項建議。

> [!NOTE]
> 如果您已在容錯移轉群組設定中建立次要資料庫，則不建議降級次要資料庫。 這是為了確保您的資料層在容錯移轉啟動之後有足夠的容量來處理一般工作負載。

## <a name="preventing-the-loss-of-critical-data"></a>防止重要資料遺失

由於廣域網路的高度延遲，連續複製採用非同步複寫機制。 如果發生失敗，非同步複寫導致部分資料遺失是無法避免的。 不過，有些應用程式可能會要求資料不能遺失。 若要保護這些重大更新，應用程式開發人員可以在認可交易後立即呼叫 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系統程序。 呼叫 **sp_wait_for_database_copy_sync** 會封鎖呼叫執行緒，直到最後認可的交易傳輸到次要資料庫。 不過，它不會等候在次要資料庫上重新執行和認可傳輸的交易。 **sp_wait_for_database_copy_sync** 以特定的連續複製連結為範圍。 任何具備主要資料庫連接權限的使用者都可以呼叫此程序。

> [!NOTE]
> **sp_wait_for_database_copy_sync** 可避免在容錯移轉之後資料遺失，但是不保證讀取權限會完整同步。 **sp_wait_for_database_copy_sync** 程序呼叫所造成的延遲可能會相當明顯，且取決於呼叫時的交易記錄大小。

## <a name="failover-groups-and-point-in-time-restore"></a>容錯移轉群組和時間點還原

如需使用容錯移轉群組中的時間點還原的相關資訊，請參閱[時間點復原 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="programmatically-managing-failover-groups"></a>以程式設計方式管理容錯移轉群組

如前所述，自動容錯移轉群組和主動式異地複寫也可以使用 Azure PowerShell 和 REST API，以程式設計的方式管理。 下表描述可用的命令集。 主動式異地複寫包含一組可管理的 Azure Resource Manager API，包括 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/azure/overview)。 這些 API 需要使用資源群組，並支援以角色為基礎的安全性 (RBAC)。 如需如何實作存取角色的詳細資訊，請參閱 [Azure 角色型存取控制](../role-based-access-control/overview.md)。

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell：使用單一資料庫與彈性集區管理 SQL 資料庫容錯移轉

| Cmdlet | 說明 |
| --- | --- |
| [New-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [Remove-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | 從伺服器移除容錯移轉群組，並刪除包含群組的所有次要資料庫 |
| [Get-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | 擷取容錯移轉群組設定 |
| [Set-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |修改容錯移轉群組的設定 |
| [Switch-AzureRMSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | 觸發容錯移轉群組的容錯移轉到次要伺服器 |
| [Add-AzureRmSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/add-azurermsqldatabasetofailovergroup)|將一或多個資料庫新增至 Azure SQL Database 容錯移轉群組|
|  | |

> [!IMPORTANT]
> 如需範例指令碼，請參閱[設定單一資料庫的容錯移轉群組並進行容錯移轉](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)。
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell：使用受控執行個體管理容錯移轉叢集 (預覽)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>安裝 Powershell 的最新發行前版本

1. 將 PowerShellGet 模組更新為 1.6.5 (或最新預覽版本)。 請參閱 [PowerShel Preview 網站](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview)。

   ```Powershell
      install-module PowerShellGet -MinimumVersion 1.6.5 -force
   ```

2. 在新的 PowerShell 視窗中，執行下列命令：

   ```Powershell
      import-module PowerShellGet
      get-module PowerShellGet #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>Powershell 指令程式，用於建立執行個體容錯移轉群組

| API | 說明 |
| --- | --- |
| New-AzureRmSqlDatabaseInstanceFailoverGroup |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |修改容錯移轉群組的設定|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |擷取容錯移轉群組設定|
| Switch-AzureRmSqlDatabaseInstanceFailoverGroup |觸發容錯移轉群組的容錯移轉到次要伺服器|
| Remove-AzureRmSqlDatabaseInstanceFailoverGroup | 移除容錯移轉群組|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API：使用單一和集區資料庫管理 SQL Database 容錯移轉群組

| API | 說明 |
| --- | --- |
| [建立或更新容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 建立或更新容錯移轉群組 |
| [刪除容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 從伺服器中移除容錯移轉群組 |
| [容錯移轉 (計劃性)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 從目前主要伺服器容錯移轉到此伺服器。 |
| [強制容錯移轉允許資料遺失](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |從目前主要伺服器容錯移轉到此伺服器。 這項作業可能會導致資料遺失。 |
| [取得容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 取得容錯移轉群組。 |
| [依伺服器列出容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 列出伺服器中的容錯移轉群組。 |
| [更新容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 更新容錯移轉群組。 |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>REST API：使用受控執行個體管理容錯移轉群組 (預覽)

| API | 說明 |
| --- | --- |
| [建立或更新容錯移轉群組](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | 建立或更新容錯移轉群組 |
| [刪除容錯移轉群組](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | 從伺服器中移除容錯移轉群組 |
| [容錯移轉 (計劃性)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | 從目前主要伺服器容錯移轉到此伺服器。 |
| [強制容錯移轉允許資料遺失](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |從目前主要伺服器容錯移轉到此伺服器。 這項作業可能會導致資料遺失。 |
| [取得容錯移轉群組](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | 取得容錯移轉群組。 |
| [列出容錯移轉群組 - 依位置列出](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | 列出位置中的容錯移轉群組。 |

## <a name="next-steps"></a>後續步驟

- 如需範例指令碼，請參閱：
  - [使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [設定單一資料庫的容錯移轉群組並進行容錯移轉](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
- 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
- 若要深入了解新的主要伺服器和資料庫的驗證需求，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。
