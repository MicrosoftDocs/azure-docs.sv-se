---
title: 'Poisson-regression: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Poisson regression-modulen i Azure Machine Learning designer för att skapa en Poisson regression-modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 2dfd8b3d919f9eeb3e183135ef543f417c878977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420707"
---
# <a name="poisson-regression"></a>Poisson-regression

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en Poisson regression-modell i en pipeline. Poisson-regressionen är avsedd för förutsägelse av numeriska värden, vanligt vis antal. Därför bör du bara använda den här modulen för att skapa en Regressions modell om de värden som du försöker förutsäga uppfyller följande villkor:

- Response-variabeln har en [Poisson-distribution](https://en.wikipedia.org/wiki/Poisson_distribution).  

- Antalet får inte vara negativt. Metoden kommer inte att fungera direkt om du försöker använda den med negativa etiketter.

- En Poisson-distribution är en diskret distribution. Därför är det inte meningsfullt att använda den här metoden med icke-heltal.

> [!TIP]
> Om målet inte är ett antal är Poisson regression förmodligen inte en lämplig metod. Testa [andra Regressions moduler i designern](./module-reference.md#machine-learning-algorithms). 

När du har konfigurerat Regressions metoden måste du träna modellen med en data uppsättning som innehåller exempel på det värde som du vill förutsäga. Den tränade modellen kan sedan användas för att göra förutsägelser.

## <a name="more-about-poisson-regression"></a>Mer om Poisson regression

Poisson regression är en särskild typ av Regressions analys som vanligt vis används för att modellera antal. Till exempel skulle Poisson-regression vara användbart i följande scenarier:

- Utforma antalet kallare som är kopplade till flyg Plans flygningar

- Beräkna antalet nöd tjänst anrop under en händelse

- Projicera antalet kund förfrågningar efter en befordran

- Skapa katastrof tabeller

Eftersom Response-variabeln har en Poisson-distribution, gör modellen olika antaganden om data och dess sannolikhets fördelning än, t. ex. minsta kvadratmetoden. Därför bör Poisson-modeller tolkas annorlunda än andra Regressions modeller.

## <a name="how-to-configure-poisson-regression"></a>Så här konfigurerar du Poisson-regression

1. Lägg till **Poisson regression** -modulen i din pipeline i designern. Du hittar den här modulen under **Machine Learning algoritmer** i **Regressions** kategorin.

2. Lägg till en data uppsättning som innehåller tränings data av rätt typ. 

    Vi rekommenderar att du använder [normaliserings data](normalize-data.md) för att normalisera indata-datauppsättningen innan du använder den för att träna modellerings regressor.

3. I den högra rutan i **Poisson regression** -modulen anger du hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    - **Enskild parameter**: Ange en viss uppsättning värden som argument om du vet hur du vill konfigurera modellen.
  
    - **Parameter intervall**: om du inte är säker på de bästa parametrarna gör du en parameter rensning med hjälp av modulen för att [finjustera modellens standardparametrar](tune-model-hyperparameters.md) . Utbildaren itererar över flera värden som du anger för att hitta den optimala konfigurationen.
  
4. **Optimerings tolerans**: Ange ett värde som definierar tolerans intervallet under optimeringen. Ju lägre värde, desto långsammare och mer korrekt passning.

5. **L1-regulariseringshastigheten vikt** och **L2-regulariseringshastigheten vikt**: Ange värden som ska användas för L1-och L2-regulariseringshastigheten. *Regulariseringshastigheten* lägger till begränsningar i algoritmen för aspekter av modellen som är oberoende av tränings data. Regulariseringshastigheten används ofta för att undvika överanpassning. 

    - L1-regulariseringshastigheten är användbart om målet är att ha en modell som är så sparse som möjligt.

        L1-regulariseringshastigheten görs genom att subtraktionen av vikt vektorns netto vikt subtraheras från det förlust uttryck som en elev försöker minimera. L1-normen är en utmärkt uppskattning till L0-norm, vilket är antalet koordinater som inte är noll.

    - L2-regulariseringshastigheten förhindrar att en enskild koordinat i vikt vektorn växer för mycket i storlek. L2-regulariseringshastigheten är användbart om målet är att ha en modell med små övergripande vikter.

    I den här modulen kan du använda en kombination av L1-och L2-regularizations. Genom att kombinera L1-och L2-regulariseringshastigheten kan du ställa in en straffning för parameter värdenas storlek. Eleven försöker minimera sanktionen på ett kompromiss med att minimera förlusten.

    En felfri diskussion om L1-och L2-regulariseringshastigheten finns i [L1-och L2-regulariseringshastigheten för Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning).

6. **Minnes storlek för L-BFGS**: Ange mängden minne som ska reserveras för modell anpassning och optimering.

     L-BFGS är en metod för optimering baserat på algoritmen Broyden – Fletcher – Goldfarb – Shanno (BFGS). Metoden använder en begränsad mängd minne (L) för att beräkna nästa steg riktning.

     Genom att ändra den här parametern kan du påverka antalet tidigare positioner och toningar som lagras för att beräkna nästa steg.

7. Anslut inlärnings data uppsättningen och den inte tränade modellen till någon av inlärnings modulerna: 

    - Om du ställer in **skapa utbildare** för **en parameter** använder du modulen [träna modell](train-model.md) .

    - Om du ställer in **skapa utbildare läge** till **parameter intervall** använder du modulen för att [finjustera modellens egenskaper](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Om du skickar ett parameter intervall för att [träna modellen](train-model.md), används bara det första värdet i parameter intervall listan.
    > 
    > - Om du skickar en enda uppsättning parameter värden till modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) , ignoreras värdena och standardvärdena används för eleven när en uppsättning inställningar förväntas för varje parameter.
    > 
    > - Om du väljer alternativet **parameter intervall** och anger ett enda värde för en parameter, används det enskilda värdet som du har angett i hela svepet, även om andra parametrar ändras i ett intervall med värden.

8.  Skicka pipelinen.

## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du modulen utbildning och växlar sedan till fliken **utdata + loggar** i den högra panelen. Klicka på ikonen **registrera data uppsättning**.  Du hittar den sparade modellen som en modul i modul trädet. 

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.