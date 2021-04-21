---
title: Azure Data Explorer Insights (FÖRHANDSVERSION AV ADX Insights)| Microsoft Docs
description: I den här artikeln Azure Data Explorer Insights (ADX Insights)
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: a8aae2dc03ba87e9782cdf3952be1bfc4a1aae75
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767049"
---
# <a name="azure-data-explorer-insights-preview"></a>Azure Data Explorer Insights (förhandsversion)

Azure Data Explorer Insights (förhandsversion) ger omfattande övervakning av dina kluster genom att leverera en enhetlig vy över klustrets prestanda, åtgärder, användning och fel.
Den här artikeln hjälper dig att förstå hur du kan registrera och använda Azure Data Explorer Insights (förhandsversion).

## <a name="introduction-to-azure-data-explorer-insights-preview"></a>Introduktion till Azure Data Explorer Insights (förhandsversion)

Innan du går in på upplevelsen bör du förstå hur den visar och visualiserar information.
-    **Skalningsperspektiv** som visar en ögonblicksbild av dina klusters primära mått för att enkelt spåra prestanda för frågor, inmatning och exportåtgärder.
-   **Öka detaljgranskningsanalysen** för ett Azure Data Explorer kluster för att utföra detaljerad analys.
-    **Anpassningsbar där** du kan ändra vilka mått du vill se, ändra eller ange tröskelvärden som överensstämmer med dina gränser och spara dina egna anpassade arbetsböcker. Diagram i arbetsboken kan fästas på Azure-instrumentpaneler.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Visa från Azure Monitor (skalningsperspektiv)

Från Azure Monitor kan du visa de viktigaste prestandamåtten för klustret, inklusive mått för frågor, inmatning och exportåtgärder från flera kluster i din prenumeration och identifiera prestandaproblem.

Utför följande steg för att visa prestanda för dina kluster i alla dina prenumerationer:

