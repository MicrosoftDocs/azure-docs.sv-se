---
title: Anpassa språk modell med Video Indexer webbplats
titleSuffix: Azure Media Services
description: Lär dig hur du anpassar en språk modell med Video Indexer webbplats.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: dd8b36340deb6c785989107461dd420e7fc0d985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97722580"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Anpassa en språk modell med Video Indexer webbplats

Med Video Indexer kan du skapa anpassade språk modeller för att anpassa tal igenkänning genom att överföra anpassnings text, nämligen text från den domän vars vokabulär du vill att motorn ska anpassa sig till. När du tränar din modell kommer nya ord som visas i anpassnings texten att identifieras.

En detaljerad översikt och bästa praxis för anpassade språk modeller finns i [Anpassa en språk modell med video Indexer](customize-language-model-overview.md).

Du kan använda Video Indexer webbplats för att skapa och redigera anpassade språk modeller i ditt konto, enligt beskrivningen i det här avsnittet. Du kan också använda API: et enligt beskrivningen i [Anpassa språk modell med hjälp av API: er](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Skapa en språk modell

1. Gå till [video Indexer](https://www.videoindexer.ai/) webbplats och logga in.
1. Om du vill anpassa en modell i ditt konto väljer du knappen **anpassning av innehålls modell** till vänster på sidan.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-language-model/model-customization.png" alt-text="Anpassa innehålls modell i Video Indexer":::
1. Välj fliken **språk** .

    Du ser en lista över språk som stöds.
1. Välj **Lägg till modell** under det språk som du vill använda.
1. Skriv in namnet på språk modellen och tryck på RETUR.

    Det här steget skapar modellen och ger alternativet att ladda upp textfiler till modellen.
1. Om du vill lägga till en textfil väljer du **Lägg till fil**. Utforskaren öppnas.
1. Navigera till och välj text filen. Du kan lägga till flera textfiler i en språk modell.

    Du kan också lägga till en textfil genom att välja knappen **...** på höger sida av språk modellen och välja **Lägg till fil**.
1. När du är klar med att ladda upp textfilerna väljer du alternativet grönt **träna** .

Inlärnings processen kan ta några minuter. När utbildningen är färdig ser du **tränad** bredvid modellen. Du kan förhandsgranska, ladda ned och ta bort filen från modellen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/customize-language-model/customize-language-model.png" alt-text="Träna modellen":::

### <a name="using-a-language-model-on-a-new-video"></a>Använda en språk modell i en ny video

Gör något av följande om du vill använda din språk modell på en ny video:

* Välj knappen **överför** överst på sidan.

    ![Knappen Överför Video Indexer](./media/customize-language-model/upload.png)
* Ta bort ljud-eller video filen eller bläddra efter filen.

Du har möjlighet att välja **språk för video källan**. Välj den nedrullningsbara List rutan och välj en språk modell som du skapade i listan. Det bör säga språket för din språk modell och det namn som du gav det inom parentes. Exempel:

![Välj video källans språk – indexera om en video med Video Indexer](./media/customize-language-model/reindex.png)

Välj alternativet **överför** längst ned på sidan så kommer din nya video att indexeras med hjälp av din språk modell.

### <a name="using-a-language-model-to-reindex"></a>Använda en språk modell för att indexera om

Följ dessa steg om du vill använda din språk modell för att indexera om en video i samlingen:

1. Logga in på Start sidan för [video Indexer](https://www.videoindexer.ai/) .
1. Klicka på knappen **...** på videon och välj **Indexera igen**.
1. Du har möjlighet att välja det språk för **video källan** som du vill indexera om videon med. Välj den nedrullningsbara List rutan och välj en språk modell som du skapade i listan. Det bör säga språket för din språk modell och det namn som du gav det inom parentes.
1. Välj knappen för att indexera om och din video kommer att indexeras på **nytt** med hjälp av din språk modell.

## <a name="edit-a-language-model"></a>Redigera en språk modell

Du kan redigera en språk modell genom att ändra dess namn, lägga till filer i den och ta bort filer från den.

Om du lägger till eller tar bort filer från språk modellen måste du träna modellen igen genom att välja alternativet för grönt **träna** .

### <a name="rename-the-language-model"></a>Byt namn på språk modellen

Du kan ändra namnet på språk modellen genom att välja knappen med tre punkter (**...**) på höger sida av språk modellen och välja **Byt namn**.

Skriv det nya namnet och tryck på RETUR.

### <a name="add-files"></a>Lägg till filer

Om du vill lägga till en textfil väljer du **Lägg till fil**. Utforskaren öppnas.

Navigera till och välj text filen. Du kan lägga till flera textfiler i en språk modell.

Du kan också lägga till en textfil genom att välja knappen med tre punkter (**...**) på höger sida av språk modellen och välja **Lägg till fil**.

### <a name="delete-files"></a>Ta bort filer

Om du vill ta bort en fil från språk modellen väljer du knappen med tre punkter (**...**) på höger sida av text filen och väljer **ta bort**. Ett nytt fönster öppnas och du får en uppmaning om att borttagningen inte kan utföras. Välj alternativet **ta bort** i det nya fönstret.

Den här åtgärden tar bort filen helt från språk modellen.

## <a name="delete-a-language-model"></a>Ta bort en språk modell

Om du vill ta bort en språk modell från ditt konto väljer du knappen med tre punkter (**...**) på höger sida av språk modellen och väljer **ta bort**.

Ett nytt fönster öppnas och du får en uppmaning om att borttagningen inte kan utföras. Välj alternativet **ta bort** i det nya fönstret.

Den här åtgärden tar bort språk modellen helt från ditt konto. Alla videoklipp som använder den borttagna språk modellen behåller samma index tills du omindexerat videon. Om du Omindexerar videon kan du tilldela en ny språk modell till videon. Annars kommer Video Indexer att använda sin standard modell för att Omindexera videon.

## <a name="customize-language-models-by-correcting-transcripts"></a>Anpassa språk modeller genom att korrigera avskrifter

Video Indexer stöder automatisk anpassning av språk modeller baserat på de faktiska korrigeringarna som användarna gör i avskrifterna av sina videor.

1. Om du vill göra ändringar i en avskrift öppnar du den video som du vill redigera från dina konto videor. Välj fliken **tids linje** .

    ![Fliken anpassa tids linje för språk modell – Video Indexer](./media/customize-language-model/timeline.png)

1. Välj Penn ikonen för att redigera avskriften för avskriften.

    ![Anpassa språk modell redigera avskrift – Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer fångar alla rader som korrigeras av dig i avskriften av videon och lägger till dem automatiskt i en textfil med namnet "från avskrifts redigeringar". Dessa ändringar används för att omträna den specifika språk modell som användes för att indexera den här videon. 
    
    De ändringar som har gjorts i [widgetens](video-indexer-embed-widgets.md) tids linje ingår också.
    
    Om du inte angav någon språk modell vid indexering av den här videon, lagras alla redigeringar för videon i en standard språk modell som kallas "konto anpassning" på det identifierade språket för videon.
    
    Om flera ändringar har gjorts på samma rad används bara den senaste versionen av den korrigerade raden för att uppdatera språk modellen.  
    
    > [!NOTE]
    > Endast text korrigeringar används för anpassningen. Korrigeringar som inte omfattar faktiska ord (till exempel skiljetecken eller blank steg) ingår inte.
    
1. Avskrifts korrigeringar visas på fliken språk på sidan anpassning av innehålls modell.

   Om du vill titta på filen "från avskrifts redigeringar" för var och en av dina språk modeller väljer du den för att öppna den.

    ![Från avskrifts redigeringar – Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Nästa steg

[Anpassa språk modellen med hjälp av API: er](customize-language-model-with-api.md)
