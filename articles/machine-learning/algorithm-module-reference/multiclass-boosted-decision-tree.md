---
title: 'Besluts träd med djup klass: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen för besluts träd med multiklass i Azure Machine Learning för att skapa en klassificerare med märkta data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 1a1cb7661ae01dd89d45afe004978813ac90eaff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905296"
---
# <a name="multiclass-boosted-decision-tree"></a>Förbättrat beslutsträd med flera klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en Machine Learning-modell som baseras på algoritmen för besluts träd.

Ett utökat besluts träd är en ensemble-utbildnings metod där det andra trädet korrigerar fel i det första trädet, och det tredje trädet korrigeras för felen i de första och andra träden och så vidare. Förutsägelser bygger på trädets ensembler tillsammans.

## <a name="how-to-configure"></a>Så här konfigurerar du 

Den här modulen skapar en modell som inte är tränad. Eftersom klassificeringen är en övervakad inlärnings metod behöver du en *etikettad data uppsättning* som innehåller en etikett kolumn med ett värde för alla rader.

Du kan träna den här typen av modell med hjälp av [träna-modellen](././train-model.md). 

1.  Lägg till modulen **besluts träd med multiklass** i din pipeline.

1.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet för att **skapa utbildare läge** .

    + **Enskild parameter**: om du vet hur du vill konfigurera modellen kan du ange en viss uppsättning värden som argument.
    
    + **Parameter intervall**: Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra en parameter rensning. Välj ett värde intervall som du vill iterera över, och de [finjusterande modellens egenskaper](tune-model-hyperparameters.md) upprepas över alla möjliga kombinationer av de inställningar som du angav för att fastställa de egenskaper som ger optimala resultat.  

1. **Maximalt antal löv per träd** begränsar det maximala antalet terminalserversessioner (löv) som kan skapas i alla träd.
    
    Genom att öka det här värdet kan du öka storleken på trädet och uppnå högre precision, samtidigt som det är risk för överanpassning och längre inlärnings tid.
  
1. **Minsta antal sampel per lövnod** anger antalet fall som krävs för att skapa en terminalserversession (löv) i ett träd.  

    Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst fem fall som uppfyller samma villkor.

1. **Inlärnings takten** definierar steg storleken under inlärningen. Ange ett tal mellan 0 och 1.

    Inlärnings frekvensen avgör hur snabbt eller långsamt en elev konvergerar i en optimal lösning. Om steg storleken är för stor kan du överskrida den optimala lösningen. Om steg storleken är för liten tar inlärningen längre tid att konvergera i den bästa lösningen.

1. **Antal träd konstruktioner** anger det totala antalet besluts träd som ska skapas i ensemblen. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden ökar.

1. Med alternativet för **slumpmässigt nummer** kan du ange ett icke-negativt heltal som ska användas som slumpmässigt Seed-värde. Genom att ange ett utsäde säkerställer du reproducerbarhet i körningar som har samma data och parametrar.  

    Det slumpmässiga startvärdet anges som standard till 42. Efterföljande körningar som använder olika slumpmässiga frön kan ha olika resultat.

1. Träna modellen:

    + Om du ställer in **skapa utbildare** för en **parameter** ansluter du en taggad data uppsättning och modulen [träna modell](train-model.md) .  
  
    + Om du ställer in **skapa utbildare** för **parameter intervall** ansluter du en taggad data uppsättning och tränar modellen med hjälp av [finjustera modellens egenskaper](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Om du skickar ett parameter intervall för att [träna modellen](train-model.md), används bara standardvärdet i listan med en parameter.  
    > 
    > Om du skickar en enda uppsättning parameter värden till modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) , ignorerar värdena och använder standardvärdena för eleven när den förväntar sig ett intervall med inställningar för varje parameter.  
    > 
    > Om du väljer alternativet **parameter intervall** och anger ett enda värde för en parameter, används det enskilda värdet som du har angett i hela svepet, även om andra parametrar ändras i ett intervall med värden.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
