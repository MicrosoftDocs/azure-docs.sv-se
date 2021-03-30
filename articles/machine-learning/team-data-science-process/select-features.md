---
title: Funktions val i team data science process
description: Förklarar syftet med val av funktioner och ger exempel på deras roll i data förbättrings processen för Machine Learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 940d4c465acb5e8478d2b204ab5b9b18c79254f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321329"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funktionsval i TDSP (Team Data Science Process)
Den här artikeln förklarar syftet med val av funktioner och ger exempel på dess roll i processen för data förbättring av Machine Learning. Dessa exempel hämtas från Azure Machine Learning Studio.

Teknikerna och valet av funktioner är en del av TDSP (Team data science process) som beskrivs i artikeln [Vad är team data science-processen?](overview.md). Funktions teknik och urval är delar av steget **utveckla funktioner** i TDSP.

* **funktions teknik**: den här processen försöker skapa ytterligare relevanta funktioner från befintliga RAW-funktioner i data och öka förutsägande kraft för inlärningen.
* **Val av funktion**: den här processen väljer en nyckel del av de ursprungliga data funktionerna i ett försök att minska inlärnings problemets dimensionalitet.

Normalt **används funktionerna först** för att generera ytterligare funktioner och sedan utförs **funktions urvalet** för att eliminera irrelevanta, redundanta eller mycket korrelerade funktioner.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrera funktioner från dina data funktions val
Funktions val kan användas för klassificerings-eller Regressions uppgifter. Målet är att välja en delmängd av funktionerna från den ursprungliga data uppsättningen som minskar sina dimensioner genom att använda en minimal uppsättning funktioner för att representera den maximala avvikelsen i data. Den här del mängden av funktioner används för att träna modellen. Val av funktioner är två huvudsakliga syfte.

* För det första ökar funktions valet ofta klassificerings precision genom att eliminera irrelevanta, redundanta eller mycket korrelerade funktioner.
* För det andra minskar antalet funktioner, vilket gör processen för modell inlärning mer effektiv. Effektiviteten är viktigt för att lära sig som är dyra att träna, till exempel för att stödja Vector-datorer.

Även om val av funktioner gör att du kan minska antalet funktioner i data uppsättningen som används för att träna modellen, så refereras den inte till av termen "Dimensional minskning". Funktions urvals metoder extraherar en delmängd av ursprungliga funktioner i data utan att ändra dem.  Metoder för minskning av dimensionalitet använder utformade funktioner som kan omvandla de ursprungliga funktionerna och därmed ändra dem. Exempel på metoder för minskning av dimensionalitet är huvudsakliga komponent analys, kanonisk korrelations analys och sammanställning av sammansatta värden.

Bland annat kallas en allmänt tillämpad kategori med funktions urvals metoder i en övervakad kontext, "filterbaserade funktions val". Genom att utvärdera korrelationen mellan varje funktion och målattributet tillämpar dessa metoder ett statistiskt mått för att tilldela varje funktion en poäng. Funktionerna rangordnas sedan efter poängen, som kan användas för att ange tröskelvärdet för att hålla eller ta bort en speciell funktion. Exempel på statistiska mått som används i dessa metoder är person korrelation, ömsesidig information och chi2-test.

I Azure Machine Learning Studio finns moduler för val av funktioner. Som du ser i följande bild inkluderar dessa moduler [filtrerings-baserade funktions val][filter-based-feature-selection] och [analys av Fisher-discriminant][fisher-linear-discriminant-analysis].

![Moduler för funktions val](./media/select-features/feature-Selection.png)

Överväg till exempel användningen av modulen för [filtrerings-baserade funktions val][filter-based-feature-selection] . Fortsätt att använda text utvinnings exemplet för bekvämlighet. Anta att du vill skapa en Regressions modell efter att en uppsättning 256 funktioner har skapats via modulen för [funktions-hash][feature-hashing] och att variabeln respons är "Col1" som innehåller gransknings klassificeringen från 1 till 5. Genom att ange "funktions bedömnings metod" som "Pearson korrelation", måste mål kolumnen vara "Col1" och "antalet önskade funktioner" till 50. Sedan skapar modulen [filterbaserade funktions val][filter-based-feature-selection] en data uppsättning som innehåller 50-funktioner tillsammans med målattributet "Col1". Följande bild visar flödet för det här experimentet och indataparametrarna:

![Egenskaper för modul för val av funktion i Filter-Based](./media/select-features/feature-Selection1.png)

Följande bild visar de resulterande data uppsättningarna:

![Resulterande data uppsättning för filter baserat funktions val modul](./media/select-features/feature-Selection2.png)

Varje funktion betygs ätts baserat på Pearsons korrelation mellan sig och målattributet "Col1". De funktioner som har flest resultat behålls.

Motsvarande resultat för de valda funktionerna visas i följande figur:

![Poäng för filtrering baserat funktions val modul](./media/select-features/feature-Selection3.png)

Genom att använda den här [filterbaserade modulen för funktions val][filter-based-feature-selection] är 50 av 256 funktioner markerade eftersom de har de mest korrelerade funktionerna med Target-variabeln "Col1", baserat på bedömnings metoden "Pearson korrelation".

## <a name="conclusion"></a>Slutsats
Funktions teknik och funktions val är två vanliga och valda funktioner som ökar effektiviteten i inlärnings processen som försöker extrahera nyckelinformation som finns i data. De kan också förbättra kraften i dessa modeller för att klassificera indata på ett korrekt sätt och förutsäga resultat av intresse mer robust. Funktions teknik och urval kan också kombineras för att göra inlärningen mer beräknings bara. Det gör det genom att förbättra och minska antalet funktioner som behövs för att kalibrera eller träna en modell. De funktioner som har valts för att träna modellen är en minimal uppsättning oberoende variabler som förklarar mönstren i data och förutsäger sedan resultatet.

Det är inte alltid nödvändigt att utföra funktion teknik eller val av funktioner. Vare sig det behövs eller inte beror på insamlade data, algoritmen som valts och syftet med experimentet.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[fisher-linear-discriminant-analysis]: /azure/machine-learning/studio-module-reference/fisher-linear-discriminant-analysis