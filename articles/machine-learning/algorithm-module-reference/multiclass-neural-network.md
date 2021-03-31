---
title: 'Neurala nätverk i multiklass: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder neurala-modulen i multiklass i Azure Machine Learning designer för att förutsäga ett mål som har värden i flera klasser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a4f7f8a7793f31ffbf2099cbfb314fc5097319f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421268"
---
# <a name="multiclass-neural-network-module"></a>Neurala för multiklass

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en neurala-nätverks modell som kan användas för att förutsäga ett mål som har flera värden. 

Till exempel kan neurala-nätverk av den här typen användas i komplexa uppgifter för visuellt innehåll, t. ex. siffer-och bokstavs igenkänning, dokument klassificering och mönster igenkänning.

Klassificering med neurala-nätverk är en övervakad inlärnings metod och kräver därför en *Taggad data uppsättning* som innehåller en etikett kolumn.

Du kan träna modellen genom att tillhandahålla modellen och den taggade data uppsättningen som indata för att [träna modellen](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya ingångs exemplen.  

## <a name="about-neural-networks"></a>Om neurala-nätverk

Ett neurala-nätverk är en uppsättning sammankopplade lager. Indata är det första lagret och är anslutna till ett utmatnings lager med ett acykliska diagram som består av viktade kanter och noder.

Mellan indata-och utdata-lagren kan du infoga flera dolda lager. De flesta förutsägande uppgifter kan enkelt utföras med bara ett eller flera dolda lager. Den senaste forskningen har dock visat att djup neurala-nätverk (DNN) med många lager kan vara effektiva i komplexa uppgifter som bild-eller tal igenkänning. Efterföljande lager används för att modellera ökande nivåer av semantiskt djup.

Relationen mellan indata och utdata har lärts från att träna neurala-nätverket på indatan. Riktningen på diagrammet fortsätter från indatan via det dolda lagret och till output-lagret. Alla noder i ett lager ansluts med de viktade kanterna till noderna i nästa lager.

För att beräkna nätverkets utdata för ett visst indata beräknas ett värde vid varje nod i de dolda lagren och i utmatnings lagret. Värdet anges genom att beräkna den viktade summan av värdena för noderna från föregående skikt. En aktiverings funktion används sedan för den viktade summan.

## <a name="configure-multiclass-neural-network"></a>Konfigurera neurala nätverk i multiklass

1. Lägg till neurala-modulen för **multiklass** i din pipeline i designern. Du kan hitta den här modulen under **Machine Learning**, **initiera** i **klassificerings** kategorin.

2. **Skapa utbildare läge**: Använd det här alternativet för att ange hur du vill att modellen ska tränas:

    - **Enda parameter**: Välj det här alternativet om du redan vet hur du vill konfigurera modellen.

    - **Parameter intervall**: Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra en parameter rensning. Välj ett värde intervall som du vill iterera över, och de [finjusterande modellens egenskaper](tune-model-hyperparameters.md) upprepas över alla möjliga kombinationer av de inställningar som du angav för att fastställa de egenskaper som ger optimala resultat.  

3. **Specifikation för dolda lager**: Välj den typ av nätverks arkitektur som ska skapas.

    - **Fullständigt anslutet**: Välj det här alternativet om du vill skapa en modell med standard nätverks arkitekturen för neurala. För neurala nätverks modeller i multiklass är standardvärdena följande:

        - Ett dolt lager
        - Utmatnings lagret är fullständigt anslutet till det dolda lagret.
        - Det dolda lagret är fullständigt anslutet till indataströmmen.
        - Antalet noder i Indatakällan bestäms av antalet funktioner i tränings data.
        - Antalet noder i det dolda lagret kan ställas in av användaren. Standardvärdet är 100.
        - Antalet noder i utmatnings lagret beror på antalet klasser.
  
   

5. **Antal dolda noder**: med det här alternativet kan du anpassa antalet dolda noder i standard arkitekturen. Ange antalet dolda noder. Standardvärdet är ett dolt lager med 100 noder.

6. **Inlärnings takten**: definiera storleken på steget som tas vid varje iteration, före korrigeringen. Ett större värde för inlärnings frekvensen kan göra att modellen konvergerar snabbare, men den kan överskotta lokala minimi.

7. **Antal inlärnings iterationer**: Ange det maximala antalet gånger som algoritmen ska bearbeta inlärnings fallen.

8. **Diametern för inledande inlärnings vikt**: Ange nodens vikt i början av inlärnings processen.

9. **Momentet**: Ange en vikt att tillämpa vid inlärning till noder från föregående iterationer.
  
11. **Blanda exempel**: Välj det här alternativet om du vill blanda fall mellan iterationer.

    Om du avmarkerar det här alternativet bearbetas ärenden i exakt samma ordning varje gång du kör pipelinen.

12. Värde för **slumpmässig siffra**: Ange ett värde som ska användas som dirigering, om du vill säkerställa repeterbarhet i flera körningar av samma pipeline.

14. Träna modellen:

    + Om du ställer in **skapa utbildare** för en **parameter** ansluter du en taggad data uppsättning och modulen [träna modell](train-model.md) .  
  
    + Om du ställer in **skapa utbildare** för **parameter intervall** ansluter du en taggad data uppsättning och tränar modellen med hjälp av [finjustera modellens egenskaper](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Om du skickar ett parameter intervall för att [träna modellen](train-model.md), används bara standardvärdet i listan med en parameter.  
    > 
    > Om du skickar en enda uppsättning parameter värden till modulen [finjustera modellens standardparametrar](tune-model-hyperparameters.md) , ignorerar värdena och använder standardvärdena för eleven när den förväntar sig ett intervall med inställningar för varje parameter.  
    > 
    > Om du väljer alternativet **parameter intervall** och anger ett enda värde för en parameter, används det enskilda värdet som du har angett i hela svepet, även om andra parametrar ändras i ett intervall med värden.  
  

## <a name="results"></a>Resultat

När utbildningen är klar:

- Om du vill spara en ögonblicks bild av den tränade modellen väljer du fliken **utdata** i den högra panelen i modulen **träna modell** . Välj ikonen **registrera data uppsättning** för att spara modellen som en återanvändbar modul.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 