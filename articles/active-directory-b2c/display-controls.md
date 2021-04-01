---
title: Visa kontroll referens
titleSuffix: Azure AD B2C
description: Referens för Azure AD B2C Visa kontroller. Använd visnings kontroller för att anpassa användar resan som definierats i dina anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 441a77823c77305e567e9e1436715bc51ca48c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97387062"
---
# <a name="display-controls"></a>Visa kontroller

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En **visnings kontroll** är ett användar gränssnitts element som har särskilda funktioner och som samverkar med backend-tjänsten Azure Active Directory B2C (Azure AD B2C). Det gör att användaren kan utföra åtgärder på sidan som anropar en [teknisk profil för verifiering](validation-technical-profile.md) på Server sidan. Visa kontroller visas på sidan och refereras till av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md).

Följande bild illustrerar en självkontrollerad registrerings sida med två visnings kontroller som verifierar en primär och sekundär e-postadress.

![Exempel på renderad visnings kontroll](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>Förutsättningar

 I avsnittet [metadata](self-asserted-technical-profile.md#metadata) i en [självkontrollerad teknisk profil](self-asserted-technical-profile.md)måste den refererade [ContentDefinition](contentdefinitions.md) ha `DataUri` ställts in på sid kontrakt version 2.0.0 eller högre. Exempel:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definiera visnings kontroller

Elementet **Visa** poster innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för visnings kontrollen. Den kan [refereras till](#referencing-display-controls). |
| UserInterfaceControlType | Ja | Typen av visnings kontroll. Stöds för närvarande [VerificationControl](display-control-verification.md) |

Elementet Visa poster innehåller **följande element:**

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** används för att förkonfigurera värdet för de anspråk som ska samlas in från användaren. Mer information finns i [InputClaims](technicalprofiles.md#input-claims) -element. |
| DisplayClaims | 0:1 | **DisplayClaims** används för att representera anspråk som ska samlas in från användaren. Mer information finns i [DisplayClaim](technicalprofiles.md#displayclaim) -element.|
| OutputClaims | 0:1 | **OutputClaims** används för att representera anspråk som ska sparas temporärt för **den här** visaren. Mer information finns i [OutputClaims](technicalprofiles.md#output-claims) -element.|
| Åtgärder | 0:1 | **Åtgärder** används för att visa en lista med de tekniska profilerna som ska anropas för användar åtgärder på klient sidan. |

### <a name="input-claims"></a>Inmatade anspråk

I en visnings kontroll kan du använda **InputClaims** -element för att förkonfigurera värdet för anspråk som ska samlas in från användaren på sidan. Alla **InputClaimsTransformations** kan definieras i den självkontrollerade tekniska profilen som refererar till den här visnings kontrollen.

I följande exempel förfylls e-postadressen som ska verifieras med adressen som redan finns.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Visa anspråk

Varje typ av visnings kontroll kräver en annan uppsättning visnings anspråk, [utgående anspråk](#output-claims)och [åtgärder](#display-control-actions) som ska utföras.

På liknande sätt som de **visnings anspråk** som definierats i en [självkontrollerad teknisk profil](self-asserted-technical-profile.md#display-claims)representerar visnings anspråken de anspråk som ska samlas in från användaren i visnings kontrollen. **ClaimType** -elementet som refereras måste ange **UserInputType** -elementet för en indatatyp som stöds av Azure AD B2C, till exempel `TextBox` eller `DropdownSingleSelect` . Om ett visnings anspråks värde krävs av en **åtgärd** anger du det attribut som **krävs** för `true` att tvinga användaren att ange ett värde för det angivna visnings kravet.

Vissa visnings anspråk krävs för vissa typer av visnings kontroll. Till exempel krävs **VerificationCode** för visnings kontrollen av typen **VerificationControl**. Använd attributet **ControlClaimType** för att ange vilken DisplayClaim som är avsedd för det begärda anspråket. Exempel:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Utgående anspråk

De **utgående anspråken** för en visnings kontroll skickas inte till nästa Orchestration-steg. De sparas tillfälligt för den aktuella sessionen för bildskärms kontroll. Dessa tillfälliga anspråk kan delas mellan olika åtgärder för samma visnings kontroll.

Om du vill bubbla ut anspråk till nästa Orchestration-steg använder du **OutputClaims** för den faktiska självkontrollerade tekniska profilen som refererar till den här visnings kontrollen.

### <a name="display-control-actions"></a>Visa kontroll åtgärder

**Åtgärder** för en visnings kontroll är procedurer som inträffar i Azure AD B2C Server del när en användare utför en viss åtgärd på klient sidan (webbläsaren). Det kan till exempel vara verifieringar som ska utföras när användaren väljer en knapp på sidan.

En åtgärd definierar en lista över **tekniska verifierings profiler**. De används för att verifiera vissa eller alla visnings anspråk för visnings kontrollen. Den tekniska verifierings profilen verifierar indata från användaren och kan returnera ett fel till användaren. Du kan använda **ContinueOnError**, **ContinueOnSuccess** och **villkor** i visnings kontroll åtgärden som liknar det sätt som de används för att [validera tekniska profiler](validation-technical-profile.md) i en egen kontrollerad teknisk profil.

#### <a name="actions"></a>Åtgärder

Elementet **Actions** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Åtgärd | 1: n | Lista med åtgärder som ska utföras. |

#### <a name="action"></a>Action

**Åtgärds** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | Typ av åtgärd. Möjliga värden: `SendCode` eller `VerifyCode` . `SendCode`Värdet skickar en kod till användaren. Den här åtgärden kan innehålla två verifierade tekniska profiler: en för att generera en kod och en för att skicka den. `VerifyCode`Värdet verifierar den kod som användaren skrev i text rutan för indata. |

**Åtgärds** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | Identifierare för tekniska profiler som används för att validera vissa eller alla visnings anspråk för den refererande tekniska profilen. Alla ingående anspråk för den refererade tekniska profilen måste visas i visnings anspråk för den refererande tekniska profilen. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

**ValidationClaimsExchange** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | En teknisk profil som används för att verifiera vissa eller alla visnings anspråk för den refererande tekniska profilen. |

**ValidationTechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i principen eller överordnad princip. |
|ContinueOnError|Inga| Anger om validering av eventuella efterföljande verifierings tekniska profiler ska fortsätta om den här verifierings tekniska profilen genererar ett fel. Möjliga värden: `true` eller `false` (standard, bearbetning av ytterligare verifierings profiler kommer att stoppas och ett fel returneras). |
|ContinueOnSuccess | Inga | Anger om validering av efterföljande validerings profiler ska fortsätta om den här verifieringen av teknisk profil lyckas. Möjliga värden: `true` eller `false` . Standardvärdet är `true` , vilket innebär att bearbetningen av ytterligare verifierings profiler fortsätter. |

**ValidationTechnicalProfile** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Villkor | 0:1 | En lista med förutsättningar som måste uppfyllas för att den tekniska profilen för validering ska kunna köras. |

**Villkors** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| `Type` | Ja | Typ av kontroll eller fråga som ska utföras för villkoret. Möjliga värden: `ClaimsExist` eller `ClaimEquals` . `ClaimsExist` anger att åtgärderna ska utföras om de angivna anspråken finns i användarens aktuella anspråks uppsättning. `ClaimEquals` anger att åtgärderna ska utföras om det angivna anspråket finns och dess värde är lika med det angivna värdet. |
| `ExecuteActionsIf` | Ja | Anger om åtgärderna i villkoret ska utföras om testet är sant eller falskt. |

**Villkors** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Värde | 1: n | De data som används av kontrollen. Om typen av kontroll är `ClaimsExist` , anger det här fältet en ClaimTypeReferenceId att fråga efter. Om typen av kontroll är `ClaimEquals` , anger det här fältet en ClaimTypeReferenceId att fråga efter. Ange det värde som ska kontrol leras i ett annat värde element.|
| Action | 1:1 | Den åtgärd som ska vidtas om villkors kontrollen i ett Orchestration-steg är sann. Värdet för **åtgärden** är inställt på `SkipThisValidationTechnicalProfile` , vilket anger att den tillhör ande tekniska profilen för verifiering inte ska köras. |

I följande exempel skickas och verifieras e-postadressen med hjälp av [Azure AD SSPR Technical Profile](aad-sspr-technical-profile.md).

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

I följande exempel skickas en kod antingen i e-post eller SMS baserat på användarens val av **mfaType** -anspråk med villkor.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Referera till visnings kontroller

Visa kontroller refereras till i [Visa anspråk](self-asserted-technical-profile.md#display-claims) för den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md).

Exempel:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Nästa steg

Exempel på hur du använder visnings kontroll finns i: 

- [Anpassad e-postverifiering med MailJet](custom-email-mailjet.md)
- [Anpassad e-postverifiering med SendGrid](custom-email-sendgrid.md)
