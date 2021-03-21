---
title: UserInfo-slutpunkt | Microsoft Docs
description: Definiera en UserInfo-slutpunkt i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c060a029b1cdbdd890ced96cab732966cb652de0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500588"
---
# <a name="userinfo-endpoint"></a>UserInfo-slutpunkt

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

UserInfo-slutpunkten ingår i [OpenID Connect standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) -specifikationen (OIDC) och är utformad för att returnera anspråk om den autentiserade användaren. UserInfo-slutpunkten definieras i principen för förlitande part med hjälp av [slut punkts](relyingparty.md#endpoints) elementet.  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>Översikt över UserInfo-slutpunkt

UserJourney för användar information anger:

- **Auktorisering**: UserInfo-slutpunkten skyddas med en Bearer-token. En utfärdad åtkomsttoken visas i auktoriseringsarkivet till UserInfo-slutpunkten. Principen anger den tekniska profil som verifierar inkommande token och extraherar anspråk, till exempel objectId för användaren. ObjectId för användaren används för att hämta de anspråk som ska returneras i svaret på körningen av UserInfo-slutpunkten. 
- **Orchestration-steg**: 
  - Ett Orchestration-steg används för att samla in information om användaren. Baserat på anspråk inom den inkommande åtkomsttoken anropar användar resan en [Azure Active Directory teknisk profil](active-directory-technical-profile.md) för att hämta data om användaren, till exempel att läsa användaren med hjälp av ObjectID. 
  - **Valfria Orchestration-steg** – du kan lägga till fler Orchestration-steg, till exempel en REST API teknisk profil för att hämta mer information om användaren. 
  - **UserInfo-utfärdare** – anger listan över anspråk som UserInfo-slutpunkten returnerar.

## <a name="create-a-userinfo-endpoint"></a>Skapa en UserInfo-slutpunkt

### <a name="1-add-the-token-issuer-technical-profile"></a>1. Lägg till den tekniska profilen för token Issuer

1. Öppna *TrustFrameworkExtensions.xml* -filen.
1. Om den inte redan finns lägger du till ett ClaimsProvider-element och dess underordnade element som det första elementet under elementet BuildingBlocks.
1. Lägg till följande anspråks leverantör:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. Avsnittet InputClaims i den **UserInfoIssuer** tekniska profilen anger de attribut som du vill returnera. Den tekniska UserInfoIssuer-profilen anropas i slutet av användar resan. 
1. Den tekniska profilen **UserInfoAuthorization** verifierar signaturen, utfärdarens namn och token-målgruppen och extraherar anspråket från den inkommande token. Ändra följande metadata för att avspegla din miljö:
    1. **utfärdare** – det här värdet måste vara identiskt med `iss` anspråket inom åtkomsttoken för åtkomsttoken. Token som utfärdats av Azure AD B2C använda en utfärdare i formatet `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` . Läs mer om att [Anpassa token](configure-tokens.md).
    1. **IdTokenAudience** – måste vara identiskt med `aud` anspråket inom åtkomsttoken för åtkomsttoken. I Azure AD B2C `aud` är anspråket ID för ditt förlitande parts program. Det här värdet är en samling och stöder flera värden med hjälp av en komma-avgränsare.

        I följande åtkomsttoken `iss` är anspråk svärdet `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` . `aud`Anspråks värde är `22222222-2222-2222-2222-222222222222` .

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  OutputClaims-elementet i den **UserInfoAuthorization** tekniska profilen anger de attribut som du vill läsa från åtkomsttoken. **ClaimTypeReferenceId** är referensen till en anspråks typ. Det valfria **PartnerClaimType** är namnet på det anspråk som definierats i åtkomsttoken.



### <a name="2-add-the-userjourney-element"></a>2. Lägg till UserJourney-elementet 

[UserJourney](userjourneys.md) -elementet definierar den sökväg som användaren ska vidta när de interagerar med ditt program. Lägg till **UserJourneys** -elementet om det inte finns med **UserJourney** som identifieras som `UserInfoJourney` :

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. inkludera slut punkten för den förlitande part principen

Om du vill inkludera UserInfo-slutpunkten i programmet för förlitande part, lägger du till ett [slut punkts](relyingparty.md#endpoints) element i filen *SocialAndLocalAccounts/SignUpOrSignIn.xml* . 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

Det slutförda förlitande part-elementet ser ut så här:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **ramverk för identitets upplevelse**.
1. På sidan **anpassade principer** väljer du **Ladda upp anpassad princip**.
1. Välj **Skriv över den anpassade principen om den redan finns** och Sök sedan efter och välj den *TrustframeworkExtensions.xml* filen.
1. Klicka på **Överför**.
1. Upprepa steg 5 till 7 för den förlitande part filen, till exempel *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Anropar UserInfo-slutpunkten

UserInfo-slutpunkten använder standard OAuth2 Bearer token API, som anropas med hjälp av den åtkomsttoken som togs emot när en token hämtades för ditt program. Den returnerar ett JSON-svar som innehåller anspråk om användaren. UserInfo-slutpunkten finns på Azure AD B2C på:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

/.Well-known konfigurera slut punkt (OpenID Connect Discovery-dokumentet) listar `userinfo_endpoint` fältet. Du kan program mässigt identifiera UserInfo-slutpunkten med hjälp av/.well-known konfigurera slut punkten på: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>Testa principen

1. Under **anpassade principer** väljer du den princip som du har integrerat UserInfo-slutpunkten med. Till exempel *B2C_1A_SignUpOrSignIn*.
1. Välj **Kör nu**. 
1. Under **Välj program** väljer du ditt program som du har registrerat tidigare. För **Välj svars-URL** väljer du `https://jwt.ms` . Mer information finns i [Registrera ett webb program i Azure Active Directory B2C](tutorial-register-applications.md).
1. Registrera dig eller logga in med en e-postadress eller ett socialt konto.
1. Kopiera id_token i sitt kodade format från [https://jwt.ms](https://jwt.ms) webbplatsen. Du kan använda detta för att skicka en GET-begäran till UserInfo-slutpunkten och hämta användar informationen.
1. Skicka en GET-begäran till UserInfo-slutpunkten och hämta användar informationen.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

Ett lyckat svar skulle se ut så här:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>Nästa steg

- Du hittar ett exempel på en princip för UserInfo-slutpunkten på [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint).

::: zone-end
