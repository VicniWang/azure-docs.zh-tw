---
title: 在 HDInsight 中上傳 Apache Hadoop 作業的資料
description: 了解如何使用 Azure 傳統 CLI、Azure 儲存體總管、Azure PowerShell、Hadoop 命令列或 Sqoop 在 HDInsight 中上傳及存取 Apache Hadoop 作業的資料。
keywords: etl hadoop、將資料上傳到 hadoop、hadoop 載入資料
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: 513cc1f0155c5e5499d0bf076d21aff46756d769
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312169"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>在 HDInsight 中上傳 Apache Hadoop 作業的資料

Azure HDInsight 在 Azure 儲存體和 Azure Data Lake Storage (Gen1 和 Gen2) 上提供了功能完整的 Hadoop 分散式檔案系統 (HDFS)。 Azure 儲存體和 Data Lake Storage Gen1 和 Gen2 是設計作為 HDFS 的延伸，以便為客戶提供順暢的體驗。 它們可讓 Hadoop 生態系統中的完整元件集直接在它管理的資料上運作。 Azure 儲存體、Data Lake Storage Gen1 和 Gen2 是不同的檔案系統，但經過最佳化後，都非常適合儲存資料以及計算儲存的資料。 如需關於使用 Azure 儲存體有哪些優點的資訊，請參閱[搭配 HDInsight 使用 Azure Storage][hdinsight-storage]、[搭配 HDInsight 使用 Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) 及[搭配 HDInsight 使用 Data Lake Storage Gen2](../storage/blobs/data-lake-storage-use-hdi-cluster.md)。

## <a name="prerequisites"></a>必要條件

開始進行之前，請注意下列需求：

