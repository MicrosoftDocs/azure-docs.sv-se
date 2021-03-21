---
title: Självstudie – Använd IoT Hub händelser för att utlösa Azure Logic Apps
description: I den här självstudien visas hur du använder tjänsten för händelse dirigering i Azure Event Grid skapa automatiserade processer för att utföra Azure Logic Apps åtgärder baserat på IoT Hub händelser.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3772988e9bf4f733323adae6c0527c8b811c4d04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624463"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Självstudie: Skicka e-postmeddelanden om Azure IoT Hub-händelser med Event Grid och Logic Apps

Med Azure Event Grid kan du reagera på händelser i IoT Hub genom att utlösa åtgärder i underordnade företagsprogram.

Den här artikeln beskriver en exempel konfiguration som använder IoT Hub och Event Grid. I slutet har du konfigurerat en Azure Logic app som skickar ett e-postmeddelande varje gång en enhet ansluts eller kopplas till din IoT-hubb. Event Grid kan användas för att få aviseringar i tid om att kritiska enheter kopplas från. Mått och diagnostik kan ta flera (t. ex. 20 eller mer – men vi vill inte placera ett tal på det) minuter att visa i loggar/varningar. Detta kan vara oacceptabelt för kritisk infrastruktur.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Ett e-postkonto från valfri e-postleverantör som stöds av Azure Logic Apps, till exempel Office 365 Outlook eller Outlook.com. Det här e-postkontot används för att skicka händelsemeddelandena.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Du kan snabbt skapa en ny IoT-hubb med hjälp av Azure Cloud Shell terminalen i portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com). 

