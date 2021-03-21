---
title: 'Linjär regression: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen linjär regression i Azure Machine Learning för att skapa en linjär Regressions modell för användning i en pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 809b1be4f9f12e1963ff5caeaacd109c84db154f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93323662"
---
# <a name="linear-regression-module"></a>Linjär Regressions modul
I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en linjär Regressions modell för användning i en pipeline.  Linjär regression försöker upprätta en linjär relation mellan en eller flera oberoende variabler och ett numeriskt resultat, eller en beroende variabel. 

Du använder den här modulen för att definiera en linjär Regressions metod och sedan träna en modell med hjälp av en etikettad data uppsättning. Den tränade modellen kan sedan användas för att göra förutsägelser.

## <a name="about-linear-regression"></a>Om linjär regression

Linjär regression är en gemensam statistisk metod som har antagits i maskin inlärning och förbättrats med många nya metoder för att anpassa linjen och mäta fel. Helt enkelt refererar regressionen till förutsägelse av ett numeriskt mål. Linjär regression är fortfarande ett bra val när du vill ha en enkel modell för en grundläggande förutsägelse aktivitet. Linjär regression tenderar också att arbeta bra på högdimensionella, glesa data uppsättningar som saknar komplexitet.

Azure Machine Learning stöder en mängd Regressions modeller, förutom linjär regression. Termen "regression" kan dock tolkas löst och vissa typer av regressioner som tillhandahålls i andra verktyg stöds inte.

+ Det klassiska Regressions problemet omfattar en enda oberoende variabel och en beroende variabel. Detta kallas *enkel regression*.  Den här modulen stöder enkel regression.

+ *Flera linjära regressioner* omfattar två eller flera oberoende variabler som bidrar till en enda beroende variabel. Problem där flera indata används för att förutsäga ett enda numeriskt resultat kallas även *multivarierad linjär regression*.

    Den **linjära Regressions** modulen kan lösa de här problemen, som kan de flesta av de andra Regressions modulerna.

+ *Regression med flera etiketter* är uppgiften att förutsäga flera beroende variabler i en enda modell. I multilabel-regressionen kan ett exempel tilldelas flera olika etiketter. (Detta skiljer sig från uppgiften att förutsäga flera nivåer inom en enskild klass variabel.)

    Den här typen av regression stöds inte i Azure Machine Learning. Om du vill förutsäga flera variabler skapar du en separat elev för varje utdata som du vill förutsäga.

I år har statistiker utvecklat avancerade metoder för regression. Detta gäller även för linjär regression. Den här modulen stöder två metoder för att mäta fel och passar Regressions linjen: vanliga minsta kvadratmetoden och tonings brantaste.

- **Tonings brantaste** är en metod som minimerar mängden fel i varje steg i modell inlärnings processen. Det finns många variationer i gradient-brantaste och dess optimering för olika inlärnings problem har för stor ATS. Om du väljer det här alternativet för **lösnings metoden** kan du ange en rad olika parametrar för att kontrol lera steg storleken, inlärnings hastigheten och så vidare. Det här alternativet stöder även användning av en integrerad parameter svepning.

- **Vanliga minsta kvadratmetoden** är en av de oftast använda teknikerna i linjär regression. Till exempel är minsta kvadratmetoden den metod som används i Analysis ToolPak för Microsoft Excel.

    Vanliga minsta kvadratmetoden avser funktionen förlust, som beräknar fel som summan av kvadraten av avståndet från det faktiska värdet till den förväntade linjen, och passar modellen genom att minimera kvadratvärdet. Den här metoden förutsätter en stark linjär relation mellan indata och den beroende variabeln.

## <a name="configure-linear-regression"></a>Konfigurera linjär regression

Den här modulen stöder två metoder för att anpassa en Regressions modell med olika alternativ:

