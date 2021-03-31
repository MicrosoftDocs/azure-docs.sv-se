---
title: Definiera en teknisk profil för anspråks omvandling
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för anspråks omvandling i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6553b9ec120ca0e1e479b400495b61bc68c88cf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85201216"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för anspråks omvandling i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Med en teknisk profil för anspråk omvandling kan du anropa omvandlingar av utgående anspråk för att manipulera anspråks värden, validera anspråk eller ange standardvärden för en uppsättning utgående anspråk.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary` . Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterings sammansättning som används av Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

I följande exempel visas en teknisk profil för anspråks omvandling:

```xml
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet är obligatoriskt. Du bör ange minst ett utgående anspråk som returneras av den tekniska profilen. I följande exempel visas hur du ställer in standardvärden i utgående anspråk:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Transformeringar av utgående anspråk

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att modifiera anspråk eller skapa nya. Följande tekniska profil anropar omvandlingen av **RemoveAlternativeSecurityIdByIdentityProvider** -anspråk. Den här anspråks omvandlingen tar bort en social identifiering från samlingen av **AlternativeSecurityIds**. De utgående anspråken för den här tekniska profilen är **identityProvider2**, som är inställd på `facebook.com` och **AlternativeSecurityIds**, som innehåller listan över sociala identiteter som är associerade med användaren efter att Facebook.com-identiteten har tagits bort.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Den tekniska profilen för anspråks omvandling gör att du kan köra en anspråks omvandling från varje användar resas Dirigerings steg. I följande exempel anropar Orchestration-steget ett av de tekniska profilerna för att ta bort länkar, till exempel **Unlink-Facebook-OAuth**. Den här tekniska profilen anropar **RemoveAlternativeSecurityIdByIdentityProvider** för anspråk omvandling, som genererar ett nytt **AlternativeSecurityIds2** -anspråk som innehåller en lista över sociala identiteter för användare, samtidigt som Facebook-identiteten tas bort från samlingarna.

```xml
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| IncludeClaimResolvingInClaimsHandling  | Inga | För indata-och utgående anspråk anges om [anspråks matchning](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` , eller `false` (standard). Om du vill använda en anspråks lösare i den tekniska profilen ställer du in den på `true` . |

## <a name="use-a-validation-technical-profile"></a>Använd en teknisk verifierings profil

En teknisk profil för anspråks omvandling kan användas för att validera information. I följande exempel ber den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md) med namnet **LocalAccountSignUpWithLogonEmail** användaren att ange e-postmeddelandet två gånger och anropar sedan den [tekniska profilen](validation-technical-profile.md) med namnet **validate-email** för att verifiera e-postmeddelandena. Den tekniska profilen för **validerings-e-post** anropar **AssertEmailAreEqual** för anspråk och jämför de två **e-postmeddelandena** och **emailRepeat** och genererar ett undantag om de inte är lika enligt den angivna jämförelsen.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Den tekniska profilen för anspråks omvandling anropar omvandlingen av **AssertEmailAreEqual** -anspråk, som förutsätter att e-postmeddelanden från användaren är desamma.

```xml
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

En självkontrollerad teknisk profil kan anropa den tekniska verifierings profilen och Visa fel meddelandet som anges i **UserMessageIfClaimsTransformationStringsAreNotEqual** metadata.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
