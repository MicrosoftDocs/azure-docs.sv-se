---
title: Körnings visare för hörn i Data Lake verktyg för Visual Studio
description: Den här artikeln beskriver hur du använder körnings vyn för hörn för att se om det finns en examen Data Lake Analytics jobb.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/13/2016
ms.openlocfilehash: 3fba7bdaa5db1d812fbcd479e5f1eab50c8d1032
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92215866"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Använd vyn hörn körning i Data Lake verktyg för Visual Studio
Lär dig hur du använder körnings vyn för hörn för att få en examen Data Lake Analytics jobb.


## <a name="open-the-vertex-execution-view"></a>Öppna körnings vyn hörn
Öppna ett U-SQL-jobb i Data Lake verktyg för Visual Studio. Klicka på **vyn för körning av hörn** i det nedre vänstra hörnet. Du kan uppmanas att först läsa in profiler och det kan ta lite tid beroende på din nätverks anslutning.

![Skärm bild som visar körnings vyn för Data Lake Analytics verktyg hörn](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Förstå körnings vy för hörn
Vyn hörn körning har tre delar:

![Skärm bild som visar körnings vyn för hörn med hörn väljaren och mitten-övre och centrerade nedersta rutor markerade.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

Med **hörn väljaren** till vänster kan du välja formhörn efter funktioner (till exempel de översta 10 lästa data eller välja efter steg). Ett av de oftast använda filtren är att se **hörnen på den kritiska linjen**. Den **kritiska linjen** är den längsta kedjan av hörn i ett U-SQL-jobb. Att förstå den kritiska vägen är användbart för att optimera dina jobb genom att kontrol lera vilket hörn som tar längst tid.
  
![Skärm bild som visar fönstret hörn körning Visa övre fönster ruta som visar "körnings status för alla hörnen".](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Det övre fönstret i mitten visar **körnings status för alla hörnen**.
  
![Skärm bild som visar fönstret hörn körning Visa längst ned i mitten som visar information om varje hörn.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

I det nedre fönstret i mitten visas information om varje hörn:
* Process namn: namnet på hörn-instansen. Den består av olika delar i StageName | VertexName | VertexRunInstance. Exempel: SV7_Split [62]. v1-hörn står för den andra aktiva instansen (. v1, index som börjar från 0) för hörn nummer 62 i steg SV7_Split.
* Totalt antal lästa/skrivna data: Data lästes/skrevs av den här Bryt punkten.
* Status/avslutnings status: den slutliga statusen när hörnen är slut.
* Slutkod/felaktig typ: felet när hörnen misslyckades.
* Orsak till skapande: Varför hörnen skapades.
* Resurs latens/process svars tid/PN svars tid för kö: den tid det tar för vertex att vänta på resurser, bearbeta data och stanna kvar i kön.
* Process/Creator GUID: GUID för den aktuella form som körs eller dess skapare.
* Version: den N-instansen av den aktiva Bryt punkten (systemet kan schemalägga nya instanser av ett hörn av många skäl, till exempel redundans, Compute-redundans osv.)
* Tid för version skapades.
* Process för att skapa start tid/process köade tid/process start tid/process slut tid: När hörn processen börjar skapas; När hörn processen börjar i kö; När en viss vertex-process startar; När ett visst formhörn har slutförts.

## <a name="next-steps"></a>Nästa steg
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Information om hur du visar jobb information finns i [använda jobb webbläsare och jobb visning för Azure Data Lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md)
