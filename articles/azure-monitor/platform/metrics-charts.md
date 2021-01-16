---
title: Avancerade funktioner i Azure Metrics Explorer
description: Lär dig mer om avancerade användnings områden i Azure Metrics Explorer.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: b4feb177abbdbfb9666be0ea0746c8316acdf5ae
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250765"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Avancerade funktioner i Azure Metrics Explorer

> [!NOTE]
> Den här artikeln förutsätter att du är bekant med grundläggande funktioner i Azure Metrics Explorer-funktionen i Azure Monitor. Om du är en ny användare och vill lära dig hur du skapar ditt första mått diagram, se [komma igång med Metrics Explorer](metrics-getting-started.md).

I Azure Monitor är [måtten](data-platform-metrics.md) en serie med uppmätta värden och antal som samlas in och lagras över tid. Mått kan vara standard (kallas även "plattform") eller anpassad. 

Azure-plattformen tillhandahåller standard mått. De återspeglar hälso-och användnings statistik för dina Azure-resurser. 

## <a name="resource-scope-picker"></a>Resurs omfattnings väljare
Med resurs omfattnings Väljaren kan du visa mått för enskilda resurser och flera resurser. I följande avsnitt beskrivs hur du använder resurs omfattnings väljaren. 

### <a name="select-a-single-resource"></a>Välj en enskild resurs
Välj **mått** på **Azure Monitor** -menyn eller från **övervaknings** avsnittet på en resurs meny. Välj sedan **Välj ett omfång** för att öppna omfattnings väljaren. 

Använd omfattnings väljaren för att välja de resurser vars mått du vill se. Omfattningen bör vara ifylld om du har öppnat Azure Metrics Explorer från en resurs meny. 

![Skärm bild som visar hur du öppnar resurs omfattnings väljaren.](./media/metrics-charts/scope-picker.png)

För vissa resurser kan du bara visa en resurs mått i taget. På menyn **resurs typer** finns resurserna i avsnittet **alla resurs typer** .

![Skärm bild som visar en enskild resurs.](./media/metrics-charts/single-resource-scope.png)

När du har valt en resurs visas alla prenumerationer och resurs grupper som innehåller resursen.

