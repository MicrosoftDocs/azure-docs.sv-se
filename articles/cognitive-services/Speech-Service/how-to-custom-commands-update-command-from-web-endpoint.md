---
title: Uppdatera ett kommando från en webbslutpunkt
titleSuffix: Azure Cognitive Services
description: Lär dig hur du uppdaterar statusen för ett kommando genom att använda ett anrop till en webb slut punkt.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560278"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Uppdatera ett kommando från en webbslutpunkt

Om klient programmet kräver en uppdatering av statusen för ett pågående kommando utan röst inspelning kan du använda ett anrop till en webb slut punkt för att uppdatera kommandot.

I den här artikeln får du lära dig hur du uppdaterar ett pågående kommando från en webb slut punkt.

## <a name="prerequisites"></a>Förutsättningar
> [!div class = "checklist"]
> * En tidigare [skapad app för Anpassade kommandon](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion 

I det här exemplet behöver du en HTTP-utlöst Azure- [funktion](../../azure-functions/index.yml) som stöder följande indatatyper (eller en delmängd av den här indatamängden):

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Vi går igenom de viktigaste attributen för den här ingången:

| Attribut | Förklaring |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Den unika identifieraren för konversationen. Observera att detta ID kan genereras från klient programmet. |
| **currentCommand** | Det kommando som är aktivt i konversationen. |
| **Namn** | Kommandots namn. `parameters`Attributet är en karta med de aktuella värdena för parametrarna. |
| **currentGlobalParameters** | En karta som till exempel `parameters` används för globala parametrar. |

Resultatet av Azure-funktionen måste ha stöd för följande format:

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Du kan känna igen det här formatet eftersom det är samma som du använde när du [uppdaterade ett kommando från klienten](./how-to-custom-commands-update-command-from-client.md). 

Skapa nu en Azure-funktion baserat på Node.js. Kopiera/klistra in den här koden:

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

När du anropar den här Azure-funktionen från anpassade kommandon skickar du de aktuella värdena i konversationen. Du kommer att returnera de parametrar som du vill uppdatera eller om du vill avbryta det aktuella kommandot.

## <a name="update-the-existing-custom-commands-app"></a>Uppdatera den befintliga anpassade kommando-appen

Nu ska vi ansluta till Azure-funktionen med den befintliga anpassade kommando-appen:

1. Lägg till ett nytt kommando med namnet `IncrementCounter` .
1. Lägg bara till en exempel mening med värdet `increment` .
1. Lägg till en ny parameter med namnet `Counter` (samma namn som anges i Azure-funktionen) av typen `Number` med standardvärdet `0` .
1. Lägg till en ny webb slut punkt som heter `IncrementEndpoint` med URL: en för din Azure-funktion, där **fjär uppdateringar** har angetts till **aktive rad**.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Skärm bild som visar inställning av en webb slut punkt med fjärruppdateringar.":::
1. Skapa en ny interaktions regel med namnet **IncrementRule** och Lägg till en åtgärd för **anrops webb slut punkt** .
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Skärm bild som visar hur en interaktions regel skapas.":::
1. I åtgärds konfigurationen väljer du `IncrementEndpoint` . Konfigurera **på klar** för att **Skicka tal svar** med värdet `Counter` och konfigurera **vid fel** med ett fel meddelande.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Skärm bild som visar inställning av en öknings räknare för att anropa en webb slut punkt.":::
1. Ange att regelns efter körnings tillstånd ska **vänta på användarens indata**.

## <a name="test-it"></a>Testa den

1. Spara och träna din app.
1. Välj **Testa regel**.
1. Skicka `increment` några gånger (som är exempel meningen för `IncrementCounter` kommandot).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Skärm bild som visar ett exempel på en öknings räknare.":::

Observera hur Azure-funktionen ökar värdet för `Counter` parametern i varje tur.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera en CI/CD-process för programmet Anpassade kommandon](./how-to-custom-commands-deploy-cicd.md)