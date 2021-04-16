---
title: 'Snabbstart: Dirigera Azure Cache for Redis händelser till webbslutpunkten med Azure Portal'
description: Använd Azure Event Grid att prenumerera på Azure Cache for Redis händelser, skicka händelserna till en Webhook och hantera händelserna i en webbapp
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.custom:
- mode-portal
ms.openlocfilehash: e021f386f255f1cef61e28cbd4fd6116fc2aa727
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529312"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Snabbstart: Dirigera Azure Cache for Redis händelser till webbslutpunkten med Azure Portal

Azure Event Grid är en händelsetjänst för molnet. I den här snabbstarten använder du Azure Portal för att skapa en Azure Cache for Redis-instans, prenumerera på händelser för den instansen, utlösa en händelse och visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. Men för att förenkla den här snabbstarten skickar du händelser till en webbapp som samlar in och visar meddelandena. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

När du är klar ser du att händelsedata har skickats till webbappen.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Viewer-skalning i JSON-format.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Skapa en Azure Cache for Redis cacheinstans 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på händelser för cacheinstansen ska vi skapa slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabbstarten distribuerar du [en förbyggd webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar händelsemeddelandena. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

1. Välj **Distribuera till Azure** i GitHub README för att distribuera lösningen till din prenumeration. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Knappen Distribuera till Azure.":::

2. Gör följande **på** sidan Anpassad distribution: 
    1. För **Resursgrupp** väljer du den resursgrupp som du skapade när du skapade cacheinstansen. Det blir enklare för dig att rensa när du är klar med självstudien genom att ta bort resursgruppen.  
    2. För **Webbplatsnamn** anger du ett namn för webbappen.
    3. I **Värdplansnamn** anger du ett namn på App Service plan som ska användas som värd för webbappen.
    4. Markera kryssrutan för Jag **godkänner de villkor som anges ovan.** 
    5. Välj **Köp**. 
    
    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Prenumeration** | Välj din prenumeration i listrutan. | Den prenumeration som webbappen ska skapas under. | 
    | **Resursgrupp** | Välj en resursgrupp i listrutan eller välj **Skapa ny och** ange ett nytt resursgruppnamn. | Genom att placera alla dina appresurser i en resursgrupp kan du enkelt hantera eller ta bort dem tillsammans. | 
    | **Webbplatsnamn** | Ange ett namn för webbappen. | Det här värdet får inte vara tomt. | 
    | **Namn på värdplan** | Ange ett namn för App Service plan som ska användas som värd för webbappen. | Det här värdet får inte vara tomt. | 

1. Välj Aviseringar (klockikon) i portalen och välj sedan **Gå till resursgrupp.** 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure Portal distributionsmeddelande.":::

4. På sidan **Resursgrupp** i listan över resurser väljer du den webbapp som du skapade. Du ser även App Service plan cacheinstansen i den här listan. 

5. På **App Service** för webbappen väljer du URL:en för att navigera till webbplatsen. URL:en ska ha det här formatet: `https://<your-site-name>.azurewebsites.net` .

6. Bekräfta att du ser webbplatsen men att inga händelser har publicerats till den ännu.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Tom Event Grid Viewer-webbplats.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Prenumerera på Azure Cache for Redis instansen

I det här steget prenumererar du på ett ämne för att Event Grid vilka händelser du vill spåra och vart du vill skicka händelserna.

1. I portalen navigerar du till den cacheinstans som du skapade tidigare. 
2. På **Azure Cache for Redis** väljer du **Händelser** på den vänstra menyn. 
3. Välj **Web Hook**. Du skickar händelser till din tittarapp med hjälp av en web hook för slutpunkten. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure Portal sidan Händelser.":::

4. På sidan **Skapa händelseprenumeration** anger du följande: 

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Ange ett namn för händelseprenumerationen. | Värdet måste vara mellan 3 och 64 tecken långt. Den får bara innehålla bokstäver, siffror och bindestreck. | 
    | **Händelsetyper** | I listrutan väljer du vilken eller vilka händelsetyper du vill ska skickas till målet. I den här snabbstarten skalar vi cacheinstansen. | Korrigering, skalning, import och export är tillgängliga alternativ. | 
    | **Typ av slutpunkt** | Välj **Web Hook**. | Händelsehanterare för att ta emot dina händelser. | 
    | **Slutpunkt** | Klicka **på Välj en slutpunkt,** ange url:en för webbappen och lägg till den i url:en för startsidan `api/updates` (till exempel: ) och välj sedan Bekräfta `https://cache.azurewebsites.net/api/updates` **markering.** | Det här är URL:en för din webbapp som du skapade tidigare. | 

5. På sidan Skapa **händelseprenumeration** väljer du Skapa **för** att skapa händelseprenumerationen. 

6. Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="send-an-event-to-your-endpoint"></a>Skicka en händelse till din slutpunkt

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Vi skalar din Azure Cache for Redis instans.

1. I Azure Portal navigerar du till din Azure Cache for Redis-instans och väljer **Skala** på den vänstra menyn.

1. Välj önskad prisnivå på sidan Skala **och** klicka på **Välj.** 

    Du kan skala till en annan prisnivå med följande begränsningar:
    
    * Du kan inte skala från en högre prisnivå till en lägre prisnivå.
      * Du kan inte skala från en **Premium-cache** till en **Standard- eller** **Basic-cache.**
      * Du kan inte skala från en **Standard-cache** till en **Basic-cache.**
    * Du kan skala från **en Basic-cache** till **en standardcache,** men du kan inte ändra storlek på samma gång. Om du behöver en annan storlek kan du göra en efterföljande skalningsåtgärd till önskad storlek.
    * Du kan inte skala från en **Basic-cache** direkt till en **Premium-cache.** Börja med att skala **från Basic** **till Standard** i en skalningsåtgärd och sedan från **Standard** **till Premium** i en efterföljande skalningsåtgärd.
    * Du kan inte skala från en större storlek ned till **storleken C0 (250 MB).**
 
    När cacheminnet skalar till den nya prisnivån visas **skalningsstatusen** på **bladet Azure Cache for Redis.** När skalningen är klar ändras statusen från **Skalning till** **Körs.**

1. Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Meddelandet är i JSON-format och innehåller en matris med en eller flera händelser. I följande exempel innehåller JSON-meddelandet en matris med en händelse. Visa webbappen och observera att en **ScalingCompleted-händelse** togs emot. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Viewer-skalning i JSON-format.":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa resurserna som skapades i den här snabbstarten. Annars tar du bort de resurser som du skapade i den här snabbstarten.

Välj resursgruppen och sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar anpassade ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Reagera på Azure Cache for Redis händelser](cache-event-grid.md)
- [Om Event Grid](../event-grid/overview.md)
