---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Ange TechnicalProfiles-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 66172fc9e258ae99e8ed263342025f5c33f7a168
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219680"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En teknisk profil är ett ramverk med en inbyggd mekanism för att kommunicera med olika typer av parter. Tekniska profiler används för att kommunicera med din Azure AD B2C-klient, för att skapa en användare eller läsa en användar profil. En teknisk profil kan självkontrolleras för att aktivera interaktion med användaren. Du kan till exempel samla in användarens autentiseringsuppgifter för att logga in och sedan återge sidan för registrerings sidan eller lösen ords återställningen.

## <a name="type-of-technical-profiles"></a>Typ av tekniska profiler

En teknisk profil möjliggör följande typer av scenarier:

- [Application Insights](analytics-with-application-insights.md) att skicka händelse data till [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) -ger stöd för Azure Active Directory B2C användar hantering.
- [Azure ad Multi-Factor Authentication](multi-factor-auth-technical-profile.md) – ger stöd för att verifiera ett telefonnummer med hjälp av azure AD Multi-Factor Authentication (MFA). 
- [Anspråks omvandling](claims-transformation-technical-profile.md) -anrop för anrop av utdata för att manipulera anspråks värden, validera anspråk eller ange standardvärden för en uppsättning utgående anspråk.
- [ID-token-tips](id-token-hint.md) – verifierar `id_token_hint` JWT-token, utfärdarens namn och token-målgruppen och extraherar anspråket från den inkommande token.
- [JWT-token-utfärdare](jwt-issuer-technical-profile.md) – avger en JWT-token som returneras tillbaka till det förlitande part programmet.
- [OAuth1](oauth1-technical-profile.md) -Federation med valfri OAuth 1,0-protokoll identitets leverantör.
- [OAuth2](oauth2-technical-profile.md) -Federation med valfri OAuth 2,0-protokoll identitets leverantör.
- [Ett eng ång slö sen ord](one-time-password-technical-profile.md) – ger stöd för att hantera genereringen och verifieringen av ett eng ång slö sen ord.
- [OpenID Connect](openid-connect-technical-profile.md) -Federation med valfri OpenID Connect Protocol Identity Provider.
- [Telefon faktor](phone-factor-technical-profile.md) – stöd för registrering och verifiering av telefonnummer.
- [RESTful-Provider](restful-technical-profile.md) – anrop till REST API tjänster, till exempel verifiera användarindata, utöka användar data eller integrera med branschspecifika program.
- [SAML Identity Provider](saml-identity-provider-technical-profile.md) – Federation med valfri SAML-protokoll identitets leverantör.
- [Utfärdare av SAML-token](saml-issuer-technical-profile.md) – genererar en SAML-token som returneras tillbaka till den förlitande part appen.
- [Självkontrollerad](self-asserted-technical-profile.md) – interagera med användaren. Du kan till exempel samla in användarens autentiseringsuppgifter för att logga in, återge registrerings sidan eller lösen ords återställning.
- [Sessionshantering](custom-policy-reference-sso.md) – hantera olika typer av sessioner.

## <a name="technical-profile-flow"></a>Tekniskt profil flöde

Alla typer av tekniska profiler delar samma koncept. Börja med att läsa ingångs anspråken, kör anspråk omvandling. Kommunicera sedan med den konfigurerade parten, till exempel en identitetsprovider, REST API eller Azure AD Directory-tjänster. När processen har slutförts returnerar den tekniska profilen utgående anspråk och kan köra transformering av utgående anspråk. Följande diagram visar hur omvandlingar och mappningar som refereras i den tekniska profilen bearbetas. När anspråks omvandlingen har körts lagras de utgående anspråken omedelbart i anspråks säcken. Oavsett vilken part den tekniska profilen interagerar med.

![Diagram som illustrerar det tekniska profil flödet](./media/technical-profiles/technical-profile-flow.png)

