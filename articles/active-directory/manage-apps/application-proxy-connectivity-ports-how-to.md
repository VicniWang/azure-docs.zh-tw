---
title: 如何開啟應用程式 Proxy 應用程式所需的防火牆連接埠 | Microsoft Docs
description: 了解要開啟哪些連接埠，Azure AD 應用程式 Proxy 才能正確運作
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: efc8c0f0581878d41eb62cbfbd88aec730ecf789
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175230"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>如何開啟應用程式 Proxy 應用程式所需的防火牆連接埠

若要查看必要連接埠的完整清單，以及每個連接埠的功能，請參閱[應用程式 Proxy 文件](application-proxy-add-on-premises-application.md)的＜必要條件＞一節。 請注意，應用程式 Proxy 只會使用輸出連接埠。

您也可以藉由從內部部署網路開啟[連接器連接埠測試工具 (英文)](https://aadap-portcheck.connectorporttest.msappproxy.net/) 來檢查是否已開啟所有必要的連接埠。 越多的綠色勾選記號，表示越佳的復原功能。 

## <a name="app-proxy-regions"></a>應用程式 Proxy 區域

我們正在努力推出能讓您了解針對您有哪些區域必須是綠色的方法。 目前，請確定它們全部都是綠色。 無論您位在那個區域，美國中部 (Central US) 都是必要的。

為了確定工具可提供您正確的結果，請務必：

-   從您已安裝連接器之伺服器的瀏覽器上開啟工具。

-   確定所有適用於您連接器的 Proxy 或防火牆也已套用至這個頁面。 您可以在 Internet Explorer 中移至 [設定] -&gt; [網際網路選項] -&gt; [連線] -&gt; [LAN 設定] 來完成。 在此頁面上，您會看到 [為您的 LAN 使用 Proxy 伺服器] 欄位。 選取此方塊，然後將 Proxy 位址放入 [位址] 欄位。

## <a name="next-steps"></a>後續步驟
[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)
