---
title: Azure SQL Database 安全性概觀 | Microsoft Docs
description: 了解 Azure SQL Database 和 SQL Server 的 安全性，包含雲端和 SQL Server 內部部署之間的差異。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 1a21ab5af8c0dc5fbbe2c1dbab8931bd903df4ff
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235983"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL Database 安全性功能的概觀

本文概述使用 Azure SQL Database 保護應用程式資料層的基本概念。 所述的安全性策略會遵循下圖所示的多層式深度防禦方法，並從外部移入：

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>網路安全性

Microsoft Azure SQL Database 為雲端和企業應用程式提供關聯式資料庫服務。 為了協助保護客戶資料，防火牆會阻止資料庫伺服器的網路存取，直到根據 IP 位址或 Azure 虛擬網路流量來源明確授與存取權。

### <a name="ip-firewall-rules"></a>IP 防火牆規則

IP 防火牆規則會根據每個要求的來源 IP 位址授與資料庫存取權。 如需詳細資訊，請參閱 [Azure SQL Database 和 SQL 資料倉儲防火牆規則概觀](sql-database-firewall-configure.md)。

### <a name="virtual-network-firewall-rules"></a>虛擬網路防火牆規則

[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)會透過 Azure 骨幹擴充您的虛擬網路連線能力，並且讓 Azure SQL Database 找出流量源自的虛擬網路子網路。 若要允許流量觸達 Azure SQL Database，請使用 SQL[服務標籤](../virtual-network/security-overview.md)，允許透過網路安全性群組輸出流量。

[虛擬網路規則](sql-database-vnet-service-endpoint-rule-overview.md)可讓 Azure SQL Database 只接受虛擬網路內所選子網路傳來的通訊。

> [!NOTE]
> 控制防火牆規則的存取「不」適用於**受控執行個體**。 如需所需網路組態的詳細資訊，請參閱[連線到受控執行個體](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>存取管理

> [!IMPORTANT]
> 在 Azure 內管理資料庫和資料庫伺服器，是由入口網站使用者帳戶的角色指派所控制。 如需有關此文章的詳細資訊，請參閱 [Azure 入口網站中的角色型存取控制](../role-based-access-control/overview.md)。

### <a name="authentication"></a>Authentication

驗證是證明使用者宣告身分的程序。 Azure SQL Database 支援兩種驗證類型：

- **SQL 驗證**：

    SQL 資料庫驗證是指使用者使用使用者名稱和密碼連線到 [Azure SQL Database](sql-database-technical-overview.md) 時的驗證。 在資料庫的資料庫伺服器建立期間，必須指定採取使用者名稱和密碼的「伺服器管理員」登入。 使用這些認證時，「伺服器管理員」能夠以資料庫擁有者身分向該資料庫伺服器上的任何資料庫進行驗證。 在那之後，伺服器管理員可以建立其他 SQL 登入和使用者，可讓使用者使用使用者名稱和密碼進行連線。

- **Azure Active Directory 驗證**：

    Azure Active Directory 驗證是使用 Azure Active Directory (Azure AD) 中的身分識別來連線到 Azure [SQL Database](sql-database-technical-overview.md) 和 [SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)的機制。 Azure AD 驗證可讓系統管理員在一個中央位置，集中管理資料庫使用者的身分識別和權限以及其他 Microsoft 服務。 這包括密碼儲存體的最小化，並啟用集中式密碼輪替原則。

     必須建立稱為 **Active Directory 系統管理員**的伺服器管理員，才能使用 Azure AD 驗證搭配 SQL Database。 如需詳細資訊，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md)。 Azure AD 驗證支援受控和同盟帳戶。 同盟帳戶支援與 Azure AD 同盟的客戶網域的 Windows 使用者和群組。

    其他可用的 Azure AD 驗證選項為[適用於 SQL Server Management Studio 的 Active Directory 通用驗證](sql-database-ssms-mfa-authentication.md)連線，包括 [Multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) 和[條件式存取](sql-database-conditional-access.md)。

> [!IMPORTANT]
> 在 Azure 內管理資料庫和伺服器，是由入口網站使用者帳戶的角色指派所控制。 如需有關此文章的詳細資訊，請參閱 [Azure 入口網站中的角色型存取控制](../role-based-access-control/overview.md)。 控制防火牆規則的存取「不」適用於**受控執行個體**。 如需所需網路設定的相關資訊，請參閱下列關於[連線到受控執行個體](sql-database-managed-instance-connect-app.md)的文章。

