---
title: Anpassa en språk modell med Video Indexer API
titlesuffix: Azure Media Services
description: Lär dig hur du anpassar en språk modell med Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: f373afae03357ffb65eb459f806fe441e29b21b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047076"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Anpassa en språk modell med Video Indexer API

Med Video Indexer kan du skapa anpassade språk modeller för att anpassa tal igenkänning genom att överföra anpassnings text, nämligen text från den domän vars vokabulär du vill att motorn ska anpassa sig till. När du tränar din modell kommer nya ord som visas i anpassnings texten att identifieras.

En detaljerad översikt och bästa praxis för anpassade språk modeller finns i [Anpassa en språk modell med video Indexer](customize-language-model-overview.md).

Du kan använda Video Indexer API: er för att skapa och redigera anpassade språk modeller i ditt konto, enligt beskrivningen i det här avsnittet. Du kan också använda webbplatsen, enligt beskrivningen i [Anpassa språk modell med hjälp av video Indexer webbplats](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Skapa en språk modell

Med [skapa ett språk modells](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) -API skapar du en ny anpassad språk modell i det angivna kontot. Du kan ladda upp filer för språk modellen i det här anropet. Du kan också skapa språk modellen här och ladda upp filer för modellen senare genom att uppdatera språk modellen.

> [!NOTE]
> Du måste fortfarande träna modellen med de aktiverade filerna för modellen för att lära dig innehållet i dess filer. Anvisningar för utbildning ett språk finns i nästa avsnitt.

Om du vill ladda upp filer som ska läggas till i språk modellen måste du ladda upp filer i texten med FormData, förutom att ange värden för de obligatoriska parametrarna ovan. Det finns två sätt att utföra den här uppgiften:

* Nyckeln är fil namnet och värdet är txt-filen.
* Nyckeln är fil namnet och värdet är en URL till txt-filen.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller metadata för den nyligen skapade språk modellen tillsammans med metadata på var och en av modellens filer som följer formatet på det här exemplet JSON-utdata:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Träna en språk modell

[Träna en språk modell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) API tågen en anpassad språk modell i det angivna kontot med innehållet i de filer som överförts till och Aktiver ATS i språk modellen.

> [!NOTE]
> Du måste först skapa språk modellen och överföra dess filer. Du kan ladda upp filer när du skapar språk modellen eller genom att uppdatera språk modellen.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller metadata för den nytränade språk modellen tillsammans med metadata på var och en av modellens filer som följer formatet på det här exemplet JSON-utdata:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Det returnerade `id` är ett unikt ID som används för att skilja mellan språk modeller, medan `languageModelId` används både för att [Ladda upp en video för att indexera](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) och [Indexera om en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) -api: er (även kallat `linguisticModelId` video Indexer uppladdning/index-API: er).

## <a name="delete-a-language-model"></a>Ta bort en språk modell

[Ta bort ett språk modells](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) -API tar bort en anpassad språk modell från det angivna kontot. Alla videoklipp som använder den borttagna språk modellen behåller samma index tills du omindexerat videon. Om du Omindexerar videon kan du tilldela en ny språk modell till videon. Annars kommer Video Indexer att använda sin standard modell för att Omindexera videon.

### <a name="response"></a>Svarsåtgärder

Det finns inget returnerat innehåll när språk modellen har tagits bort.

## <a name="update-a-language-model"></a>Uppdatera en språk modell

[Uppdateringen av ett språk modell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) -API uppdaterar en anpassad språk modell i det angivna kontot.

> [!NOTE]
> Du måste redan ha skapat språk modellen. Du kan använda det här anropet för att aktivera eller inaktivera alla filer under modellen, uppdatera namnet på språk modellen och ladda upp filer som ska läggas till i språk modellen.

Om du vill ladda upp filer som ska läggas till i språk modellen måste du ladda upp filer i texten med FormData, förutom att ange värden för de obligatoriska parametrarna ovan. Det finns två sätt att utföra den här uppgiften:

* Nyckeln är fil namnet och värdet är txt-filen.
* Nyckeln är fil namnet och värdet är en URL till txt-filen.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller metadata för den nytränade språk modellen tillsammans med metadata på var och en av modellens filer som följer formatet på det här exemplet JSON-utdata:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Använd de `id` filer som returneras i svaret för att ladda ned innehållet i filen.

## <a name="update-a-file-from-a-language-model"></a>Uppdatera en fil från en språk modell

Med [Uppdatera en fil](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) kan du uppdatera namn och `enable` tillstånd för en fil i en anpassad språk modell i det angivna kontot.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller metadata för den fil som du uppdaterade efter formatet på exemplet JSON-utdata nedan.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Använd `id` filen som returnerades i svaret för att ladda ned innehållet i filen.

## <a name="get-a-specific-language-model"></a>Hämta en specifik språk modell

[Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) -API: et returnerar information om den angivna språk modellen i det angivna kontot, till exempel språk och de filer som finns i språk modellen.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller metadata på den angivna språk modellen tillsammans med metadata på var och en av modellens filer som följer formatet på det här exemplet JSON-utdata:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Använd `id` filen som returnerades i svaret för att ladda ned innehållet i filen.

## <a name="get-all-the-language-models"></a>Hämta alla språk modeller

[Hämta alla](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API: er returnerar alla anpassade språk modeller i det angivna kontot i en lista.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller en lista över alla språk modeller i ditt konto och var och en av deras metadata och filer efter formatet på det här exemplet JSON-utdata:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Ta bort en fil från en språk modell

[Borttagnings](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) -API: n tar bort den angivna filen från den angivna språk modellen i det angivna kontot.

### <a name="response"></a>Svarsåtgärder

Det finns inget returnerat innehåll när filen tas bort från språk modellen.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Hämta metadata för en fil från en språk modell

[Hämta metadata för ett fil](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) -API returnerar innehållet i och metadata för den angivna filen från den valda språk modellen i ditt konto.

### <a name="response"></a>Svarsåtgärder

Svaret innehåller innehåll och metadata för filen i JSON-format, ungefär som i det här exemplet:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Innehållet i den här exempel filen är orden "Hello" och "World" i två separata rader.

## <a name="download-a-file-from-a-language-model"></a>Ladda ned en fil från en språk modell

[Hämtningen av ett fil](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) -API laddar ned en textfil som innehåller innehållet i den angivna filen från den angivna språk modellen i det angivna kontot. Text filen måste matcha innehållet i text filen som ursprungligen överfördes.

### <a name="response"></a>Svarsåtgärder

Svaret kommer att hämtas till en textfil med innehållet i filen i JSON-format.

## <a name="next-steps"></a>Nästa steg

[Anpassa språk modellen med hjälp av webbplats](customize-language-model-with-website.md)
