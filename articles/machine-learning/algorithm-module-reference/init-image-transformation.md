---
title: Avbildnings omvandling för init-avbildning Transformationply
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen init-avbildnings-omvandling i Azure Machine Learning designer för att initiera avbildnings omvandlingen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fc0eb196ed24e413c35d64f0571ff29dc3725032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421285"
---
# <a name="init-image-transformation"></a>Initiera bildtransformering

Den här artikeln beskriver hur du använder modulen **init-avbildnings-omvandling** i Azure Machine Learning designer för att initiera avbildnings omvandling för att ange hur du vill att bilden ska transformeras.

## <a name="how-to-configure-init-image-transformation"></a>Konfigurera omvandling av init-avbildningar

1.  Lägg till modulen **init-avbildnings omvandling** i din pipeline i designern. 

2.  För **storleks ändring** anger du om du vill ändra storlek på pil-bilden för indata till den angivna storleken. Om du väljer sant kan du ange önskad storlek för bild storleken i **storlek**, som standard 256. 

3.  För **Center-beskärning** anger du om du vill beskära den aktuella pil-bilden i mitten. Om du väljer "sant" kan du ange önskad storlek för den resulterande bilden i **beskärnings storlek**, som standard 224.  

4.  Ange om du vill fylla den aktuella PIL-bilden på alla sidor med pad-värdet 0 för **pad**. Om du väljer sant kan du ange utfyllnad (hur många bild punkter som ska läggas till) för varje kant linje i **utfyllnaden**.

5.  För **färg Darr** anger du om du vill ändra ljus styrka, kontrast och mättnad i en bild slumpmässigt.

6.  I **gråskala** anger du om du vill konvertera bilden till gråskala.

7.  Ange om du vill beskära den aktuella PIL-bilden till slumpmässig storlek och proportioner för slumpmässig storleks **ändring**. En beskärning av slumpmässig storlek (mellan 0,08 och 1,0) för den ursprungliga storleken och ett slumpmässigt höjd förhållande (mellan 3/4 och 4/3) för det ursprungliga höjd förhållandet. Den här beskärningen ändrar storlek till den storlek som anges.
    Detta används vanligt vis för att träna inverkande nätverk. Om du väljer sant kan du ange den förväntade utmatnings storleken för varje kant i **slumpmässig storlek**, som standard 256.

8.  För **slumpmässig beskärning** anger du om du vill beskära den aktuella pil-bilden på en slumpmässig plats. Om du väljer sant kan du ange önskad utmatnings storlek för beskärningen i **slumpmässig beskärnings storlek**, som standard 224.

9.  För **slumpmässig vågrät vändning** anger du om du vill vända den aktuella pil-bilden vågrätt slumpmässigt med sannolikhet 0,5.

10.  För **slumpmässig lodrät vändning** anger du om du vill vända den aktuella pil-bilden lodrätt slumpmässigt med sannolikhet 0,5.

11.  För **slumpmässig rotation** anger du om du vill rotera bilden efter vinkel. Om du väljer sant kan du ange en omfattning i grader genom att ange **slumpmässiga rotations grader**, vilket innebär (-grader, + grader), som standard 0.

12.  För **slumpmässig tillhörighet** anger du om du vill slumpmässigt skapa en slumpmässig tilldelning av avbildningens behållar Center-variant. Om du väljer "sant" kan du ange i ett intervall med grader som du vill välja bland i **slumpmässiga tillhörighets grader**, vilket innebär (-grader, + grader), som standard 0.

13.  För **slumpmässig gråskala** anger du om du vill konvertera bilden slumpmässigt till gråskala med sannolikhet 0,1.

14.  I **slumpmässigt perspektiv** anger du om du vill utföra en perspektiv omvandling av den aktuella pil-bilden slumpmässigt med sannolikhet 0,5.


