---
title: Konfigurera ett flöde för autentiseringsuppgifter för resurs ägar lösen ord
titleSuffix: Azure AD B2C
description: Lär dig hur du konfigurerar ROPC-flödet (Resource Owner Password Credential) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a7c93bb0e0dcc51e35bc27fa0799d8410e66df6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581889"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Konfigurera ett flöde för autentiseringsuppgifter för resurs ägar lösen ord i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

I Azure Active Directory B2C (Azure AD B2C) är ROPC-flödet (Resource Owner Password Credential) ett OAuth-standardautentiserings flöde. I det här flödet, som även kallas för den förlitande parten, utbyter giltiga autentiseringsuppgifter för token. Autentiseringsuppgifterna innehåller ett användar-ID och lösen ord. De token som returneras är en ID-token, en åtkomsttoken och en uppdateringstoken.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>ROPC Flow-anteckningar

I Azure Active Directory B2C (Azure AD B2C) stöds följande alternativ:

- **Inbyggd klient**: användar interaktion under autentisering sker när kod körs på en enhet på användar sidan. Enheten kan vara ett mobilt program som körs i ett inbyggt operativ system, till exempel Android och iOS.
- **Offentligt klient flöde**: endast användarautentiseringsuppgifter som samlats in av ett program skickas i API-anropet. Autentiseringsuppgifterna för programmet skickas inte.
- **Lägg till nya anspråk**: innehållet i ID-token kan ändras för att lägga till nya anspråk.

Följande flöden stöds inte:

- **Server-till-Server**: identitets skydds systemet behöver en tillförlitlig IP-adress som samlats in från anroparen (den interna klienten) som en del av interaktionen. I ett API-anrop på Server sidan används bara serverns IP-adress. Om ett dynamiskt tröskelvärde för misslyckade autentiseringar överskrids, kan identitets skydds systemet identifiera en upprepad IP-adress som en angripare.
- **Konfidentiellt klient flöde**: programmets klient-ID är verifierat, men program hemligheten är inte verifierad.

Tänk på följande när du använder ROPC-flödet:

