---
title: Definiera en teknisk Azure AD-profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en Azure Active Directory teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3f92bfe98a45117264c957481a75493de652abc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97508109"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en Azure Active Directory teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för Azure Active Directory användar hantering. I den här artikeln beskrivs de olika kraven för en teknisk profil för att interagera med en anspråks leverantör som stöder detta standardiserade protokoll.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary` . Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på protokoll hanterarens sammansättning `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

I de tekniska profilerna för [anpassade principer för start paket](custom-policy-get-started.md#custom-policy-starter-pack) för Azure AD ingår **AAD-vanliga** tekniska profiler. De tekniska profilerna för Azure AD anger inte protokollet eftersom protokollet har kon figurer ATS i **AAD-common** Technical Profile:
 
- **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingAlternativeSecurityId-noerror** – slå upp ett socialt konto i katalogen.
- **AAD-UserWriteUsingAlternativeSecurityId** – skapa ett nytt socialt konto.
- **AAD-UserReadUsingEmailAddress** – leta upp ett lokalt konto i katalogen.
- **AAD-UserWriteUsingLogonEmail** – skapa ett nytt lokalt konto.
- **AAD-UserWritePasswordUsingObjectId** – uppdatera ett lösen ord för ett lokalt konto.
- **AAD-UserWriteProfileUsingObjectId** – uppdatera en användar profil för ett lokalt eller socialt konto.
- **AAD-UserReadUsingObjectId** – Läs en användar profil för ett lokalt eller socialt konto.
- **AAD-UserWritePhoneNumberUsingObjectId** – Skriv MFA-telefonnumret för ett lokalt eller socialt konto

I följande exempel visas **AAD – vanlig** teknisk profil:

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

InputClaims-elementet innehåller ett anspråk, som används för att leta upp ett konto i katalogen eller skapa ett nytt. Det måste finnas exakt ett InputClaim-element i den inloggade anspråks samlingen för alla Azure AD-tekniska profiler. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i Azure Active Directory.

Om du vill läsa, uppdatera eller ta bort ett befintligt användar konto är indata-anspråk en nyckel som unikt identifierar kontot i Azure AD-katalogen. Till exempel **ObjectID**, **userPrincipalName**, **signInNames. EmailAddress**, **signInNames. username** eller **alternativeSecurityId**. 

Om du vill skapa ett nytt användar konto är indata-anspråk en nyckel som unikt identifierar ett lokalt eller federerat konto. Till exempel lokalt konto: **signInNames. EmailAddress** eller **signInNames. username**. För ett federerat konto: **alternativeSecurityId**.

[InputClaimsTransformations](technicalprofiles.md#input-claims-transformations) -elementet kan innehålla en samling ingångs anspråk för omvandlings element som används för att ändra det inloggade anspråket eller skapa ett nytt.

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** -elementet innehåller en lista över anspråk som returneras av den tekniska Azure AD-profilen. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i Azure Active Directory. Du kan också inkludera anspråk som inte returneras av Azure Active Directory, så länge som du ställer in `DefaultValue` attributet.

[OutputClaimsTransformations](technicalprofiles.md#output-claims-transformations) -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

Till exempel skapar den tekniska profilen **AAD-UserWriteUsingLogonEmail** ett lokalt konto och returnerar följande anspråk:

- **ObjectID**, som är identifierare för det nya kontot
- **newUser**, som anger om användaren är ny
- **authenticationSource**, som anger autentiseringen till `localAccountAuthentication`
- **userPrincipalName**, som är User Principal Name för det nya kontot
- **signInNames. EmailAddress**, som är kontots inloggnings namn, på liknande sätt som **e-** postanspråket

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** -elementet innehåller alla värden som ska sparas av Azure AD med möjlig mappnings information mellan en anspråks typ som redan har definierats i [ClaimsSchema](claimsschema.md) -avsnittet i principen och namnet på Azure AD-attributnamnet.

Den tekniska profilen **AAD-UserWriteUsingLogonEmail** , som skapar ett nytt lokalt konto, behåller följande anspråk:

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Namnet på anspråket är namnet på Azure AD-attributet om inte attributet **PartnerClaimType** anges, som innehåller namnet på Azure AD-attributet.

## <a name="requirements-of-an-operation"></a>Krav för en åtgärd

- Det måste finnas exakt ett **InputClaim** -element i anspråks säcken för alla Azure AD-tekniska profiler.
- I [artikeln med användar profil attribut](user-profile-attributes.md) beskrivs de Azure AD B2C-användarattribut som stöds som du kan använda i indata-anspråk, utgående anspråk och beständiga anspråk. 
- Om åtgärden är `Write` eller `DeleteClaims` , måste den också visas i ett **PersistedClaims** -element.
- Värdet för **userPrincipalName** -anspråket måste ha formatet `user@tenant.onmicrosoft.com` .
- Detta **DisplayName** -anspråk krävs och kan inte vara en tom sträng.

## <a name="azure-ad-technical-provider-operations"></a>Azure AD Technical Provider-åtgärder

### <a name="read"></a>Läs

**Läs** åtgärden läser data om ett enda användar konto. Följande tekniska profil läser data om ett användar konto med hjälp av användarens objectId:

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Skriva

**Skriv** åtgärden skapar eller uppdaterar ett enskilt användar konto. Följande tekniska profil skapar ett nytt socialt konto:

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

**DeleteClaims** -åtgärden rensar informationen från en angiven lista över anspråk. Följande tekniska profil tar bort anspråk:

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Åtgärden **DeleteClaimsPrincipal** tar bort ett enskilt användar konto från katalogen. Följande tekniska profil tar bort ett användar konto från katalogen med hjälp av User Principal Name:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Följande tekniska profil tar bort ett socialt användar konto med **alternativeSecurityId**:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Åtgärden som ska utföras. Möjliga värden: `Read` , `Write` , `DeleteClaims` eller `DeleteClaimsPrincipal` . |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Inga | Generera ett fel om objektet användare inte finns i katalogen. Möjliga värden: `true` eller `false` . |
| RaiseErrorIfClaimsPrincipalAlreadyExists | Inga | Generera ett fel om det redan finns ett användar objekt. Möjliga värden: `true` eller `false` .|
| ApplicationObjectId | Inga | Programobjekts-ID för tilläggets attribut. Värde: ObjectId för ett program. Mer information finns i [använda anpassade attribut](user-flow-custom-attributes.md?pivots=b2c-custom-policy). |
| ClientId | Inga | Klient identifieraren för åtkomst till klienten som en tredje part. Mer information finns i [använda anpassade attribut i en anpassad profil redigerings princip](user-flow-custom-attributes.md?pivots=b2c-custom-policy) |
| IncludeClaimResolvingInClaimsHandling  | Inga | För indata-och utgående anspråk anges om [anspråks matchning](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` , eller `false` (standard). Om du vill använda en anspråks lösare i den tekniska profilen ställer du in den på `true` . |

### <a name="ui-elements"></a>Element för användargränssnitt
 
Följande inställningar kan användas för att konfigurera fel meddelandet som visas vid fel. Metadata bör konfigureras i den [självkontrollerade](self-asserted-technical-profile.md) tekniska profilen. Fel meddelandena kan [lokaliseras](localization.md).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | Inga | Om ett fel ska höjas (se RaiseErrorIfClaimsPrincipalAlreadyExists-Attribute Description) anger du det meddelande som ska visas för användaren om användar objekt redan finns. |
| UserMessageIfClaimsPrincipalDoesNotExist | Inga | Om ett fel ska höjas (se beskrivningen av RaiseErrorIfClaimsPrincipalDoesNotExist) anger du det meddelande som ska visas för användaren om användar objekt inte finns. |


## <a name="next-steps"></a>Nästa steg

Se följande artikel, till exempel om du använder teknisk profil för Azure AD:

- [Lägg till anspråk och anpassa användarindata med anpassade principer i Azure Active Directory B2C](configure-user-input.md)














