---
title: Videoinspelning med gransknings verktyget – Content Moderator
titleSuffix: Azure Cognitive Services
description: Använd datorbaserade videokontrollanter och gransknings verktyget för att måttligt olämpligt innehåll
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 05ece4d8ea24fb9da106ec3daa2cc1a63706dab9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96853506"
---
# <a name="video-moderation-with-the-review-tool"></a>Videoinspelning med gransknings verktyget

Använd Content Moderator datorbaserade [videokontrollanter](video-moderation-api.md) och [gransknings verktyg](Review-Tool-User-Guide/human-in-the-loop.md) för att få till gång till måttliga videor och avskrifter för vuxen (Explicit) och vågat (förslag) innehåll för att få bästa möjliga resultat för ditt företag.

## <a name="view-videos-under-review"></a>Visa videor under granskning

På instrument panelen väljer du någon av gransknings köerna inom video innehålls typen. Då startar en granskning och öppnar sidan för inspelning av video innehåll.

> [!div class="mx-imgBorder"]
> ![På Content Moderator (för hands version) är skjutreglaget markerat och inställt på 4 granskningar. Växlarna oskärpa alla och svart och vit är markerade och båda är inställda.](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Recensionsantal

Använd skjutreglaget i det övre högra hörnet för att ange antalet granskningar som du vill ska visas på sidan.

### <a name="view-type"></a>Vytyp

Du kan visa olika innehålls poster som paneler eller i en detaljerad vy. I **detaljvyn** kan du se viktiga bild rutor och annan information om den valda videon. 

> [!NOTE]
> I stället för att lägga till ramar med jämna mellanrum, identifierar videoinspelnings tjänsten och matar bara ut potentiellt slutförda (lämpliga) ramar. Den här funktionen möjliggör effektiv ram-generering för barns och vågat analys på radnivå.

I vyn **sida vid sida** visas varje video som en enda panel. Välj knappen expandera ovanför en video ram för att förstora videon och dölja de andra.

### <a name="content-obscuring-effects"></a>Innehåll – dölja effekter

Använd alternativet **oskärpa alla** och **svart och vitt** för att ställa in dessa effekter för att dölja innehåll. De är aktiverade som standard. I vyn **sida vid sida** kan du växla effekterna individuellt för varje video.

## <a name="check-video-details"></a>Kontrol lera video information

I detaljvyn **visar** den högra rutan flera flikar som ger dig information om videon.

* Välj fliken **anteckningar** om du vill lägga till anpassade anteckningar i videor.
* Välj fliken **avskrift** för att se video avskriften &mdash; . tjänsten extraherar automatiskt en avskrift för alla tal i videon. När du väljer ett text avsnitt hoppar Videos pelaren till den delen av videon.
* Välj fliken **meta-data** för att visa video filens metadata.
* Välj fliken **Historik** om du vill se historiken för granskningen, till exempel när den skapades och hur den ändrades.

> [!div class="mx-imgBorder"]
> ![Den högra rutan är markerad och fliken anteckningar är markerad. Det finns ett test områden med etiketten Lägg till en anteckning.](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Använd moderator Taggar

Den huvudsakliga uppgiften i en video granskning är att tillämpa eller ta bort moderatorer på videor eller delar av videor.

### <a name="bulk-tagging"></a>Mass markering

I verktygsfältet för **Mass etiketter** kan du lägga till taggar till flera markerade videor samtidigt. Välj en eller flera videor och välj sedan de taggar som du vill använda och klicka på **Skicka**. 

> [!div class="mx-imgBorder"]
> ![Knappen + är markerad i fönstret massredigera taggar.](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Nyckel Rute taggning

Du kan också lägga till moderator Taggar i vissa nyckel ramar. Välj bild rutorna i panel rutan nyckel ram och välj sedan **nyckel Rute Taggar +** för att använda de önskade taggarna.

> [!NOTE]
> Om tjänsten inte kan extrahera nyckel rutor visas **inga tillgängliga ramar** i panel rutan nyckel ram och alternativet för att välja nyckel rutor är nedtonat. I det här fallet kan du bara använda taggar för videon som helhet (med **video taggarna +** knappen).

> [!div class="mx-imgBorder"]
> ![Panelerna panel, Videos pelare, nyckel Rute Taggar och rutor för video etiketter visas. Nyckel Rute taggarna + och video taggarna + knappar är markerade.](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Spärra en granskning

Med knappen **Håll** nere i video fönstret kan du se till att en granskning är stoppad så att du kan hämta den och slutföra den senare. Du kan göra detta för en granskning som kräver en annan grupp medlem eller ansvarig som för tillfället inte är tillgänglig. 

Du kan se vilka videor som är kvar genom att klicka på knappen **Håll** högst upp på skärmen. Rutan Behåll visas till höger. Härifrån kan du välja flera granskningar och antingen släppa dem i kön igen eller ange deras förfallo tid. Efter den förkonfigurerade tiden frigörs granskningar av undantag tillbaka till kön. Välj **Spara** för att börja räkna från den för tillfället valda förfallo tiden.

> [!div class="mx-imgBorder"]
> ![I fönstret video är knappen håll markerad. Längst ned i fönstret markeras en kombinations ruta för parkerad tid, tillsammans med knapparna för att frigöra och spara.](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>Skicka en granskning

När du har tillämpat dina Taggar väljer du knappen **Skicka** längst ned i fönstret video. Om du har taggat flera videor kan du skicka dem till en enda granskning eller som separata granskningar.

## <a name="limbo-state"></a>Limbo-tillstånd

När du har skickat en granskning flyttas videon till **limbo** -läget, som du kan visa genom att välja knappen **limbo** överst på skärmen. Videor är kvar i limbo-tillstånd för en förkonfigurerad tids period (som du kan ändra i menyn längst ned), eller tills de har granskats igen eller skickats manuellt.

När videoerna går ut från limbo markeras deras granskningar som slutförda.

## <a name="next-steps"></a>Nästa steg

- Kom igång med snabb starten för [videoinspelning](video-moderation-api.md).
- Lär dig hur du genererar [video granskningar](video-reviews-quickstart-dotnet.md) för dina mänskliga granskare från dina kontrollerade utdata.
- Lägg till [video avskrifts granskningar](video-transcript-reviews-quickstart-dotnet.md) i dina video granskningar.
