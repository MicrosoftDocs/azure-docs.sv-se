---
title: Beställ anslutnings händelser för enheter fr Azure IoT Hub med Azure Cosmos DB
description: Den här artikeln beskriver hur du beställer och registrerar enhets anslutnings händelser från Azure IoT Hub att använda Azure Cosmos DB för att underhålla det senaste anslutnings läget
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: 90b7b6aebfce1c37bef76d371d829048d755e39e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147268"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Begär enhetsanslutningshändelser från Azure IoT Hub med hjälp av Azure Cosmos DB

Azure Event Grid hjälper dig att bygga händelsebaserade program och enkelt integrera IoT-händelser i dina affärs lösningar. Den här artikeln vägleder dig genom en installation som du kan använda för att spåra och lagra den senaste statusen för enhets anslutning i Cosmos DB. Vi kommer att använda sekvensnumret som är tillgängligt i enheten ansluten och enhets frånkopplade händelser och lagra det senaste läget i Cosmos DB. Vi ska använda en lagrad procedur, som är en program logik som körs mot en samling i Cosmos DB.

Sekvensnumret är en sträng representation av ett hexadecimalt tal. Du kan använda sträng jämförelse för att identifiera det större talet. Om du konverterar strängen till hex blir talet ett 256-bitars tal. Sekvensnumret är helt ökande och den senaste händelsen har en högre siffra än andra händelser. Detta är användbart om du ofta har en enhet som är ansluten och frånkopplad och vill se till att endast den senaste händelsen används för att utlösa en underordnad åtgärd, eftersom Azure Event Grid inte stöder sortering av händelser.

## <a name="prerequisites"></a>Förutsättningar

* Ett aktivt Azure-konto. Om du inte har någon kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/).

