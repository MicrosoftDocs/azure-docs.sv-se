---
title: UserJourneys | Microsoft Docs
description: Ange UserJourneys-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ce41edd2c0048a20368dd02c2dd6101248e26c14
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400021"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Användar resan anger explicita sökvägar genom vilka en princip tillåter ett förlitande parts program att hämta önskade anspråk för en användare. Användaren tas via dessa sökvägar för att hämta de anspråk som ska visas för den förlitande parten. Med andra ord definierar användar resan affärs logiken för vad en användare går igenom när ramverket för Azure AD B2C Identity Experience bearbetar begäran.

Dessa användar resor kan betraktas som mallar som är tillgängliga för att tillgodose de olika förlitande parternas viktigaste behov av intresse för de olika förlitande parterna i intresset. Användar resor underlättar definitionen av den förlitande part delen av en princip. En princip kan definiera flera användar resor. Varje användar resa är en sekvens av Orchestration-steg.

För att definiera användar resan som stöds av principen läggs ett **UserJourneys** -element till under elementet på den översta nivån i princip filen.

**UserJourneys** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| UserJourney | 1: n | En användar resa som definierar alla konstruktioner som krävs för ett fullständigt användar flöde. |

**UserJourney** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare för en användar resa som kan användas för att referera till den från andra element i principen. **DefaultUserJourney** -elementet för den [förlitande part-principen](relyingparty.md) pekar på det här attributet. |

**UserJourney** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | Lista över tekniska profiler för auktorisering. | 
| OrchestrationSteps | 1: n | En Orchestration-sekvens som måste följas av för en lyckad transaktion. Varje användar resa består av en ordnad lista över Orchestration-steg som körs i följd. Om ett steg Miss lyckas, Miss lyckas transaktionen. |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

Anta att en användare har slutfört en UserJourney och erhållit en åtkomst eller en ID-token. Användaren måste identifieras för att kunna hantera ytterligare resurser, t. ex. [UserInfo-slutpunkten](userinfo-endpoint.md). För att starta den här processen måste användaren presentera åtkomsttoken som utfärdats tidigare som bevis på att de ursprungligen autentiserades av en giltig Azure AD B2C-princip. En giltig token för användaren måste alltid vara närvarande under den här processen för att se till att användaren får göra denna begäran. De tekniska profilerna för auktorisering validerar inkommande token och extraherar anspråk från token.

**AuthorizationTechnicalProfiles** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | Lista över tekniska profiler för auktorisering. | 

**AuthorizationTechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | Ja | Identifieraren för den tekniska profil som ska köras. |

I följande exempel visas ett användar resa-element med tekniska profiler för auktorisering:

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

En användar resa representeras som en Orchestration-sekvens som måste följas av för en lyckad transaktion. Om ett steg Miss lyckas, Miss lyckas transaktionen. Dessa steg hänvisar till både de bygg stenar och de anspråks leverantörer som tillåts i princip filen. Alla Orchestration-steg som är ansvariga för att visa eller återge en användar upplevelse har också en referens till motsvarande innehålls definitions identifierare.

Orchestration-steg kan utföras villkorligt baserat på de villkor som definieras i elementet Orchestration-steg. Du kan till exempel kontrol lera att du bara utför ett Dirigerings steg om det finns ett specifikt anspråk eller om ett anspråk är lika med eller inte det angivna värdet.

Om du vill ange den sorterade listan över Orchestration-steg läggs ett **OrchestrationSteps** -element till som en del av principen. Det här elementet är obligatoriskt.

**OrchestrationSteps** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1: n | Ett ordnat Orchestration-steg. |

**OrchestrationStep** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| `Order` | Ja | Ordningen för Orchestration-stegen. |
| `Type` | Ja | Typ av Orchestration-steg. Möjliga värden: <ul><li>**ClaimsProviderSelection** – anger att Orchestration-steget presenterar olika anspråks leverantörer för användaren att välja ett.</li><li>**CombinedSignInAndSignUp** – anger att Orchestration-steget presenterar en kombinerad registrering av sociala leverantörs inloggnings sidor och lokalt konto.</li><li>**ClaimsExchange** – anger att Orchestration-steget utbyter anspråk med en anspråks leverantör.</li><li>**GetClaims** – anger att Orchestration-steget ska bearbeta anspråks data som skickas till Azure AD B2C från den förlitande parten via dess `InputClaims` konfiguration.</li><li>**InvokeSubJourney** – anger att Orchestration-steget utbyter anspråk med en [under resa](subjourneys.md) (i offentlig för hands version).</li><li>**SendClaims** – anger att Orchestration-steget skickar anspråk till den förlitande parten med en token som utfärdats av en anspråks utfärdare.</li></ul> |
| ContentDefinitionReferenceId | Inga | Identifieraren för den [innehålls definition](contentdefinitions.md) som är associerad med det här Orchestration-steget. Vanligt vis definieras referens identifieraren för innehålls definitionen i den självkontrollerade tekniska profilen. Men det finns vissa fall då Azure AD B2C behöver visa något utan en teknisk profil. Det finns två exempel – om typen av Orchestration-steg är något av följande: eller så `ClaimsProviderSelection`  `CombinedSignInAndSignUp` måste Azure AD B2C Visa valet av identitetsprovider utan att ha en teknisk profil. |
| CpimIssuerTechnicalProfileReferenceId | Inga | Typen av Orchestration-steg är `SendClaims` . Den här egenskapen definierar den tekniska profil identifieraren för den anspråks leverantör som utfärdar token för den förlitande parten.  Om inget används skapas ingen förlitande parts-token. |

