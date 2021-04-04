---
title: 'Gruppera data till lager platser: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder grupp data i modulen lager platser för att gruppera nummer eller ändra distributionen av kontinuerliga data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/13/2020
ms.openlocfilehash: 392cb9b4c2ded1b98b79ce8dcd780ac59e96b78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91998479"
---
# <a name="group-data-into-bins-module"></a>Gruppera data i modulen lager

Den här artikeln beskriver hur du använder grupp data i modulen lager platser i Azure Machine Learning designer, för att gruppera nummer eller ändra distributionen av kontinuerliga data.

Grupp data till modulen lager platser stöder flera alternativ för diskretisering-data. Du kan anpassa hur lager plats kanter är inställda och hur värdena fördelas på lager platserna. Du kan till exempel:  

+ Ange en serie värden manuellt för att fungera som lager plats gränser.  
+ Tilldela värden till lager platser genom att använda *quantiles*-eller percentils rang.  
+ Tvinga en jämn fördelning av värden till lager platserna.  

## <a name="more-about-binning-and-grouping"></a>Mer om diskretisering och gruppering

*Diskretisering* eller gruppering av data (kallas ibland *kvantifieringsfel*) är ett viktigt verktyg för att förbereda numeriska data för maskin inlärning. Det är användbart i scenarier som dessa:

+ En kolumn med kontinuerliga tal har för många unika värden för att modellera effektivt. Så att du automatiskt eller manuellt tilldelar värden till grupper, för att skapa en mindre uppsättning diskreta intervall.

+ Du vill ersätta en kolumn med tal med kategoriska-värden som representerar vissa intervall.

    Du kanske vill gruppera värden i en ålders kolumn genom att ange anpassade intervall, till exempel 1-15, 16-22, 23-30, och så vidare för användar demografiska data.

+ En data uppsättning har några extrema värden, helt utanför det förväntade intervallet, och dessa värden har en storlek som påverkar den tränade modellen. För att minimera förskjutningen i modellen kan du omvandla data till en enhetlig distribution med hjälp av metoden quantiles.

    Med den här metoden fastställer grupp data i modulen lager platser de idealiska lager platserna och lager plats bredderna för att säkerställa att ungefär samma antal exempel hamnar på varje lager plats. Sedan, beroende på vilken normaliserings metod du väljer, omvandlas värdena i lager platserna antingen till percentiler eller mappas till ett lager plats nummer.

### <a name="examples-of-binning"></a>Exempel på diskretisering

Följande diagram visar distributionen av numeriska värden före och efter diskretisering med *quantiles* -metoden. Observera att data har diskretiseras och omvandlats till en enhets normal skala jämfört med rå data till vänster.  

> [!div class="mx-imgBorder"]
> ![Resultat visualisering](media/module/group-data-into-bins-result-example.png)

Eftersom det finns många sätt att gruppera data på, rekommenderar vi att du experimenterar med olika metoder och värden. 

## <a name="how-to-configure-group-data-into-bins"></a>Så här konfigurerar du grupp data till lager platser

1. Lägg till **grupp data i modulen lager** i din pipeline i designern. Du hittar den här modulen i kategorin **data omvandling**.

2. Anslut den data uppsättning som har numeriska data till bin. Kvantifieringsfel kan bara användas för kolumner som innehåller numeriska data. 

    Om data uppsättningen innehåller icke-numeriska kolumner använder du modulen [Välj kolumner i data uppsättning](select-columns-in-dataset.md) för att välja en delmängd med kolumner att arbeta med.

