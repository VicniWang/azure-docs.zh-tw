---
title: 在 Azure 上部署 IBM DB2 pureScale
description: 了解如何部署最近用來將企業從其 IBM DB2 環境 (在 z/OS 上執行) 遷移到 Azure 上的 IBM DB2 pureScale 的範例架構。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 104730d94134d935f56fb95fd55d05b515e9f501
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245560"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>在 Azure 上部署 IBM DB2 pureScale

本文說明如何部署企業客戶最近用來從其 IBM DB2 環境 (在 z/OS 上執行) 遷移到 Azure 上的 IBM DB2 pureScale 的[範例架構](ibm-db2-purescale-azure.md)。

若要依照用於移轉的步驟執行，請參閱 GitHub 上 [DB2onAzure](http://aka.ms/db2onazure) 存放庫中的安裝指令碼。 這些指令碼是以用於一般、中型線上交易處理 (OLTP) 工作負載的架構為基礎。

## <a name="get-started"></a>開始使用

若要部署此架構，請下載並執行可在 GitHub 上 [DB2onAzure](http://aka.ms/db2onazure) 存放庫中找到的 deploy.sh 指令碼。

存放庫也有可供設定 Grafana 儀表板的指令碼。 您可以使用此儀表板來查詢 Prometheus 這個開放原始碼監視與警示系統 (DB2 所隨附) 的指令碼。

> [!NOTE]
> 用戶端上的 deploy.sh 指令碼會建立私人 SSH 金鑰並透過 HTTPS 將它們傳遞到部署範本。 若要獲得更高的安全性，我們建議您使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 來存放祕密、金鑰與密碼。

## <a name="how-the-deployment-script-works"></a>部署指令碼如何運作

deploy.sh 指令碼會建立並設定此架構的 Azure 資源。 該指令碼會提示您提供目標環境中使用的 Azure 訂用帳戶與虛擬機器，然後執行下列作業：

-   針對安裝設定 Azure 上的資源群組、虛擬網路與子網路

-   針對環境設定網路安全性群組和 SSH

-   在 GlusterFS 與 DB2 pureScale 虛擬機器上設定 NIC

-   建立 GlusterFS 儲存體虛擬機器

-   建立 jumpbox 虛擬機器

-   建立 DB2 pureScale 虛擬機器

-   建立供 DB2 pureScale 執行 ping 命令的見證虛擬機器

-   建立 Windows 虛擬機器以用於測試，但不在其上安裝任何項目

接著，部署指令碼會針對 Azure 上的共用儲存體設定 iSCSI 虛擬存放區域網路 (vSAN)。 在此範例中，IiSCSI 會連線到 GlusterFS。 此解決方案也為您提供安裝 iSCSI 目標做為單一 Windows 節點的選項。 iSCSI 透過 TCP/IP 提供共用區塊儲存體介面，它允許 DB2 pureScale 安裝程序使用裝置介面來連線到共用儲存體。 如需 GlusterFS 基本概念，請參閱 Gluster Docs 中的[架構：磁碟區類型](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/)主題。

部署指令碼會執行下列一般步驟：

1.  使用 GlusterFS 在 Azure 上設定共用儲存體叢集。 此步驟牽涉到至少兩個 Linux 節點。 如需設定詳細資料，請參閱 Red Hat 叢集文件中的[在 Microsoft Azure 中設定 Red Hat 叢集儲存體](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) \(英文\)。

2.  針對 GlusterFS，在目標 Linux 伺服器上設定 iSCSI Direct 介面。 如需設定詳細資料，請參閱《GlusterFS 系統管理指南》\(英文\) 中的 [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) \(英文\)。

3.  在 Linux 虛擬機器上設定 iSCSI 啟動器。 啟動器會使用 iSCSI 目標來存取 GlusterFS 叢集。 如需設定詳細資料，請參閱 RootUsers 文件中的[如何在 Linux 中設定 iSCSI 目標與啟動器](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) \(英文\)。

4.  針對 iSCSI 介面，安裝 GlusterFS 做為儲存體層。

在指令碼建立 iSCSI 裝置之後，最終步驟是安裝 DB2 pureScale。 在 DB2 pureScale 安裝期間，會編譯 [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (先前稱為 GPFS) 並安裝在ˋ GlusterFS 叢集上。 這個叢集化檔案系統可讓 DB2 pureScale 在執行 Db2 pureScale 引擎的虛擬機器之間共用資料。 如需詳細資訊，請參閱 IBM 網站上的 [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) 文件 \(英文\)。

## <a name="db2-purescale-response-file"></a>DB2 pureScale 回應檔

GitHub 存放庫包括 DB2server.rsp 回應檔 (.rsp)，此檔案可讓您產生自動化指令碼以進行 DB2 pureScale 安裝。 下表列出回應檔用於安裝程式的 DB2 pureScale 選項。 您可以根據您的環境需求自訂回應檔。

> [!NOTE]
> GitHub 上的 [DB2onAzure](http://aka.ms/db2onazure) 存放庫中包含一個範例回應檔 DB2server.rsp。 若您使用此檔案，您必須先編輯它，才能在您的環境中使用。

| 畫面名稱               | 欄位                                        | 值                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| 歡迎使用                   |                                              | 新安裝                                                                                           |
| 選擇產品          |                                              | DB2 版本 11.1.3.3。 具有 DB2 pureScale 的伺服器版本                                              |
| 組態             | 目錄                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | 選取安裝類型                 | 一般                                                                                               |
|                           | 我同意 IBM 條款                     | 已檢查                                                                                               |
| 執行個體擁有者            | 例如現有使用者、使用者名稱        | DB2sdin1                                                                                              |
| 受限使用者               | 現有使用者、使用者名稱                     | DB2sdfe1                                                                                              |
| 叢集檔案系統       | 共用磁碟磁碟分割裝置路徑            | /dev/dm-2                                                                                             |
|                           | 掛接點                                  | /DB2sd\_1804a                                                                                         |
|                           | 資料的共用磁碟                         | /dev/dm-1                                                                                             |
|                           | 掛接點 (資料)                           | /DB2fs/datafs1                                                                                        |
|                           | 記錄檔的共用磁碟                          | /dev/dm-0                                                                                             |
|                           | 掛接點 (記錄檔)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 叢集服務 Tiebreaker。 裝置路徑 | /dev/dm-3                                                                                             |
| 主機清單                 | d1 [eth1]、d2 [eth1]、cf1 [eth1],、cf2 [eth1] |                                                                                                       |
|                           | 慣用主要 CF                         | cf1                                                                                                   |
|                           | 慣用次要 CF                       | cf2                                                                                                   |
| 回應檔與摘要 | 第一個選項                                 | 安裝具有 IBM DB2 pureScale 功能的 DB2 Server Edition 並將我的設定儲存在回應檔中 |
|                           | 回應檔名稱                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>關於此部署的注意事項

- /dev-dm0、/dev-dm1、/dev-dm2 與 /dev-dm3 的值可在虛擬機器重新啟動之後、安裝程式接手 (自動化指令碼中的 d0) 時變更。 若要尋找正確的值，您可以在於伺服器 (安裝程式將在其上執行的伺服器) 上完成回應檔之前發出下列命令：

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- 安裝程式指令碼會為 iSCSI 磁碟使用別名，因此您可以輕鬆找到實際名稱。

- 當安裝程式指令碼在 d0 上執行時，**/dev/dm-\*** 值在 d1、cf0 與 cf1 上可能會不同。 值的差異並不會影響 DB2 pureScale 設定。

## <a name="troubleshooting-and-known-issues"></a>疑難排解與已知問題

GitHub 存放庫包括由作者維護的知識庫。 它會列出您可能有的潛在問題，以及您可以嘗試的解決方法。 例如，在下列情況可能會發生已知問題：

-   您正嘗試觸達閘道 IP 位址。

-   您正在編譯一般公用授權 (GPL)。

-   主機之間的安全性交握失敗。

-   DB2 安裝程式偵測到現有的檔案系統。

-   您正在手動安裝 IBM Spectrum Scale。

-   當 IBM Spectrum Scale 已建立時，您正安裝 Db2 pureScale。

-   您正在移除 DB2 pureScale 與 IBM Spectrum Scale。

如需有關這些與其他已知問題的詳細資訊，請參閱 [DB2onAzure](http://aka.ms/DB2onAzure) 存放庫中的 kb.md 檔案。

## <a name="next-steps"></a>後續步驟

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [針對 DB2 pureScale 功能安裝建立必要使用者](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - 建立執行個體命令](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale 叢集資料解決方案](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Platform Modernization Alliance：Azure 上的 IBM DB2](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Azure 虛擬資料中心：原形移轉指南](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
