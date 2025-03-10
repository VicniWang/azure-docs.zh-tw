---
title: 將 Docker 映像推送至私人 Azure 容器登錄
description: 使用 Docker CLI 推送和提取 Docker 映像至 Azure 中的私人容器登錄
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 164d705a16dd82a1c5f3ff6f5e6982f80eb40dab
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330863"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>使用 Docker CLI 將您的第一個映像推送至私人 Docker 容器登錄

Azure 容器登錄庫儲存和管理私人 [Docker](http://hub.docker.com) 容器映像，其方式類似於 [Docker 中樞](https://hub.docker.com/)儲存公用 Docker 映像。 您可以使用 [Docker 命令列介面](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) 進行[登入](https://docs.docker.com/engine/reference/commandline/login/)、[推送](https://docs.docker.com/engine/reference/commandline/push/)、[提取](https://docs.docker.com/engine/reference/commandline/pull/)和其他容器登錄庫作業。

在下列步驟中，您會從公用 Docker 中樞登錄庫下載官方提供的 [Nginx 映像](https://store.docker.com/images/nginx)，將其標記為私人 Azure 容器登錄庫，推送到您的登錄庫，然後再從登錄庫將其提取出來。

## <a name="prerequisites"></a>必要條件

* **Azure 容器登錄庫** - 在 Azure 訂用帳戶中建立容器登錄庫。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
* **Docker CLI** - 您也必須在本機上安裝 Docker。 Docker 提供可輕鬆在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。

## <a name="log-in-to-a-registry"></a>登入登錄庫

您的私人容器登錄庫有[數種方式可進行驗證](container-registry-authentication.md)。 在命令列中工作時，建議的方法是使用 Azure CLI 命令 [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login)。 例如，若要登入名為 myregistry 的登錄庫：

```azurecli
az acr login --name myregistry
```

您也可以使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 來登入。 例如，您可能已基於自動化案例[指派服務主體](container-registry-authentication.md#service-principal)到您的登錄庫。 當您執行下列命令時，若出現提示，請以互動方式提供服務主體 appID (使用者名稱) 和密碼。 如需管理登入認證的最佳作法，請參閱 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令參考：

```
docker login myregistry.azurecr.io
```

完成後，這兩個命令會傳回 `Login Succeeded`。

> [!TIP]
> 當您使用 `docker login` 時，以及當您標記要推送到您的登錄庫的映像時，請一定要指定完整登錄庫名稱 (全部小寫)。 在本文的範例中，完整名稱是 myregistry.azurecr.io。

## <a name="pull-the-official-nginx-image"></a>提取官方的 Nginx 映像

先將公用 Nginx 映像提取至您的本機電腦。

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>在本機執行容器

執行下列 [docker run](https://docs.docker.com/engine/reference/run/) 命令，在連接埠 8080 上以互動方式 (`-it`) 啟動 Nginx 容器的本機執行個體。 `--rm` 引數會指定當您停止容器時，便將其移除。

```
docker run -it --rm -p 8080:80 nginx
```

瀏覽至 [http://localhost:8080](http://localhost:8080)，檢視由 Nginx 在執行中的容器內提供的預設網頁。 您應該會看到如下所示的頁面：

![本機電腦上的 Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

由於您是使用 `-it` 以互動方式啟動容器，所以您可以在從瀏覽器瀏覽該容器時，於命令列上看到 Nginx 伺服器的輸出。

若要停止並移除該容器，請按 `Control`+`C`。

## <a name="create-an-alias-of-the-image"></a>建立映像的別名

使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/)，就能以登錄庫的完整路徑建立映像的別名。 這個範例會指定 `samples` 命名空間，以避免登錄庫根目錄雜亂。

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

如需使用命名空間進行標記的詳細資訊，請參閱 [Azure Container Registry 的最佳做法](container-registry-best-practices.md)的[存放庫命名空間](container-registry-best-practices.md#repository-namespaces)一節。

## <a name="push-the-image-to-your-registry"></a>將映像推送至您的登錄庫

您已使用私人登錄庫的完整路徑標記映像，接下來您可以使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 將映像推送到登錄庫：

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>從登錄庫提取映像

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令從登錄庫提取映像：

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>啟動 Nginx 容器

使用 [docker run](https://docs.docker.com/engine/reference/run/) 命令從登錄庫執行您所提取的映像：

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

瀏覽至 [http://localhost:8080](http://localhost:8080) 以檢視執行中的容器。

若要停止並移除該容器，請按 `Control`+`C`。

## <a name="remove-the-image-optional"></a>移除映像 (選用)

如果您不再需要該 Nginx 映像，您可以使用 [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) 命令在本機上將其刪除。

```
docker rmi myregistry.azurecr.io/samples/nginx
```

若要從 Azure Container Registry 移除映像，您可以使用 Azure CLI 命令 [az acr repository delete](/cli/azure/acr/repository#az-acr-repository-delete)。 例如，下列命令會刪除 `samples/nginx:latest` 標記所參考的資訊清單、任何唯一的圖層資料，以及參考該資訊清單的所有其他標記。

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>後續步驟

現在您已瞭解基本概念了，可以開始使用您的登錄庫！ 例如，將登錄中的容器映像部署至：

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure 容器執行個體](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)
