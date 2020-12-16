---
title: Avancerade funktioner i Azure Metrics Explorer
description: Lär dig mer om avancerade funktioner i Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: be3d3f11e90c17bd8c4792418500da651039e480
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562811"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Avancerade funktioner i Azure Metrics Explorer

> [!NOTE]
> Den här artikeln förutsätter att du är bekant med grundläggande funktioner i Metrics Explorer. Om du är en ny användare och vill lära dig hur du skapar ditt första mått diagram, se [komma igång med Azure Metrics Explorer](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Mått i Azure

[Mått i Azure Monitor](data-platform-metrics.md) är serien med uppmätta värden och antal som samlas in och lagras över tid. Det finns standard-(eller "Platform") mått och anpassade mått. Standard måtten tillhandahålls av själva Azure-plattformen. Standard måtten återspeglar hälso-och användnings statistik för dina Azure-resurser. Anpassade mått skickas till Azure av dina program med hjälp av [Application Insights API för anpassade händelser och mått](../app/api-custom-events-metrics.md),  [Windows Azure-diagnostik (wad)-tillägg](./diagnostics-extension-overview.md)eller [Azure Monitor REST API](./metrics-store-custom-rest-api.md).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Skapa vyer med flera mått och diagram

Du kan skapa diagram som ritar flera mått linjer eller Visa flera Mät diagram samtidigt. Med den här funktionen kan du:

- korrelera relaterade mått i samma graf för att se hur ett värde är relaterat till ett annat
- Visa mått med olika mått enheter i nära närhet
- Sammanställ och jämför mått från flera resurser visuellt

Om du till exempel har 5 lagrings konton och vill veta hur mycket totalt utrymme som förbrukas mellan dem, kan du skapa ett (staplat) ytdiagram som visar den enskilda och summan av alla värden vid specifika tidpunkter.

### <a name="multiple-metrics-on-the-same-chart"></a>Flera mått i samma diagram

Börja med att [skapa ett nytt diagram](metrics-getting-started.md#create-your-first-metric-chart). Klicka på **Lägg till mått** och upprepa stegen för att lägga till ett mått i samma diagram.

   > [!NOTE]
   > Du vill normalt inte ha mått med olika mått enheter (t. ex. "millisekunder" och "kilobyte") eller med betydligt annorlunda skala i ett diagram. Överväg i stället att använda flera diagram. Klicka på knappen Lägg till diagram för att skapa flera diagram i Metrics Explorer.

### <a name="multiple-charts"></a>Flera diagram

Klicka på **Lägg till diagram** och skapa ett annat diagram med ett annat mått.

### <a name="order-or-delete-multiple-charts"></a>Ordna eller ta bort flera diagram

Om du vill ordna eller ta bort flera diagram klickar du på ellipsen ( **...** )-symbolen för att öppna Diagram-menyn och välja lämpligt meny alternativ för **Flytta upp**, **Flytta ned** eller **ta bort**.

## <a name="changing-aggregation"></a>Ändrar agg regering

När du lägger till ett mått i ett diagram väljer Metric Explorer automatiskt sin standard agg regering. Standardvärdet är meningsfullt i de grundläggande scenarierna, men du kan använda en annan agg regering för att få ytterligare insikter om måttet. Om du visar olika agg regeringar i ett diagram måste du förstå hur Mät Utforskaren hanterar dem. 

Mått är den serie av mått (eller "mått värden") som har hämtats under tids perioden. När du ritar ett diagram sammanställs värdena för de valda måtten separat under *tids kornig het*. Du väljer storleken på tidskornigt på [panelen Metrics Explorer tids väljare](metrics-getting-started.md#select-a-time-range). Om du inte gör ett explicit val av tids kornig het väljs tids kornig het automatiskt utifrån det valda tidsintervallet. När tids kornigheten har fastställts sammanställs Mät värdena som fångades under varje tids kornig het och placeras i diagrammet-en Datapoint per tids kornig het.

Anta till exempel att diagrammet visar värdet för **Server svars tid** med hjälp av den **genomsnittliga** agg regeringen under de **senaste 24 timmarna** tids rymden:

- Om tids kornigheten har angetts till 30 minuter ritas diagrammet från 48-aggregerade Datapoints (t. ex. linje diagrammet ansluter 48 punkter i diagrammets rityta). Det vill säga 24 timmar x 2 Datapoints per timme. Varje Datapoint representerar *genomsnittet* av alla uppfångade svars tider för server begär Anden som inträffat under de 30 min tids perioderna.
- Om du växlar tids kornig het till 15 minuter får du 96 sammanställd datapoints.  Det vill säga 24 timmar x 4 Datapoints per timme.

Det finns fem grundläggande agg regerings typer i mått Utforskaren: **Sum**, **Count**, **min**, **Max** och **Average**. **Sum** -aggregering kallas ibland **Total** aggregation. För många mått kommer Metrics Explorer dölja de agg regeringar som är helt irrelevanta och inte kan användas.

- **Sum** – summan av alla värden som har samlats in under samlings intervallet
- **Count** – antalet mått som har hämtats över samlings intervallet. Observera att **Count** motsvarar **Sum** i det fall då måttet alltid fångas med värdet 1. Detta är vanligt när måttet spårar antalet distinkta händelser och varje mått representerar en händelse (d.v.s. koden inaktive ras en mått post varje gång en ny begäran kommer in)
- **Genomsnitt** – medelvärdet av mått värden som har samlats in över samlings intervallet
- **Min** – det minsta värdet som fångas över agg regerings intervallet
- **Max** – det största värdet som har fångats över samlings intervallet

## <a name="apply-filters-to-charts"></a>Tillämpa filter på diagram

Du kan använda filter för diagram som visar mått med dimensioner. Om till exempel måttet "antal transaktioner" har en dimension, "svarstyp", som anger om svaret från transaktioner lyckades eller inte har misslyckats, skulle filtrering av den här dimensionen rita en diagram rad för endast lyckade (eller endast misslyckade) transaktioner. 

### <a name="to-add-a-filter"></a>Lägga till ett filter

1. Välj **Lägg till filter** ovanför diagrammet

2. Välj vilken dimension (egenskap) som du vill filtrera

   ![Skärm bild som visar de dimensioner (egenskaper) som du kan filtrera.](./media/metrics-charts/028.png)

3. Välj vilka dimensions värden som du vill ta med när diagrammet ritas (det här exemplet visar filtrering av lyckade lagrings transaktioner):

   ![Skärm bild som visar filtreringen av lyckade lagrings transaktioner.](./media/metrics-charts/029.png)

4. När du har valt filter värden klickar du på bort från filter väljaren för att stänga den. Nu visar diagrammet hur många lagrings transaktioner som har misslyckats:

   ![Skärm bild som visar hur många lagrings transaktioner som har misslyckats](./media/metrics-charts/030.png)

5. Du kan upprepa steg 1-4 om du vill tillämpa flera filter på samma diagram.



## <a name="apply-splitting-to-a-chart"></a>Använd delning i ett diagram

Du kan dela upp ett mått per dimension för att visualisera hur olika segment i måttet jämförs med varandra och identifiera de yttre segmenten i en dimension.

### <a name="apply-splitting"></a>Använd delning

1. Klicka på **Använd delning** ovanför diagrammet.
 
   > [!NOTE]
   > Delning kan inte användas med diagram som har flera mått. Du kan också ha flera filter, men bara en delnings dimension som tillämpas på ett enskilt diagram.

2. Välj en dimension som du vill segmentera diagrammet på:

   ![Skärm bild som visar den valda dimensionen på vilken du segmenterar diagrammet.](./media/metrics-charts/031.png)

   Nu visar diagrammet flera rader, en för varje dimensions segment:

   ![Skärm bild som visar flera rader, en för varje dimensions segment.](./media/metrics-charts/032.png)

3. Klicka bort från **grupp väljaren** för att stänga den.

   > [!NOTE]
   > Använd både filtrering och delning på samma dimension för att dölja de segment som är irrelevanta för ditt scenario och göra diagram lättare att läsa.

## <a name="lock-boundaries-of-chart-y-axis"></a>Lås gränser i diagrammets y-axel

Att låsa y-axelns intervall blir viktigt när diagrammet visar mindre variationer av större värden. 

Om antalet lyckade förfrågningar till exempel sjunker från 99,99% till 99,5% kan det innebära en betydande minskning av tjänst kvaliteten. Märker av små numeriska värden skulle dock vara svårt eller ens omöjligt från standard diagram inställningarna. I det här fallet kan du låsa den lägsta kant linjen i diagrammet till 99%, vilket gör den små minskningen tydligare. 

Ett annat exempel är en fluktuation i det tillgängliga minnet, där värdet då tekniskt aldrig når 0. Att korrigera intervallet till ett högre värde kan göra att det tillgängliga minnet blir lättare att hitta. 

Om du vill styra y-axelns intervall använder du "..." Diagram-menyn och välj **diagram inställningar** för att få åtkomst till avancerade diagram inställningar.

![Skärm bild som markerar alternativet diagram inställningar.](./media/metrics-charts/033.png)

 Ändra värdena i avsnittet intervall i Y-axeln eller Använd knappen **Auto** för att återgå till standardvärdena.
 
 ![Skärm bild som visar avsnittet intervall för Y-axeln.](./media/metrics-charts/034.png)

> [!WARNING]
> Att låsa gränserna för y-axeln för de diagram som spårar olika antal eller summor under en tids period (och därför användnings antal, sum, minimal eller maximal agg regeringar) kräver vanligt vis att du anger en fast tids kornig het i stället för att förlita dig på de automatiska standardvärdena. Detta är nödvändigt eftersom värdena i diagram ändras när tids kornig het ändras automatiskt av användarens storleks ändrings fönster eller från en skärmupplösning till en annan. Den resulterande ändringen av tids kornig het påverkar diagrammets utseende, vilket gör att det aktuella valet av y-axelns intervall är ogiltigt.

## <a name="change-colors-of-chart-lines"></a>Ändra färger i diagram linjer

När du har konfigurerat diagrammen tilldelas diagram linjerna automatiskt en färg från en standardpalett. Du kan ändra dessa färger.

Om du vill ändra färgen på en diagram linje klickar du på det färgade fältet i förklaringen som motsvarar diagrammet. Dialog rutan färg väljare öppnas. Använd färg väljaren för att konfigurera färg för linjen.

![Skärm bild som visar hur du ändrar färg](./media/metrics-charts/035.png)

När diagrammets färger har kon figurer ATS kommer de att förbli på det sättet när du fäster diagrammet på en instrument panel. I följande avsnitt visas hur du fäster ett diagram.

## <a name="pin-charts-to-dashboards"></a>Fästa diagram på instrument paneler

När du har konfigurerat diagrammen kanske du vill lägga till det i instrument panelerna så att du kan visa det igen, eventuellt i samband med andra övervakning av telemetri, eller dela med ditt team.

Fästa ett konfigurerat diagram på en instrument panel:

När du har konfigurerat diagrammet klickar **du på Fäst på instrument panelen** i det högra övre hörnet i diagrammet.

![Skärm bild som visar hur du fäster i diagrammet](./media/metrics-charts/036.png)

## <a name="create-alert-rules"></a>Skapa aviseringsregler

Du kan använda de kriterier som du har angett för att visualisera måtten som grund för en Metric-baserad varnings regel. Den nya varnings regeln innehåller dina mål resurser, mått, delning och filter dimensioner från diagrammet. Du kommer att kunna ändra inställningarna senare i fönstret Skapa aviserings regel.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Om du vill skapa en ny varnings regel klickar du på **ny aviserings regel**

![Knappen Ny varnings regel markerad i rött](./media/metrics-charts/042.png)

Du kommer att gå till fönstret Skapa aviserings regel med de underliggande mått dimensionerna från diagrammet i förväg ifyllda för att göra det enklare att skapa anpassade aviserings regler.

![Skapa aviseringsregel](./media/metrics-charts/041.png)

Kolla in den här [artikeln](alerts-metric.md) om du vill veta mer om hur du konfigurerar mått aviseringar.

## <a name="troubleshooting"></a>Felsökning

*Jag ser inga data i diagrammet.*

* Filter gäller för alla diagram i fönstret. Se till att du inte har angett något filter som utesluter alla data på ett annat, medan du fokuserar på ett diagram.

* Om du vill ange olika filter för olika diagram skapar du dem på olika blad, sparar dem som separata favoriter. Om du vill kan du fästa dem på instrument panelen så att du kan se dem bredvid varandra.

* Om du segmentera ett diagram med en egenskap som inte har definierats för måttet, kommer det inte att finnas något i diagrammet. Försök att rensa segmenteringen (delning) eller Välj en annan egenskap.

## <a name="next-steps"></a>Nästa steg

  Läs [skapa anpassade KPI-instrumentpaneler](../learn/tutorial-app-dashboards.md) för att lära dig mer om metod tips för att skapa åtgärds bara instrument paneler med mått.
