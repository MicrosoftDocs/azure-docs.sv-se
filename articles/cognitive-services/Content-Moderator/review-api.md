---
title: Granskningar, arbets flöden och jobb koncept – Content Moderator
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig mer om huvud begreppen i gransknings verktyget. recensioner, arbets flöden och jobb.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 43c39c40af6e02861211a8666fefa57c34072f32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905204"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Granskningar av innehålls moderatorer, arbets flöden och jobb

Content Moderator kombinerar dator-assisterad redaktör med funktioner för mänsklig inblandning för att skapa en optimal redigerings process för verkliga scenarier. Detta sker via det molnbaserad [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com). I den här guiden får du lära dig mer om huvud begreppen i gransknings verktyget: granskningar, arbets flöden och jobb.

## <a name="reviews"></a>Omdömen

I en granskning överförs innehållet till gransknings verktyget. Du kan visa den genom att klicka på dess innehålls typ under fliken **Granska** på instrument panelen. Från gransknings skärmen kan du ändra de använda taggarna och använda dina egna anpassade taggar efter behov. När du skickar en granskning skickas resultatet till en angiven slut punkt för återanrop och innehållet tas bort från platsen.

> [!div class="mx-imgBorder"]
> ![Den nedrullningsbara menyn granskning är markerad. Den visar dessa innehålls typer: bild, text och video.](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>Hantera granskningar

Från instrument panelen navigerar du till **Administration**  ->  **Hantera granskningar** för att Visa administratörs skärmen. Här kan du se en lista över alla granskningar (väntande och avslutade).

Med knappen tre punkt **åtgärder** i varje granskning kan du gå till gransknings skärmen eller granska historiken för den granskningen.

> [!div class="mx-imgBorder"]
> ![Granska verktygs webbplatsen på gransknings skärmen](./Review-Tool-user-Guide/images/manage-reviews.png)

Använd verktygsfältet **Sök** för att sortera granskningar efter en rad olika kategorier, till exempel gransknings status, taggar, innehålls typ, under grupper, tilldelade användare och skapat/ändrat datum.

> [!div class="mx-imgBorder"]
> ![Verktygsfältet Sök visas. Den har olika kombinations rutor för att ange Sök villkor, till exempel gransknings status och taggar.](./Review-Tool-user-Guide/images/review-search.png)

Se [guiden granska verktyg](./review-tool-user-guide/review-moderated-images.md) för att komma igång med att skapa recensioner eller se [API-konsolens guide](./try-review-api-review.md) för att lära dig hur du gör det program mässigt.

## <a name="workflows"></a>Arbetsflöden

Ett arbets flöde är ett molnbaserad anpassat filter för innehåll. Arbets flöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och vidta lämplig åtgärd. Med Content Moderator-anslutningen kan ett arbets flöde automatiskt tillämpa kontrollanter och skapa recensioner med innehåll som har skickats.

### <a name="view-workflows"></a>Visa arbets flöden

Om du vill visa dina befintliga arbets flöden går du till [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) och väljer **admin**-  >  **arbetsflöden**.

> [!div class="mx-imgBorder"]
> ![Standard arbets flöde](images/default-workflow-list.png)

Arbets flöden definieras som JSON-strängar som gör dem tillgängliga via programmering. Om du väljer **redigerings** alternativet för arbets flödet och sedan väljer fliken **JSON** visas ett JSON-uttryck som följande:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

I [guiden granska verktyg](./review-tool-user-guide/workflows.md) kan du komma igång med att skapa och använda arbets flöden, eller se [API-konsolens guide](./try-review-api-workflow.md) för att lära dig hur du gör det program mässigt.

## <a name="jobs"></a>Jobb

Ett redigerings jobb fungerar som en typ av omslutning för funktionerna i Content moderatoring, arbets flöden och granskningar. Jobbet skannar ditt innehåll med hjälp av API: et för Content Moderator bild redigeringsprogram eller API för text redigering och kontrollerar det mot det angivna arbets flödet. Utifrån arbets flödes resultatet kan det hända att det inte går att skapa en granskning för innehållet i [gransknings verktyget](./review-tool-user-guide/human-in-the-loop.md). Både granskningar och arbets flöden kan skapas och konfigureras med deras respektive API: er, men jobb-API: et gör att du kan få en detaljerad rapport över hela processen (som kan skickas till en angiven slut punkt för återanrop).

I [Guide för API-konsolen](./try-review-api-job.md) kan du komma igång med jobb.

## <a name="next-steps"></a>Nästa steg

* Testkör jobb- [API-konsolen](try-review-api-job.md)och Använd REST API kod exempel. Om du är bekant med Visual Studio och C# kan du också ta en titt på [jobben .net snabb start](moderation-jobs-quickstart-dotnet.md). 
* För recensioner kan du komma igång med [Granska API-konsolen](try-review-api-review.md)och använda REST API kod exempel. Se avsnittet recensioner i [.net-snabb](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)starten.
* För video granskningar använder du [snabb starten för video granskning](video-reviews-quickstart-dotnet.md)och lär dig hur du [lägger till avskrifter i video granskningen](video-transcript-reviews-quickstart-dotnet.md).