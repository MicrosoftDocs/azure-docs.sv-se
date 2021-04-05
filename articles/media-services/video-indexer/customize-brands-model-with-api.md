---
title: Anpassa en modell med Video Indexer-API
titleSuffix: Azure Media Services
description: Lär dig hur du anpassar en modell med Video Indexer-API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: c3056415d3432dbe64dd1f2bcf974a676bbf6c6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586065"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Anpassa en modell med ett varumärke med Video Indexer-API

Video Indexer stöder varumärkes identifiering från tal-och visuell text vid indexering och Omindexering av video-och ljud innehåll. Funktionen varumärkes identifiering identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bing-databasens varumärken. Om Microsoft till exempel nämns i video-eller ljud innehåll, eller om det visas i visuell text i en video, identifierar Video Indexer det som ett varumärke i innehållet. Med en modell med anpassade varumärken kan du undanta vissa varumärken från att identifieras och innehålla varumärken som ska ingå i din modell som kanske inte finns i Bing-databasen. Mer information finns i [Översikt](customize-brands-model-overview.md).

> [!NOTE]
> Om videon var indexerad innan du lade till ett varumärke måste du indexera om den.

Du kan använda Video Indexer API: er för att skapa, använda och redigera anpassade varumärke modeller som identifieras i en video, enligt beskrivningen i det här avsnittet. Du kan också använda Video Indexer webbplats som beskrivs i [Anpassa varumärkes-modellen med hjälp av video Indexer webbplats](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Skapa ett varumärke

[Skapa ett varumärke](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) -API skapar ett nytt anpassat varumärke och lägger till det i den anpassade varumärkes-modellen för det angivna kontot.

> [!NOTE]
> Inställningen `enabled` (i bröd texten) till True placerar varumärket i listan *ta med* för video Indexer att identifiera. `enabled`Om du anger false placeras varumärket i *uteslutnings* listan, så video Indexer inte att identifiera det.

Några andra parametrar som du kan ange i texten:

* `referenceUrl`Värdet kan vara alla referens webbplatser för varumärket, till exempel en länk till en wikipedia-sida.
* `tags`Värdet är en lista med taggar för varumärket. Den här taggen visas i fältet för märkets *kategori* på video Indexer webbplats. Till exempel kan märket "Azure" märkas eller kategoriseras som "moln".

### <a name="response"></a>Svarsåtgärder

Svaret innehåller information om det varumärke som du nyss skapade enligt formatet i exemplet nedan.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Ta bort ett varumärke

[Ta bort ett varumärke](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) -API tar bort ett varumärke från anpassade varumärkes-modellen för det angivna kontot. Kontot anges i `accountId` parametern. När det har anropats kommer varumärket inte längre att finnas i listorna *Inkludera* eller *exkludera* varumärken.

### <a name="response"></a>Svarsåtgärder

Det finns inget returnerat innehåll när varumärket har tagits bort.

## <a name="get-a-specific-brand"></a>Skaffa ett speciellt varumärke

Med [Hämta ett varumärke](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) -API kan du söka efter information om ett varumärke i anpassade varumärkes-modellen för det angivna kontot med hjälp av märkes-ID.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller information om det märke som du sökte efter (med varumärkes-ID) enligt exemplet nedan.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled` anges till `true` visar att varumärket är i listan *inkludering* för video Indexer att identifiera och att det `enabled` är falskt betyder att varumärket är i *uteslutnings* listan, så video Indexer inte identifierar det.

## <a name="update-a-specific-brand"></a>Uppdatera ett speciellt varumärke

Med [Uppdatera en varumärkes](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) -API kan du söka efter information om ett varumärke i anpassade varumärkes-modellen för det angivna kontot med hjälp av varumärkes-ID.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller den uppdaterade informationen om det varumärke som du uppdaterade efter formatet i exemplet nedan.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Hämta alla varumärken

API: t [Hämta alla varumärken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) returnerar alla varumärken i den anpassade varumärkes-modellen för det angivna kontot, oavsett om varumärket är avsett att finnas i listan *Inkludera* eller *exkludera* varumärken.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller en lista över alla varumärken i ditt konto och var och en av sina uppgifter efter formatet i exemplet nedan.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Varumärket *varumärke är i* listan *Inkludera* för video Indexer för att identifiera och varumärket med namnet *Example2* finns i *uteslutnings* listan så video Indexer inte att identifiera det.

## <a name="get-brands-model-settings"></a>Hämta inställningar för varumärkes modell

Med API: erna för [Get-inställningar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) returneras modell inställningarna för varumärke i det angivna kontot. Modell inställningarna för varumärken representerar om identifiering från Bing-databasen är aktive rad eller inte. Om Bing-varumärken inte är aktiverade, kommer Video Indexer bara identifiera varumärken från den anpassade varumärke modellen för det angivna kontot.

### <a name="response"></a>Svarsåtgärder

Svaret visar om Bing-varumärken är aktiverade efter formatet i exemplet nedan.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` anges till sant anger att Bing-varumärken är aktiverade. Om `useBuiltin` är falskt inaktive ras Bing-varumärken. `state`Värdet kan ignoreras eftersom det är inaktuellt.

## <a name="update-brands-model-settings"></a>Uppdatera modell inställningar för varumärke

[Uppdateringen varumärkes](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) -API uppdaterar modell inställningarna för varumärkena i det angivna kontot. Modell inställningarna för varumärken representerar om identifiering från Bing-databasen är aktive rad eller inte. Om Bing-varumärken inte är aktiverade, kommer Video Indexer bara identifiera varumärken från den anpassade varumärke modellen för det angivna kontot.

`useBuiltIn`Flaggan som anges till True innebär att Bing-varumärken är aktiverade. Om `useBuiltin` är falskt inaktive ras Bing-varumärken.

### <a name="response"></a>Svarsåtgärder

Det finns inget returnerat innehåll när modell inställningen för varumärken har uppdaterats.

## <a name="next-steps"></a>Nästa steg

[Anpassa varumärkes-modellen med hjälp av webbplats](customize-brands-model-with-website.md)
