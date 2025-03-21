---
title: 雲端市集的潛在客戶管理 | Azure Marketplace 和 AppSource
description: 向 Azure Marketplace 和 AppSource 發佈供應項目和技術成品的各種相關主題概觀
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: Marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/05/2018
ms.author: yijenj
ms.openlocfilehash: abaad7eabf92b6f3f53d7807af132959533552b9
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076955"
---
# <a name="lead-management-for-cloud-marketplace"></a>雲端市集的潛在客戶管理


客戶是所有優秀企業的核心。 在現今的產品併購變革中，行銷人員需要著重在直接與客戶連結，建立關係。 這也是為什麼產生高品質潛在客戶，對您的銷售循環而言是一個相當關鍵的工具。 在 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)中列出您的供應項目後，系統會為您啟用工具，讓您在客戶對您市集中的產品表達興趣，或部署您的產品後，能以程式化方式，立即收到客戶連絡資訊。 



## <a name="what-are-leads-in-the-marketplace"></a>什麼是市集中的潛在客戶？

潛在客戶是對您在 Marketplace 中的產品有興趣，或從 Marketplace 部署您產品的客戶。 無論您的產品是列在 Azure Marketplace 或 AppSource 上，只要將產品從您的 CRM 正確地設定到 Cloud Partner 入口網站中的清單中，您就能夠從客戶端接收到潛在客戶 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>如何將 CRM 系統與 Cloud Partner 入口網站連線

為了能開始取得潛在客戶，我們在 Cloud Partner 入口網站上設計了潛在客戶管理連接器，可讓您輕鬆地將可用 CRM 系統清單的 CRM 資訊插入 Cloud Partner 入口網站。 現在您無須耗費浩大的工程心力來整合外部系統，便可以輕鬆地運用由 Marketplace 產生的潛在客戶。

以下逐步指示將說明如何連線每個潛在客戶目的地：

**Dynamics CRM Online** - [按一下這裡](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)以取得如何設定 Dynamics CRM Online 來獲得潛在客戶的說明。

**Marketo** - [按一下這裡](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo)以取得如何設定 Marketo Lead 組態來獲得潛在客戶的說明。

**Salesforce** - [按一下這裡](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce)以取得如何設定 Salesforce 執行個體來獲得潛在客戶的說明。

**Azure Table** – [按一下這裡](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table)以取得如何設定 Azure 儲存體帳戶，並在 Azure 資料表獲得潛在客戶的說明。

**Https Endpoint** – [按一下這裡](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https)，取得有關設定 Https 端點以獲得潛在客戶的說明。

您妥善設定您的潛在客戶目的地，並按下您供應項目上的 [發佈] 後，我們將驗證連接，並傳送測試用潛在客戶給您。 在上架前檢視供應項目時，您也可以試著在預覽環境中自行取得供應項目，藉此測試您的潛在客戶連線。 關鍵是，請務必確定您的潛在客戶設定保持在最新狀態，如此您將不會流失潛在客戶；因此您的商務若有變更，請記得更新這些連接。


### <a name="what-are-the-next-steps"></a>後續步驟是什麼？

技術設定完成後，您應該將這些潛在客戶整合至您目前的銷售與行銷策略，以及營運流程當中。 我們有興趣進一步了解您的整體銷售流程，並想與您密切合作，為您提供高品質潛在客戶及足夠的資料，讓您獲致成功。 我們歡迎您針對我們如何能最佳化及改善我們傳送給您的潛在客戶，及提供額外資料協助這些客戶達成您的成就，提出您的意見反應。 如果您有興趣提供意見反應及建議，讓我們的銷售團隊能推出更成功的 Marketplace 潛在客戶，請告訴我們。



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>在 Cloud Partner 入口網站上發佈時的常見潛在客戶設定錯誤 

**無法將潛在客戶儲存至 Dynamics CRM。請檢查 Dynamics CRM 帳戶設定。LastCRMError：無法登入 Dynamics CRM、LastCRMException：** 

> 如果已選取 O365 驗證，請檢查使用者帳戶和密碼是否有效。 如果已選取 AAD，請檢查租用戶識別碼、應用程式識別碼和應用程式祕密金鑰是否符合 AAD 上已設定的項目。 請遵循[此處](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)指示。如果帳戶使用者名稱/密碼有效，請確定其可以存取 Dynamics 365，並已指派授權 (如果是使用 Azure Active Directory，請參考步驟 11-15，如果使用 Office 使用者，請參考安全性設定)。 

 
**無法將潛在客戶儲存至 Dynamics CRM。使用者沒有在潛在客戶實體中建立 leadsourcecode 屬性的權限** 

> 應用程式/使用者遺失 Microsoft Marketplace 潛在客戶寫入器的安全性角色。 請參閱[此處](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)，如果使用 Azure Active Directory，請遵循步驟 11 至 15，如果是使用 Office 使用者，請遵循安全性設定。

**無法使用 AAD 將潛在客戶儲存至 Dynamics CRM。例外狀況：找不到租用戶。如果沒有任何作用中的租用戶訂用帳戶，可能會發生這個情況。**  

> 潛在客戶管理一節中提供的目錄識別碼不是有效的目錄。 請根據[此處](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)步驟 2 中的指示取得目錄識別碼 (在 Azure Active Directory 下) 

**無法將潛在客戶儲存至 Dynamics CRM。LastCRMError：SecLib::RetrievePrivilegeForUser 失敗 - 沒有指派任何角色給使用者。**  

