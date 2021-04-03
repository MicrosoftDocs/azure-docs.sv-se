---
title: 'Snabb skog Quantile regression: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder den snabba Quantile regression-modulen i skogar för att skapa en Regressions modell som kan förutsäga värden för ett angivet antal quantiles.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6d59d2fabb0b74ef12e33f55467b0ba68e0b7386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907962"
---
# <a name="fast-forest-quantile-regression"></a>Fast Forest Quantile Regression

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en fast skogs quantile Regressions modell i en pipeline. Snabb skogs quantile regression är användbart om du vill veta mer om distributionen av det förväntade värdet, i stället för att få ett enda genomsnitts förutsägelse värde. Den här metoden har många program, inklusive:  
  
- Förutsäga priser  
  
- Uppskatta student prestanda eller använda tillväxt diagram för att utvärdera den underordnade utvecklingen  
  
- Identifiera förutsägelse relationer i fall där det bara finns en svag relation mellan variabler  
  
Den här Regressions algoritmen är en **övervakad** inlärnings metod, vilket innebär att den kräver en taggad data uppsättning som innehåller en etikett kolumn. Eftersom det är en Regressions algoritm får inte etikett kolumnen innehålla numeriska värden.

## <a name="more-about-quantile-regression"></a>Mer om quantile regression

Det finns många olika typer av regression. Bara genom att använda regression innebär att en modell läggs till i ett mål uttryckt som en numerisk Vector. Statistiker har dock utvecklat alltmer avancerade metoder för regression.

Den enklaste definitionen av *quantile* är ett värde som delar upp en uppsättning data i grupper med samma storlek. Därför markerar quantile-värdena gränserna mellan grupper. Quantiles är värden som tas med jämna mellanrum från inversen av den kumulativa fördelnings funktionen (CDF) i en slumpmässig variabel.

Med linjära Regressions modeller görs ett försök att förutsäga värdet för en numerisk variabel med en enda uppskattning, vilket *innebär* att du ibland måste förutsäga intervallet eller hela fördelningen av mål variabeln. Tekniker som Bayesian regression och quantile regression har utvecklats för detta ändamål.

Quantile regression hjälper dig att förstå distributionen av det förväntade värdet. Tree-baserade quantile Regressions modeller, till exempel den som används i den här modulen, har den extra fördelen att de kan användas för att förutsäga icke-parameter distributioner.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>Konfigurera en fast skog Quantile regression

1. Lägg till den **snabbt Quantile regression** -modulen i din pipeline i designern. Du hittar den här modulen under **Machine Learning algoritmer** i **Regressions** kategorin.

2. I den högra rutan i den **snabba skogen Quantile regression** -modulen anger du hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    - **Enskild parameter**: Ange en viss uppsättning värden som argument om du vet hur du vill konfigurera modellen. När du tränar modellen använder du [träna modell](train-model.md).
  
    - **Parameter intervall**: om du inte är säker på de bästa parametrarna gör du en parameter rensning med hjälp av modulen för att [finjustera modellens standardparametrar](tune-model-hyperparameters.md) . Utbildaren itererar över flera värden som du anger för att hitta den optimala konfigurationen.

3. **Antal träd**, ange det maximala antalet träd som kan skapas i ensemblen. Om du skapar fler träd, leder det vanligt vis till större precision, men i kostnad av längre utbildnings tid.  

4. **Antal löv**, ange det maximala antalet löv eller terminalserversessioner som kan skapas i alla träd.  

5. **Minsta antal utbildnings instanser som krävs för att bilda ett löv**, ange det minsta antal exempel som krävs för att skapa en Terminal-nod (löv) i ett träd.  
  
     Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst 5 fall som uppfyller samma villkor.

6. **Bagage-bråk**, ange ett tal mellan 0 och 1 som representerar fraktionen av de exempel som ska användas när du skapar varje grupp av quantiles. Exempel väljs slumpmässigt, med ersättnings.

7. **Delat bråk**, ange ett tal mellan 0 och 1 som representerar delar av de funktioner som ska användas i varje delning av trädet. De funktioner som används är alltid slumpmässigt markerade.

8. **Quantiles som ska uppskattas** skriver du en semikolonavgränsad lista med de Quantiles som du vill att modellen ska träna och skapa förutsägelser för.
  
     Om du till exempel vill bygga en modell som uppskattar för kvartilen skriver du `0.25; 0.5; 0.75` .  

9. Du kan också ange ett värde för ett **slumptal för slumpmässig siffra** för att dirigera den slumpmässiga nummer generatorn som används av modellen.  Standardvärdet är 0, vilket innebär att ett slumpmässigt utsäde väljs.
  
     Du bör ange ett värde om du behöver återskapa resultat mellan efterföljande körningar på samma data.  

10. Anslut inlärnings data uppsättningen och den inte tränade modellen till någon av inlärnings modulerna: 

    - Om du ställer in **skapa utbildare** för **en parameter** använder du modulen [träna modell](train-model.md) .

    - Om du ställer in **skapa utbildare läge** till **parameter intervall** använder du modulen för att [finjustera modellens egenskaper](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Om du skickar ett parameter intervall för att [träna modellen](train-model.md), används bara det första värdet i parameter intervall listan.
    > 
    > - Om du skickar en enda uppsättning parameter värden till modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) , ignoreras värdena och standardvärdena används för eleven när en uppsättning inställningar förväntas för varje parameter.
    > 
    > - Om du väljer alternativet **parameter intervall** och anger ett enda värde för en parameter, används det enskilda värdet som du har angett i hela svepet, även om andra parametrar ändras i ett intervall med värden.

11. Skicka pipelinen.

## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du modulen utbildning och växlar sedan till fliken **utdata + loggar** i den högra panelen. Klicka på ikonen **registrera data uppsättning**.  Du hittar den sparade modellen som en modul i modul trädet.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.
