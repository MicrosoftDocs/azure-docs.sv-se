---
title: REST API anspråks utbyten som verifiering
titleSuffix: Azure AD B2C
description: En genom gång för att skapa en Azure AD B2C användar resa som samverkar med RESTful-tjänster.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 761bc4db7760ef5e84e3fc3c8a5deea5d4508f51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94951935"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa för att verifiera användarindata

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

IEF (Identity Experience Framework) som unders Azure Active Directory B2C (Azure AD B2C) gör det möjligt för identitet utvecklare att integrera en interaktion med ett RESTful-API i en användar resa.  I slutet av den här genom gången kan du skapa en Azure AD B2C användar resa som interagerar med RESTful- [tjänster](custom-policy-rest-api-intro.md) för att verifiera användarindata.

I det här scenariot lägger vi till möjligheten för användarna att ange ett förmåns nummer i Azure AD B2C registrerings sida. Vi verifierar om den här kombinationen av e-post och lojalitets nummer är mappad till en kampanj kod genom att skicka dessa data till en REST API. Om REST API hittar en kampanj kod för den här användaren kommer den att returneras till Azure AD B2C. Slutligen kommer kampanj koden att infogas i token-anspråk för att programmet ska förbruka.

Du kan också utforma interaktionen som ett Orchestration-steg. Detta är lämpligt när REST API inte kommer att verifiera data på skärmen och alltid returnera anspråk. Mer information finns i [genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som ett Dirigerings steg](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Förutsättningar

- Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.
- Lär dig hur du [integrerar REST API Claims-utbyten i din Azure AD B2C anpassade princip](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Förbereda en REST API-slutpunkt

I den här genom gången ska du ha en REST API som kontrollerar om en e-postadress är registrerad i backend-systemet med ett förmåns-ID. Om det är registrerat bör REST API returnera en kod för registrering av registrering som kunden kan använda för att köpa varor i ditt program. Annars ska REST API returnera ett HTTP 409-fel meddelande: "förmåns-ID {lojalitets-ID} är inte kopplat till e-postadressen {email}.".

Följande JSON-kod illustrerar data Azure AD B2C skickas till din REST API-slutpunkt. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

När REST API verifierar data måste den returnera en HTTP 200 (OK), med följande JSON-data:

```json
{
    "promoCode": "24534"
}
```

Om verifieringen misslyckades måste REST API returnera en HTTP 409 (konflikt) med `userMessage` JSON-elementet. IEF förväntar sig det `userMessage` anspråk som REST API returnerar. Detta anspråk visas som en sträng för användaren om valideringen Miss lyckas.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Lägg till teknisk profil för RESTful-API 

En [RESTful-teknisk profil](restful-technical-profile.md) ger stöd för att rikta in dig mot din egen RESTful-tjänst. Azure AD B2C skickar data till RESTful-tjänsten i en `InputClaims` samling och tar emot data tillbaka i en `OutputClaims` samling. Hitta **ClaimsProviders** -elementet och Lägg till en ny anspråks leverantör enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
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

## <a name="validate-the-user-input"></a>Verifiera användarindata

Om du vill hämta användarens lojalitets nummer under registreringen måste du tillåta att användaren anger dessa data på skärmen. Lägg till **loyaltyId** utgående anspråk på registrerings sidan genom att lägga till den i det befintliga avsnittet för registrering av tekniska profiler `OutputClaims` . Ange hela listan med utgående anspråk för att kontrol lera i vilken ordning anspråken visas på skärmen.  

Lägg till den tekniska referensen för verifiering i den tekniska profilen för registrering, som anropar `REST-ValidateProfile` . Den nya verifierings tekniska profilen läggs till överst i `<ValidationTechnicalProfiles>` samlingen som definieras i bas principen. Det här beteendet innebär att endast efter lyckad verifiering, Azure AD B2C flyttar till för att skapa kontot i katalogen.   

1. Hitta **ClaimsProviders** -elementet. Lägg till en ny anspråks leverantör enligt följande:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Inkludera ett anspråk i token 

Om du vill returnera kampanj koden anspråk tillbaka till den förlitande parten, lägger du till ett utgående anspråk till <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> filen. Utgående anspråk gör att anspråk läggs till i token efter en lyckad användar resa och skickas till programmet. Ändra det tekniska profil elementet i avsnittet förlitande part för att lägga till `promoCode` som ett utgående anspråk.
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ramverk för identitets upplevelse**.
1. Välj **överför anpassad princip** och överför sedan de principfiler som du ändrade: *TrustFrameworkExtensions.xml* och *SignUpOrSignin.xml*. 
1. Välj den registrerings-eller inloggnings princip som du laddade upp och klicka på knappen **Kör nu** .
1. Du bör kunna registrera dig med en e-postadress.
1. Klicka på länken **Registrera dig nu** .
1. I **ditt lojalitets-ID** skriver du 1234 och klickar på **Fortsätt**. Nu bör du få ett verifierings fel meddelande.
1. Ändra till ett annat värde och klicka på **Fortsätt**.
1. Den token som skickas tillbaka till programmet inkluderar `promoCode` anspråket.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skyddar dina API: er finns i följande artiklar:

- [Genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som ett Orchestration-steg](custom-policy-rest-api-claims-exchange.md)
- [Skydda ditt RESTful-API](secure-rest-api.md)
- [Referens: RESTful teknisk profil](restful-technical-profile.md)