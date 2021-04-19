---
title: Tekniska profiler för villkorsstyrd åtkomst i anpassade principer
titleSuffix: Azure AD B2C
description: Anpassad principreferens för tekniska profiler för villkorsstyrd åtkomst i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebdc1c9c92f6e3debf08cb640e46424c4ad9d5ad
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107721896"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för villkorsstyrd åtkomst i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) villkorsstyrd åtkomst är det verktyg som används av Azure AD B2C för att samla signaler, fatta beslut och genomdriva organisationsprinciper. Automatisering av riskbedömning med principvillkor innebär att riskfyllda inloggningar samtidigt identifieras och åtgärdas eller blockeras.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokoll

Attributet **Name** för **elementet Protocol** måste anges till `Proprietary` . **Hanterarattributet** måste innehålla det fullständigt kvalificerade namnet på protokollhanterarens sammansättning som används av Azure AD B2C:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

I följande exempel visas en teknisk profil för villkorsstyrd åtkomst:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Utvärdering av villkorsstyrd åtkomst

För varje inloggning utvärderar Azure AD B2C alla principer och säkerställer att alla krav uppfylls innan användaråtkomst beviljas. "Blockera åtkomst" åsidosätter alla andra konfigurationsinställningar. **Utvärderingsläget** för den tekniska profilen för villkorsstyrd åtkomst utvärderar de signaler som samlas in Azure AD B2C under inloggningen med ett lokalt konto. Resultatet av den tekniska profilen för villkorsstyrd åtkomst är en uppsättning anspråk som är resultatet av utvärderingen av villkorlig åtkomst. Principen Azure AD B2C använder dessa anspråk i nästa orkestreringssteg för att vidta en åtgärd, till exempel blockera användaren eller uppmana användaren med multifaktorautentisering. Följande alternativ kan konfigureras för det här läget.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärdstyp | Yes | Måste vara Evaluation ( **Utvärdering).**  |

### <a name="input-claims"></a>Inkommande anspråk

Elementet **InputClaims** innehåller en lista över anspråk som ska skickas till villkorlig åtkomst. Du kan också mappa namnet på ditt anspråk till det namn som definierats i den tekniska profilen för villkorsstyrd åtkomst.

| ClaimReferenceId | Obligatorisk | Datatyp | Beskrivning |
| --------- | -------- | ----------- |----------- |
| UserId | Yes | sträng | Identifieraren för den användare som loggar in. |
| AuthenticationMethodsUsed | Yes |stringCollection | Listan över metoder som användaren använde för att logga in. Möjliga värden: `Password` och `OneTimePasscode` . |
| IsFederated | Yes |boolean | Anger om en användare har loggat in med ett federerat konto eller inte. Värdet måste vara `false` . |
| IsMfaRegistered | Yes |boolean | Anger om användaren redan har registrerat ett telefonnummer för multifaktorautentisering. |


**Elementet InputClaimsTransformations** kan innehålla en samling **InputClaimsTransformation-element** som används för att ändra inkommande anspråk eller generera nya innan de skickas till tjänsten för villkorlig åtkomst.

### <a name="output-claims"></a>Utgående anspråk

**OutputClaims-elementet** innehåller en lista över anspråk som genereras av ConditionalAccessProtocolProvider. Du kan också mappa namnet på anspråket till det namn som definieras nedan.

| ClaimReferenceId | Obligatorisk | Datatyp | Beskrivning |
| --------- | -------- | ----------- |----------- |
| Utmaningar | Yes |stringCollection | Lista över åtgärder för att åtgärda det identifierade hotet. Möjliga värden: `block` |
| MultiConditionalAccessStatus | Yes | stringCollection |  |

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utgående anspråk eller generera nya.

### <a name="example-evaluation"></a>Exempel: Utvärdering

I följande exempel visas en teknisk profil för villkorsstyrd åtkomst som används för att utvärdera inloggningshotet.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Åtgärder

Åtgärdsläget **för den** tekniska profilen för villkorsstyrd åtkomst informerar Azure AD B2C att det identifierade hotet har åtgärdats. Följande alternativ kan konfigureras för reparationsläget.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärdstyp | Yes | Måste **vara Reparation.**  |

### <a name="input-claims"></a>Inkommande anspråk

Elementet **InputClaims** innehåller en lista över anspråk som ska skickas till villkorlig åtkomst. Du kan också mappa namnet på ditt anspråk till det namn som definierats i den tekniska profilen för villkorsstyrd åtkomst.

| ClaimReferenceId | Obligatorisk | Datatyp | Beskrivning |
| --------- | -------- | ----------- |----------- |
| UtmaningarNöjd | Yes | stringCollection| Listan över uppfyllda utmaningar för att åtgärda det identifierade hotet som retur från utvärderingsläget, utmaningsanspråk.|


**Elementet InputClaimsTransformations** kan innehålla en samling **InputClaimsTransformation-element** som används för att ändra inkommande anspråk eller generera nya innan tjänsten för villkorlig åtkomst anropas.

### <a name="output-claims"></a>Utgående anspråk

Protokollprovidern för villkorsstyrd åtkomst returnerar inga **OutputClaims,** så du behöver inte ange utgående anspråk. Du kan dock inkludera anspråk som inte returneras av protokollprovidern för villkorsstyrd åtkomst så länge du anger `DefaultValue` attributet.

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utgående anspråk eller generera nya.

### <a name="example-remediation"></a>Exempel: Reparation

I följande exempel visas en teknisk profil för villkorsstyrd åtkomst som används för att åtgärda inloggningshotet.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [lägger till villkorlig åtkomst till användarflöden i Azure Active Directory B2C](conditional-access-user-flow.md).
