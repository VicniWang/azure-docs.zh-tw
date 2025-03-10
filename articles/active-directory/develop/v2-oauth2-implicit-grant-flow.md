---
title: 使用 Azure AD v2.0 隱含流程保護單一頁面應用程式的安全 | Microsoft Docs
description: 使用針對單一頁面應用程式的隱含流程的 Azure AD v2.0 實作，建置 Web 應用程式。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec47e6d52a3aef8533a3d16f0f81693b8f01f3cf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205047"
---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>2.0 通訊協定 - 使用隱含流程的 SPA

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

透過 v2.0 端點，您可以使用 Microsoft 的個人和公司或學校帳戶將使用者登入您的單頁應用程式。 主要於瀏覽器中執行的單頁及 JavaScript 應用程式在驗證時會面臨一些有趣的挑戰：

* 這些應用程式的安全性特性與傳統伺服器型 Web 應用程式大不相同。
* 許多授權伺服器及識別提供者不支援 CORS 要求。
* 重新導向離開應用程式的完整網頁瀏覽器變得對使用者經驗特別有侵入性。

對於這些應用程式 (AngularJS、Ember.js、React.js 等)，Azure Active Directory (Azure AD) 支援 OAuth 2.0 隱含授與流程。 隱含流程相關說明，請參閱 [OAuth 2.0 規格](https://tools.ietf.org/html/rfc6749#section-4.2)。 其主要優點是它可讓應用程式從 Azure AD 取得權杖，不需要執行後端伺服器認證交換。 這可讓應用程式登入使用者、維護工作階段，並且取得用戶端 JavaScript 程式碼中所有其他 Web API 的權杖。 使用隱含流程時需注意幾個重要的安全性考量，特別是[用戶端](https://tools.ietf.org/html/rfc6749#section-10.3)及[使用者模擬](https://tools.ietf.org/html/rfc6749#section-10.3)。

若要使用隱含流程及 Azure AD 將驗證新增至 JavaScript 應用程式，建議您使用我們的開放原始碼 JavaScript 程式庫 [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)。

但如果您不想在單頁應用程式中使用程式庫，也不想自行傳送通訊協定訊息，請遵循下列一般步驟。

> [!NOTE]
> v2.0 端點並未支援每個 Azure AD 案例和功能。 若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。

## <a name="protocol-diagram"></a>通訊協定圖表

下圖說明整個隱含登入流程，而後續幾節會更詳細地說明每個步驟。

![OpenId Connect 區隔線](./media/v2-oauth2-implicit-grant-flow/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>傳送登入要求

若要開始將使用者登入應用程式，您可以傳送 [OpenID Connect](v2-protocols-oidc.md) 授權要求，並透過 v2.0 端點取得 `id_token`。

> [!IMPORTANT]
> 若要成功要求識別碼權杖，[註冊入口網站](https://apps.dev.microsoft.com)中的應用程式註冊必須為 Web 用戶端啟用 [允許隱含流程]。 如果未啟用，會傳回 `unsupported_response` 錯誤：**不允許此用戶端使用為輸入參數 "response_type" 提供的值。值應為 'code'**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 若要使用隱含流程來測試登入，請按一下 <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. </a>登入之後，您的瀏覽器應重新導向至在位址列中有 `id_token` 的 `https://localhost/myapp/`。
>

| 參數 |  | 說明 |
| --- | --- | --- |
| `tenant` | 必要 |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | 必要 |註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給應用程式的應用程式識別碼。 |
| `response_type` | 必要 |必須包含 OpenID Connect 登入的 `id_token` 。 它也可能包含 response_type `token`。 這裡使用 `token` ，讓您的應用程式能夠立即從授權端點接收存取權杖，而不需要向授權端點進行第二次要求。 如果您使用 `token` response_type，`scope` 參數必須包含範圍，以指出要對哪個資源發出權杖。 |
| `redirect_uri` | 建議使用 |應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。 |
| `scope` | 必要 |[範圍](v2-permissions-and-consent.md)的空格分隔清單。 針對 OpenID Connect，即必須包含範圍 `openid`，其會在同意 UI 中轉譯成「讓您登入」權限。 (選擇性) 您也可以包含 `email` 或 `profile` 範圍，以取得額外使用者資料的存取權。 您也可以在此要求中包含其他範圍，以要求同意各種資源。 |
| `response_mode` | 選用 |指定將產生的權杖送回到應用程式所應該使用的方法。 預設為查詢存取權杖，但若要求包含 id_token，則會查詢片段。 |
| `state` | 建議使用 |同樣會隨權杖回應傳回之要求中所包含的值。 其可以是您想要之任何內容的字串。 隨機產生的唯一值通常用於 [防止跨站台要求偽造攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此狀態也用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| `nonce` | 必要 |由應用程式產生且包含在要求中的值，會以宣告方式包含在產生的 id_token 中。 應用程式接著便可確認此值，以減少權杖重新執行攻擊。 此值通常是隨機的唯一字串，可用以識別要求的來源。 只有要求 id_token 時，才需要此值。 |
| `prompt` | 選用 |表示需要的使用者互動類型。 目前的有效值只有 'login'、'none'、'select_account' 和 'consent'。 `prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。 `prompt=none` 則相反 - 它會確保不會對使用者顯示任何互動式提示。 如果要求無法透過單一登入以無訊息方式完成，v2.0 端點會傳回錯誤。 `prompt=select_account` 會將使用者傳送至帳戶選擇器，工作階段中記下的所有帳戶都會出現在當中。 `prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| `login_hint`  |選用 |如果您事先知道其使用者名稱，可用來預先填入使用者登入頁面的使用者名稱/電子郵件地址欄位。 通常應用程式會在重新驗證期間使用此參數，已經使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。|
| `domain_hint` | 選用 |可以是 `consumers` 或 `organizations` 其中一個。 如果包含，它會略過使用者在 v2.0 登入頁面上經歷的以電子郵件為基礎的探索程序，導致稍微更佳流暢的使用者經驗。 通常應用程式會在重新驗證期間使用此參數，方法是從 id_token 擷取 `tid` 宣告。 如果 `tid` 宣告值是 `9188040d-6c67-4c5b-b112-36a304b66dad` (Microsoft 帳戶取用者租用戶)，您應該使用 `domain_hint=consumers`。 否則，您可以在重新驗證期間使用 `domain_hint=organizations`。 |

此時，會要求使用者輸入其認證並完成驗證。 v2.0 端點也會確保使用者已經同意 `scope` 查詢參數所示的權限。 如果使用者**完全未**同意這些權限的任一，它會要求使用者同意必要權限。 如需詳細資訊，請參閱[權限、同意及多租用戶應用程式](v2-permissions-and-consent.md)。

一旦使用者驗證並同意，v2.0 端點就會使用 `response_mode` 參數中指定的方法，將回應傳回至位於指定所在 `redirect_uri` 的應用程式。

#### <a name="successful-response"></a>成功回應

使用 `response_mode=fragment` 及 `response_type=id_token+token` 的成功回應如下所示 (內含分行符號以利閱讀)：

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 參數 | 說明 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖，在此案例中為 Microsoft Graph 的存取權杖。 此存取權杖不得經過解碼或檢查，而應將其視為不透明字串。 |
| `token_type` |如果 `response_type` 包含 `token` 則納入。 一律為 `Bearer`。 |
| `expires_in`|如果 `response_type` 包含 `token` 則納入。 表示權杖有效的秒數 (針對快取目的)。 |
| `scope` |如果 `response_type` 包含 `token` 則納入。 表示在 access_token 的有效範圍。 如果範圍不適用於使用者 (例如：在使用個人帳戶登入時，要求 AAD 專用範圍)，則可能不會包含所有要求的範圍。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取並顯示值，但不應依賴這些值來取得任何授權或安全性界限。 如需有關 id_token 的詳細資訊，請參閱 [`id_token reference`](id-tokens.md)。 <br> **附註：** 只在要求 `openid` 範圍時提供。 |
| `state` |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理：

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 說明 |
| --- | --- |
| `error` |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

## <a name="validate-the-idtoken"></a>驗證 id_token

僅接收 id_token 並不足以驗證使用者，您還必須驗證 id_token 的簽章，以及根據您應用程式的需求確認權杖中的宣告。 v2.0 端點使用 [JSON Web Tokens (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公開金鑰加密簽署權杖及驗證其是否有效。

您可以選擇驗證用戶端程式碼中的 `id_token`，但是常見的作法是將 `id_token` 傳送至後端伺服器，並且在那裡執行驗證。 一旦驗證了 id_token 的簽章，就會有數項宣告需要驗證。 如需詳細資訊，請參閱 [`id_token` 參考](id-tokens.md)，包括[驗證權杖](id-tokens.md#validating-an-idtoken)及[簽署金鑰變換的重要資訊](active-directory-signing-key-rollover.md)。 我們建議利用程式庫來剖析和驗證權杖 - 對於大部分語言和平台至少有一個可用。

您可能也希望根據自己的案例驗證其他宣告。 一些常見的驗證包括：

* 確保使用者/組織已註冊應用程式。
* 確保使用者擁有適當授權/權限。
* 確保驗證具有特定強度，例如多重要素驗證。

一旦驗證完畢 id_token，即可利用使用者開始工作階段，並使用 id_token 中的宣告來取得應用程式中的使用者相關資訊。 此資訊可以用於顯示、記錄、個人化等等。

## <a name="get-access-tokens"></a>取得存取權杖

您已經將使用者註冊到單一頁面應用程式，您可以取得存取權杖以呼叫受到 Azure AD 保護的 Web API，例如 [Microsoft Graph](https://developer.microsoft.com/graph)。 即使您已經收到使用 `token` response_type 的權杖，仍可使用此方法來取得其他資源的權杖，而不需重新導向使用者進行再次登入。

在正常的 OpenID Connect/OAuth 流程中，您可以藉由對 v2.0 `/token` 端點進行要求來完成這個操作。 不過，v2.0 端點不支援 CORS 要求，因此進行 AJAX 呼叫以取得和重新整理權杖是不可能的。 相反地，您可以在隱藏的 iframe 中使用隱含流程，為其他 Web API 取得新權杖： 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

如需 URL 中查詢參數的詳細資料，請參閱[傳送登入要求](#send-the-sign-in-request)。

> [!TIP]
> 請嘗試將下列要求貼至瀏覽器！ (請務必以您使用者的正確值取代 `login_hint` 值)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

由於 `prompt=none` 參數，此要求會立即成功或失敗，並且傳回給您的應用程式。 會使用 `response_mode` 參數中指定的方法，將成功的回應傳送至指定的 `redirect_uri` 給您的應用程式。

#### <a name="successful-response"></a>成功回應

使用 `response_mode=fragment` 的成功回應如下所示：

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| 參數 | 說明 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖，在此案例中為 Microsoft Graph 的存取權杖。 此存取權杖不得經過解碼或檢查，而應將其視為不透明字串。 |
| `token_type` | 一律為 `Bearer`。 |
| `expires_in` | 表示權杖有效的秒數 (針對快取目的)。 |
| `scope` | 表示在 access_token 的有效範圍。 如果範圍不適用於使用者 (例如：在使用個人帳戶登入時，要求 AAD 專用範圍)，則可能不會包含所有要求的範圍。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 如果 `response_type` 包含 `id_token` 則納入。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取並顯示值，但不應依賴這些值來取得任何授權或安全性界限。 如需 id_token 的詳細資訊，請參閱 [`id_token` 參考](id-tokens.md)。 <br> **附註：** 只在要求 `openid` 範圍時提供。 |
| `state` |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |


#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理。 如果是 `prompt=none`，預期的錯誤為：

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 參數 | 說明 |
| --- | --- |
| `error` |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

如果您在 iframe 要求中收到此錯誤，使用者必須再次以互動方式登入以擷取新的權杖。 您可以選擇對於您的應用程式合理的任何方式處理這種情況。

## <a name="validating-access-tokens"></a>驗證存取權杖

一旦您收到 access_token，請務必要驗證權杖的簽章以及下列宣告。 您也可以選擇根據您的案例驗證其他宣告。 

* **對象**宣告，用以確定權杖預定要提供給您的應用程式
* **簽發者**宣告 - 用以確認權杖是由 v2.0 端點簽發給您的應用程式
* **生效時間**和**到期時間**宣告，用以確認權杖尚未過期

如需存取權杖中所出示宣告的詳細資訊，請參閱[存取權杖參考](access-tokens.md)

## <a name="refreshing-tokens"></a>重新整理權杖

隱含授與不提供重新整理權杖功能。 `id_token` 和 `access_token` 馬上就會到期，因此您的應用程式必須準備好定期重新整理這些權杖。 若要重新整理其中任何一個類型的權杖，您可以使用 `prompt=none` 參數來控制 Azure AD 的行為，執行上述的相同隱藏的 iframe 要求。 如果您想要收到新的 `id_token`，務必使用 `response_type=id_token` 和 `scope=openid`，以及 `nonce` 參數。

## <a name="send-a-sign-out-request"></a>傳送登出要求

OpenIdConnect `end_session_endpoint` 允許您的應用程式向 v2.0 端點傳送要求，以結束使用者的工作階段及清除 v2.0 端點設定的 Cookie。 若要將使用者從 Web 應用程式完全登出，您的應用程式應結束自己和使用者之間的工作階段 (通常是透過清除權杖快取或卸除 Cookie)，然後將瀏覽器重新導向至：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 參數 |  | 說明 |
| --- | --- | --- |
| `tenant` |必要 |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `post_logout_redirect_uri` | 建議使用 | 使用者在完成登出之後應該要返回的 URL。 這個值必須符合為應用程式註冊的其中一個重新導向 URI。 如果未包含，v2.0 端點會向使用者顯示一般訊息。 |

## <a name="next-steps"></a>後續步驟

* 瀏覽所有 [MSAL JS 範例](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)以開始撰寫程式碼。
