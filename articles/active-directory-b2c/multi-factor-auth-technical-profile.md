---
title: Tekniska profiler för Azure AD MFA i anpassade principer
titleSuffix: Azure AD B2C
description: Anpassad princip referens för Azure AD Multi-Factor Authentication (MFA) tekniska profiler i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e81ac35555e6653cecb602e5af2f19aa3e2f05e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840601"
---
# <a name="define-an-azure-ad-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiera en Azure AD MFA Technical-profil i en Azure AD B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för att verifiera ett telefonnummer med hjälp av Azure AD Multi-Factor Authentication (MFA). Använd den här tekniska profilen för att generera och skicka en kod till ett telefonnummer, och sedan verifiera koden. Azure AD MFA Technical-profilen kan också returnera ett fel meddelande.  Den tekniska verifierings profilen verifierar de data som anges av användaren innan användar resan fortsätter. Med den tekniska profilen för validering visas ett fel meddelande på en egen kontrollerad sida.

Den här tekniska profilen:

- Tillhandahåller inte ett gränssnitt för att interagera med användaren. I stället anropas användar gränssnittet från en [självkontrollerad](self-asserted-technical-profile.md) teknisk profil, eller en [visnings kontroll](display-controls.md) som en [teknisk validerings profil](validation-technical-profile.md).
- Använder Azure AD MFA-tjänsten för att skapa och skicka en kod till ett telefonnummer och sedan verifiera koden.  
- Verifierar ett telefonnummer via textmeddelanden.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary` . Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterings sammansättning som används av Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

I följande exempel visas en teknisk profil för Azure AD MFA:

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Skicka SMS

Det första läget i den här tekniska profilen är att generera en kod och skicka den. Följande alternativ kan konfigureras för det här läget.

### <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk att skicka till Azure AD MFA. Du kan också mappa namnet på ditt anspråk till namnet som definierats i den tekniska profilen för MFA.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| userPrincipalName | Ja | Identifieraren för den användare som äger telefonnumret. |
| phoneNumber | Ja | Telefonnumret som en SMS-kod ska skickas till. |
| companyName | Inga |Företags namnet i SMS. Om inget namn anges används namnet på programmet. |
| locale | Inga | Språk inställningen för SMS. Om den inte anges används webbläsarens nationella inställningar. |

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan de skickas till Azure AD MFA-tjänsten.

### <a name="output-claims"></a>Utgående anspråk

Azure AD MFA-providern returnerar inga **OutputClaims**, därför behöver du inte ange några utgående anspråk. Du kan dock ta med anspråk som inte returneras av Azure AD MFA Identity-providern så länge du ställer in `DefaultValue` attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Måste vara **OneWaySMS**.  |

#### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera fel meddelanden som visas vid sändning av SMS-fel. Metadata bör konfigureras i den [självkontrollerade](self-asserted-technical-profile.md) tekniska profilen. Fel meddelandena kan [lokaliseras](localization-string-ids.md#azure-ad-mfa-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | Inga | Användar fel meddelande om det angivna telefonnumret inte accepterar SMS. |
| UserMessageIfInvalidFormat | Inga | Användar fel meddelande om det angivna telefonnumret inte är ett giltigt telefonnummer. |
| UserMessageIfServerError | Inga | Användar fel meddelande om ett internt fel inträffar på servern. |
| UserMessageIfThrottled| Inga | Användar fel meddelande om en begäran har begränsats.|

### <a name="example-send-an-sms"></a>Exempel: Skicka ett SMS

I följande exempel visas en Azure AD MFA-teknisk profil som används för att skicka en kod via SMS.

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verifiera koden

Det andra läget i den här tekniska profilen är att verifiera en kod. Följande alternativ kan konfigureras för det här läget.

### <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk att skicka till Azure AD MFA. Du kan också mappa namnet på ditt anspråk till namnet som definierats i den tekniska profilen för MFA.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Ja | Samma telefonnummer som tidigare användes för att skicka en kod. Den används också för att hitta en fjärrstyrningssession. |
| verificationCode  | Ja | Verifierings koden som anges av användaren som ska verifieras |

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan du anropar tjänsten Azure AD MFA.

### <a name="output-claims"></a>Utgående anspråk

Azure AD MFA-providern returnerar inga **OutputClaims**, därför behöver du inte ange några utgående anspråk. Du kan dock ta med anspråk som inte returneras av Azure AD MFA Identity-providern så länge du ställer in `DefaultValue` attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Måste **Verifiera** |

#### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera fel meddelanden som visas vid kod verifierings fel. Metadata bör konfigureras i den [självkontrollerade](self-asserted-technical-profile.md) tekniska profilen. Fel meddelandena kan [lokaliseras](localization-string-ids.md#azure-ad-mfa-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| Inga | Användar fel meddelande om användaren försökte utföra en verifierings kod för många gånger. |
| UserMessageIfServerError | Inga | Användar fel meddelande om ett internt fel inträffar på servern. |
| UserMessageIfThrottled| Inga | Användar fel meddelande om begäran är begränsad.|
| UserMessageIfWrongCodeEntered| Inga| Användar fel meddelande om den kod som angetts för verifiering är felaktig.|

### <a name="example-verify-a-code"></a>Exempel: verifiera en kod

I följande exempel visas en Azure AD MFA Technical-profil som används för att verifiera koden.

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