* Azure HDInsight 叢集。 如需指示，請參閱 [Azure HDInsight 使用者入門][hdinsight-get-started]或[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。
* 下列文章的知識：

    - [搭配 HDInsight 使用 Azure 儲存體][hdinsight-storage]
    - [搭配 HDInsight 使用 Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md)
    - [搭配 HDInsight 使用 Data Lake Storage Gen2](../storage/blobs/data-lake-storage-use-hdi-cluster.md)  

## <a name="upload-data-to-azure-storage"></a>將資料上傳至 Azure 儲存體

## <a name="utilities"></a>公用事業
Microsoft 提供下列公用程式來使用 Azure 儲存體：

| 工具 |  Linux | OS X |  Windows |
| --- |:---:|:---:|:---:|
| [Azure 入口網站](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop 命令](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> Hadoop 命令只能在 HDInsight 叢集上使用。 此命令只允許從本機檔案系統將資料載入 Azure 儲存體中。  


## <a id="commandline"></a>Hadoop 命令列
Hadoop 命令列僅適用於當資料已存在於叢集前端節點時，將資料儲存到 Azure 儲存體 blob。

若要使用 Hadoop 命令，您必須先使用下列其中一個方法連線到前端節點：

* **Windows 型 HDInsight**：[使用遠端桌面進行連線](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **Linux 型 HDInsight**：使用 [SSH 或 PuTTY 進行連線](hdinsight-hadoop-linux-use-ssh-unix.md).

連線之後，您就可以使用下列語法來將檔案上傳到儲存體。

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

例如， `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

因為 HDInsight 的預設檔案系統是位於 Azure 儲存體中，所以 /example/data.txt 實際上是在 Azure 儲存體中。 您也可以用下列語法來參考此檔案：

    wasb:///example/data/data.txt

或

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

如需其他可用來處理檔案的 Hadoop 命令清單，請參閱 [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> 在 Apache HBase 叢集上，寫入資料時所使用的預設區塊大小是 256 KB。 雖然在使用 HBase API 或 REST API 時此大小可正常運作，但使用 `hadoop` 或 `hdfs dfs` 命令寫入大於 ~12 GB 的資料會導致錯誤。 如需詳細資訊，請參閱本文中[在 Blob 上寫入時的儲存體例外狀況](#storageexception)一節。

## <a name="graphical-clients"></a>圖形化用戶端
其他還有數個應用程式也會提供可搭配 Azure 儲存體使用的圖形化介面。 以下資料表提供數個這類應用程式的清單：

| 用戶端 |  Linux | OS X |  Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure 儲存體總管](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [適用於 Microsoft Azure 的 CloudBerry 總管](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>將 Azure 儲存體掛接為本機磁碟機
請參閱[將 Azure 儲存體掛接為本機磁碟機](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)。

## <a name="upload-using-services"></a>使用服務上傳
### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory 服務是完全受控的服務，可將資料儲存、資料處理及資料移動服務組合成有效率、可調整且可靠的資料生產管線。

|儲存體類型|文件|
|----|----|
|Azure Blob 儲存體|[使用 Azure Data Factory 將資料複製到 Azure Blob 儲存體或從該處複製資料](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[使用 Azure Data Factory 將資料複製到 Azure Data Lake Storage Gen1 儲存體或從該處複製資料](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[使用 Azure Data Factory 將資料載入 Azure Data Lake Storage Gen2 中](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop 是一種專門在 Hadoop 和關聯式資料庫之間傳送資料的工具。 此工具可讓您從 SQL Server、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS)，將資料匯入 Hadoop 分散式檔案系統 (HDFS)，使用 MapReduce 或 Hive 轉換 Hadoop 中的資料，然後將資料匯回 RDBMS。

如需詳細資訊，請參閱[搭配 HDInsight 使用 Sqoop][hdinsight-use-sqoop]。

### <a name="development-sdks"></a>開發 SDK
Azure 儲存體也可以使用 Azure SDK，透過下列程式設計語言進行存取：

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

如需安裝 Azure SDK 的詳細資訊，請參閱 [Azure 下載](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>疑難排解
### <a id="storageexception"></a>在 Blob 上寫入時的儲存體例外狀況
**徵兆**：使用 `hadoop` 或 `hdfs dfs` 命令在 HBase 叢集上寫入 ~12 GB 或更大的檔案時，您可能會遇到下列錯誤︰

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**原因**︰在寫入 Azure 儲存體時，HDInsight 叢集上的 HBase 會將區塊大小預設為 256 KB。 雖然這適用於 HBase API 或 REST API，但會導致在使用 `hadoop` 或 `hdfs dfs` 命令列公用程式時發生錯誤。

**解決方式**：使用 `fs.azure.write.request.size` 來指定較大的區塊大小。 您可以使用 `-D` 參數針對每一次使用進行指定。 以下命令是搭配使用此參數與 `hadoop` 命令的範例︰

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

您也可以使用 Apache Ambari 來全域提高 `fs.azure.write.request.size` 的值。 使用下列步驟即可變更 Ambari Web UI 中的值︰

1. 在瀏覽器中，移至叢集的 Ambari Web UI。 這是 https://CLUSTERNAME.azurehdinsight.net，其中 **CLUSTERNAME** 代表叢集名稱。

    出現提示時，請輸入該叢集的管理員名稱和密碼。
2. 在畫面左側選取 [HDFS]，然後選取 [設定] 索引標籤。
3. 在 [篩選...] 欄位中，輸入 `fs.azure.write.request.size`。 這會在頁面中間顯示欄位和目前的值。
4. 將值從 262144 (256 KB) 變更為新值。 例如，4194304 (4 MB)。

![透過 Ambari Web UI 變更值的影像](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

如需有關使用 Ambari 的詳細資訊，請參閱[使用 Apache Ambari Web UI 來管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

## <a name="next-steps"></a>後續步驟
您現在已了解如何將資料匯入 HDInsight，請接著閱讀下列文章以了解如何執行分析：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [以程式設計方式提交 Apache Hadoop 作業][hdinsight-submit-jobs]
* [搭配 HDInsight 使用 Apache Hive][hdinsight-use-hive]
* [搭配 HDInsight 使用 Apache Pig][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