> 解決方案：將安全性角色指派給 Microsoft Marketplace 潛在客戶寫入器。 請依照[此處](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)安全性設定下的指示 

**無法使用 AAD 將潛在客戶儲存至 Dynamics CRM。例外狀況：在目錄中找不到包含識別碼的應用程式** 

> 潛在客戶管理一節中提供的應用程式識別碼不是有效的目錄。 請根據[此處](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)步驟 8 的指示取得目錄識別碼 (在 Azure Active Directory 下)。 

**無法使用 AAD 將潛在客戶儲存至 Dynamics CRM。例外狀況：所要求的租用戶識別碼無效，且不是有效的外部網域格式** 

> 潛在客戶管理一節中提供的目錄識別碼不是有效的目錄。 請根據[此處](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)步驟 2 中的指示取得目錄識別碼 (在 Azure Active Directory 下)。 

**無法使用 AAD 將潛在客戶儲存至 Dynamics CRM。例外狀況：驗證認證時發生錯誤：提供的用戶端祕密無效。** 

> 解決方案：登入 Azure 入口網站，檢查應用程式金鑰是否符合 Cloud Partner 入口網站中的內容。 請根據[此處](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)步驟 10 中的指示產生密碼 (在 Azure Active Directory 下)。 

**無法將潛在客戶儲存至 Dynamics CRM。LastCRMError：等待回覆至 00:02:00 之後，要求通道逾時。提高傳遞至呼叫要求的逾時值，或增加繫結上的 SendTimeout 值。分配給此作業的時間可能是較長逾時的一部分。**  

> 解決方案：登入 Cloud Partner 入口網站，查看店面詳細資料 >> 潛在客戶目的地 >> URL，檢查是否為有效的 Dynamics CRM 執行個體

## <a name="frequently-asked-questions"></a>常見問題集

**什麼是潛在客戶？作為 Marketplace 上的發行者，為什麼潛在客戶對我至關重要？** 

潛在客戶是從 Marketplace 部署您產品的客戶。 無論您的產品是列在 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) 或 [AppSource](https://appsource.microsoft.com/)，如果您在供應項目上設定了潛在客戶目的地，就都可以收到對您產品感興趣的潛在客戶。  


**可以在哪裡取得設定我潛在客戶目的地的說明？** 

您可以在這裡找到文件：[取得潛在客戶](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)，或透過 aka.ms/marketplacepublishersupport 提交支援票證，選取供應項目類型及潛在客戶管理。 



**我是否必須設定潛在客戶目的地，才能在 Marketplace 上發佈供應項目？**

是，如果您要發佈「連絡我 SaaS 應用程式」或「諮詢服務」。  
 


**如何確認潛在客戶設定是否正確？**

設定好您的供應項目和潛在客戶目的地之後，請發佈您的供應項目。 在潛在客戶驗證步驟中，Marketplace 會將測試潛在客戶傳送至您在供應項目中設定的潛在客戶目的地。 


**如何找到測試潛在客戶？**


在您的潛在客戶目的地中搜尋 “MSFT_TEST”，以下是測試潛在客戶的範例資料： 

company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**我有作用中的供應項目，但我沒看到任何潛在客戶？**

每位潛在客戶都會將資料傳入所選潛在客戶目的地中的欄位，潛在客戶會以下列格式顯示：**來源動作|供應項目** 

  來源：

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  動作：

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  供應項目：

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  以下是客戶資訊的範例資料

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

請在[潛在客戶資訊](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)下深入了解。 


**我已將 Azure BLOB 設定為我的潛在客戶目的地，為何沒有看到潛在客戶？** 

只有當您選取 Azure BLOB 儲存體作為您的潛在客戶目的地時，才會寫入潛在客戶。 切換至 Azure 資料表以即時接收潛在客戶 


**我已從 Marketplace 收到一封電子郵件，為什麼在我的 CRM 中找不到潛在客戶？**  

終端使用者的電子郵件網域可能來自 .edu。 基於隱私原因，我們不會從 .edu 網域傳遞 PII 資料。 請透過 aka.ms/marketplacepublishersupport 提交支援票證 


 **我已將 Azure 表格/Azure BLOB 設定為我的潛在客戶目的地，該如何檢視潛在客戶？** 

您可以從 Azure 入口網站存取 Blob 或資料表，或是免費下載及安裝 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，來檢視您 Azure 儲存體帳戶的資料表/Blob。 


**我已設定 Azure 資料表作為我的潛在客戶目的地，是否可以在 Marketplace 傳送新的潛在客戶時隨時收到通知？** 

是，請依照[此處](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table)文件上的指示來設定 Azure 資料表 + 函式。 



**我已設定 Salesforce 作為我的潛在客戶目的地，為什麼找不到潛在客戶？** 

根據挑選清單檢查潛在客戶表單的 Web 是否為必要欄位。 如果是，請將欄位切換為非必要文字欄位。  
 

**我的潛在客戶目的地發生問題，而我遺漏了一些潛在客戶。是否可以在電子郵件中將這些潛在客戶傳送給我？** 

由於 PII (個人識別資訊) 原則，我們無法透過不安全的電子郵件分享潛在客戶資訊。 



**我已將 Azure 儲存體 (BLOB/資料表) 設定為我的潛在客戶目的地，它的售價如何？** 

潛在客戶的一般資料很小 (幾乎所有的發行者都 < 1 GB)。 售價取決於所收到的潛在客戶數量，如果在一個月內收到 1,000 個潛在客戶，則成本大約 50 分。 
