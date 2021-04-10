---
title: Granska data kvaliteten för Custom Speech tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Custom Speech innehåller verktyg som gör det möjligt att visuellt inspektera igenkännings kvaliteten för en modell genom att jämföra ljuddata med motsvarande igenkännings resultat. Du kan spela upp ljud som laddats upp och avgöra om det angivna igenkännings resultatet är korrekt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: erhopf
ms.openlocfilehash: 9ce0d3a06846cbc3aa37ab836564150e6f2c34ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100362819"
---
# <a name="inspect-custom-speech-data"></a>Inspektera Custom Speech-data

> [!NOTE]
> På den här sidan förutsätter vi att du har läst [förbereda test data för Custom Speech](./how-to-custom-speech-test-and-train.md) och har laddat upp en data uppsättning för inspektion.

Custom Speech innehåller verktyg som gör det möjligt att visuellt inspektera igenkännings kvaliteten för en modell genom att jämföra ljuddata med motsvarande igenkännings resultat. Från [tal Studio](https://speech.microsoft.com/customspeech)kan du spela upp ljud som laddats upp och avgöra om det angivna igenkännings resultatet är korrekt. Med det här verktyget kan du kontrol lera kvaliteten på Microsofts grundläggande tal-till-text-modell, inspektera en utbildad anpassad modell eller jämföra avskrifter med två modeller.

I det här dokumentet får du lära dig att visuellt kontrol lera kvaliteten på Microsofts bas linje tal till text-modell och/eller anpassade modeller som du har tränat. Du lär dig också hur du använder redigerings redigeraren för att skapa och förfina etiketterade ljud data uppsättningar.

## <a name="create-a-test"></a>Skapa ett test

Följ de här anvisningarna för att skapa ett test:

1. Logga in på [tal Studio](https://speech.microsoft.com/customspeech).
2. Navigera till **> för tal till text Custom Speech > [namn på projektet] > testning**.
3. Klicka på **Lägg till test**.
4. Välj **inspektera kvalitet (endast ljuddata)**. Ge testet ett namn, en beskrivning och välj din ljud data uppsättning.
5. Välj upp till två modeller som du vill testa.
6. Klicka på **Skapa**.

När ett test har skapats kan du se hur en modell anger den ljud data uppsättning du angav eller jämför resultat från två modeller sida vid sida.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Jämförelser av modeller sida vid sida

När test status är _klar_ klickar du på test objekt namnet för att se information om testet. Den här informations sidan visar alla yttranden i din data uppsättning och visar igenkännings resultatet för de två modeller som du jämför.

För att kunna kontrol lera jämförelsen sida vid sida kan du växla mellan olika typer av fel, inklusive infogning, borttagning och ersättning. Genom att lyssna på ljudet och jämföra igenkännings resultaten i varje kolumn (med mänsklig avskrift och resultatet av två tal till text-modeller) kan du bestämma vilken modell som uppfyller dina behov och var förbättringar behövs.

Modell testning sida vid sida är användbar för att validera vilken tal igenkännings modell som passar bäst för ett program. Om du vill ha ett objektivt mått på precision, som kräver uppmätta ljud, följer du anvisningarna i [utvärdera noggrannhet](how-to-custom-speech-evaluate-data.md).

## <a name="online-transcription-editor"></a>Redigerare för online-avskrift

Med avskrifts redigeraren online kan du enkelt arbeta med ljud avskrifter i Custom Speech. Huvud användnings fallen i redigeraren är följande: 

* Du har bara ljuddata, men vill bygga upp korrekt ljud-och data uppsättningar med mänsklig etikett från grunden till användning i modell träning.
* Du har redan ljud och data uppsättningar med mänsklig etikett, men det finns fel eller fel i avskriften. Med redigeraren kan du snabbt ändra avskrifterna för att få bästa möjliga inlärning.

Det enda kravet att använda avskrifts redigeraren är att överföra ljuddata (antingen ljud-eller ljud-och avskrifter).

### <a name="import-datasets-to-editor"></a>Importera data uppsättningar till redigerare

Om du vill importera data till redigeraren går du först till **Custom Speech > [ditt projekt] > redigeraren**.

![Flik för redigerare](media/custom-speech/custom-speech-editor-detail.png)

Använd sedan följande steg för att importera data.

1. Klicka på **Importera data**
1. Skapa en ny data uppsättning (er) och ge den en beskrivning
1. Välj data uppsättningar. Flera val stöds och du kan välja enbart ljuddata, samt ljud-och mänsklig data.
1. För endast ljuddata kan du välja att använda standard modeller för att automatiskt generera dator avskrift när du har importerat till redigeraren
1. Klicka på **Importera**

När data har importer ATS kan du klicka i data uppsättningarna och börja redigera.

> [!TIP]
> Du kan också importera data uppsättningar till redigeraren direkt genom att välja data uppsättningar och klicka på **Exportera till redigerare**

### <a name="edit-transcription-by-listening-to-audio"></a>Redigera avskrift genom att lyssna på ljud

När data överföringen har slutförts klickar du på varje objekt namn för att se information om data. Du kan också använda **föregående** och **Nästa** för att flytta mellan varje fil.

Detalj sidan visar alla segment i varje ljudfil och du kan klicka i önskad uttryck. För varje uttryck kan du spela upp ljudet och granska avskrifterna och redigera avskrifterna om du hittar några infognings-, borttagnings-eller ersättnings fel. Mer information om fel typer finns i avsnittet [data utvärderings anvisningar](how-to-custom-speech-evaluate-data.md) .

![Sidan redigerare](media/custom-speech/custom-speech-editor.png)

När du har gjort ändringarna klickar du på knappen **Spara** .

### <a name="export-datasets-from-the-editor"></a>Exportera data uppsättningar från redigeraren

Om du vill exportera data uppsättningar tillbaka till fliken **data** navigerar du till sidan data information och klickar på knappen **Exportera** för att exportera alla filer som en ny data uppsättning. Du kan också filtrera filerna efter senaste redigerade tid, ljud varaktigheter osv. för att delvis välja önskade filer. 

![Exportera data](media/custom-speech/custom-speech-editor-export.png)

De filer som exporteras till data används som en helt ny data uppsättning och kommer inte att påverka någon av de befintliga data-/inlärnings-/test enheterna.

## <a name="next-steps"></a>Nästa steg

- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
- [Träna modellen](how-to-custom-speech-train-model.md)
- [Förbättra din modell](./how-to-custom-speech-evaluate-data.md)
- [Distribuera din modell](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

- [Förbered test data för Custom Speech](./how-to-custom-speech-test-and-train.md)