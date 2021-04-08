---
title: 'Logistik regression i multiklass: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder MultiClass logistik regression-modulen i Azure Machine Learning designer för att förutsäga flera värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: e2bbc28735bcbfd952c4941453956acd0568ea67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420741"
---
# <a name="multiclass-logistic-regression-module"></a>Logistik Regressions-modul för multiklass

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en logistik Regressions modell som kan användas för att förutsäga flera värden.

Klassificering med Logistisk regression är en övervakad inlärnings metod och kräver därför en etikettad data uppsättning. Du tränar modellen genom att tillhandahålla modellen och den märkta data uppsättningen som indata till en modul, till exempel [träna modell](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för nya exempel på ingångar.

Azure Machine Learning tillhandahåller också en [logistik Regressions modell med två klasser](./two-class-logistic-regression.md) som passar för klassificering av binära variabler eller dichotomous variabler.

## <a name="about-multiclass-logistic-regression"></a>Om multiklass logistik regression

Logistisk regression är en välkänd metod i statistik som används för att förutsäga sannolikheten för ett resultat och är populär för klassificerings aktiviteter. Algoritmen förutsäger sannolikheten för förekomst av en händelse genom att anpassa data till en logistik funktion. 

I multiklass Logistisk regression kan klassificeraren användas för att förutsäga flera resultat.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurera en logistik regression med multiklass

1. Lägg till **logistik Regressions** -modulen för multiklass i pipelinen.

2. Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .

    + **Enskild parameter**: Använd det här alternativet om du vet hur du vill konfigurera modellen och ange en viss uppsättning värden som argument.

    + **Parameter intervall**: Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra en parameter rensning. Välj ett värde intervall som du vill iterera över, och de [finjusterande modellens egenskaper](tune-model-hyperparameters.md) upprepas över alla möjliga kombinationer av de inställningar som du angav för att fastställa de egenskaper som ger optimala resultat.  

3. **Optimerings tolerans**, ange tröskelvärdet för optimerings konvergens. Om förbättringen mellan iterationer är mindre än tröskelvärdet stannar algoritmen och returnerar den aktuella modellen.

4. **L1 regulariseringshastigheten vikt**, **L2 regulariseringshastigheten vikt**: Ange ett värde som ska användas för regulariseringshastigheten-parametrarna L1 och L2. Ett värde som inte är noll rekommenderas för båda.

    Regulariseringshastigheten är en metod för att förhindra övermontering genom att motverka modeller med extrema koefficient värden. Regulariseringshastigheten fungerar genom att lägga till den påföljd som är kopplad till koefficienterna till felet i hypotesen. En korrekt modell med extrema koefficienter skulle innebära mer, men en mindre exakt modell med fler restriktiva värden skulle bli mindre.

     L1-och L2-regulariseringshastigheten har olika effekter och användnings områden. L1 kan användas för sparse-modeller, vilket är användbart när du arbetar med avancerade data. L2-regulariseringshastigheten är däremot bättre för data som inte är sparse.  Den här algoritmen stöder en linjär kombination av L1-och L2-regulariseringshastigheten värden: det vill säga, om `x = L1` och `y = L2` , `ax + by = c` definierar det linjära omfånget för regulariseringshastigheten-villkoren.

     Olika linjära kombinationer av L1-och L2-villkor har gjorts för logistik Regressions modeller som [elastiska net regulariseringshastigheten](https://wikipedia.org/wiki/Elastic_net_regularization).

6. Värde för **slumpmässig siffra**: Ange ett heltals värde som ska användas som startvärdet för algoritmen om du vill att resultatet ska upprepas vid körning. Annars används ett system klock värde som startvärdet, vilket kan producera något annorlunda resultat i körningar av samma pipeline.

8. Anslut en data uppsättning med etiketter och träna modellen:

    + Om du ställer in **skapa utbildare** för en **parameter** ansluter du en taggad data uppsättning och modulen [träna modell](train-model.md) .  
  
    + Om du ställer in **skapa utbildare** för **parameter intervall** ansluter du en taggad data uppsättning och tränar modellen med hjälp av [finjustera modellens egenskaper](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Om du skickar ett parameter intervall för att [träna modellen](train-model.md), används bara standardvärdet i listan med en parameter.  
    > 
    > Om du skickar en enda uppsättning parameter värden till modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) , ignorerar värdena och använder standardvärdena för eleven när den förväntar sig ett intervall med inställningar för varje parameter.  
    > 
    > Om du väljer alternativet **parameter intervall** och anger ett enda värde för en parameter, används det enskilda värdet som du har angett i hela svepet, även om andra parametrar ändras i ett intervall med värden.

9. Skicka pipelinen.



## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 