**OrchestrationStep** -elementet kan innehålla följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Villkor | 0: n | En lista med förutsättningar som måste uppfyllas för att Orchestration-steget ska kunna köras. |
| ClaimsProviderSelections | 0: n | En lista med val av anspråks leverantörer för Orchestration-steget. |
| ClaimsExchanges | 0: n | En lista med anspråks utbyten för Orchestration-steget. |
| JourneyList | 0:1 | En lista över under transport kandidater för Orchestration-steget. |

### <a name="preconditions"></a>Villkor

Elementet **villkor** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Villkor | 1: n | Beroende på den tekniska profil som används omdirigerar antingen klienten enligt valet av anspråks leverantör eller gör ett Server anrop till Exchange-anspråk. |


#### <a name="precondition"></a>Villkor

**Villkors** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| `Type` | Ja | Typ av kontroll eller fråga som ska utföras för det här villkoret. Värdet kan vara **ClaimsExist**, som anger att åtgärderna ska utföras om de angivna anspråken finns i användarens aktuella anspråks uppsättning eller **ClaimEquals**, vilket anger att åtgärderna ska utföras om det angivna anspråket finns och dess värde är lika med det angivna värdet. |
| `ExecuteActionsIf` | Ja | Använd ett sant-eller falskt-test för att avgöra om åtgärderna i villkoret ska utföras. |

**Villkors** elementen innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Värde | 1: n | En ClaimTypeReferenceId som ska frågas efter. Ett annat värde element innehåller det värde som ska kontrol leras.</li></ul>|
| Åtgärd | 1:1 | Den åtgärd som ska utföras om villkors kontrollen i ett Orchestration-steg är sann. Om värdet för `Action` är inställt på `SkipThisOrchestrationStep` , ska den associerade `OrchestrationStep` inte utföras. |

#### <a name="preconditions-examples"></a>Exempel på villkor

Följande villkor kontrollerar om användarens objectId finns. Användaren har valt att logga in med ett lokalt konto i användar resan. Om objectId finns hoppar du över det här Orchestration-steget.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Följande villkor kontrollerar om användaren har loggat in med ett socialt konto. Ett försök görs att hitta användar kontot i katalogen. Om användaren loggar in eller registrerar sig med ett lokalt konto kan du hoppa över det här steget för att dirigera.

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Villkor kan kontrol lera flera villkor. I följande exempel kontrol leras om "objectId" eller "e-post" finns. Om det första villkoret är sant hoppar resan till nästa steg för att dirigera.

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Ett Dirigerings steg av typen `ClaimsProviderSelection` eller `CombinedSignInAndSignUp` kan innehålla en lista över anspråks leverantörer som en användare kan logga in med. Ordningen på elementen inuti `ClaimsProviderSelections` elementen styr ordningen för de identitets leverantörer som visas för användaren.

**ClaimsProviderSelections** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1: n | Tillhandahåller listan över anspråks leverantörer som kan väljas.|

**ClaimsProviderSelections** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| DisplayOption| Inga | Styr beteendet för ett ärende där ett enda val av anspråks leverantör är tillgängligt. Möjliga värden: `DoNotShowSingleProvider` (standard), användaren omdirigeras omedelbart till den federerade identitets leverantören. Eller `ShowSingleProvider` Azure AD B2C visar inloggnings sidan med valet för enskild identitetsprovider. Om du vill använda det här attributet måste [innehålls definitions versionen](page-layout.md) vara `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` och högre.|

**ClaimsProviderSelection** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Inga | Identifieraren för anspråks utbytet, som körs i nästa Dirigerings steg för valet av anspråks leverantör. Det här attributet eller attributet ValidationClaimsExchangeId måste anges, men inte båda. |
| ValidationClaimsExchangeId | Inga | ID för anspråk utbytet, som körs i det aktuella Orchestration-steget för att verifiera valet av anspråks leverantör. Det här attributet eller attributet TargetClaimsExchangeId måste anges, men inte båda. |

### <a name="claimsproviderselection-example"></a>ClaimsProviderSelection-exempel

I följande Orchestration-steg kan användaren välja att logga in med Facebook, LinkedIn, Twitter, Google eller ett lokalt konto. Om användaren väljer en av sociala identitets leverantörer körs det andra Orchestration-steget med det valda anspråks utbytet som anges i `TargetClaimsExchangeId` attributet. Det andra Orchestration-steget omdirigerar användaren till den sociala identitets leverantören för att slutföra inloggnings processen. Om användaren väljer att logga in med det lokala kontot Azure AD B2C stanna kvar på samma Dirigerings steg (samma registrerings sida eller inloggnings sida) och hoppar över det andra Orchestration-steget.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

**ClaimsExchanges** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1: n | Beroende på den tekniska profil som används omdirigerar antingen klienten enligt de valda ClaimsProviderSelection eller gör ett Server anrop till Exchange-anspråk. |

**ClaimsExchange** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare för steget anspråk Exchange. Identifieraren används för att referera till anspråks utbytet från en anspråks leverantör urvals steg i principen. |
| TechnicalProfileReferenceId | Ja | Identifieraren för den tekniska profil som ska köras. |

## <a name="journeylist"></a>JourneyList

**JourneyList** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Dr | 1:1 | En referens till en under transport som ska anropas. |

### <a name="candidate"></a>Dr

**Kandidat** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Ja | Identifieraren för den [under resa](subjourneys.md) som ska köras. |
