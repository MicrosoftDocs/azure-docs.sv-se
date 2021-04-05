---
title: Diagnostik för Azure Application Insights-transaktion | Microsoft Docs
description: Application Insights slut punkt till slut punkt för transaktions diagnostik
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 7623b7131e6344a67c468d0436884ebfef9b0058
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96746105"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Enhetlig transaktions-diagnostik mellan komponenter

Den enhetliga diagnostiken korrelerar automatiskt telemetri från Server sidan från alla dina Application Insights övervakade komponenter till en enda vy. Det spelar ingen roll om du har flera resurser med separata Instrumentation-nycklar. Application Insights identifierar den underliggande relationen och gör att du enkelt kan diagnostisera program komponenten, beroendet eller undantaget som orsakade en transaktions minskning eller fel.

## <a name="what-is-a-component"></a>Vad är en komponent?

Komponenterna är oberoende distributions bara delar av ditt distribuerade/mikrotjänster-program. Utvecklare och drifts grupper har synlighet på kod nivå eller åtkomst till telemetri som genereras av dessa program komponenter.

* Komponenterna skiljer sig från "observerade" externa beroenden som SQL, EventHub osv. som ditt team/din organisation inte har åtkomst till (kod eller telemetri).
* Komponenter körs på valfritt antal server-/roll-/container instanser.
* Komponenter kan vara separata Application Insights Instrumentation-nycklar (även om prenumerationer är olika) eller olika roller som rapporterar till en enda Application Insights Instrumentation-nyckel. Den nya upplevelsen visar information för alla komponenter, oavsett hur de har kon figurer ATS.

> [!NOTE]
> * **Saknas relaterade objekt länkar?** All relaterad telemetri finns i de [övre](#cross-component-transaction-chart) och [nedre](#all-telemetry-with-this-operation-id) avsnitten på vänster sida. 

## <a name="transaction-diagnostics-experience"></a>Upplevelse för transaktions diagnostik
Den här vyn har fyra nyckel delar: resultat lista, ett transaktions diagram över flera komponenter, en tidssekvens-lista över all telemetri som är relaterad till den här åtgärden och informations fönstret för alla valda telemetridata till vänster.

![Nyckel delar](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Diagram över komponent transaktion

Det här diagrammet innehåller en tids linje med horisontella staplar för varaktigheten för begär Anden och beroenden mellan komponenter. Eventuella undantag som samlas in markeras också på tids linjen.

* Den översta raden i det här diagrammet representerar start punkten, den inkommande begäran till den första komponenten som anropas i den här transaktionen. Varaktigheten är den totala tid det tar för transaktionen att slutföras.
* Alla anrop till externa beroenden är enkla icke-döljbara rader, med ikoner som representerar beroende typen.
* Anrop till andra komponenter är komprimerbara rader. Varje rad motsvarar en speciell åtgärd som anropas vid komponenten.
* Som standard visas begäran, beroendet eller undantaget som du har valt på höger sida.
* Välj en rad för att se [information till höger](#details-of-the-selected-telemetry). 

> [!NOTE]
> Anrop till andra komponenter har två rader: en rad representerar det utgående anropet (beroendet) från anroparens komponent, och den andra raden motsvarar den inkommande begäran på den anropade komponenten. Den ledande ikonen och en distinkt formatering av varaktighets fälten bidrar till att skilja dem åt.

## <a name="all-telemetry-with-this-operation-id"></a>All telemetri med detta åtgärds-ID

I det här avsnittet visas en platt lista i en tidssekvens av all telemetri som är relaterad till den här transaktionen. Den visar även anpassade händelser och spår som inte visas i transaktions diagrammet. Du kan filtrera listan till telemetri som genereras av en bestämd komponent/anrop. Du kan välja ett telemetri-objekt i den här listan för att se motsvarande [information till höger](#details-of-the-selected-telemetry).

![Tidssekvens för all telemetri](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Information om den valda Telemetrin

Det här komprimerbara fönstret visar Detaljer för alla markerade objekt i transaktions diagrammet eller listan. "Visa alla" listar alla standardattribut som samlas in. Alla anpassade attribut visas separat under standard uppsättningen. Klicka på "..." under stack spårnings fönstret kan du hämta ett alternativ för att kopiera spårningen. "Öppna profiler spår" eller "öppna fel söknings ögonblicks bilder" visar diagnostik för kod nivå i motsvarande informations fönster.

![Undantags information](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Sökresultat

Det här komprimerbara fönstret visar de andra resultaten som uppfyller filter kriterierna. Klicka på ett resultat om du vill uppdatera respektive information i de tre avsnitten som anges ovan. Vi försöker hitta exempel som troligen har den information som är mest tillgänglig från alla komponenter, även om samplingen gäller. De visas som "föreslagna" exempel.

![Sökresultat](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler för profiler och ögonblicks bilds fel

[Application Insights profiler](./profiler.md) eller fel [söknings](snapshot-debugger.md) hjälp med kod nivå för diagnostik av prestanda-och fel problem. Med den här upplevelsen kan du se profiler och ögonblicks bilder från valfri komponent med ett enda klick.

Kontakta **serviceprofilerhelp \@ Microsoft.com** om du inte kan få igång profiler.

Kontakta **snapshothelp \@ Microsoft.com** om du inte kunde sätta igång Snapshot debugger.

![Profiler-integration](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Vanliga frågor

*Jag ser en enskild komponent i diagrammet och de andra visas bara som externa beroenden utan någon information om vad som hände i dessa komponenter.*

Möjliga orsaker:

* Är de andra komponenterna instrumenterade med Application Insights?
* Använder de den senaste stabila Application Insights SDK?
* Om de här komponenterna är separata Application Insights-resurser, har du nödvändig [åtkomst](resources-roles-access-control.md) om du har åtkomst och komponenterna är instrumenterade med de senaste Application Insights SDK: erna, berätta för oss via den övre högra feedback kanalen.

*Jag ser duplicerade rader för beroenden. Förväntas detta?*

För tillfället visar vi det utgående beroende anropet separat från den inkommande begäran. De två anropen ser vanligt vis likadana ut med att värdet för varaktighet skiljer sig på grund av nätverks fördröjningen. Den ledande ikonen och en distinkt formatering av varaktighets fälten bidrar till att skilja dem åt. Är den här presentationen av data förvirrande? Ge oss din feedback!

*Vad händer om klockan skevar över olika komponent instanser?*

Tids linjer justeras för klock skevar i transaktions diagrammet. Du kan se exakta tidsstämplar i informations fönstret eller med hjälp av analys.

*Varför saknar den nya upplevelsen de flesta av de relaterade objekt frågorna?*

Det här är avsiktligt. Alla relaterade objekt i alla komponenter är redan tillgängliga på den vänstra sidan (översta och nedre avsnitten). Den nya upplevelsen har två relaterade objekt som den vänstra sidan inte avser: all telemetri från fem minuter före och efter den här händelsen och tids linjen för användaren.

