---
title: Testa för DevOps för LUIS-appar
description: Testa din Language Understanding-app (LUIS) i en DevOps-miljö.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788459"
---
# <a name="testing-for-luis-devops"></a>Testa för LUIS-DevOps

Program varu tekniker som utvecklar en Language Understanding-app (LUIS) kan tillämpa DevOps-metoder kring [käll kontroll](luis-concept-devops-sourcecontrol.md), [automatiserade versioner](luis-concept-devops-automation.md), [testning](luis-concept-devops-testing.md)och [versions hantering](luis-concept-devops-automation.md#release-management) genom att följa dessa rikt linjer.

I Agile-metoder för program varu utveckling spelar testningen en integral roll i att skapa kvalitets program vara. Varje betydande ändring av en LUIS-app bör åtföljas av tester som är utformade för att testa de nya funktionerna som utvecklaren bygger på appen. De här testerna kontrol leras i din käll kods lagring tillsammans med `.lu` källan för din Luis-app. Implementeringen av ändringen är färdig när appen uppfyller testerna.

Tester är en viktig del av [CI/CD-arbetsflöden](luis-concept-devops-automation.md). När ändringar i en LUIS-app föreslås i en pull-begäran (PR) eller när ändringar har slagits samman i huvud grenen, ska CI-arbetsflöden köra testerna för att kontrol lera att uppdateringarna inte har orsakat några regressioner.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Så här utför du enhets testning och batch-testning

Det finns två olika typer av testning för en LUIS-app som du måste utföra i kontinuerliga integrations arbets flöden:

- **Enhets test** – relativt enkla tester som verifierar de viktigaste funktionerna i din Luis-app. Ett enhets test skickas när den förväntade avsikten och förväntade entiteter returneras för en specifik test-uttryck. Alla enhets test måste klara för att test körningen ska slutföras.  
Den här typen av testning liknar [interaktiv testning](./luis-concept-test.md) som du kan göra i Luis- [portalen](https://www.luis.ai/).

- **Batch-test** – batch-testning är ett omfattande test på din aktuella tränade modell för att mäta dess prestanda. Till skillnad från enhets test är batch-testningen inte klar | det går inte att testa. Förväntat med batch-testning är att varje test returnerar den förväntade avsikten och förväntade entiteter. I stället kan du använda ett batch-test för att Visa precisionen för varje avsikt och enhet i din app och hjälpa dig att jämföra med tiden när du gör förbättringar.  
Den här typen av testning är samma som [batch-testningen](./luis-how-to-batch-test.md) som du kan utföra interaktivt i Luis-portalen.

Du kan använda enhets testning från början av ditt projekt. Batch-testning är bara faktiskt av värde när du har utvecklat schemat för din LUIS-app och du arbetar med att förbättra dess noggrannhet.

För både enhets test och batch-test ser du till att dina test-yttranden hålls åtskilda från din utbildnings yttranden. Om du testar samma data som du tränar in får du det falska intryck som appen fungerar bra när den bara översätts till test data. Testerna måste vara osett av modellen för att testa hur väl den generaliseras.

### <a name="writing-tests"></a>Skriver test

När du skriver en uppsättning tester måste du för varje test definiera:

* Testa uttryck
* Förväntad avsikt
* Förväntade entiteter.

Använd LUIS [batch File-syntax](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities) för att definiera en grupp av tester i en JSON-formaterad fil. Ett exempel:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Vissa test verktyg, till exempel [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) stöder även LUDown-formaterade testfiler.

#### <a name="designing-unit-tests"></a>Utforma enhets tester

Enhets test bör utformas för att testa kärn funktionerna i din LUIS-app. I varje iteration eller Sprint på din app-utveckling bör du skriva ett tillräckligt antal tester för att kontrol lera att de nyckel funktioner som du implementerar i iterationen fungerar korrekt.

I varje enhets test, för en specifik test uttryck, kan du:

* Testa att rätt avsikt returneras
* Testa att "Key"-entiteter – de som är viktiga för din lösning-returneras.
* Testa att [förutsägelse poängen](./luis-concept-prediction-score.md) för avsikt och entiteter överskrider ett tröskelvärde som du definierar. Du kan till exempel bestämma att du bara vill att ett test har godkänts om förutsägelse poängen för avsikten och dina nyckel enheter överskrider 0,75.

I enhets test är det en bra idé att testa att dina nyckel enheter har returnerats i förutsägelse svaret, men att ignorera falska positiva identifieringar. *Falska positiva identifieringar* är entiteter som finns i förutsägelse svaret men som inte har definierats i de förväntade resultaten för ditt test. Genom att ignorera falska positiva identifieringar gör det mindre betungande för att skapa enhets tester samtidigt som du kan fokusera på att testa att de data som är viktiga för din lösning returneras i ett förutsägelse svar.

> [!TIP]
> [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -verktyget stöder alla dina Luis-testnings behov. `compare`Kommandot när det används i [enhets test läge](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) förutsätter att alla tester passas och kommer att ignorera falska positiva resultat för entiteter som inte är märkta i förväntade resultat.

#### <a name="designing-batch-tests"></a>Utforma batch-tester

Batch-testuppsättningar ska innehålla ett stort antal testfall som är utformade för att testa alla syften och alla entiteter i din LUIS-app. Information om hur du definierar en batch-testuppsättning finns [i batch-testning i Luis-portalen](./luis-how-to-batch-test.md) .

### <a name="running-tests"></a>Köra tester

LUIS-portalen erbjuder funktioner som hjälper dig med interaktiv testning:

* Med [**interaktiv testning**](./luis-concept-test.md) kan du skicka in ett exempel på uttryck och få svar på Luis-identifierade avsikter och entiteter. Du verifierar att testet har genomförts genom visuell inspektion.

* [**Batch-testning**](./luis-how-to-batch-test.md) använder en batch-testfil som indata för att verifiera din aktiva utbildade version för att mäta dess förutsägelse noggrannhet. Ett batch-test hjälper dig att Visa precisionen för varje avsikt och entitet i din aktiva version, vilket visar resultat med ett diagram.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Köra tester i ett automatiserat Bygg arbets flöde

De interaktiva test funktionerna i LUIS-portalen är användbara, men för DevOps har automatiserade tester som utförs i ett CI/CD-arbetsflöde vissa krav:

* Test verktyg måste köras i ett arbets flödes steg på en build-Server. Det innebär att verktygen måste kunna köras på kommando raden.
* Test verktygen måste kunna köra en grupp av tester mot en slut punkt och automatiskt verifiera de förväntade resultaten mot de faktiska resultaten.
* Om testerna inte fungerar måste testverktyg returnera en status kod för att stoppa arbets flödet och "det går inte att bygga".

LUIS erbjuder inte ett kommando rads verktyg eller ett API för hög nivå som erbjuder dessa funktioner. Vi rekommenderar att du använder [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -verktyget för att köra tester och verifiera resultat, både på kommando raden och under automatiserad testning i ett CI/CD-arbetsflöde.

Test funktionerna som är tillgängliga i LUIS-portalen kräver inte en publicerad slut punkt och är en del av LUIS redigerings funktionerna. När du implementerar tester i ett automatiserat Bygg arbets flöde måste du publicera LUIS app-versionen för att testas till en slut punkt så att test verktyg som NLU. DevOps kan skicka förutsägelse begär Anden som en del av testningen.

> [!TIP]
> * Om du implementerar en egen test lösning och skriver kod för att skicka test-yttranden till en slut punkt, måste du komma ihåg att om du använder LUIS Authoring-nyckeln är den tillåtna transaktions taxan begränsad till 5TPS. Antingen begränsar du sändnings frekvensen eller använder en förutsägelse nyckel i stället.
> * Kom ihåg att använda `log=false` i frågesträngen för din förutsägelse förfrågan när du skickar test frågor till en slut punkt. Detta säkerställer att din test-yttranden inte loggas av LUIS och slutar i gransknings listan för slut punkts yttranden som visas i den LUIS [aktiva inlärnings](./luis-concept-review-endpoint-utterances.md) funktionen. det innebär att de av misstag läggs till i övnings yttranden för appen.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Köra enhets tester på kommando raden och i CI/CD-arbetsflöden

Du kan använda [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -paket för att köra tester på kommando raden:

* Använd NLU. DevOps [test kommando](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) för att skicka tester från en test fil till en slut punkt och för att avbilda faktiska förutsägelse resultat i en fil.
* Använd NLU. DevOps [compare-kommando](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) för att jämföra faktiska resultat med förväntade resultat som definierats i test filen för indata. `compare`Kommandot genererar nunit-testutdata och när det används i [enhets test läge](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) genom att använda `--unit-test` flaggan, förutsätts att alla tester passas.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Köra batch-tester på kommando raden och i CI/CD-arbetsflöden

Du kan också använda NLU. DevOps-paket för att köra batch-test på kommando raden.

* Använd NLU. DevOps [test kommando](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) för att skicka tester från en test fil till en slut punkt och för att avbilda faktiska förutsägelse resultat i en fil, samma som med enhets test.
* Använd NLU. DevOps [Jämför kommando](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) i [prestanda test läge](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) för att mäta appens prestanda du kan också jämföra appens prestanda mot ett prestandatest, till exempel resultaten från den senaste incheckningen av huvud versionen eller den aktuella versionen. I test läge för prestanda `compare` genererar kommandot nunit test-utdata och [batch-test resulterar](./luis-glossary.md#batch-test) i JSON-format.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS icke-deterministisk utbildning och påverkan på testning

När LUIS är träna en modell, t. ex. en avsikt, behöver den både positiva data – den yttranden utbildning som du har angett för att träna appen för modell-och negativa data data som *inte* är giltiga exempel på användningen av modellen. Under utbildningen bygger LUIS negativa data från en modell från alla positiva data som du har angett för de andra modellerna, men i vissa fall kan en data obalans uppstå. För att undvika den här obalansen samplar LUIS en delmängd av de negativa data i ett icke-deterministiskt sätt för att optimera för en bättre balanserade inlärning, bättre modell prestanda och snabbare inlärnings tid.

Resultatet av denna icke deterministiska utbildning är att du kan få ett något [annorlunda förutsägelse svar mellan olika utbildningar](./luis-concept-prediction-score.md), vanligt vis för intentor och/eller entiteter där [förutsägelse poängen](./luis-concept-prediction-score.md) inte är hög.

Om du vill inaktivera icke-deterministisk utbildning för de LUIS app-versioner som du skapar för test ändamål, använder du [API: et för versions inställningar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med `UseAllTrainingData` inställningen inställd på `true` .

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [implementerar CI/CD-arbetsflöden](luis-concept-devops-automation.md)
* Lär dig hur du [implementerar DevOps för Luis med GitHub](luis-how-to-devops-with-github.md)