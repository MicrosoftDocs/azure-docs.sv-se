---
title: 'Filtrera baserat funktions val: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder funktionen filtrera baserat funktions val i Azure Machine Learning för att identifiera funktionerna i en data uppsättning med den största förutsägelse kraften.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f4a7f5581703ae6932f3b40e62085fed76f5e6f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91945710"
---
# <a name="filter-based-feature-selection"></a>Filterbaserat funktionsval

Den här artikeln beskriver hur du använder modulen filtrerad funktions val i Azure Machine Learning designer. Den här modulen hjälper dig att identifiera kolumnerna i din indata-datauppsättning som har den största förutsägelse kraften. 

I allmänhet avser *funktions val* processen att tillämpa statistiska tester på indata, med en angiven utmatning. Målet är att avgöra vilka kolumner som är mer förutsägbara för utdata. I modulen filter-baserad funktions val finns flera olika funktioner för val av funktioner som du kan välja mellan. Modulen innehåller korrelations metoder som Pearson-korrelation och chi2-värden. 

När du använder funktionen filtrera baserat funktions val anger du en data uppsättning och identifierar kolumnen som innehåller etiketten eller den beroende variabeln. Sedan anger du en enda metod som ska användas för att mäta funktions prioritet.

Modulen matar ut en data uppsättning som innehåller de bästa funktions kolumnerna som rankas av förutsägande kraft. Den ger också ut namnen på funktionerna och deras resultat från det valda måttet.  

## <a name="what-filter-based-feature-selection-is"></a>Vilket filter-baserat funktions val är  

Den här modulen för val av funktioner kallas "filterbaserade" eftersom du använder det valda måttet för att hitta irrelevanta attribut. Sedan filtrerar du bort överflödiga kolumner från modellen. Du väljer ett enda statistiskt mått som passar dina data och modulen beräknar en poäng för varje funktions kolumn. Kolumnerna returneras rangordnade efter deras funktions resultat. 

Genom att välja rätt funktioner kan du eventuellt förbättra klassificeringens exakthet och effektivitet. 

Du använder vanligt vis bara kolumnerna med de bästa poängen för att bygga en förutsägelse modell. Kolumner med dålig funktions val kan bli kvar i data uppsättningen och ignoreras när du skapar en modell.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Hur man väljer mått för funktions val

