---
title: Hantering av enkel inloggning med anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du hanterar SSO-sessioner med anpassade principer i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 453042766c427b05ec1ee1090a0702f64065542d
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508058"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Hantering av enkel inloggning i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Hantering av [enkel inloggning (SSO)](session-behavior.md) använder samma semantik som andra tekniska profiler i anpassade principer. När ett Orchestration-steg körs, frågas den tekniska profilen som är associerad med steget för en `UseTechnicalProfileForSessionManagement` referens. Om det finns en sådan, kontrol leras den refererade SSO-replikeringsprovidern sedan för att se om användaren är en session-deltagare. I så fall, används SSO-replikeringsprovidern för att fylla i sessionen igen. När körningen av ett Dirigerings steg har slutförts används providern för att lagra information i sessionen om en SSO-replikeringsprovider har angetts.

Azure AD B2C har definierat ett antal SSO-användarsessioner som kan användas:

|Replikeringsprovider  |Omfång  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  Inga       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C intern sessionshanteraren.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Mellan Azure AD B2C och OAuth1, OAuth2 eller OpenId Connect Identity Provider.        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Mellan ett OAuth2-eller OpenId Connect-program för förlitande part och Azure AD B2C.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Mellan Azure AD B2C och SAML Identity Provider. Och mellan en SAML-tjänstprovider (förlitande parts program) och Azure AD B2C.  |        




SSO-hanterings klasser anges med hjälp av `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` elementet i en teknisk profil.

## <a name="input-claims"></a>Inmatade anspråk

`InputClaims`Elementet är tomt eller saknas.

## <a name="persisted-claims"></a>Beständiga anspråk

Anspråk som måste returneras till programmet eller används av villkor i efterföljande steg, ska lagras i sessionen eller utökas av en läsning från användarens profil i katalogen. Om du använder beständiga anspråk ser du till att det inte går att köra autentiseringen på saknade anspråk. Om du vill lägga till anspråk i sessionen använder du `<PersistedClaims>` elementet i den tekniska profilen. När providern används för att fylla i sessionen läggs de beständiga anspråken till i anspråks säcken.

## <a name="output-claims"></a>Utgående anspråk

`<OutputClaims>`Används för att hämta anspråk från sessionen.

## <a name="session-providers"></a>Sessions-providers

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Den här providern gör inget under namnet. Den här providern kan användas för att förhindra SSO-beteende för en speciell teknisk profil. Följande `SM-Noop` tekniska profil ingår i [Start paketet för den anpassade principen](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Den här providern kan användas för att lagra anspråk i en session. Den här providern refereras vanligt vis till i en teknisk profil som används för att hantera lokala och federerade konton. Följande `SM-AAD` tekniska profil ingår i [Start paketet för den anpassade principen](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


Följande `SM-MFA` tekniska profil ingår i [Start paketet för den anpassade principen](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa` . Den här tekniska profilen hanterar Multi-Factor Authentication-sessionen.

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Den här providern används för att utelämna skärmen "Välj identitetsprovider" och logga ut från en federerad identitets leverantör. Det hänvisas vanligt vis till en teknisk profil som kon figurer ATS för en federerad identitets leverantör, till exempel Facebook eller Azure Active Directory. Följande `SM-SocialLogin` tekniska profil ingår i [Start paketet för den anpassade principen](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Nej | Används inte för närvarande, kan ignoreras. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Den här providern används för att hantera Azure AD B2C-sessioner mellan en OAuth2 eller OpenId Connect-förlitande part och Azure AD B2C.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Den här providern används för att hantera Azure AD B2C SAML-sessioner mellan ett förlitande parts program eller en federerad SAML-identitetsprovider. När du använder SSO-providern för att lagra en SAML-identitetsprovider `RegisterServiceProviders` måste du ställa in på `false` . Följande `SM-Saml-idp` tekniska profil används av den [tekniska profilen för SAML Identity Provider](saml-identity-provider-technical-profile.md).

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

När du använder providern för att lagra B2C SAML-sessionen `RegisterServiceProviders` måste anges till `true` . Utloggningen av SAML-sessionen kräver `SessionIndex` och `NameID` slutförs.

Följande `SM-Saml-issuer` tekniska profil används av den [tekniska profilen för SAML-utfärdaren](saml-issuer-technical-profile.md)

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning|
| --- | --- | --- |
| IncludeSessionIndex | Nej | Används inte för närvarande, kan ignoreras.|
| RegisterServiceProviders | Nej | Anger att leverantören ska registrera alla SAML-tjänstleverantörer som har utfärdat en kontroll. Möjliga värden: `true` (standard) eller `false` .|


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar sessionens beteende](session-behavior.md).