授權是指在 Azure SQL Database 內指派給使用者的權限，並可決定允許使用者執行的動作。 將使用者帳戶新增至[資料庫角色](/sql/relational-databases/security/authentication-access/database-level-roles)可控制權限，以定義資料庫層級權限，或授與使用者某些[物件層級權限](/sql/relational-databases/security/permissions-database-engine)。 如需詳細資訊，請參閱[登入與使用者](sql-database-manage-logins.md)

最佳做法是將使用者新增至具備執行其工作職責所需最低權限的角色。 伺服器管理帳戶是 db_owner 角色的成員，其具有廣泛權限且應謹慎地授與給使用者。 使用應用程式搭配 Azure SQL Database 時，請使用具備有限權限的[應用程式角色](/sql/relational-databases/security/authentication-access/application-roles)。 這可確保連線到資料庫的應用程式具有應用程式所需的最低權限。

### <a name="row-level-security"></a>資料列層級安全性

資料列層級安全性讓客戶能夠根據執行查詢的使用者特性 (例如，依群組成員資格或執行內容)，來控制資料庫資料表中的資料列存取。 如需詳細資訊，請參閱[資料列層級安全性](/sql/relational-databases/security/row-level-security)。

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

  此驗證方法會使用使用者名稱和密碼。 

如需 Azure SQL Database 中的權限概觀，請參閱[登入和使用者](sql-database-manage-logins.md#permissions)

## <a name="threat-protection"></a>威脅保護

SQL Database 可藉由提供稽核和威脅偵測功能來保護客戶資料。

### <a name="sql-auditing-in-log-analytics-and-event-hubs"></a>Log Analytics 和事件中樞的 SQL 稽核

SQL Database 稽核會將資料庫事件記錄到客戶自有 Azure 儲存體帳戶中的稽核記錄，藉此追蹤資料庫活動並協助維護安全性標準的合規性。 稽核可讓使用者監視進行中的資料庫活動，以及分析和調查歷史活動，以找出潛在威脅或可疑的濫用和安全性違規。 如需詳細資訊，請參閱[開始使用 Azure Database 稽核](sql-database-auditing.md)。  

### <a name="threat-detection"></a>威脅偵測

威脅偵測會分析稽核記錄中的異常行為，以及可能有害的資料庫存取或入侵嘗試，藉此加強稽核。 系統會針對可疑活動或異常存取模式 (例如 SQL 插入式攻擊、潛在資料滲透，以及暴力密碼破解攻擊) 建立警示。 從 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)檢視威脅偵測警示，其中會提供可疑活動的詳細資料，並提供進一步調查的建議以及減輕威脅的動作。 每部伺服器的威脅偵測費用為每個月 $15 元。 前 60 天不收取任何費用。 如需詳細資訊，請參閱[開始使用 SQL Database 威脅偵測](sql-database-threat-detection.md)。

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>資訊保護和加密

### <a name="transport-layer-security-tls-encryption-in-transit"></a>傳輸層安全性 TLS (傳輸中加密)

SQL Database 可藉由使用[傳輸層安全性](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)將移動中的資料加密來保護客戶資料。