- ROPC fungerar inte om det sker ett avbrott i det autentiseringsschema som kräver användar interaktion. Till exempel, när ett lösen ord har upphört att gälla eller behöver ändras, krävs [Multi-Factor Authentication](multi-factor-authentication.md) eller när mer information måste samlas in under inloggningen (till exempel användar medgivande).
- ROPC stöder endast lokala konton. Användare kan inte logga in med [federerade identitets leverantörer](add-identity-provider.md) som Microsoft, Google +, Twitter, AD-FS eller Facebook.
- [Hantering av sessioner](session-behavior.md), inklusive [Håll mig inloggad (KMSI avgör)](session-behavior.md#enable-keep-me-signed-in-kmsi), är inte tillämpligt.


## <a name="register-an-application"></a>Registrera ett program

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>Skapa ett användar flöde för resurs ägare

1. Logga in på Azure Portal som global administratör för Azure AD B2C-klientorganisationen.
2. Om du vill växla till Azure AD B2C klient väljer du katalogen B2C i det övre högra hörnet i portalen.
3. Välj **användar flöden** och välj **nytt användar flöde**.
4. Välj **Logga in med autentiseringsuppgifter för resurs ägar lösen ord (ROPC)**.
5. Under **version** kontrollerar du att **förhands granskning** är markerat och väljer sedan **skapa**.
7. Ange ett namn för användar flödet, t. ex. *ROPC_Auth*.
8. Under **program anspråk** klickar du på **Visa fler**.
9. Välj de program anspråk som du behöver för ditt program, till exempel visnings namn, e-postadress och identitets leverantör.
10. Välj **OK** och sedan **Skapa**.

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>Skapa en resurs ägar princip

1. Öppna *TrustFrameworkExtensions.xml* -filen.
2. Om den inte redan finns lägger du till ett **ClaimsSchema** -element och dess underordnade element som det första elementet under elementet **BuildingBlocks** :

    ```xml
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

3. Efter **ClaimsSchema** lägger du till ett **ClaimsTransformations** -element och dess underordnade element till **BuildingBlocks** -elementet:

    ```xml
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

4. Leta upp det **ClaimsProvider** -element som har ett **visnings** namn `Local Account SignIn` och Lägg till följande tekniska profil:

    ```xml
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
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
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

    Ersätt **DefaultValue** för **client_id** med program-ID: t för det ProxyIdentityExperienceFramework-program som du skapade i den nödvändiga självstudien. Ersätt sedan **DefaultValue** för **resource_id** med program-ID: t för det IdentityExperienceFramework-program som du också skapade i den nödvändiga självstudien.

5. Lägg till följande **ClaimsProvider** -element med sina tekniska profiler i **ClaimsProviders** -elementet:

    ```xml
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

6. Lägg till ett **UserJourneys** -element och dess underordnade element till elementet **TrustFrameworkPolicy** :

    ```xml
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

7. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
8. Aktivera **Skriv över principen om den finns** och bläddra sedan till och välj *TrustFrameworkExtensions.xml* -filen.
9. Klicka på **Överför**.

## <a name="create-a-relying-party-file"></a>Skapa en förlitande parts fil

Uppdatera sedan den förlitande part filen som initierar användar resan som du skapade:

1. Gör en kopia av *SignUpOrSignin.xml* -filen i din arbets katalog och Byt namn på den till *ROPC_Auth.xml*.
2. Öppna den nya filen och ändra värdet för attributet **PolicyId** för **TrustFrameworkPolicy** till ett unikt värde. Princip-ID är namnet på principen. Till exempel **B2C_1A_ROPC_Auth**.
3. Ändra värdet för attributet **ReferenceId** i **DefaultUserJourney** till `ResourceOwnerPasswordCredentials` .
4. Ändra **OutputClaims** -elementet så att det endast innehåller följande anspråk:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
6. Aktivera **Skriv över principen om den finns** och bläddra sedan till och välj *ROPC_Auth.xml* -filen.
7. Klicka på **Överför**.


::: zone-end

## <a name="test-the-ropc-flow"></a>Testa ROPC-flödet

Använd ditt favorit-API utvecklings program för att generera ett API-anrop och granska svaret för att felsöka principen. Skapa ett anrop som det här exemplet med följande information som brödtext i POST-begäran:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Ersätt `<tenant-name>` med namnet på din Azure AD B2C-klient.
- Ersätt `B2C_1A_ROPC_Auth` med det fullständiga namnet på din resurs ägar lösen ords princip för autentiseringsuppgifter.

| Tangent | Värde |
| --- | ----- |
| användarnamn | `user-account` |
| password | `password1` |
| grant_type | password |
| omfång | OpenID `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Ersätt `user-account` med namnet på ett användar konto i din klient organisation.
- Ersätt `password1` med användar kontots lösen ord.
- Ersätt `application-id` med program-ID: t från *ROPC_Auth_app* registreringen.
- *Offline_access* är valfritt om du vill ta emot en uppdateringstoken.

Förfrågningen om faktisk POST ser ut som i följande exempel:

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Ett lyckat svar med offline-åtkomst ser ut som i följande exempel:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Lös in en uppdateringstoken

Skapa ett POST samtal som det som visas här. Använd informationen i följande tabell som brödtext i begäran:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Ersätt `<tenant-name>` med namnet på din Azure AD B2C-klient.
- Ersätt `B2C_1A_ROPC_Auth` med det fullständiga namnet på din resurs ägar lösen ords princip för autentiseringsuppgifter.

| Tangent | Värde |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Ersätt `application-id` med program-ID: t från *ROPC_Auth_app* registreringen.
- Ersätt `refresh-token` med **refresh_token** som skickades tillbaka i föregående svar.

Ett lyckat svar ser ut som i följande exempel:

```json
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

## <a name="use-a-native-sdk-or-app-auth"></a>Använd en inbyggd SDK eller App-Auth

Azure AD B2C uppfyller OAuth 2,0-standarder för offentliga autentiseringsuppgifter för lösen ord för klient resurs ägare och bör vara kompatibla med de flesta klient-SDK: er. Den senaste informationen finns i [native app SDK för OAuth 2,0 och OpenID Connect implementering av moderna bästa metoder](https://appauth.io/).

## <a name="next-steps"></a>Nästa steg

Hämta arbets exempel som har kon figurer ATS för användning med Azure AD B2C från GitHub, [för Android](https://aka.ms/aadb2cappauthropc) och [för iOS](https://aka.ms/aadb2ciosappauthropc).
