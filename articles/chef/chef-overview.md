---
title: 搭配 Azure 使用 Chef
description: 使用 Chef 來設定及測試 Azure 基礎結構的簡介
ms.service: virtual-machines-linux
keywords: azure, chef, devops, 虛擬機器, 概觀, 自動化
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: c0ec8b98ff711f8e5746d6d4731266ed4b09cc8f
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658839"
---
# <a name="using-chef-with-azure"></a>搭配 Azure 使用 Chef
[Chef](http://www.chef.io) 是個功能強大的自動化平台，可將 Azure 上的虛擬機器基礎結構轉換為程式碼。 無論您網路之間的基礎結構設定大小，Chef 都會將其設定、部署及受控方式自動化。

本文說明使用 Chef 來管理 Azure 基礎結構的優點。

## <a name="chef-extension-on-azure"></a>Azure 上的 Chef 擴充功能
在 Azure 入口網站上使用 [Chef 擴充功能](https://docs.microsoft.com/azure/chef/chef-extension-portal)，即可佈建將 Chef 用戶端當作背景服務執行的虛擬機器。 完成佈建之後，這些虛擬機器即可由 Chef 伺服器進行管理。

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
直接在 Azure Cloud Shell 中使用 Chef 工作站！ 直接從 Cloud Shell 執行所有 Chef 公用程式和 InSpec。 您可以從下列項目使用 Chef 命令：

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

將我們的命令公用程式與 Cloud Shell 中的其他可用工具 (`git`、`az-cli` 和 `terraform`) 結合，並從瀏覽器撰寫您的基礎結構和合規性自動化。

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>使用一個平台將基礎結構、應用程式及合規性自動化
公司需要保持作業速度、迅速反應並提高安全性，才能在數位市場上競爭。 Chef 會與 Microsoft 攜手合作，協助個人、小組及企業達成上述三點。 有了 Chef Automate 這個平台，您現在就可以在 Microsoft 資產間進行自動化並且持續交付基礎結構、應用程式及合規性。

## <a name="test-drive-chef-automate-on-azure"></a>在 Azure 上試用 Chef Automate
[Chef Automate Azure Marketplace 解決方案](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) 受 Chef 支援，可讓您以共同作業方式建置、部署及管理您的基礎結構和應用程式。 只要按一下，就能立即存取 Chef Automate 隨附的所有商用功能、端對端檢視整個公司內部、持續保持合規性，並透過統一的工作流程來管理所有變更。

## <a name="next-steps"></a>後續步驟

* [使用 Chef 在 Azure 上建立 Windows 虛擬機器](/azure/virtual-machines/windows/chef-automation)