16.  Anslut till [Använd avbildnings omvandlings](apply-image-transformation.md) modul för att tillämpa den omvandling som anges ovan för data uppsättningen för indata.

17. Skicka pipelinen.

## <a name="results"></a>Resultat

När omvandlingen har slutförts kan du hitta transformerade avbildningar i utdata från modulen [Använd avbildnings omvandling](apply-image-transformation.md) .


## <a name="technical-notes"></a>Tekniska anteckningar  

Se [https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html) för mer information om avbildnings omvandling.

###  <a name="module-parameters"></a>Parametrar för modul  

| Name                    | Intervall   | Typ    | Standardvärde | Beskrivning                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Ändra storlek                  | Valfri     | Boolesk | Sant    | Ändra storlek på PIL-avbildningen till den angivna storleken |
| Storlek                    | >= 1     | Integer | 256     | Ange önskad storlek på utdata          |
| Center-beskärning             | Valfri     | Boolesk | Sant    | Beskär den aktuella PIL-bilden i mitten  |
| Beskärnings storlek               | >= 1     | Integer | 224     | Ange önskad utmatnings storlek för beskärningen |
| Pad                     | Valfri     | Boolesk | Falskt   | Fyll den aktuella PIL-bilden på alla sidor med angivet "pad"-värde |
| Utfyllnad                 | >= 0     | Integer | 0       | Utfyllnad på varje kant linje                   |
| Färg Darr            | Valfri     | Boolesk | Falskt   | Ändra ljus styrka, kontrast och mättnad i en bild slumpmässigt |
| I gråskala               | Valfri     | Boolesk | Falskt   | Konvertera bild till gråskala               |
| Slumpmässig beskärnings storlek     | Valfri     | Boolesk | Falskt   | Beskär den aktuella PIL-bilden till slumpmässig storlek och höjd-breddförhållandet |
| Slumpmässig storlek             | >= 1     | Integer | 256     | Förväntad utmatnings storlek för varje kant        |
| Slumpmässig beskärning             | Valfri     | Boolesk | Falskt   | Beskär den aktuella PIL-avbildningen på en slumpmässig plats |
| Slumpmässig beskärnings storlek        | >= 1     | Integer | 224     | Önskad utmatnings storlek för beskärningen          |
| Slumpmässig vågrät vändning  | Valfri     | Boolesk | Sant    | Vänd den aktuella PIL-bilden vågrätt slumpmässigt med en bestämd sannolikhet |
| Slumpmässig lodrät vändning    | Valfri     | Boolesk | Falskt   | Vänd lodrätt den aktuella PIL-bilden slumpmässigt med en bestämd sannolikhet |
| Slumpmässig rotation         | Valfri     | Boolesk | Falskt   | Rotera bilden efter vinkel                |
| Slumpmässiga rotations grader | [0180] | Integer | 0       | Antal grader att välja mellan          |
| Slumpmässig tillhörighet           | Valfri     | Boolesk | Falskt   | Slumpmässig tilldelnings omvandling för avbildningens Center-invariant |
| Slumpmässiga tillhörighets grader   | [0180] | Integer | 0       | Antal grader att välja mellan          |
| Slumpmässig gråskala        | Valfri     | Boolesk | Falskt   | Konvertera bild slumpmässigt till gråskala med sannolikhet 0,1 |
| Slumpmässigt perspektiv      | Valfri     | Boolesk | Falskt   | Utför perspektiv omvandling av den aktuella PIL-avbildningen slumpvis med sannolikhet 0,5 |
| Slumpmässig radering          | Valfri     | Boolesk | Falskt   | Väljer slumpmässigt en Rectangle-region i en bild och raderar dess pixlar med sannolikhet 0,5 |

###  <a name="output"></a>Utdata  

| Namn                        | Typ                    | Beskrivning                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Transformering av utgående avbildning | TransformationDirectory | Transformering av utgående avbildning som kan anslutas till **Använd bild-Transformation** . |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 