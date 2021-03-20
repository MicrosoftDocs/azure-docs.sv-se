---
title: Använda bildtransformering
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Använd avbildnings omvandling för att tillämpa en avbildnings omvandling till en avbildnings katalog.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: a64d5cebfd8e70e2f54a66193a7041c47887c54a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898918"
---
# <a name="apply-image-transformation"></a>Använda bildtransformering 

I den här artikeln beskrivs hur du använder modulen Använd avbildnings-transformering i Azure Machine Learning designer för att ändra en katalog för indata-avbildningar baserat på en tidigare angiven avbildnings omvandling.  

Du måste ansluta en modul för [omvandling av init-bilder](init-image-transformation.md) för att ange omvandlingen och sedan kan du använda en sådan omvandling i katalogen för indata-avbildningar i modulen Använd avbildnings omvandling.

## <a name="how-to-use-apply-image-transformation"></a>Använda omvandling av Använd avbildning  

1. Lägg till modulen **Använd avbildnings omvandling** i din pipeline. Du hittar den här modulen under *visuellt innehåll/bild data omvandlings* kategori. 

2. Anslut utdata från **init-bildtransformering** till den vänstra inmatningen av **Använd avbildnings omvandling**.

     > [!NOTE]
     > Den här modulen accepterar bara avbildnings omvandlingen som genererats av modulen [init-omvandling](init-image-transformation.md) . För annan typ av omvandling ansluter du den för att [tillämpa omvandlingen](apply-transformation.md), annars kommer InvalidTransformationDirectoryError att genereras.


3. Anslut den avbildnings katalog som du vill transformera.

4. För **läge** anger du i vilket syfte du använder transformationen av inflödet: "för utbildning" eller "för härledning". 

   Om du väljer **för utbildning** tillämpas all omvandling som du anger i omvandling av init-avbildning.

   Om du väljer **för att få en härledning** utesluts omvandlingen som att skapa nya exempel slumpmässigt innan den tillämpas. Detta beror på att omvandlings åtgärder för att skapa nya exempel slumpvis som "slumpmässig horisontell vändning" används för data förstärkning i träning, som bör tas bort i en härledning eftersom det måste finnas ett härlednings exempel som måste åtgärdas för korrekt förutsägelse och utvärdering.

   > [!NOTE]
   > Transformationer som ska undantas i läget **för härledning** är: slumpmässig storlek på beskärning, slumpmässig beskärning, slumpmässig vågrät vändning, slumpmässig lodrät vändning, slumpmässig rotation, slumpmässig tillhörighet, slumpmässig gråskala, slumpmässigt perspektiv, slumpmässig radering.

5. Om du vill tillämpa en avbildnings omvandling i en ny avbildnings katalog skickar du pipelinen.  

### <a name="module-parameters"></a>Parametrar för modul

| Name | Intervall | Typ | Standardvärde                   | Beskrivning                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Läge | Valfri   | Läge | (Kräv att användaren ska ange) | För vilket ändamål du använder Transformation av inflöde. Du bör undanta slumpmässiga transformerings åtgärder i en härlednings process men se till att de är i utbildning |

### <a name="expected-inputs"></a>Förväntade indata  

| Namn                       | Typ                    | Beskrivning                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Transformering av inspelnings avbildning | TransformationDirectory | Transformering av inspelnings avbildning        |
| Avbildnings katalog för indata      | ImageDirect          | Avbildnings katalog som ska transformeras |

### <a name="outputs"></a>Utdata  

| Namn                   | Typ           | Beskrivning            |
| ---------------------- | -------------- | ---------------------- |
| Avbildnings katalog för utdata | ImageDirect | Avbildnings katalog för utdata |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
