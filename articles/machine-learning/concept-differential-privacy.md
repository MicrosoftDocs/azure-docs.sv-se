---
title: Differentiell sekretess i Machine Learning (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig vad differentiell sekretess är och hur du kan implementera Differentiellt privata system som behåller data sekretessen.
author: luisquintanilla
ms.author: luquinta
ms.date: 01/21/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: responsible-ml
ms.openlocfilehash: 39f4b1a7b9eb1ad7a87097240dd772e4f2dadf17
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683554"
---
# <a name="what-is-differential-privacy-in-machine-learning-preview"></a>Vad är differentiell integritet i Machine Learning (för hands version)

Lär dig mer om differentiell integritet i Machine Learning och hur det fungerar.

Eftersom den mängd data som en organisation samlar in och använder för analyser ökar, så kan du läsa sekretess och säkerhet. Analyser kräver data. Normalt är de mer data som används för att träna modeller, desto mer exakta är de. När person uppgifter används för dessa analyser är det särskilt viktigt att data förblir privata under hela användningen.

## <a name="how-differential-privacy-works"></a>Hur differentiell integritet fungerar

Differentiell sekretess är en uppsättning system och metoder som hjälper till att skydda enskilda personers och privata data.

> [!div class="mx-imgBorder"]
> ![Studie process för differentiell integritets dator](./media/concept-differential-privacy/differential-privacy-machine-learning.jpg)

I traditionella scenarier lagras rå data i filer och databaser. När användarna analyserar data använder de vanligt vis rå data. Detta är ett problem eftersom det kan orsaka intrång i en individs integritet. Differentiell sekretess försöker åtgärda problemet genom att lägga till "brus" eller slumpmässig het för data så att användarna inte kan identifiera enskilda data punkter. Som minst ger ett sådant system plausible för att kunna nekas. Därför bevaras sekretessen för enskilda personer med begränsad inverkan på data noggrannheten.

I ett särskilt privat system delas data via begär Anden som kallas **frågor**. När en användare skickar en fråga för data, så kallas de **säkerhetsmekanismerna** för att överföra data till de begärda data. Sekretess rutiner returnerar en *uppskattning av data* i stället för rå data. Den här sekretessen – bevara resultatet visas i en **rapport**. Rapporter består av två delar, faktiska data som beräknas och en beskrivning av hur data skapades.

## <a name="differential-privacy-metrics"></a>Differentiella integritets mått

Differentiell sekretess försöker skydda mot möjligheten att en användare kan skapa ett obestämt antal rapporter för att slutligen avslöja känsliga data. Ett värde känt som **Epsilon** mäter hur brus eller privat en rapport är. Epsilon har en inverterad relation till brus eller sekretess. Ju lägre Epsilon, desto mer brus (och privat) data.

Epsilon-värden är icke-negativa. Värdena under 1 ger fullständig plausible för att kunna nekas. Allt som är högre än 1 har en högre risk för exponering av faktiska data. När du implementerar Differentiellt privata system vill du skapa rapporter med Epsilon-värden mellan 0 och 1.

Ett annat värde som är direkt korrelerat med Epsilon är **delta**. Delta är ett mått på sannolikheten att en rapport inte är helt privat. Ju högre delta, desto högre Epsilon. Eftersom dessa värden är korrelerade används Epsilon oftare.

## <a name="limit-queries-with-a-privacy-budget"></a>Begränsa frågor med en sekretess budget

För att säkerställa sekretess i system där flera frågor tillåts definierar differentiell integritet en hastighets begränsning. Den här gränsen kallas för en **Sekretess budget**. Sekretess budgetar förhindrar att data återskapas genom flera frågor. Sekretess budgetar tilldelas en Epsilon-mängd, vanligt vis mellan 1 och 3 för att begränsa risken för omidentifiering. När rapporter skapas håller sekretess budgetarna reda på Epsilon-värdet för enskilda rapporter samt summan för alla rapporter. När en sekretess budget har lagts till eller blivit slut kan användarna inte längre komma åt data. 

## <a name="reliability-of-data"></a>Data tillförlitlighet

Även om bevarande av sekretess bör vara målet, är det en kompromiss när det gäller användbarhet och tillförlitlighet för data. I data analys kan precisionen betraktas som ett åtgärds mått som introduceras genom samplings fel. Denna osäkerhet tenderar att falla inom vissa gränser. **Noggrannhet** från ett Differentiellt sekretess perspektiv i stället mäter tillförlitligheten för data, vilket påverkas av den osäkerhet som introducerats av sekretess rutinerna. I korthet översätts en högre nivå av brus eller sekretess till data som har lägre Epsilon, exakthet och tillförlitlighet. 

## <a name="open-source-differential-privacy-libraries"></a>Integritets bibliotek med öppen källkod

SmartNoise är ett projekt med öppen källkod som innehåller olika komponenter för att skapa globala, Differentiellt privata system. SmartNoise består av följande toppnivå komponenter:

- Kärn bibliotek för SmartNoise
- SDK-bibliotek för SmartNoise

### <a name="smartnoise-core"></a>SmartNoise-kärna

Kärn biblioteket innehåller följande sekretessfunktioner för att implementera ett Differentiellt privat system:

|Komponent  |Beskrivning  |
|---------|---------|
|Analys     | En diagram Beskrivning av godtyckliga beräkningar. |
|Systemhälsoverifierare     | Ett Rust-bibliotek som innehåller en uppsättning verktyg för att kontrol lera och härleda de nödvändiga villkoren för en analys som är Differentiellt privat.          |
|Körning     | Mediet för att köra analysen. Referens körningen skrivs i Rust men körningar kan skrivas med hjälp av alla beräknings ramverk som SQL och Spark beroende på dina data behov.        |
|Bindningar     | Språk bindningar och hjälp bibliotek för att bygga analyser. För närvarande tillhandahåller SmartNoise python-bindningar. |

### <a name="smartnoise-sdk"></a>SmartNoise SDK

System biblioteket innehåller följande verktyg och tjänster för att arbeta med tabell-och Relations data:

|Komponent  |Beskrivning  |
|---------|---------|
|Dataåtkomst     | Bibliotek som fångar upp och bearbetar SQL-frågor och genererar rapporter. Det här biblioteket implementeras i python och stöder följande ODBC-och DBAPI-data Källor:<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>Pandas</li></ul>|
|Tjänst     | Körnings tjänst som tillhandahåller en REST-slutpunkt för att hantera begär Anden eller frågor mot delade data källor. Tjänsten är utformad för att tillåta sammansättning av differentiella integritets moduler som fungerar på begär Anden som innehåller olika delta-och Epsilon-värden, även kallade heterogena begär Anden. Det här referens implementerings kontona ger ytterligare påverkan från frågor om korrelerade data. |
|Utvärderings     | Stochastic-utvärdering som kontrollerar sekretess överträdelser, precision och bias. Utvärderings metoden stöder följande tester: <ul><li>Sekretess test – avgör om en rapport följer villkoren i differentiell sekretess.</li><li>Noggrannhets test – mått om tillförlitlighet för rapporter ligger inom de övre och nedre gräns värdena med en konfidensnivå på 95%.</li><li>Verktygs test – avgör om konfidens gränserna för en rapport är tillräckligt nära för att komma åt data samtidigt som sekretessen fortfarande maximeras.</li><li>Bias-test – mäter fördelningen av rapporter för upprepade frågor för att säkerställa att de inte är obalanserade</li></ul> |

## <a name="next-steps"></a>Nästa steg

[Så här skapar du ett Differentiellt privat system](how-to-differential-privacy.md) i Azure Machine Learning.

Mer information om komponenterna i SmartNoise finns i GitHub-databaserna för [SmartNoise Core](https://github.com/opendifferentialprivacy/smartnoise-core), [SmartNoise SDK](https://github.com/opendifferentialprivacy/smartnoise-sdk)och [SmartNoise-exempel](https://github.com/opendifferentialprivacy/smartnoise-samples).