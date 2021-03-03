---
title: Visualisera data från Azure Monitor | Microsoft Docs
description: Innehåller en översikt över tillgängliga metoder för att visualisera mått-och loggdata som lagras i Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2021
ms.openlocfilehash: 29164bbb205e9f3ad59ac28200b5c6cd1e47bf39
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707582"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualisera data från Azure Monitor
Den här artikeln innehåller en översikt över tillgängliga metoder för att visualisera logg-och mått data som lagras i Azure Monitor.

Visualiseringar som diagram och diagram kan hjälpa dig att analysera dina övervaknings data och gå ned på problem och identifiera mönster. Beroende på vilket verktyg du använder kan du också välja att dela visualiseringar med andra användare i och utanför organisationen.

## <a name="workbooks"></a>Arbetsböcker
[Arbets böcker](./visualize/workbooks-overview.md) är interaktiva dokument som ger djupgående insikter om dina data, undersökningar och samarbete i teamet. Vissa exempel där arbets böcker är användbara är fel söknings guider och incident Postmortem.

![Diagrammet visar skärm dum par av flera sidor från en arbets bok, inklusive analys av sid visningar, användning och tid som lagts på sidan.](media/visualizations/workbook.png)

### <a name="advantages"></a>Fördelar
- Stöder både mått och loggar.
- Stöder parametrar som aktiverar interaktiva rapporter där du kan välja ett element i en tabell dynamiskt uppdaterar associerade diagram och visualiseringar.
- Dokument som påminner om flödet.
- Alternativ för personliga eller delade arbets böcker.
- Enkel, användarvänlig redigerings upplevelse.
- Mallar har stöd för offentlig GitHub mall Galleri.

### <a name="limitations"></a>Begränsningar
- Ingen automatisk uppdatering.
- Ingen kompakt layout, t. ex. instrument paneler, som gör arbets böcker mindre användbara som ett enda fönster i glaset. Avsett för att ge djupare insikter.


## <a name="azure-dashboards"></a>Azure-instrumentpaneler
[Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md) är den primära instrument panels tekniken för Azure. De är särskilt användbara när man tillhandahåller en enda panel med glas över din Azure-infrastruktur och tjänster så att du snabbt kan identifiera viktiga problem.

![Skärm bild som visar ett exempel på en Azure-instrumentpanel med anpassningsbar information.](media/visualizations/dashboard.png)

Här är en video genom gång av hur du skapar instrument paneler.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

### <a name="advantages"></a>Fördelar
- Djupgående integrering i Azure. Visualiseringar kan fästas på instrument paneler från flera Azure-sidor, inklusive [Metrics Explorer](essentials/metrics-charts.md), [Log Analytics](logs/log-analytics-overview.md)och [Application Insights](app/app-insights-overview.md).
- Stöder både mått och loggar.
- Kombinera data från flera källor, inklusive utdata från [Metrics Explorer](essentials/metrics-charts.md), [logg frågor](logs/log-query-overview.md)och [kartor](app/app-map.md) och tillgänglighet i [Application Insights](app/app-insights-overview.md).
- Alternativ för personliga eller delade instrument paneler. Integrerad med [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md).
- Automatisk uppdatering. Mått uppdateringen är beroende av tidsintervallet med minst fem minuter. Loggar uppdateras varje timme med ett manuellt uppdaterings alternativ på begäran genom att klicka på ikonen "uppdatera" i en specifik visualisering, eller genom att uppdatera hela instrument panelen.
- Parametrized Mät instrument paneler med tidsstämpel och anpassade parametrar.
- Alternativ för flexibel layout.
- Hel skärms läge.


### <a name="limitations"></a>Begränsningar
- Begränsad kontroll över logg visualiseringar utan stöd för data tabeller. Det totala antalet data serier är begränsat till 50 med ytterligare data serier grupperade under en _annan_ Bucket.
- Inga anpassade parameter stöd för logg diagram.
- Logg diagram är begränsade till de senaste 30 dagarna.
- Det går bara att fästa logg diagram på delade instrument paneler.
- Ingen interaktivitet med instrument panels data.
- Begränsad kontext granskning – nedåt.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är särskilt användbart för att skapa affärsinriktade instrument paneler och rapporter, samt rapporter som analyserar långsiktiga KPI-trender. Du kan [Importera resultatet av en logg fråga](visualize/powerbi.md) till en Power BI data uppsättning så att du kan dra nytta av dess funktioner, till exempel att kombinera data från olika källor och dela rapporter på webben och mobila enheter.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar.
- Omfattande interaktivitet, inklusive inzoomning och kors filtrering.
- Lätt att dela hela organisationen.
- Integrering med andra data från flera data källor.
- Bättre prestanda med resultat som cachelagrats i en kub.