1. I det övre högra hörnet på sidan väljer du knappen Cloud Shell.

   ![Cloud Shell-knapp](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Kör följande kommando för att skapa en ny resursgrupp:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. Kör följande kommando för att skapa en IoT-hubb:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Minimera Cloud Shell terminalen. Du kommer tillbaka till gränssnittet senare i självstudien.

## <a name="create-a-logic-app"></a>Skapa en logikapp

Skapa sedan en Logic app och Lägg till en HTTP Event Grid-utlösare som bearbetar begär Anden från IoT Hub. 

### <a name="create-a-logic-app-resource"></a>Skapa en resurs för en logikapp

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs** och skriver sedan "Logic app" i sökrutan och väljer RETUR. Välj **Logic app** från resultaten.

   ![Skapa en logikapp](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. På nästa skärm väljer du **skapa**. 

1. Ge logikappen ett namn som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb. 

   ![Fält för att skapa Logic app](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Välj **Granska + skapa**.

1. Verifiera inställningarna och välj sedan **skapa**.

1. När resursen har skapats väljer **du gå till resurs**. 

1. I Logic Apps designer går du nedåt för att se **mallarna**. Välj **Tom Logic app** så att du kan bygga din Logic app från grunden.

### <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en specifik händelse som startar din logikapp. I den här självstudien tar utlösaren som utlöser arbetsflödet emot en begäran via HTTP.  

1. Skriv **HTTP** i sökfältet för anslutningsprogram och utlösare.

1. Bläddra igenom resultaten och välj **begäran – när en HTTP-begäran tas emot** som utlösare. 

   ![Välj utlösare för HTTP-begäranden](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Välj **Generera schemat genom att använda en exempelnyttolast**. 

   ![Använd exempel nytto Last](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Klistra in den *anslutna händelse schema* -JSON i text rutan och välj sedan **färdig**:

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   Den här händelsen publicerar när en enhet är ansluten till en IoT-hubb.

> [!NOTE]
> Ett popup-meddelande med texten **Remember to include a Content-Type header set to application/json in your request** (Glöm inte att ta med ett Content-Type-huvud konfigurerat till application/json i din begäran). Du kan ignorera det här förslaget och gå vidare till nästa avsnitt. 

### <a name="create-an-action"></a>Skapa en åtgärd

Åtgärder är alla steg som utförs när utlösaren har startat logikappens arbetsflöde. I den här självstudiekursen är åtgärden att skicka ett e-postmeddelande från din e-postleverantör. 

1. Välj **Nytt steg**. Då öppnas ett fönster där du kan **välja en åtgärd**.

1. Sök efter **Outlook**.

1. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. I den här självstudien används **Outlook.com**. Stegen för andra e-postleverantörer liknar dessa. 

   ![Välj anslutningsprogram för e-postleverantör](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. Välj åtgärden **Skicka ett e-postmeddelande (v2)** . 

1. Välj **Logga** in och logga in på ditt e-postkonto. Välj **Ja** om du vill att appen ska kunna komma åt din information.

1. Skapa din e-postmall. 

   * **Till**: Ange e-postadressen som e-postaviseringarna ska skickas till. I den här självstudien använder du ett e-postkonto som du kan komma åt för testning. 

   * **Ämne**: Fyll i ämnestexten. När du klickar på text rutan ämne kan du välja dynamiskt innehåll som ska inkluderas. I den här självstudien används till exempel `IoT Hub alert: {eventType}` . Om du inte kan se dynamiskt innehåll, väljer du hyperlänken **Lägg till dynamiskt innehåll** – detta växlar till och från.

   * **Brödtext**: Skriv texten för e-postmeddelandet. Välj JSON-egenskaper från valverktyget för att ta med dynamiskt innehåll baserat på händelsedata. Om du inte kan se det dynamiska innehållet väljer du hyperlänken **Lägg till dynamiskt innehåll** under text rutan **brödtext** . Om du inte ser de fält som du vill ha klickar du på *mer* på skärmen med dynamiskt innehåll för att ta med fälten från föregående åtgärd.

   Din e-postmall kanske liknar den i det här exemplet:

   ![Fyll i e-postinformation](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Välj **Spara** i Logikappdesignern.  

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL:en

Innan du lämnar Logic Apps-designern kopierar du URL:en som används av dina logikappar för att lyssna efter en utlösare. Du använder den här URL:en för att konfigurera Event Grid. 

1. Expandera konfigurationsrutan för utlösaren **När en HTTP-begäran tas emot** genom att klicka på den. 

1. Kopiera värdet för **HTTP POST-URL** genom att välja kopieringsknappen bredvid det. 

   ![Kopiera HTTP POST-URL:en](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Spara den här URL:en så att du kan referera till den i nästa avsnitt. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurera prenumerationen för IoT Hub-händelser

I det här avsnittet ska du konfigurera din IoT-hubb så att den publicerar händelser när de inträffar. 

1. Gå till din IoT-hubb på Azure Portal. Du kan göra detta genom att välja **resurs grupper** och sedan välja resurs gruppen för den här själv studie kursen och sedan välja din IoT Hub i listan över resurser.

1. Välj **Händelser**.

   ![Öppna Event Grid-informationen](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. Välj **Händelseprenumeration**. 

   ![Skapa ny händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Skapa händelseprenumerationen med följande värden: 

   1. I avsnittet **Information om händelseprenumeration**:
      1. Ange ett **namn** för händelse prenumerationen. 
      2. Välj **Event Grid schema** för **händelse schema**. 
   2. I avsnittet **Ämnesinformation**:
      1. Bekräfta att **IoT Hub** har angetts som **Ämnestyp**. 
      2. Bekräfta att namnet på IoT-hubben har angetts som värdet för fältet **Källresurs**. 
      3. Ange ett namn på **system avsnittet** som ska skapas åt dig. Information om system ämnen finns i [Översikt över system ämnen](system-topics.md).
   3. I avsnittet **Händelsetyper**:
      1. Välj listrutan **Filtrera till händelsetyper**.
      1. Avmarkera kryss rutorna **enhet som skapats** och **enhet borttagna** , så att kryss rutorna enhet **ansluten** och **avkopplad enhet** är markerade.

         ![välja händelsetyper för prenumeration](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. I avsnittet **Information om slutpunkt**: 
       1. Välj **typ av slut punkt** som **Web Hook**.
       2. Klicka på **Välj en slutpunkt**, klistra in URL:en som du kopierade från din logikapp och bekräfta valet.

         ![webbadress till vald slutpunkt](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         När du är färdig ska fönstret se ut som i följande exempel: 

         ![Exempelformulär för händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  Välj **Skapa**.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Simulera en ny enhet som ansluter och skickar telemetri

Testa din Logic app genom att snabbt simulera en enhets anslutning med hjälp av Azure CLI. 

1. Välj knappen Cloud Shell för att öppna din terminal igen.

1. Kör följande kommando för att skapa en simulerad enhets identitet:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. Kör följande kommando för att simulera anslutning av enheten till IoT Hub och skicka telemetri:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. När den simulerade enheten ansluter till IoT Hub får du ett e-postmeddelande som meddelar dig om en "DeviceConnected"-händelse.

1. När simuleringen är klar får du ett e-postmeddelande som meddelar dig om en "DeviceDisconnected"-händelse. 

    ![Exempel på aviserings post](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Rensa resurser

I den här självstudiekursen användes resurser som medför kostnader för din Azure-prenumeration. När du är klar med att testa självstudien och testa dina resultat kan du inaktivera eller ta bort resurser som du inte vill behålla. 

Ta bort resurs gruppen om du vill ta bort alla resurser som skapats i den här självstudien. 

1. Välj **resurs grupper** och välj sedan den resurs grupp som du skapade för den här självstudien.

2. I fönstret resurs grupp väljer du **ta bort resurs grupp**. Du uppmanas att ange namnet på resurs gruppen och sedan kan du ta bort det. Alla resurser som ingår däri tas också bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [hur du kan svara på IoT Hub-händelser med hjälp av Event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md).
* [Lär dig att ordna enhetsanslutningshändelser och frånkopplingar](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Lär dig mer om vad du kan göra med [Event Grid](overview.md).

En fullständig lista över Logic app-kopplingar som stöds finns i 

> [!div class="nextstepaction"]
> [Översikt över anslutningar](/connectors/).