---
title: Azure Application Insights Transaction Diagnostics | Microsoft Docs
description: Application Insights transaktionsdiagnostik från slutet till slut
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 60365079c295e154ff0a38277c9ccdec35157e6e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481403"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Enhetlig transaktionsdiagnostik mellan komponenter

Den enhetliga diagnostikupplevelsen korrelerar automatiskt telemetri på serversidan från alla dina Application Insights övervakade komponenter i en enda vy. Det spelar ingen roll om du har flera resurser med separata instrumentationsnycklar. Application Insights identifierar den underliggande relationen och gör att du enkelt kan diagnostisera programkomponenten, beroendet eller undantaget som orsakade en långsammare transaktion eller ett fel.

## <a name="what-is-a-component"></a>Vad är en komponent?

Komponenter är oberoende distribuerade delar av ditt distribuerade program/mikrotjänstprogram. Utvecklare och driftteam har synlighet på kodnivå eller åtkomst till telemetri som genereras av dessa programkomponenter.

* Komponenter skiljer sig från "observerade" externa beroenden som SQL, EventHub osv. som ditt team/din organisation kanske inte har åtkomst till (kod eller telemetri).
* Komponenter körs på val annat antal server-/roll-/containerinstanser.
* Komponenterna kan vara separata Application Insights instrumentationsnycklar (även om prenumerationerna är olika) eller olika roller som rapporterar till en Application Insights instrumentationsnyckel. Den nya upplevelsen visar information om alla komponenter, oavsett hur de har ställts in.

