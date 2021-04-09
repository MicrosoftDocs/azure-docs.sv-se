---
title: Integrera Azure Storage för meddelanden och modell säkerhets kopiering
titleSuffix: Azure Cognitive Services
description: Lär dig hur du integrerar Azure Storage för att ta emot push-meddelanden när du tränar eller exporterar Custom Vision modeller. Du kan också spara en säkerhets kopia av exporterade modeller.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fd4ad1443f466a78abfc569d5f52f6bdeff2d5be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048892"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Integrera Azure Storage för meddelanden och säkerhets kopiering

Du kan integrera ditt Custom Vision-projekt med en Azure Blob Storage-kö för att få push-meddelanden om projekt utbildning/-export-aktivitet och säkerhets kopior av publicerade modeller. Den här funktionen är användbar för att undvika kontinuerlig avsökning av tjänsten för resultat när tids krävande åtgärder körs. I stället kan du integrera meddelanden i lagrings kön i ditt arbets flöde.

Den här guiden visar hur du använder dessa REST API: er med hjälp av sväng. Du kan också använda en HTTP-begäran som Postman för att utfärda begär Anden.

> [!NOTE]
> Push-meddelanden är beroende av den valfria _notificationQueueUri_ -parametern i **CreateProject** -API: et, och modell säkerhets kopieringar kräver att du också använder den valfria parametern _exportModelContainerUri_ . I den här guiden används både för en fullständig uppsättning funktioner.

## <a name="prerequisites"></a>Förutsättningar

- En Custom Vision-resurs i Azure. Om du inte har en sådan går du till Azure Portal och [skapar en ny Custom vision resurs](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). Den här funktionen stöder för närvarande inte den kognitiva tjänst resursen (alla i en nyckel).
- Ett Azure Storage konto med en BLOB-behållare. Följ [Övning 1 i Azure Storage labbet](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) om du behöver hjälp med det här steget.
* [PowerShell version 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows)eller liknande kommando rads program.

## <a name="set-up-azure-storage-integration"></a>Konfigurera integrering av Azure Storage

Gå till din Custom Vision utbildnings resurs på Azure Portal, Välj sidan **identitet** och aktivera systemtilldelad hanterad identitet.

Gå sedan till lagrings resursen i Azure Portal. Gå till sidan **åtkomst kontroll (IAM)** och Lägg till en roll tilldelning för varje integrerings funktion:
* Välj din Custom Vision utbildnings resurs och tilldela rollen **Storage BLOB data Contributor** om du planerar att använda funktionen för modell säkerhets kopiering. 
* Välj sedan din Custom Vision utbildnings resurs och tilldela **lagrings köns data deltagare** om du planerar att använda funktionen aviserings kön.

> [!div class="mx-imgBorder"]
> ![Sidan Lägg till roll tilldelning för lagrings konto](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>Hämta integrations-URL: er

Därefter får du de URL: er som ger din Custom Vision-resurs åtkomst till dessa slut punkter.

För integrations webb adressen för meddelande kön går du till sidan **köer** i ditt lagrings konto, lägger till en ny kö och sparar dess URL på en tillfällig plats.

> [!div class="mx-imgBorder"]
> ![Sidan Azure Storage Queue](./media/storage-integration/queue-url.png) 

För integrerings-URL: en för modell säkerhets kopiering går du till sidan **behållare** för ditt lagrings konto och skapar en ny behållare. Markera det och gå till sidan **Egenskaper** . Kopiera URL: en till en tillfällig plats.
 
> [!div class="mx-imgBorder"]
> ![Egenskaps sida för Azure Storage-behållare](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Integrera Custom Vision projekt

Nu när du har integrations-URL: erna kan du skapa ett nytt Custom Vision-projekt som integrerar Azure Storage funktionerna. Du kan också uppdatera ett befintligt projekt om du vill lägga till funktionerna.

### <a name="create-new-project"></a>Skapa nytt projekt

När du anropar [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) -API: et lägger du till de valfria parametrarna _exportModelContainerUri_ och _notificationQueueUri_. Tilldela URL-värdena som du fick i föregående avsnitt. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

Om du får ett `200/OK` svar innebär det att URL: erna har kon figurer ATS korrekt. Du bör se dina URL-värden i JSON-svaret även:

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
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Uppdatera befintligt projekt

Om du vill uppdatera ett befintligt projekt med Azure Storage-funktionen kan du anropa [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) -API: et med ID: t för det projekt som du vill uppdatera. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

Ange begär ande texten ( `body` ) till följande JSON-format, Fyll i lämpliga värden för _ExportModelContainerUri_ och _notificationQueueUri_:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

Om du får ett `200/OK` svar innebär det att URL: erna har kon figurer ATS korrekt.

## <a name="verify-the-connection"></a>Verifiera anslutningen 

Ditt API-anrop i föregående avsnitt bör redan ha utlöst ny information i ditt Azure Storage-konto. 

I den angivna behållaren bör det finnas en test-BLOB i en **CustomVision-TestPermission-** mapp. Denna BLOB kommer bara att finnas tillfälligt.

I meddelande kön bör du se ett test meddelande i följande format:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Hämta händelse meddelanden

När du är klar kan du anropa [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) -API: et i projektet för att utföra en vanlig utbildning.

I lagrings aviserings kön får du ett meddelande när inlärningen är klar:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"`Fältet kan vara antingen `"TrainingCompleted"` eller `"TrainingFailed"` . `"iterationId"`Fältet är ID: t för den tränade modellen.

## <a name="get-model-export-backups"></a>Hämta säkerhets kopior av modell export

När du är klar kan du anropa [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) -API: et för att exportera en utbildad modell till en angiven plattform.

I din angivna lagrings behållare visas en säkerhets kopia av den exporterade modellen. BLOB-namnet kommer att ha formatet:

```
{projectId} - {iterationId}.{platformType}
```

Du får också ett meddelande i kön när exporten är klar. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"`Fältet kan vara antingen `"ExportCompleted"` eller `"ExportFailed"` . `"modelUri"`Fältet innehåller URL: en för den säkerhets kopierings modell som lagras i din behållare, förutsatt att du har integrerat meddelanden i kön i början. Om du inte gjorde det visas `"modelUri"` SAS-URL: en för din Custom vision Model-blob.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du kopierar och flyttar ett projekt mellan Custom Vision-resurser. Nu ska du utforska API-referenserna för att se vad mer du kan göra med Custom Vision.
* [REST API referens dokumentation (utbildning)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [REST API Reference Documentation (förutsägelse)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)