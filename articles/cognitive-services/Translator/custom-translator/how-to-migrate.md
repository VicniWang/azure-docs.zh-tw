---
title: 要移轉 Microsoft Translator Hub 工作區和專案嗎？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 將您的中樞工作區和專案移轉至自訂翻譯工具。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: b8347a8c34cf5a0585e9bb6c247102207a70015a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225613"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>將中樞工作區和專案移轉至自訂翻譯工具

您可以輕鬆地將 [Microsoft Translator Hub](https://hub.microsofttranslator.com/) 工作區和專案移轉至自訂翻譯工具。 若要從 Microsoft Hub 起始移轉，只要選取工作區或專案，然後選取自訂翻譯工具中的工作區，再選取您要移轉的訓練即可。 移轉開始後，選取的訓練設定將會連同所有相關的文件一起移轉。 已部署的模型已定型，並且可在完成後自動部署。

在移轉期間會執行下列動作：
* 所有文件和專案定義的名稱都會在前面加上 "hub_" 再進行移轉。 自動產生的測試和調整資料將會命名為 hub_systemtune_\<modelid> 或 hub_systemtest_\<modelid>。
* 任何在移轉執行時處於已部署狀態的訓練，都將使用 Hub 訓練的文件自動定型。 此訓練不會對您的訂用帳戶產生費用。 如果在移轉時選取了自動部署，已定型的模型就會在完成後隨即部署。 這適用一般裝載費用。
* 任何已移轉、但未處於已部署狀態的訓練，都會進入已移轉的草稿狀態中。 在此狀態下，您可以選擇使用已移轉的定義為模型定型，但需支付一般訓練費用。
* 您隨時都可在模型的 TrainingDetails 頁面中找到從 Hub 訓練移轉的 BLEU 分數，其標題為「MT Hub 中的 Bleu 分數」。

>[!Note]
>若要成功定型，自訂翻譯工具至少需要 10,000 個擷取的句子。 如果擷取的句數少於[建議的最小值](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences)，自訂翻譯工具就無法執行定型。

## <a name="enable-account-migration"></a>啟用帳戶移轉

若要使用移轉工具，您必須啟用 Hub 帳戶移轉。 若要這樣做，請將電子郵件傳送至 [custommt@microsoft.com](mailto:custommt@microsoft.com)，並在信中列出您要啟用的所有 LiveID 帳戶。 這些帳戶應為您登入時使用的電子郵件地址。

## <a name="find-custom-translator-workspace-id"></a>尋找自訂翻譯工具工作區識別碼

若要移轉 [Microsoft Translator Hub](https://hub.microsofttranslator.com/) 工作區，您必須要有自訂翻譯工具中的目的地工作區識別碼。 自訂翻譯工具中的目的地工作區，是您所有的中樞工作區和專案所應移轉到的位置。

您可以在自訂翻譯工具的 [設定] 頁面上找到目的地工作區識別碼：

1. 移至自訂翻譯工具入口網站中的 [設定] 頁面。

2. 您可以在 [基本資訊] 區段中找到工作區識別碼。

    ![如何尋找目的地工作區識別碼](media/how-to/how-to-find-destination-ws-id.png)

3. 妥善保存您的目的地工作區識別碼，以便在移轉程序期間參考。

## <a name="migrate-a-project"></a>移轉專案

如果您想要選擇性地移轉專案，可以使用 Microsoft Translator Hub 所提供的功能。

若要移轉專案：

1. 登入 Microsoft Translator Hub。

2. 移至 [專案] 頁面。

3. 按一下適當專案的 [移轉] 連結。

    ![如何從中樞移轉](media/how-to/how-to-migrate-from-hub.png)

4. 按下移轉連結後，您將會看到可讓您執行下列動作的表單：
   * 指定要移轉至自訂翻譯工具的工作區
   * 指出您是要移轉所有成功的訓練，或只是已部署的訓練。 依預設會移轉所有成功的訓練。
   * 指出您是否要讓訓練在訓練完成後自動部署。 根據預設，您的訓練並不會在完成時自動部署。

5. 按一下 [提交要求]。

## <a name="migrate-a-workspace"></a>移轉工作區

除了移轉單一專案以外，您也可以移轉工作區中所有包含成功訓練的專案。 這會使工作區中的每個專案都受到評估，就像已按下移轉連結一樣。 有許多專案的使用者若想要將其所有專案以相同的設定移轉至自訂翻譯工具，就適用這項功能。 工作區移轉可以從 Translator Hub 的 [設定] 頁面起始。

若要移轉工作區：

1. 登入 Microsoft Translator Hub。

2. 移至 [設定] 頁面。

3. 在 [設定] 頁面上，按一下 [將工作區資料移轉至自訂翻譯工具]。

    ![如何從中樞移轉](media/how-to/how-to-migrate-workspace-from-hub.png)

4. 在下一個頁面上，選取下列兩個選項之一：

    a. 僅限已部署的訓練：若選取此選項，將只會移轉您已部署的系統和相關文件。

    b. 所有成功的訓練：若選取此選項，將會移轉您所有成功的訓練和相關文件。

    c. 輸入您在自訂翻譯工具中的目的地工作區識別碼。

    ![如何從中樞移轉](media/how-to/how-to-migrate-from-hub-screen.png)

5. 按一下 [提交要求]。

## <a name="migration-history"></a>移轉歷程記錄

如果您要求從中樞進行工作區/專案移轉，您將可在自訂翻譯工具的 [設定] 頁面中找到移轉歷程記錄。

若要檢視移轉歷程記錄，請遵循下列步驟：

1. 移至自訂翻譯工具入口網站中的 [設定] 頁面。

2. 在 [設定] 頁面的 [移轉歷程記錄] 區段中，按一下 [移轉歷程記錄]。

    ![移轉歷程記錄](media/how-to/how-to-migration-history.png)

[移轉歷程記錄] 頁面會針對您所要求的各個移轉顯示下列摘要資訊。

1. 移轉者：提交此移轉要求的使用者名稱及其電子郵件

2. 移轉日期：移轉的日期和時間戳記

3. 專案：要求移轉的專案數目與成功移轉的專案數目。

4. 訓練：要求移轉的訓練數目與成功移轉的訓練數目。

5. 文件：要求移轉的文件數目與成功移轉的文件數目。

    ![移轉歷程記錄詳細資料](media/how-to/how-to-migration-history-details.png)

如果您希望專案、定型和文件有更詳細的相關移轉報告，您可以選擇將詳細資料匯出為 CSV。

## <a name="implementation-notes"></a>實作附註
* 將專案從 Hub 移轉至自訂翻譯工具，並不會對您的 Hub 訓練或專案造成任何影響。 在移轉期間，我們不會從 Hub 中刪除專案或文件，也不會將模型解除部署。
* 您的每個專案只能移轉一次。 如果您需要重複移轉某個專案，請與我們連絡。
* 目前，自訂翻譯工具支援 36 種語言與英文互譯，而我們正努力新增更多語言。 Hub 不需要基準模型，因此可支援數千種語言。 您可以移轉不受支援的語言組，但我們只會執行文件和專案定義的移轉。 我們無法為新模型定型。 此外，這些文件和專案將會顯示為非作用中，以指出它們目前無法使用。 如果新增了這些專案和 (或) 文件的支援，它們就會變成作用中狀態，且可以定型。
* 自訂翻譯工具目前不支援單一語言定型資料。 和不受支援的語言組一樣，您可以移轉單一語言文件，但在單一語言資料受到支援之前，這些文件都會顯示為非作用中。
* 自訂翻譯工具需要 10k 個平行句子才能執行定型。 Microsoft Hub 無法對較小的資料集定型。 不符合此需求的訓練若進行移轉，則不會定型。

## <a name="custom-translator-versus-hub"></a>自訂翻譯工具與中樞

下表比較 Microsoft Translator Hub 與自訂翻譯工具之間的功能。

|   | 中樞 | 自訂翻譯工具 |
|:-----|:----:|:----:|
|自訂功能狀態   | 正式運作  | 正式運作 |
| 文字 API 版本  | V2    | V3  |
| SMT 自訂 | 是   | 否 |
| NMT 自訂 | 否    | 是 |
| 新的統一語音服務自訂 | 否    | 是 |
| 不追蹤 | 是 | 是 |

## <a name="next-steps"></a>後續步驟

- [將模型定型](how-to-train-model.md)。
- 透過 [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 開始使用您已部署的自訂翻譯模型。
