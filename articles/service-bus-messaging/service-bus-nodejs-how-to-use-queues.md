---
title: Så här använder du azure/service-bus-köer i JavaScript
description: Lär dig hur du skriver ett JavaScript-program som använder den senaste versionen av paketet för att skicka meddelanden till och ta emot @azure/service-bus meddelanden från en Service Bus kö.
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: aee67becf7519f03839eafbd897838f938871307
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537239"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Skicka meddelanden till och ta emot meddelanden Azure Service Bus köer (JavaScript)
I den här självstudien lär du dig att använda paketet i ett JavaScript-program för att skicka meddelanden till och ta emot [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) meddelanden från en Service Bus kö.

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Om du inte har någon kö att arbeta med följer du stegen i artikeln Använda Azure Portal för att skapa en [Service Bus för](service-bus-quickstart-portal.md) att skapa en kö. Anteckna **anslutningssträngen** för Service Bus namnområdet och namnet på den **kö som** du skapade.

> [!NOTE]
> - Den här självstudien fungerar med exempel som du kan kopiera och köra med [Nodejs](https://nodejs.org/). Anvisningar om hur du skapar ett Node.js-program finns i Skapa och distribuera ett Node.js-program till en [Azure-webbplats](../app-service/quickstart-nodejs.md)eller [Node.js-molntjänst med hjälp av Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)
Om du vill installera npm-paketet för Service Bus öppnar du en kommandotolk som har i sökvägen, ändrar katalogen till den mapp där du vill ha dina exempel och kör sedan `npm` det här kommandot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Följande exempelkod visar hur du skickar ett meddelande till en kö.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/).
2. Skapa en fil med `send.js` namnet och klistra in koden nedan i den. Den här koden skickar ett meddelande till din kö. Meddelandet har en etikett (Scientist) och brödtext (Bob).

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
    async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Ersätt `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` med anslutningssträngen till Service Bus namnområdet.
1. Ersätt `<QUEUE NAME>` med namnet på kön. 
1. Kör sedan kommandot i en kommandotolk för att köra den här filen.

    ```console
    node send.js 
    ```
1. Du bör se följande utdata.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med `receive.js` namnet och klistra in följande kod i den.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Ersätt `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` med anslutningssträngen till Service Bus namnområdet.
1. Ersätt `<QUEUE NAME>` med namnet på kön. 
1. Kör sedan kommandot i en kommandotolk för att köra den här filen.

    ```console
    node receive.js
    ```
1. Du bör se följande utdata.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

På sidan **Översikt** för Service Bus i Azure Portal kan du se antal **inkommande** och **utgående** meddelanden. Du kan behöva vänta i någon minut och sedan uppdatera sidan för att se de senaste värdena. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Antal inkommande och utgående meddelanden":::

Välj kön på den här **översiktssidan** för att gå till **sidan Service Bus Kö.** Du ser även **antalet inkommande** **och** utgående meddelanden på den här sidan. Du ser också annan information, till exempel **den aktuella** storleken på kön, **maximal storlek,** **antal aktiva meddelanden** och så vidare. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Köinformation":::
## <a name="next-steps"></a>Nästa steg
Se följande dokumentation och exempel: 

- [Azure Service Bus klientbibliotek för JavaScript](https://www.npmjs.com/package/@azure/service-bus)
- [JavaScript-exempel](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript-exempel](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Referensdokumentation för API](/javascript/api/overview/azure/service-bus)