![Skärm bild som visar tillgängliga resurser.](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Om du vill att funktionen ska visa måtten för flera resurser samtidigt, eller om du vill visa mått i en prenumeration eller resurs grupp, väljer du **rösten**.

När du är nöjd med ditt val väljer du **tillämpa**.

### <a name="view-metrics-across-multiple-resources"></a>Visa mått över flera resurser
Vissa resurs typer kan fråga efter mått över flera resurser. Resurserna måste vara inom samma prenumeration och plats. Sök efter dessa resurs typer överst i menyn **resurs typer** . 

Mer information finns i [Välj flera resurser](metrics-dynamic-scope.md#select-multiple-resources).

![Skärm bild som visar olika typer av resurser.](./media/metrics-charts/multi-resource-scope.png)

För typer som är kompatibla med flera resurser kan du fråga efter mått i en prenumeration eller flera resurs grupper. Mer information finns i [Välj en resurs grupp eller prenumeration](metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Flera mått linjer och diagram

I Azure Metrics Explorer kan du skapa diagram som ritar flera mått linjer eller Visa flera mått diagram samtidigt. Med den här funktionen kan du:

- Korrelera relaterade mått i samma graf för att se hur ett värde relaterar till ett annat.
- Visa mått som använder olika mått enheter i nära närhet.
- Sammanställ och jämför mått från flera resurser visuellt.

Anta till exempel att du har fem lagrings konton och vill veta hur mycket utrymme de använder tillsammans. Du kan skapa ett (staplat) ytdiagram som visar de enskilda värdena och summan av alla värden vid specifika tidpunkter.

### <a name="multiple-metrics-on-the-same-chart"></a>Flera mått i samma diagram

Om du vill visa flera mått i samma diagram måste du först [skapa ett nytt diagram](metrics-getting-started.md#create-your-first-metric-chart). Välj sedan **Lägg till mått**. Upprepa det här steget om du vill lägga till ett mått i samma diagram.

> [!NOTE]
> Normalt ska dina diagram inte blanda mått som använder olika mått enheter. Undvik till exempel att blanda ett mått som använder millisekunder med en annan som använder kilobyte. Undvik också att blanda mått vars skalning skiljer sig avsevärt. 
>
> I dessa fall bör du överväga att använda flera diagram i stället. I mått Utforskaren väljer du **Lägg till diagram** för att skapa ett nytt diagram.

### <a name="multiple-charts"></a>Flera diagram

Om du vill skapa ett annat diagram som använder ett annat mått väljer du **Lägg till diagram**.

Om du vill ändra ordning på eller ta bort flera diagram väljer du knappen med tre punkter (**...**) för att öppna Diagram menyn. Välj sedan **Flytta upp**, **Flytta ned** eller **ta bort**.

## <a name="aggregation"></a>Aggregering

När du lägger till ett mått i ett diagram tillämpar Metric Explorer automatiskt en standard agg regering. Standardvärdet är bra i grundläggande scenarier. Men du kan använda en annan agg regering för att få mer insikter om måttet. 

Innan du använder olika agg regeringar i ett diagram bör du förstå hur mått Utforskaren hanterar dem. Mått är en serie mätningar (eller "mått värden") som har hämtats under en viss tids period. När du ritar ett diagram sammanställs värdena för de valda måtten separat under *tids kornig het*. 

Du väljer storlek på tids kornig het genom att använda [panelen för tids väljare](metrics-getting-started.md#select-a-time-range)för mått Utforskaren. Om du inte uttryckligen väljer tids kornig het används det valda tidsintervallet som standard. När tids kornigheten har fastställts sammanställs Mät värdena som samlades in under varje tids kornig het i diagrammet, en data punkt per tids kornig het.

Anta till exempel att ett diagram visar värdet för *Server svars tid* . Den använder *genomsnittlig* agg regering över tids perioden för de *senaste 24 timmarna*. I det här exemplet:

- Om tids kornig het har angetts till 30 minuter ritas diagrammet från 48 sammanställda data punkter. Det vill säga linje diagrammet ansluter 48 punkter i diagram området (24 timmar x 2 data punkter per timme). Varje data punkt representerar *genomsnittet* av alla uppfångade svars tider för server begär Anden som inträffat under var och en av de relevanta 30 minuters tids perioderna.
- Om du växlar tids kornig het till 15 minuter får du 96 sammanställda data punkter.  Det innebär att du får 24 timmar x 4 data punkter per timme.

Mått Utforskaren har fem grundläggande statistiska agg regerings typer: sum, Count, min, max och Average. *Sum* -aggregering kallas ibland *Total* aggregation. För många mått döljer Metric Explorer de agg regeringar som är irrelevanta och kan inte användas.

* **Sum**: summan av alla värden som har registrerats under agg regerings intervallet.

    ![Skärm bild av en sum-begäran.](./media/metrics-charts/request-sum.png)

* **Count**: antalet mätningar som registrerats under agg regerings intervallet. 
    
    När måttet alltid fångas med värdet 1, är agg regerings aggregatet lika med sum-aggregeringen. Det här scenariot är vanligt när måttet spårar antalet distinkta händelser och varje mått representerar en händelse. Koden avger en mått post varje gång en ny begäran tas emot.

    ![Skärm bild av en Count-begäran.](./media/metrics-charts/request-count.png)

* **Genomsnitt**: medelvärdet av mått värden som samlas in under agg regerings intervallet.

    ![Skärm bild av en genomsnittlig begäran.](./media/metrics-charts/request-avg.png)

* **Min**: det minsta värdet som fångades under samlings intervallet.

    ![Skärm bild av en minimal begäran.](./media/metrics-charts/request-min.png)

* **Max**: det största värdet som samlas in under samlings intervallet.

    ![Skärm bild av en maximal begäran.](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filter

Du kan använda filter för diagram vars mått har dimensioner. Anta till exempel att ett "antal transaktioner"-mått har en "svars typ"-dimension. Den här dimensionen anger om svaret från transaktioner lyckades eller misslyckades. Om du filtrerar på den här dimensionen visas en diagram rad för endast lyckade (eller endast misslyckade) transaktioner. 

### <a name="add-a-filter"></a>Lägga till ett filter

1. Välj **Lägg till filter** ovanför diagrammet.

2. Välj en dimension (egenskap) att filtrera.

   ![Skärm bild som visar de dimensioner (egenskaper) som du kan filtrera.](./media/metrics-charts/028.png)

3. Välj de dimensions värden som du vill ta med när du ritar diagrammet. I följande exempel filtreras de lyckade lagrings transaktionerna:

   ![Skärm bild som visar lyckade filtrerade lagrings transaktioner.](./media/metrics-charts/029.png)

4. Välj utanför **filter väljaren** för att stänga den. Nu visar diagrammet hur många lagrings transaktioner som har misslyckats:

   ![Skärm bild som visar hur många lagrings transaktioner som har misslyckats.](./media/metrics-charts/030.png)

Du kan upprepa dessa steg om du vill tillämpa flera filter på samma diagram.



## <a name="metric-splitting"></a>Mått delning

Du kan dela upp ett mått per dimension för att visualisera hur olika segment i måttet jämför. Delning kan också hjälpa dig att identifiera de yttre segmenten i en dimension.

### <a name="apply-splitting"></a>Använd delning

1. Välj **Använd dela upp** ovanför diagrammet.
 
   > [!NOTE]
   > Diagram som har flera mått kan inte använda delnings funktionen. Även om ett diagram kan ha flera filter, kan det bara ha en delnings dimension.

2. Välj en dimension som diagrammet ska segmenteras på:

   ![Skärm bild som visar den valda dimensionen för att segmentera diagrammet.](./media/metrics-charts/031.png)

   Diagrammet visar nu flera rader, ett för varje dimensions segment:

   ![Skärm bild som visar rader för varje dimensions segment.](./media/metrics-charts/032.png)

3. Välj utanför **grupp väljaren** för att stänga den.

   > [!NOTE]
   > Om du vill dölja segment som är irrelevanta för ditt scenario och göra dina diagram lättare att läsa, använder du både filtrering och delning på samma dimension.

## <a name="locking-the-range-of-the-y-axis"></a>Låser intervallet för y-axeln

Låsning av intervallet för värde axeln (y) blir viktigt i diagram som visar små variationer av stora värden. 

En minskning av antalet lyckade förfrågningar från 99,99 till 99,5 procent kan till exempel utgöra en betydande minskning av tjänst kvaliteten. Men märker av små numeriska värden skulle vara svårt eller ens omöjligt om du använder standard diagram inställningarna. I det här fallet kan du låsa det lägsta kant linjen i diagrammet till 99 procent för att göra en liten minskning tydligare. 

Ett annat exempel är en fluktuation i det tillgängliga minnet. I det här scenariot kommer värdet tekniskt aldrig att uppgå till 0. Att korrigera intervallet till ett högre värde kan göra att det tillgängliga minnet blir lättare att hitta. 

Du styr intervallet för y-axeln genom att öppna Diagram-menyn (**...**). Välj sedan **diagram inställningar** för att få åtkomst till avancerade diagram inställningar.

![Skärm bild som visar val av diagram inställningar.](./media/metrics-charts/033.png)

Ändra värdena i avsnittet **intervall i Y-axeln** eller Välj **Auto** om du vill återgå till standardvärdena.
 
 ![Skärm bild som visar avsnittet intervall för Y-axeln.](./media/metrics-charts/034.png)

> [!WARNING]
> Om du behöver låsa y-axelns gränser för diagram som spårar antal eller belopp under en tids period (med antal, sum, min eller Max agg regeringar) bör du vanligt vis ange en fast tids kornig het. I det här fallet ska du inte förlita dig på de automatiska standardinställningarna. 
>
> Du väljer en fast tids kornig het eftersom diagram värden ändras när tids kornig het ändras automatiskt när en användare ändrar storlek på webbläsarfönstret eller ändrar skärmupplösningen. Den resulterande ändringen i tids kornig het påverkar diagrammets utseende, vilket gör att det aktuella valet av y-axelns intervall är ogiltigt.

## <a name="line-colors"></a>Linje färger

När du har konfigurerat diagrammen tilldelas diagram linjerna automatiskt en färg från en standardpalett. Du kan ändra dessa färger.

Om du vill ändra färgen på en diagram linje väljer du det färgade fältet i förklaringen som motsvarar diagrammet. Dialog rutan färg väljare öppnas. Använd färg väljaren för att konfigurera linje färgen.

![Skärm bild som visar hur du ändrar färg.](./media/metrics-charts/035.png)

Dina anpassade färger bevaras när du fäster diagrammet på en instrument panel. I följande avsnitt visas hur du fäster ett diagram.

## <a name="pinning-to-dashboards"></a>Fästa på instrument paneler 

När du har konfigurerat ett diagram kanske du vill lägga till det på en instrument panel. Genom att fästa ett diagram på en instrument panel kan du göra det tillgängligt för ditt team. Du kan också få insikter genom att se den i samband med andra övervakning av telemetri.

Om du vill fästa ett konfigurerat diagram på en instrument panel väljer du **Fäst på instrument panelen** i det övre högra hörnet i diagrammet.

![Skärm bild som visar hur du fäster ett diagram på en instrument panel.](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Aviseringsregler

Du kan använda dina visualiserings kriterier för att skapa en Metric-baserad aviserings regel. Den nya varnings regeln kommer att innehålla diagrammets mål resurs, mått, delning och filter dimensioner. Du kan ändra inställningarna med hjälp av fönstret Skapa aviserings regel.

Börja genom att välja **ny aviserings regel**.

![Skärm bild som visar knappen Ny varnings regel markerad i rött.](./media/metrics-charts/042.png)

Fönstret Skapa aviserings regel öppnas. I fönstret visas diagrammets mått mått. Fälten i fönstret är förifyllda för att hjälpa dig att anpassa regeln.

![Skärm bild som visar fönstret Skapa regel.](./media/metrics-charts/041.png)

Mer information finns i [skapa, Visa och hantera mått varningar](alerts-metric.md).

## <a name="troubleshooting"></a>Felsökning

Om du inte ser några data i diagrammet kan du läsa följande felsöknings information:

* Filter gäller för alla diagram i fönstret. När du fokuserar på ett diagram bör du se till att du inte anger ett filter som undantar alla data i ett annat diagram.

* Skapa diagram på olika blad om du vill ange olika filter för olika diagram. Spara sedan diagrammen som separata favoriter. Om du vill kan du fästa diagrammen på instrument panelen så att du kan se dem tillsammans.

* Om du segmentera ett diagram med en egenskap som måttet inte definierar, visar diagrammet inget innehåll. Försök att rensa segmenteringen (delning) eller Välj en annan egenskap.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar åtgärds bara instrument paneler med hjälp av mått finns i [skapa anpassade KPI-instrumentpaneler](../learn/tutorial-app-dashboards.md).

 