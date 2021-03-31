---
title: Märka bilder snabbare med Smart Labeler
titleSuffix: Azure Cognitive Services
description: I den här guiden får du lära dig hur du använder Smart Labeler för att generera föreslagna taggar för avbildningar. På så sätt kan du märka ett stort antal bilder snabbare när du tränar en Custom Vision modell.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "73647758"
---
# <a name="label-images-faster-with-smart-labeler"></a>Märka bilder snabbare med Smart Labeler

I den här guiden får du lära dig hur du använder Smart Labeler för att generera föreslagna taggar för avbildningar. På så sätt kan du märka ett stort antal bilder snabbare när du tränar en Custom Vision modell.

När du taggar avbildningar för en Custom Vision modell använder tjänsten den senaste utbildade iterationen av modellen för att förutsäga etiketterna för otaggade bilder. Sedan visas dessa förutsägelser som föreslagna Taggar baserat på det valda konfidens tröskelvärdet och förutsägelse osäkerhet. Du kan sedan antingen bekräfta eller ändra förslagen och påskynda processen för att manuellt tagga bilderna för träning.

## <a name="when-to-use-smart-labeler"></a>När du ska använda Smart Labeler

Tänk på följande begränsningar:

* Du bör endast begära föreslagna taggar för bilder vars innehåll redan har tränats en gång. Få inga förslag på en ny tagg som du bara börjar träna.

> [!IMPORTANT]
> Funktionen Smart Labeler använder samma [pris modell](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) som vanliga förutsägelser. Första gången du utlöser föreslagna taggar för en uppsättning avbildningar debiteras du på samma sätt som för förutsägelse samtal. Efter det lagrar tjänsten resultaten för de valda avbildningarna i en databas i 30 dagar, och du kan komma åt dem när som helst utan kostnad inom den perioden. Efter 30 dagar debiteras du om du begär de föreslagna taggarna igen.

## <a name="smart-labeler-workflow"></a>Smart Labeler-arbetsflöde

Följande steg visar hur du använder Smart Labeler:

1. Ladda upp alla dina utbildnings avbildningar till ditt Custom Vision-projekt.
1. Märk en del av din data uppsättning och välj ett lika antal bilder för varje tagg.
    > [!TIP]
    > Se till att du använder alla Taggar som du vill ha förslag på senare.
1. Starta inlärnings processen.
1. När inlärningen är klar navigerar du till vyn **otaggade** och väljer knappen **Hämta föreslagna Taggar** i det vänstra fönstret.
    > [!div class="mx-imgBorder"]
    > ![Knappen föreslagna taggar visas under fliken otaggade bilder.](./media/suggested-tags/suggested-tags-button.png)
1. I popup-fönstret som visas anger du antalet bilder som du vill ha förslag på. Du bör bara hämta inledande märkes förslag för en del av de otaggade bilderna. Du får bättre märkes förslag när du itererar genom den här processen.
1. Bekräfta de föreslagna taggarna och åtgärda eventuella fel.
    > [!TIP]
    > Bilder med föreslagna Taggar sorteras efter deras förutsägelse osäkerhet (lägre värden visar högre tillförlitlighet). Du kan ändra sorterings ordningen med alternativet **Sortera efter osäkerhet** . Om du ställer in ordningen på **hög till låg** kan du korrigera högosäkerhets förutsägelserna först och sedan snabbt bekräfta de låga osäkerheterna.
    * I bild klassificerings projekt kan du välja och bekräfta Taggar i batchar. Filtrera vyn efter en angiven föreslagen tagg, avmarkera bilder som är felaktigt taggade och bekräfta resten i en batch.
        > [!div class="mx-imgBorder"]
        > ![Föreslagna taggar visas i batch-läge för IC med filter.](./media/suggested-tags/ic-batch-mode.png)

        Du kan också använda föreslagna Taggar i ett enskilt bild läge genom att välja en bild från galleriet.

        ![Föreslagna taggar visas i enskilda bild lägen för IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * I objekt identifierings projekt stöds inte batch-bekräftelser, men du kan fortfarande filtrera och sortera efter föreslagna taggar för en mer strukturerad etikett upplevelse. Miniatyrerna av dina otaggade bilder visar ett överlägg med avgränsnings rutor som visar placeringen av föreslagna taggar. Om du inte väljer ett föreslaget märkes filter visas alla dina otaggade bilder utan att täcka över avgränsnings rutor.
        > [!div class="mx-imgBorder"]
        > ![Föreslagna taggar visas i batch-läge för OD med filter.](./media/suggested-tags/od-batch-mode.png)

        Om du vill bekräfta taggar för objekt identifiering måste du tillämpa dem på varje enskild bild i galleriet.

        ![Föreslagna taggar visas i ett enskilt bild läge för OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Starta inlärnings processen igen.
1. Upprepa föregående steg tills du är nöjd med förslags kvaliteten.

## <a name="next-steps"></a>Nästa steg

Följ en snabb start för att komma igång med att skapa och träna ett Custom Vision projekt.

* [Skapa en klassificerare](getting-started-build-a-classifier.md)
* [Skapa en objektidentifierare](get-started-build-detector.md)