+ [Anpassa en Regressions modell med minsta möjliga kvadratmetoden](#create-a-regression-model-using-ordinary-least-squares)

    För små data uppsättningar är det bäst att välja vanliga minsta kvadratmetoden. Detta bör ge liknande resultat till Excel.
    
+ [Skapa en Regressions modell med brantaste för online-gradient](#create-a-regression-model-using-online-gradient-descent)

    Gradient-brantaste är en bättre förlust funktion för modeller som är mer komplexa eller som har för lite utbildnings data som har fått antalet variabler.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Skapa en Regressions modell med minsta möjliga kvadratmetoden

1. Lägg till modulen **linjär Regressions modell** i din pipeline i designern.

    Du hittar den här modulen i kategorin **Machine Learning** . Expandera **initiera modell**, expandera **regression** och dra sedan modulen **linjär Regressions modell** till din pipeline.

2. I rutan **Egenskaper** går du till List rutan **lösnings metod** och väljer **vanliga minsta kvadratmetoden**. Det här alternativet anger den beräknings metod som används för att hitta Regressions linjen.

3. I **L2-regulariseringshastigheten vikt** anger du det värde som ska användas som vikt för L2-regulariseringshastigheten. Vi rekommenderar att du använder ett värde som inte är noll för att undvika överanpassning.

     Mer information om hur regulariseringshastigheten påverkar modell anpassning finns i den här artikeln: [L1-och L2-regulariseringshastigheten för Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)

4. Välj alternativet, **Inkludera spärr villkor**, om du vill visa villkoret för skärningen.

    Avmarkera det här alternativet om du inte behöver granska Regressions formeln.

5. Du kan ange ett värde för att dirigera den slumpmässiga nummer generatorn som används av modellen om du vill använda **slumpmässig numrering**.

    Att använda ett Seed-värde är användbart om du vill behålla samma resultat för olika körningar av samma pipeline. Annars är standardvärdet att använda ett värde från system klockan.


7. Lägg till modulen [träna modell](./train-model.md) i din pipeline och Anslut en etikettad data uppsättning.

8. Skicka pipelinen.

### <a name="results-for-ordinary-least-squares-model"></a>Resultat för vanlig minsta kvadratmetoden

När utbildningen är klar:


+ Om du vill göra förutsägelser ansluter du den tränade modellen till [Poäng modellens](./score-model.md) modul, tillsammans med en data uppsättning med nya värden. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Skapa en Regressions modell med brantaste för online-gradient

1. Lägg till modulen **linjär Regressions modell** i din pipeline i designern.

    Du hittar den här modulen i kategorin **Machine Learning** . Expandera **initiera modell**, expandera **regression** och dra modulen **linjär Regressions modell** till din pipeline

2. I rutan **Egenskaper** i list rutan **lösnings metod** väljer du brantaste för **online-toning** som den beräknings metod som används för att hitta Regressions linjen.

3. För **skapa utbildare-läge** anger du om du vill träna modellen med en fördefinierad uppsättning parametrar, eller om du vill optimera modellen med hjälp av en parameter rensning.

    + **Enskild parameter**: om du vet hur du vill konfigurera ett linjärt Regressions nätverk kan du ange en viss uppsättning värden som argument.
    
    + **Parameter intervall**: Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra en parameter rensning. Välj ett värde intervall som du vill iterera över, och de [finjusterande modellens egenskaper](tune-model-hyperparameters.md) upprepas över alla möjliga kombinationer av de inställningar som du angav för att fastställa de egenskaper som ger optimala resultat.  

   
4. För **inlärnings frekvens** anger du den inledande inlärnings takten för Stochastic gradient brantaste optimering.

5. Ange ett värde som anger hur många gånger algoritmen ska iterera genom exempel i **antal utbildnings epoker**. För data uppsättningar med ett litet antal exempel bör det här talet vara stort för att uppnå konvergens.

6. **Normaliserings funktioner**: om du redan har normaliserat numeriska data som används för att träna modellen kan du avmarkera det här alternativet. Som standard normaliserar modulen alla numeriska indata till ett intervall mellan 0 och 1.

    > [!NOTE]
    > 
    > Kom ihåg att använda samma normaliserings metod för nya data som används för poängsättning.

7. I **L2-regulariseringshastigheten vikt** anger du det värde som ska användas som vikt för L2-regulariseringshastigheten. Vi rekommenderar att du använder ett värde som inte är noll för att undvika överanpassning.

    Mer information om hur regulariseringshastigheten påverkar modell anpassning finns i den här artikeln: [L1-och L2-regulariseringshastigheten för Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)


9. Välj alternativet för att **minska inlärnings** takten om du vill att inlärnings takten ska minska allteftersom iterationer fortskrider.  

10. Du kan ange ett värde för att dirigera den slumpmässiga nummer generatorn som används av modellen om du vill använda **slumpmässig numrering**. Att använda ett Seed-värde är användbart om du vill behålla samma resultat för olika körningar av samma pipeline.


12. Träna modellen:

    + Om du ställer in **skapa utbildare** för en **parameter** ansluter du en taggad data uppsättning och modulen [träna modell](train-model.md) .  
  
    + Om du ställer in **skapa utbildare** för **parameter intervall** ansluter du en taggad data uppsättning och tränar modellen med hjälp av [finjustera modellens egenskaper](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Om du skickar ett parameter intervall för att [träna modellen](train-model.md), används bara standardvärdet i listan med en parameter.  
    > 
    > Om du skickar en enda uppsättning parameter värden till modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) , ignorerar värdena och använder standardvärdena för eleven när den förväntar sig ett intervall med inställningar för varje parameter.  
    > 
    > Om du väljer alternativet **parameter intervall** och anger ett enda värde för en parameter, används det enskilda värdet som du har angett i hela svepet, även om andra parametrar ändras i ett intervall med värden.

13. Skicka pipelinen.

### <a name="results-for-online-gradient-descent"></a>Resultat för brantaste för online-toning

När utbildningen är klar:

+ Om du vill göra förutsägelser ansluter du den tränade modellen till [Poäng modellens](./score-model.md) modul, tillsammans med nya indata.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.