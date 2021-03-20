---
title: Under transporter i Azure Active Directory B2C | Microsoft Docs
description: Ange under resan-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97386875"
---
# <a name="sub-journeys"></a>Under transporter

Under transporter kan användas för att organisera och förenkla flödet av Orchestration-steg i en användar resa. [Användarresor](userjourneys.md) anger explicita sökvägar genom vilka en princip tillåter att ett anspråksbaserat program hämtar önskade anspråk för en användare. Via dessa sökvägar kan användaren hämta de anspråk som ska presenteras för den förlitande parten. Med andra ord definierar användar resan affärs logiken för vad en användare går igenom när ramverket för Azure AD B2C Identity Experience bearbetar begäran. En användar resa representeras som en Orchestration-sekvens som måste följas av för en lyckad transaktion. [ClaimsExchange](userjourneys.md#claimsexchanges) -elementet i ett Orchestration-steg är knutet till en enskild [teknisk profil](technicalprofiles.md) som körs.

En under resa är en gruppering av Orchestration-steg som kan anropas när som helst inom en användar resa. Du kan använda under transporter för att skapa återanvändbara steg sekvenser eller implementera branchning för att bättre representera affärs logiken.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Användar resa, förgrening

Under transporter beter sig som [användar resor](userjourneys.md), eftersom båda representeras som en Orchestration-sekvens som måste följas av för en lyckad transaktion. Användar resor kan anropas på egen hand och kräver ett SendClaims-steg för att köra. Under transporter är komponenter för användar resor och kan inte anropas oberoende, och de anropas alltid från en användar resa.

Huvud delen av branchning är att möjliggöra bättre affärs logik bearbetning i en användar resa. Vanliga Orchestration-steg grupperas i enskilda delar som anropas separat. En under resa kan förenkla en resa där flera Dirigerings steg kombineras (med samma förutsättningar). En under resa anropas bara från en användar resa, den får inte anropa en annan under resa.

Det finns två typer av under transporter:

- **Call** -returnerar kontroll till anroparen. Under resan körs och sedan återgår kontrollen till Orchestration-steget som för närvarande körs inom användar resan.
- **Överföring** – överför kontroll till under transporten (irreversibl förgrening). Under resan måste ha ett SendClaims-steg för att returnera anspråk tillbaka till den förlitande partens program.

## <a name="example-scenarios"></a>Exempelscenarier

### <a name="call-sub-journey"></a>Samtals under resa

En samtals under resa är användbar i följande scenarier:

- Ålders hantera: för ålders hantera finns det många delade komponenter bland användar resan. Branchning gör det möjligt att kompilera gemensamma element till delar av komponenter.  
- Överordnat medgivande: förgreningar gör det möjligt för dig att delta i den överordnade godkännande designen genom att tillåta oss att få åtkomst till anspråk från användar resan den mindre utförda, tillsammans med att kunna förgrena till en användare som har ett överordnat medgivande när användaren har beviljat sitt medgivande. 
- Registrera dig för att logga in: Överväg ett scenario där en användare redan finns i katalogen, men kan ha glömt att de hade skapat ett konto. Det kan vara önskvärt i stället för att meddela användaren att autentiseringsuppgifterna som de har angett redan finns och tvinga användaren att starta om resan att principen kan utföra en växel från ett registrerings flöde till ett inloggnings flöde för användaren.  

### <a name="transfer-sub-journey"></a>Överföring under resa

En överförings under resa är användbar i följande scenarier:

- Visar en block sida.
- A/B-testning genom att vidarebefordra begäran till en under resa för att köra och utfärda en token.

## <a name="adding-a-subjourneys-element"></a>Lägga till ett under transport element

Följande är ett exempel på ett `SubJourney` element av typen `Call` , som återgår tillbaka till användar resan.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Följande är ett exempel på ett `SubJourney` element av typen `Transfer` , som returnerar en token tillbaka till den förlitande part applikationen.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>Anropa ett under transport steg

Ett nytt Orchestration-steg av typen `InvokeSubJourney` används för att köra en under resa. Följande är ett exempel som visar alla körnings element i det här Orchestration-steget.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> En under resa ska inte anropa en annan under resa.

## <a name="components"></a>Komponenter

Om du vill definiera de under transporter som stöds av principen lägger du till ett under **transport** element under elementet på den översta nivån i princip filen.

**Subtransports** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Under resa | 1: n | En under resa som definierar alla konstruktioner som krävs för ett fullständigt användar flöde. |

**Subtransports** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | Under res-ID som kan användas av användar resan för att referera till under resan i principen. Elementet **SubJourneyReferenceId** i [kandidat](userjourneys.md#journeylist) elementet pekar på det här attributet. |
| Typ | Ja | Möjliga värden: `Call` , eller `Transfer` . Mer information finns i [användar resa branchning](#user-journey-branching)|

**Subtransport** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | En Orchestration-sekvens som måste följas av för en lyckad transaktion. Varje användar resa består av en ordnad lista över Orchestration-steg som körs i följd. Om ett steg Miss lyckas, Miss lyckas transaktionen. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

En fullständig lista över steg för Orchestration-steg finns i [UserJourneys](userjourneys.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [UserJourneys](userjourneys.md)