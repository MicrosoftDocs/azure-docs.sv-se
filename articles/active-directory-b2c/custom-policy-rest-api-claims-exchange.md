---
title: REST API Claims-börser – Azure Active Directory B2C
description: Lägg till REST API åberopade anspråk till anpassade principer i Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84053df34ffda0d4686ad80a9e5f3af00ac53d72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94949504"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Genom gång: Lägg till REST API Claims-utbyten till anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) gör det möjligt för identitet utvecklare att integrera en interaktion med ett RESTful-API i en användar resa. I slutet av den här genom gången kan du skapa en Azure AD B2C användar resa som interagerar med RESTful- [tjänster](custom-policy-rest-api-intro.md).

I det här scenariot kan vi utöka användarens token-data genom att integrera med ett affärs arbets flöde i företags nivå. Vid registrering eller inloggning med ett lokalt eller federerat konto anropar Azure AD B2C ett REST API för att hämta användarens utökade profil data från en fjärrdatakälla. I det här exemplet skickar Azure AD B2C användarens unika identifierare, objectId. REST API returnerar sedan användarens konto saldo (ett slumpmässigt nummer). Använd det här exemplet som en utgångs punkt för att integrera med ditt egna CRM-system, marknadsförings databaser eller affärs arbets flöden.

Du kan också utforma interaktionen som en teknisk profil för validering. Detta är lämpligt när REST API kommer att verifiera data på skärmen och returnera anspråk. Mer information finns i [genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa för att verifiera användarindata](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Förutsättningar

- Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.
- Lär dig hur du [integrerar REST API Claims-utbyten i din Azure AD B2C anpassade princip](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Förbereda en REST API-slutpunkt

I den här genom gången ska du ha en REST API som verifierar om en användares Azure AD B2C objectId är registrerat i Server dels systemet. Om det är registrerat returnerar REST API saldot för användar kontot. Annars registrerar REST API det nya kontot i katalogen och returnerar Startsaldot `50.00` .

Följande JSON-kod illustrerar data Azure AD B2C skickas till din REST API-slutpunkt. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

När REST API verifierar data måste den returnera en HTTP 200 (OK), med följande JSON-data:

```json
{
    "balance": "760.50"
}
```

Installationen av REST API slut punkten ligger utanför omfånget för den här artikeln. Vi har skapat ett [Azure Functions](../azure-functions/functions-reference.md) -exempel. Du kan komma åt den fullständiga Azure Function-koden på [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definiera anspråk

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C princip körning. Du kan deklarera anspråk i avsnittet [anspråks schema](claimsschema.md) . 

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråk i **ClaimsSchema** -elementet.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Lägg till teknisk profil för RESTful-API 

En [RESTful-teknisk profil](restful-technical-profile.md) ger stöd för samverkan med din egen RESTful-tjänst. Azure AD B2C skickar data till RESTful-tjänsten i en `InputClaims` samling och tar emot data tillbaka i en `OutputClaims` samling. Hitta **ClaimsProviders** -elementet i <em>**`TrustFrameworkExtensions.xml`**</em> filen och Lägg till en ny anspråks leverantör enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

I det här exemplet `userLanguage` skickas till rest-tjänsten som `lang` i JSON-nyttolasten. Värdet för `userLanguage` anspråket innehåller det aktuella användarens språk-ID. Mer information finns i [anspråk lösare](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Konfigurera teknisk profil för RESTful-API 

När du har distribuerat REST API ställer du in metadata för den `REST-ValidateProfile` tekniska profilen så att de motsvarar dina egna REST API, inklusive:

- **ServiceUrl**. Ange URL: en för REST API slut punkten.
- **SendClaimsIn**. Ange hur inloggade anspråk skickas till RESTful-anspråks leverantören.
- **AuthenticationType**. Ange vilken typ av autentisering som utförs av RESTful-anspråks leverantören. 
- **AllowInsecureAuthInProduction**. I en produktions miljö, se till att ange dessa metadata som `true`
    
Se [metadata för RESTful teknisk profil](restful-technical-profile.md#metadata) för fler konfigurationer.

Kommentarerna ovan `AuthenticationType` och `AllowInsecureAuthInProduction` anger ändringar som du bör göra när du flyttar till en produktions miljö. Information om hur du skyddar dina RESTful-API: er för produktion finns i [Secure RESTful API](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Lägg till ett Orchestration-steg

[Användarresor](userjourneys.md) anger explicita sökvägar genom vilka en princip tillåter att ett anspråksbaserat program hämtar önskade anspråk för en användare. En användar resa representeras som en Orchestration-sekvens som måste följas av för en lyckad transaktion. Du kan lägga till eller ta bort Orchestration-steg. I det här fallet lägger du till ett nytt Orchestration-steg som används för att utöka den information som ges till programmet efter att användaren loggat in eller loggar in via REST API-anropet.

1. Öppna bas filen för din princip. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> .
1. Sök efter `<UserJourneys>` elementet. Kopiera hela elementet och ta sedan bort det.
1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Klistra in i `<UserJourneys>` tilläggs filen efter slutet av `<ClaimsProviders>` elementet.
1. Leta upp `<UserJourney Id="SignUpOrSignIn">` och Lägg till följande Orchestration-steg före det sista.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Återtvingar det senaste Orchestration-steget genom att ändra `Order` till `8` . Dina sista steg i dirigeringen bör se ut ungefär så här:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Upprepa de två sista stegen för **ProfileEdit** -och **PasswordReset original** -användar resan.


## <a name="include-a-claim-in-the-token"></a>Inkludera ett anspråk i token 

Om du vill återgå `balance` tillbaka till det förlitande part programmet lägger du till ett utgående anspråk i <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> filen. Om du lägger till ett utgående anspråk utfärdas anspråket till token efter en lyckad användar resa och skickas till programmet. Ändra det tekniska profil elementet i avsnittet förlitande part och Lägg till `balance` som ett utgående anspråk.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Upprepa det här steget för **ProfileEdit.xml** och **PasswordReset.xml** användar resor.

Spara de filer som du ändrade: *TrustFrameworkBase.xml* och *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* och *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ramverk för identitets upplevelse**.
1. Välj **överför anpassad princip** och överför sedan de principfiler som du ändrade: *TrustFrameworkBase.xml* och *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* och *PasswordReset.xml*. 
1. Välj den registrerings-eller inloggnings princip som du laddade upp och klicka på knappen **Kör nu** .
1. Du bör kunna registrera dig med hjälp av en e-postadress eller ett Facebook-konto.
1. Den token som skickas tillbaka till programmet inkluderar `balance` anspråket.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skyddar dina API: er finns i följande artiklar:

- [Genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som ett Orchestration-steg](custom-policy-rest-api-claims-exchange.md)
- [Skydda ditt RESTful-API](secure-rest-api.md)
- [Referens: RESTful teknisk profil](restful-technical-profile.md)