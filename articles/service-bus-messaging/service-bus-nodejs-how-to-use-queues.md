---
title: Använda Azure/Service-Bus-köer i Java Script
description: Lär dig hur du skriver ett JavaScript-program som använder den senaste för hands versionen av @azure/service-bus paketet för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: ac24d84176f27170648545bc8044c5dcbc77781a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180021"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Skicka meddelanden till och ta emot meddelanden från Azure Service Bus köer (Java Script)
I den här självstudien får du lära dig hur du använder [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketet i ett JavaScript-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö.

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö att arbeta med följer du stegen i artikeln [använd Azure Portal för att Service Bus skapa](service-bus-quickstart-portal.md) en kö. Anteckna **anslutnings strängen** för Service Bus namn området och namnet på **kön** som du skapade.

> [!NOTE]
> - Den här självstudien fungerar med exempel som du kan kopiera och köra med [NodeJS](https://nodejs.org/). Instruktioner för hur du skapar ett Node.js program finns i [skapa och distribuera ett Node.js program till en Azure-webbplats](../app-service/quickstart-nodejs.md)eller [Node.js moln tjänst med Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)
Om du vill installera NPM-paketet för Service Bus öppnar du en kommando tolk med `npm` sökvägen och ändrar katalogen till den mapp där du vill ha dina exempel och kör sedan det här kommandot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Följande exempel kod visar hur du skickar ett meddelande till en kö.

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/).
2. Skapa en fil med namnet `send.js` och klistra in nedanstående kod i den. Den här koden skickar ett meddelande till kön. Meddelandet har en etikett (expert) och brödtext (Einstein).

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
3. Ersätt `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` med anslutnings strängen till Service Bus namn området.
1. Ersätt `<QUEUE NAME>` med namnet på kön. 
1. Kör sedan kommandot i en kommando tolk för att köra den här filen.

    ```console
    node send.js 
    ```
1. Du bör se följande utdata.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `receive.js` och klistra in följande kod i den.

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
3. Ersätt `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` med anslutnings strängen till Service Bus namn området.
1. Ersätt `<QUEUE NAME>` med namnet på kön. 
1. Kör sedan kommandot i en kommando tolk för att köra den här filen.

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

På sidan **Översikt** för Service Bus namn området i Azure Portal kan du se antalet **inkommande** och **utgående** meddelanden. Du kan behöva vänta en minut eller så och sedan uppdatera sidan för att se de senaste värdena. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Antal inkommande och utgående meddelanden":::

Välj kön på den här **översikts** sidan för att gå till sidan **Service Bus kö** . Du ser det **inkommande** och **utgående** meddelande antalet på den här sidan. Du kan också se annan information, till exempel köns **aktuella storlek** , **maximal storlek**, **Antal aktiva meddelanden** och så vidare. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Information om kö":::
## <a name="next-steps"></a>Nästa steg
Se följande dokumentation och exempel: 

- [Azure Service Bus klient bibliotek för JS](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). **JavaScript** -mappen har JavaScript-exempel och **typescript** har typescript-exempel. 
- [dokumentation om Azure-Service Bus-referens](/javascript/api/overview/azure/service-bus)
