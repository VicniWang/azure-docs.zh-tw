---
title: 鎖定 App Service 環境的連出流量 - Azure
description: 描述如何與 Azure 防火牆整合來保護輸出流量
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: af6a32d7e32f23561b207c729402eaea7925f520
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453846"
---
# <a name="locking-down-an-app-service-environment"></a>鎖定 App Service 環境

App Service Environment (ASE) 有一些外部相依性，它需要能夠存取，才能正確運作。 ASE 位於客戶 Azure 虛擬網路 (VNet)。 客戶必須允許 ASE 相依性流量，這對於想要鎖定從其 VNet 所有輸出流量的客戶而言會是個問題。

ASE 有一些輸入相依性。 輸入的管理流量不能透過防火牆裝置傳送。 此流量的來源位址已知，且發佈在 [App Service Environment 管理位址](https://docs.microsoft.com/azure/app-service/environment/management-addresses)文件。 您可以建立具有該資訊的網路安全性群組規則，來保護輸入的流量。

ASE 輸出相依性幾乎完全使用 FQDN 定義，它背後並沒有靜態位址。 缺乏靜態位址表示網路安全性群組 (NSG) 不能用來鎖定來自 ASE 的輸出流量。 地址經常變更，使得無法根據目前的解析度來設定規則，以用來建立 NSG。 

保護輸出位址的解決方案在於使用可以根據網域名稱控制輸出流量的防火牆裝置。 Azure 防火牆可以依據目的地的 FQDN 限制 HTTP 和 HTTPS 流量輸出。  

## <a name="configuring-azure-firewall-with-your-ase"></a>使用 ASE 設定 Azure 防火牆 

使用 Azure 防火牆來鎖定現有 ASE 輸出流量的步驟如下：

1. 在 ASE 子網路上為 SQL、儲存體和事件中樞啟用服務端點。 若要這樣做，請移至網路入口網站 > 子網路，然後從服務端點的下拉式清單中選取 Microsoft.EventHub、Microsoft.SQL 和 Microsoft.Storage。 當您為 Azure SQL 啟用服務端點時，任何具有 Azure SQL 相依性的應用程式也必須設定服務端點。 

   ![選取服務端點][2]
  
1. 在存在 ASE 的 VNet 中建立名為 AzureFirewallSubnet 的子網路。 請遵照 [Azure 防火牆文件](https://docs.microsoft.com/azure/firewall/)的指示，來建立您的 Azure 防火牆。
1. 從 [Azure 防火牆 UI > 規則 > 應用程式規則集合] 中，選取 [新增應用程式規則集合]。 提供名稱、優先順序，並設定為 [允許]。 在 [FQDN 標記] 區段中，提供名稱、將來源位址設定為 *，然後選取 [App Service 環境 FQDN 標記] 和 [Windows Update]。 
   
   ![加入應用程式規則][1]
   
1. 從 [Azure 防火牆 UI > 規則 > 網路規則集合] 中，選取 [新增網路規則集合]。 提供名稱、優先順序，並設定為 [允許]。 在 [規則] 區段中，提供名稱、選取 [任何]、將 * 設定為來源和目的地位址，並將連接埠設定為 123。 此規則可讓系統使用 NTP 執行時鐘同步。 以與連接埠 12000 相同的方式建立另一個規則，以協助分類任何系統問題。

   ![加入 NTP 網路規則][3]

1. 建立路由表，其中具有來自 [App Service Environment 管理位址]( https://docs.microsoft.com/azure/app-service/environment/management-addresses)的管理位址，以及網際網路的下一個躍點。 需要路由表項目，才能避免發生非對稱式路由問題。 針對底下＜IP 位址相依性＞所註明的 IP 位址相依性，新增具有網際網路下一個躍點的路由。 在路由表中新增 0.0.0.0/0 的虛擬設備路由，並且以 Azure 防火牆私人IP 位址作為下一個躍點。 

   ![建立路由表][4]
   
1. 將您建立的路由表指派給 ASE 子網路。

#### <a name="deploying-your-ase-behind-a-firewall"></a>在防火牆後面部署 ASE

將 ASE 部署在防火牆後面的步驟與使用 Azure 防火牆設定現有 ASE 的步驟相同，除非您需要建立 ASE 子網路，則依照先前的步驟。 若要在預先存在的子網中建立 ASE，您需要使用 Resource Manager 範本，如[使用 Resource Manager 範本建立 ASE](https://docs.microsoft.com/azure/app-service/environment/create-from-template) 中的文件所述。

## <a name="application-traffic"></a>應用程式流量 

上述步驟可讓您的 ASE 正常運作。 您仍然需要設定以容納應用程式需求。 針對已設定 Azure 防火牆的 ASE，其中的應用程式有兩個問題。  

- 應用程式相依性必須新增至 Azure 防火牆或路由表。 
- 必須為應用程式流量建立路由，以避免非對稱式路由問題

如果您的應用程式有相依性，它們必須新增至您的 Azure 防火牆。 建立應用程式規則來允許 HTTP/HTTPS 流量，以及建立網路規則來允許其他一切流量。 

如果您知道應用程式要求流量來源的位址範圍，可以將它新增到指派給您 ASE 子網路的路由表。 如果位址範圍很大或未指定，則可以使用網路設備，例如應用程式閘道，提供您一個位址以便新增至路由表。 如需設定應用程式閘道與 ILB ASE 的詳細資訊，請參閱[整合 ILB ASE 與應用程式閘道](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

![具有 Azure 防火牆連線流程的 ASE][5]

此應用程式閘道的使用方式只是如何設定系統的範例。 如果您確實遵循此路徑，則您需要將路由新增至 ASE 子網路路由表，以便傳送至應用程式閘道的回覆流量會直接傳送至該處。 

## <a name="logging"></a>記錄 

Azure 防火牆可以將記錄傳送至 Azure 儲存體、事件中樞或 Azure 監視器記錄。 若要將您的應用程式與任何支援的目的地整合，請移至 [Azure 防火牆入口網站 > 診斷記錄檔]，並啟用所需目的地的記錄檔。 如果您與 Azure 監視器記錄整合，則可以看到傳送至 Azure 防火牆的任何流量記錄。 若要查看被拒絕的流量，請開啟 [Log Analytics 工作區入口網站] > [記錄檔]，然後輸入如下查詢 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
在不知道所有應用程式相依性存在時，第一次讓應用程式正常運作時，將 Azure 防火牆與 Azure 監視器記錄整合非常有用。 您可以從[分析 Azure 監視器中的記錄資料](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)深入了解 Azure 監視器記錄
 
## <a name="dependencies"></a>相依性

當您想要設定 Azure 防 火牆以外的防火牆設備時，才需要下列資訊。 

- 應使用服務端點來設定支援的服務端點服務。
- 適用於非 HTTP/S 流量的 IP 地址相依性 (TCP 與 UDP 流量)
- FQDN HTTP/HTTPS 端點可以放在您的防火牆裝置。
- 萬用字元 HTTP/HTTPS 端點是根據一些限定條件，可能隨著 ASE 而變的相依性。 
- 只有在您要將 Linux 應用程式部署到 ASE 時，才需要考量 Linux 相依性。 如果您沒有要將 Linux 應用程式部署到 ASE，則這些位址不需要新增至您的防火牆。 


#### <a name="service-endpoint-capable-dependencies"></a>服務端點功能相依性 

| 端點 |
|----------|
| Azure SQL |
| Azure 儲存體 |
| Azure 事件中樞 |

#### <a name="ip-address-dependencies"></a>IP 位址相依性

| 端點 | 詳細資料 |
|----------| ----- |
| \*:123 | NTP 時鐘檢查。 在連接埠 123 上的多個端點檢查流量 |
| \*:12000 | 此連接埠用於某些系統監視。 如果被封鎖，則某些問題會更加難以分類，但你的 ASE 會繼續運作 |

使用 Azure 防火牆，您可以自動獲得以下使用 FQDN 標記設定的所有內容。 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 相依性 

| 端點 |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80   |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>萬用字元 HTTP/HTTPS 相依性 

| 端點 |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*.servicebus.windows.net:443 |
|grmdsprod\*lini\*.servicebus.windows.net:443 |
|grsecprod\*mini\*.servicebus.windows.net:443 |
|grsecprod\*lini\*.servicebus.windows.net:443 |
|graudprod\*mini\*.servicebus.windows.net:443 |
|graudprod\*lini\*.servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Linux 相依項目 

| 端點 |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
