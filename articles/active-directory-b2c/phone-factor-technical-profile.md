---
title: Definiera en telefon faktors tekniska profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en telefon faktors tekniska profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950405"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för telefon faktor i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för registrering och verifiering av telefonnummer. Den här tekniska profilen:

- Tillhandahåller ett användar gränssnitt för att interagera med användaren för att verifiera eller registrera ett telefonnummer.
- Stöder telefonsamtal och textmeddelanden för att verifiera telefonnumret.
- Har stöd för flera telefonnummer. Användaren kan välja ett av de telefonnummer som ska verifieras.  
- Returnerar ett anspråk som anger om användaren angav ett nytt telefonnummer. Du kan använda detta anspråk för att bestämma om telefonnumret ska bevaras till den Azure AD B2C användar profilen.  
- Använder en [innehålls definition](contentdefinitions.md) för att kontrol lera utseendet och känslan.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary` . Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterings sammansättning som används av Azure AD B2C för telefon faktor: `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

I följande exempel visas en teknisk profil för telefon faktorer för registrering och validering:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Transformeringar av inmatade anspråk

InputClaimsTransformations-elementet kan innehålla en samling med inloggade anspråks omvandlingar som används för att ändra inaktuella anspråk eller skapa nya. Följande omvandling av inloggade anspråk genererar ett `UserId` anspråk som används senare i insamlingen av inloggade anspråk.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Inmatade anspråk

InputClaims-elementet måste innehålla följande anspråk. Du kan också mappa namnet på ditt anspråk till namnet som definierats i den tekniska profilen för telefon faktorn. 

|  Datatyp| Krävs | Beskrivning |
| --------- | -------- | ----------- | 
| sträng| Ja | En unik identifierare för användaren. Anspråks namnet eller PartnerClaimType måste anges till `UserId` . Detta anspråk bör inte innehålla personlig identifierbar information.|
| sträng| Ja | Lista över anspråks typer. Varje anspråk innehåller ett telefonnummer. Om något av de angivna anspråken inte innehåller ett telefonnummer, uppmanas användaren att registrera sig och verifiera ett nytt telefonnummer. Det verifierade telefonnumret returneras som ett utgående anspråk. Om något av de angivna anspråken innehåller ett telefonnummer, uppmanas användaren att verifiera det. Om flera indata-anspråk innehåller ett telefonnummer, uppmanas användaren att välja och verifiera ett av telefonnumren. |

I följande exempel visas hur du använder flera telefonnummer. Mer information finns i [exempel princip](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Utgående anspråk

OutputClaims-elementet innehåller en lista över anspråk som returneras av den tekniska profilen för telefon faktorn.

|  Datatyp| Krävs | Beskrivning |
|  -------- | ----------- |----------- |
| boolean | Ja | Anger om det nya telefonnumret har angetts av användaren. Anspråks namnet eller PartnerClaimType måste anges till `newPhoneNumberEntered`|
| sträng| Ja | Det verifierade telefonnumret. Anspråks namnet eller PartnerClaimType måste anges till `Verified.OfficePhone` .|

OutputClaimsTransformations-elementet kan innehålla en samling av OutputClaimsTransformation-element som används för att ändra de utgående anspråken eller skapa nya.

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

**CryptographicKeys** -elementet används inte.


## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Ja | Identifieraren för den [innehålls definition](contentdefinitions.md) som är associerad med den här tekniska profilen. |
| ManualPhoneNumberEntryAllowed| Inga | Ange om en användare får ange ett telefonnummer manuellt. Möjliga värden: `true` , eller `false` (standard).|
| anger. authenticationMode | Inga | Metoden för att verifiera telefonnumret. Möjliga värden: `sms` , `phone` eller `mixed` (standard).|
| anger. AutoDial| Inga| Ange om den tekniska profilen ska Ring upp automatiskt eller automatiskt skicka ett SMS. Möjliga värden: `true` , eller `false` (standard). Automatisk uppringning kräver att `setting.authenticationMode` metadata anges till `sms` eller `phone` . Insamlings anspråket måste ha ett enda telefonnummer. |

### <a name="ui-elements"></a>Element för användargränssnitt

Användar gränssnitts elementen på sidan med telefon faktorns autentisering kan [lokaliseras](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements).

## <a name="next-steps"></a>Nästa steg

- Kontrol lera [sociala och lokala konton med MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) start paket.