> [!IMPORTANT]
> Azure SQL Database 一律會針對所有連線強制執行加密 (SSL/TLS)，以確保所有資料在資料庫與用戶端之間「傳輸中」都會加密。 不論連接字串中 **Encrypt** 或 **TrustServerCertificate** 的設定為何，都將發生此情況。
>
> 在您的應用程式連接字串中，請確定您會指定加密的連線，而且「不會」信任伺服器憑證 (對於 ADO.NET 驅動程式，這是 **Encrypt=True** 和 **TrustServerCertificate=False**)。 這有助於藉由強制您的應用程式驗證伺服器並強制執行加密，來防止該應用程式受到「中間人」攻擊。 如果您從 Azure 入口網站取得連接字串，則它將具有正確的設定。
>
> 如需 TLS 和連線能力的相關資訊，請參閱 [TLS 考量](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>透明資料加密 (待用資料加密)

[Azure SQL Database 的透明資料加密 (TDE)](transparent-data-encryption-azure-sql.md) 會新增一層安全性來協助保護待用資料，以免原始檔案或備份遭到未經授權或離線存取。 常見的案例包括資料中心遭竊或不安全的硬體或媒體 (例如磁碟機和備份磁帶) 處置方式。 TDE 會使用 AES 加密演算法將整個資料庫加密，應用程式開發人員不需對現有的應用程式進行任何變更。

在 Azure 中，預設會加密新建立的 SQL 資料庫，並以內建伺服器憑證保護資料庫加密金鑰。  憑證維護和輪替是由服務管理，使用者不需要輸入任何資料。 想要掌控加密金鑰的客戶可以在 [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) 中管理金鑰。

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault 的金鑰管理

 [透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) 的[攜帶您自己的金鑰](transparent-data-encryption-byok-azure-sql.md) (BYOK) 支援可讓客戶使用  [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) (Azure 的雲端式外部金鑰管理系統)，取得金鑰管理與輪替的擁有權。 如果撤銷了資料庫對金鑰保存庫的存取權，資料庫即無法解密並讀取到記憶體中。 Azure Key Vault 可提供集中金鑰管理平台、使用嚴密監控的硬體安全性模組 (HSM)，並可區分管理金鑰和資料的職責，以利符合安全性合規性需求。

### <a name="always-encrypted-encryption-in-use"></a>一律加密 (使用中加密)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[一律加密](/sql/relational-databases/security/encryption/always-encrypted-database-engine)功能的設計訴求是要保護特定資料庫資料行中儲存的敏感性資料以防存取 (例如，信用卡號碼、身分證號碼，或「必須知道」的資料)。 這包括資料庫管理員或其他特殊權限的使用者，該使用者經授權存取資料庫以執行管理工作，但沒有存取已加密資料行中特定資料的商務需求。 資料一律會加密，這表示加密的資料會解密，僅供可存取加密金鑰的用戶端應用程式進行處理。  加密金鑰決不會公開給 SQL，而且可以存放於 [Windows 憑證存放區](sql-database-always-encrypted.md)或 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)。

### <a name="masking"></a>遮罩

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>動態資料遮罩

SQL Database 動態資料遮罩可藉由遮罩處理，使不具權限的使用者無法看見機密資料。 動態資料遮罩會自動探索 Azure SQL Database 中的可能敏感性資料，並提供可動作的建議來為這些欄位加上遮罩，盡量避免對應用程式層造成影響。 其運作方式為針對指定的資料庫欄位隱匿查詢結果集中的敏感性資料，而不變更資料庫中的資料。 如需詳細資訊，請參閱[開始使用 SQL Database 動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)。

#### <a name="static-data-masking"></a>靜態資料遮罩

[靜態資料遮罩](/sql/relational-databases/security/static-data-masking)是 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18.0 預覽版 5 和更新版本中可用的用戶端工具。  靜態資料遮罩可讓使用者建立資料庫複本，其中所選資料行中的資料已經永久遮罩處理。 可用的遮罩函式包括 NULL 遮罩、單一值遮罩、隨機播放和群組隨機遮罩，以及字串複合遮罩。 有了資料庫的遮罩複本，組織便能夠藉由共用遮罩複本來區隔生產和測試環境。 敏感性資料受到充分保護，所有其他資料庫特性都受到維護。 需要對資料庫的第三方存取時，建議進行資料庫遮罩處理。

## <a name="security-management"></a>安全性管理

### <a name="vulnerability-assessment"></a>弱點評估

[弱點評量](sql-vulnerability-assessment.md)是容易設定的服務，可探索、追蹤及協助修復潛在的資料庫弱點，其目標在於主動改善整體資料庫安全性。 弱點評量 (VA) 是進階資料安全性 (ADS) 供應項目的一部分，該供應項目是進階 SQL 安全性功能的整合套件。 弱點評量可以透過中央 SQL ADS 入口網站存取及管理。

### <a name="data-discovery--classification"></a>資料探索與分類

資料探索與分類 (目前處於預覽階段) 提供內建於 Azure SQL Database 的進階功能，可用於探索、分類、標記和保護您資料庫中的敏感性資料。 探索與分類您最具敏感性的資料 (商業/財務、醫療保健、個人資料等)，可在您組織的資訊保護方面扮演著關鍵角色。 它可以作為以下的基礎結構：

- 各種安全性案例，例如針對敏感性資料異常存取的監視 (稽核) 及警示。
- 對包含高度敏感性資料的資料庫進行存取控制並強化安全性。
- 協助符合資料隱私標準和法規合規性需求。

如需詳細資訊，請參閱[開始使用資料探索與分類](sql-database-data-discovery-and-classification.md)。

### <a name="compliance"></a>法規遵循

除了上述可協助您的應用程式符合各種安全性需求的特色和功能之外，Azure SQL Database 也定期參與稽核，並且經過認證符合許多法規標準。 如需詳細資訊，請參閱 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)，您可以在當中找到 [SQL Database 法規認證](https://www.microsoft.com/trustcenter/compliance/complianceofferings)的最新清單。

## <a name="next-steps"></a>後續步驟

- 如需在 SQL Database 中使用存取控制功能的討論，請參閱[控制存取](sql-database-control-access.md)。
- 如需資料庫稽核的相關討論，請參閱 [SQL Database 稽核 (英文)](sql-database-auditing.md)。
- 如需威脅偵測的相關討論，請參閱 [SQL Database 威脅偵測](sql-database-threat-detection.md)。