1. **Hantering av enkel inloggning (SSO)** – återställer den tekniska profilens sessionstillstånd med hjälp av SSO- [sessionshantering](custom-policy-reference-sso.md).
1. **Transformering av inmatade anspråk** – innan den tekniska profilen startas körs omvandlingen av inmatade [anspråk](claimstransformations.md)i Azure AD B2C.
1. Inloggade **anspråk** – anspråk hämtas från den anspråks väska som används för den tekniska profilen.
1. **Körning av teknisk profil** – den tekniska profilen utbyter anspråk med den konfigurerade parten. Exempel:
    - Omdirigera användaren till identitets leverantören för att slutföra inloggningen. Efter lyckad inloggning återgår användaren och den tekniska profil körningen fortsätter.
    - Anropa ett REST API när du skickar parametrar som InputClaims och få tillbaka information som OutputClaims.
    - Skapa eller uppdatera användar kontot.
    - Skickar och verifierar MFA SMS-meddelandet.
1. **Verifiering av tekniska profiler** – en [självkontrollerad teknisk profil](self-asserted-technical-profile.md) kan anropa [verifierings tekniska profiler](validation-technical-profile.md) för att verifiera de data som profileringen har gjort av användaren.
1. **Utgående anspråk** – anspråk returneras tillbaka till anspråks säcken. Du kan använda de här anspråken i nästa steg för att dirigera eller utföra anspråk på utdata.
1. [Transformeringar](claimstransformations.md)av **utdata-anspråk** – när den tekniska profilen har slutförts körs omvandlingen av utgående anspråk i Azure AD B2C. 
1. **Hantering av enkel inloggning (SSO)** – behåller teknisk profils data till sessionen med hjälp av [SSO-sessionshantering](custom-policy-reference-sso.md).

Ett **TechnicalProfiles** -element innehåller en uppsättning tekniska profiler som stöds av anspråks leverantören. Varje anspråks leverantör måste ha minst en teknisk profil. Den tekniska profilen avgör slut punkterna och de protokoll som behövs för att kommunicera med anspråks leverantören. En anspråks leverantör kan ha flera tekniska profiler.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**TechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
|---------|---------|---------|
| Id | Ja | En unik identifierare för den tekniska profilen. Den tekniska profilen kan refereras till med hjälp av den här identifieraren från andra element i princip filen. Till exempel **OrchestrationSteps** och **ValidationTechnicalProfile**. |

