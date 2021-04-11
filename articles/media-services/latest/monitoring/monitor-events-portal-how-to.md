---
title: Övervaka Media Services händelser med Event Grid Portal
description: Den här artikeln visar hur du prenumererar på Event Grid för att övervaka Azure Media Services händelser.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 1e288e4490916a7ee03371baa8e7cc3b798e272b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961718"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Skapa och övervaka Media Services-händelser med Event Grid med hjälp av Azure-portalen

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Azure Event Grid är en händelsetjänst för molnet. Den här tjänsten använder [händelse prenumerationer](../../../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Media Services händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Media Services-händelse eftersom egenskapen eventType börjar med "Microsoft. Media.". Mer information finns i [Media Services händelse scheman](media-services-event-schemas.md).

I den här artikeln använder du Azure Portal för att prenumerera på händelser för ditt Azure Media Services-konto. Sedan utlöser du händelser för att visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. I artikeln skickar vi händelser till en webbapp som samlar in och visar meddelandena.

När du är klar kan se du att händelsedata som har skickats till webbappen.

## <a name="prerequisites"></a>Förutsättningar 

* En aktiv Azure-prenumeration.
* Skapa ett nytt Azure Media Services-konto, som beskrivs i [den här snabbstarten](../account-create-how-to.md).

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på händelser för Media Services kontot ska vi skapa slut punkten för händelse meddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. I den här artikeln distribuerar du en [fördefinierad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar händelse meddelandena. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

1. Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

   [![Bild som visar en knapp med etiketten "distribuera till Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Om du växlar till webbplatsen "Azure Event Grid Viewer" visas inga händelser än.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Prenumerera på Media Services händelser

Du prenumererar på ett ämne därför att du vill ange för Event Grid vilka händelser du vill följa och vart du vill skicka händelserna.

1. I portalen väljer du ditt Media Services konto och väljer **händelser**.
1. För att skicka händelser till visningsprogrammet använder du en webhook för slutpunkten. 

   ![Välj webhook](../media/monitor-events-portal/select-web-hook.png)

1. Händelse prenumerationen är förifylld med värden för ditt Media Services-konto. 
1. Välj Web Hook för **slut punkts typen**.
1. I det här avsnittet lämnar vi **prenumerationen på alla händelse typer** som kontrol leras. Du kan dock avmarkera det och filtrera efter vissa händelse typer. 
1. Klicka på länken **Välj en slut punkt** .

    För webhookens slutpunkt anger du webbappens webbadress och lägger till `api/updates` till startsidans webbadress. 

1. Tryck på **Bekräfta markering**.
1. Tryck på **Skapa**.
1. Namnge prenumerationen.

   ![Välj loggar](../media/monitor-events-portal/create-subscription.png)

1. Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. 

    Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Slut punkten måste anges `validationResponse` till `validationCode` . Mer information finns i [Event Grid säkerhet och autentisering](../../../event-grid/security-authentication.md). Du kan visa webb program koden för att se hur den validerar prenumerationen.

Nu ska vi utlösa händelser för att se hur Event Grid distribuerar meddelandet till din slut punkt.

## <a name="send-an-event-to-your-endpoint"></a>Skicka en händelse till din slutpunkt

Du kan utlösa händelser för Media Services kontot genom att köra ett kodnings jobb. Du kan följa [den här snabb](../stream-files-dotnet-quickstart.md) starten för att koda en fil och börja skicka händelser. Om du prenumererar på alla händelser visas en skärm som liknar följande:

> [!TIP]
> Välj ögonikonen för att utöka informationen om händelsen. Uppdatera inte sidan om du vill visa alla händelser.

![Visa prenumerationshändelse](../media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Nästa steg

[Ladda upp, koda och strömma](../stream-files-tutorial-with-api.md)
