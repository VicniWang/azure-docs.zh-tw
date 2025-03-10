---
title: 使用 Azure Site Recovery 來管理處理序伺服器以便進行從 VMware VM 和實體伺服器至 Azure 的災害復原 | Microsoft Docs
description: 本文說明如何使用 Azure Site Recovery 來管理處理序伺服器的設定，以便進行從 VMware VM 和實體伺服器至 Azure 的災害復原。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: ramamill
ms.openlocfilehash: fd434ed8d5b625a1a0ed1ff195da0f864a4024b0
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769419"
---
# <a name="manage-process-servers"></a>管理處理序伺服器

根據預設，您將 VMware VM 或實體伺服器複寫至 Azure 時使用的處理序伺服器，會安裝在內部部署組態伺服器機器上。 您需要在幾個執行個體中設定個別的處理序伺服器：

- 針對大型部署，您可能需要額外的內部部署處理序伺服器才能調整容量。
- 針對容錯回復，您需要在 Azure 中設定臨時處理序伺服器。 容錯回復完成後，您可以刪除此 VM。 

本文摘要說明這些額外處理序伺服器的一般管理工作。

## <a name="upgrade-a-process-server"></a>升級處理序伺服器

升級在內部部署環境或在 Azure 中執行的處理序伺服器 (針對容錯回復目的)，如下所示：

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  一般而言，當您為了容錯回復目的，在 Azure 中使用 Azure 資源庫映像來建立處理序伺服器時，它會執行可用的最新版本。 Site Recovery 小組會定期發行修正程式和增強功能，我們建議您將處理序伺服器保持在最新狀態。

## <a name="balance-the-load-on-process-server"></a>對處理序伺服器上的負載進行平衡

若要對兩個處理序伺服器之間的負載進行平衡：

1. 瀏覽至 [復原服務保存庫] > [管理] > [Site Recovery 基礎結構] > [針對 VMware 和實體機器] > [組態伺服器]。
2. 按一下註冊處理序伺服器的組態伺服器。
3. 註冊至該組態伺服器的處理序伺服器清單隨即顯示在頁面上。
4. 按一下您想要修改工作負載的處理序伺服器。

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

5. 您可以視需求使用 [負載平衡] 或 [切換] 選項，如下面所述。

### <a name="load-balance"></a>負載平衡

透過此選項，您可以選取一或多個虛擬機器，並能將它們傳送到另一個處理序伺服器。

1. 按一下 [負載平衡]，從下拉式清單選取目標處理序伺服器。 按一下 [檔案] &gt; [新增] &gt; [專案] 

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. 按一下 [選取機器]，選擇您想要從目前的處理序伺服器移至目標處理序伺服器的虛擬機器。 系統會針對每個虛擬機器顯示平均資料變更的詳細資料。
3. 按一下 [確定]。 您可以在 [復原服務保存庫] > [監視] > [Site Recovery 作業] 之下監視作業進度。
4. 在此作業成功完成之後，其變更需要 15 分鐘的時間才會反映出來。您也可以[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)來使變更立即生效。

### <a name="switch"></a>Switch

透過此選項，保護在處理序伺服器之下的整個工作負載，會被移至不同的處理序伺服器。

1. 按一下 [切換]，選取目標處理序伺服器，按一下 [確定]。

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

2. 您可以在 [復原服務保存庫] > [監視] > [Site Recovery 作業] 之下監視作業進度。
3. 在此作業成功完成之後，其變更需要 15 分鐘的時間才會反映出來。您也可以[重新整理組態伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)來使變更立即生效。

## <a name="reregister-a-process-server"></a>重新註冊處理序伺服器

如果您需要重新註冊在內部部署環境或在 Azure 中執行的處理序伺服器，請使用組態伺服器執行下列動作：

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

儲存設定之後，請執行下列作業：

1. 在處理序伺服器上，開啟系統管理員命令提示字元。
2. 瀏覽至 **%PROGRAMDATA%\ASR\Agent** 資料夾，然後執行命令：

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>修改內部部署處理序伺服器的 Proxy 設定

如果處理序伺服器使用 Proxy 來連線到 Azure 中的 Site Recovery，當您需要修改現有 Proxy 設定時，請使用此程序。

1. 登入處理序伺服器電腦。 
2. 開啟系統管理 PowerShell 命令視窗並執行下列命令：
  ```powershell
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. 瀏覽至 **%PROGRAMDATA%\ASR\Agent** 資料夾，然後執行下列命令：
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>移除處理序伺服器

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>在處理序伺服器上管理防毒軟體

如果防毒軟體在獨立處理序伺服器或主要目標伺服器上作用，請從防毒作業中排除下列資料夾：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 處理序伺服器安裝目錄，範例：C:\Program Files (x86)\Microsoft Azure Site Recovery

