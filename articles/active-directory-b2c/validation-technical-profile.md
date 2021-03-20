---
title: Definiera en teknisk validerings profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Verifiera anspråk genom att använda en validerings teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2d4c538a9292698fecc8b44c055ab201748e292c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85203001"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk validerings profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En teknisk verifierings profil är en vanlig teknisk profil från alla protokoll, till exempel [Azure Active Directory](active-directory-technical-profile.md) eller en [REST API](restful-technical-profile.md). Den tekniska verifierings profilen returnerar utgående anspråk eller returnerar 4xx HTTP-statuskod med följande data. Mer information finns i [returnera fel meddelande](restful-technical-profile.md#returning-validation-error-message)

```json
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Omfattningen av de utgående anspråken för en verifierings teknisk profil är begränsad till den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md) som anropar den tekniska profilen för verifiering och dess verifierings tekniska profiler. Om du vill använda de utgående anspråken i nästa Orchestration-steg lägger du till de utgående anspråken till den självkontrollerade tekniska profilen som anropar den tekniska profilen för verifiering.

De tekniska profilerna för verifiering utförs i den ordning som de visas i **ValidationTechnicalProfiles** -elementet. Du kan konfigurera i en teknisk verifierings profil om körningen av eventuella efterföljande validerings tekniska profiler ska fortsätta om verifieringen av den tekniska profilen genererar ett fel eller lyckas.

En teknisk verifierings profil kan köras villkorligt baserat på de villkor som definierats i **ValidationTechnicalProfile** -elementet. Du kan till exempel kontrol lera om det finns ett specifikt anspråk eller om ett anspråk är lika med eller inte det angivna värdet.

En självkontrollerad teknisk profil kan definiera en validerings teknisk profil som ska användas för att verifiera vissa eller alla dess utgående anspråk. Alla indata-anspråk för den refererade tekniska profilen måste visas i utdata-anspråk för den refererande verifieringen av den tekniska profilen.

> [!NOTE]
> Endast självkontrollerade tekniska profiler kan använda verifiering av tekniska profiler. Om du behöver validera de utgående anspråken från icke-självkontrollerade tekniska profiler bör du överväga att använda ett ytterligare Orchestration-steg i din användar resa för att hantera den tekniska profilen som är ansvarig för verifieringen.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | En teknisk profil som används för att verifiera vissa eller alla utgående anspråk för den refererande tekniska profilen. |

**ValidationTechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i principen eller överordnad princip. |
|ContinueOnError|Inga| Anger om validering av eventuella efterföljande verifierings tekniska profiler ska fortsätta om den här verifierings tekniska profilen genererar ett fel. Möjliga värden: `true` eller `false` (standard, bearbetning av ytterligare verifierings profiler kommer att stoppas och ett fel returneras). |
|ContinueOnSuccess | Inga | Anger om validering av eventuella efterföljande validerings profiler ska fortsätta om den här verifieringen av teknisk profil lyckas. Möjliga värden: `true` eller `false` . Standardvärdet är `true` , vilket innebär att bearbetningen av ytterligare verifierings profiler fortsätter. |

**ValidationTechnicalProfile** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Villkor | 0:1 | En lista med förutsättningar som måste uppfyllas för att den tekniska profilen för validering ska kunna köras. |

**Villkors** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| `Type` | Ja | Typ av kontroll eller fråga som ska utföras för villkoret. Antingen `ClaimsExist` anges för att säkerställa att åtgärder ska utföras om de angivna anspråken finns i användarens aktuella anspråks uppsättning eller `ClaimEquals` anges att åtgärderna ska utföras om det angivna anspråket finns och dess värde är lika med det angivna värdet. |
| `ExecuteActionsIf` | Ja | Anger om åtgärderna i villkoret ska utföras om testet är sant eller falskt. |

**Villkors** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Värde | 1: n | De data som används av kontrollen. Om typen av kontroll är `ClaimsExist` , anger det här fältet en ClaimTypeReferenceId att fråga efter. Om typen av kontroll är `ClaimEquals` , anger det här fältet en ClaimTypeReferenceId att fråga efter. Ett annat värde-element innehåller det värde som ska kontrol leras.|
| Action | 1:1 | Den åtgärd som ska vidtas om villkors kontrollen i ett Orchestration-steg är sann. Värdet för **åtgärden** är inställt på `SkipThisValidationTechnicalProfile` . Anger att den tillhör ande tekniska profilen för verifiering inte ska köras. |

### <a name="example"></a>Exempel

I följande exempel används de här verifierings teknik profilerna:

1. Den första verifierings tekniska profilen kontrollerar användarautentiseringsuppgifter och fortsätter inte om ett fel inträffar, t. ex. ogiltigt användar namn eller felaktigt lösen ord.
2. Nästa validerings tekniska profil körs inte om userType-anspråket inte finns eller om värdet för userType är `Partner` . Den tekniska verifierings profilen försöker läsa användar profilen från den interna kund databasen och fortsätta om ett fel inträffar, till exempel REST API tjänsten inte är tillgänglig eller något internt fel.
3. Den senaste verifierings tekniska profilen körs inte om userType-anspråket inte fanns eller om värdet för userType är `Customer` . Den tekniska verifierings profilen försöker läsa användar profilen från den interna partner databasen och fortsätter om ett fel inträffar, till exempel REST API tjänsten inte är tillgänglig eller något internt fel.

```xml
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
