---
title: Azure Monitor för Azure Datautforskaren (förhands granskning) | Microsoft Docs
description: I den här artikeln beskrivs Azure Monitor insikter för Azure Datautforskaren-kluster.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: dcfe12b30e336863c8e112d9ad675a2f57fe48f4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179144"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure Monitor för Azure Datautforskaren (för hands version)

Azure Monitor för Azure Datautforskaren (för hands version) tillhandahåller omfattande övervakning av dina kluster genom att leverera en enhetlig vy över kluster prestanda, åtgärder, användning och fel.
Den här artikeln hjälper dig att förstå hur du kan publicera och använda Azure Monitor för Azure Datautforskaren (för hands version).

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Introduktion till Azure Monitor för Azure Datautforskaren (för hands version)

Innan du hoppar till erfarenheten bör du förstå hur den visar och visualiserar information.
-    **I skalnings perspektiv** visas en ögonblicks bild av dina klusters primära mått för att enkelt spåra prestanda för frågor, inmatning och export åtgärder.
-   **Detalj** analys av ett visst Azure datautforskaren-kluster för att utföra detaljerad analys.
-    **Anpassningsbara** där du kan ändra vilka mått du vill se, ändra eller ange tröskelvärden som överensstämmer med dina gränser och spara dina egna anpassade arbets böcker. Diagram i arbets boken kan fästas på Azure-instrumentpaneler.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Visa från Azure Monitor (i skala perspektiv)

Från Azure Monitor kan du Visa huvud prestanda måtten för klustret, inklusive mått för frågor, inmatning och export åtgärder från flera kluster i din prenumeration och hjälpa till att identifiera prestanda problem.

Gör så här om du vill visa prestanda för dina kluster i alla prenumerationer:

