---
title: 選擇適合資料轉送的 Azure 解決方案 | Microsoft Docs
description: 了解如何選擇根據資料大小和環境中可用的網路頻寬，選擇適合資料轉送的 Azure 解決方案
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/10/2018
ms.author: alkohli
ms.openlocfilehash: b90e84082eb73dde5a2303f166e9aa254c6f3101
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263233"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>選擇適合資料轉送的 Azure 解決方案

本文提供一些常見的 Azure 資料轉送解決方案概觀。 文章也提供視您環境中的網路頻寬與要轉送的資料大小而有所不同的建議選項連結。

## <a name="types-of-data-movement"></a>資料移動的類型

您可以離線方式或透過網路連線來進行資料轉送。 要選擇何種解決方案，取決於您的：

- **資料大小**：要轉送的資料大小，
- **轉送頻率**：一次性或週期性的資料擷取，和
- **網路**：環境中可用來進行資料轉送的頻寬。

資料移動可以是下列類型：

- **使用可寄送的裝置離線轉送**：當您想要進行離線的一次性大量資料轉送時，請使用實體可寄送的裝置。 Microsoft 會寄送磁碟或是安全的特製化裝置給您。 或者，您可以購買自己的磁碟並寄送。 您要將資料複製到裝置，然後將它寄送至要上傳資料的 Azure 位置。  此情況可用的選項是資料箱磁碟、資料箱、Data Box Heavy 和匯入/匯出 (使用自己的磁碟)。

- **網路轉送**：透過您的網路連線，將資料轉送給 Azure。 這有多種方式可以完成。

    - **圖形化介面**：如果您偶爾轉送幾個檔案，且不需要將資料轉送自動化，您可以選擇圖形化介面工具，例如 Azure 儲存體總管或 Azure 入口網站中的 Web 型瀏覽工具。
    - **以指令碼或程式設計方式轉送**：您可以使用我們所提供已最佳化的軟體工具，或是直接呼叫 REST API/SDK。 可用的指令碼工具為 AzCopy、Azure PowerShell 和 Azure CLI。 針對程式設計介面，使用 .NET、Java、Python、Node/JS、C++、Go、PHP 或 Ruby 的 SDK。
    - **內部部署裝置**：我們提供位於您資料中心內部的實體或虛擬裝置，並將透過網路的資料轉送最佳化。 這些裝置也提供常用檔案的本機快取。 實體裝置是 Data Box Edge，而虛擬裝置是資料箱閘道。 兩者都可在您的內部部署中永久執行，並透過網路連線到 Azure。
    - **受控資料管線**：您可以設定雲端管線，定期在數個 Azure 服務、內部部署或兩者的結合之間轉送檔案。 使用 Azure Data Factory 設定和管理資料管線，並移動和轉換資料以供分析。

下圖說明根據可用來轉送的網路頻寬、要轉送的資料大小和轉送頻率，選擇各種 Azure 資料轉送工具的指導方針。

![Azure 資料轉送工具](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

離線轉送裝置的上限：資料箱磁碟、資料箱和 Data Box Heavy 可透過下訂某裝置類型的多個訂單來擴充。*

## <a name="selecting-a-data-transfer-solution"></a>選取資料轉送解決方案

回答下列問題，協助您選取資料轉送解決方案：

- 您的可用網路頻寬有限 (或無頻寬)，而您想要轉送大型資料集？
  
    如果是，請參閱：[案例 1：不使用網路頻寬或透過低速網路頻寬轉送大型資料集](storage-solution-large-dataset-low-network.md)。
- 您想要透過網路轉送大型資料集，且您有中速至高速的網路頻寬？

    如果是，請參閱：[案例 2：使用中速至高速網路頻寬轉送大型資料集](storage-solution-large-dataset-moderate-high-network.md)。
- 您想偶爾透過網路轉送幾個檔案？

    如果是，請參閱[案例 3：使用有限至中速網路頻寬轉送小型資料集](storage-solution-small-dataset-low-moderate-network.md)。
- 您需要以定期間隔的時間點進行資料轉送嗎？

    如果是，請使用下列所述的指令碼/程式設計選項：[案例 4：定期資料轉送](storage-solution-periodic-data-transfer.md)。
- 您需要持續不斷進行資料轉送嗎？

    如果是，請使用下列中的選項：[案例 4：定期資料轉送](storage-solution-periodic-data-transfer.md)。

## <a name="next-steps"></a>後續步驟

- [取得 Azure 儲存體總管簡介](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/) \(英文\)。
- [閱讀 AzCopy 的概觀](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)。
- [使用 Azure PowerShell 搭配 Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [使用 Azure CLI 搭配 Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- 了解：

    - [適用於離線轉送的 Azure 資料箱、Azure 資料箱磁碟和 Azure Data Box Heavy ](https://docs.microsoft.com/azure/databox/)。
    - [適用於線上轉送的 Azure 資料箱閘道和 Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/)。
- [了解 Azure Data Factory 是什麼](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)。
- 使用 REST API 轉送資料

    - [使用 .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [使用 Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
