---
title: 'Permutations funktions prioritet: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen permutations prioritet i designern för att beräkna permutations funktionens prioritets resultat för funktions variabler.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: 8ae1e79922cc0f34e8b2d1f253fce5078df286d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421251"
---
# <a name="permutation-feature-importance"></a>Permutationfunktionsprioritet

Den här artikeln beskriver hur du använder modulen permutations prioritet i Azure Machine Learning designer för att beräkna en uppsättning funktions resultat för din data uppsättning. Du kan använda de här poängen för att avgöra vilka funktioner som ska användas i en modell.

I den här modulen är funktions värden slumpmässigt blandade, en kolumn i taget. Modellens prestanda mäts före och efter. Du kan välja något av standard måtten för att mäta prestanda.

Poängen som modulen returnerar representerar *ändringen* av prestandan för en utbildad modell efter permutation. Viktiga funktioner är ofta mer känsliga för blandning-processen, så de resulterar i högre prioritets poäng. 

Den här artikeln innehåller en översikt över permutation-funktionen, dess teoretiska bas och dess program i Machine Learning: [permutation-funktionen är viktig](/archive/blogs/machinelearning/permutation-feature-importance).  

## <a name="how-to-use-permutation-feature-importance"></a>Så här använder du permutations funktions prioritet

Att generera en uppsättning funktions resultat kräver att du har en redan utbildad modell, samt en test data uppsättning.  

1.  Lägg till modulen permutations prioritet i din pipeline. Du hittar den här modulen i kategorin **funktions val** . 

2.  Anslut en tränad modell till den vänstra Indataporten. Modellen måste vara en Regressions modell eller en klassificerings modell.  

3.  Anslut en data uppsättning till rätt indata. Helst väljer du en som skiljer sig från den data uppsättning som du använde för att träna modellen. Den här data uppsättningen används för resultat som baseras på den tränade modellen. Den används också för att utvärdera modellen efter att funktions värden har ändrats.  

4.  Ange ett värde som ska användas som ett Dirigerings värde för **slumpmässigt utsäde**. Om du anger 0 (standard) genereras en siffra baserat på system klockan.

     Ett Seed-värde är valfritt, men du bör ange ett värde om du vill ha reproducerbarhet i samma pipeline.  

5.  För Mät **värden för att mäta prestanda** väljer du ett mått som ska användas när du beräknar modell kvalitet efter permutation.  

     Azure Machine Learning Designer stöder följande mått, beroende på om du utvärderar en klassificerings-eller Regressions modell:  

    -   **Klassificering**

        Precision, precision, återkallande  

    -   **Regression**

        Precision, återkalla, medels absolut fel, rot genomsnitts fel, relativt absolut fel, relativt kvadratvärde, koefficient för bestämning  

     En mer detaljerad beskrivning av dessa utvärderings mått och hur de beräknas finns i [utvärdera modell](evaluate-model.md).  

6.  Skicka pipelinen.  

7.  Modulen matar ut en lista över funktions kolumner och poängen som är kopplade till dem. Listan rangordnas i fallande ordning i poängen.  


##  <a name="technical-notes"></a>Tekniska anteckningar

Prioritet för permutations funktionen fungerar genom att slumpmässigt ändra värdena för varje funktions kolumn, en kolumn i taget. Den utvärderar sedan modellen. 

De rankninger som modulen tillhandahåller är ofta olika än de som du får från [filtrering baserat funktions val](filter-based-feature-selection.md). Filtrera baserat funktions val beräknar poängen *innan* en modell skapas. 

Orsaken till skillnaden är att permutations funktionens prioritet inte mäter associationen mellan en funktion och ett mål värde. I stället inhämtas hur mycket inverkan varje funktion har på förutsägelser från modellen.
  
## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.