---
title: Övervaka Azure Cosmos DB med Azure Monitor för Cosmos DB | Microsoft Docs
description: I den här artikeln beskrivs Azure Monitor för Cosmos DB-funktionen som ger Cosmos DB ägare en snabb förståelse för prestanda-och användnings problem med sina CosmosDB-konton.
author: lgayhardt
ms.author: lagayhar
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: d88bf65f1bd94e29bd9f60f5597d655f0040623b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725808"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Utforska Azure Monitor för Azure Cosmos DB

Azure Monitor för Azure Cosmos DB ger en översikt över övergripande prestanda, fel, kapacitet och drifts hälsa för alla dina Azure Cosmos DB resurser i en enhetlig interaktiv upplevelse. Den här artikeln hjälper dig att förstå fördelarna med den nya övervaknings miljön och hur du kan ändra och anpassa upplevelsen så att den passar organisationens unika behov.   

## <a name="introduction"></a>Introduktion

Innan du simhopp i upplevelsen bör du förstå hur den visar och visualiserar information. 

Den ger:

* I **skalnings perspektiv** för dina Azure Cosmos DB resurser över alla dina prenumerationer på en enda plats, med möjlighet att selektivt begränsa till de prenumerationer och resurser som du är intresse rad av att utvärdera.

* **Nedbrytnings analys** av en viss Azure CosmosDB-resurs för att hjälpa till att diagnostisera problem eller utföra detaljerad analys efter kategori användning, fel, kapacitet och åtgärder. Om du väljer något av dessa alternativ visas en djupgående vy över relevanta Azure Cosmos DB mått.  

* **Anpassningsbar** – den här upplevelsen bygger på Azure Monitor mallar för arbets böcker som gör att du kan ändra vilka mått som visas, ändra eller ange tröskelvärden som överensstämmer med dina gränser och sedan spara dem i en anpassad arbets bok. Diagram i arbets böckerna kan sedan fästas på Azure-instrumentpaneler.  

Den här funktionen kräver inte att du aktiverar eller konfigurerar något, dessa Azure Cosmos DB mått samlas in som standard.

