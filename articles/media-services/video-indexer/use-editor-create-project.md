---
title: Använd Video Indexer redigeraren för att skapa projekt och lägga till videoklipp
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du använder Video Indexer redigeraren för att skapa projekt och lägga till video klipp.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 07e4b05e12a5994c707a171c5736aea04a9c9723
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632934"
---
# <a name="add-video-clips-to-your-projects"></a>Lägga till video klipp i dina projekt

På [video Indexer](https://www.videoindexer.ai/) webbplats kan du använda videons djup insikter för att: hitta rätt medie innehåll, leta upp de delar som du är intresse rad av och Använd resultatet för att skapa ett helt nytt projekt. 

När projektet har skapats kan projektet renderas och hämtas från Video Indexer och användas i dina egna redigerings program eller i efterföljande arbets flöden.

Vissa scenarier där den här funktionen kan vara användbar är: 

* Skapa film markeringar för släp vagnar.
* Använda gamla klipp av videor i nyhets brev.
* Skapa kortare innehåll för sociala medier.

Den här artikeln visar hur du skapar ett projekt och lägger till valda klipp från videor till projektet. 

## <a name="create-new-project-and-manage-videos"></a>Skapa nytt projekt och hantera videor

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
1. Välj fliken **projekt** . Om du har skapat projekt tidigare visas alla dina andra projekt här.
1. Klicka på **Skapa nytt projekt**.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Skapa ett nytt projekt":::
1. Ge projektet ett namn genom att klicka på Penn ikonen. Ersätt texten som säger "namnlöst projekt" med ditt projekt namn och klicka på krysset.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Ett nytt projekt":::
    
### <a name="add-videos-to-the-project"></a>Lägga till videor i projektet

> [!NOTE]
> För närvarande kan projekt bara innehålla videor som indexerats på samma språk. </br>När du har valt en video på ett språk kan du inte lägga till videor i ditt konto på ett annat språk, men videor som har andra språk kommer att tonas ut/inaktive ras.

1. Lägg till videor som du vill arbeta med i det här projektet genom att välja **Lägg till videor**.

    Du kommer att se alla videor i ditt konto och en sökruta med texten "söka efter text, nyckelord eller visuellt innehåll". Du kan söka efter videor som har en angiven person, etikett, varumärke, nyckelord eller förekomst i avskriften och OCR.
    
    I bilden nedan letar vi till exempel efter videor som nämner "anpassad vision" i avskrift (Använd **filter** om du vill filtrera Sök resultaten).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="Skärm bild som visar sökning efter videor som nämner anpassad vision":::
1. Klicka på **Lägg till** för att lägga till videor i projektet.
1. Nu visas alla videor som du har valt. Det här är de videor från vilka du ska välja klipp för ditt projekt.

    Du kan ändra ordningen på videor genom att dra och släppa eller genom att välja menyn List meny och välja **Flytta ned** eller **Flytta upp**. Du kan också ta bort videon från det här projektet från menyn lista. 
    
    Du kan när som helst lägga till fler videor i projektet genom att välja **Lägg till videor**. Du kan också lägga till flera förekomster av samma video i projektet. Du kanske vill göra detta om du vill visa ett klipp från en video och sedan ett klipp från en annan och ett annat klipp från den första videon. 

### <a name="select-clips-to-use-in-your-project"></a>Välj klipp som ska användas i projektet

Om du klickar på nedåtpilen till höger om varje video kommer du att öppna insikterna i videon baserat på tidsstämplar (klipp i videon). 

1. Om du vill skapa frågor för vissa klipp använder du sökrutan med texten "Sök i avskrift, visuell text, personer och etiketter".
1. Välj **Visa insikter** för att anpassa vilka insikter du vill se och vilka du inte vill se. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="Skärm bild som visar sökning efter videor som säger testa kognitiva tjänster":::
1. Lägg till filter för att ytterligare ange information om vilka scener du letar efter genom att välja **filter alternativ**.

    Du kan lägga till flera filter. 
1. När du är nöjd med resultaten väljer du de klipp som du vill lägga till i projektet genom att välja det segment som du vill lägga till. Du kan avmarkera det här klippet genom att klicka på segmentet igen.
    
    Lägg till alla segment i en video (eller, allt som returnerades efter sökningen) genom att klicka på meny alternativet lista bredvid videon och välja **Markera alla**. 

När du väljer och sorterar dina klipp kan du förhandsgranska videon i spelaren på höger sida av sidan. 

> [!IMPORTANT]
> Kom ihåg att spara projektet när du gör ändringar genom att välja **Spara projekt** överst på sidan. 

### <a name="render-and-download-the-project"></a>Rendera och ladda ned projektet

> [!NOTE]
> För Video Indexer betalda konton har åter givningen av projektet kodnings kostnader. Video Indexer utvärderings konton är begränsade till 5 timmars åter givning.

1. När du är färdig kontrollerar du att projektet har sparats. Nu kan du återge det här projektet. Klicka på **Render**, en popup-dialog ruta visas som talar om att video Indexer återger en fil och att nedladdnings länken skickas till e-postmeddelandet. Välj Fortsätt. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="Skärm bild som visar Video Indexer med alternativet att återge och ladda ned ditt projekt":::
    
    Du ser också ett meddelande om att projektet återges ovanpå sidan. När den är klar visas ett nytt meddelande om att projektet har Render ATS. Klicka på meddelandet för att ladda ned projektet. Projektet laddas ned i MP4-format.
1. Du kan komma åt sparade projekt från fliken **projekt** . 

    Om du väljer det här projektet visas alla insikter och tids linjen för projektet. Om du väljer **video redigerare** kan du fortsätta att göra ändringar i det här projektet. Redigeringarna inkluderar att lägga till eller ta bort videor och klipp eller att byta namn på projektet.
    
## <a name="create-a-project-from-your-video"></a>Skapa ett projekt från videon

Du kan skapa ett nytt projekt direkt från en video i ditt konto. 

1. Gå till fliken **bibliotek** på video Indexer webbplats.
1. Öppna den video som du vill använda för att skapa projektet. På sidan insikter och tids linje väljer du knappen för **video redigeraren** .

    Detta tar dig till samma sida som du använde för att skapa ett nytt projekt. Till skillnad från det nya projektet ser du de tidsstämplade insikts segmenten i videon som du började redigera tidigare.

## <a name="see-also"></a>Se även

[Översikt över Video Indexer](video-indexer-overview.md)

