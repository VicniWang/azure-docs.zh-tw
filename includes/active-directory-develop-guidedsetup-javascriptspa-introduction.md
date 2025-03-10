---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 2cb4895fc2f884d6da41b55faa91fbcb9e88f52f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978730"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>從 JavaScript 單一頁面應用程式 (SPA) 呼叫 Microsoft Graph API

本指南示範 JavaScript 單一頁面應用程式 (SPA) 如何登入個人、公司和學校帳戶、取得存取權杖，以及呼叫 Microsoft Graph API 或其他需要來自 Azure Active Directory v2.0 端點之存取權杖的 API。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式

![本指南產生之範例應用程式的運作方式](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>相關資訊

本指南所建立的範例應用程式可讓 JavaScript SPA 查詢 Microsoft Graph API，或查詢可接受來自 Azure Active Directory v2.0 端點之權杖的 Web API。 針對這個案例，在使用者登入之後，系統會透過授權標頭要求一個存取權杖，並將其新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|說明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library for JavaScript 預覽|

> [!NOTE]
> *msal.js* 的目標是「Azure Active Directory v2.0 端點」 - 它會啟用個人、學校及公司帳戶以登入和取得權杖。 「Azure Active Directory v2.0 端點」具有[一些限制](../articles/active-directory/develop/active-directory-v2-limitations.md)。
> 若要了解 v1.0 與 v2.0 端點之間的差異，請參閱[端點比較指南](../articles/active-directory/develop/azure-ad-endpoint-comparison.md)。

<!--end-collapse-->
