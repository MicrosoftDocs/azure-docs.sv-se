---
title: Gen2-användnings fall – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om Azure Time Series Insights Gen2 användnings fall.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7d97958c5fd1274495da88c064b63e59e354f691
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606971"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure Time Series Insights Gen2 användnings fall

I den här artikeln sammanfattas flera vanliga användnings fall för Azure Time Series Insights Gen2. Rekommendationerna i den här artikeln fungerar som en utgångs punkt för att utveckla dina program och lösningar med Azure Time Series Insights Gen2.

Mer specifikt besvarar den här artikeln följande frågor:

* Vilka är de vanligaste användnings fallen för Azure Time Series Insights Gen2?
* Vilka är fördelarna med att använda Azure Time Series Insights Gen2 för [data utforskning och visuell avvikelse identifiering](#data-exploration-and-visual-anomaly-detection)?
* Vilka är fördelarna med att använda Azure Time Series Insights Gen2 för [drift analys och process effektivitet](#operational-analysis-and-driving-process-efficiency)?
* Vilka är fördelarna med att använda Azure Time Series Insights Gen2 för [Avancerad analys](#advanced-analytics)?

En översikt över dessa användnings scenarier beskrivs i följande avsnitt.

## <a name="introduction"></a>Introduktion

Azure Time Series Insights Gen2 är ett slut punkt till slut punkt, plattform-som-tjänst-erbjudande. Den används för att samla in, bearbeta, lagra, analysera och fråga mycket sammanhangsbaserade, tidsbaserad, tidsbaserad och optimerad IoT-Scale-data. Den är idealisk för ad hoc-dataundersökning och drift analys. Azure Time Series Insights Gen2 är ett unikt utöknings Bart, anpassat tjänst erbjudande som uppfyller de breda behoven i industriella IoT-distributioner.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Data utforskning och visuell avvikelse identifiering

Utforska och analysera miljard tals händelser för att upptäcka avvikelser och identifiera dolda trender i dina data. Azure Time Series Insights Gen2 ger nästan real tids prestanda för dina IoT-och DevOps analys-arbetsbelastningar.

[![Data Utforskaren](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

De flesta kunder samtycker till att den minsta tiden som krävs för att få insikter är en av främsta-funktionerna i Azure Time Series Insights Gen2:

* Azure Time Series Insights Gen2 kräver ingen förberedelse av första data.
* Det fungerar snabbt för att ansluta dig till miljarder händelser i Azure IoT Hub eller Azure Event Hubs-instanser på några minuter.
* När du är ansluten kan du visualisera och analysera miljarder händelser för att upptäcka avvikelser och identifiera dolda trender i dina data.

Azure Time Series Insights Gen2 är intuitivt och enkelt att använda. Du kan interagera med dina data utan att behöva skriva en enda rad kod. Det finns inget nytt språk som du måste lära dig, men Azure Time Series Insights Gen2 tillhandahåller ett detaljerat textbaserat frågespråk för avancerade användare som är bekanta med SQL. Du kan också välja att välja och klicka på utforskning för nybörjare.

Kunder kan dra nytta av hastigheten för att snabbt diagnostisera problem med till gångs problem. De kan utföra DevOps analys för att komma till rotor saken till en bugg i en IoT-lösning. De kan också identifiera områden för att flagga för ytterligare undersökning som en del av deras initiativ för data vetenskap.

Det finns tre huvudsakliga sätt att interagera med data som lagras i Azure Time Series Insights Gen2:

* Det första och enklaste sättet att komma igång med Azure Time Series Insights Gen2 Explorer. Du kan använda den för att snabbt visualisera alla dina IoT-data på ett och samma ställe. Den innehåller verktyg som värme kartan för att hjälpa dig att upptäcka avvikelser i dina data. Den innehåller också en perspektivvy. Använd den för att jämföra upp till fyra vyer från en eller flera Azure Time Series Insights Gen2-miljöer på en enda instrument panel. På instrument panelen får du en översikt över dina tids serie data över alla dina platser. Läs mer om [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md). Läs [Azure Time Series Insights Gen2-planering](./how-to-plan-your-environment.md)för att planera ut din miljö.

* Det andra sättet att starta är att använda JavaScript SDK för att snabbt bädda in kraftfulla diagram och grafer i ditt webb program. Med bara några rader med kod kan du skapa kraftfulla frågor. Använd dem för att fylla i linje diagram, cirkel diagram, liggande diagram, värme kartor, data rutnät med mera. Alla dessa element finns kvar i rutan med hjälp av SDK. SDK sammanfattar också Azure Time Series Insights Gen2 fråge-API: er. Du kan använda dem för att skapa SQL-liknande predikat för att fråga de data som du vill visa på en instrument panel. För Hybrid lösningar för presentations lager erbjuder Azure Time Series Insights Gen2 URL: er. De ger sömlösa anslutnings punkter med Azure Time Series Insights Gen2 Explorer för djup dykningar i data.

  * Läs om [JS-klient biblioteket](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) och [exempel klient](https://github.com/Microsoft/tsiclient) dokumentationen om du vill veta mer om Java Script SDK.

  * Lär dig mer om delnings-URL: er och det nya användar gränssnittet genom att granska [visualisera data i Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

* Det tredje sättet att starta är att använda de kraftfulla API: erna för att fråga data som lagras i Azure Time Series Insights Gen2. Azure Time Series Insights Gen2 har temporala operatörer som `from` , `to` , `first` och `last` . Det innehåller agg regeringar och transformeringar som,,,,, `average` `sum` `min` `max` `time-weighted average` `time-weighted sum` osv. Den tillåter också filtrering, aritmetiska och booleska operatorer, skalära funktioner osv. Alla dessa operatörer gör det möjligt för underordnade program att snabbt hitta intressanta trender och mönster i dina data. Använd dem för att fylla i Homegrown-visualiseringar för att upptäcka avvikelser.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Drift analys och process effektivitet

Använd Azure Time Series Insights Gen2 för att övervaka hälsa, användning och prestanda för utrustning i skala och mäta operationell effektivitet. Azure Time Series Insights Gen2 hjälper till att hantera olika och oförutsägbara IoT-arbetsbelastningar utan att offra inmatnings-eller frågans prestanda.

[![Skärm bild som visar I o T enheter/program data, strömnings bearbetning, operationell effektivitet, intelligens/insikter och avancerad analys i Azure Time Series Insights Gen2.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Strömning och kontinuerlig bearbetning av data som kommer från operativa processer kan omvandlas till alla företag om den kombineras med rätt teknik eller lösning. Dessa lösningar är ofta en kombination av flera system. De möjliggör utforskning och analys av data som ändras konstant, särskilt i IoT-sfären, och som delar ett gemensamt mönster.

Dessa mönster börjar ofta med IoT-aktiverade plattformar som matar in miljarder händelser från enheter och sensorer som sträcker sig över olika språk. Dessa system bearbetar och analyserar strömmande data för att få insikter och åtgärder i real tid. Data arkiveras vanligt vis i varmt och kall lagrings utrymme för nära real tids-och batch-analyser.

Data som samlas in går igenom en serie bearbetning för att rengöra och sätta den för efterföljande frågor och analys scenarier. Azure erbjuder omfattande tjänster som kan användas i IoT-scenarier som till exempel underhåll och tillverkning. Dessa tjänster omfattar Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning och Power BI.

Lösnings arkitektur kan uppnås på följande sätt:

* Mata in data via IoT Hub eller Event Hubs för förstklassig säkerhet, data flöde och svars tid.
* Utför data bearbetning och beräkningar. Tratt-inmatade data via tjänster som Stream Analytics, Logic Apps och Azure Functions. Vilken tjänst du använder beror på de olika data bearbetnings behoven.
* Beräknade signaler från bearbetnings pipelinen skickas till Azure Time Series Insights Gen2 för lagring och analys.

Azure Time Series Insights Gen2 erbjuder nära data utforskning i real tid och till gångs rika insikter över historiska data. Beroende på dina affärs behov kan MapReduce och Hive-jobb köras på data som lagras i Azure Time Series Insights Gen2 genom att ansluta Azure Time Series Insights Gen2 till Azure HDInsight. Data som lagras i Azure Time Series Insights Gen2 är tillgängliga för Power BI och andra kund program via Azure Time Series Insights Gen2 API för frågor på offentlig yta. Dessa data kan användas för djup affärs-och användnings informations scenarier.

## <a name="advanced-analytics"></a>Avancerad analys

Integrera med avancerade analys tjänster som Machine Learning och Azure Databricks. Azure Time Series Insights Gen2 ingress rå data från miljon tals enheter. Den lägger till sammanhangsbaserade data som kan förbrukas sömlöst av en serie Azure Analytics-tjänster.

[![Analys](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Avancerad analys och maskin inlärning förbrukar och bearbetar stora mängder data. Dessa data används för att fatta data drivna beslut och utföra förutsägelse analyser. I IoT-användningsfall lär sig avancerade analys algoritmer från data som samlas in från miljon tals enheter. Dessa enheter överför data flera gånger varje sekund. Data som samlas in från IoT-enheter är RAW. Den saknar sammanhangsbaserad information, till exempel enhetens plats och enhetens enhets läsning. Det innebär att rå data är svåra att förbruka direkt för avancerad analys.

Azure Time Series Insights Gen2 broar klyftan mellan IoT-data och avancerad analys på två enkla och kostnads effektiva sätt:

* Först Azure Time Series Insights Gen2 samlar in rå telemetridata från miljon tals enheter med IoT Hub. Den berikar data med sammanhangsbaserad information och transformerar data i ett Parquet-format. Det här formatet kan enkelt integreras med andra avancerade analys tjänster, till exempel Machine Learning, Azure Databricks och program från tredje part.

    Azure Time Series Insights Gen2 kan fungera som källan till sanningen för alla data i en organisation. Den skapar en central lagrings plats för efterföljande analys arbets belastningar att använda. Eftersom Azure Time Series Insights Gen2 är en nära lagrings tjänst i real tid, kan avancerade analys modeller lära sig kontinuerligt från inkommande IoT-telemetridata. Därför kan modeller göra mer exakta förutsägelser.

* För det andra kan resultatet av maskin inlärnings-och förutsägelse modeller matas in i Azure Time Series Insights Gen2 för att visualisera och lagra sina resultat. Den här proceduren hjälper organisationer att optimera och justera sina modeller. Azure Time Series Insights Gen2 gör det enkelt att visualisera telemetridata för data strömmar på samma plan som de tränade modell utmatningarna. På så sätt hjälper det data vetenskaps team att upptäcka avvikelser och identifiera mönster.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).
* Läs [Azure Time Series Insights Gen2 metod tips](./how-to-plan-your-environment.md) för att planera för din miljö.
* Läs dokumentationen om [exempel klienten](https://github.com/Microsoft/tsiclient) .