* Ett aktivt Azure Cosmos DB SQL API-konto. Om du inte har skapat en ännu, se [skapa ett databas konto](../cosmos-db/create-sql-api-java.md#create-a-database-account) för en genom gång.

* En samling i databasen. Se [lägga till en samling](../cosmos-db/create-sql-api-java.md#add-a-container) för en genom gång. När du skapar samlingen använder du `/id` för partitionsnyckel.

* En IoT-hubb i Azure. Om du inte redan har skapat en hubb läser du genomgången i [Kom igång med IoT Hub](./quickstart-send-telemetry-dotnet.md).

## <a name="create-a-stored-procedure"></a>Skapa en lagrad procedur

Börja med att skapa en lagrad procedur och konfigurera den så att den kör en logik som jämför antalet inkommande händelser och registrerar den senaste händelsen per enhet i databasen.

1. I Cosmos DB SQL API väljer du **datautforskaren**  >  **objekt**  >  **ny lagrad procedur**.

   ![Skapa lagrad procedur](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Ange **LatestDeviceConnectionState** för det lagrade procedur-ID: t och klistra in följande i den **lagrade procedurens brödtext**. Observera att den här koden ska ersätta eventuell befintlig kod i den lagrade procedurens brödtext. Den här koden underhåller en rad per enhets-ID och registrerar det senaste anslutnings läget för enhets-ID: t genom att identifiera det högsta ordnings numret.

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

    ![Spara lagrad procedur](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Skapa en logikapp

Börja med att skapa en logikapp och lägg till en utlösare för händelserutnät som övervakar resursgruppen för den virtuella datorn.

### <a name="create-a-logic-app-resource"></a>Skapa en resurs för en logikapp

1. I [Azure Portal](https://portal.azure.com)väljer du **+ skapa en resurs**, väljer **integration** och sedan **Logic app**.

   ![Skapa en logikapp](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Ge logikappen ett namn som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb.

   ![Ny Logic-app](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Välj **skapa** för att skapa Logic-appen.

   Du har nu skapat en Azure-resurs för din logikapp. När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE]
   > Om du vill hitta och öppna din Logic app igen väljer du **resurs grupper** och väljer den resurs grupp som du använder för den här instruktionen. Välj sedan den nya Logic-appen. Då öppnas Logic Apps designer.

4. I Logic Apps designer rullar du åt höger tills du ser vanliga utlösare. Under **mallar** väljer du **Tom Logic app** så att du kan bygga din Logic app från grunden.

### <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en specifik händelse som startar din logikapp. I den här självstudien tar utlösaren som utlöser arbetsflödet emot en begäran via HTTP.

1. I Sök fältet kopplingar och utlösare skriver du **http** och trycker på RETUR.

2. Välj **Begäran – När en HTTP-begäran tas emot** som utlösare.

   ![Välj utlösare för HTTP-begäranden](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Välj **Generera schemat genom att använda en exempelnyttolast**.

   ![Använd exempel nytto last för att generera ett schema](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Klistra in exempel-JSON-nyttolast](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Ett popup-meddelande med texten **Remember to include a Content-Type header set to application/json in your request** (Glöm inte att ta med ett Content-Type-huvud konfigurerat till application/json i din begäran). Du kan ignorera det här förslaget och gå vidare till nästa avsnitt.

### <a name="create-a-condition"></a>Skapa ett villkor

I ditt Logic app-arbetsflöde kan villkor hjälpa dig att köra vissa åtgärder när du har överfört det aktuella villkoret. När villkoret är uppfyllt kan en önskad åtgärd definieras. I den här självstudien är villkoret att kontrol lera om eventType är enhet ansluten eller om enheten är frånkopplad. Åtgärden kommer att utföra den lagrade proceduren i databasen.

1. Välj **+ nytt steg** sedan **inbyggd** och sedan Sök och välj **villkor**. Klicka på **Välj ett värde** så visas en ruta med det dynamiska innehållet – fälten som kan väljas. Fyll i fälten så som visas nedan för att endast köra detta för enhet anslutna och frånkopplade enhets händelser:

   * Välj ett värde: **eventType** --Välj detta från fälten i det dynamiska innehåll som visas när du klickar på det här fältet.
   * Ändra "är lika med" som **slutar med**.
   * Välj ett värde: **nected**.

     ![Fyllnings villkor](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. I dialog rutan **om True** klickar du på **Lägg till en åtgärd**.
  
   ![Lägg till åtgärd om värdet är sant](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Sök efter Cosmos DB och välj **Azure Cosmos DB-kör lagrad procedur**

   ![Sök efter CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Fyll i **cosmosdb – anslutning** för **anslutnings namnet** och välj posten i tabellen och välj sedan **skapa**. Du ser panelen **Kör lagrad procedur** . Ange värdena för fälten:

   **Databas-ID**: ToDoList

   **Samlings-ID**: objekt

   **Sproc-ID**: LatestDeviceConnectionState

5. Välj **Lägg till ny parameter**. I list rutan som visas markerar du kryss rutorna bredvid **partitionsnyckel** och **Parametrar för den lagrade proceduren**. Klicka sedan på någon annan på skärmen. Det lägger till ett fält för partitionerings nyckel värde och ett fält för parametrar för den lagrade proceduren.

   ![Skärm bild som visar ett objekt för att köra lagrade procedurer med Lägg till ny parameter valt.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Ange värdet och parametrarna för partitionsnyckel som visas nedan. Se till att du anger hakparenteser och dubbla citat tecken enligt vad som visas. Du kanske måste klicka på **Lägg till dynamiskt innehåll** för att få giltiga värden som du kan använda här.

   ![Skärm bild som visar ett kör lagrat procedur objekt med angivna parametrar.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Överst i fönstret där **det står,** under **Välj utdata från föregående steg**, kontrollerar  **du att den är markerad** .

   ![Fyll i Logic app för – varje](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Spara logikappen.

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL:en

Innan du lämnar Logic Apps designer kopierar du den URL som din Logic app lyssnar på för en utlösare. Du använder den här URL:en för att konfigurera Event Grid.

1. Expandera konfigurationsrutan för utlösaren **När en HTTP-begäran tas emot** genom att klicka på den.

2. Kopiera värdet för **HTTP POST-URL** genom att välja kopieringsknappen bredvid det.

   ![Kopiera HTTP POST-URL:en](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Spara den här URL:en så att du kan referera till den i nästa avsnitt.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurera prenumerationen för IoT Hub-händelser

I det här avsnittet ska du konfigurera din IoT-hubb så att den publicerar händelser när de inträffar.

1. Gå till din IoT-hubb på Azure Portal.

2. Välj **Händelser**.

   ![Öppna Event Grid-informationen](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Välj **+ händelse prenumeration**.

   ![Skapa ny händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Fyll i **händelse prenumerations information**: Ange ett beskrivande namn och välj **Event Grid schema**.

5. Fyll i fälten **händelse typer** . I list rutan väljer du endast **enhet ansluten** och **enheten frånkopplad** från menyn. Klicka på någon annan stans på skärmen för att stänga listan och spara dina val.

   ![Ange händelse typer som ska sökas efter](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. För **slut punkts information** väljer du slut punkts typ som **Web Hook** och klickar på Välj slut punkt och klistrar in den URL som du kopierade från din Logic app och bekräftar valet.

   ![Välj slut punkts-URL](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Formuläret bör nu se ut ungefär som i följande exempel:

   ![Exempelformulär för händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Välj **Skapa** för att spara händelseprenumerationen.

## <a name="observe-events"></a>Observera händelser

Nu när din händelse prenumeration har kon figurer ATS kan vi testa genom att ansluta en enhet.

### <a name="register-a-device-in-iot-hub"></a>Registrera en enhet i IoT Hub

1. Från din IoT Hub väljer du **IoT-enheter**.

2. Välj **+ Lägg till** överst i fönstret.

3. Ange `Demo-Device-1` för **Enhets-ID**.

4. Välj **Spara**.

5. Du kan lägga till flera enheter med olika enhets-ID: n.

   ![Enheter som lagts till i hubben](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klicka på enheten igen. nu kommer anslutnings strängarna och nycklarna att fyllas i. Kopiera **anslutnings strängen – primär nyckel** för senare användning.

   ![ConnectionString för enhet](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Starta Raspberry Pi-Simulator

Vi använder Raspberry Pi Web Simulator för att simulera enhets anslutning.

[Starta Raspberry Pi-Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Köra ett exempel program i Raspberry Pi Web Simulator

Då utlöses en enhets ansluten händelse.

1. I kodnings området ersätter du plats hållaren på rad 15 med den Azure IoT Hub enhets anslutnings sträng som du sparade i slutet av föregående avsnitt.

   ![Klistra in i enhets anslutnings sträng](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Kör programmet genom att välja **Kör**.

Du ser något som liknar följande utdata som visar sensor data och meddelanden som skickas till din IoT-hubb.

   ![Köra programmet](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klicka på **stoppa** för att stoppa simulatorn och utlösa en enhet som är **frånkopplad** .

Du har nu kört ett exempel program för att samla in sensor data och skicka det till din IoT-hubb.

### <a name="observe-events-in-cosmos-db"></a>Observera händelser i Cosmos DB

Du kan se resultatet av den utförda lagrade proceduren i Cosmos DB dokumentet. Så här ser det ut. Varje rad innehåller det senaste status för enhets anslutning per enhet.

   ![Hur man utfaller](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

I stället för att använda [Azure Portal](https://portal.azure.com)kan du utföra IoT Hub stegen med Azure CLI. Mer information finns i Azure CLI-sidorna för att [skapa en händelse prenumeration](/cli/azure/eventgrid/event-subscription) och [skapa en IoT-enhet](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Rensa resurser

I den här självstudiekursen användes resurser som medför kostnader för din Azure-prenumeration. När du är klar med att testa självstudien och testa dina resultat kan du inaktivera eller ta bort resurser som du inte vill behålla.

Om du inte vill förlora det arbete du gjort i logikappen inaktiverar du den i stället för att ta bort den.

1. Gå till logikappen.

2. På bladet **Översikt** väljer du **ta bort** eller **inaktivera**.

    Varje prenumeration kan ha en kostnadsfri IoT-hubb. Om du har skapat en kostnadsfri hubb för den här självstudiekursen behöver du inte ta bort den för att undvika kostnader.

3. Gå till IoT-hubben.

4. Välj **ta bort** på bladet **Översikt** .

    Även om du behåller din IoT-hubb kanske du vill ta bort händelseprenumerationen som du skapade.

5. Välj **Event Grid** i IoT-hubben.

6. Välj den händelseprenumeration som du vill ta bort.

7. Välj **Ta bort**.

Om du vill ta bort ett Azure Cosmos DB konto från Azure Portal högerklickar du på konto namnet och klickar på **ta bort konto**. Se detaljerade instruktioner för att [ta bort ett Azure Cosmos DB-konto](../cosmos-db/how-to-manage-database-account.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur [du reagerar på IoT Hub händelser med event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md)

* [Prova själv studie kursen om IoT Hub händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Lär dig mer om vad du kan göra med [Event Grid](../event-grid/overview.md)