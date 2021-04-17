---
title: Mappa dataflödets felsökningsläge
description: Starta en interaktiv felsökningssession när du skapar dataflöden
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ac0e088c587132b32f2112d21bce936eac35dc72
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515877"
---
# <a name="mapping-data-flow-debug-mode"></a>Mappa dataflödets felsökningsläge

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Översikt

Azure Data Factory att mappa dataflödets felsökningsläge kan du interaktivt se dataformtransformen medan du skapar och felsöker dina dataflöden. Felsökningssessionen kan användas både i Dataflöde designsessioner och under pipelinefelsökningskörning av dataflöden. Om du vill aktivera felsökningsläget använder du **knappen Dataflöde Felsökning** i det översta fältet på dataflödesarbetsytan eller pipelinearbetsytan när du har dataflödesaktiviteter.

![Skjutreglage för felsökning 1](media/data-flow/debugbutton.png "Skjutreglage för felsökning")

![Skjutreglage för felsökning 2](media/data-flow/debug-button-4.png "Skjutreglage för felsökning")

När du aktiverar skjutreglaget uppmanas du att välja vilken integration runtime-konfiguration du vill använda. Om AutoResolveIntegrationRuntime väljs, kommer ett kluster med åtta kärnor allmän beräkning med en standardtid på 60 minuter att vara live. Om du vill tillåta fler inaktiva team innan sessionens slut kan du välja en högre TTL-inställning. Mer information om körningar för integrering av dataflöden finns i [Dataflödesprestanda.](concepts-data-flow-performance.md#ir)

![Felsöka val av IR](media/data-flow/debug-new-1.png "Felsöka val av IR")

När felsökningsläget är aktiverat skapar du ditt dataflöde interaktivt med ett aktivt Spark-kluster. Sessionen stängs när du inaktiverar felsökningen i Azure Data Factory. Du bör vara medveten om de timavgifter som Azure Databricks debiteras under den tid då felsökningssessionen är aktiverad.

I de flesta fall är det en bra idé att skapa dina dataflöden i felsökningsläge så att du kan validera affärslogiken och visa dina datatransformationer innan du publicerar ditt arbete i Azure Data Factory. Använd knappen "Felsök" på pipelinepanelen för att testa ditt dataflöde i en pipeline.

![Visa felsökningssessioner för dataflöden](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Varje felsökningssession som en användare startar från sitt ADF-webbläsargränssnitt är en ny session med ett eget Spark-kluster. Du kan använda övervakningsvyn för felsökningssessioner ovan för att visa och hantera felsökningssessioner per fabrik. Du debiteras för varje timme som varje felsökningssession körs, inklusive TTL-tiden.

## <a name="cluster-status&quot;></a>Klusterstatus

Klusterstatusindikatorn överst på designytan blir grön när klustret är redo för felsökning. Om klustret redan är varmt visas den gröna indikatorn nästan omedelbart. Om klustret inte redan kördes när du gick in i felsökningsläge utför Spark-klustret en kallstart. Indikatorn snurrar tills miljön är redo för interaktiv felsökning.

När du är klar med felsökningen stänger du av felsökningsknappen så att Spark-klustret kan avslutas och du inte längre debiteras för felsökningsaktiviteten.

## <a name=&quot;debug-settings&quot;></a>Felsökningsinställningar

När du aktiverar felsökningsläget kan du redigera hur dataflöde förhandsgranskar data. Du kan redigera felsökningsinställningarna genom att klicka på &quot;Felsökningsinställningar&quot; Dataflöde verktygsfältet för arbetsytan. Du kan välja den radgräns eller filkälla som ska användas för var och en av dina källtransformningar här. Radgränserna i den här inställningen gäller endast för den aktuella felsökningssessionen. Du kan också välja den länkade mellanlagringstjänst som ska användas för en Azure Synapse Analytics källa. 

![Felsökningsinställningar](media/data-flow/debug-settings.png &quot;Felsökningsinställningar")

Om du har parametrar i Dataflöde eller någon av dess refererade datauppsättningar kan du ange vilka värden som ska användas under felsökningen genom att **välja fliken** Parametrar.

Använd samplingsinställningarna här för att peka på exempelfiler eller exempeltabeller med data så att du inte behöver ändra dina källdatauppsättningar. Genom att använda en exempelfil eller tabell här kan du underhålla samma logik- och egenskapsinställningar i ditt dataflöde vid testning mot en delmängd av data.

![Parametrar för felsökningsinställningar](media/data-flow/debug-settings2.png "Parametrar för felsökningsinställningar")

Standard-IR som används för felsökningsläge i ADF-dataflöden är en liten 4-kärnig enskild arbetsnod med en nod med 4 kärnor med en enda drivrutin. Detta fungerar bra med mindre dataexempel när du testar dataflödeslogiken. Om du expanderar radgränserna i dina felsökningsinställningar under dataförhandsvisningen eller anger ett högre antal samplade rader i källan under pipelinefelsökning, kan du överväga att ange en större beräkningsmiljö i en ny Azure Integration Runtime. Sedan kan du starta om felsökningssessionen med hjälp av den större beräkningsmiljön.

## <a name="data-preview"></a>Förhandsgranskning

När felsökning är på kommer fliken Förhandsgranskning av data att lysa upp på den nedre panelen. Utan felsökningsläget aktiverat visar Dataflöde endast aktuella metadata in och ut från var och en av dina transformningar på fliken Inspektera. Dataförhandsvisningen frågar bara efter antalet rader som du har angett som gräns i felsökningsinställningarna. Klicka **på Uppdatera** för att hämta dataförhandsvisningen.

![Förhandsgranskning](media/data-flow/datapreview.png "Förhandsgranskning")

> [!NOTE]
> Filkällor begränsar endast de rader som visas, inte de rader som läses. För mycket stora datamängder rekommenderar vi att du tar en liten del av filen och använder den för testning. Du kan välja en temporär fil i Felsökningsinställningar för varje källa som är en fildatauppsättningstyp.

När du kör i felsökningsläge i Dataflöde skrivs inte dina data till sink-transformen. En felsökningssession är avsedd att fungera som ett test fullt ut för dina transformningar. Mottagare krävs inte under felsökningen och ignoreras i ditt dataflöde. Om du vill testa att skriva data i din mottagare kör du Dataflöde från en Azure Data Factory Pipeline och använder felsökningskörningen från en pipeline.

Dataförhandsvisning är en ögonblicksbild av dina transformerade data med radgränser och datasampling från dataramar i Spark-minnet. Därför används eller testas inte mottagarens drivrutiner i det här scenariot.

### <a name="testing-join-conditions"></a>Testa kopplingsvillkor

När du testar kopplingar, finns eller sökningsomvandlar ska du se till att du använder en liten uppsättning kända data för testet. Du kan använda alternativet Felsökningsinställningar ovan för att ange en tillfällig fil som ska användas för testningen. Detta är nödvändigt eftersom du inte kan förutsäga vilka rader och vilka nycklar som ska läsas in i flödet för testning när du begränsar eller tar bort rader från en stor datamängd. Resultatet är icke-deterministiskt, vilket innebär att kopplingsvillkoren kan misslyckas.

### <a name="quick-actions"></a>Snabbåtgärder

När du ser dataförhandsvisningen kan du generera en snabb transformering för att typcasta, ta bort eller göra en ändring i en kolumn. Klicka på kolumnrubriken och välj sedan något av alternativen i verktygsfältet för förhandsgranskning av data.

![Skärmbild som visar verktygsfältet för dataförhandsgranskning med alternativ: Typecast, Modify, Statistics och Remove.](media/data-flow/quick-actions1.png "Snabbåtgärder")

När du har valt en ändring uppdateras dataförhandsgranskningen omedelbart. Klicka **på** Bekräfta i det övre högra hörnet för att generera en ny transformering.

![Skärmbild som visar knappen Bekräfta.](media/data-flow/quick-actions2.png "Snabbåtgärder")

**Typecast och** **Modify genererar** en transformering av härledd kolumn och **Remove** genererar en Select-transformering.

![Skärmbild som visar Inställningar för härledd kolumn.](media/data-flow/quick-actions3.png "Snabbåtgärder")

> [!NOTE]
> Om du redigerar Dataflöde måste du hämta dataförhandsvisningen på nytt innan du lägger till en snabbtransformering.

### <a name="data-profiling"></a>Dataprofilering

Om du väljer en kolumn  på fliken för dataförhandsgranskning och klickar på Statistik i verktygsfältet för dataförhandsgranskning visas ett diagram längst till höger i datarutnätet med detaljerad statistik om varje fält. Azure Data Factory gör en bestämning baserat på datasampling av vilken typ av diagram som ska visas. Fält med hög kardinalitet kommer som standard att ha NULL-/NOT NULL-diagram medan kategoriska och numeriska data med låg kardinalitet visar stapeldiagram som visar datavärdesfrekvensen. Du ser också max/längd för strängfält, min/max-värden i numeriska fält, standardutveckling, percentiler, antal och medelvärde.

![Kolumnstatistik](media/data-flow/stats.png "Kolumnstatistik")

## <a name="next-steps"></a>Nästa steg

* När du är klar med att skapa och felsöka dataflödet kör [du det från en pipeline.](control-flow-execute-data-flow-activity.md)
* När du testar din pipeline med ett dataflöde använder du körningsalternativet För [felsökning av pipeline.](iterative-development-debugging.md)