>[!NOTE]
>Det kostar inget att få åtkomst till den här funktionen och du debiteras bara för Azure Monitor viktiga funktioner som du konfigurerar eller aktiverar, enligt beskrivningen på sidan [pris information för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Visa användnings-och prestanda mått för Azure Cosmos DB

Utför följande steg för att visa användning och prestanda för dina lagrings konton i alla dina prenumerationer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **övervakare** och välj **övervaka**.

    ![Sökrutan med ordet "Monitor" och en listruta som säger tjänster "Monitor" med en hastighets format bild](./media/cosmosdb-insights-overview/search-monitor.png)

3. Välj **Cosmos DB**.

    ![Skärm bild av Cosmos DB översikt över arbets bok](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Översikt

I **Översikt** visar tabellen interaktiva Azure Cosmos DB mått. Du kan filtrera resultaten baserat på de alternativ du väljer i följande List rutor:

* **Prenumerationer – endast** prenumerationer som har en Azure Cosmos DB resurs visas.  

* **Cosmos DB** – du kan välja alla, en delmängd eller en enskild Azure Cosmos DB resurs.

* **Tidsintervall** – som standard visar de senaste 4 timmarna med information baserat på motsvarande val som gjorts.

Panelen räknare under List rutor slår upp det totala antalet Azure Cosmos DB resurser finns i de valda prenumerationerna. Det finns en villkorsstyrd färg kodning eller termiska kartor för kolumner i arbets boken som rapporterar transaktions mått. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena. 

Om du väljer en nedrullningsbar pil bredvid någon av Azure Cosmos DB resurserna visas en uppdelning av prestanda måtten på den enskilda databas behållar nivån:

![Expanderad listruta för att visa enskilda databas behållare och associerad prestanda analys](./media/cosmosdb-insights-overview/container-view.png)

Om du markerar Azure Cosmos DB resurs namn som marker ATS i blått tas du till standard **översikten** för det associerade Azure Cosmos DB-kontot. 

### <a name="failures"></a>Fel

Välj **felen** överst på sidan och **felen** i arbets bokens mall öppnas. Det visar totalt antal begär Anden med distributionen av svar som utgör dessa förfrågningar:

![Skärm bild av problem med nedbrytning efter HTTP-begäran](./media/cosmosdb-insights-overview/failures.png)

| Kod |  Beskrivning       | 
|-----------|:--------------------|
| `200 OK`  | En av följande REST-åtgärder lyckades: </br>– Hämta på en resurs. </br> – PUBLICERA på en resurs. </br> – PUBLICERA på en resurs. </br> – PUBLICERA på en lagrad procedur resurs för att köra den lagrade proceduren.|
| `201 Created` | En POST-åtgärd för att skapa en resurs har slutförts. |
| `404 Not Found` | Åtgärden försöker vidta en resurs som inte längre finns. Till exempel kanske resursen redan har tagits bort. |

En fullständig lista över status koder finns i artikeln om [status kod för Azure Cosmos DB http](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Kapacitet

Välj **kapacitet** överst på sidan och **kapacitets** delen i arbets boks mal len öppnas. Det visar hur många dokument du har, din dokument tillväxt över tid, data användning och den totala mängden tillgängligt lagrings utrymme som du har kvar.  Detta kan användas för att identifiera eventuella problem med lagring och data användning.

![Arbets bok för kapacitet](./media/cosmosdb-insights-overview/capacity.png) 

Precis som med översikts arbets boken kan du välja den nedrullningsbara List rutan bredvid en Azure Cosmos DB-resurs i kolumnen **prenumeration** för att visa en uppdelning av de enskilda behållare som utgör databasen.

### <a name="operations"></a>Operations

Välj **åtgärder** överst på sidan och **drift** delen av arbets bokens mall öppnas. Det ger dig möjlighet att se dina begär Anden uppdelade efter den typ av förfrågningar som görs.

I exemplet nedan ser du att i `eastus-billingint` huvudsak tar emot Läs begär Anden, men med ett litet antal upsert-och Create-begäranden. `westeurope-billingint`Är skrivskyddad från ett ärende perspektiv, minst de senaste fyra timmarna som arbets boken för närvarande är begränsad till via dess tidsintervalls parameter.

![Arbets bok](./media/cosmosdb-insights-overview/operation.png)

## <a name="view-from-an-azure-cosmos-db-resource"></a>Visa från en Azure Cosmos DB resurs

1. Sök efter eller Välj något av dina befintliga Azure Cosmos DB-konton.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-search.png" alt-text="Sök efter Azure Cosmos DB." border="true":::

2. När du har navigerat till ditt Azure Cosmos DB-konto i avsnittet övervakning väljer du **insikter (för hands version)** eller **arbets böcker** för att utföra ytterligare analys av data flöde, begär Anden, lagring, tillgänglighet, svars tid, system och konto hantering.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-overview.png" alt-text="Översikt över Cosmos DB Insights." border="true":::

### <a name="time-range"></a>Tidsintervall

Fältet **tidsintervall** visar som standard data från de **senaste 24 timmarna**. Du kan ändra tidsintervallet för att visa data var som helst under de senaste 5 minuterna till de senaste sju dagarna. Tids intervalls väljaren innehåller också ett **anpassat** läge som gör att du kan ange start-/slutdatum för att visa en anpassad tidsram baserat på tillgängliga data för det valda kontot.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-time-range.png" alt-text="Cosmos DB tidsintervall." border="true":::

### <a name="insights-overview"></a>Översikt över insikter

Fliken **Översikt** visar de vanligaste måtten för det valda Azure Cosmos DB kontot, inklusive:

* Totalt antal förfrågningar
* Misslyckade förfrågningar (429s)
* Normaliserad RU-förbrukning (max)
* Användning av data & index
* Cosmos DB konto mått per samling

**Totalt antal förfrågningar:** I den här grafen visas en översikt över det totala antalet begär Anden för kontot uppdelat efter status kod. Enheterna längst ned i diagrammet är en summa av den totala antalet begär Anden för perioden.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-total-requests.png" alt-text="Diagram över Cosmos DB totalt antal begär Anden." border="true":::

**Misslyckade förfrågningar (429s)**: den här grafen visar en vy över misslyckade förfrågningar med status koden 429. Enheterna längst ned i diagrammet är en summa av de totala misslyckade förfrågningarna för perioden.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-429.png" alt-text="Diagram över Cosmos DB misslyckade förfrågningar." border="true":::

**Normaliserad ru-förbrukning (max)**: det här diagrammet ger max procent andelen mellan 0-100% av NORMALISERAde ru-förbruknings enheter för den angivna perioden.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-normalized-ru.png" alt-text="Cosmos DB normaliserad RU-förbrukning." border="true":::

## <a name="pin-export-and-expand"></a>Fäst, exportera och expandera

Du kan fästa något av mått avsnitten på en Azure- [instrumentpanel](../../azure-portal/azure-portal-dashboards.md) genom att välja kartnål-ikonen längst upp till höger i avsnittet.

![Mått avsnitt fäst vid instrument panelen exempel](./media/cosmosdb-insights-overview/pin.png)

Om du vill exportera data till Excel-formatet väljer du nedåtpilen till vänster om ikonen kartnål.

![Ikonen Exportera arbets bok](./media/cosmosdb-insights-overview/export.png)

Om du vill visa eller dölja alla nedrullningsbara List rutor i arbets boken väljer du ikonen Expandera till vänster om export ikonen:

![Ikonen Visa arbets bok](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Anpassa Azure Monitor för Azure Cosmos DB

Eftersom den här upplevelsen bygger på Azure Monitor arbetsboksmall kan du **Anpassa**  >  **Redigera** och **Spara** en kopia av den ändrade versionen i en anpassad arbets bok. 

![Anpassa stapel](./media/cosmosdb-insights-overview/customize.png)

Arbets böcker sparas i en resurs grupp, antingen i avsnittet **Mina rapporter** som är privat för dig eller i avsnittet **delade rapporter** som är tillgängliga för alla som har åtkomst till resurs gruppen. När du har sparat den anpassade arbets boken måste du gå till arbets boks galleriet för att starta den.

![Starta arbets boks galleriet från kommando fältet](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Felsökning

Information om fel sökning finns i [artikeln](troubleshoot-workbooks.md)om att hitta en dedikerad arbets bok baserad insikter.

## <a name="next-steps"></a>Nästa steg

* Konfigurera [mått aviseringar](../alerts/alerts-metric.md) och [meddelanden om tjänst hälsa](../../service-health/alerts-activity-log-service-notifications-portal.md) för att ställa in automatiserad avisering som hjälper till att identifiera problem.

* Lär dig mer om arbets böckerna i scenarier är utformade för att stödja, hur du skapar nya och anpassar befintliga rapporter och mer genom att granska [skapa interaktiva rapporter med Azure Monitor arbets böcker](../visualize/workbooks-overview.md).
