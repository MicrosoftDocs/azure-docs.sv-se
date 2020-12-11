---
title: Skicka eller ta emot händelser från Azure Event Hubs med hjälp av Java Script (senaste)
description: Den här artikeln innehåller en genom gång av hur du skapar ett JavaScript-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det senaste Azure/Event-Hub-paketet.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 2e5f297d5edb9a271843db060f948209dd076074
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106095"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs"></a>Skicka händelser till eller ta emot händelser från Event Hub med hjälp av Java Script (Azure/Event-Hub)
Den här snabb starten visar hur du skickar händelser till och tar emot händelser från en händelsehubben med hjälp av JavaScript **-paketet Azure/Event-Hub** . 


## <a name="prerequisites"></a>Förutsättningar
Om du är nybörjare på Azure Event Hubs, se [Event Hubs översikt](event-hubs-about.md) innan du gör den här snabb starten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- Node.js version 8. x eller senare. Hämta den senaste [versionen av LTS (Long-term support)](https://nodejs.org).  
- Visual Studio Code (rekommenderas) eller andra Integrated Development Environment (IDE).  
- Ett aktivt Event Hubs namn område och händelsehubben. Gör så här för att skapa dem: 

   1. Skapa ett namn område av typen *Event Hubs* i [Azure Portal](https://portal.azure.com)och hämta sedan de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. 
   1. Om du vill skapa namn området och händelsehubben följer du anvisningarna i [snabb start: skapa en Event Hub med hjälp av Azure Portal](event-hubs-create.md).
   1. Fortsätt genom att följa anvisningarna i den här snabb starten. 
   1. Följ anvisningarna i [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)för att hämta anslutnings strängen för Event Hub-namnområdet. Registrera anslutnings strängen som ska användas senare i den här snabb starten.
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutnings strängen för Event Hubs namn området genom att** följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutnings strängen senare i den här snabb starten.

### <a name="install-the-npm-package"></a>Installera NPM-paketet
Om du vill installera [NPM-paketet (Node Package Manager) för Event Hubs](https://www.npmjs.com/package/@azure/event-hubs)öppnar du en kommando tolk som har *NPM* i sökvägen, ändrar katalogen till den mapp där du vill behålla dina exempel och kör sedan kommandot:

```shell
npm install @azure/event-hubs
```

Du måste installera två fler paket för den mottagande sidan. I den här snabb starten använder du Azure Blob Storage för att bevara kontroll punkter så att programmet inte läser de händelser som redan har lästs. Den utför kontroll punkter för metadata på mottagna meddelanden med jämna mellanrum i en blob. Den här metoden gör det enkelt att fortsätta att ta emot meddelanden senare, där du slutade.

Kör följande kommandon:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Skicka händelser

I det här avsnittet skapar du ett JavaScript-program som skickar händelser till en Event Hub.

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com).
1. Skapa en fil med namnet *send.js* och klistra in följande kod i den:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. I koden använder du verkliga värden för att ersätta följande:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Kör `node send.js` för att köra den här filen. Det här kommandot skickar en batch med tre händelser till händelsehubben.
1. I Azure Portal kontrollerar du att händelsehubben har tagit emot meddelandena. I avsnittet **mått** växlar du till vyn **meddelanden** . Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan det visar att meddelandena har mottagits.

    [![Verifiera att händelsehubben tog emot meddelanden](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > För den fullständiga käll koden, inklusive ytterligare informations kommentarer, går du till [sidan GitHub sendEvents.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Grattis! Du har nu skickat händelser till en händelsehubben.


## <a name="receive-events"></a>Ta emot händelser
I det här avsnittet får du händelser från en händelsehubben genom att använda en Azure Blob Storage-kontrollpunkt i ett JavaScript-program. Den utför kontroll punkter för metadata på mottagna meddelanden med jämna mellanrum i en Azure Storage-blob. Den här metoden gör det enkelt att fortsätta att ta emot meddelanden senare, där du slutade.

> [!WARNING]
> Om du kör den här koden på Azure Stack hubb får du körnings fel om du inte riktar in dig på en viss lagrings-API-version. Det beror på att Event Hubs SDK använder det senaste tillgängliga Azure Storage API som är tillgängligt i Azure och som kanske inte är tillgängligt på din Azure Stack Hub-plattform. Azure Stack Hub kan stödja en annan version av Storage BLOB SDK än vad som normalt är tillgängligt på Azure. Om du använder Azure blogg Storage som kontroll punkts Arkiv, kontrollerar du [Azure Storage API-versionen som stöds för din Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) -version och aktiverar den versionen i din kod. 
>
> Om du till exempel kör på Azure Stack Hub version 2005 är den högsta tillgängliga versionen för lagrings tjänsten version 2019-02-02. Som standard använder klient biblioteket för Event Hubs SDK den senaste tillgängliga versionen på Azure (2019-07-07 vid tidpunkten för lanseringen av SDK: n). I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod som mål för Storage Service API-versionen 2019-02-02. Ett exempel på hur du kan rikta en speciell lagrings-API-version finns i [Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) -och  [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) -exempel på GitHub. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Skapa ett Azure Storage-konto och en BLOB-behållare
Om du vill skapa ett Azure Storage-konto och en BLOB-behållare i det gör du följande:

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Skapa en blob-container i lagringskontot](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md)

Se till att du registrerar anslutnings strängen och behållar namnet för senare användning i mottagar koden.

### <a name="write-code-to-receive-events"></a>Skriva kod för att ta emot händelser

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com).
1. Skapa en fil med namnet *receive.js* och klistra in följande kod i den:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. I koden använder du verkliga värden för att ersätta följande värden:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Kör `node receive.js` i en kommando tolk för att köra den här filen. Fönstret ska visa meddelanden om mottagna händelser.

    > [!NOTE]
    > För den fullständiga käll koden, inklusive ytterligare informations kommentarer, går du till [sidan GitHub receiveEventsUsingCheckpointStore.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js).

Grattis! Du har nu tagit emot händelser från händelsehubben. Mottagar programmet tar emot händelser från alla partitioner i standard gruppen för användare i händelsehubben.

## <a name="next-steps"></a>Nästa steg
Kolla in de här exemplen på GitHub:

- [JavaScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
