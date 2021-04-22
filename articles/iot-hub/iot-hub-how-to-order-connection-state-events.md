---
title: Beställa enhetsanslutningshändelser Azure IoT Hub w/Azure Cosmos DB
description: Den här artikeln beskriver hur du beställer och registrerar enhetsanslutningshändelser från Azure IoT Hub med Azure Cosmos DB för att upprätthålla det senaste anslutningstillståndet
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: bcbfc0941e3c97e96ebc3746b946553e67a10f93
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878550"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Begär enhetsanslutningshändelser från Azure IoT Hub med hjälp av Azure Cosmos DB

Azure Event Grid hjälper dig att skapa händelsebaserade program och enkelt integrera IoT-händelser i dina affärslösningar. Den här artikeln går igenom en konfiguration som kan användas för att spåra och lagra det senaste enhetsanslutningstillståndet i Cosmos DB. Vi använder det sekvensnummer som är tillgängligt i händelserna Ansluten enhet och Frånkopplad enhet och lagrar det senaste tillståndet i Cosmos DB. Vi ska använda en lagrad procedur, som är en programlogik som körs mot en samling i Cosmos DB.

Sekvensnumret är en strängrepresentation av ett hexadecimalt tal. Du kan använda strängjämföring för att identifiera det större talet. Om du konverterar strängen till hex är talet ett 256-bitars tal. Sekvensnumret ökar strikt och den senaste händelsen har ett högre tal än andra händelser. Detta är användbart om du har frekventa enhetskopplingar och frånkopplingar och vill se till att endast den senaste händelsen används för att utlösa en underordnad åtgärd, eftersom Azure Event Grid inte stöder ordningsföljd av händelser.

## <a name="prerequisites"></a>Förutsättningar

