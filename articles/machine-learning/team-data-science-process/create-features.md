---
title: Funktions teknik i Machine Learning – team data science process
description: Lär dig mer om funktions teknik och dess roll i data förbättrings processen för Machine Learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: b20a6744644678879fedf44e960854f558eb0f03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98610445"
---
# <a name="feature-engineering-in-machine-learning"></a>Funktions teknik i Machine Learning

I den här artikeln får du lära dig mer om funktions teknik och dess roll i att förbättra data i Machine Learning. Lär dig mer om exempel som ritats från [Azure Machine Learning Studio (klassiska)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) experiment. 

* **Funktions teknik**: processen för att skapa nya funktioner från rå data för att öka den förutsägande kraften hos inlärnings algoritmen. De funktioner som är tillverkade bör fånga ytterligare information som inte är lätt att hitta i den ursprungliga funktions uppsättningen.
* **Val av funktion**: processen att välja en nyckel del uppsättning av funktioner för att minska inlärnings problemets dimensionalitet.

Normalt implementeras **funktions teknik** först för att generera ytterligare funktioner, och **funktions val** görs för att eliminera irrelevanta, redundanta eller mycket korrelerade funktioner.

Funktions teknik och urval är en del av [modellerings fasen](lifecycle-modeling.md) för team data science-processen (TDSP). Mer information om TDSP och data vetenskapens livs cykel finns i [Vad är TDSP?](overview.md)

## <a name="what-is-feature-engineering"></a>Vad är funktions teknik?

Tränings data består av en matris som består av rader och kolumner. Varje rad i matrisen är en observations-eller post. Kolumnerna för varje rad är de funktioner som beskriver varje post. De funktioner som anges i experiment designen bör karakterisera mönstren i data.

Även om många av rå data fälten kan användas direkt för att träna en modell, är det ofta nödvändigt att skapa ytterligare (utformade) funktioner för en förbättrad utbildnings data uppsättning.

Tekniker som förbättrar utbildningen ger information som bättre särskiljer mönstren i data. Men den här processen är något av en bild. Ett ljud-och produktivt beslut kräver ofta domän expert kunskaper.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exempel 1: Lägg till temporala funktioner för en Regressions modell

Vi använder test [efter frågan på prognoser om cyklar](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) i Azure Machine Learning Studio (klassisk) för att visa hur man kan använda funktioner för en Regressions uppgift. Syftet med det här experimentet är att förutse efter frågan på cykel uthyrningar inom en bestämd månad/dag/timme.

### <a name="bike-rental-dataset"></a>Data uppsättning för cykel hyra

Den andra enhets [uppsättningen för cykel uthyrning](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) baseras på verkliga data från ett cykel resurs företag som är baserat på USA. Den representerar antalet cykel uthyrningar inom en angiven timme på en dag för åren 2011 och 2012. Den innehåller 17 379 rader och 17 kolumner.

RAW-funktions uppsättningen innehåller väder förhållanden (temperatur/fuktighet/vindhastighet) och typ av dag (helgdag/veckodag). Fältet att Förutsäg är antalet, som representerar cykel hyrarna inom en angiven timme. Räkna intervallen från 1 till 977.

### <a name="create-a-feature-engineering-experiment"></a>Skapa ett funktions teknik experiment

Med målet att konstruera effektiva funktioner i tränings data skapas fyra Regressions modeller med samma algoritm, men med fyra olika inlärnings data uppsättningar. De fyra data uppsättningarna representerar samma rå data, men med ett ökande antal funktioner. Dessa funktioner är grupperade i fyra kategorier:

1. A = väder + helg + helg + veckosluts funktioner för den förväntade dagen
2. B = antalet cyklar som har hyrs under de föregående 12 timmarna
3. C = antal cyklar som har hyrs under de föregående 12 dagarna i samma timme
4. D = antal cyklar som hyrs under de närmast föregående 12 veckorna i samma timme och samma dag

Förutom feature set A, som redan finns i de ursprungliga rå data, skapas de andra tre uppsättningarna med funktioner i funktions teknik processen. Funktions uppsättning B fångar upp senaste efter frågan för cyklarna. Funktions uppsättning C fångar efter frågan för cyklar vid en viss timme. Funktionen D samlar in efter frågan för cyklar vid en viss tid och en viss dag i veckan. De fyra inlärnings data uppsättningarna omfattar en funktion som har angetts A, A + B, A + B + C och A + B + C + D.

### <a name="feature-engineering-using-studio-classic"></a>Funktions teknik med Studio (klassisk)

