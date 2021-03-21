---
title: Definiera moderator arbets flöden med REST API-konsolen – Content Moderator
titleSuffix: Azure Cognitive Services
description: 'Du kan använda Azure Content Moderator gransknings-API: er för att definiera anpassade arbets flöden och tröskelvärden baserat på dina innehålls principer.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 79749533d636f4b73ff3bef6b12d9e842ac485ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905183"
---
# <a name="define-and-use-moderation-workflows-api-console"></a>Definiera och använda moderator arbets flöden (API-konsol)

Arbets flöden är molnbaserade anpassade filter som du kan använda för att hantera innehåll mer effektivt. Arbets flöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och vidta lämplig åtgärd. Den här guiden visar hur du använder REST-API: er för arbets flöden via API-konsolen för att skapa och använda arbets flöden. När du förstår API: ernas struktur kan du enkelt Porta dessa anrop till alla REST-kompatibla plattformar.

## <a name="prerequisites"></a>Förutsättningar

- Logga in eller skapa ett konto på webbplatsen för Content Moderator [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="create-a-workflow"></a>Skapa ett arbetsflöde

Om du vill skapa eller uppdatera ett arbets flöde går du till sidan **[arbets flöde – skapa eller uppdatera API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** referens och väljer knappen för nyckel området. Du kan hitta din region i slut punkts-URL: en på sidan **autentiseringsuppgifter** i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/). Detta startar API-konsolen där du enkelt kan skapa och köra REST API-anrop.

![Arbets flöde – skapa eller uppdatera val av sid region](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Ange REST-anrops parametrar

Ange värden för **team**, **workflowname** och **OCP-APIM-Subscription-Key**:

- **team**: det team-ID som du skapade när du konfigurerade kontot för [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) (finns i fältet **ID** på skärmens inloggnings uppgifter).
- **workflowname**: namnet på ett nytt arbets flöde som ska läggas till (eller ett befintligt namn om du vill uppdatera ett befintligt arbets flöde).
- **OCP-APIM-Subscription-Key**: din Content moderator-nyckel. Du hittar den här nyckeln på fliken **Inställningar** i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com).

![Arbets flöde – skapa eller uppdatera parametrar och rubriker för konsolen](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Ange en arbets flödes definition

1. Redigera rutan **begär ande text** för att ange JSON-begäran med information om **Beskrivning** och **typ** (antingen `Image` eller `Text` ).
2. För **uttryck** kopierar du standard uttrycket för arbets flödets JSON. Den slutgiltiga JSON-strängen bör se ut så här:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Du kan definiera enkla, komplexa och till och med kapslade uttryck för dina arbets flöden med hjälp av det här API: et. [Arbets flödet – skapa eller uppdatera](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dokumentationen innehåller exempel på mer komplex logik.

### <a name="submit-your-request"></a>Skicka din begäran
  
Välj **Skicka**. Om åtgärden lyckas visas **svars status** `200 OK` och rutan **svars innehåll** visas `true` .

### <a name="examine-the-new-workflow"></a>Granska det nya arbets flödet

I [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/)väljer du **Inställningar**  >  **arbets flöden**. Det nya arbets flödet bör visas i listan.

![Gransknings verktygs lista över arbets flöden](images/workflow-console-new-workflow.PNG)

Välj **redigerings** alternativet för arbets flödet och gå till fliken **Designer** . Här kan du se en intuitiv representation av JSON-logiken.

![Fliken Designer för ett valt arbets flöde](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Hämta arbets flödes information

Om du vill hämta information om ett befintligt arbets flöde går du till sidan **[arbets flöde – Hämta API-](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** referens och väljer knappen för din region (den region där nyckeln administreras).

![Arbets flöde – Hämta regions val](images/test-drive-region.png)

Ange resten anrops parametrar som i avsnittet ovan. Se till att den här tiden, **workflowname** är namnet på ett befintligt arbets flöde.

![Hämta frågeparametrar och rubriker](images/workflow-get-default.PNG)

Välj **Skicka**. Om åtgärden lyckas, är **svars statusen** och i `200 OK` rutan **svars innehåll** visas arbets flödet i JSON-format, som i följande exempel:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder arbets flöden med [innehålls redigerings jobb](try-review-api-job.md).