---
title: 將 Apache Flink 用於 Apache Kafka - Azure 事件中樞 | Microsoft Docs
description: 本文說明如何將 Apache Flink 連線至已啟用 Apache Kafka 的 Azure 事件中樞
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: cb964451d25bb381ee9cca84628852a61e4be9b6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090902"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>使用 Apache Flink 搭配適用於 Apache Kafka 的 Azure 事件中樞
本教學課程說明如何將 Apache Flink 連線至已啟用 Kafka 的事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 Azure 事件中樞支援 [Apache Kafka 1.0 版](https://kafka.apache.org/10/documentation.html)。

使用 Apache Kafka 的其中一項優點，便是能夠連線之架構的生態系統。 已啟用 Kafka 的事件中樞結合了 Kafka 的彈性與 Azure 生態系統的延展性、一致性和支援度。

在本教學課程中，您了解如何：
> [!div class="checklist"]
> * 建立事件中樞命名空間
> * 複製範例專案
> * 執行 Flink 生產者 
> * 執行 Flink 取用者

> [!NOTE]
> 您可在 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) 上取得此範例

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您具有下列必要條件：

* 請參閱[適用於 Apache Kafka 的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)一文。 
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * 在 Ubuntu 上，執行 `apt-get install default-jdk` 來安裝 JDK。
    * 務必設定 JAVA_HOME 環境變數，以指向 JDK 安裝所在的資料夾。
* [下載](http://maven.apache.org/download.cgi)並[安裝 ](http://maven.apache.org/install.html) Maven 二進位封存檔
    * 在 Ubuntu 上，您可以執行 `apt-get install maven` 來安裝 Maven。
* [Git](https://www.git-scm.com/downloads)
    * 在 Ubuntu 上，您可以執行 `sudo apt-get install git` 來安裝 Git。

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間

您需要事件中樞命名空間，才能從任何事件中樞服務傳送或接收。 如需有關事件中樞 Kafka 端點的資訊，請參閱[建立已啟用 Kafka 的事件中樞](event-hubs-create-kafka-enabled.md)。 請務必複製事件中樞連接字串以供稍後使用。

## <a name="clone-the-example-project"></a>複製範例專案

既然您已經有已啟用 Kafka 的事件中樞連接字串，請複製適用於 Kafka 的 Azure 事件中樞存放庫，並瀏覽至 `flink` 子資料夾：

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>執行 Flink 生產者

使用提供的 Flink 生產者範例，傳送訊息到事件中樞服務。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>提供事件中樞 Kafka 端點

#### <a name="producerconfig"></a>producer.config

更新 `producer/src/main/resources/producer.config` 中 `bootstrap.servers` 和 `sasl.jaas.config` 的值，以使用正確的驗證將生產者導向至事件中樞 Kafka 端點。

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>從命令列執行生產者

若要從命令列執行生產者，請從 Maven 中產生 JAR 然後從中執行 (或是使用 Maven 產生 JAR，然後將必要的 Kafka JAR 加入至 classpath，在 Java 中執行)：

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

生產者現在會開始將事件傳送到已啟用 Kafka 的事件中樞 (位於主題 `test`)，並將事件印出至 stdout。

## <a name="run-flink-consumer"></a>執行 Flink 取用者

使用提供的取用者範例，接收來自已啟用 Kafka 之事件中樞的訊息。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>提供事件中樞 Kafka 端點

#### <a name="consumerconfig"></a>consumer.config

更新 `consumer/src/main/resources/consumer.config` 中 `bootstrap.servers` 和 `sasl.jaas.config` 的值，以使用正確的驗證將取用者導向至事件中樞 Kafka 端點。

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>從命令列執行取用者

若要從命令列執行取用者，請從 Maven 中產生 JAR 然後從中執行 (或是使用 Maven 產生 JAR，然後將必要的 Kafka JAR 加入至 classpath，以在 Java 中執行)：

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

如果已啟用 Kafka 的事件中樞有事件 (例如您的生產者也正在執行)，則取用者現在會開始接收來自主題 `test` 的事件。

如需有關將 Flink 連線至 Kafka 的詳細資訊，請參閱 [Flink 的 Kafka 連接器指南](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) \(英文\)。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何將 Apache Flink 連線至已啟用 Kafka 的事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 您已在本教學課程進行期間執行下列步驟： 

> [!div class="checklist"]
> * 建立事件中樞命名空間
> * 複製範例專案
> * 執行 Flink 生產者 
> * 執行 Flink 取用者

若要深入了解事件中樞和適用於 Kafka 的事件中樞，請參閱下列主題：  

- [了解事件中樞](event-hubs-what-is-event-hubs.md)
- [適用於 Apache Kafka 的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)
- [如何建立已啟用 Kafka 的事件中樞](event-hubs-create-kafka-enabled.md)
- [從您的 Kafka 應用程式串流到事件中樞](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [在已啟用 Kafka 的事件中樞中鏡射 Kafka 訊息代理程式](event-hubs-kafka-mirror-maker-tutorial.md)
- [將 Apache Spark 連線到已啟用 Kafka 的事件中樞](event-hubs-kafka-spark-tutorial.md)
- [整合 Kafka Connect 與已啟用 Kafka 的事件中樞](event-hubs-kafka-connect-tutorial.md)
- [將 Akka Streams 連線到已啟用 Kafka 的事件中樞](event-hubs-kafka-akka-streams-tutorial.md)
- [在 GitHub 上探索範例](https://github.com/Azure/azure-event-hubs-for-kafka) \(英文\)
