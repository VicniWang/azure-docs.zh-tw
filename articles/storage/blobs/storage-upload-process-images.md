---
title: 使用 Azure 儲存體在雲端中上傳影像資料 | Microsoft Docs
description: 使用 Azure Blob 儲存體搭配 Web 應用程式來儲存應用程式資料
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 01404c89665ebfea62e7bda0e7566289bb15f2ae
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300953"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>教學課程：使用 Azure 儲存體在雲端中上傳影像資料

本教學課程是一個系列的第一部分。 本教學課程會示範如何部署 Web 應用程式，其使用 Azure 儲存體用戶端程式庫將映像上傳至儲存體帳戶。 完成後，您就有可儲存和顯示 Azure 儲存體映像資料的 Web 應用程式。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![影像容器檢視](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![影像容器檢視](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
![影像容器檢視](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

在系列的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 建立儲存體帳戶
> * 建立容器並設定權限
> * 擷取存取金鑰
> * 將 Web 應用程式部署至 Azure
> * 進行應用程式設定
> * 與 Web 應用程式互動

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要 Azure 訂用帳戶。 開始之前，請建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，本教學課程要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>建立資源群組 

使用 [az group create](/cli/azure/group) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。  

下列範例會建立名為 `myResourceGroup` 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶

此範例會將影像上傳至 Azure 儲存體帳戶的 Blob 容器。 儲存體帳戶提供唯一命名空間來儲存及存取您的 Azure 儲存體資料物件。 在使用 [az storage account create](/cli/azure/storage/account) 命令所建立的資源群組中建立儲存體帳戶。

> [!IMPORTANT]
> 在本教學課程的第 2 部分中，您要使用 Azure 事件格線搭配 Blob 儲存體。 請務必在支援事件格線的 Azure 區域中建立儲存體帳戶。 如需支援的區域清單，請參閱[不同區域的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

在下列命令中，使用您自己的全域唯一名稱來取代見到 `<blob_storage_account>` 預留位置的 Blob 儲存體帳戶。  

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

az storage account create --name $blobStorageAccount \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>建立 Blob 儲存體容器

應用程式在 Blob 儲存體帳戶中使用兩個容器。 容器類似資料夾，可儲存 Blob。 應用程式會將高解析度的影像上傳到 [影像] 容器。 在系列的後半部分，Azure 函式應用程式會將已調整大小的影像縮圖上傳到 [縮圖] 容器。

使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令取得儲存體帳戶金鑰。 接著，使用此金鑰透過 [az storage container create](/cli/azure/storage/container) 命令來建立兩個容器。  

images 容器的公用存取設為 `off`。 thumbnails 容器的公用存取設為 `container`。 `container` 公用存取設定允許使用者瀏覽網頁來檢視縮圖。

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

請記下您的 Blob 儲存體帳戶名稱和金鑰。 範例應用程式會使用下列設定連線到儲存體帳戶，以上傳映像。 

## <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[App Service 方案](../../app-service/overview-hosting-plans.md)會指定用來裝載應用程式的 Web 伺服器陣列位置、大小和功能。

使用 [az appservice plan create](/cli/azure/appservice/plan) 命令來建立 App Service 方案。

下列範例會在**免費**定價層中建立名為 `myAppServicePlan` 的 App Service 方案。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>建立 Web 應用程式

Web 應用程式提供裝載範例應用程式程式碼的空間，此程式碼是從 GitHub 範例存放庫部署。 使用 [az webapp create](/cli/azure/webapp) 命令，在 `myAppServicePlan` App Service 方案中建立 [Web 應用程式](../../app-service/overview.md)。  

在下列命令中，使用唯一的名稱取代 `<web_app>`。 有效字元是 `a-z`、`0-9` 和 `-`。 如果 `<web_app>` 不是唯一的，您會收到錯誤訊息：_具有指定名稱 `<web_app>` 的網站已經存在。_ Web 應用程式的預設 URL 是 `https://<web_app>.azurewebsites.net`。  

```azurecli-interactive
webapp=<web_app>

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>從 GitHub 存放庫部署範例應用程式

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

應用程式服務支援數種將內容部署至 Web 應用程式的方法。 在本教學課程中，您會從[公用 GitHub 範例存放庫](https://github.com/Azure-Samples/storage-blob-upload-from-webapp)部署 Web 應用程式。 使用 [az webapp deployment source config](/cli/azure/webapp/deployment/source) 命令設定 Web 應用程式的 GitHub 部署。

範例專案包含 [ASP.NET MVC](https://www.asp.net/mvc) 應用程式。 此應用程式可接受映像、將它儲存到儲存體帳戶，顯示縮圖容器中的映像。 Web 應用程式使用來自 Azure 儲存體用戶端程式庫的 [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet)、[Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) 和 [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) 命名空間與 Azure 儲存體進行互動。

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
應用程式服務支援數種將內容部署至 Web 應用程式的方法。 在本教學課程中，您會從[公用 GitHub 範例存放庫](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node)部署 Web 應用程式。 使用 [az webapp deployment source config](/cli/azure/webapp/deployment/source) 命令設定 Web 應用程式的 GitHub 部署。 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
應用程式服務支援數種將內容部署至 Web 應用程式的方法。 在本教學課程中，您會從[公用 GitHub 範例存放庫](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10)部署 Web 應用程式。 使用 [az webapp deployment source config](/cli/azure/webapp/deployment/source) 命令設定 Web 應用程式的 GitHub 部署。 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>設定 Web 應用程式設定

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

範例 Web 應用程式使用 [Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)要求存取權杖，其用來上傳影像。 儲存體 SDK 所使用的儲存體帳戶認證是在 Web 應用程式的應用程式設定中設定。 使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) 命令將應用程式設定新增至已部署的應用程式。

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=$blobStorageAccountKey  
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

範例 Web 應用程式使用 [Azure 儲存體用戶端程式庫](https://docs.microsoft.com/javascript/api/azure-storage)要求存取權杖，其用來上傳影像。 儲存體 SDK 所使用的儲存體帳戶認證是在 Web 應用程式的應用程式設定中設定。 使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) 命令將應用程式設定新增至已部署的應用程式。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__AccountKey=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

範例 Web 應用程式使用 [Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-storage-js)要求存取權杖，其用來上傳影像。 儲存體 SDK 所使用的儲存體帳戶認證是在 Web 應用程式的應用程式設定中設定。 使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) 命令將應用程式設定新增至已部署的應用程式。

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

部署及設定 Web 應用程式之後，您可以在應用程式中測試映像上傳功能。

## <a name="upload-an-image"></a>上傳影像

若要測試 Web 應用程式，請瀏覽至已發佈應用程式的 URL。 Web 應用程式的預設 URL 是 `https://<web_app>.azurewebsites.net`。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

選取 [上傳相片] 區域以選取並上傳檔案，或將檔案拖曳到區域。 如果上傳成功，影像就會消失。 [產生的縮圖] 區段就會維持空白，直到我們在本主題稍後對其完成測試。

![ImageResizer 應用程式](media/storage-upload-process-images/figure1.png)

在範例程式碼中，Storagehelper.cs 檔案中的 `UploadFiletoStorage` 工作，可供使用 [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) 方法將映像上傳至儲存體帳戶內的 images 容器。 下列程式碼範例包含 `UploadFiletoStorage` 工作。

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

以下是前面工作中使用的類別和方法：

|類別  |方法  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

選取 [選擇檔案] 來選取檔案，然後按一下 [上傳映像]。 [產生的縮圖] 區段就會維持空白，直到我們在本主題稍後對其完成測試。 

![影像上傳應用程式](media/storage-upload-process-images/upload-app-nodejs.png)

在範例程式碼中，`post` 路由會負責將影像上傳至 Blob 容器中。 此路由會使用模組來協助處理上傳作業：

- [multer](https://github.com/expressjs/multer) 會實作路由處理常式的上傳策略。
- [into-stream](https://github.com/sindresorhus/into-stream) 會將緩衝區轉換為 [createBlockBlobFromStream] 所需的資料流。(http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)

當檔案傳送至路由時，檔案的內容在檔案上傳至 Blob 容器之前都會留在記憶體中。

> [!IMPORTANT]
> 將大型檔案載入記憶體中，可能會對 Web 應用程式的效能產生負面影響。 如果您預期使用者會發佈大型檔案，則可能應考慮在 Web 伺服器檔案系統上使用暫存檔案，然後排程上傳至 Blob 儲存體的作業。 在檔案放入 Blob 儲存體後，您即可將其從伺服器檔案系統中移除。

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

選取 [選擇檔案] 來選取檔案，然後按一下 [上傳映像]。 [產生的縮圖] 區段就會維持空白，直到我們在本主題稍後對其完成測試。 

![影像上傳應用程式](media/storage-upload-process-images/upload-app-nodejs.png)

在範例程式碼中，`post` 路由會負責將影像上傳至 Blob 容器中。 此路由會使用模組來協助處理上傳作業：

- [multer](https://github.com/expressjs/multer) 會實作路由處理常式的上傳策略。
- [into-stream](https://github.com/sindresorhus/into-stream) 會將緩衝區轉換為 [createBlockBlobFromStream](http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html) 所需的資料流。

當檔案傳送至路由時，檔案的內容在檔案上傳至 Blob 容器之前都會留在記憶體中。

> [!IMPORTANT]
> 將大型檔案載入記憶體中，可能會對 Web 應用程式的效能產生負面影響。 如果您預期使用者會發佈大型檔案，則可能應考慮在 Web 伺服器檔案系統上使用暫存檔案，然後排程上傳至 Blob 儲存體的作業。 在檔案放入 Blob 儲存體後，您即可將其從伺服器檔案系統中移除。

```javascript
const {
  Aborter,
  BlobURL,
  BlockBlobURL,
  ContainerURL,
  ServiceURL,
  StorageURL,
  SharedKeyCredential,
  uploadStreamToBlockBlob
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };
const ONE_MINUTE = 60 * 1000;
const aborter = Aborter.timeout(30 * ONE_MINUTE);

const sharedKeyCredential = new SharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(sharedKeyCredential);
const serviceURL = new ServiceURL(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, ''); 
  return `${identifier}-${originalName}`;
};

router.post('/', uploadStrategy, async (req, res) => {

    const blobName = getBlobName(req.file.originalname);
    const stream = getStream(req.file.buffer);
    const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
    const blobURL = BlobURL.fromContainerURL(containerURL, blobName);
    const blockBlobURL = BlockBlobURL.fromBlobURL(blobURL);

    try {
      
      await uploadStreamToBlockBlob(aborter, stream,
        blockBlobURL, uploadOptions.bufferSize, uploadOptions.maxBuffers);

      res.render('success', { message: 'File uploaded to Azure Blob storage.' });   

    } catch (err) {

      res.render('error', { message: 'Something went wrong.' });

    }
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>確定影像顯示在儲存體帳戶中

登入 [Azure 入口網站](https://portal.azure.com)。 從左側的功能表中選取 [儲存體帳戶]，然後選取您的儲存體帳戶名稱。 在 [Blob 服務] 之下，選取 [Blob]，然後選取 **images** 容器。

確認影像顯示在容器中。

![影像容器檢視](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>測試縮圖檢視

若要測試縮圖檢視，您要將映像上傳至 **thumbnails** 容器，以檢查應用程式是否否讀取 **thumbnails** 容器。

登入 [Azure 入口網站](https://portal.azure.com)。 從左側的功能表中選取 [儲存體帳戶]，然後選取您的儲存體帳戶名稱。 在 [Blob 服務] 之下，選取 [Blob]，然後選取 **thumbnails** 容器。 選取 [上傳] 開啟 [上傳 Blob] 窗格。

使用檔案選擇器選擇檔案，並選取 [上傳]。

巡覽回您的應用程式，確認可以看到上傳至 [縮圖] 容器的影像。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![影像容器檢視](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![影像容器檢視](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
![影像容器檢視](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

在本系列的第二部分，您會自動建立縮圖映像，因此您不需要此映像。 在 Azure 入口網站的 [縮圖] 容器中，選取您上傳的影像，然後選取 [刪除] 來刪除影像。 

您可以啟用 CDN，從 Azure 儲存體帳戶快取內容。 如需如何在 Azure 儲存體帳戶啟用 CDN 的詳細資訊，請參閱[整合 Azure 儲存體帳戶與 Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md)。

## <a name="next-steps"></a>後續步驟

在本系列的第一部分中，您了解到如何設定 Web 應用程式來與儲存體互動。

繼續本系列的第二個部分，了解使用事件格線觸發 Azure 函式，以調整映像大小。

> [!div class="nextstepaction"]
> [使用事件格線觸發 Azure 函式以調整上傳的影像大小](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
