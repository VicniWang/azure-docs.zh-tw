---
title: 在 Azure Active Directory B2C 中設定資源擁有者密碼認證流程 | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中設定資源擁有者密碼認證流程。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 816d25473bfe5f9dc61d6d6f2e50d6cd82ace50c
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562179"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>在 Azure Active Directory B2C 中使用自訂原則來設定資源擁有者密碼認證流程

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory (Azure AD) B2C 中，資源擁有者密碼認證 (ROPC) 流程是一個 OAuth 標準驗證流程。 在此流程中，應用程式 (也稱為信賴憑證者) 會以有效的認證交換權杖。 認證包含使用者識別碼和密碼。 傳回的權杖為識別碼權杖、存取權杖及重新整理權杖。

以下是 ROPC 流程中支援的選項：

- **原生用戶端** - 在驗證期間，會在程式碼於使用者端的裝置上執行時，進行使用者互動。
- **公用用戶端流程** - 在 API 呼叫中只會傳送應用程式所收集的使用者認證。 不會傳送應用程式的認證。
- **新增宣告** - 可以變更識別碼權杖內容以新增宣告。

以下是不支援的流程：

- **伺服器對伺服器** - 身分識別保護系統在互動過程中需要有從呼叫端 (原生用戶端) 收集的可靠 IP 位址。 伺服器端的 API 呼叫只會使用伺服器的 IP 位址。 如果太多次登入失敗，身分識別保護系統可能會將重複的 IP 位址視為攻擊者。
- **單頁應用程式** - 主要以 JavaScript 撰寫的前端應用程式。 通常會使用 AngularJS、Ember.js 或 Durandal 等架構來撰寫應用程式。
- **機密用戶端流程** - 系統會驗證應用程式用戶端識別碼，但不會驗證應用程式祕密。

## <a name="prerequisites"></a>必要條件

完成在 [Azure Active Directory B2C 中開始使用自訂原則](active-directory-b2c-get-started-custom.md)中的步驟。

## <a name="register-an-application"></a>註冊應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [應用程式]，然後選取 [新增]。
5. 輸入應用程式的名稱，例如 ROPC_Auth_app。
6. 針對 [Web 應用程式/Web API] 選取 [否]，然後針對 [原生用戶端] 選取 [是]。
7. 保留所有其他值，然後選取 [建立]。
8. 選取新的應用程式，並記錄「應用程式識別碼」以供稍後使用。

##  <a name="create-a-resource-owner-policy"></a>建立資源擁有者原則

1. 開啟 *TrustFrameworkExtensions.xml* 檔案。
2. 如果該檔案尚未存在，請新增 **ClaimsSchema** 元素及其子元素作為 **BuildingBlocks** 元素底下的第一個元素：

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. 在 **ClaimsSchema** 之後，將 **ClaimsTransformations** 元素及其子元素 **BuildingBlocks** 元素：

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. 找出 **DisplayName** 為 `Local Account SignIn` 的 **ClaimsProvider** 元素，然後新增下列技術設定檔：

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    以您在先決條件教學課程中所建立 ProxyIdentityExperienceFramework 應用程式的「應用程式識別碼」取代 **client_id** 和 **resource_id** 的 **DefaultValue**。

5. 將下列 **ClaimsProvider** 元素及其技術設定檔一起新增至 **ClaimsProviders** 元素：

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. 將 **UserJourneys** 元素及其子元素新增至 **TrustFrameworkPolicy** 元素：

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
8. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
9. 按一下 [上傳] 。

## <a name="create-a-relying-party-file"></a>建立信賴憑證者檔案

接下來，更新會起始您所建立使用者旅程圖的信賴憑證者檔案：

1. 在您的工作目錄中建立一份 *SignUpOrSignin.xml* 檔案複本，然後將它重新命名為 *ROPC_Auth.xml*。
2. 開啟新檔案，然後將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性值變更成唯一值。 原則識別碼是您原則的名稱。 例如 **B2C_1A_ROPC_Auth**。
3. 將 **DefaultUserJourney** 中的 **ReferenceId** 屬性值變更成 `ResourceOwnerPasswordCredentials`。
4. 將 **OutputClaims** 元素變更成只包含下列宣告：
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
6. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
7. 按一下 [上傳] 。

## <a name="test-the-policy"></a>測試原則

使用您最慣用的 API 開發應用程式產生 API 呼叫，並檢視回應以對您的原則偵錯。 請使用下列資訊作為 POST 要求的本文，以建構與此範例類似的呼叫：

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。
- 以您資源擁有者密碼認證原則的完整名稱取代 `B2C_1A_ROPC_Auth`。

| Key | 值 |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- 以您租用戶中的使用者帳戶名稱取代 `user-account`。
- 以使用者帳戶的密碼取代 `password1`。
- 以來自 *ROPC_Auth_app* 註冊的「應用程式識別碼」取代 `application-id`。
- *Offline_access* 是您要接收重新整理權杖時可選用的項目。

實際的 POST 要求與以下範例類似：

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

離線存取的成功回應會類似於下列範例：

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>兌換重新整理權杖

建構一個如以下所示的 POST 呼叫。 使用下表中的資訊作為要求的本文：

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。
- 以您資源擁有者密碼認證原則的完整名稱取代 `B2C_1A_ROPC_Auth`。

| Key | 值 |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- 以來自 *ROPC_Auth_app* 註冊的「應用程式識別碼」取代 `application-id`。
- 以先前回應中傳回的 **refresh_token** 取代 `refresh-token`。

成功的回應看起來如以下範例：

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>使用原生 SDK 或應用程式驗證

Azure AD B2C 符合公用用戶端資源擁有者密碼認證的 OAuth 2.0 標準，應該與大多數用戶端 SDK 都相容。 如需最新資訊，請參閱[實作現代化最佳做法的 OAuth 2.0 和 OpenID Connect 所適用的原生應用程式 SDK](https://appauth.io/)。

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure Active Directory B2C 自訂原則入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc)(英文\) 中此案例的完整範例。
- 在[權杖參考](active-directory-b2c-reference-tokens.md)中深入了解 Azure Active Directory B2C 所使用的權杖。