3. Ange diskretisering-läge. Diskretisering-läget bestämmer andra parametrar, så se till att välja alternativet **diskretisering läge** först. Följande typer av diskretisering stöds:

    - **Quantiles**: quantile-metoden tilldelar värden till lager platser baserat på percentils rang. Den här metoden kallas även diskretisering för samma höjd.

    - **Samma bredd**: med det här alternativet måste du ange det totala antalet lager platser. Värdena från data kolumnen placeras på lager platserna så att varje lager plats har samma intervall mellan start-och slut värden. Därför kan vissa lager platser ha fler värden om data är clumped kring en viss tidpunkt.

    - **Anpassade kanter**: du kan ange de värden som börjar varje lager plats. Värdet Edge är alltid den nedre gränsen för lager platsen. 
    
      Anta till exempel att du vill gruppera värden i två lager platser. En har värden som är större än 0 och en har värden som är mindre än eller lika med 0. I det här fallet anger du **0** i en **kommaavgränsad lista över lager plats kanter** för plats kanter. Utdata från modulen är 1 och 2, vilket anger lager plats indexet för varje rad värde. Observera att listan med kommaavgränsade värden måste vara i stigande ordning, till exempel 1, 3, 5, 7.
    
    > [!Note]
    > *Entropi mdl* -läge definieras i Studio (klassisk) och det finns inget motsvarande paket med öppen källkod som kan utnyttjas för att stödja i designern ännu.        

4. Om du använder diskretisering-lägena **Quantiles** och **samma bredd** använder du alternativet **antal lager platser** för att ange hur många lager platser eller *Quantiles* som du vill skapa.

5. För **kolumner till bin** använder du kolumn väljaren för att välja de kolumner som har de värden som du vill använda för bin. Kolumner måste vara en numerisk datatyp.

    Samma diskretisering-regel tillämpas på alla tillämpliga kolumner som du väljer. Om du behöver använda en annan metod för att lägga till binge i vissa kolumner använder du en separat instans av grupp data i modulen lager platser för varje uppsättning kolumner.

    > [!WARNING]
    > Om du väljer en kolumn som inte är en tillåten typ genereras ett körnings fel. Modulen returnerar ett fel så fort den hittar en kolumn av en otillåten typ. Om du får ett fel meddelande granskar du alla valda kolumner. Felet visar inte alla ogiltiga kolumner.

6. I **output-läge** anger du hur du vill att quantized-värdena ska matas ut:

    + **Lägg till**: skapar en ny kolumn med diskretiseras-värdena och lägger till dem i tabellen indatamängd.

    + **InPlace**: ersätter de ursprungliga värdena med de nya värdena i data uppsättningen.

    + **ResultOnly**: returnerar bara resultat kolumnerna.

7. Om du väljer **Quantiles** diskretisering-läget använder du alternativet **Quantile normalisering** för att avgöra hur värdena normaliseras innan du sorterar i Quantiles. Observera att normaliserade värden omvandlar värdena men påverkar inte det slutliga antalet lager platser.

    Följande normaliserings typer stöds:

    + **Procent**: värdena är normaliserade inom intervallet [0100].

    + **PQuantile**: värdena är normaliserade inom intervallet [0, 1].

    + **QuantileIndex**: värdena är normaliserade inom intervallet [1, antal lager platser].

8. Om du väljer alternativet **anpassade kanter** anger du en kommaavgränsad lista med siffror som ska användas som *lager plats kanter* i text rutan **kommaavgränsad lista över lager plats kanter** . 

    Värdena markerar den punkt som delar upp lager platser. Om du till exempel anger ett gräns värde för lager plats skapas två lager platser. Om du anger två gräns värden för lager plats skapas tre lager platser.

    Värdena måste sorteras i den ordning som lager platserna skapas, från lägsta till högsta.

10. Välj alternativet **tag columns as kategoriska** för att indikera att quantized-kolumnerna ska hanteras som kategoriska-variabler.

11. Skicka pipelinen.

## <a name="results"></a>Resultat

Grupp data till modulen lager platser returnerar en data uppsättning där varje element har diskretiseras enligt det angivna läget. 

Den returnerar också en *diskretisering-omvandling*. Funktionen kan skickas till modulen [Använd omvandling](apply-transformation.md) till bin nya exempel på data med hjälp av samma diskretisering-läge och parametrar.  

> [!TIP]
> Om du använder diskretisering på dina utbildnings data måste du använda samma diskretisering-metod för data som används för testning och förutsägelse. Du måste också använda samma lager plats platser och lager plats bredder. 
> 
> För att säkerställa att data alltid omvandlas med hjälp av samma diskretisering-metod rekommenderar vi att du sparar användbara data transformationer. Tillämpa dem sedan på andra data uppsättningar med hjälp av modulen [Använd omvandling](apply-transformation.md) .

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
