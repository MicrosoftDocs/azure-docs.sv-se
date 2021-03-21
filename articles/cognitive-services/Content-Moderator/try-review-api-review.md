---
title: Skapa moderator granskningar med REST API konsol – Content Moderator
titleSuffix: Azure Cognitive Services
description: 'Använd Azure Content Moderator granska API: er för att skapa bilder eller text granskningar för mänsklig redaktör.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: 479c7c455f07d098edd327196803e85df24dfb6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905169"
---
# <a name="create-human-reviews-api-console"></a>Skapa mänsklig granskningar (API-konsol)

[Granskar](./review-api.md#reviews) lagring och visning av innehåll för de mänskliga moderatorerna att utvärdera. När en användare slutför en granskning skickas resultatet till en angiven slut punkt för motringning. I den här guiden får du lära dig hur du ställer in granskningar med hjälp av API-konsolen för att granska REST-API: er. När du förstår API: ernas struktur kan du enkelt Porta dessa anrop till alla REST-kompatibla plattformar.

## <a name="prerequisites"></a>Förutsättningar

- Logga in eller skapa ett konto på webbplatsen för Content Moderator [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="create-a-review"></a>Skapa en granskning

Om du vill skapa en granskning går du till sidan **[Granska – skapa API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** referens och väljer knappen för nyckel regionen (du hittar det i slut punkts-URL: en på sidan **autentiseringsuppgifter** i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/)). Detta startar API-konsolen där du enkelt kan skapa och köra REST API-anrop.

![Granska – Hämta regions val](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Ange REST-anrops parametrar

Ange värden för **teamName** och **OCP-APIM-Subscription-Key**:

- **teamName**: det team-ID som du skapade när du konfigurerade kontot för [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) (finns i fältet **ID** på skärmens inloggnings uppgifter).
- **OCP-APIM-Subscription-Key**: din Content moderator-nyckel. Du hittar detta på fliken **Inställningar** i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Ange en gransknings definition

Redigera rutan **begär ande text** för att ange JSON-begäran med följande fält:

- **Metadata**: anpassade nyckel/värde-par som returneras till återanrops slut punkten. Om nyckeln är en kort kod som definieras i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com)visas den som en tagg.
- **Innehåll**: om bild-och video innehåll är det här en URL-sträng som pekar på innehållet. För text innehåll är detta den faktiska text strängen.
- **ContentId**: en anpassad ID-sträng. Den här strängen skickas till API: et och returneras via återanropet. Det är användbart för att associera interna identifierare eller metadata med resultatet av ett redigerings jobb.
- **CallbackEndpoint**: (valfritt) URL: en som tar emot information om motringning när granskningen är klar.

Standard texten för begäran visar exempel på olika typer av granskningar som du kan skapa:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Skicka din begäran
  
Välj **Skicka**. Om åtgärden lyckas, är **svars statusen** `200 OK` och i rutan **svars innehåll** visas ett ID för granskningen. Kopiera detta ID så att det används i följande steg.

![Granska – skapa innehålls rutan för konsol svar visar gransknings-ID: t](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Granska den nya granskningen

I [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com)väljer du **Granska**  >  **bild** / **text** / **video** (beroende på vilket innehåll du använde). Det innehåll som du överförde bör visas, redo för mänsklig granskning.

![Granska verktygs bild av en fotboll](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Visa gransknings information

Om du vill hämta information om en befintlig granskning går du till sidan [Granska – Hämta API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) referens och väljer knappen för din region (den region där nyckeln administreras).

![Arbets flöde – Hämta regions val](images/test-drive-region.png)

Ange resten anrops parametrar som i avsnittet ovan. I det här steget är **reviewId** den unika ID-strängen som du fick när du skapade granskningen.

![Granska – skapa konsol hämta resultat](images/test-drive-review-3.PNG)
  
Välj **Skicka**. Om åtgärden lyckas, är **svars statusen** `200 OK` och rutan **svars innehåll** visar gransknings informationen i JSON-format, t. ex. följande:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Anteckna följande fält i svaret:

- **statusfältet**
- **reviewerResultTags**: detta visas om några taggar har lagts till manuellt av gruppen mänsklig granskning (visas i fältet **createdBy** ).
- **metadata**: det här visar de taggar som ursprungligen lades till i granskningen, innan gruppen för mänsklig granskning gjorde ändringar.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du skapar granskningar av innehålls granskning med hjälp av REST API. Sedan integrerar du granskningar i ett scenario från slut punkt till slut punkt, till exempel själv studie kursen om [E-handelskontrollanter](./ecommerce-retail-catalog-moderation.md) .