---
title: 'Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkten med Azure Portal'
description: Använd Azure Event Grid för att prenumerera på Azure cache för Redis-händelser, skicka händelser till en webhook och hantera händelserna i ett webb program
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
author: curib
ms.author: cauribeg
ms.openlocfilehash: 5bdd6b0e6f97f7e5a738ab17d68282cf402004b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055593"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkten med Azure Portal

Azure Event Grid är en händelsetjänst för molnet. I den här snabb starten använder du Azure Portal för att skapa en Azure-cache för Redis-instansen, prenumerera på händelser för den instansen, utlösa en händelse och visa resultatet. Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. För att förenkla den här snabb starten kommer du dock att skicka händelser till en webbapp som samlar in och visar meddelandena. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

När du är klar ser du att händelse data har skickats till webbappen.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid granskarens skalning i JSON-format.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Skapa en Azure-cache för Redis-cache-instans 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på händelserna för cache-instansen ska vi skapa slut punkten för händelse meddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabb starten distribuerar du en [fördefinierad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar händelse meddelandena. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

1. Välj **distribuera till Azure** i GitHub README för att distribuera lösningen till din prenumeration. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Knappen distribuera till Azure.":::

2. Utför följande steg på sidan **Anpassad distribution** : 
    1. För **resurs grupp** väljer du den resurs grupp som du skapade när du skapade cache-instansen. Det blir enklare för dig att rensa efter att du är klar med självstudien genom att ta bort resurs gruppen.  
    2. För **webbplats namn** anger du ett namn för webbappen.
    3. Ange ett namn för den App Service plan som ska användas som värd för webbappen för **värd Plans namn**.
    4. Markera kryss rutan för **Jag accepterar villkoren som anges ovan**. 
    5. Välj **Köp**. 
    
    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här webbappen i. | 
    | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
    | **Webbplatsnamn** | Ange ett namn för din webbapp. | Värdet får inte vara tomt. | 
    | **Namn på värd plan** | Ange ett namn för den App Service plan som ska användas som värd för webbappen. | Värdet får inte vara tomt. | 

1. Välj aviseringar (klock ikon) i portalen och välj sedan **gå till resurs grupp**. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Avisering om Azure Portal distribution.":::

4. På sidan **resurs grupp** i listan över resurser väljer du den webbapp som du skapade. Du ser också App Service plan och cache-instansen i den här listan. 

5. På sidan **App Service** för din webbapp väljer du webb adressen för att navigera till webbplatsen. URL: en ska ha följande format: `https://<your-site-name>.azurewebsites.net` .

6. Bekräfta att du ser platsen men inga händelser har publicerats till den ännu.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Töm Event Grid Viewer-webbplatsen.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Prenumerera på Azure-cachen för Redis-instansen

I det här steget ska du prenumerera på ett ämne för att berätta Event Grid vilka händelser du vill spåra och var de ska skickas.

1. I portalen navigerar du till din cache-instans som du skapade tidigare. 
2. På sidan **Azure cache för Redis** väljer du **händelser** på den vänstra menyn. 
3. Välj **Web Hook**. Du skickar händelser till visnings programmet med en webhook för slut punkten. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Sidan Azure Portal händelser.":::

4. På sidan **Skapa händelse prenumeration** anger du följande: 

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Ange ett namn för händelse prenumerationen. | Värdet måste vara mellan 3 och 64 tecken långt. Det får bara innehålla bokstäver, siffror och bindestreck. | 
    | **Händelse typer** | List rutan och välj vilken händelse typ (er) som du vill ska flyttas till ditt mål. I den här snabb starten kommer vi att skala vår cache-instans. | Korrigering, skalning, import och export är tillgängliga alternativ. | 
    | **Typ av slutpunkt** | Välj **Web Hook**. | Händelse hanterare för att ta emot händelser. | 
    | **Slutpunkt** | Klicka på **Välj en slut punkt** och ange URL: en för din webbapp och Lägg till `api/updates` på Start sidans URL (t. ex.: `https://cache.azurewebsites.net/api/updates` ) och välj sedan **Bekräfta markering**. | Det här är webb adressen för din webbapp som du skapade tidigare. | 

5. Gå nu till sidan **Skapa händelse prenumeration** och välj **skapa** för att skapa händelse prenumerationen. 

6. Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="send-an-event-to-your-endpoint"></a>Skicka en händelse till din slutpunkt

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Vi kommer att skala Azure-cachen för Redis-instansen.

1. I Azure Portal går du till Azure-cachen för Redis-instansen och väljer **skala** på den vänstra menyn.

1. Välj önskad pris nivå på sidan **skala** och klicka på **Välj**. 

    Du kan skala till en annan pris nivå med följande begränsningar:
    
    * Du kan inte skala från en högre pris nivå till en lägre pris nivå.
      * Du kan inte skala från en **Premium** -cache till en **standard** -eller **Basic** -cache.
      * Du kan inte skala från **en** standardcache till en **grundläggande** cache.
    * Du kan skala från en **grundläggande** **cache till en** standardcache, men du kan inte ändra storlek på samma tid. Om du behöver en annan storlek kan du utföra en efterföljande skalnings åtgärd till önskad storlek.
    * Du kan inte skala från en **grundläggande** cache direkt till en **Premium** -cache. Börja med att skala från **Basic** till **standard** i en skalnings åtgärd och sedan från **standard** till **Premium** i en efterföljande skalnings åtgärd.
    * Du kan inte skala från en större storlek till storleken på **C0 (250 MB)** .
 
    Medan cachen skalas till den nya pris nivån visas en **skalnings** status i bladet **Azure cache för Redis** . När skalningen är klar ändras statusen från **skalning** till **körs**.

1. Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Meddelandet är i JSON-format och innehåller en matris med en eller flera händelser. I följande exempel innehåller JSON-meddelandet en matris med en händelse. Visa din webbapp och se att en **ScalingCompleted** -händelse togs emot. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid granskarens skalning i JSON-format.":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa upp resurserna som du skapade i den här snabb starten. Annars tar du bort de resurser som du skapade i den här snabb starten.

Välj resursgruppen och sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar anpassade ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Reagerar på Azure cache för Redis-händelser](cache-event-grid.md)
- [Om Event Grid](../event-grid/overview.md)

