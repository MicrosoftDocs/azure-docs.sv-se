---
title: 'Förstärkt besluts träds regression: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Regressions modulen för besluts träd i Azure Machine Learning för att skapa en ensemble med Regressions träd med förstärkning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 664943fc5535883b3df77b2795383e5c0586a71c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94375337"
---
# <a name="boosted-decision-tree-regression-module"></a>Regressions modul för besluts träd

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en ensemble of Regressions träd med förstärkning. *Förstärkning* innebär att varje träd är beroende av föregående träd. Algoritmen lär sig genom att anpassa resten av de träd som föregår det. Därför är det bättre att öka noggrannheten i besluts trädets ensemblen för att förbättra noggrannheten med liten risk för mindre täckning.  

Den här modulen är baserad LightGBM-algoritm.
  
Den här Regressions metoden är en övervakad inlärnings metod och kräver därför en *etikettad data uppsättning*. Etikett kolumnen måste innehålla numeriska värden.  

> [!NOTE]
> Använd endast den här modulen med data uppsättningar som använder numeriska variabler.  

När du har definierat modellen tränar du den med hjälp av [träna-modellen](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Mer om Boosted Regressions träd  

Förstärkning är en av flera klassiska metoder för att skapa Ensemble-modeller, tillsammans med bagage, slumpmässiga skogar och så vidare.  I Azure Machine Learning använder förstärkta besluts träd en effektiv implementering av algoritmen för MART-gradient-Boost. Tonings förstärkning är en maskin inlärnings teknik för Regressions problem. Det skapar varje Regressions träd i ett steg-ett steg, med en fördefinierad förlust funktion för att mäta felet i varje steg och korrigera det i nästa steg. Därför är förutsägelse modellen faktiskt en ensemble med svagare förutsägelse modeller.  
  
Vid Regressions problem skapar förstärkningen en serie träd i ett stegvist sätt och väljer sedan det optimala trädet med en godtycklig differentiable-förlust funktion.  
  
Mer information finns i följande artiklar:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Den här Wikipedia-artikeln om tonings förstärkning ger viss bakgrund i förstärkta träd. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: från RankNet till LambdaRank till LambdaMART: en översikt. Av J.C. Burges.

Metoden för tonings förstärkning kan också användas för klassificerings problem genom att minska dem till regression med lämplig förlust funktion. Mer information om implementerade träd för klassificerings aktiviteter finns i [besluts trädet i två klasser](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Så här konfigurerar du Regressions trädet för besluts träd

1.  Lägg till modulen för **besluts träd med högre** till din pipeline. Du hittar den här modulen under **Machine Learning**, **initiera** under **Regressions** kategorin. 
  
2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: Välj det här alternativet om du vet hur du vill konfigurera modellen och ange en viss uppsättning värden som argument. 
     
    -   **Parameter intervall**: Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra en parameter rensning. Välj ett värde intervall som du vill iterera över, och de [finjusterande modellens egenskaper](tune-model-hyperparameters.md) upprepas över alla möjliga kombinationer av de inställningar som du angav för att fastställa de egenskaper som ger optimala resultat.    
   
  
3. **Maximalt antal löv per träd**: Ange det maximala antalet terminalservrar (löv) som kan skapas i alla träd.  

    Genom att öka det här värdet kan du öka storleken på trädet och få bättre precision vid överanpassning och längre inlärnings tid.  

4. **Minsta antal sampel per lövnod**: Ange det minsta antal fall som krävs för att skapa en terminalsession (löv) i ett träd.

    Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst 5 fall som uppfyller samma villkor.

5. **Inlärnings pris**: Ange ett tal mellan 0 och 1 som definierar steg storleken under inlärningen. Inlärnings frekvensen avgör hur snabbt eller långsamt en elev konvergerar på den optimala lösningen. Om steg storleken är för stor kanske du överbelastar den optimala lösningen. Om steg storleken är för liten tar inlärningen längre tid att konvergera i den bästa lösningen.

6. **Antal skapade träd**: Ange det totala antalet besluts träd som ska skapas i ensemblen. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden ökar.

    Om du ställer in värdet på 1; dock skapas endast ett träd (trädet med den inledande uppsättningen parametrar) och inga ytterligare iterationer utförs.

7. Värde för **slumpmässig siffra**: Ange ett valfritt icke-negativt heltal som ska användas som det slumpmässiga startvärdet. Genom att ange ett utsäde säkerställer du reproducerbarhet i körningar som har samma data och parametrar.

    Som standard är det slumpmässiga startvärdet inställt på 0, vilket innebär att det första startvärdet hämtas från system klockan.
  

9. Träna modellen:

    + Om du ställer in **skapa utbildare** för en **parameter** ansluter du en taggad data uppsättning och modulen [träna modell](train-model.md) .  
  
    + Om du ställer in **skapa utbildare** för **parameter intervall** ansluter du en taggad data uppsättning och tränar modellen med hjälp av [finjustera modellens egenskaper](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Om du skickar ett parameter intervall för att [träna modellen](train-model.md), används bara standardvärdet i listan med en parameter.  
    > 
    > Om du skickar en enda uppsättning parameter värden till modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) , ignorerar värdena och använder standardvärdena för eleven när den förväntar sig ett intervall med inställningar för varje parameter.  
    > 
    > Om du väljer alternativet **parameter intervall** och anger ett enda värde för en parameter, används det enskilda värdet som du har angett i hela svepet, även om andra parametrar ändras i ett intervall med värden.
    

10. Skicka pipelinen.  
  
## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill använda modellen för poängsättning ansluter du [träna modell](train-model.md) till [Scores-modellen](./score-model.md)för att förutsäga värden för nya exempel på ingångar.

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du fliken **utdata** i den högra panelen i den **tränade modellen** och klickar på ikonen **registrera data uppsättning** . Kopian av den tränade modellen sparas som en modul i modul trädet och kommer inte att uppdateras vid efterföljande körningar av pipelinen.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
