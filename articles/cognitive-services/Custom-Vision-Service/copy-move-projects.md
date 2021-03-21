---
title: Kopiera och flytta Custom Vision projekt
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur du använder API: erna ExportProject och ImportProject för att kopiera och flytta dina Custom Vision-projekt.'
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 7d58a8239c728f70efe3584c2649e196dffd791f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501098"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Kopiera och flytta dina Custom Vision-projekt

När du har skapat och tränat ett Custom Vision projekt kanske du vill kopiera projektet till en annan resurs. Du kanske till exempel vill flytta ett projekt från en utveckling till en produktions miljö eller säkerhetskopiera ett projekt till ett konto i en annan Azure-region för ökad data säkerhet.

**[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** -och **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** -API: erna aktiverar det här scenariot genom att du kan kopiera projekt från ett Custom vision konto till andra. Den här guiden visar hur du använder dessa REST API: er med hjälp av sväng. Du kan också använda en HTTP-begäran som Postman för att utfärda begär Anden.

## <a name="business-scenarios"></a>Affärs scenarier

Om din app eller ditt företag är beroende av att ett Custom Vision-projekt används, rekommenderar vi att du kopierar din modell till ett annat Custom Vision konto i en annan region. Om ett regionalt avbrott inträffar kan du komma åt projektet i den region där det kopierades.

##  <a name="prerequisites"></a>Förutsättningar

- Två Azure Custom Vision-resurser. Om du inte har dem går du till Azure Portal och [skapar en ny Custom vision resurs](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- Utbildnings nycklar och slut punkts-URL: er för dina Custom Vision resurser. Du hittar dessa värden på resurs-fliken **Översikt** på Azure Portal.
- Ett Custom Vision-projekt som skapats. Mer information om hur du gör detta finns i [skapa en klassificerare](./getting-started-build-a-classifier.md) .
* [PowerShell version 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows), eller ett liknande kommando rads verktyg.

## <a name="process-overview"></a>Process översikt

Processen för att kopiera ett projekt består av följande steg:

1. Först får du ID: t för projektet i ditt käll konto som du vill kopiera.
1. Sedan anropar du **ExportProject** -API: et med projekt-ID: t och utbildnings nyckeln för ditt käll konto. Du får en tillfällig token-sträng.
1. Sedan anropar du **ImportProject** -API: et med hjälp av token-strängen och utbildnings nyckeln för ditt mål konto. Projektet visas sedan under ditt mål konto.

## <a name="get-the-project-id"></a>Hämta projekt-ID

Först anropa **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** för att se en lista över befintliga Custom vision-projekt och deras ID. Använd tränings nyckeln och slut punkten för ditt käll konto.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

Du får ett `200\OK` svar med en lista över projekt och deras metadata i bröd texten. `"id"`Värdet är den sträng som ska kopieras för nästa steg.

```json
[
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "string",
    "description": "string",
    "settings": {
      "domainId": "00000000-0000-0000-0000-000000000000",
      "classificationType": "Multiclass",
      "targetExportPlatforms": [
        "CoreML"
      ],
      "useNegativeSet": true,
      "detectionParameters": "string",
      "imageProcessingSettings": {
        "augmentationMethods": {}
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>Exportera projektet

Anropa **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** med projekt-ID: t och din käll tränings nyckel och slut punkt.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

Du får ett `200/OK` svar med metadata om det exporterade projektet och en referens sträng `"token"` . Kopiera värdet för token.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>Importera projektet

Anropa **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** med hjälp av målets utbildnings nyckel och slut punkt, tillsammans med referens-token. Du kan också ge ditt projekt ett namn i det nya kontot.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

Du får ett `200/OK` svar med metadata om ditt nyligen importerade projekt.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du kopierar och flyttar ett projekt mellan Custom Vision-resurser. Nu ska du utforska API-referenserna för att se vad mer du kan göra med Custom Vision.
* [REST API referens dokumentation](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)