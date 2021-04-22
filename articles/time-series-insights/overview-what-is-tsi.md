---
title: 'Översikt: Vad är Azure Time Series Insights Gen2? – Azure Time Series Insights Gen2-| Microsoft Docs'
description: Lär dig mer om ändringar, förbättringar och funktioner i Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 4ff26289962aa06f9638644e28da23b646554281
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865323"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Vad är Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 är en öppen och skalbar IoT-analystjänst från end-to-end med förstklassiga användarupplevelser och omfattande API:er som integrerar dess kraftfulla funktioner i ditt befintliga arbetsflöde eller program.

Du kan använda den för att samla in, bearbeta, lagra, fråga och visualisera data i IoT-skala (Sakernas Internet) – data som är mycket kontextualiserade och optimerade för tidsserier.

Azure Time Series Insights Gen2 har utformats för ad hoc-datagranskning och driftsanalys så att du kan upptäcka dolda trender, upptäcka avvikelser och utföra rotorsaksanalys. Det är ett öppet och flexibelt erbjudande som uppfyller de breda behoven i industriella IoT-distributioner.

## <a name="video"></a>Video

Läs mer om Azure Time Series Insights Gen2.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>Definition av IoT-data

Industriella IoT-data i tillgångsintensiva organisationer saknar ofta strukturell konsekvens på grund av den varierande typen av enheter och sensorer i en industrimiljö. Data från dessa strömmar kännetecknas av betydande luckor, ibland skadade meddelanden och falska avläsningar. IoT-data är ofta meningsfulla i samband med ytterligare datainmatningar som kommer från första part eller tredje källor, till exempel CRM eller ERP som lägger till kontext för arbetsflöden från början till slut. Indata från datakällor från tredje part, till exempel väderdata, kan hjälpa till att utöka telemetriströmmar i en viss installation.

Allt detta innebär att endast en bråkdel av data används i drift- och affärssyften, och analys kräver kontextualisering. Industriella data är ofta historiska för djupgående analys över längre tidsintervall för att förstå och korrelera trender. Omvandlingen av insamlad IoT-data till användbara insikter kräver:

* Databehandling för att rensa, filtrera, interpolera, transformera och förbereda data för analys.
* En struktur för att navigera och förstå data, det vill säga för att normalisera och kontextualisera data.
* Kostnadseffektiv lagring för lång eller oändlig kvarhållning av bearbetade (eller härledda) data och rådata.

Sådana data ger konsekvent, omfattande, aktuell och korrekt information för affärsanalys och rapportering.

Följande bild visar ett typiskt IoT-dataflöde.

