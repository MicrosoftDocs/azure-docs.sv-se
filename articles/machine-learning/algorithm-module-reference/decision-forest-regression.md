---
title: 'Besluts skogs regression: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder besluts skogs Regressions modulen i Azure Machine Learning för att skapa en Regressions modell som baseras på en ensemble i besluts träd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 76fd09b5e2c2540cbc1608558800e7897a6cf839
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94375371"
---
# <a name="decision-forest-regression-module"></a>Besluts skogs Regressions modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en Regressions modell som baseras på en ensemble i besluts träd.

När du har konfigurerat modellen måste du träna modellen med hjälp av en etikettad data uppsättning och modulen [träna modell](./train-model.md) . Den tränade modellen kan sedan användas för att göra förutsägelser. 

## <a name="how-it-works"></a>Så här fungerar det

Besluts träd är icke-parameter modeller som utför en sekvens med enkla tester för varje instans, och som påbörjar en binär träd data struktur tills en lövnod (beslut) har nåtts.

Besluts träd har följande fördelar:

- De är effektiva i både beräknings-och minnes användning vid utbildning och förutsägelse.

- De kan representera icke-linjära besluts gränser.

- De utför integrerade funktions val och klassificering och är elastiska i närvaro av brus funktioner.

Denna Regressions modell består av en ensemble i besluts träd. Varje träd i en Regressions besluts skog matar ut en Gaussisk-distribution som en förutsägelse. En agg regering utförs över ensemblen i träd för att hitta en Gaussisk-distribution som är närmast den kombinerade distributionen för alla träd i modellen.

Mer information om det teoretiska ramverket för den här algoritmen och dess implementering finns i den här artikeln: [besluts skogar: ett enhetligt ramverk för klassificering, regression, densitets beräkning, samlings inlärning och Semi-Supervised inlärning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Konfigurera Regressions modellen för besluts skogar

1. Lägg till **besluts skogs Regressions** modulen i pipelinen. Du kan hitta modulen i designern under **Machine Learning**, **initiera modellen** och **regression**.

2. Öppna egenskaperna för modulen och välj den metod som används för att skapa enskilda träd för **omsamplings metod**.  Du kan välja mellan **bagage** eller **Replikera**.

    - **Bagage**: bagage kallas även *Start agg regering*. Varje träd i en Regressions besluts skog matar ut en Gaussisk fördelning genom förutsägelse. Aggregation är att hitta en Gaussisk vars första två moment motsvarar momentet i blandningen av Gaussisk distributioner genom att kombinera alla distributioner som returneras av enskilda träd.

         Mer information finns i Wikipedia-posten för [Start agg regering](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikera**: i replikering tränas varje träd på exakt samma indata. Bestämning av vilket delat predikat som används för varje trädnod förblir slumpmässigt och träden är olika.

         Mer information om inlärnings processen med alternativet **Replikera** finns i [besluts skogar för visuellt innehåll och medicinsk bild analys. Criminisi och J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .

    - **Enskild parameter**

      Om du vet hur du vill konfigurera modellen kan du ange en speciell uppsättning värden som argument. Du kanske har lärt dig dessa värden genom att experimentera eller ta emot dem som vägledning.

    - **Parameter intervall**: Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra en parameter rensning. Välj ett värde intervall som du vill iterera över, och de [finjusterande modellens egenskaper](tune-model-hyperparameters.md) upprepas över alla möjliga kombinationer av de inställningar som du angav för att fastställa de egenskaper som ger optimala resultat. 



4. För **antalet besluts träd** anger du det totala antalet besluts träd som ska skapas i ensemblen. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden ökar.

    > [!TIP]
    > Om du ställer in värdet på 1; Det innebär dock att endast ett träd skapas (trädet med den inledande uppsättningen parametrar) och inga ytterligare iterationer utförs.

5. Ange ett tal för att begränsa det maximala djupet för besluts träden för att begränsa det maximala djupet för **besluts träd**. Att öka djupet i trädet kan öka precisionen på risken för viss överanpassning och ökad inlärnings tid.

6. För **antalet slumpmässiga delningar per nod** anger du antalet delningar som ska användas när du skapar varje nod i trädet. En *delning* innebär att funktionerna i varje nivå i trädet (noden) är slumpmässigt uppdelade.

7. För **minsta antal sampel per lövnod** anger du det minsta antal fall som krävs för att skapa en terminalsession (löv) i ett träd.

     Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst fem fall som uppfyller samma villkor.


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

### <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du modulen utbildning och växlar sedan till fliken **utdata** i den högra panelen. Klicka på ikonen **Registrera modell**.  Du hittar den sparade modellen som en modul i modul trädet. 

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 