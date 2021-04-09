---
title: Använda moderator jobb med REST API-konsolen – Content Moderator
titleSuffix: Azure Cognitive Services
description: 'Använd gransknings-API: s jobb åtgärder för att initiera jobb från slut punkt till slut punkt för innehålls redigering för bilder eller text innehåll i Azure Content Moderator.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 924c21037a464770fac13c9b45ddcf261ff5a058
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905187"
---
# <a name="define-and-use-moderation-jobs-api-console"></a>Definiera och använda moderator jobb (API-konsolen)

Ett redigerings jobb fungerar som en typ av omslutning för funktionerna i Content moderatoring, arbets flöden och granskningar. Den här guiden visar hur du använder REST-API: er för jobb för att initiera och kontrol lera innehålls moderator jobb. När du förstår API: ernas struktur kan du enkelt Porta dessa anrop till alla REST-kompatibla plattformar.

## <a name="prerequisites"></a>Förutsättningar

- Logga in eller skapa ett konto på webbplatsen för Content Moderator [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) .
- Valfritt [Definiera ett anpassat arbets flöde](./Review-Tool-User-Guide/Workflows.md) som ska användas med ditt jobb. Du kan också använda standard arbets flödet.

## <a name="create-a-job"></a>Skapa ett jobb

Om du vill skapa ett redigerings jobb går du till sidan [jobb-skapa API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) referens och väljer knappen för din prenumerations region. Du kan hitta din region i slut punkts-URL: en på sidan **autentiseringsuppgifter** i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/). Detta startar API-konsolen där du enkelt kan skapa och köra REST API-anrop.

![Jobb – skapa val av sid region](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Ange REST-anrops parametrar

Ange följande värden för att konstruera REST-anropet:

- **teamName**: det team-ID som du skapade när du konfigurerade kontot för [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) (finns i fältet **ID** på skärmens inloggnings uppgifter).
- **ContentType**: Detta kan vara "image", "text" eller "video".
- **ContentId**: en anpassad ID-sträng. Den här strängen skickas till API: et och returneras via återanropet. Det är användbart för att associera interna identifierare eller metadata med resultatet av ett redigerings jobb.
- **Workflowname**: namnet på arbets flödet som du skapade tidigare (eller "standard" för standard arbets flödet).
- **CallbackEndpoint**: (valfritt) URL: en som tar emot information om motringning när granskningen är klar.
- **OCP-APIM-Subscription-Key**: din Content moderator-nyckel. Du hittar den här nyckeln på fliken **Inställningar** i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Fyll i begär ande texten

Innehållet i REST-anropet innehåller ett fält, **ContentValue**. Klistra in innehållet i rå text om du redigerar text eller ange en bild-eller video-URL om du vill ha en bild eller video. Du kan använda följande URL för exempel bild: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Jobb – skapa parametrar för konsolens fråga, rubriker och brödtext i begäran](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Skicka din begäran

Välj **Skicka**. Om åtgärden lyckas, är **svars statusen** `200 OK` och i rutan **svars innehåll** visas ett ID för jobbet. Kopiera detta ID så att det används i följande steg.

![Granska – skapa innehålls rutan för konsol svar visar gransknings-ID: t](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Hämta jobb status

Om du vill hämta status och information om ett pågående eller slutfört jobb går du till sidan [jobb-Hämta API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) referens och väljer knappen för din region (den region där nyckeln administreras).

![Jobb – Hämta regions val](images/test-drive-region.png)

Ange resten anrops parametrar som i avsnittet ovan. I det här steget är **jobId** den unika ID-sträng som du fick när du skapade jobbet. Välj **Skicka**. Om åtgärden lyckas, är **svars statusen** och i `200 OK` rutan **svars innehåll** visas jobbet i JSON-format, t. ex. följande:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Jobb – Hämta REST samtals svar](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Granska nya granskningar

Om ditt innehålls jobb resulterade i att en granskning skapades, kan du se det i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com). Välj **Granska**  >  **bild** / **text** / **video** (beroende på vilket innehåll du använde). Innehållet bör visas, redo för mänsklig granskning. När en mänsklig moderator granskar de automatiskt tilldelade taggarna och förutsägelse data och skickar ett slutligt kontroll beslut skickar jobb-API: n all den här informationen till den angivna slut punkten för återanrop.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du skapar och frågar efter innehålls redigerings jobb med hjälp av REST API. Sedan integrerar du jobb i ett scenario från slut punkt till slut punkt, till exempel själv studie kursen om [E-handelskontrollanter](./ecommerce-retail-catalog-moderation.md) .