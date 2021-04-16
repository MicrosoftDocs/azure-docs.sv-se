---
title: 'Snabbstart: Azure Queue Storage klientbibliotek v12 – JavaScript'
description: Lär dig hur du använder Azure Queue Storage-klientbiblioteket v12 för JavaScript för att skapa en kö och lägga till meddelanden i den. Lär dig sedan hur du läser och tar bort meddelanden från kön. Du får också lära dig hur du tar bort en kö.
author: twooley
ms.author: twooley
ms.date: 12/13/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 943678debc32116ff7a2e54810c4edcf2d331bd6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534433"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Snabbstart: Azure Queue Storage klientbibliotek v12 för JavaScript

Kom igång med Azure Queue Storage-klientbiblioteket v12 för JavaScript. Azure Queue Storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ de här stegen för att installera paketet och prova exempelkoden för grundläggande uppgifter.

Använd Azure Queue Storage v12 för JavaScript för att:

- Skapa en kö
- Lägga till meddelanden i en kö
- Titta på meddelanden i en kö
- Uppdatera ett meddelande i en kö
- Ta emot meddelanden från en kö
- Ta bort meddelanden från en kö
- Ta bort en kö

Ytterligare resurser:

- [Referensdokumentation för API](/javascript/api/@azure/storage-queue/)
- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Paket (npm)](https://www.npmjs.com/package/@azure/storage-queue)
- [Exempel](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/)
- Azure Storage konto – skapa [ett lagringskonto](../common/storage-account-create.md)
- Aktuella [Node.js](https://nodejs.org/en/download/) för ditt operativsystem.

## <a name="setting-up"></a>Inrätta

Det här avsnittet går igenom hur du förbereder ett projekt för att arbeta Azure Queue Storage klientbiblioteket v12 för JavaScript.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Node.js program med namnet `queues-quickstart-v12`

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för projektet.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Växla till den nyligen skapade `queues-quickstart-v12` katalogen.

    ```console
    cd queues-quickstart-v12
    ```

1. Skapa en ny textfil med namnet `package.json` . Den här filen definierar Node.js projekt. Spara filen i `queues-quickstart-v12` katalogen . Här är innehållet i filen:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Du kan ange ditt eget namn i `author` för fältet om du vill.

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är `queues-quickstart-v12` i katalogen installerar du Azure Queue Storage-klientbiblioteket för JavaScript-paketet med hjälp av kommandot `npm install` .

```console
npm install
```

Det här kommandot läser filen och installerar `package.json` Azure Queue Storage-klientbiblioteket v12 för JavaScript-paketet och alla bibliotek som det är beroende av.

### <a name="set-up-the-app-framework"></a>Konfigurera appramverket

Från projektkatalogen:

1. Öppna en ny textfil i kodredigeraren
1. Lägga `require` till anrop för att läsa in Azure Node.js moduler
1. Skapa strukturen för programmet, inklusive mycket grundläggande undantagshantering

    Här är koden:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue Storage client library v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Spara den nya filen `queues-quickstart-v12.js` som i `queues-quickstart-v12` katalogen .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett kömeddelande kan vara upp till 64 kB stort. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning för arbete som ska bearbetas asynkront. Queue Storage erbjuder tre typer av resurser:

- Lagringskontot
- En kö i lagringskontot
- Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över arkitekturen för Queue Storage](./media/storage-queues-introduction/queue1.png)

Använd följande JavaScript-klasser för att interagera med dessa resurser:

- [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient` gör att du kan hantera alla köer i ditt lagringskonto.
- [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient): Med `QueueClient` klassen kan du hantera och ändra en enskild kö och dess meddelanden.
- [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage): Klassen `QueueMessage` representerar de enskilda objekt som returneras vid anrop till en [`ReceiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) kö.

## <a name="code-examples"></a>Kodexempel

De här exempelkodfragmenten visar hur du gör följande med Azure Queue Storage klientbiblioteket för JavaScript:

- [Hämta anslutningssträngen](#get-the-connection-string)
- [Skapa en kö](#create-a-queue)
- [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
- [Granska meddelanden i en kö](#peek-at-messages-in-a-queue)
- [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
- [Ta emot meddelanden från en kö](#receive-messages-from-a-queue)
- [Ta bort meddelanden från en kö](#delete-messages-from-a-queue)
- [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Följande kod hämtar anslutningssträngen för lagringskontot från miljövariabeln som skapades i [avsnittet Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den här koden i `main` funktionen:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Skapa en kö

Bestäm ett namn för den nya kön. Följande kod lägger till ett UUID-värde i könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemener, siffror och bindestreck och måste börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste också vara mellan 3 och 63 tecken långt. Mer information finns i [Namngivning av köer och metadata](/rest/api/storageservices/naming-queues-and-metadata).

Skapa en instans av [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) klassen . Anropa sedan metoden [`create`](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) för att skapa kön i ditt lagringskonto.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Lägga till meddelanden i en kö

Följande kodfragment lägger till meddelanden i kön genom att anropa [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) metoden . Den sparar också det [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage) returnerade från det tredje `sendMessage` anropet. Den `sendMessageResponse` returnerade används för att uppdatera meddelandeinnehållet senare i programmet.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Granska meddelanden i en kö

Granska meddelandena i kön genom att anropa [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) metoden . Den här metoden hämtar ett eller flera meddelanden från kön, men ändrar inte meddelandets synlighet.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Uppdatera ett meddelande i en kö

Uppdatera innehållet i ett meddelande genom att anropa [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) metoden . Den här metoden kan ändra tidsgränsen och innehållet för ett meddelandes synlighet. Meddelandeinnehållet måste vara en UTF-8-kodad sträng som är upp till 64 kB stor. Tillsammans med det nya innehållet skickar du in `messageId` och från svaret som `popReceipt` sparades tidigare i koden. Egenskaperna `sendMessageResponse` identifierar vilket meddelande som ska uppdateras.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Ladda ned tidigare tillagda meddelanden genom att anropa [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) metoden . I fältet `numberOfMessages` anger du det maximala antalet meddelanden som ska tas emot för anropet.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Ta bort meddelanden från en kö

Ta bort meddelanden från kön när de har tagits emot och bearbetats. I det här fallet visar bearbetningen bara meddelandet i konsolen.

Ta bort meddelanden genom att anropa [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) metoden . Meddelanden som inte uttryckligen tas bort blir så småningom synliga i kön igen för en annan möjlighet att bearbeta dem.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Ta bort en kö

Följande kod rensar de resurser som appen skapade genom att ta bort kön med hjälp av [`delete`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) metoden .

Lägg till den här koden i slutet `main` av funktionen och spara filen:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. Koden listar meddelandena i kön och hämtar och tar sedan bort dem innan de slutligen tar bort kön.

I konsolfönstret navigerar du till katalogen som innehåller `queues-quickstart-v12.js` filen och använder sedan följande kommando för att köra `node` appen.

```console
node queues-quickstart-v12.js
```

Appens utdata liknar följande exempel:

```output
Azure Queue Storage client library v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Stega igenom koden i felsökningsprogrammet och kontrollera [Azure Portal](https://portal.azure.com) i hela processen. Kontrollera ditt lagringskonto för att kontrollera att meddelanden i kön skapas och tas bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en kö och lägger till meddelanden i den med hjälp av JavaScript-kod. Sedan lärde du dig att granska, hämta och ta bort meddelanden. Slutligen lärde du dig hur du tar bort en meddelandekö.

Självstudier, exempel, snabbstarter och annan dokumentation finns här:

> [!div class="nextstepaction"]
> [Dokumentation om Azure för JavaScript](/azure/developer/javascript/)

- Mer information finns i Azure Queue Storage [klientbibliotek för JavaScript.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- Mer information Azure Queue Storage exempelappar finns i [Azure Queue Storage-klientbiblioteket v12 för JavaScript – exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
