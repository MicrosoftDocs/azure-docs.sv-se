---
title: 'Two-Class utökat besluts träd: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Two-Class Boosted beslut Tree i designern för att skapa en binär klassificerare.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 810878956a89b6e7400be99692cf43925d1cd134
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94375935"
---
# <a name="two-class-boosted-decision-tree-module"></a>Two-Class utökat besluts träd

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en Machine Learning-modell som baseras på algoritmen för besluts träd. 

Ett utökat besluts träd är en ensemble-utbildnings metod där det andra trädet korrigerar fel i det första trädet, och det tredje trädet korrigeras för felen i de första och andra träden och så vidare. Förutsägelser baseras på hela ensemblen för träd som utgör förutsägelsen.
  
När rätt konfigurerade besluts träd är korrekt konfigurerade är de enkla metoder som du kan använda för att få bästa prestanda på en mängd olika Machine Learning-uppgifter. De är dock också en av de mer minnes intensiva eleverna och den aktuella implementeringen innehåller allt i minnet. Därför kanske en bättre besluts träd modell inte kan bearbeta de stora data mängderna som vissa linjära lärare kan hantera.

Den här modulen baseras på LightGBM-algoritmen.

## <a name="how-to-configure"></a>Så här konfigurerar du

Den här modulen skapar en modell som inte är tränad. Eftersom klassificeringen är en övervakad inlärnings metod behöver du en *Taggad data uppsättning* som innehåller en etikett kolumn med ett värde för alla rader för att träna modellen.

Du kan träna den här typen av modell med hjälp av [träna modell](././train-model.md). 

1.  I Azure Machine Learning lägger du till modulen för **besluts träd med högre** till din pipeline.
  
2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .
  
    + **Enskild parameter**: om du vet hur du vill konfigurera modellen kan du ange en viss uppsättning värden som argument.
  
    + **Parameter intervall**: om du inte är säker på de bästa parametrarna kan du hitta de optimala parametrarna med hjälp av modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) . Du anger några värden och utbildaren upprepas över flera kombinationer av inställningarna för att avgöra vilken kombination av värden som ger bäst resultat.
  
3.  För **maximalt antal löv per träd**, anger du det maximala antalet terminalservrar (löv) som kan skapas i alla träd.
  
     Genom att öka det här värdet kan du öka storleken på trädet och få bättre precision vid överanpassning och längre inlärnings tid.
  
4.  För **minsta antal exempel per lövnod**, anger du antalet fall som krävs för att skapa en terminalserversession (löv) i ett träd.  
  
     Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst fem fall som uppfyller samma villkor.
  
5.  För **inlärnings hastighet** anger du ett tal mellan 0 och 1 som definierar steg storleken under inlärningen.  
  
     Inlärnings frekvensen avgör hur snabbt eller långsamt en elev konvergerar på den optimala lösningen. Om steg storleken är för stor kan du överskrida den optimala lösningen. Om steg storleken är för liten tar inlärningen längre tid att konvergera i den bästa lösningen.
  
6.  Ange det totala antalet besluts träd som ska skapas i ensemblen för **antalet skapade träd**. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden ökar.
  
     Om du ställer in värdet på 1 skapas endast ett träd (trädet med den inledande uppsättningen parametrar) och inga ytterligare iterationer utförs.
  
7.  För **slumpmässigt antal frön** kan du ange ett icke-negativt heltal som ska användas som det slumpmässiga startvärdet. Genom att ange ett utsäde säkerställer du reproducerbarhet i körningar som har samma data och parametrar.  
  
     Det slumpmässiga startvärdet anges som standard till 0, vilket innebär att det första startvärdet hämtas från system klockan.  Efterföljande körningar med hjälp av ett slumpmässigt Seed kan ha olika resultat.
  

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
   
## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du fliken **utdata** i den högra panelen i modulen **träna modell** . Välj ikonen **registrera data uppsättning** för att spara modellen som en återanvändbar modul.

+ Om du vill använda modellen för poängsättning lägger du till modulen **Poäng modell** i en pipeline.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 