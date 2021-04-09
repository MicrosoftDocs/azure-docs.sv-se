---
title: 'Two-Class neurala nätverk: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Two-Class neurala-nätverk i Azure Machine Learning för att skapa en binär klassificerare.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 9131a2439facef00cae818bffef38e536a40a2fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421166"
---
# <a name="two-class-neural-network-module"></a>Two-Class neurala-nätverksmapp

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en neurala-nätverks modell som kan användas för att förutsäga ett mål som bara har två värden.

Klassificering med neurala-nätverk är en övervakad inlärnings metod och kräver därför en *Taggad data uppsättning* som innehåller en etikett kolumn. Du kan till exempel använda den här neurala-nätverks modellen till att förutsäga binära resultat, till exempel om en patient har en viss sjukdom eller om en dator sannolikt kommer att sluta inom ett visst tidsintervall.  

När du har definierat modellen tränar du den genom att tillhandahålla en taggad data uppsättning och modellen som indata för att [träna modellen](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för nya indata.

### <a name="more-about-neural-networks"></a>Mer om neurala-nätverk

Ett neurala-nätverk är en uppsättning sammankopplade lager. Indata är det första lagret och är anslutna till ett utmatnings lager med ett acykliska diagram som består av viktade kanter och noder.

Mellan indata-och utdata-lagren kan du infoga flera dolda lager. De flesta förutsägande uppgifter kan enkelt utföras med bara ett eller flera dolda lager. Den senaste forskningen har dock visat att djup neurala-nätverk (DNN) med många lager kan vara effektiva i komplexa uppgifter som bild-eller tal igenkänning. Efterföljande lager används för att modellera ökande nivåer av semantiskt djup.

Relationen mellan indata och utdata har lärts från att träna neurala-nätverket på indatan. Riktningen på diagrammet fortsätter från indatan via det dolda lagret och till output-lagret. Alla noder i ett lager ansluts med de viktade kanterna till noderna i nästa lager.

För att beräkna nätverkets utdata för ett visst indata beräknas ett värde vid varje nod i de dolda lagren och i utmatnings lagret. Värdet anges genom att beräkna den viktade summan av värdena för noderna från föregående skikt. En aktiverings funktion används sedan för den viktade summan.
  
## <a name="how-to-configure"></a>Så här konfigurerar du

1.  Lägg till **neurala-modulen i två klasser** i din pipeline. Du kan hitta den här modulen under **Machine Learning**, **initiera** i **klassificerings** kategorin.  
  
2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enda parameter**: Välj det här alternativet om du redan vet hur du vill konfigurera modellen.

    -   **Parameter intervall**: om du inte är säker på de bästa parametrarna kan du hitta de optimala parametrarna med hjälp av modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) . Du anger några värden och utbildaren upprepas över flera kombinationer av inställningarna för att avgöra vilken kombination av värden som ger bäst resultat.  

3.  För **dolda lager specifikation** väljer du vilken typ av nätverks arkitektur som ska skapas.  
  
    -   **Fullständigt anslutet ärende**: använder standard nätverks arkitekturen neurala, som definieras för neurala-nätverk i två klasser enligt följande:
  
        -   Har ett dolt lager.
  
        -   Utmatnings lagret är fullständigt anslutet till det dolda lagret och det dolda lagret är fullständigt anslutet till inmatnings lagret.
  
        -   Antalet noder i inmatnings lagret är lika med antalet funktioner i tränings data.
  
        -   Antalet noder i det dolda lagret anges av användaren. Standardvärdet är 100.
  
        -   Antalet noder är lika med antalet klasser. För ett neurala nätverk med två klasser innebär detta att alla indata måste mappas till en av två noder i output-lagret.

5.  För **inlärnings takt** definierar du storleken på steget som tas vid varje iteration, före korrigeringen. Ett större värde för inlärnings frekvensen kan göra att modellen konvergerar snabbare, men den kan överskotta lokala minimi.

6.  I **antal inlärnings iterationer** anger du det maximala antalet gånger som algoritmen ska bearbeta inlärnings fallen.

7.  För **den inledande utbildningen för inlärnings viktning** anger du nodens vikt i början av inlärnings processen.

8.  För **det** här steget anger du en vikt att tillämpa vid inlärning till noder från föregående iterationer  

10. Välj alternativet **blanda exempel** för att blanda fall mellan iterationer. Om du avmarkerar det här alternativet bearbetas ärenden i exakt samma ordning varje gång du kör pipelinen.
  
11. Ange ett värde som ska användas som startvärdet för **slumpmässig numrering av utsäde**.
  
     Att ange ett Seed-värde är användbart när du vill säkerställa repeterbarhet i flera körningar av samma pipeline.  Annars används ett system klock värde som Seed, vilket kan orsaka något annorlunda resultat varje gången du kör pipelinen.
  
13. Lägg till en etikettad data uppsättning i pipelinen och träna modellen:

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

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du fliken **utdata** i den högra panelen i modulen **träna modell** . Välj ikonen **registrera data uppsättning** för att spara modellen som en återanvändbar modul.

+ Om du vill använda modellen för poängsättning lägger du till modulen **Poäng modell** i en pipeline.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
