---
title: 快速入門：使用適用於 Python 的自訂視覺 SDK 建立影像分類專案
titlesuffix: Azure Cognitive Services
description: 建立專案、新增標籤、上傳影像、為您的專案定型，以及使用 Python SDK 進行預測。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 11/2/2018
ms.author: areddish
ms.openlocfilehash: 551d713d6cc5c6ae8024d4784dbc3a8cca79b401
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854824"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>快速入門：使用自訂視覺 Python SDK 建立影像分類專案

本文提供資訊和範例程式碼，可協助您開始使用自訂視覺 SDK 與 Python 來建置影像分類模型。 建立它之後，您就可以新增標記、上傳影像、為專案定型、取得專案的預設預測端點 URL，並使用端點以程式設計方式測試影像。 請使用此範例作為範本來建置您自己的 Python 應用程式。 如果您想要進行「不用」程式碼來建置及使用分類模型的程序，請改為參閱[以瀏覽器為基礎的指引](getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>必要條件

- [Python 2.7+ 或 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 工具

## <a name="install-the-custom-vision-sdk"></a>安裝自訂視覺 SDK

若要安裝適用於 Python 的自訂視覺服務 SDK，請在 PowerShell 中執行下列命令：

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>新增程式碼

在您偏好的專案目錄中建立名為 sample.py 的新檔案。

### <a name="create-the-custom-vision-service-project"></a>建立自訂視覺服務專案

在指令碼中新增下列程式碼，以建立新的自訂視覺服務專案。 在適當的定義中插入訂用帳戶金鑰。

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>在專案中建立標記

若要在專案中建立分類標記，請在 sample.py 結尾新增以下程式碼：

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>上傳和標記影像

若要將範例影像新增到專案，在標記建立之後插入下列程式碼。 此程式碼會上傳每個影像及其對應標記。 您必須根據認知服務 Python SDK 範例專案的下載位置，來輸入基底影像 URL 路徑。

> [!NOTE]
> 您必須根據認知服務 Python SDK 範例專案稍早的下載位置，來變更影像的路徑。

```Python
base_image_url = "<path to project>"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])
```

### <a name="train-the-classifier"></a>為分類器定型

此程式碼會在專案中建立第一個反覆項目，並將其設定為預設反覆項目。 預設反覆項目會反映將會回應預測要求的模型版本。 每次重新訓練模型時，均應更新此反覆項目。

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>取得並使用預設預測端點

若要將影像傳送到預測端點並擷取預測，在檔案結尾處新增以下程式碼：

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>執行應用程式

執行 sample.py。

```PowerShell
python sample.py
```

應用程式的輸出應該會類似下列文字：

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

接著，您可以確認測試影像 (位於 **<base_image_url>/Images/Test/**) 的標記是否適當。 您也可以返回[自訂視覺網站](https://customvision.ai)，然後查看新建立專案的目前狀態。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>後續步驟

現在您已經知道如何在程式碼中完成影像分類程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。

> [!div class="nextstepaction"]
> [測試和重新定型模型](test-your-model.md)
