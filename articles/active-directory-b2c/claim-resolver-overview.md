---
title: Anspråk matchare i anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du använder anspråk matchare i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 682b83d7016a89b27b5c936853abda1438f59c28
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508024"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Om anspråk matchare i Azure Active Directory B2C anpassade principer

Anspråk matchare i Azure Active Directory B2C (Azure AD B2C) [anpassade principer](custom-policy-overview.md) ger Sammanhangs information om en auktoriseringsbegäran, till exempel princip namn, KORRELATIONS-ID för begäran, användar gränssnitts språk med mera.

Om du vill använda en anspråks lösare i ett indata-eller utgående anspråk definierar du en sträng **claimType**, under elementet [ClaimsSchema](claimsschema.md) , och anger sedan **DefaultValue** till anspråks lösa ren i elementet indata-eller utdata-anspråk. Azure AD B2C läser värdet för anspråks matcharen och använder värdet i den tekniska profilen.

I följande exempel definieras en anspråks typ `correlationId` med namnet med en **data** typ `string` .

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

I den tekniska profilen mappar du anspråks matcharen till anspråks typen. Azure AD B2C fyller i värdet för anspråks matcharen `{Context:CorrelationId}` i anspråket `correlationId` och skickar anspråket till den tekniska profilen.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typer av anspråks matchare

I följande avsnitt listas tillgängliga anspråks lösningar.

### <a name="culture"></a>Kultur

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Culture: LanguageName} | Den två bokstävernas ISO-kod för språket. | en |
| {Culture: LCID}   | LCID för språk koden. | 1033 |
| {Culture: RegionName} | ISO-koden för den två bokstaven för regionen. | USA |
| {Culture: RFC5646} | Språk koden RFC5646. | sv-SE |

### <a name="policy"></a>Policy

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Princip: PolicyId} | Princip namnet för den förlitande parten. | B2C_1A_signup_signin |
| {Princip: RelyingPartyTenantId} | Klient-ID för den förlitande part principen. | your-tenant.onmicrosoft.com |
| {Princip: TenantObjectId} | Klientens objekt-ID för den förlitande part principen. | 00000000-0000-0000-0000-000000000000 |
| {Princip: TrustFrameworkTenantId} | Klient-ID för förtroende ramverket. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {OIDC: AuthenticationContextReferences} |Frågesträngparametern `acr_values` . | Saknas |
| {OIDC: ClientId} |Frågesträngparametern `client_id`  . | 00000000-0000-0000-0000-000000000000 |
| {OIDC: DomainHint} |Frågesträngparametern `domain_hint`  . | facebook.com |
| {OIDC: LoginHint} |  Frågesträngparametern `login_hint` . | someone@contoso.com |
| {OIDC: MaxAge} | `max_age`. | Saknas |
| {OIDC: nonce} |Frågesträngparametern `Nonce`  . | defaultNonce |
| {OIDC: lösen ord}| [Autentiseringsuppgifter för resurs ägarens lösen ord flödar](ropc-custom.md) användarens lösen ord.| password1| 
| {OIDC: prompt} | Frågesträngparametern `prompt` . | inloggning |
| {OIDC: RedirectUri} |Frågesträngparametern `redirect_uri`  . | https://jwt.ms |
| {OIDC: resurs} |Frågesträngparametern `resource`  . | Saknas |
| {OIDC: omfång} |Frågesträngparametern `scope`  . | OpenID |
| {OIDC: username}| [Autentiseringsuppgifter för resurs ägar lösen ord flöda](ropc-custom.md) användarens användar namn.| emily@contoso.com| 

### <a name="context"></a>Kontext

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Context: BuildNumber} | Versionen av ID Experience Framework (versions nummer).  | 1.0.507.0 |
| {Context: CorrelationId} | Korrelations-ID: t.  | 00000000-0000-0000-0000-000000000000 |
| {Context: DateTimeInUtc} |Datum tid i UTC.  | 10/10/2018 12:00:00 PM |
| {Context: DeploymentMode} |Princip distributions läget.  | Produktion |
| {Context: IPAddress} | Användarens IP-adress. | 11.111.111.11 |
| {Context: KMSI avgör} | Anger om kryss rutan [Behåll mig inloggad](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) är markerad. |  true |

### <a name="claims"></a>Anspråk 

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Claim: anspråks typ} | En identifierare för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen eller den överordnade princip filen.  Till exempel: `{Claim:displayName}` , eller `{Claim:objectId}` . | Ett värde för anspråks typ.|


### <a name="oauth2-key-value-parameters"></a>OAuth2 nyckel värdes parametrar

Alla parameter namn som ingår i en OIDC-eller OAuth2-begäran kan mappas till ett anspråk i användar resan. Begäran från programmet kan till exempel innehålla en frågesträngparametern med namnet `app_session` , `loyalty_number` eller en anpassad frågesträng.

| Begär | Beskrivning | Exempel |
| ----- | ----------------------- | --------|
| {OAUTH-KV: campaignId} | En frågesträngparametern. | Hawaii |
| {OAUTH-KV: app_session} | En frågesträngparametern. | A3C5R |
| {OAUTH-KV: loyalty_number} | En frågesträngparametern. | 1234 |
| {OAUTH-KV: valfri anpassad frågesträng} | En frågesträngparametern. | Saknas |

