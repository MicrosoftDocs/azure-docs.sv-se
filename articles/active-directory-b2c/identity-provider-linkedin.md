---
title: Konfigurera registrering och inloggning med ett LinkedIn-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med LinkedIn-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1ce9c00cb58253e2cca9a7d60c4cce9b77709688
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953860"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett LinkedIn-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>Skapa ett LinkedIn-program

Om du vill aktivera inloggning för användare med ett LinkedIn-konto i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program på [LinkedIn Developer-webbplatsen](https://www.developer.linkedin.com/). Mer information finns i [Authorization Code Flow](/linkedin/shared/authentication/authorization-code-flow). Om du inte redan har ett LinkedIn-konto kan du registrera dig på [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Logga in på [webbplatsen LinkedIn-utvecklare](https://www.developer.linkedin.com/) med dina LinkedIn-kontoautentiseringsuppgifter.
1. Välj **Mina appar** och klicka sedan på **skapa app**.
1. Ange **appens namn**, **LinkedIn-sida**, **URL för sekretess policy** och **app-logotyp**.
1. Godkänn LinkedIn- **API-villkoren för användning** och klicka på **skapa app**.
1. Välj fliken **autentisering** . Under **nycklar för autentisering** kopierar du värdena för **klient-ID** och **klient hemlighet**. Du behöver båda dessa för att konfigurera LinkedIn som en identitets leverantör i din klient. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.
1. Välj Redigera penna bredvid **auktoriserade omdirigerings-URL: er för din app** och välj sedan **Lägg till omdirigerings-URL**. Skriv `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` och Ersätt `your-tenant-name` med namnet på din klient. Du måste använda små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C. Välj **Uppdatera**.
2. Din LinkedIn-app är som standard inte godkänd för omfattningar som är relaterade till inloggning. Om du vill begära en granskning väljer du fliken **produkter** och väljer sedan **Logga in med LinkedIn**. När granskningen är klar kommer de obligatoriska omfattningarna att läggas till i ditt program.
   > [!NOTE]
   > Du kan visa de omfattningar som för närvarande är tillåtna för din app på fliken **auth** i avsnittet **OAuth 2,0-omfång** .

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>Konfigurera LinkedIn som identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj **LinkedIn**.
1. Ange ett **namn**. Till exempel *LinkedIn*.
1. För **klient-ID** anger du klient-ID för LinkedIn-programmet som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>Lägg till LinkedIn Identity Provider i ett användar flöde 

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill lägga till LinkedIn Identity-providern.
1. Under **leverantörer av sociala identitet** väljer du **LinkedIn**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra klient hemligheten som du tidigare registrerade i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ** väljer du `Manual` .
7. Ange ett **namn** för princip nyckeln. Ett exempel är `LinkedInSecret`. Prefixet *B2C_1A_* läggs automatiskt till namnet på din nyckel.
8. I **hemlighet** anger du den klient hemlighet som du tidigare har registrerat.
9. För **nyckel användning** väljer du `Signature` .
10. Klicka på **Skapa**.

## <a name="configure-linkedin-as-an-identity-provider"></a>Konfigurera LinkedIn som identitets leverantör

Om du vill att användarna ska kunna logga in med ett LinkedIn-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Definiera ett LinkedIn-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil.

1. Öppna filen * SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _ i redigeraren. Den här filen finns i det [Start paket för anpassad princip][starter-pack] som du laddade ned som en del av en av kraven.
1. Hitta elementet _ *ClaimsProviders**. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Ersätt värdet för **client_id** med klient-ID: t för det LinkedIn-program som du tidigare har spelat in.
1. Spara filen.

### <a name="add-the-claims-transformations"></a>Lägg till anspråks omvandlingarna

Den tekniska profilen LinkedIn kräver att **ExtractGivenNameFromLinkedInResponse** -och **ExtractSurNameFromLinkedInResponse** -postomvandlingar läggs till i listan över ClaimsTransformations. Om du inte har ett **ClaimsTransformations** -element definierat i filen lägger du till de överordnade XML-elementen som visas nedan. Anspråks omvandlingarna behöver också en ny anspråks typ som definierats med namnet **nullStringClaim**.

Lägg till elementet **BuildingBlocks** längst upp i *TrustFrameworkExtensions.xmls* filen. Se *TrustFrameworkBase.xml* ett exempel.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

## <a name="migration-from-v10-to-v20"></a>Migrering från v 1.0 till v 2.0

LinkedIn har nyligen [uppdaterat sina API: er från v 1.0 till v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Om du vill migrera den befintliga konfigurationen till den nya konfigurationen använder du informationen i följande avsnitt för att uppdatera elementen i den tekniska profilen.

### <a name="replace-items-in-the-metadata"></a>Ersätt objekt i metadata

I det befintliga **metadata** -elementet i **TechnicalProfile** uppdaterar du följande **objekt** element från:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Till:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Lägg till objekt i metadata

I TechnicalProfile **metadata** lägger du till följande **objekt** element:

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Uppdatera OutputClaims

I den befintliga **OutputClaims** av **TechnicalProfile** uppdaterar du följande **OutputClaim** -element från:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Till:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Lägg till nya OutputClaimsTransformation-element

Lägg till följande **OutputClaimsTransformation** -element i **OutputClaimsTransformations** i **TechnicalProfile**:

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definiera nya anspråk-omvandlingar och anspråks typ

I det sista steget lade du till nya anspråk-omvandlingar som måste definieras. Du definierar anspråks Transformationerna genom att lägga till dem i listan över **ClaimsTransformations**. Om du inte har ett **ClaimsTransformations** -element definierat i filen lägger du till de överordnade XML-elementen som visas nedan. Anspråks omvandlingarna behöver också en ny anspråks typ som definierats med namnet **nullStringClaim**.

**BuildingBlocks** -elementet ska läggas till längst upp i filen. Se *TrustframeworkBase.xml* som exempel.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Hämta en e-postadress

Som en del av LinkedIn-migreringen från v 1.0 till v 2.0 krävs ytterligare ett anrop till ett annat API för att hämta e-postadressen. Om du behöver skaffa e-postadressen under registreringen gör du följande:

1. Slutför stegen ovan för att tillåta att Azure AD B2C federera med LinkedIn så att användaren kan logga in. Som en del av federationen Azure AD B2C tar emot åtkomsttoken för LinkedIn.
1. Spara LinkedIn-åtkomsttoken i ett anspråk. [Se instruktionerna här](idp-pass-through-user-flow.md).
1. Lägg till följande anspråks leverantör som gör begäran till LinkedIn- `/emailAddress` API: et. För att godkänna den här begäran behöver du LinkedIn-åtkomsttoken.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Lägg till följande Orchestration-steg i din användar resa, så att API-anspråks leverantören utlöses när en användare loggar in med LinkedIn. Se till att uppdatera `Order` numret på rätt sätt. Lägg till det här steget direkt efter Orchestration-steget som utlöser den tekniska profilen för LinkedIn.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Att hämta e-postadressen från LinkedIn under registreringen är valfritt. Om du väljer att inte hämta e-postmeddelandet från LinkedIn men behöver ett under registreringen, måste användaren manuellt ange e-postadressen och verifiera den.

Ett fullständigt exempel på en princip som använder LinkedIn-identitetsprovider finns i [Start paketet för den anpassade principen](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migrering från v 1.0 till v 2.0

LinkedIn har nyligen [uppdaterat sina API: er från v 1.0 till v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Som en del av migreringen kan Azure AD B2C bara hämta det fullständiga namnet på LinkedIn-användaren under registreringen. Om en e-postadress är en av de attribut som samlas in under registreringen måste användaren manuellt ange e-postadressen och verifiera den.

::: zone-end