I det klassiska Studio-experimentet skapas dessa fyra inlärnings data uppsättningar via fyra grenar från den förbehandlade indata-datauppsättningen. Förutom för grenen längst till vänster innehåller var och en av dessa grenar en modul för att [köra R-skript](/azure/machine-learning/studio-module-reference/execute-r-script) där de härledda funktionerna (funktions uppsättning B, C och D) skapas och läggs till i den importerade data uppsättningen.

Följande bild visar det R-skript som används för att skapa funktions uppsättning B i den andra vänstra grenen.

![skapa funktioner](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Resultat

En jämförelse av prestanda resultatet för de fyra modellerna sammanfattas i följande tabell: 

![resultat jämförelse](./media/create-features/result1.png)

De bästa resultaten visas av funktionerna A + B + C. Fel frekvensen minskar när ytterligare funktions uppsättning ingår i tränings data. Den verifierar att funktionen uppsättning B, C ger ytterligare relevant information för Regressions uppgiften. Men om du lägger till D-funktionen verkar det inte ge någon ytterligare minskning av fel frekvensen.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> Exempel 2: skapa funktioner för text utvinning

Funktions teknik används ofta i uppgifter som rör text utvinning, till exempel dokument klassificering och sentiment analys. Eftersom enskilda delar av RAW-text vanligt vis fungerar som indata, krävs funktions teknik processen för att skapa funktioner som rör frekvenser för ord/fraser.

### <a name="feature-hashing"></a>Hashing av funktioner

För att uppnå den här uppgiften används en teknik som kallas [funktion-hash](/azure/machine-learning/studio-module-reference/feature-hashing) för att effektivt aktivera godtycklig text funktioner i index. I stället för att associera varje text funktion (ord/fraser) till ett visst index, använder den här metoden en hash-funktion för funktionerna och använder sina hash-värden som index direkt.

I Studio (klassisk) finns en modul för [hashing](/azure/machine-learning/studio-module-reference/feature-hashing) av funktioner som skapar ord/fras-funktioner bekvämt. Följande bild visar ett exempel på hur du använder den här modulen. Data uppsättningen för indata innehåller två kolumner: bok klassificeringen sträcker sig från 1 till 5 och det faktiska gransknings innehållet. Målet med den här modulen är att hämta en massa nya funktioner som visar förekomst frekvensen för motsvarande Word-/phrase i en viss bok granskning. Utför följande steg för att använda den här modulen:

* Börja med att markera den kolumn som innehåller indatamängden ("Col2" i det här exemplet).
* Ange sedan "hashing bitsize" till 8, vilket innebär att 2 ^ 8 = 256 funktioner skapas. Ordet/fasen i all text kommer att hashas till 256-index. Parametern "hashing bitsize" sträcker sig från 1 till 31. Ord/phrase (s) är mindre sannolika att hashas i samma index om det ställs in som ett större tal.
* För det tredje anger du parametern "N-g" till 2. Det här värdet hämtar förekomst frekvensen för unigrams (en funktion för varje enskilt ord) och två g (en funktion för varje par av intilliggande ord) från den inmatade texten. Parametern "N-gram" sträcker sig från 0 till 10, vilket anger det maximala antalet sekventiella ord som ska tas med i en funktion.  

![Modulen "funktion-hashing"](./media/create-features/feature-Hashing1.png)

Följande bild visar hur den nya funktionen ser ut.

![Exempel på funktions-hashing](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Slutsats
Utformade och valda funktioner ökar effektiviteten i inlärnings processen, som försöker extrahera nyckelinformation som finns i data. De kan också förbättra kraften i dessa modeller för att klassificera indata på ett korrekt sätt och förutsäga resultat av intresse mer robust.

Funktions teknik och urval kan också kombineras för att göra inlärningen mer beräknings bara. Det gör det genom att förbättra och minska antalet funktioner som behövs för att kalibrera eller träna en modell. De valda funktionerna är matematiska och är en minimal uppsättning oberoende variabler som förklarar mönstren i data och förutsäger att de lyckas.

Det är inte alltid nödvändigt att utföra funktion teknik eller val av funktioner. Det beror på data, vilken algoritm som valts och syftet med experimentet.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar funktioner för data i vissa miljöer finns i följande artiklar:

* [Skapa funktioner för data i SQL Server](create-features-sql-server.md)
* [Skapa funktioner för data i ett Hadoop-kluster med Hive-frågor](create-features-hive.md)