1. Logga in på [Azure Portal](https://portal.azure.com/)

2. Välj **Övervaka** i det vänstra fönstret i Azure Portal och under avsnittet Insights Hub väljer du **Azure Data Explorer kluster (förhandsversion)**.

![Skärmbild av översiktsupplevelse med flera grafer](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Fliken Översikt

På fliken **Översikt** för den valda prenumerationen visar tabellen interaktiva mått för Azure Data Explorer grupperade i prenumerationen. Du kan filtrera resultaten baserat på de alternativ som du väljer i följande listlistor:

* Prenumerationer – endast prenumerationer som har Azure Data Explorer-kluster visas.

* Azure Data Explorer kluster – som standard är endast upp till fem kluster förvalda. Om du väljer alla eller flera kluster i omfångsväljaren returneras upp till 200 kluster.

* Tidsintervall – visar som standard de senaste 24 timmarna med information baserat på motsvarande val som gjorts.

Räknarpanelen, under listrutan, rullar upp det totala antalet Azure Data Explorer i de valda prenumerationerna och visar hur många som har valts. Det finns villkorliga färgkodningar för kolumnerna: Keep alive, CPU, Ingestion Utilization och Cache Utilization. Orangekodade celler har värden som inte är hållbara för klustret. 

För att bättre förstå vad vart och ett av måtten representerar rekommenderar vi att du läser igenom [dokumentationen om Azure Data Explorer mått](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Fliken Frågeprestanda

Den här fliken visar frågevaraktighet, totalt antal samtidiga frågor och det totala antalet begränsade frågor.

![Skärmbild av fliken frågeprestanda](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Fliken Datainmatningsprestanda

Den här fliken visar inmatningssvarstid, resultat av lyckades inmatning, misslyckade inmatningsresultat, inmatningsvolym och händelser som bearbetas för Event/IoT Hubs.

[![Skärmbild av fliken för datainmatningsprestanda](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Fliken Strömningsinmatningsprestanda

Den här fliken innehåller information om genomsnittlig datahastighet, genomsnittlig varaktighet och begärandefrekvens.

### <a name="export-performance-tab"></a>Fliken Exportera prestanda

Den här fliken innehåller information om exporterade poster, lateness, väntande antal och användningsprocent för löpande exportåtgärder.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Visa från en Azure Data Explorer klusterresurs (detaljgranskningsanalys)

Så här får Azure Data Explorer Insights direkt från ett Azure Data Explorer kluster:

1. I Azure Portal väljer du **Azure Data Explorer kluster**.

2. I listan väljer du ett Azure Data Explorer kluster. I övervakningsavsnittet väljer du **Insights (förhandsversion)**.

De här vyerna är också tillgängliga genom att välja resursnamnet för ett Azure Data Explorer kluster från Azure Monitor insights-vyn.

Azure Data Explorer Insights kombinerar både loggar och mått för att tillhandahålla en global övervakningslösning. Inkludering av loggbaserade visualiseringar kräver att användarna aktiverar diagnostisk loggning av sina Azure Data Explorer och skickar dem till [en Log Analytics-arbetsyta.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) De diagnostikloggar som ska aktiveras är: **Kommando,** **Fråga,** **TableDetails** och **TableUsageStatistics**.

![Skärmbild av den blå knappen som visar texten "Aktivera loggar för övervakning"](./media/data-explorer/enable-logs.png)


 Fliken **Översikt** visar:

- Måttpaneler som visar tillgänglighet och övergripande status för klustret för att snabbt utvärdera dess hälsa.

- En sammanfattning av aktiva [Advisor-rekommendationer](/azure/data-explorer/azure-advisor) och [resurshälsostatus.](/azure/data-explorer/monitor-with-resource-health)

- Diagram som visar de främsta processor- och minneskonsumenterna och antalet unika användare över tid.


[![Skärmbild av vy från en Azure Data Explorer klusterresurs](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

Fliken **Nyckelmått** visar en enhetlig vy över några av klustrets mått, grupperade efter: allmänna mått, frågerelaterade, inmatningsrelaterade och strömmande inmatningsrelaterade mått.

[![Skärmbild av vyn fel](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

På **fliken** Användning kan användarna fördjupa sig i prestandan för klustrets kommandon och frågor. På den här sidan kan du:
 
 - Se vilka arbetsbelastningsgrupper, användare och program som skickar flest frågor eller förbrukar mest processor- och minne (så att du kan förstå vilka arbetsbelastningar som skickar de tämst frågor som klustret ska bearbeta).
 - Identifiera de främsta arbetsbelastningsgrupperna, användarna och programmen efter misslyckade frågor.
 - Identifiera de senaste ändringarna i antalet frågor, jämfört med det historiska dagliga genomsnittet (under de senaste 16 dagarna) efter arbetsbelastningsgrupp, användare och program.
 - Identifiera trender och toppar i antalet frågor, minne och CPU-förbrukning efter arbetsbelastningsgrupp, användare, program och kommandotyp.

[![Skärmbild av driftvyn med ringdiagram i programmets översta program efter antal kommandon och frågor, de främsta huvudnamnen efter kommando och antal frågor samt de vanligaste kommandona efter kommandotyper](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Skärmbild av driftvyn med linjediagram över antal frågor efter program, totalt minne efter program och total CPU per program](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

Fliken **Tabeller** visar de senaste och historiska egenskaperna för tabeller i klustret. Du kan se vilka tabeller som förbrukar mest utrymme, spåra tillväxthistorik efter tabellstorlek, heta data och antalet rader över tid.

På **cachefliken** kan användarna analysera sina faktiska frågemönster och jämföra dem med den konfigurerade cacheprincipen (för varje tabell). Du kan identifiera tabeller som används av de flesta frågor och tabeller som inte efterfrågas alls och anpassa cacheprincipen därefter. Du kan få specifika rekommendationer för cacheprinciper för specifika tabeller i Azure Advisor (för närvarande är cacherekommendationer endast tillgängliga från huvudinstrumentpanelen [för Azure Advisor),](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)baserat på faktiska frågors utseende tillbaka under de senaste 30 dagarna och en icke-optimerad cacheprincip för minst 95 % av frågorna. Rekommendationerna för cacheminskning i Azure Advisor är tillgängliga för kluster som är "avgränsade av data" (vilket innebär att klustret har låg CPU-användning och låg inmatningsanvändning, men på grund av hög datakapacitet kunde klustret inte skalas in eller ned).

[![Skärmbild av cacheinformation](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

Fliken **klustergränser** visar klustergränserna baserat på din användning. På den här fliken kan du granska processor-, inmatnings- och cacheanvändningen. De här måtten poängeras som "Låg", "Medel" eller "Hög". De här måtten och poängen är viktiga när du bestämmer det optimala antalet SKU:er och instanser för klustret, och de beaktas i Azure Advisor-SKU/storleksrekommendation. På den här fliken kan du välja en måttpanel och djupdykning för att förstå dess trend och hur dess poäng bestäms. Du kan också visa Azure Advisor SKU/storleksrekommendation för klustret. I följande bild kan du till exempel se att alla mått poängeras som "Låg" och därmed får klustret en kostnadsrekommendation som gör att det kan skala in/ned och spara kostnader.

> [!div class="mx-imgBorder"]
> [![Skärmbild av klustergränser.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Fäst på Azure-instrumentpanelen

Du kan fästa något av måttavsnitten (i skalningsperspektivet) på en Azure-instrumentpanel genom att välja kartnålsikonen längst upp till höger i avsnittet.

![Skärmbild av fästikonen markerad](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Anpassa Azure Data Explorer Insights

Det här avsnittet visar vanliga scenarier för att redigera arbetsboken för att anpassa efter dina dataanalysbehov:
* Begränsa arbetsboken till att alltid välja en viss prenumeration Azure Data Explorer kluster
* Ändra mått i rutnätet
* Ändra tröskelvärden eller färgåtergivning/kodning

Du kan börja anpassningar genom att aktivera redigeringsläget genom att välja **knappen Anpassa** i det översta verktygsfältet.

![Skärmbild av knappen Anpassa](./media/data-explorer/customize.png)

Anpassningar sparas i en anpassad arbetsbok för att förhindra att standardkonfigurationen i vår publicerade arbetsbok skrivs över. Arbetsböcker sparas i en resursgrupp, antingen i avsnittet Mina rapporter som är privat för dig eller i avsnittet Delade rapporter som är tillgängligt för alla med åtkomst till resursgruppen. När du har sparat den anpassade arbetsboken måste du gå till arbetsboksgalleriet för att starta den.

![Skärmbild av arbetsboksgalleriet](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Felsökning

Allmänna felsökningsvägledning finns i felsökningsartikeln om dedikerade [arbetsboksbaserade insikter.](troubleshoot-workbooks.md)

Det här avsnittet hjälper dig med diagnostisering och felsökning av några av de vanliga problem som kan uppstå när du använder Azure Data Explorer Insights (förhandsversion). Använd listan nedan för att hitta den information som är relevant för ditt specifika problem.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Varför visas inte alla mina prenumerationer i prenumerationsväljaren?

Vi visar endast prenumerationer som innehåller Azure Data Explorer kluster, som valts från det valda prenumerationsfiltret, som väljs i "Katalog + prenumeration" i Azure Portal sidhuvud.

![Skärmbild av prenumerationsfilter](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Varför visas inga data för mitt Azure Data Explorer under avsnitten Användning, Tabeller eller Cache?

Om du vill visa dina loggbaserade [](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) data måste du aktivera diagnostikloggar för varje Azure Data Explorer kluster som du vill övervaka. Detta kan göras under diagnostikinställningarna för varje kluster. Du måste skicka dina data till en Log Analytics-arbetsyta. De diagnostikloggar som ska aktiveras är: Kommando, Fråga, TableDetails och TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Jag har redan aktiverat loggar för mitt Azure Data Explorer kluster. Varför kan jag fortfarande inte se mina data under Kommandon och frågor?

För närvarande fungerar inte diagnostikloggar retroaktivt, så data börjar bara visas när det har vidtagits åtgärder mot din Azure Data Explorer. Det kan därför ta lite tid, från timmar till dag, beroende på hur aktivt Azure Data Explorer klustret är.


## <a name="next-steps"></a>Nästa steg

Lär dig de scenarier som arbetsböcker är utformade för att stödja, hur du skapar nya och anpassar befintliga rapporter med mera genom att granska Skapa interaktiva rapporter [med Azure Monitor arbetsböcker](../visualize/workbooks-overview.md).
