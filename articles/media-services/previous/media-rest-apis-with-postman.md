---
title: 設定 Postman 以進行 Azure 媒體服務 REST API 呼叫
description: 了解如何設定 Postman 以進行媒體服務 REST API 呼叫。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 71f96420fe99ec230c9b25936fb6af4c993f615c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000978"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>設定 Postman 以進行媒體服務 REST API 呼叫  

本教學課程說明如何設定 **Postman**，以便用來呼叫 Azure 媒體服務 (AMS) REST API。 本教學課程說明如何將環境和集合檔案匯入 **Postman**。 集合包含呼叫 Azure 媒體服務 (AMS) REST API 之 HTTP 要求的分組定義。 環境檔案包含集合所使用的變數。

說明如何使用 Azure 媒體服務 REST API 來完成各項工作的文章中，都會使用此環境和集合。

## <a name="prerequisites"></a>必要條件

- 安裝 [Postman](https://www.getpostman.com/) \(英文\) REST 用戶端，來執行在某些 AMS REST 教學課程中所示範的 REST API。 

    我們使用的是 **Postman**，但任何 REST 工具都適用。 其他替代方式為：搭配 REST 外掛程式的 **Visual Studio Code**，或 **Telerik Fiddler**。 

## <a name="configure-the-environment"></a>建立環境 

1. 建立包含用於 AMS 教學課程中之環境變數的 .json 檔案。 命名檔案 (例如，**AzureMediaServices.postman_environment.json**)。 開啟該檔案，並將[此程式碼清單](postman-environment.md)中定義 Postman 環境的程式碼貼上。 
2. 開啟 **Postman**。
3. 選取畫面右側的 [管理環境] 選項。

    ![上傳檔案](./media/media-services-rest-upload-files/postman-create-env.png)
4. 按一下 [管理環境] 對話方塊中的 [匯入]。
5. 瀏覽並選取 **AzureMediaServices.postman_environment.json** 檔案。
6. 隨即新增 **AzureMedia** 環境。
7. 關閉對話方塊。
8. 選取 **AzureMedia** 環境。

    ![上傳檔案](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>設定集合

1. 建立包含 **Postman** 集合的 .json 檔案，該集合具有將檔案上傳至媒體服務所需的所有作業。 命名檔案 (例如，**AzureMediaServicesOperations.postman_collection.json**)。 開啟該檔案，並將[此程式碼清單](postman-collection.md)中定義 **Postman** 集合的程式碼貼上。
2. 按一下 [匯入] 來匯入集合檔案。
3. 選擇 **AzureMediaServicesOperations.postman_collection.json** 檔案。

    ![上傳檔案](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>後續步驟

請參閱[上傳資產](media-services-rest-upload-files.md)一文。  