**TechnicalProfile** innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| Domain | 0:1 | Domän namnet för den tekniska profilen. Om din tekniska profil exempelvis anger Facebook Identity Provider, är domän namnet Facebook.com. |
| DisplayName | 1:1 | Visnings namnet för den tekniska profilen. |
| Description | 0:1 | Beskrivningen av den tekniska profilen. |
| Protokoll | 1:1 | Protokollet som används för kommunikation med den andra parten. |
| Metadata | 0:1 | En samling nyckel/värde som styr beteendet för den tekniska profilen. |
| InputTokenFormat | 0:1 | Formatet för Indataporten. Möjliga värden: `JSON` , `JWT` , `SAML11` eller `SAML2` . `JWT`Värdet representerar ett JSON Web token enligt IETF-specifikationen. `SAML11`Värdet representerar en SAML 1,1-säkerhetstoken som per Oasis-specifikation.  `SAML2`Värdet representerar en SAML 2,0-säkerhetstoken som per Oasis-specifikation. |
| OutputTokenFormat | 0:1 | Formatet för utdatabufferten. Möjliga värden: `JSON` , `JWT` , `SAML11` eller `SAML2` . |
| CryptographicKeys | 0:1 | En lista över kryptografiska nycklar som används i den tekniska profilen. |
| InputClaimsTransformations | 0:1 | En lista med tidigare definierade referenser till anspråks omvandlingar som ska utföras innan anspråk skickas till anspråks leverantören eller den förlitande parten. |
| InputClaims | 0:1 | En lista med tidigare definierade referenser till anspråks typer som tas in i den tekniska profilen. |
| PersistedClaims | 0:1 | En lista över tidigare definierade referenser till anspråks typer som kommer att behållas av den tekniska profilen. |
| DisplayClaims | 0:1 | En lista över tidigare definierade referenser till anspråks typer som presenteras av den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md). Funktionen DisplayClaims är för närvarande en för **hands version**. |
| OutputClaims | 0:1 | En lista med tidigare definierade referenser till anspråks typer som tas ut i den tekniska profilen. |
| OutputClaimsTransformations | 0:1 | En lista med tidigare definierade referenser till anspråks omvandlingar som ska utföras när anspråken tas emot från anspråks leverantören. |
| ValidationTechnicalProfiles | 0: n | En lista med referenser till andra tekniska profiler som den tekniska profilen använder i validerings syfte. Mer information finns i [verifiering teknisk profil](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Styr produktionen av ämnes namnet i tokens där ämnes namnet anges separat från anspråk. Till exempel OAuth eller SAML.  |
| IncludeInSso | 0:1 |  Om användningen av den här tekniska profilen ska använda enkel inloggning (SSO) för sessionen eller i stället kräva explicit interaktion. Det här elementet är endast giltigt i SelfAsserted-profiler som används i en teknisk validerings profil. Möjliga värden: `true` (standard) eller `false` . |
| IncludeClaimsFromTechnicalProfile | 0:1 | En identifierare för en teknisk profil från vilken du vill att alla indata-och utgående anspråk ska läggas till i den här tekniska profilen. Den tekniska profilen som refereras till måste definieras i samma princip fil. |
| IncludeTechnicalProfile |0:1 | En identifierare för en teknisk profil från vilken du vill att alla data ska läggas till i den här tekniska profilen. |
| UseTechnicalProfileForSessionManagement | 0:1 | En annan teknisk profil som ska användas för hantering av sessioner. |
|EnabledForUserJourneys| 0:1 |Kontrollerar om den tekniska profilen körs i en användar resa.  |

## <a name="protocol"></a>Protokoll

**Protokollet** anger vilket protokoll som ska användas för kommunikation med den andra parten. **Protokoll** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Name | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C som används som en del av den tekniska profilen. Möjliga värden:,,,, `OAuth1` `OAuth2` `SAML2` `OpenIdConnect` `Proprietary` eller `None` . |
| Hanterare | Inga | När protokoll namnet är inställt på `Proprietary` anger du namnet på den sammansättning som används av Azure AD B2C för att fastställa protokoll hanteraren. |

## <a name="metadata"></a>Metadata

Elementet **metadata** innehåller relevanta konfigurations alternativ för ett speciellt protokoll. Listan över metadata som stöds finns dokumenterade i motsvarande [tekniska profil](#type-of-technical-profiles) specifikation. Ett **metadataelement** innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| Objekt | 0: n | Metadata som relaterar till den tekniska profilen. Varje typ av teknisk profil har en annan uppsättning metadata-objekt. Mer information finns i avsnittet tekniska profil typer.  |

### <a name="item"></a>Objekt

**Objekt** elementet i **metadata** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Nyckel | Ja | Nyckeln metadata. Se varje [teknisk profil typ](#type-of-technical-profiles)för listan med metadataobjekt. |

I följande exempel illustreras användningen av metadata som är relevanta för [OAuth2 Technical Profile](oauth2-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

I följande exempel illustreras användningen av metadata som är relevanta för [REST API tekniska profilen](restful-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

För att upprätta förtroende med de tjänster som den integrerar med, Azure AD B2C lagra hemligheter och certifikat i form av [princip nycklar](policy-keys-overview.md). Under den tekniska profilen som körs hämtar Azure AD B2C de kryptografiska nycklarna från Azure AD B2C princip nycklar. Använder sedan nycklarna för att upprätta förtroende, kryptera eller signera en token. Dessa förtroenden består av:

- Federation med [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys)och [SAML](saml-identity-provider-technical-profile.md#cryptographic-keys) Identity providers
- Skydda anslutningen till [REST API Services](secure-rest-api.md)
- Signera och kryptera [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) -och [SAML](saml-issuer-technical-profile.md#cryptographic-keys) -token

**CryptographicKeys** -elementet innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| Nyckel | 1: n | En kryptografisk nyckel som används i den här tekniska profilen. |

### <a name="key"></a>Nyckel

**Nyckel** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Inga | En unik identifierare för ett visst nyckel par som refereras från andra element i princip filen. |
| StorageReferenceId | Ja | En identifierare för lagrings nyckel behållare som refereras från andra element i princip filen. |

## <a name="input-claims-transformations"></a>Transformeringar av inmatade anspråk

**InputClaimsTransformations** -elementet kan innehålla en samling med ingångs anspråk för omvandlings element som används för att ändra inloggade anspråk eller skapa nya. 

De utgående anspråken för en tidigare anspråk-omvandling i omvandlings samlingen för anspråk kan vara indata-anspråk för en efterföljande omvandling av indata-anspråk så att du kan ha en sekvens av anspråks omvandlingar beroende på varandra.

**InputClaimsTransformations** -elementet innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Identifieraren för en anspråks omvandling som ska köras innan anspråk skickas till anspråksprovidern eller den förlitande parten. En anspråks omvandling kan användas för att ändra befintliga ClaimsSchema-anspråk eller skapa nya. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en anspråks omvandling som redan har definierats i princip filen eller den överordnade princip filen. |

Följande tekniska profiler hänvisar till omvandlingen av **CreateOtherMailsFromEmail** -anspråk. Transformationen Claims lägger till värdet för `email` anspråket i `otherMails` samlingen innan data sparas i katalogen.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>Inmatade anspråk

**InputClaims** hämtar anspråk från anspråks säcken och används för den tekniska profilen. En [självkontrollerad teknisk profil](self-asserted-technical-profile.md) använder till exempel indata-anspråk för att fylla i de utgående anspråk som användaren tillhandahåller. En REST API teknisk profil använder ingångs anspråk för att skicka indataparametrar till REST API-slutpunkten. Azure Active Directory använder ingångs anspråk som unik identifierare för att läsa, uppdatera eller ta bort ett konto.

**InputClaims** -elementet innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | En förväntad typ av Indatatyp. |

### <a name="inputclaim"></a>InputClaim

**InputClaim** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifieraren för en anspråks typ. Anspråket har redan definierats i avsnittet anspråks schema i princip filen, eller överordnad princip fil. |
| Standar | Inga | Ett standardvärde som ska användas för att skapa ett anspråk om det anspråk som anges av ClaimTypeReferenceId inte finns så att det resulterande anspråket kan användas som en InputClaim av den tekniska profilen. |
| PartnerClaimType | Inga | Identifieraren för anspråks typen för den externa partner som den angivna princip anspråks typen mappar till. Om attributet PartnerClaimType inte anges mappas den angivna typen av princip anspråk till partner anspråks typen med samma namn. Använd den här egenskapen när anspråks typ namnet skiljer sig från den andra parten. Till exempel är det första anspråks namnet ' givenName ', medan partnern använder ett anspråk med namnet ' first_name '. |

## <a name="display-claims"></a>Visa anspråk

**DisplayClaims** -elementet innehåller en lista över anspråk som ska visas på skärmen för att samla in data från användaren. I visnings anspråks samlingen kan du inkludera en referens till en [anspråks typ](claimsschema.md)eller en visare [som du](display-controls.md) har skapat. 

- En anspråks typ är en referens till ett anspråk som ska visas på skärmen. 
  - Om du vill tvinga användaren att ange ett värde för ett bestämt anspråk anger du det **obligatoriska** attributet för **DisplayClaim** -elementet till `true` .
  - Om du vill förkonfigurera värden för visnings anspråk använder du de inloggade anspråk som tidigare beskrivits. Elementet kan också innehålla ett standardvärde.
  - Elementet **claimType** i **DisplayClaims** -samlingen måste ange **UserInputType** -elementet till alla typer av användarindata som stöds av Azure AD B2C. Exempel: `TextBox` eller `DropdownSingleSelect`.
- En visnings kontroll är ett användar gränssnitts element som har särskilda funktioner och som samverkar med Azure AD B2C backend-tjänsten. Det gör att användaren kan utföra åtgärder på sidan som anropar en teknisk profil för verifiering på Server sidan. Du kan till exempel verifiera en e-postadress, telefonnummer eller kund lojalitets nummer.

Ordningen på elementen i **DisplayClaims** anger i vilken ordning som Azure AD B2C återger anspråk på skärmen. 

**DisplayClaims** -elementet innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | En förväntad typ av Indatatyp. |

### <a name="displayclaim"></a>DisplayClaim

**DisplayClaim** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Inga | Identifieraren för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen eller den överordnade princip filen. |
| DisplayControlReferenceId | Inga | Identifieraren för en [visnings kontroll](display-controls.md) som redan har definierats i ClaimsSchema-avsnittet i princip filen eller den överordnade princip filen. |
| Obligatorisk | Inga | Anger om visnings kravet krävs. |

I följande exempel illustreras användningen av Visa anspråk och visa kontroller med i en självkontrollerad teknisk profil.

![En självkontrollerad teknisk profil med visnings anspråk](./media/technical-profiles/display-claims.png)

I följande tekniska profil:

- Det första visnings kravet gör en referens till `emailVerificationControl` visnings kontrollen, som samlar in och verifierar e-postadressen.
- Det femte visnings kravet gör en referens till `phoneVerificationControl` visnings kontrollen, som samlar in och verifierar ett telefonnummer.
- De andra visnings anspråken är ClaimTypes som ska samlas in från användaren.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

### <a name="persisted-claims"></a>Beständiga anspråk

**PersistedClaims** -elementet innehåller alla värden som ska sparas av den [tekniska profilen i Azure AD](active-directory-technical-profile.md) med eventuell mappnings information mellan en anspråks typ som redan definierats i avsnittet [ClaimsSchema](claimsschema.md) i principen och namnet på Azure AD-attributnamnet.

Namnet på anspråket är namnet på [Azure AD-attributet](user-profile-attributes.md) om inte attributet **PartnerClaimType** anges, som innehåller namnet på Azure AD-attributet.

**PersistedClaims** -elementet innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Anspråks typen som ska sparas. |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifieraren för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen eller den överordnade princip filen. |
| Standar | Inga | Ett standardvärde som ska användas för att skapa ett anspråk om anspråket inte finns. |
| PartnerClaimType | Inga | Identifieraren för anspråks typen för den externa partner som den angivna princip anspråks typen mappar till. Om attributet PartnerClaimType inte anges mappas den angivna typen av princip anspråk till partner anspråks typen med samma namn. Använd den här egenskapen när anspråks typ namnet skiljer sig från den andra parten. Till exempel är det första anspråks namnet ' givenName ', medan partnern använder ett anspråk med namnet ' first_name '. |

I följande exempel är den tekniska profilen **AAD-UserWriteUsingLogonEmail** eller [Start paketet](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)som skapar ett nytt lokalt konto kvar efter anspråk:

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** är en samling anspråk som returneras tillbaka till anspråks säcken när den tekniska profilen har slutförts. Du kan använda de här anspråken i nästa steg för att dirigera eller utföra anspråk på utdata. **OutputClaims** -elementet innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 1: n | En förväntad typ av utgående anspråk. |

### <a name="outputclaim"></a>OutputClaim

**OutputClaim** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Identifieraren för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen eller den överordnade princip filen. |
| Standar | Inga | Ett standardvärde som ska användas för att skapa ett anspråk om anspråket inte finns. |
|AlwaysUseDefaultValue |Inga |Tvinga användningen av standardvärdet.  |
| PartnerClaimType | Inga | Identifieraren för anspråks typen för den externa partner som den angivna princip anspråks typen mappar till. Om typ attributet för partner anspråk inte anges mappas den angivna typen av princip anspråk till partner anspråks typen med samma namn. Använd den här egenskapen när anspråks typ namnet skiljer sig från den andra parten. Till exempel är det första anspråks namnet ' givenName ', medan partnern använder ett anspråk med namnet ' first_name '. |

## <a name="output-claims-transformations"></a>Transformeringar av utgående anspråk

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element. Transformeringarna av utgående anspråk används för att ändra de utgående anspråken eller skapa nya. Efter körningen sätts de utgående anspråken tillbaka i anspråks säcken. Du kan använda dessa anspråk i nästa Orchestration-steg.

De utgående anspråken för en tidigare anspråk-omvandling i insamlingen av anspråk kan vara indata-anspråk för en efterföljande omvandling av indata-anspråk, vilket gör att du kan ha en sekvens av anspråks omvandlingar beroende på varandra.

**OutputClaimsTransformations** -elementet innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | Identifierare för anspråks omvandlingar som ska köras innan anspråk skickas till anspråksprovidern eller den förlitande parten. En anspråks omvandling kan användas för att ändra befintliga ClaimsSchema-anspråk eller skapa nya. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en anspråks omvandling som redan har definierats i princip filen eller den överordnade princip filen. |

Följande tekniska profil refererar till omvandlingen av AssertAccountEnabledIsTrue-anspråk för att utvärdera om kontot är aktiverat eller inte efter läsning av `accountEnabled` anspråket från katalogen.    

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>Verifiera tekniska profiler

En teknisk validerings profil används för att verifiera utgående anspråk i en [egen kontrollerad teknisk profil](self-asserted-technical-profile.md#validation-technical-profiles). En teknisk verifierings profil är en vanlig teknisk profil från alla protokoll, till exempel [Azure Active Directory](active-directory-technical-profile.md) eller en [REST API](restful-technical-profile.md). Den tekniska verifierings profilen returnerar utgående anspråk eller returnerar felkoden. Fel meddelandet återges för användaren på skärmen, så att användaren kan försöka igen.

Följande diagram illustrerar hur Azure AD B2C använder en teknisk validerings profil för att verifiera användarautentiseringsuppgifter

![Diagram validering, tekniskt profil flöde](./media/technical-profiles/validation-technical-profile.png) 

**ValidationTechnicalProfiles** -elementet innehåller följande element:

| Element | Förekomster | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Identifierarna för de tekniska profiler som används validerar några eller alla utgående anspråk för den refererande tekniska profilen. Alla indata-anspråk för den refererade tekniska profilen måste visas i de utgående anspråken för den refererande tekniska profilen. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i princip filen eller överordnad princip fil. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** definierar ämnes namnet som används i tokens i en princip för [förlitande part](relyingparty.md#subjectnaminginfo). **SubjectNamingInfo** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimType | Ja | En identifierare för en anspråks typ som redan har definierats i avsnittet ClaimsSchema i princip filen. |

## <a name="include-technical-profile"></a>Ta med teknisk profil

En teknisk profil kan innehålla en annan teknisk profil för att ändra inställningar eller lägga till nya funktioner. **IncludeTechnicalProfile** -elementet är en referens till den gemensamma tekniska profil som en teknisk profil härleds från. Använd inkludering när du har flera tekniska profiler som delar kärn elementen för att minska redundansen och komplexiteten hos dina princip element. Använd en vanlig teknisk profil med den gemensamma uppsättningen konfiguration, tillsammans med en speciell uppgift som innehåller de vanliga tekniska profilerna. 

Anta att du har en [REST API teknisk profil](restful-technical-profile.md) med en enda slut punkt där du måste skicka en annan uppsättning anspråk för olika scenarier. Skapa en gemensam teknisk profil med delade funktioner, till exempel REST API slut punkts-URI, metadata, autentiseringstyp och kryptografiska nycklar. Skapa en speciell uppgift tekniska profiler som innehåller den vanliga tekniska profilen. Lägg sedan till indata-anspråk, utgående anspråk eller Skriv över den REST API slut punkts-URI som är relevant för den tekniska profilen.

**IncludeTechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i princip filen eller överordnad princip fil. |


Följande exempel illustrerar användningen av inkludering:

- *REST-API – common* – en vanlig teknisk profil med den grundläggande konfigurationen.
- *Rest-ValidateProfile* – innehåller den tekniska profilen *REST-API-Commom* och anger indata och utgående anspråk.
- *Rest-UpdateProfile* – innehåller den tekniska profilen *REST-API-Commom* , anger indata-anspråk och skriver över `ServiceUrl` metadata.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Commom">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Commom" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Commom" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multi-level-inclusion"></a>Inkludering av flera nivåer 

En teknisk profil kan innehålla en enda teknisk profil. Det finns ingen gräns för antalet inkluderings nivåer. Till exempel innehåller den tekniska profilen **AAD-UserReadUsingAlternativeSecurityId-noerror** filen **AAD-UserReadUsingAlternativeSecurityId**. Den här tekniska profilen anger `RaiseErrorIfClaimsPrincipalDoesNotExist` metadataobjektet till `true` och genererar ett fel om ett socialt konto inte finns i katalogen. **AAD-UserReadUsingAlternativeSecurityId – noerror** åsidosätter detta beteende och inaktiverar det fel meddelandet.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** innehåller den `AAD-Common` tekniska profilen.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Både **AAD-UserReadUsingAlternativeSecurityId-noerror** och  **AAD-UserReadUsingAlternativeSecurityId** anger inte det **protokoll** element som krävs, eftersom det har angetts i **AAD-vanlig** teknisk profil.

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>Använd teknisk profil för hantering av sessioner

**UseTechnicalProfileForSessionManagement** -element-referensen till [den tekniska profilen för enkel inloggnings session](custom-policy-reference-sso.md). **UseTechnicalProfileForSessionManagement** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i princip filen eller överordnad princip fil. |

## <a name="enabled-for-user-journeys"></a>Aktive rad för användar resor

[ClaimsProviderSelections](userjourneys.md#claimsproviderselection) i en användar resa definierar listan över val alternativ för anspråks leverantör och deras ordning. Med det **EnabledForUserJourneys** -element som du filtrerar, vilken anspråks leverantör som är tillgänglig för användaren. **EnabledForUserJourneys** -elementet innehåller ett av följande värden:

- Kör **alltid** den tekniska profilen.
- Hoppa **aldrig** över den tekniska profilen.
- **OnClaimsExistence** körs bara när ett visst anspråk som anges i den tekniska profilen finns.
- **OnItemExistenceInStringCollectionClaim**, kör bara när ett objekt finns i ett anspråk för en sträng samling.
- **OnItemAbsenceInStringCollectionClaim** körs bara när det inte finns något objekt i ett anspråks krav för en sträng samling.

Om du använder **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** eller **OnItemAbsenceInStringCollectionClaim** måste du ange följande metadata: 

- **ClaimTypeOnWhichToEnable** – anger det anspråks typ som ska utvärderas.
- **ClaimValueOnWhichToEnable** – anger värdet som ska jämföras.

Följande tekniska profil körs endast om **identityprovider** -sträng samlingen innehåller värdet för `facebook.com` :

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
