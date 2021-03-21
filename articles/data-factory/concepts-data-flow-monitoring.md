---
title: Övervaka data flöden för mappning
description: Övervaka mappning av data flöden visuellt i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/22/2020
ms.openlocfilehash: 9ca5ea5cdebe297af5081ae6e219935c56ba942e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004895"
---
# <a name="monitor-data-flows"></a>Övervaka data flöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du har slutfört skapandet och fel sökningen av ditt data flöde vill du schemalägga att ditt data flöde ska köras enligt ett schema inom ramen för en pipeline. Du kan schemalägga pipelinen från Azure Data Factory med utlösare. Eller så kan du använda alternativet utlösare nu från Azure Data Factory pipeline Builder för att köra en körning med en körning för att testa ditt data flöde inom pipeline-kontexten.

När du kör din pipeline kan du övervaka pipelinen och alla aktiviteter som finns i pipelinen, inklusive data flödes aktiviteten. Klicka på ikonen övervaka i panelen till vänster Azure Data Factory användar gränssnitt. Du kan se en skärm som liknar den nedan. Med de markerade ikonerna kan du öka detalj nivån i aktiviteterna i pipelinen, inklusive data flödes aktiviteten.

![Skärm bild som visar ikoner att välja för pipelines för mer information.](media/data-flow/mon001.png "Dataflödesövervakning")

Du ser statistik på den här nivån även körnings tiderna och-statusen. Körnings-ID: t på aktivitets nivån skiljer sig från körnings-ID: t på pipelin nivån. Körnings-ID: t på den föregående nivån är för pipelinen. Genom att välja glasögon får du djupgående information om din data flödes körning.

![Skärm bild som visar glasögon-ikonen för att se information om data flödes körning.](media/data-flow/monitoring-details.png "Dataflödesövervakning")

När du befinner dig i vyn övervakning av grafisk nod kan du se en förenklad visnings version av data flödes diagrammet.

![Skärm bild visar diagrammets visnings version.](media/data-flow/mon003.png "Dataflödesövervakning")

Här är en video översikt över övervakning av prestanda för dina data flöden från fönstret ADF Monitoring:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Visa körnings planer för data flöde

När ditt data flöde körs i Spark, fastställer Azure Data Factory optimala kod Sök vägar baserat på hela ditt data flöde. Dessutom kan sökvägar för körning ske på olika skalbara noder och datapartitioner. Därför representerar övervaknings diagrammet utformningen av ditt flöde, med hänsyn till körnings vägen för dina transformeringar. När du väljer enskilda noder kan du se "grupperingar" som representerar kod som har körts tillsammans i klustret. De tids inställningar och räknare som du ser representerar dessa grupper i stället för de enskilda stegen i din design.

![Skärm bild som visar sidan för ett data flöde.](media/data-flow/mon004.png "Dataflödesövervakning")

* När du väljer det öppna utrymmet i övervaknings fönstret, visar statistik i det nedre fönstret tidtagnings-och rad antal för varje mottagare och de omvandlingar som ledde till mottagar data för omvandlings härkomst.

* När du väljer enskilda transformeringar får du ytterligare feedback på den högra panelen som visar partitionerings statistik, kolumn antal, snedhet (hur jämnt är data som distribueras mellan partitioner) och toppar (hur höga data är).

* När du väljer mottagaren i vyn Node kan du se kolumnen härkomst. Det finns tre olika metoder som kolumner samlas in i data flödet för att hamna i mottagaren. De är:

  * Beräknad: du använder kolumnen för villkorlig bearbetning eller inom ett uttryck i ditt data flöde, men du kan inte landa den i mottagaren
  * Härlett: kolumnen är en ny kolumn som du har genererat i ditt flöde, det vill säga den fanns inte i källan
  * Mappad: kolumnen kommer från källan och din mappas till fältet mottagare
  * Data flödes status: aktuell status för din körning
  * Kluster start tid: hur lång tid det tar att hämta JIT Spark Compute-miljön för data flödes körning
  * Antal transformeringar: hur många omvandlings steg som körs i ditt flöde
  
![Skärm bild som visar uppdaterings alternativet.](media/data-flow/monitornew.png "Data flödes övervakning New")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>Total bearbetningstid för mottagare jämfört med bearbetningstid för transformering

Varje omvandlings steg innehåller en total tid för fasen att slutföras med varje partitions körnings tid sammanlagt tillsammans. När du klickar på mottagaren visas "mottagar bearbetnings tid". Den här tiden inkluderar summan av Transformations tiden *plus* i/O-tiden det tog att skriva data till mål lagret. Skillnaden mellan bearbetnings tiden för mottagaren och det totala antalet omvandlingar är i/O-tiden för att skriva data.

Du kan också se detaljerad tids inställning för varje partitions omvandlings steg om du öppnar JSON-utdata från din data flödes aktivitet i vyn ADF pipeline-övervakning. JSON innehåller millisekund-timing för varje partition, medan vyn UX-övervakning är en sammanställd tids inställning för partitionerna som läggs till tillsammans:

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```

### <a name="post-processing-time"></a>Bearbetnings tid för inlägg

När du väljer en omvandlings ikon för mottagare i kartan visas en ytterligare data punkt med namnet "efter bearbetnings tid" längst ned i den högra panelen. Detta är den mängd tid som krävs för att köra jobbet på Spark-klustret *efter* att dina data har lästs in, omvandlats och skrivits. Den här tiden kan vara att stänga anslutningspooler, stänga av driv rutin, ta bort filer, sammanföra filer osv. När du utför åtgärder i ditt flöde, t. ex. "flytta filer" och "utdata till en enskild fil", kommer du troligen att se en ökning i värdet efter bearbetnings tid.
  
## <a name="error-rows"></a>Felrader

Att aktivera fel rad hantering i din data flödes mottagare visas i övervaknings resultatet. När du ställer in Sink till "rapportera lyckade vid fel" visar övervaknings resultatet antalet lyckade och misslyckade rader när du klickar på noden för övervakning av mottagare.

![Skärm bild som visar fel rader.](media/data-flow/error-row-2.png "Fel rad övervakningen lyckades")

När du väljer "rapportera fel vid fel" visas samma utdata endast i text för aktiviteten övervakning av utdata. Detta beror på att data flödes aktiviteten returnerar ett problem med körningen och att vyn detaljerad övervakning inte är tillgänglig.

![Skärm bild som visar fel rader i aktivitet.](media/data-flow/error-rows-4.png "Fel vid rad övervakning")

## <a name="monitor-icons"></a>Övervaka ikoner

Den här ikonen innebär att Transformations data redan har cachelagrats i klustret, så tids inställningarna och körnings Sök vägen har tagit detta i beräkningen:

![Skärm bild som visar disk ikonen.](media/data-flow/mon005.png "Dataflödesövervakning")

Du ser också gröna cirkel ikoner i omvandlingen. De representerar antalet handfat som data flödar in i.