Modulen Filter-Based funktion val innehåller en mängd olika mått för att utvärdera värdet i varje kolumn. Det här avsnittet innehåller en allmän beskrivning av varje mått och hur det används. Du hittar ytterligare krav för att använda varje mått i [tekniska anteckningar](#technical-notes) och i [anvisningarna](#how-to-configure-filter-based-feature-selection) för att konfigurera varje modul.

-   **Pearson-korrelation**  

    Pearsons korrelations statistik, eller Pearsons korrelations koefficient, är också känd i statistiska modeller som `r` värde. För två variabler returnerar den ett värde som anger styrkan för korrelationen.

    Korrelationskoefficienten till Pearson är beräknad genom att ta den kovariansen av två variabler och dividera med produkten av standard avvikelserna. Ändringar av skala i de två variablerna påverkar inte koefficienten.  

-   **Chi, kvadrat**  

    Det tvåvägs chi2-kvadrat-testet är en statistisk metod som mäter hur nära förväntade värden är faktiska resultat. Metoden förutsätter att variablerna är slumpmässiga och ritade från ett lämpligt exempel av oberoende variabler. Den resulterande Chi-kvadrat-statistiken anger hur långt resultat som kommer från det förväntade resultatet (slumpmässigt).  


> [!TIP]
> Om du behöver ett annat alternativ för den anpassade funktions urvals metoden använder du modulen [Kör R-skript](execute-r-script.md) . 

## <a name="how-to-configure-filter-based-feature-selection"></a>Så här konfigurerar du Filter-Based funktions val

Du väljer ett standard statistik mått. Modulen beräknar korrelationen mellan ett kolumn par: kolumnen etikett och en funktions kolumn.

1.  Lägg till modulen Filter-Based funktions val i din pipeline. Du hittar den i kategorin **funktions val** i designern.

2. Anslut en indatamängd som innehåller minst två kolumner som är potentiella funktioner.  

    För att säkerställa att en kolumn analyseras och att funktions poängen genereras, använder du modulen [Redigera metadata](edit-metadata.md) för att ange attributet **IsFeature** . 

    > [!IMPORTANT]
    > Se till att de kolumner som du har angett som indatamängd är möjliga funktioner. Till exempel har en kolumn som innehåller ett enda värde inget värde för information.
    >
    > Om du vet att vissa kolumner skulle medföra dåliga funktioner kan du ta bort dem från kolumn urvalet. Du kan också använda modulen [Redigera metadata](edit-metadata.md) för att flagga dem som **kategoriska**. 
3.  För **funktions bedömnings metod** väljer du någon av följande etablerade statistiska metoder som ska användas för att beräkna poängen.  

    | Metod              | Krav                             |
    | ------------------- | ---------------------------------------- |
    | Pearson-korrelation | Etiketten kan vara text eller numerisk. Funktioner måste vara numeriska. |
    Chi, kvadrat| Etiketter och funktioner kan vara text eller numeriska. Använd den här metoden för att beräkna funktioner i två kategoriska-kolumner.|

    > [!TIP]
    > Om du ändrar det valda måttet så återställs alla andra val. Var noga med att ange det här alternativet först.
4.  Välj alternativet för att **endast använda funktions kolumner** om du vill generera en poäng för kolumner som tidigare har marker ATS som funktioner. 

    Om du avmarkerar det här alternativet skapar modulen en poäng för alla kolumner som på annat sätt uppfyller villkoren, upp till antalet kolumner som anges i **antal önskade funktioner**.  

5.  För **kolumnen mål** väljer du **Starta kolumn väljare** för att välja etikettens kolumn antingen efter namn eller index. (Index är ett-baserat.)  
    En etikett kolumn krävs för alla metoder som inbegriper statistisk korrelation. Modulen returnerar ett design tids fel om du väljer ingen etikett kolumn eller flera etikett kolumner. 

6.  För **antal önskade funktioner** anger du det antal funktions kolumner som du vill ska returneras som ett resultat:  

    - Det minsta antalet funktioner som du kan ange är ett, men vi rekommenderar att du ökar det här värdet.  

    - Om det angivna antalet önskade funktioner är större än antalet kolumner i data uppsättningen returneras alla funktioner. Även funktioner med noll resultat returneras.  

    - Om du anger färre resultat kolumner än det finns funktions kolumner rangordnas funktionerna efter fallande poäng. Endast de översta funktionerna returneras. 

7.  Skicka pipelinen.

> [!IMPORTANT]
> Om du ska använda **filter baserat funktions val** i en härlednings funktion måste du använda funktionen [Välj kolumner](./select-columns-transform.md) för att lagra det valda resultatet och [tillämpa omvandlingen](./apply-transformation.md) för att tillämpa den valda omvandlingen på poängsättnings data uppsättningen.
>
> Se följande skärm bild för att bygga din pipeline för att se till att kolumn valen är desamma för bedömnings processen.
> [!div class="mx-imgBorder"]
> ![Exempel på pipeline](media/module/filter-based-feature-selection-score.png)

## <a name="results"></a>Resultat

När bearbetningen är klar:

+ Om du vill se en fullständig lista över de analyserade funktions kolumnerna och deras resultat högerklickar du på modulen och väljer **visualisera**.  

+ Om du vill visa data uppsättningen baserat på ditt val av funktions villkor högerklickar du på modulen och väljer **visualisera**. 

Om data uppsättningen innehåller färre kolumner än förväntat, kontrollerar du inställningarna för modulen. Kontrol lera också data typerna för de kolumner som angetts som indata. Om du till exempel anger **antalet önskade funktioner** till 1 innehåller den utgående data uppsättningen bara två kolumner: kolumnen etikett och den mest rankade funktions kolumnen.


##  <a name="technical-notes"></a>Tekniska anteckningar  

### <a name="implementation-details"></a>Implementeringsdetaljer

Om du använder Pearson-korrelation på en numerisk funktion och en kategoriska-etikett, beräknas funktions poängen på följande sätt:  

1.  Beräkna det villkorliga medelvärdet för en numerisk kolumn för varje nivå i kolumnen kategoriska.  

2.  Korrelera kolumnen med villkorliga medelvärden med den numeriska kolumnen.  

### <a name="requirements"></a>Krav  

-   Det går inte att generera en funktions markerings Poäng för en kolumn som har angetts som en **etikett** eller en **score** -kolumn.  

-   Om du försöker använda en bedömnings metod med en kolumn av en datatyp som metoden inte stöder, kommer modulen att generera ett fel. Eller så kommer ett noll poäng att tilldelas till kolumnen.  

-   Om en kolumn innehåller logiska värden (sant/falskt) bearbetas de som `True = 1` och `False = 0` .  

-   En kolumn kan inte vara en funktion om den har angetts som en **etikett** eller **Poäng**.  

### <a name="how-missing-values-are-handled"></a>Hur värden som saknas hanteras  

-   Du kan inte ange som en mål kolumn (etikett) kolumn som innehåller alla värden som saknas.  

-   Om en kolumn innehåller saknade värden, ignorerar modulen dem när den beräknar poängen för kolumnen.  

-   Om en kolumn som har angetts som en funktions kolumn har alla värden som saknas tilldelar modulen noll poäng.   


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