### <a name="limitations"></a>Begränsningar
- Stöder loggar men inte mått.
- Ingen Azure-integrering. Det går inte att hantera instrument paneler och modeller via Azure Resource Manager.
- Frågeresultatet måste importeras till Power BI-modellen för att konfigurera. Begränsning av resultat storlek och uppdatering.
- Begränsad data uppdatering på åtta gånger per dag.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) är en öppen plattform som perfekt på drift instrument paneler. Det är särskilt användbart för att identifiera och isolera och sorterar drift incidenter. Du kan lägga till [Grafana Azure Monitor data källans plugin-program](visualize/grafana-plugin.md) i din Azure-prenumeration för att visualisera dina Azure Metric-data.

![Skärm bild som visar Grafana-visualiseringar.](media/visualizations/grafana.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar.
- Omfattande eko system för data källor.
- Data interaktivitet inklusive zooma in.
- Stöder parametrar.

### <a name="limitations"></a>Begränsningar
- Ingen Azure-integrering. Det går inte att hantera instrument paneler och modeller via Azure Resource Manager.
- Kostnad för att stödja ytterligare Grafana-infrastruktur eller ytterligare kostnad för Grafana-molnet.


## <a name="build-your-own-custom-application"></a>Bygg ditt eget anpassade program
Du kan komma åt data i logg-och mät data i Azure Monitor via deras API med hjälp av en REST-klient, vilket gör att du kan bygga egna anpassade webbplatser och program.

### <a name="advantages"></a>Fördelar
- Fullständig flexibilitet i användar gränssnitt, visualisering, interaktivitet och funktioner.
- Kombinera mått och loggdata med andra data källor.

### <a name="disadvantages"></a>Nackdelar
- Betydande tekniska ansträngningar krävs.


## <a name="azure-monitor-views"></a>Azure Monitor vyer

> [!IMPORTANT]
> Vyer håller på att föråldras. Mer information om hur du konverterar vyer till arbets böcker finns i [Azure Monitor Visa designer till arbets böcker över gångs guide](visualize/view-designer-conversion-overview.md) .

Med [vyer i Azure Monitor](visualize/view-designer.md) kan du skapa anpassade visualiseringar med loggdata. De används av [övervaknings lösningar](insights/solutions.md) för att presentera de data som de samlar in.


![Skärm bild som visar en panel för övervakning av behållare och den detaljerade Azure Monitor vy som öppnas när du väljer den.](media/visualizations/view.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar för loggdata.
- Exportera och importera vyer för att överföra dem till andra resurs grupper och prenumerationer.
- Integrerar i Azure Monitor hanterings modell med arbets ytor och övervaknings lösningar.
- [Filter](visualize/view-designer-filters.md) för anpassade parametrar.
- Interaktiva, stöder flernivå granskning (Visa som visar mer information om en annan vy)

### <a name="limitations"></a>Begränsningar
- Stöder loggar men inte mått.
- Inga personliga vyer. Tillgänglig för alla användare med åtkomst till arbets ytan.
- Ingen automatisk uppdatering.
- Alternativ för begränsad layout.
- Inget stöd för att fråga över flera arbets ytor eller Application Insights program.
- Frågor är begränsade till svars storlek på 8 MB och frågekörningen på 110 sekunder.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [data som samlas in av Azure Monitor](data-platform.md).
- Lär dig om [Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md).
- Läs mer om [Metrics Explorer](essentials/metrics-getting-started.md)
- Lär dig mer om [arbets böcker](./visualize/workbooks-overview.md).
- Lär dig mer om att [Importera loggdata till Power BI](./visualize/powerbi.md).
- Lär dig mer om [Grafana-Azure Monitor data källans plugin-program](./visualize/grafana-plugin.md).
- Lär dig mer om [vyer i Azure Monitor](visualize/view-designer.md).