[![IoT-dataflöde](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Azure Time Series Insights Gen2 för industriell IoT

IoT-miljön är mångskiftande med kunder som omfattar en mängd olika branschsegment, inklusive tillverkning, bil, energi, verktyg, smarta byggnader och rådgivning. På den här breda IoT-marknaden utvecklas fortfarande molnbaserade lösningar som tillhandahåller omfattande analyser riktade mot storskaliga IoT-data.

Azure Time Series Insights Gen2 åtgärdar detta behov på marknaden genom att tillhandahålla en nyckelfärdig IoT-analyslösning från end-to-end med omfattande semantisk modellering för kontextualisering av tidsseriedata, tillgångsbaserade insikter och förstklassig användarupplevelse för identifiering, trendande, avvikelseidentifiering och driftsinformation.

Du kan använda en omfattande plattform för driftsanalys i kombination med våra interaktiva datautforskningsfunktioner i Azure Time Series Insights Gen2 för att härleda mer värde från data som samlas in från IoT-tillgångar. Gen2-erbjudandet stöder:

* Lagringslösning med flera lager med stöd för varm och kall analys ger kunderna möjlighet att dirigera data mellan varm och kall för interaktiv analys över varma data samt driftsinformation över årtionden av historiska data.

  * En mycket interaktiv varm analyslösning för frekventa och stora antal frågor över kortare tidsintervalldata
  * En skalbar, prestandaoptimerad och kostnadsoptimerad tidsseriedatasjö som baseras på Azure Storage som gör det möjligt för kunder att trenda års tidsseriedata på några sekunder.

* Stöd för semantisk modell som beskriver domänen och metadata som är associerade med de härledda signalerna och rådatasignalerna från tillgångar och enheter.

* Flexibel analysplattform för att lagra historiska tidsseriedata i Azure Storage konto, vilket gör det möjligt för kunder att ha ägarskap för sina IoT-data. Data lagras i Apache Parquet-format med öppen källkod som möjliggör anslutning och interop i en mängd olika datascenarier, inklusive förutsägelseanalys, maskininlärning och andra anpassade beräkningar som görs med hjälp av välbekanta tekniker som Spark och Databricks.

* Omfattande analys med förbättrade fråge-API:er och användarupplevelse som kombinerar tillgångsbaserade datainsikter med omfattande ad hoc-dataanalys med stöd för interpolering, skalär- och aggregeringsfunktioner, kategorivariabler, punktdiagram och tidsseriesignaler för tidsväxling för djupgående analys.

* Plattform i företagsklass för att stödja skalning, prestanda, säkerhet och tillförlitlighet för våra IoT-företagskunder.

* Utökningsbarhet och integrationsstöd för analys från end-to-end. Azure Time Series Insights Gen2 är en utökningsbar analysplattform för en mängd olika datascenarier. Power BI-anslutningsappen gör det möjligt för kunder att ta de frågor som de gör i Azure Time Series Insights Gen2 direkt till Power BI för att få en enhetlig vy över sina BI- och tidsserieanalyser i ett enda fönster.

Följande diagram visar dataflödet på hög nivå.

  [![De viktigaste funktionerna](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights Gen2 ger en skalbar prismodell där du betalar per användning för databehandling, lagring (data och metadata) och frågor, så att kunderna kan justera sin användning så att den passar deras affärsbehov.

Med introduktionen av dessa viktiga industriella IoT-funktioner Azure Time Series Insights Gen2 också följande viktiga fördelar:  

| Funktion | Fördelar |
| ---| ---|
| Lagring i flera lager för tidsseriedata i IoT-skala | Med en delad databearbetningspipeline för att mata in data kan du mata in data i både varma och kalla lager. Använd varm lagring för interaktiva frågor och kalllager för lagring av stora mängder data. Mer information om hur du kan dra nytta av högpresterande tillgångsbaserade frågor finns [i frågor.](./concepts-query-overview.md) |
| Tidsseriemodell för att kontextualisera råtelemetridata och härleda tillgångsbaserade insikter | Du kan använda tidsseriemodellen för att skapa instanser, hierarkier, typer och variabler för dina tidsseriedata. Mer information om Tidsseriemodell finns i [Time Series Model](./concepts-model-overview.md).  |
| Jämn och kontinuerlig integrering med andra datalösningar | Data i Azure Time Series Insights Gen2-kalllager [lagras](./concepts-storage.md) i Apache Parquet-filer med öppen källkod. Detta möjliggör dataintegrering med andra datalösningar, första eller tredje part, för scenarier som omfattar business intelligence, avancerad maskininlärning och förutsägelseanalys. |
| Datagranskning i nära realtid | Användarupplevelsen [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md) tillhandahåller visualisering för alla data som strömmas via inmatningspipelinen. När du har anslutt en händelsekälla kan du visa, utforska och fråga efter händelsedata. På så sätt kan du verifiera om en enhet genererar data som förväntat. Du kan också övervaka en IoT-tillgångs hälsotillstånd, produktivitet och övergripande effektivitet. |
| Utökningsbarhet och integrering | Integreringen av Power BI Connector är tillgänglig direkt i Time Series Explorer-användarupplevelsen via alternativet **Exportera,** så att kunder kan exportera de tidsseriefrågor som de skapar i användarupplevelsen direkt till Power BI-skrivbordet och visa sina tidsseriediagram tillsammans med andra BI-analyser. Detta öppnar dörren till en ny typ av scenarier för industriella IoT-företag som har investerat i Power BI genom att tillhandahålla ett enda fönster över analys från olika datakällor, inklusive IoT-tidsserier. |
| Anpassade program som bygger på Azure Time Series Insights Gen2-plattformen | Azure Time Series Insights Gen2 stöder [JavaScript SDK.](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) SDK innehåller omfattande kontroller och förenklad åtkomst till frågor. Använd SDK för att skapa anpassade IoT-program ovanpå Azure Time Series Insights Gen2 som passar dina affärsbehov. Du kan också använda Azure Time Series Insights [Gen2-fråge-API:er](./concepts-query-overview.md) direkt för att driva data till anpassade IoT-program. |

## <a name="region-availability"></a>Regional tillgänglighet

Besök sidan [azures globala infrastrukturprodukter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=time-series-insights) för att se var Azure Time Series Insights är tillgänglig.

Azure Time Series Insights finns i en enda region. Dock lagras/bearbetas kunddata utanför den region som kunden distribuerar tjänstinstansen i, förutom följande regioner där data inte replikeras utanför regionen:

* Asien, östra
* Sydostasien
*   Australien, sydöstra
*   Europa, västra
*   Storbritannien, södra
*   USA, västra
*   Frankrike, centrala
*   Kanada, centrala

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Snabbstartsguide](./quickstart-explore-tsi.md)

Läs mer om användarsituationer:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2-användningsfall](./overview-use-cases.md)
