---
title: 使用 AzCopy v10 (預覽) 複製或移動資料到 Azure 儲存體 | Microsoft Docs
description: 使用 AzCopy v10 (預覽) 公用程式在 Blob、Data Lake 和檔案內容間來回移動或複製資料。 從本機檔案複製資料到 Azure 儲存體，或在儲存體帳戶內或之間複製資料。 輕鬆地將資料移轉至 Azure 儲存體。
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: c9009e898b00212dba4dec9bf38af2bfa057b8ea
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244601"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>使用 AzCopy v10 (預覽) 傳輸資料

AzCopy v10 (預覽) 是新一代的命令列公用程式，可在 Microsoft Azure Blob 和檔案儲存體來回複製資料，以提供經過重新設計的命令列介面和新的架構，以供進行高效能的可靠資料傳輸。 您可以使用 AzCopy 在檔案系統和儲存體帳戶之間，或者儲存體帳戶之間複製資料。

## <a name="whats-new-in-azcopy-v10"></a>AzCopy v10 的新增功能

- 將檔案系統同步處理到 Azure Blob，反之亦然。 使用 `azcopy sync <source> <destination>`。 適用於增量複製案例。
- 支援 Azure Data Lake Storage Gen2 API。 使用 `myaccount.dfs.core.windows.net` 作為用來呼叫 ADLS Gen2 API 的 URI。
- 支援將整個帳戶 (僅限 Blob 服務) 複製到另一個帳戶。
- 帳戶對帳戶的複製現在會使用新的 [Put from URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API。 不需要傳輸任何資料到用戶端，所以傳輸速度更快！
- 列出/移除指定路徑中的檔案和 Blob。
- 在路徑中支援萬用字元模式以及 --include 和 --exclude 旗標。
- 提升復原能力：每個 AzCopy 執行個體都會建立作業順序和相關的記錄檔。 您可以檢視和重新啟動先前的作業，並繼續失敗的作業。 AzCopy 也會在失敗之後自動重試傳輸作業。
- 一般效能改進。

## <a name="download-and-install-azcopy"></a>下載並安裝 AzCopy

### <a name="latest-preview-version-v10"></a>最新預覽版本 (v10)

下載 AzCopy 的最新預覽版本：
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>最新生產版本 (v8.1)

下載 [AzCopy for Windows 的最新生產版本](https://aka.ms/downloadazcopy)。

### <a name="azcopy-supporting-table-storage-service-v73"></a>支援資料表儲存體服務的 AzCopy (v7.3)

下載[支援在 Microsoft Azure 資料表儲存體服務中來回複製資料的 AzCopy v7.3](https://aka.ms/downloadazcopynet)。

## <a name="post-installation-steps"></a>後續安裝步驟

AzCopy v10 不需要安裝。 請開啟慣用的命令列應用程式，並瀏覽至 `azcopy.exe` 可執行檔所在的資料夾。 若有需要，您可以在您的系統路徑中加入 AzCopy 資料夾位置。

## <a name="authentication-options"></a>驗證選項

AzCopy v10 可讓您在向 Azure 儲存體進行驗證時使用下列選項：
- **[Blob 和 ADLS Gen2 服務支援的] Azure Active Directory**。 使用 Azure Active Directory 透過 ```.\azcopy login``` 來登入。  使用者應該[獲派「儲存體 Blob 資料參與者」角色](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)，才能使用 Azure Active Directory 驗證寫入至 Blob 儲存體。
- **[Blob 和檔案服務支援的] SAS 權杖**。 將 SAS 權杖附加至命令列上的 Blob 路徑中來加以使用。 您可以使用 Azure 入口網站、[儲存體總管](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)、[PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken) 或您選擇的其他工具來產生 SAS 權杖。 如需詳細資訊，請參閱[範例](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)。

> [!IMPORTANT]
> 提交支援要求給 Microsoft 支援服務 (或針對涉及任何協力廠商的問題進行疑難排解) 時，請共用您正嘗試執行之命令的編校版本，以確保不會意外與任何人共用 SAS。 您可以在記錄檔開頭找到編校的版本。 如需詳細資訊，請檢閱本文稍後的＜疑難排解＞一節。

## <a name="getting-started"></a>開始使用

AzCopy v10 的自我記載語法很簡單。 登入 Azure Active Directory 時的一般語法如下所示：

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

用來取得可用命令清單的方法如下：

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

若要查看特定命令的說明頁面和範例，請執行下列命令：

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>建立 Blob 容器或檔案共用 

**建立 Blob 容器**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**建立檔案共用**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**使用 ADLS Gen2 建立 Blob 容器**

如果您已在 Blob 儲存體帳戶上啟用階層命名空間，您可以使用下列命令來建立新的檔案系統 (Blob 容器)，以便將檔案上傳到該處。

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>將資料複製到 Azure 儲存體

使用 copy 命令將資料從來源傳輸到目的地。 來源/目的地可以是：
- 本機檔案系統
- Azure Blob/虛擬目錄/容器 URI
- Azure 檔案/目錄/檔案共用 URI
- Azure Data Lake Storage Gen2 檔案系統/目錄/檔案 URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

下列命令會將資料夾 `C:\local\path` 下的所有檔案，以遞迴方式上傳到在容器中建立 `path` 目錄的容器 `mycontainer1`：

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

下列命令會將資料夾 `C:\local\path` 下的所有檔案 (不會遞迴至子目錄) 上傳到容器 `mycontainer1`：

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

若要取得更多範例，請使用下列命令：

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>在兩個儲存體帳戶之間複製資料

在兩個儲存體帳戶之間複製資料的作業會使用 [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API，而且不會使用用戶端電腦的網路頻寬。 資料會在兩個 Azure 儲存體伺服器之間直接複製，AzCopy 只會協調複製作業。 此選項目前只能供 Blob 儲存體使用。

若要在兩個儲存體帳戶之間複製資料，請使用下列命令：
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> 此命令會列舉所有 Blob 容器，並將它們複製到目的地帳戶。 AzCopy v10 目前只支援在兩個儲存體帳戶之間複製區塊 Blob。 所有其他儲存體帳戶物件 (附加 Blob、分頁 Blob、檔案、資料表和佇列) 則會略過。

## <a name="copy-a-vhd-image-to-a-storage-account"></a>將 VHD 映像複製到儲存體帳戶

AzCopy v10 依預設會將資料上傳至區塊 Blob。 不過，如果來源檔案的副檔名是 vhd，則 AzCopy v10 依預設會將它上傳至分頁 Blob。 此行為目前無法設定。

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>同步處理：累加複製和刪除 (僅限 Blob 儲存體)

> [!NOTE]
> Sync 命令會將內容從來源同步處理到目的地，此外，這也會刪除目的地檔案 (如果這些檔案不存在於來源中)。 請確定您所使用的是想要同步處理的目的地。

若要將本機檔案系統同步處理到儲存體帳戶，請使用下列命令：

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

您可以透過相同方式將 Blob 容器同步處理到本機檔案系統：

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

此命令可讓您根據上一次修改的時間戳記，以累加方式將來源同步處理到目的地。 如果您在來源中新增或刪除檔案，AzCopy v10 也會在目的地進行相同操作。 刪除之前，AzCopy 會提示您確認刪除檔案。

## <a name="advanced-configuration"></a>進階組態

### <a name="configure-proxy-settings"></a>進行 Proxy 設定

若要設定 AzCopy v10 的 Proxy 設定，請使用下列命令設定環境變數 https_proxy：

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>輸送量最佳化

請設定環境變數 AZCOPY_CONCURRENCY_VALUE 來設定並行要求數目，並控制輸送量效能和資源耗用量。 依預設，此值會設定為 300。 減少此值便可限制 AzCopy v10 所使用的頻寬和 CPU。

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

## <a name="troubleshooting"></a>疑難排解

AzCopy v10 會為所有作業建立記錄檔和方案檔。 您可以使用記錄來調查任何可能的問題並進行疑難排解。 記錄會包含失敗狀態 (UPLOADFAILED、COPYFAILED 和 DOWNLOADFAILED)、完整路徑和失敗原因。 作業記錄和方案檔位於 Windows 上的 %USERPROFILE\\.azcopy 資料夾中，或在 Mac 和 Linux 上的 $HOME\\.azcopy 資料夾中。

> [!IMPORTANT]
> 提交支援要求給 Microsoft 支援服務 (或針對涉及任何協力廠商的問題進行疑難排解) 時，請共用您正嘗試執行之命令的編校版本，以確保不會意外與任何人共用 SAS。 您可以在記錄檔開頭找到編校的版本。

### <a name="change-the-location-of-the-log-files"></a>變更記錄檔的位置

您可以視需要變更記錄檔的位置，或避免填滿 OS 磁碟。

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="review-the-logs-for-errors"></a>檢閱記錄以了解錯誤

下列命令會從 04dc9ca9-158f-7945-5933-564021086c79 記錄取得狀態為 UPLOADFAILED 的所有錯誤：

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>檢視和繼續作業

每個傳輸作業都會建立 AzCopy 作業。 您可以使用下列命令檢視作業的歷程記錄：

```azcopy
.\azcopy jobs list
```

若要檢視作業統計資料，請使用下列命令：

```azcopy
.\azcopy jobs show <job-id>
```

若要依狀態篩選傳輸，請使用下列命令：

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

您可以使用識別碼以及 SAS 權杖 (為保安全，此權杖不會持續) 來繼續失敗/已取消的作業：

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

### <a name="change-the-default-log-level"></a>變更預設記錄層級

根據預設，AzCopy 記錄層級設定為 INFO。 如果您想要減少記錄詳細資訊以節省磁碟空間，請使用 ``--log-level`` 選項來覆寫此設定。 可用記錄層級如下：DEBUG、INFO、WARNING、ERROR、PANIC 及 FATAL

## <a name="next-steps"></a>後續步驟

永遠歡迎您提供寶貴的意見。 如果您有任何問題或一般意見，請前往 https://github.com/Azure/azure-storage-azcopy 來提交。 感謝您！
