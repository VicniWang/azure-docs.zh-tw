---
title: 使用 Log Analytics 監視 Azure HDInsight 叢集
description: 了解如何使用 Azure Log Analytics，以監視在 HDInsight 叢集中執行的作業。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: cd129ea68315223516ac1cd3e7577b5ee4bf92e5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005109"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>使用 Azure Log Analytics 監視 HDInsight 叢集

了解如何啟用 Azure Log Analytics 以監視 HDInsight 中的 Hadoop 叢集作業，以及如何新增 HDInisght 監視解決方案。

[Log Analytics](../log-analytics/log-analytics-overview.md) 是 Azure 監視器 中的一項服務，可監視您的雲端和內部部署環境，以維護其可用性和效能。 它會收集您的雲端和內部部署環境中的資源所產生的資料，以及從其他監視工具提供橫跨多個來源的分析。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

* **Log Analytics 工作區**。 您可以將此工作區視為唯一的 Log Analytics 環境，有其自己的資料存放庫、資料來源和方案。 如需指示，請參閱[建立 Log Analytics 工作區](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)。

* **Azure HDInsight 叢集**。 目前，您可以使用具有以下 HDInsight 叢集類型的 Log Analytics：

  * Hadoop
  * hbase
  * 互動式查詢
  * Kafka
  * Spark
  * Storm

  如需如何建立 HDInsight 叢集的指示，請參閱[開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

> [!NOTE]  
> 建議您將 HDInsight 叢集和 Log Analytics 工作區放在相同的區域中，以提升效能。 Azure Log Analytics 並非適用於所有 Azure 區域。

## <a name="enable-log-analytics-by-using-the-portal"></a>使用入口網站啟用 Log Analytics

在本節中，您會設定現有 HDInsight Hadoop 叢集，以使用 Azure Log Analytics 工作區來監視作業、偵錯記錄等等。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 從左側功能表中選取 [所有服務]。

3. 在 [分析] 底下，選取 [HDInsight 叢集]。

4. 在左邊的 [監視]下方，選取 [Operations Management Suite]。

5. 從主要檢視中的 [OMS 監視] 下方，選取 [啟用]。

6. 從 [選取工作區] 下拉式清單中，選取現有的 Log Analytics 工作區。

7. 選取 [ **儲存**]。

    ![針對 HDInsight 叢集啟用監視](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "針對 HDInsight 叢集啟用監視")

    需要一些時間來儲存設定。

## <a name="enable-log-analytics-by-using-azure-powershell"></a>使用 Azure PowerShell 啟用 Log Analytics

您可以使用 Azure PowerShell 啟用 Log Analytics。 此 Cmdlet 是：

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

請參閱 [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0)。

若要停用，則 Cmdlet 是：

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

請參閱 [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0)。

## <a name="install-hdinsight-cluster-management-solutions"></a>安裝 HDInsight 叢集管理解決方案

HDInsight 提供叢集特定的管理解決方案，您可以為 Azure Log Analytics 新增。 [管理解決方案](../log-analytics/log-analytics-add-solutions.md)可將功能新增至 Log Analytics，以提供其他資料和分析工具。 這些解決方案會從您的 HDInsight 叢集收集重要效能計量，並且提供工具以搜尋計量。 這些解決方案也會針對 HDInsight 中支援的大部分叢集類型，提供視覺效果和儀表板。 藉由使用您以解決方案收集的計量，您可以建立自訂的監視規則和警示。

可用的 HDInsight 解決方案如下：

* HDInsight Hadoop 監視
* HDInsight HBase 監視
* HDInsight 互動式查詢監視
* HDInsight Kafka 監視
* HDInsight Spark 監視
* HDInsight Storm 監視

如需安裝管理解決方案的指示，請參閱[在 Azure 中的管理解決方案](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)。 若要進行實驗，請安裝 HDInsight Hadoop 監視解決方案。 完成後，您會看到 **HDInsightHadoop** 圖格列示於 [摘要] 下方。 選取 **HDInsightHadoop** 圖格。 HDInsightHadoop 解決方案顯示如下：

![HDInsight 監視解決方案檢視](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

由於叢集是全新的叢集，因此報表不會顯示任何活動。

## <a name="next-steps"></a>後續步驟

* [查詢 Azure Log Analytics 以監視 HDInsight 叢集](hdinsight-hadoop-oms-log-analytics-use-queries.md)
