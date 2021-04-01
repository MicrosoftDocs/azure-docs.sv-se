---
title: 'Neurala nätverks regression: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder neurala Network regression-modulen i Azure Machine Learning för att skapa en Regressions modell med en anpassningsbar neurala-nätverks algoritm.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 403576454615effeb53651b51679681422b08e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90890354"
---
# <a name="neural-network-regression-module"></a>Neurala Network regression-modul

*Skapar en Regressions modell med en neurala*  
  
 Kategori: Machine Learning/initiera modell/regression
  
## <a name="module-overview"></a>Översikt över modul  

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en Regressions modell med en anpassningsbar neurala-nätverks algoritm.
  
 Även om neurala-nätverk är allmänt kända för användning i djup inlärning och modellering av komplexa problem, till exempel bild igenkänning, är de lätt att anpassa till Regressions problem. En klass med statistiska modeller kan kallas ett neurala nätverk om de använder anpassningsbara vikter och kan uppskatta icke-linjära funktioner i sina indata. Därför är neurala Network regression lämplig för problem där en mer traditionell Regressions modell inte kan passa en lösning.
  
 Neurala Network regression är en övervakad inlärnings metod och kräver därför en *Taggad data uppsättning* som innehåller en etikett kolumn. Eftersom en Regressions modell förutsäger ett numeriskt värde måste etikett kolumnen vara en numerisk datatyp.  
  
 Du kan träna modellen genom att tillhandahålla modellen och den taggade data uppsättningen som indata för att [träna modellen](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya ingångs exemplen.  
  
## <a name="configure-neural-network-regression"></a>Konfigurera neurala Network regression 

Neurala-nätverk kan anpassas i stor utsträckning. I det här avsnittet beskrivs hur du skapar en modell med hjälp av två metoder:
  
+ [Skapa en neurala-nätverks modell med standard arkitekturen](#bkmk_DefaultArchitecture)  
  
    Om du godkänner standard nätverks arkitekturen för neurala kan du använda fönstret **Egenskaper** för att ange parametrar som styr beteendet för neurala-nätverket, till exempel antalet noder i det dolda lagret, inlärnings takt och normalisering.

    Börja här om du är nybörjare på neurala-nätverk. Modulen stöder många anpassningar, samt modell justering, utan djupgående kunskaper om neurala-nätverk. 

+ Definiera en anpassad arkitektur för ett neurala-nätverk 

    Använd det här alternativet om du vill lägga till extra dolda lager eller helt anpassa nätverks arkitekturen, dess anslutningar och aktiverings funktioner.
    
    Det här alternativet är bäst om du redan är bekant med neurala-nätverk. Du använder språket net # för att definiera nätverks arkitekturen.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a> Skapa en neurala-nätverks modell med standard arkitekturen

1.  Lägg till **neurala Network regression** -modulen i din pipeline i designern. Du kan hitta den här modulen under **Machine Learning**, **initiera** i **Regressions** kategorin. 
  
2. Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enda parameter**: Välj det här alternativet om du redan vet hur du vill konfigurera modellen.

    -   **Parameter intervall**: Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra en parameter rensning. Välj ett värde intervall som du vill iterera över, och de [finjusterande modellens egenskaper](tune-model-hyperparameters.md) upprepas över alla möjliga kombinationer av de inställningar som du angav för att fastställa de egenskaper som ger optimala resultat.   

3.  I **specifikationen för dolt lager** väljer du **fullständigt anslutet ärende**. Med det här alternativet skapas en modell som använder standard nätverks arkitekturen neurala, som för en neurala-nätverks Regressions modell, som har följande attribut:  
  
    + Nätverket har exakt ett dolt lager.
    + Utmatnings lagret är fullständigt anslutet till det dolda lagret och det dolda lagret är fullständigt anslutet till inmatnings lagret.
    + Antalet noder i det dolda lagret kan ställas in av användaren (Standardvärdet är 100).  
  
    Eftersom antalet noder i inmatnings lagret bestäms av antalet funktioner i tränings data, kan det i en Regressions modell bara finnas en nod i output-lagret.  
  
4. Ange antalet dolda noder för **antalet dolda noder**. Standardvärdet är ett dolt lager med 100 noder. (Det här alternativet är inte tillgängligt om du definierar en anpassad arkitektur med net #.)
  
5.  För **inlärnings hastighet** anger du ett värde som definierar det steg som tas vid varje iteration, före korrigeringen. Ett större värde för inlärnings frekvensen kan göra att modellen konvergerar snabbare, men den kan överskotta lokala minimi.

6.  I **antal inlärnings iterationer** anger du det maximala antalet gånger som algoritmen bearbetar inlärnings fallen.


8.  Ange ett **värde som ska** användas under inlärningen som en vikt på noder från föregående iterationer.

10. Välj alternativet, **blanda exempel** för att ändra ordningen på fall mellan iterationer. Om du avmarkerar det här alternativet bearbetas ärenden i exakt samma ordning varje gång du kör pipelinen.
  
11. Du kan ange ett värde som ska användas som startvärdet för det **slumpmässiga antalet frön**. Att ange ett Seed-värde är användbart när du vill säkerställa repeterbarhet i flera körningar av samma pipeline.
  
13. Anslut en data uppsättning för utbildning och träna modellen:

    + Om du ställer in **skapa utbildare** för en **parameter** ansluter du en taggad data uppsättning och modulen [träna modell](train-model.md) .  
  
    + Om du ställer in **skapa utbildare** för **parameter intervall** ansluter du en taggad data uppsättning och tränar modellen med hjälp av [finjustera modellens egenskaper](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Om du skickar ett parameter intervall för att [träna modellen](train-model.md), används bara standardvärdet i listan med en parameter.  
    > 
    > Om du skickar en enda uppsättning parameter värden till modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) , ignorerar värdena och använder standardvärdena för eleven när den förväntar sig ett intervall med inställningar för varje parameter.  
    > 
    > Om du väljer alternativet **parameter intervall** och anger ett enda värde för en parameter, används det enskilda värdet som du har angett i hela svepet, även om andra parametrar ändras i ett intervall med värden.  
  
   
14. Skicka pipelinen.  

## <a name="results"></a>Resultat

När utbildningen är klar:

- Om du vill spara en ögonblicks bild av den tränade modellen väljer du fliken **utdata** i den högra panelen i modulen **träna modell** . Välj ikonen **registrera data uppsättning** för att spara modellen som en återanvändbar modul.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 