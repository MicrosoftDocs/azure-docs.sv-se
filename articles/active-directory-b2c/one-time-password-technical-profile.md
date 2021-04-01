---
title: Aktivera verifiering av eng ång slö sen ord
titleSuffix: Azure AD B2C
description: Lär dig hur du konfigurerar ett eng ång slö sen ord (eng ång slö sen ord) genom att använda Azure AD B2C anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 12b9639342e2e35b9229aa15bb9cfb4695427606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881199"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiera en teknisk profil för eng ång slö sen ord i en Azure AD B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för att hantera genereringen och verifieringen av ett eng ång slö sen ord. Använd en teknisk profil för att generera en kod och kontrol lera sedan koden senare.

Den tekniska profilen för eng ång slö sen ord kan också returnera ett fel meddelande under kod verifieringen. Utforma integrationen med eng ång slö sen ord genom att använda en **verifierad teknisk profil**. En teknisk validerings profil anropar en teknisk profil för eng ång slö sen ord för att verifiera en kod. Den tekniska verifierings profilen verifierar de data som anges av användaren innan användar resan fortsätter. Med den tekniska verifierings profilen visas ett fel meddelande på en självkontrollerad sida.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary` . Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterings sammansättning som används av Azure AD B2C:

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

I följande exempel visas en teknisk profil för eng ång slö sen ord:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generera kod

Det första läget i den här tekniska profilen är att generera en kod. Nedan visas de alternativ som kan konfigureras för det här läget. Genererade koder och försök spåras i sessionen. 

### <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk som krävs för att skicka till eng ång slö sen ord. Du kan också mappa namnet på ditt anspråk till namnet som anges nedan.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| beteckning | Ja | Identifieraren som identifierar den användare som behöver verifiera koden senare. Den används ofta som identifierare för det mål där koden levereras, till exempel e-postadress eller telefonnummer. |

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan de skickas till eng ång slö sen lösen ords protokollets Provider.

### <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som skapats av eng ång slö sen ord. Du kan också mappa namnet på ditt anspråk till namnet som anges nedan.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| otpGenerated | Ja | Den genererade koden vars session hanteras av Azure AD B2C. |

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="metadata"></a>Metadata

Följande inställningar kan användas för att konfigurera läget för kodgenerering:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Inga | Tid i sekunder fram till att koden upphör att gälla. Minimum: `60` ; Max: `1200` ; Standard: `600` . Varje gång en kod anges (samma kod med `ReuseSameCode` eller en ny kod) utökas kodens förfallo datum. Den här tiden används också för att ange tids gräns för återförsök (när maximalt antal försök har uppnåtts, är användaren utelåst från att försöka hämta nya koder tills den här tiden upphör att gälla) |
| CodeLength | Inga | Längden på koden. Standardvärdet är `6`. |
| CharacterSet | Inga | Teckenuppsättningen för koden, formaterad för användning i ett reguljärt uttryck. Till exempel `a-z0-9A-Z`. Standardvärdet är `0-9`. Tecken uppsättningen måste innehålla minst 10 olika tecken i den angivna uppsättningen. |
| NumRetryAttempts | Inga | Antalet verifierings försök innan koden betraktas som ogiltig. Standardvärdet är `5`. |
| NumCodeGenerationAttempts | Inga | Antalet tillåtna försök per identifierare för högsta antal kodgenerering. Standardvärdet är 10 om inget värde anges. |
| Åtgärd | Ja | Åtgärden som ska utföras. Möjligt värde: `GenerateCode` . |
| ReuseSameCode | Inga | Om samma kod ska anges i stället för att generera en ny kod när den aktuella koden inte har upphört att gälla och fortfarande är giltig. Standardvärdet är `false`.  |



### <a name="example"></a>Exempel

Följande exempel `TechnicalProfile` används för att generera en kod:

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verifiera koden

Det andra läget i den här tekniska profilen är att verifiera en kod. Nedan visas de alternativ som kan konfigureras för det här läget.

### <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk som krävs för att skicka till eng ång slö sen ord. Du kan också mappa namnet på ditt anspråk till namnet som anges nedan.

| ClaimReferenceId | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| beteckning | Ja | Identifieraren som identifierar den användare som tidigare har genererat en kod. Den används ofta som identifierare för det mål där koden levereras, till exempel e-postadress eller telefonnummer. |
| otpToVerify | Ja | Verifierings koden som anges av användaren. |

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan de skickas till eng ång slö sen lösen ords protokollets Provider.

### <a name="output-claims"></a>Utgående anspråk

Det finns inga utgående anspråk under kod verifieringen av den här protokoll leverantören.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

### <a name="metadata"></a>Metadata

Följande inställningar kan användas för kod verifierings läge:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Åtgärd | Ja | Åtgärden som ska utföras. Möjligt värde: `VerifyCode` . |


### <a name="ui-elements"></a>Element för användargränssnitt

Följande metadata kan användas för att konfigurera fel meddelanden som visas vid kod verifierings fel. Metadata bör konfigureras i den [självkontrollerade](self-asserted-technical-profile.md) tekniska profilen. Fel meddelandena kan [lokaliseras](localization-string-ids.md#one-time-password-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Inga | Meddelandet som ska visas för användaren om kod verifierings sessionen har upphört att gälla. Antingen har koden upphört att gälla eller också har koden aldrig skapats för en specifik identifierare. |
| UserMessageIfMaxRetryAttempted | Inga | Meddelandet som ska visas för användaren om de har överskridit det högsta antalet tillåtna verifierings försök. |
| UserMessageIfMaxNumberOfCodeGenerated | Inga | Meddelandet som ska visas för användaren om kodgenerering har överskridit det högsta tillåtna antalet försök. |
| UserMessageIfInvalidCode | Inga | Meddelandet som ska visas för användaren om de har angett en ogiltig kod. |
| UserMessageIfVerificationFailedRetryAllowed | Inga | Meddelandet som ska visas för användaren om de har angett en ogiltig kod, och användaren får ange rätt kod.  |
|UserMessageIfSessionConflict|Inga| Meddelandet som ska visas för användaren om det inte går att verifiera koden.|

### <a name="example"></a>Exempel

Följande exempel `TechnicalProfile` används för att verifiera en kod:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Nästa steg

I följande artikel visas exempel på en teknisk profil med eng ång slö sen ord med anpassad e-postverifiering:

- Anpassad e-postverifiering i Azure Active Directory B2C ([MailJet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md))

