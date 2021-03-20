---
title: 'Two-Class stöder Vector Machine: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Two-Class support Vector Machine i Azure Machine Learning för att skapa en binär klassificerare.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 46cfdd319fc89e569d165dc2e11303e67c6dd54e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420571"
---
# <a name="two-class-support-vector-machine-module"></a>Modulen Two-Class stöder Vector

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en modell som baseras på algoritmen för att stödja Vector-datorer. 

Support Vector Machines (SVMs) är en väl genomsökd klass med övervakade inlärnings metoder. Den här specifika implementeringen är anpassad till förutsägelse av två möjliga resultat, baserat på antingen kontinuerliga eller kategoriska variabler.

När du har definierat modell parametrarna tränar du modellen med hjälp av modulen utbildning och tillhandahåller en *Taggad data uppsättning* som innehåller en etikett eller en resultat kolumn.

## <a name="about-support-vector-machines"></a>Om support Vector-datorer

Support vektor datorer är bland de flesta av Machine Learning-algoritmer och SVM-modeller har använts i många program, från informations hämtning till text och bild klassificering. SVMs kan användas för både klassificerings-och Regressions uppgifter.

Den här SVM-modellen är en övervakad utbildnings modell som kräver etiketterade data. I övnings processen analyserar algoritmen indata och identifierar mönster i ett flerdimensionellt funktions utrymme som kallas för den här *planen*.  Alla exempel på indata visas som punkter i det här utrymmet och mappas till utdata-kategorier på ett sådant sätt att kategorierna är indelade som breda och rensar en lucka som är möjlig.

För förutsägelse tilldelar SVM-algoritmen nya exempel till en kategori eller en annan, och mappar dem till samma utrymme. 

## <a name="how-to-configure"></a>Så här konfigurerar du 

För den här modell typen rekommenderar vi att du normaliserar data uppsättningen innan du använder den för att träna klassificeraren.
  
1.  Lägg till modulen för **dubbelriktad Vector-** modul i din pipeline.  
  
2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: om du vet hur du vill konfigurera modellen kan du ange en viss uppsättning värden som argument.  

    -   **Parameter intervall**: om du inte är säker på de bästa parametrarna kan du hitta de optimala parametrarna med hjälp av modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) . Du anger några värden och utbildaren upprepas över flera kombinationer av inställningarna för att avgöra vilken kombination av värden som ger bäst resultat.

3.  För **antal iterationer** anger du ett tal som anger antalet iterationer som används när modellen skapas.  
  
     Den här parametern kan användas för att styra kompromisser mellan inlärnings hastigheten och noggrannhet.  
  
4.  För **lambda** anger du ett värde som ska användas som vikt för L1-regulariseringshastigheten.  
  
     Den här regulariseringshastigheten-koefficienten kan användas för att finjustera modellen. Större värden bestraffa mer komplexa modeller.  
  
5.  Välj alternativet, **normalisera funktioner** om du vill normalisera funktioner innan du tränar.
  
     Om du använder normalisering före träning centreras data punkter på medelvärdet och skalas för att ha en enhet av standard avvikelsen.
  
6.  Välj alternativet och **projicera till enhets sfären** för att normalisera koefficienter.
  
     Projekt värden till enhets utrymme innebär att data punkter centreras på 0 och skalas för att ha en enhet av standard avvikelsen.
  
7.  Skriv ett heltals värde som ska användas som ett Seed-värde för att säkerställa reproducerbarhet i körningar i **slumptal**.  Annars används ett värde för system klockan som ett Seed, vilket kan resultera i något annorlunda resultat mellan körningar.
  
9. Anslut en data uppsättning med etiketter och träna modellen:

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

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du fliken **utdata** i den högra panelen i modulen **träna modell** . Välj ikonen **registrera data uppsättning** för att spara modellen som en återanvändbar modul.

+ Om du vill använda modellen för poängsättning lägger du till modulen **Poäng modell** i en pipeline.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 