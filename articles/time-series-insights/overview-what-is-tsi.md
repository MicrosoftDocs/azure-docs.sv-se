---
title: 'Översikt: Vad är Azure Time Series Insights Gen2? -Azure Time Series Insights Gen2 | Microsoft Docs'
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
ms.openlocfilehash: ae5887657e7657e60348217c21aa0ed17f9f4b07
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606308"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Vad är Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 är en öppen och skalbar IoT Analytics-tjänst från slut punkt till slut punkt med förstklassiga användar upplevelser och omfattande API: er för att integrera de kraftfulla funktionerna i ditt befintliga arbets flöde eller program.

Du kan använda den för att samla in, bearbeta, lagra, fråga och visualisera data på Sakernas Internet (IoT)-data som är mycket sammanhangsbaserade och optimerade för tids serier.

Azure Time Series Insights Gen2 har utformats för ad hoc-dataundersökning och drift analys, så att du kan få dolda trender, upptäcka avvikelser och utföra rotor Saks analyser. Det är ett öppet och flexibelt erbjudande som uppfyller de breda behoven för industriella IoT-distributioner.

## <a name="video"></a>Video

Läs mer om Azure Time Series Insights Gen2.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>Definition av IoT-data

Industriella IoT-data i till gångs intensiva organisationer saknar ofta strukturell konsekvens på grund av den varierande typen av enheter och sensorer i en industriell inställning. Data från dessa data strömmar karakteriseras av betydande luckor och ibland skadade meddelanden och falsk läsning. IoT-data är ofta meningsfulla i samband med ytterligare data inmatning som kommer från första eller tredje källor, till exempel CRM eller ERP som lägger till kontext för slut punkt till slut punkt. Indata från tredje parts data källor som väder data kan bidra till att utöka telemetri strömmar i en specifik installation.

Allt detta innebär att endast en bråkdel av data används för operativa och affärsmässiga behov, och analys kräver contextualization. Industriella data är ofta historiska för djupgående analys över längre tid i sträck att förstå och korrelera trender. Omvandlingen av insamlad IoT-data till användbara insikter kräver:

* Databehandling för att rensa, filtrera, interpolera, transformera och förbereda data för analys.
* En struktur för att navigera genom och förstå data, det vill säga för att normalisera och sätta data.
* Kostnads effektiv lagring för lång eller oändlig kvarhållning av bearbetade (eller härledda) data och rå data.

Sådana data ger konsekvent, omfattande, aktuell och korrekt information för företags analys och rapportering.

Följande bild visar ett typiskt IoT-dataflöde.

