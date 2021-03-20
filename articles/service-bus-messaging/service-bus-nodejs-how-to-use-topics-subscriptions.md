---
title: Använd för hands versionen av Java Script Azure/Service-Bus med ämnen och prenumerationer
description: Lär dig hur du skriver ett JavaScript-program som använder den senaste för hands versionen av @azure/service-bus paketet för att skicka meddelanden till ett Service Bus ämne och ta emot meddelanden från en prenumeration på avsnittet.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: a1afe4207ce3833f3bcb55bc7bc2e8e27f393f63
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98180004"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Snabb start: Service Bus ämnen och prenumerationer med Node.js och för hands versionen av Azure/Service-Bus-paketet
I den här självstudien får du lära dig hur du använder [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketet i ett JavaScript-program för att skicka meddelanden till ett Service Bus ämne och ta emot meddelanden från en Service Bus-prenumeration på det avsnittet.

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Följ stegen i [snabb starten: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på avsnittet](service-bus-quickstart-topics-subscriptions-portal.md). Anteckna anslutnings strängen, ämnes namnet och ett prenumerations namn. Du kommer bara att använda en prenumeration för den här snabb starten. 

> [!NOTE]
> - Den här självstudien fungerar med exempel som du kan kopiera och köra med [NodeJS](https://nodejs.org/). Instruktioner för hur du skapar ett Node.js program finns i [skapa och distribuera ett Node.js program till en Azure-webbplats](../app-service/quickstart-nodejs.md)eller [Node.js moln tjänst med Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)
Om du vill installera NPM-paketet för Service Bus öppnar du en kommando tolk med `npm` sökvägen och ändrar katalogen till den mapp där du vill ha dina exempel och kör sedan det här kommandot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Följande exempel kod visar hur du skickar en batch med meddelanden till ett Service Bus ämne. Se kod kommentarer för mer information. 

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `sendtotopic.js` och klistra in nedanstående kod i den. Den här koden skickar ett meddelande till ditt ämne.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
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
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
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
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
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
3. Ersätt `<SERVICE BUS NAMESPACE CONNECTION STRING>` med anslutnings strängen till Service Bus namn området.
1. Ersätt `<TOPIC NAME>` med namnet på ämnet. 
1. Kör sedan kommandot i en kommando tolk för att köra den här filen.

    ```console
    node sendtotopic.js 
    ```
1. Du bör se följande utdata.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet **receivefromsubscription.js** och klistra in följande kod i den. Se kod kommentarer för mer information. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
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
3. Ersätt `<SERVICE BUS NAMESPACE CONNECTION STRING>` med anslutnings strängen till namn området. 
1. Ersätt `<TOPIC NAME>` med namnet på ämnet. 
1. Ersätt `<SUBSCRIPTION NAME>` med namnet på prenumerationen i ämnet. 
1. Kör sedan kommandot i en kommando tolk för att köra den här filen.

    ```console
    node receivefromsubscription.js
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

I Azure Portal navigerar du till Service Bus namn området och markerar avsnittet i den nedre rutan för att se sidan **Service Bus ämne** för ditt ämne. På den här sidan bör du se tre inkommande och tre utgående meddelanden i **meddelande** diagrammet. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Inkommande och utgående meddelanden":::

Om du kör den enda sändnings appen nästa gången ser du sex inkommande meddelanden (3 nya) på sidan **Service Bus ämne** , men tre utgående meddelanden. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Uppdaterad ämnes sida":::

Om du väljer en prenumeration på den här sidan kommer du till sidan **Service Bus prenumeration** . Du kan se antalet aktiva meddelanden, antal meddelanden om obeställbara meddelanden och mer på den här sidan. I det här exemplet finns det tre aktiva meddelanden som inte har tagits emot av en mottagare än. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Antal aktiva meddelanden":::

## <a name="next-steps"></a>Nästa steg
Se följande dokumentation och exempel: 

- [Azure Service Bus klient bibliotek för python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). **JavaScript** -mappen har JavaScript-exempel och **typescript** har typescript-exempel. 
- [dokumentation om Azure-Service Bus-referens](/javascript/api/overview/azure/service-bus)