> [!NOTE]
> * **Saknar du de relaterade objektlänkarna?** All relaterad telemetri finns i de övre [och](#cross-component-transaction-chart) [nedre avsnitten](#all-telemetry-with-this-operation-id) på vänster sida. 

## <a name="transaction-diagnostics-experience"></a>Upplevelse av transaktionsdiagnostik
Den här vyn har fyra viktiga delar: resultatlista, ett transaktionsdiagram mellan komponenter, en tidssekvenslista över all telemetri som är relaterad till den här åtgärden och informationsfönstret för alla valda telemetriobjekt till vänster.

![Viktiga delar](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Transaktionsdiagram mellan komponenter

Det här diagrammet innehåller en tidslinje med vågräta staplar för varaktigheten för begäranden och beroenden mellan komponenter. Alla undantag som samlas in markeras också på tidslinjen.

* Den översta raden i det här diagrammet representerar startpunkten, den inkommande begäran till den första komponenten som anropas i den här transaktionen. Varaktigheten är den totala tid det tar för transaktionen att slutföras.
* Alla anrop till externa beroenden är enkla rader som inte kan komprimeras, med ikoner som representerar beroendetypen.
* Anrop till andra komponenter är komppulerbara rader. Varje rad motsvarar en specifik åtgärd som anropas på komponenten.
* Som standard visas den begäran, det beroende eller det undantag som du har valt på höger sida.
* Välj en rad om du vill [se dess information till höger.](#details-of-the-selected-telemetry) 

> [!NOTE]
> Anrop till andra komponenter har två rader: en rad representerar det utgående anropet (beroende) från anroparkomponenten och den andra raden motsvarar den inkommande begäran för den anropade komponenten. Den inledande ikonen och distinkt format för varaktighetsstaplar hjälper till att skilja dem åt.

## <a name="all-telemetry-with-this-operation-id"></a>All telemetri med det här åtgärds-ID:t

Det här avsnittet visar en platt listvy i en tidssekvens av all telemetri som är relaterad till den här transaktionen. Den visar även anpassade händelser och spårningar som inte visas i transaktionsdiagrammet. Du kan filtrera listan till telemetri som genereras av en specifik komponent/ett anrop. Du kan välja ett telemetriobjekt i den här listan om du vill [se motsvarande information till höger.](#details-of-the-selected-telemetry)

![Tidssekvens för all telemetri](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Information om den valda telemetrin

Det här kommerbara fönstret visar information om alla markerade objekt från transaktionsdiagrammet eller listan. "Visa alla" visar en lista över alla standardattribut som samlas in. Anpassade attribut visas separat under standarduppsättningen. Klicka på "..." under stackspårningsfönstret för att få ett alternativ för att kopiera spårningen. "Open profiler traces" (Öppna profilerarspårningar) eller "Open debug snapshot" (Öppna ögonblicksbild av felsökning) visar diagnostik på kodnivå i motsvarande informationsfönster.

![Undantagsdetaljer](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Sökresultat

Det här kommerbara fönstret visar de andra resultaten som uppfyller filtervillkoren. Klicka på ett resultat för att uppdatera respektive information i de tre avsnitten som anges ovan. Vi försöker hitta exempel som troligen har informationen tillgänglig från alla komponenter även om samplingen används i någon av dem. Dessa visas som "föreslagna" exempel.

![Sökresultat](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler och snapshot debugger

[Application Insights profilerare](./profiler.md) [eller felsökningsprogrammet för ögonblicksbilder](snapshot-debugger.md) med diagnostik på kodnivå för prestanda- och felproblem. Med den här upplevelsen kan du se profilerarspårningar eller ögonblicksbilder från valfri komponent med ett enda klick.

Om du inte kunde få Profiler att fungera kontaktar du **serviceprofilerhelp \@ microsoft.com**

Om du inte kunde Snapshot Debugger att fungera kontaktar du **snapshothelp \@ microsoft.com**

![Profiler-integrering](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Vanliga frågor

*Jag ser en enda komponent i diagrammet, och de andra visas bara som externa beroenden utan någon information om vad som hände i dessa komponenter.*

Potentiella orsaker:

* Instrumenteras de andra komponenterna med Application Insights?
* Använder de den senaste stabila Application Insights SDK?
* Om dessa komponenter är separata Application Insights-resurser, [](resources-roles-access-control.md) har du nödvändig åtkomst Om du har åtkomst och komponenterna instrumenteras med de senaste Application Insights-SDK:erna, så meddela oss via den övre högra feedbackkanalen.

*Jag ser dubblettrader för beroendena. Är detta förväntat?*

Just nu visar vi det utgående beroendeanropet separat från den inkommande begäran. Vanligtvis ser de två anropen identiska ut, och endast varaktighetsvärdet skiljer sig åt på grund av nätverkets tur och retur. Den inledande ikonen och distinkt format för varaktighetsstaplar hjälper till att skilja dem åt. Är den här presentationen av data förvirrande? Ge oss din feedback!

*Hur är det med klockskevningar mellan olika komponentinstanser?*

Tidslinjerna justeras för klockskevningar i transaktionsdiagrammet. Du kan se de exakta tidsstämplar i informationsfönstret eller med hjälp av Analytics.

*Varför saknas de flesta relaterade objektfrågor i den nya upplevelsen?*

Det här är avsiktligt. Alla relaterade objekt, för alla komponenter, är redan tillgängliga på vänster sida (övre och nedre avsnitt). Den nya upplevelsen har två relaterade objekt som den vänstra sidan inte tar upp: all telemetri från fem minuter före och efter den här händelsen och användarens tidslinje.

*Jag ser fler händelser än förväntat i transaktionsdiagnostikupplevelsen när jag använder Application Insights JavaScript SDK. Finns det något sätt att se färre händelser per transaktion?*

Transaktionsdiagnostikupplevelsen visar all telemetri i en enda [åtgärd som delar](correlation.md#data-model-for-telemetry-correlation) ett [åtgärds-ID.](data-model-context.md#operation-id) Som standard skapar Application Insights SDK för JavaScript en ny åtgärd för varje unik sidvy. I en ensidesapplikation (SPA) genereras endast en sidvisningshändelse och ett enda åtgärds-ID används för all telemetri som genereras, vilket kan resultera i att många händelser korreleras med samma åtgärd. I dessa scenarier kan du använda Automatisk vägspårning för att automatiskt skapa nya åtgärder för navigering i din ensidesapp. Du måste aktivera [enableAutoRouteTracking](javascript.md#single-page-applications) så att en sidvy genereras varje gång URL-vägen uppdateras (logisk sidvy sker). Om du vill uppdatera åtgärds-ID:t manuellt kan du göra det genom att anropa `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` . Om du utlöser en PageView-händelse manuellt återställs även åtgärds-ID:t.
