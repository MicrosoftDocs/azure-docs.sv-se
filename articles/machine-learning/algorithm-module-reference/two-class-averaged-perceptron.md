---
title: 'Two-Class genomsnittlig Perceptron: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder den Two-Class genomsnittlig Perceptron-modulen i designern för att skapa en binär klassificerare.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421149"
---
# <a name="two-class-averaged-perceptron-module"></a>Two-Class genomsnittlig Perceptron-modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en Machine Learning-modell som baseras på den genomsnittliga Perceptron-algoritmen.  
  
Den här klassificerings algoritmen är en övervakad inlärnings metod och kräver en *Taggad data uppsättning* som innehåller en etikett kolumn. Du kan träna modellen genom att tillhandahålla modellen och den taggade data uppsättningen som indata för att [träna modellen](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya ingångs exemplen.  

### <a name="about-averaged-perceptron-models"></a>Om Genomsnittligt antal Perceptron-modeller

Den *genomsnittliga Perceptron-metoden* är en tidig och enkel version av ett neurala-nätverk. I den här metoden klassificeras indata i flera möjliga utdata baserat på en linjär funktion och kombineras sedan med en uppsättning vikter som härleds från funktions vektorn, och därför namnet "Perceptron".

De enklare Perceptron-modellerna är lämpliga för inlärning av linjärt särskiljande mönster, medan neurala-nätverk (särskilt djup neurala-nätverk) kan modellera mer komplexa klass gränser. Perceptrons är dock snabbare, och eftersom de behandlar ärenden seriellt kan perceptrons användas med kontinuerlig utbildning.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Så här konfigurerar du Two-Class genomsnittlig Perceptron

1.  Lägg till den **genomsnittliga Perceptron-modulen med två klasser** i din pipeline.  

2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: Ange en viss uppsättning värden som argument om du vet hur du vill konfigurera modellen.

    -   **Parameter intervall**: Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra en parameter rensning. Välj ett värde intervall som du vill iterera över, och de [finjusterande modellens egenskaper](tune-model-hyperparameters.md) upprepas över alla möjliga kombinationer av de inställningar som du angav för att fastställa de egenskaper som ger optimala resultat.  
  
3.  För **inlärnings frekvens** anger du ett värde för *inlärnings takten*. Inlärnings takt svärdet styr storleken på det steg som används i Stochastic gradient-brantaste varje gång modellen testas och korrigeras.
  
     Genom att göra hastigheten mindre testar du modellen oftare, med den risk att du kan fastna i en lokal platå. Genom att göra steget större kan du konvergera snabbare, vid risken för att den sanna minimi överskrids.
  
4.  För **maximalt antal iterationer** anger du hur många gånger du vill att algoritmen ska undersöka tränings data.  
  
     Att stoppa tidigt ger ofta bättre generalisering. Genom att öka antalet iterationer kan du förbättra anpassningen vid risk för överanpassning.
  
5.  Om du vill använda **slumpmässigt antal frön** kan du ange ett heltals värde som ska användas som start värde. Vi rekommenderar att du använder ett Dirigerings värde om du vill säkerställa att pipelinen löper över.  
  
1.  Anslut en data uppsättning för utbildning och träna modellen:

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