* Ett aktivt Azure-konto. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto.](https://azure.microsoft.com/pricing/free-trial/)

* Ett aktivt Azure Cosmos DB SQL API-konto. Om du inte har skapat ett konto än kan du [gå igenom skapa](../cosmos-db/create-sql-api-java.md#create-a-database-account) ett databaskonto.

* En samling i databasen. En [genomgång finns i Lägga](../cosmos-db/create-sql-api-java.md#add-a-container) till en samling. När du skapar din samling använder du `/id` för partitionsnyckeln.

* En IoT-hubb i Azure. Om du inte redan har skapat en hubb läser du genomgången i [Kom igång med IoT Hub](./quickstart-send-telemetry-dotnet.md).

## <a name="create-a-stored-procedure"></a>Skapa en lagrad procedur

Skapa först en lagrad procedur och konfigurera den så att den kör en logik som jämför sekvensnummer för inkommande händelser och registrerar den senaste händelsen per enhet i databasen.

1. I sql Cosmos DB-API:et **väljer du Datautforskaren** Objekt  >  **ny**  >  **lagrad procedur**.

   ![Skapa lagrad procedur](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Ange **LatestDeviceConnectionState** som ID för lagrad procedur och klistra in följande i **brödtexten för lagrad procedur.** Observera att den här koden ska ersätta all befintlig kod i brödtexten för den lagrade proceduren. Den här koden upprätthåller en rad per enhets-ID och registrerar det senaste anslutningstillståndet för detta enhets-ID genom att identifiera det högsta sekvensnumret.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Spara den lagrade proceduren:

    ![spara lagrad procedur](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Skapa en logikapp

Börja med att skapa en logikapp och lägg till en utlösare för händelserutnät som övervakar resursgruppen för den virtuella datorn.

### <a name="create-a-logic-app-resource"></a>Skapa en resurs för en logikapp

1. I [Azure Portal](https://portal.azure.com)väljer du **+Skapa en resurs,** integration **och** sedan **logikapp.**

   ![Skapa en logikapp](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Ge logikappen ett namn som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb.

   ![Ny logikapp](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Välj **Skapa** för att skapa logikappen.

   Du har nu skapat en Azure-resurs för din logikapp. När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE]
   > Om du vill hitta och öppna logikappen igen väljer du **Resursgrupper** och väljer den resursgrupp som du använder för den här i vilken du vill göra det. Välj sedan din nya logikapp. Logikappdesignern öppnas.

4. I Logikappdesignern rullar du åt höger tills du ser vanliga utlösare. Under **Mallar** väljer du **Tom logikapp** så att du kan skapa logikappen från grunden.

### <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en specifik händelse som startar din logikapp. I den här självstudien tar utlösaren som utlöser arbetsflödet emot en begäran via HTTP.

1. I sökfältet för anslutningsappar och utlösare skriver du **HTTP** och trycker på Retur.

2. Välj **Begäran – När en HTTP-begäran tas emot** som utlösare.

   ![Välj utlösare för HTTP-begäranden](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Välj **Generera schemat genom att använda en exempelnyttolast**.

   ![Använda exempelnyttolast för att generera ett schema](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Klistra in följande JSON-exempelkod i textrutan och välj sedan **Klar**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Klistra in JSON-exempelnyttolast](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Ett popup-meddelande med texten **Remember to include a Content-Type header set to application/json in your request** (Glöm inte att ta med ett Content-Type-huvud konfigurerat till application/json i din begäran). Du kan ignorera det här förslaget och gå vidare till nästa avsnitt.

### <a name="create-a-condition"></a>Skapa ett villkor

I logikappens arbetsflöde hjälper villkor till att köra specifika åtgärder när du har godkänt det specifika villkoret. När villkoret är uppfyllt kan en önskad åtgärd definieras. I den här självstudien är villkoret att kontrollera om eventType är ansluten till enheten eller om enheten är frånkopplad. Åtgärden är att köra den lagrade proceduren i databasen.

1. Välj **+ Nytt steg** och sedan **Inbyggt.** Leta sedan reda på och välj **Villkor.** Klicka på **Välj ett** värde så visas en ruta med dynamiskt innehåll – de fält som kan väljas. Fyll i fälten som visas nedan för att endast köra detta för enhetsanslutna och frånkopplade händelser:

   * Välj ett värde: **eventType** – välj det här från fälten i det dynamiska innehåll som visas när du klickar på det här fältet.
   * Ändra "är lika med" till **slutar med**.
   * Välj ett värde: **nected**.

     ![Fyllningsvillkor](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. I dialogrutan **if true (om** sant) klickar du på **Add an action (Lägg till en åtgärd).**
  
   ![Lägg till åtgärd om sant](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Sök efter Cosmos DB och välj **Azure Cosmos DB – Kör lagrad procedur**

   ![Sök efter CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Fyll i **cosmosdb-connection** **som Anslutningsnamn,** välj posten i tabellen och välj sedan **Skapa.** Panelen Kör **lagrad** procedur visas. Ange värdena för fälten:

   **Databas-ID:** ToDoList

   **Samlings-ID:** Objekt

   **Sproc ID:** LatestDeviceConnectionState

5. Välj **Lägg till ny parameter.** I listrutan som visas markerar du kryssrutorna bredvid **Partitionsnyckel** och Parametrar för den lagrade proceduren **och** klickar sedan någon annanstans på skärmen. Det lägger till ett fält för partitionsnyckelvärde och ett fält för parametrar för den lagrade proceduren.

   ![Skärmbild som visar objektet Kör lagrad procedur med Lägg till ny parameter valt.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Ange nu partitionsnyckelvärdet och parametrarna enligt nedan. Se till att sätta inom hakparenteser och dubbla citattecken enligt bilden. Du kan behöva klicka på **Lägg till dynamiskt innehåll** för att få de giltiga värden som du kan använda här.

   ![Skärmbild som visar objektet Kör lagrad procedur med angivna parametrar.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Kontrollera att Brödtext är markerat under Välj utdata från föregående steg längst upp i fönstret där det står **For** **Each**(För varje).

   ![fylla i logikappen för varje](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Spara logikappen.

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL:en

Innan du lämnar Logic Apps Designer kopierar du url:en som logikappen lyssnar efter en utlösare. Du använder den här URL:en för att konfigurera Event Grid.

1. Expandera konfigurationsrutan för utlösaren **När en HTTP-begäran tas emot** genom att klicka på den.

2. Kopiera värdet för **HTTP POST-URL** genom att välja kopieringsknappen bredvid det.

   ![Kopiera HTTP POST-URL:en](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Spara den här URL:en så att du kan referera till den i nästa avsnitt.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurera prenumerationen för IoT Hub-händelser

I det här avsnittet ska du konfigurera din IoT-hubb så att den publicerar händelser när de inträffar.

1. Gå till din IoT-hubb på Azure Portal.

2. Välj **Händelser**.

   ![Öppna Event Grid-informationen](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Välj **+ Händelseprenumeration.**

   ![Skapa ny händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Fyll i **Information om händelseprenumeration:** Ange ett beskrivande namn och **välj Event Grid Schema**.

5. Fyll i fälten **Händelsetyper.** I listrutan väljer du endast Enheten **är ansluten och** Enheten är **frånkopplad** på menyn. Klicka någon annanstans på skärmen för att stänga listan och spara dina val.

   ![Ange händelsetyper som ska sökas efter](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. För **Slutpunktsinformation** väljer du Slutpunktstyp som **Web Hook** och klickar på välj slutpunkt och klistrar in den URL som du kopierade från logikappen och bekräftar valet.

   ![Välj slutpunkts-URL](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Formuläret bör nu se ut ungefär som i följande exempel:

   ![Exempelformulär för händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Välj **Skapa** för att spara händelseprenumerationen.

## <a name="observe-events"></a>Observera händelser

Nu när din händelseprenumeration har ställts in ska vi testa genom att ansluta en enhet.

### <a name="register-a-device-in-iot-hub"></a>Registrera en enhet i IoT Hub

1. Från din IoT-hubb väljer du **IoT-enheter**.

2. Välj **+Lägg** till överst i fönstret.

3. Ange `Demo-Device-1` för **Enhets-ID**.

4. Välj **Spara**.

5. Du kan lägga till flera enheter med olika enhets-ID:er.

   ![Enheter som har lagts till i hubben](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klicka på enheten igen; nu fylls anslutningssträngarna och nycklarna i. Kopiera **Anslutningssträng – primärnyckel för** senare användning.

   ![ConnectionString för enheten](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Starta Raspberry Pi-simulatorn

Nu ska vi använda Raspberry Pi-webbsimulatorn för att simulera enhetsanslutningen.

[Starta Raspberry Pi-simulatorn](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Köra ett exempelprogram på Raspberry Pi-webbsimulatorn

Detta utlöser en enhetsansluten händelse.

1. I kodningsområdet ersätter du platshållaren på rad 15 med Azure IoT Hub enhetsanslutningssträng som du sparade i slutet av föregående avsnitt.

   ![Klistra in enhetens anslutningssträng](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Kör programmet genom att välja **Kör**.

Du ser något som liknar följande utdata som visar sensordata och de meddelanden som skickas till din IoT-hubb.

   ![Köra programmet](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klicka **på Stoppa** för att stoppa simulatorn och utlösa en **frånkopplad enhet-händelse.**

Nu har du kört ett exempelprogram för att samla in sensordata och skicka dem till din IoT-hubb.

### <a name="observe-events-in-cosmos-db"></a>Observera händelser i Cosmos DB

Du kan se resultatet av den körda lagrade proceduren i ditt Cosmos DB dokument. Så här ser det ut. Varje rad innehåller det senaste enhetsanslutningstillståndet per enhet.

   ![Så här gör du för att få resultat](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

I stället för [att Azure Portal](https://portal.azure.com)kan du utföra stegen IoT Hub med hjälp av Azure CLI. Mer information finns på Azure CLI-sidorna för att [skapa en händelseprenumeration](/cli/azure/eventgrid/event-subscription) [och skapa en IoT-enhet.](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create)

## <a name="clean-up-resources"></a>Rensa resurser

I den här självstudiekursen användes resurser som medför kostnader för din Azure-prenumeration. När du är klar med självstudien och testar dina resultat kan du inaktivera eller ta bort resurser som du inte vill behålla.

Om du inte vill förlora det arbete du gjort i logikappen inaktiverar du den i stället för att ta bort den.

1. Gå till logikappen.

2. På **bladet Översikt** väljer du **Ta bort** eller **Inaktivera**.

    Varje prenumeration kan ha en kostnadsfri IoT-hubb. Om du har skapat en kostnadsfri hubb för den här självstudiekursen behöver du inte ta bort den för att undvika kostnader.

3. Gå till IoT-hubben.

4. På **bladet Översikt** väljer du Ta **bort.**

    Även om du behåller din IoT-hubb kanske du vill ta bort händelseprenumerationen som du skapade.

5. Välj **Event Grid** i IoT-hubben.

6. Välj den händelseprenumeration som du vill ta bort.

7. Välj **Ta bort**.

Om du vill Azure Cosmos DB ett konto från Azure Portal högerklickar du på kontonamnet och klickar på Ta **bort konto.** Se detaljerade anvisningar för att [ta bort ett Azure Cosmos DB-konto](../cosmos-db/how-to-manage-database-account.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att reagera på IoT Hub händelser genom att använda Event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md)

* [Prova självstudien om IoT Hub händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Läs mer om vad du kan göra med [Event Grid](../event-grid/overview.md)