1. Logga in på [Azure Portal](https://portal.azure.com/)

2. Välj **övervaka** i rutan till vänster i Azure Portal och i avsnittet Insights-hubb väljer du **Azure datautforskaren-kluster (för hands version)**.

![Skärm bild av översikts upplevelse med flera grafer](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Fliken Översikt

På fliken **Översikt** för den valda prenumerationen visar tabellen interaktiva mått för Azure datautforskaren-kluster grupperade i prenumerationen. Du kan filtrera resultat baserat på de alternativ du väljer i följande List rutor:

* Prenumerationer – endast prenumerationer som har Azure Datautforskaren-kluster visas i listan.

* Azure Datautforskaren-kluster – som standard är bara upp till fem kluster markerade. Om du väljer alla eller flera kluster i omfattnings väljaren returneras upp till 200 kluster.

* Tidsintervall – visar som standard de senaste 24 timmarna med information baserat på motsvarande val som gjorts.

Panelen räknare, under List rutan, slår samman det totala antalet Azure Datautforskaren-kluster i de valda prenumerationerna och visar hur många som är markerade. Det finns villkorsstyrda färg kodningar för kolumnerna: Keep Alive, CPU, förbruknings användning och cache-användning. Orange-kodade celler har värden som inte är hållbara för klustret. 

För att bättre förstå vad vart och ett av måtten representerar rekommenderar vi att du läser igenom dokumentationen om [Azure datautforskaren mått](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Fliken prestanda för fråga

På den här fliken visas frågans varaktighet, det totala antalet samtidiga frågor och det totala antalet begränsade frågor.

![Skärm bild av fliken fråga prestanda](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Fliken inmatnings prestanda

På den här fliken visas inmatnings fördröjningen, lyckade inmatnings resultat, misslyckade inmatnings resultat, inmatnings volym och händelser som bearbetas för Event/IoT-hubbar.

[![Skärm bild av fliken inmatnings prestanda](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Fliken prestanda vid direkt uppspelning

Den här fliken innehåller information om den genomsnittliga data frekvensen, genomsnittlig varaktighet och begär ande frekvens.

### <a name="export-performance-tab"></a>Fliken Exportera prestanda

Den här fliken innehåller information om exporterade poster, sena, väntande antal och användnings procent för kontinuerliga export åtgärder.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Visa från en Azure Datautforskaren kluster resurs (detalj nivå analys)

Få åtkomst till Azure Monitor för Azure Datautforskaren-kluster direkt från ett Azure Datautforskaren-kluster:

1. I Azure Portal väljer du **Azure datautforskaren-kluster**.

2. Välj ett Azure Datautforskaren-kluster i listan. I avsnittet övervakning väljer du **insikter (för hands version)**.

Dessa vyer är också tillgängliga genom att du väljer resurs namnet för ett Azure Datautforskaren-kluster i vyn Azure Monitor Insights.

Azure Monitor för Azure Datautforskaren kombinerar både loggar och mått för att tillhandahålla en global övervaknings lösning. Inkludering av loggarbaserade visualiseringar kräver att användare [aktiverar diagnostisk loggning av Azure datautforskaren-klustret och skickar dem till en Log Analytics arbets yta.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs). Diagnostikloggar som ska aktive ras är: **kommando**, **fråga**, **TableDetails** och **TableUsageStatistics**.

![Skärm bild av blå knapp som visar texten "Aktivera loggar för övervakning"](./media/data-explorer/enable-logs.png)


 Fliken **Översikt** visar:

- Mått paneler som markerar klustrets tillgänglighet och övergripande status för att snabbt utvärdera dess hälsa.

- En sammanfattning av Active [Advisor-rekommendationer](/azure/data-explorer/azure-advisor) och [resurs hälso](/azure/data-explorer/monitor-with-resource-health) status.

- Diagram som visar de främsta processor-och minnes förbrukarna och antalet unika användare över tid.


[![Skärm bild av vy från en Azure Datautforskaren Cluster-resurs](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

På fliken **viktiga mått** visas en enhetlig vy över några av klustrets mått, grupperat efter: allmänna mått, frågor relaterade, inmatnings-och strömnings-relaterade mått.

[![Skärm bild av vyn med problem](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

På fliken **användning** kan användare djupgående att gå in på prestandan för klustrets kommandon och frågor. På den här sidan kan du:
 
 - Se vilka arbets belastnings grupper, användare och program som skickar de flesta frågor eller använder mest processor kraft och minne (så att du kan förstå vilka arbets belastningar som skickar de tyngsta frågorna för klustret att bearbeta).
 - Identifiera de vanligaste arbets belastnings grupperna, användare och program efter misslyckade frågor.
 - Identifiera de senaste ändringarna i antalet frågor, jämfört med det historiska dagliga genomsnittet (under de senaste 16 dagarna), efter arbets belastnings grupp, användare och program.
 - Identifiera trender och toppar i antalet frågor, minne och processor förbrukning per arbets belastnings grupp, användare, program och kommando typ.

[![Skärm bild av vyn vyer med ring diagram med högsta användning av kommando-och antal frågor, främsta huvud konton med kommando-och antal frågor och främsta kommandon efter kommando typer](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Skärm bild av vyn vyer med linje diagram av antal frågor per program, totalt minne efter program och total CPU per program](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

Fliken **tabeller** visar de senaste och historiska egenskaperna för tabeller i klustret. Du kan se vilka tabeller som utnyttjar det mest stora utrymmet, spåra tillväxt historiken efter tabell storlek, frekventa data och antalet rader över tid.

På fliken **cache** kan användare analysera sina faktiska frågor se tillbaka mönster och jämföra dem med den konfigurerade cache-principen (för varje tabell). Du kan identifiera tabeller som används av de flesta frågor och tabeller som inte har frågats alls, och anpassa den cachelagrade principen på lämpligt sätt. Du kan få särskilda principer för cache-principer för specifika tabeller i Azure Advisor (för närvarande är cache-rekommendationer bara tillgängliga från [huvud Azure Advisors instrument panelen](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)), baserat på faktiska frågor, se tillbaka de senaste 30 dagarna och en icke-optimerad cache-princip för minst 95% av frågorna. Rekommendationer för minskning av cachelagring i Azure Advisor är tillgängliga för kluster som är "avgränsade av data" (vilket innebär att klustret har låg processor och låg inmatnings användning, men på grund av hög data kapacitet kunde klustret inte skalas in eller skalas ned).

[![Skärm bild av cache-information](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Fäst på Azure-instrumentpanelen

Du kan fästa något av mått avsnitten (i "i-skala"-perspektivet) på en Azure-instrumentpanel genom att välja kartnål-ikonen längst upp till höger i avsnittet.

![Skärm bild av PIN-ikonen vald](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Anpassa Azure Monitor för Azure Datautforskaren-kluster

I det här avsnittet beskrivs vanliga scenarier för att redigera arbets boken för att anpassa stöd för dina data analys behov:
* Ange omfång för arbets boken att alltid välja en viss prenumeration eller Azure Datautforskaren-kluster
* Ändra mått i rutnätet
* Ändra tröskelvärden eller färg åter givning/kodning

Du kan börja anpassningar genom att aktivera redigerings läget genom att välja knappen **Anpassa** i det översta verktygsfältet.

![Skärm bild av knappen anpassa](./media/data-explorer/customize.png)

Anpassningar sparas i en anpassad arbets bok för att förhindra att standard konfigurationen skrivs över i vår publicerade arbets bok. Arbets böcker sparas i en resurs grupp, antingen i avsnittet Mina rapporter som är privat för dig eller i avsnittet delade rapporter som är tillgängliga för alla som har åtkomst till resurs gruppen. När du har sparat den anpassade arbets boken måste du gå till arbets boks galleriet för att starta den.

![Skärm bild av arbets bokens Galleri](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Felsökning

Allmän fel söknings vägledning finns i artikeln om dedikerad informations [fel sökning](troubleshoot-workbooks.md)för arbets böcker.

I det här avsnittet får du hjälp med diagnos och fel sökning av några vanliga problem som kan uppstå när du använder Azure Monitor för Azure Datautforskaren-kluster (för hands version). Använd listan nedan för att hitta den information som är relevant för det aktuella problemet.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Varför visas inte alla mina prenumerationer i prenumerations väljaren?

Vi visar bara prenumerationer som innehåller Azure Datautforskaren-kluster, som valts från det valda prenumerations filtret, som väljs i "Directory + Subscription" i Azure Portal-huvudet.

![Skärm bild av prenumerations filter](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Varför visas inte några data för mitt Azure Datautforskaren-kluster under avsnitten användning, tabeller eller cache?

Om du vill visa dina loggarbaserade data måste du [Aktivera diagnostikloggar](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) för vart och ett av de Azure datautforskaren-kluster som du vill övervaka. Detta kan göras under diagnostikinställningar för varje kluster. Du måste skicka dina data till en Log Analytics-arbetsyta. Diagnostikloggar som ska aktive ras är: kommando, fråga, TableDetails och TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Jag har redan aktiverat loggar för mitt Azure Datautforskaren-kluster, varför kan jag fortfarande inte se mina data under kommandon och frågor?

Diagnostikloggar fungerar för närvarande inte retroaktivt, så data kommer bara att börja visas när du har vidtagit åtgärder för din Azure-Datautforskaren. Därför kan det ta lite tid, från timmar till en dag, beroende på hur aktivt ditt Azure Datautforskaren-kluster är.


## <a name="next-steps"></a>Nästa steg

Lär dig mer om arbets böckerna i scenarier är utformade för att stödja, hur du skapar nya och anpassar befintliga rapporter och mer genom att granska [skapa interaktiva rapporter med Azure Monitor arbets böcker](../visualize/workbooks-overview.md).