[![IoT-dataflöde](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Azure Time Series Insights Gen2 för industriella IoT

IoT-landskapet skiljer sig mellan kunder som sträcker sig över olika bransch segment, inklusive tillverkning, bil, energi, verktyg, smarta byggnader och konsult tjänster. I den här breda serien av industriella IoT-marknader utvecklas molnbaserade lösningar som tillhandahåller omfattande analyser riktade till storskaliga IoT-data i stor skala.

Azure Time Series Insights Gen2 de här marknadens behov genom att tillhandahålla en nyckel färdig lösning för IoT Analytics med omfattande semantisk modellering för contextualization av tids serie data, till gångs-baserade insikter och förstklassig användar upplevelse för identifiering, Trends, avvikelse identifiering och drifts information.

En intensiv drifts analys plattform tillsammans med våra interaktiva data utforsknings funktioner kan du använda Azure Time Series Insights Gen2 för att härleda mer värde av data som samlas in från IoT-tillgångar. Gen2-erbjudandet stöder:

* Lagrings lösning med extra lager med varm och kall analys som ger kunderna möjlighet att dirigera data mellan varma och kalla för interaktiva analyser över varmt data och drifts information över årtionden av historiska data.

  * En mycket interaktiv lösning för varm lösning för att utföra frekventa och många frågor över kortare tids periods data
  * En skalbar, genomförd och kostnads optimerad tids serie data Lake baserat på Azure Storage som gör det möjligt för kunder att trenda års tids serie data på några sekunder.

* Stöd för semantisk modell som beskriver den domän och de metadata som är kopplade till de härledda och råa signalerna från till gångar och enheter.

* Flexibel analys plattform för att lagra historiska tids serie data i kundägda Azure Storage-konto, så att kunderna kan ha ägande rätt till sina IoT-data. Data lagras i Apache Parquet-format med öppen källkod som möjliggör anslutningar och interop i flera olika data scenarier, inklusive förutsägelse analyser, maskin inlärning och andra anpassade beräkningar som gjorts med hjälp av den välkända tekniken, inklusive Spark och Databricks.

* Omfattande analyser med förbättrade API: er för frågor och användar upplevelse som kombinerar till gångs data insikter med omfattande ad hoc-dataanalys med stöd för interpolation, skalära funktioner och mängd funktioner, kategoriska-variabler, punkt diagram och tids växlings tids serie signaler för djupgående analys.

* Plattform för företags klass som stöder skalnings-, prestanda-, säkerhets-och Tillförlitlighets behoven hos våra Enterprise IoT-kunder.

* Utöknings-och integrations support för slut punkt till slut punkts analys. Azure Time Series Insights Gen2 tillhandahåller en utöknings bar analys plattform för flera olika data scenarier. Power BI Connector gör det möjligt för kunderna att ta de frågor de gör i Azure Time Series Insights Gen2 direkt till Power BI för att få en enhetlig vy över sin BI-och Time Series-analys i ett enda fönster i glaset.

I följande diagram visas data flödet på hög nivå.

  [![De viktigaste funktionerna](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights Gen2 ger en skalbar pris modell enligt principen betala per användning för data bearbetning, lagring (data och metadata) och fråga, vilket gör det möjligt för kunderna att justera sin användning så att de passar deras affärs behov.

Med introduktionen av dessa viktiga funktioner för industriella IoT kan Azure Time Series Insights Gen2 också tillhandahålla följande viktiga fördelar:  

| Funktion | Fördelar |
| ---| ---|
| Lagring med flera lager för IoT-Scale Time Series-data | Med en data behandlings pipeline för delade data för att mata in data kan du mata in data i både varma och kalla butiker. Använd varmt Store för interaktiva frågor och kall lagring för lagring av stora mängder data. Om du vill veta mer om hur du kan dra nytta av högpresterande till gångs frågor, se [frågor](./concepts-query-overview.md). |
| Tidsseriemodell för att kontextualisera råtelemetridata och härleda tillgångsbaserade insikter | Du kan använda Time Series-modellen för att skapa instanser, hierarkier, typer och variabler för dina Time Series-data. Mer information om tids serie modellen finns i [tids serie modell](./concepts-model-overview.md).  |
| Jämn och kontinuerlig integrering med andra datalösningar | Data i Azure Time Series Insights Gen2 kall lagring [lagras](./concepts-storage.md) i Apache Parquet-filer med öppen källkod. Detta möjliggör data integrering med andra data lösningar, 1st eller tredje part, för scenarier som omfattar Business Intelligence, avancerad maskin inlärning och förutsägelse analys. |
| Datagranskning i nära realtid | Användar upplevelsen för [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md) tillhandahåller visualisering för all data strömning genom inmatnings pipelinen. När du har anslutit en händelse källa kan du Visa, utforska och fråga händelse data. På så sätt kan du verifiera om en enhet genererar data som förväntat. Du kan också övervaka en IoT-tillgångs hälsotillstånd, produktivitet och övergripande effektivitet. |
| Utökning och integrering | Integreringen med Power BI-anslutaren är tillgänglig direkt i Time Series Explorer-användar upplevelsen via **export** alternativet, så att kunderna kan exportera tids serie frågorna som de skapar i vår användar upplevelse direkt till Power BI Desktop och visa sina tids serie diagram tillsammans med andra bi-analyser. Detta öppnar dörren till en ny klass med scenarier för industriella IoT-företag som har investerat i Power BI genom att tillhandahålla ett enda fönster för analys från olika data källor, inklusive IoT Time Series. |
| Anpassade program som bygger på Azure Time Series Insights Gen2-plattformen | Azure Time Series Insights Gen2 stöder [Java Script SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). SDK innehåller omfattande kontroller och förenklad åtkomst till frågor. Använd SDK: n för att bygga anpassade IoT-program ovanpå Azure Time Series Insights Gen2 som passar dina affärs behov. Du kan också använda [API: erna](./concepts-query-overview.md) för Azure Time Series Insights Gen2-frågor direkt för att köra data i anpassade IoT-program. |

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Snabbstartsguide](./quickstart-explore-tsi.md)

Läs mer om användarsituationer:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 användnings fall](./overview-use-cases.md)