### <a name="oauth2"></a>OAuth2

| Begär | Beskrivning | Exempel |
| ----- | ----------------------- | --------|
| {OAuth2: access_token} | Åtkomsttoken. | Saknas |
| {OAuth2: refresh_token} | Refresh-token. | Saknas |


### <a name="saml"></a>SAML

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {SAML: AuthnContextClassReferences} | `AuthnContextClassRef`Elementet-värdet från SAML-begäran. | urn: Oasis: Names: TC: SAML: 2.0: AC: klasser: PasswordProtectedTransport |
| {SAML: NameIdPolicyFormat} | `Format`Attributet, från `NameIDPolicy` ELEMENTET i SAML-begäran. | urn: Oasis: Names: TC: SAML: 1.1: NameID-format: emailAddress |
| {SAML: Issuer} |  SAML- `Issuer` elementets värde för SAML-begäran.| `https://contoso.com` |
| {SAML: AllowCreate} | `AllowCreate`Attributvärdet, från `NameIDPolicy` ELEMENTET i SAML-begäran. | Sant |
| {SAML: ForceAuthn} | `ForceAuthN`Attributvärdet, från `AuthnRequest` ELEMENTET i SAML-begäran. | Sant |
| {SAML: ProviderName} | `ProviderName`Attributvärdet, från `AuthnRequest` ELEMENTET i SAML-begäran.| Contoso.com |
| {SAML: RelayState} | Frågesträngparametern `RelayState` .| 
| {SAML: subject} | `Subject`Från NameId-elementet för SAML authn-begäran.| 

## <a name="using-claim-resolvers"></a>Använda anspråks matchare

Du kan använda anspråk matchare med följande element:

| Objekt | Element | Inställningar |
| ----- | ----------------------- | --------|
|Application Insights teknisk profil |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) teknisk profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md) teknisk profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID Connect](openid-connect-technical-profile.md) , teknisk profil| `InputClaim`, `OutputClaim`| 1, 2|
|Teknisk profil för [anspråks omvandling](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Teknisk profil för [RESTful-Provider](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Teknisk profil för [SAML Identity Provider](saml-identity-provider-technical-profile.md)| `OutputClaim`| 1, 2|
|[Egen, kontrollerad](self-asserted-technical-profile.md) teknisk profil| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) teknisk profil| `OutputClaim`| 2 |

Autentiseringsinställningar
1. `IncludeClaimResolvingInClaimsHandling`Metadata måste anges till `true` .
1. Attributet indata-eller utdata-anspråk `AlwaysUseDefaultValue` måste anges till `true` .

## <a name="claim-resolvers-samples"></a>Exempel på anspråks lösare

### <a name="restful-technical-profile"></a>RESTful teknisk profil

I en [RESTful](restful-technical-profile.md) teknisk profil kanske du vill skicka användar språket, princip namnet, omfattningen och klient-ID: t. Baserat på de anspråk som REST API kan köra anpassad affärs logik, och om det behövs för att utlösa ett lokaliserat fel meddelande.

I följande exempel visas en RESTful teknisk profil med det här scenariot:

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Direkt inloggning

Med hjälp av anspråks matchare kan du fylla i inloggnings namnet eller dirigera inloggningen till en speciell social identitetsprovider, till exempel Facebook, LinkedIn eller en Microsoft-konto. Mer information finns i [Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Anpassning av dynamiskt gränssnitt

Med Azure AD B2C kan du skicka frågesträngs parametrar till definitions slut punkter för HTML-innehåll för att dynamiskt återge sid innehållet. Med den här funktionen kan du till exempel ändra bakgrunds bilden på Azure AD B2C registrerings-eller inloggnings sida baserat på en anpassad parameter som du skickar från ditt webb program eller mobil program. Mer information finns i [Konfigurera användar gränssnittet dynamiskt genom att använda anpassade principer i Azure Active Directory B2C](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri). Du kan också lokalisera HTML-sidan baserat på en språk parameter, eller så kan du ändra innehållet baserat på klient-ID: t.

Följande exempel skickar i frågesträngparametern med namnet **campaignId** med värdet `Hawaii` , **språk** koden `en-US` och **appen** som representerar klient-ID:

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Därför skickar Azure AD B2C ovanstående parametrar till sidan HTML-innehåll:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Innehålls definition

I en [ContentDefinition](contentdefinitions.md) `LoadUri` kan du skicka anspråk matchare för att hämta innehåll från olika platser, baserat på de parametrar som används.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Application Insights teknisk profil

Med Azure Application insikter och anspråks matchare kan du få insikter om användar beteendet. I Application Insights teknisk profil skickar du inloggade anspråk som är sparade för att Azure Application insikter. Mer information finns i [spåra användar beteende i Azure AD B2C-transporter med hjälp av Application Insights](analytics-with-application-insights.md). I följande exempel skickas princip-ID: t, korrelations-ID, språk och klient-ID till Azure Application insikter.

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Princip för förlitande part

I en teknisk profil för [förlitande part](relyingparty.md) , kanske du vill skicka klient-ID eller KORRELATIONS-ID till det förlitande part programmet i JWT.

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
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
