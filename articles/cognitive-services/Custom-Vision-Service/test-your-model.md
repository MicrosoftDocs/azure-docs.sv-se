---
title: Testa och träna en modell-Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du testar en avbildning och använder den för att träna om modellen i Custom Visions tjänsten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 736d135323e2a2df38a13dc46189e2a243fbe381
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097542"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testa och träna en modell med Custom Vision Service

När du har tränat din modell kan du snabbt testa den med en lokalt lagrad bild eller en online-avbildning. Testet använder den senast utbildade iterationen.

## <a name="test-your-model"></a>Testa din modell

1. Välj ditt projekt på [Custom vision webb sida](https://customvision.ai). Välj **snabb test** till höger om den översta meny raden. Den här åtgärden öppnar ett fönster med etiketten **snabb test**.

    ![Knappen snabb test visas i det övre högra hörnet i fönstret.](./media/test-your-model/quick-test-button.png)

2. I fönstret **snabb test** klickar du på fältet **överförings avbildning** och anger URL: en för den avbildning som du vill använda för testet. Om du vill använda en lokalt sparad avbildning i stället klickar du på knappen **Bläddra i lokala filer** och väljer en lokal avbildnings fil.

    ![Bild av sidan sändnings avbildning](./media/test-your-model/submit-image.png)

Den bild du väljer visas mitt på sidan. Sedan visas resultaten under bilden i form av en tabell med två kolumner, etiketterade **taggar** och **säkerhet**. När du har granskat resultaten kan du stänga fönstret **snabb test** .

Nu kan du lägga till den här test avbildningen i din modell och sedan träna modellen.

## <a name="use-the-predicted-image-for-training"></a>Använd den förutsagda bilden för utbildning

Använd följande steg för att använda den avbildning som du har skickat in tidigare för utbildning:

1. Om du vill visa bilder som skickats till klassificeraren öppnar du [Custom vision webb sidan](https://customvision.ai) och väljer fliken __förutsägelser__ .

    ![Bild av fliken förutsägelser](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Standardvyn visar bilder från den aktuella iterationen. Du kan använda List rutan __iteration__ för att visa bilder som skickats under föregående iterationer.

2. Hovra över en bild för att se de taggar som förutsägs av klassificeraren.

    > [!TIP]
    > Bilderna rangordnas så att de bilder som kan få flest vinster till klassificeraren visas överst. Använd __sorterings__ avsnittet om du vill välja en annan sortering.

    Om du vill lägga till en bild i dina utbildnings data väljer du bilden, väljer taggen och väljer sedan __Spara och Stäng__. Bilden tas bort från __förutsägelser__ och läggs till i utbildnings avbildningarna. Du kan visa den genom att välja fliken __träna bilder__ .

    ![Bild av taggnings Sidan](./media/test-your-model/tag-image.png)

3. Använd knappen __träna__ för att träna om klassificeraren.

## <a name="next-steps"></a>Nästa steg

[Förbättra din modell](getting-started-improving-your-classifier.md)
