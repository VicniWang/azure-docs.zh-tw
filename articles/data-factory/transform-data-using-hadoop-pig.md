---
title: 使用 Azure Data Factory 中的 Hadoop Pig 活動轉換資料 | Microsoft Docs
description: 了解如何使用 Azure 資料處理站中的 Pig 活動，以在隨選/您自己的 HDInsight 叢集上執行 Pig 指令碼。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: a29bd64c6b18d41028c8952f531698bbfa9e01e2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014704"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>使用 Azure Data Factory 中的 Hadoop Pig 活動轉換資料
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](v1/data-factory-pig-activity.md)
> * [目前的版本](transform-data-using-hadoop-pig.md)

Data Factory [管線](concepts-pipelines-activities.md)中的 HDInsight Pig 活動會在您[自己](compute-linked-services.md#azure-hdinsight-linked-service)或[隨選](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)的 HDInsight 叢集上執行 Pig 查詢。 本文是根據 [資料轉換活動](transform-data.md) 一文，它呈現資料轉換和支援的轉換活動的一般概觀。

如果您是 Azure Data Factory 的新手，請在閱讀本文之前先閱讀 [Azure Data Factory 簡介](introduction.md)，以及研習[教學課程：轉換資料](tutorial-transform-data-spark-powershell.md)。 

## <a name="syntax"></a>語法

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>語法詳細資料

| 屬性            | 說明                              | 必要 |
| ------------------- | ---------------------------------------- | -------- |
| name                | 活動的名稱                     | 是      |
| 說明         | 說明活動用途的文字 | 否       |
| type                | 對於 Hive 活動，活動類型為 HDinsightPig | 是      |
| 預設容器   | 參考 HDInsight 叢集註冊為 Data Factory 中的連結服務。 若要深入了解此已連結的服務，請參閱[計算已連結的服務](compute-linked-services.md)一文。 | 是      |
| scriptLinkedService | Azure 儲存體已連結的服務用來儲存要執行之 Pig 指令碼的參考。 如果您未指定這項連結服務，則會使用 HDInsight 已連結的服務中定義的 Azure 儲存體已連結的服務。 | 否       |
| scriptPath          | 提供儲存在 scriptLinkedService 引用之 Azure 儲存體中指令碼檔案的路徑。 檔案名稱有區分大小寫。 | 否       |
| getDebugInfo        | 指定何時將記錄檔複製到 HDInsight 叢集所使用 (或) scriptLinkedService 所指定的 Azure 儲存體。 允許的值：None、Always 或 Failure。 預設值：無。 | 否       |
| arguments           | 指定 Hadoop 作業的引數陣列。 引數會以命令列引數的方式傳遞給每項工作。 | 否       |
| 定義             | 在使用 Pig 指令碼內指定參數做為參考的機碼/值組。 | 否       |

## <a name="next-steps"></a>後續步驟
請參閱下列文章，其說明如何以其他方式轉換資料： 

* [U-SQL 活動](transform-data-using-data-lake-analytics.md)
* [Hive 活動](transform-data-using-hadoop-hive.md)
* [MapReduce 活動](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 串流活動](transform-data-using-hadoop-streaming.md)
* [Spark 活動](transform-data-using-spark.md)
* [.NET 自訂活動](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning 批次執行活動](transform-data-using-machine-learning.md)
* [預存程序活動](transform-data-using-stored-procedure.md)
