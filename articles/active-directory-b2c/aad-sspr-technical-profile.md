---
title: Tekniska profiler för Azure AD SSPR i anpassade principer
titleSuffix: Azure AD B2C
description: Anpassad princip referens för Azure AD SSPR-tekniska profiler i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85383605"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiera en Azure AD SSPR-teknisk profil i en Azure AD B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för att verifiera en e-postadress för självbetjäning för återställning av lösen ord (SSPR). Använd Azure AD SSPR Technical Profile för att skapa och skicka en kod till en e-postadress och verifiera sedan koden. Den tekniska profilen för Azure AD SSPR kan också returnera ett fel meddelande. Den tekniska verifierings profilen verifierar de data som anges av användaren innan användar resan fortsätter. Med den tekniska profilen för validering visas ett fel meddelande på en egen kontrollerad sida.

Den här tekniska profilen:

- Tillhandahåller inte ett gränssnitt för att interagera med användaren. I stället anropas användar gränssnittet från en [självkontrollerad](self-asserted-technical-profile.md) teknisk profil, eller en [visnings kontroll](display-controls.md) som en [teknisk validerings profil](validation-technical-profile.md).
- Använder Azure AD SSPR-tjänsten för att skapa och skicka en kod till en e-postadress och sedan verifiera koden.  
- Verifierar en e-postadress via en verifierings kod.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary` . Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterings sammansättning som används av Azure AD B2C:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

I följande exempel visas en teknisk profil för Azure AD SSPR:

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>Skicka e-postmeddelande

Det första läget i den här tekniska profilen är att generera en kod och skicka den. Följande alternativ kan konfigureras för det här läget.

### <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk att skicka till Azure AD-SSPR. Du kan också mappa namnet på anspråket till namnet som definierats i den tekniska SSPR-profilen.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| emailAddress | Ja | ID för den användare som äger e-postadressen. `PartnerClaimType`Egenskapen för det angivna anspråket måste vara inställd på `emailAddress` . |


**InputClaimsTransformations** -elementet kan innehålla en samling **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan de skickas till Azure AD SSPR-tjänsten.

### <a name="output-claims"></a>Utgående anspråk

Azure AD SSPR-providern returnerar inte några **OutputClaims**, därför behöver du inte ange några utgående anspråk. Du kan dock ta med anspråk som inte returneras av Azure AD SSPR-protokollstacken så länge du ställer in `DefaultValue` attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Måste vara **SendCode**.  |

#### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera fel meddelanden som visas vid sändning av SMS-fel. Metadata bör konfigureras i den [självkontrollerade](self-asserted-technical-profile.md) tekniska profilen. Fel meddelandena kan [lokaliseras](localization-string-ids.md#azure-ad-sspr).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | Inga | Användar fel meddelande om ett internt fel inträffar på servern. |
| UserMessageIfThrottled| Inga | Användar fel meddelande om en begäran har begränsats.|


### <a name="example-send-an-email"></a>Exempel: Skicka ett e-postmeddelande

I följande exempel visas en Azure AD SSPR-teknisk profil som används för att skicka en kod via e-post.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verifiera koden

Det andra läget i den här tekniska profilen är att verifiera en kod. Följande alternativ kan konfigureras för det här läget.

### <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk att skicka till Azure AD-SSPR. Du kan också mappa namnet på anspråket till namnet som definierats i den tekniska SSPR-profilen.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Ja | Samma e-postadress som tidigare användes för att skicka en kod. Den används också för att hitta en e-postverifierings session. `PartnerClaimType`Egenskapen för det angivna anspråket måste vara inställd på `emailAddress` .|
| verificationCode  | Ja | Verifierings koden som anges av användaren som ska verifieras. `PartnerClaimType`Egenskapen för det angivna anspråket måste vara inställd på `verificationCode` . |

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan du anropar Azure AD SSPR-tjänsten.

### <a name="output-claims"></a>Utgående anspråk

Azure AD SSPR-providern returnerar inte några **OutputClaims**, därför behöver du inte ange några utgående anspråk. Du kan dock ta med anspråk som inte returneras av Azure AD SSPR-protokollstacken så länge du ställer in `DefaultValue` attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Måste vara **VerifyCode** |

#### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera fel meddelanden som visas vid kod verifierings fel. Metadata bör konfigureras i den [självkontrollerade](self-asserted-technical-profile.md) tekniska profilen. Fel meddelandena kan [lokaliseras](localization-string-ids.md#azure-ad-sspr).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | Meddelandet som ska visas för användaren om kod verifierings sessionen har upphört att gälla. Antingen har koden upphört att gälla eller också har koden aldrig skapats för en specifik identifierare.|
|UserMessageIfInternalError | Användar fel meddelande om ett internt fel inträffar på servern.|
|UserMessageIfThrottled | Användar fel meddelande om en begäran har begränsats.|
|UserMessageIfVerificationFailedNoRetry | Meddelandet som ska visas för användaren om de har angett en ogiltig kod och användaren inte får ange rätt kod.|
|UserMessageIfVerificationFailedRetryAllowed | Det meddelande som ska visas för användaren om de har angett en ogiltig kod och användaren får ange rätt kod.|

### <a name="example-verify-a-code"></a>Exempel: verifiera en kod

I följande exempel visas en Azure AD SSPR-teknisk profil som används för att verifiera koden.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```