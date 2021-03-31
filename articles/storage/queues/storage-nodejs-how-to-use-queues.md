---
title: Använda Azure Queue Storage från Node.js-Azure Storage
description: Lär dig att använda Azure-Queue Storage för att skapa och ta bort köer. Lär dig att infoga, hämta och ta bort meddelanden med hjälp av Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 12ae05e10cdf0fa9a5f0725acaa1784eedc3612c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803695"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Så här använder du Azure Queue Storage från Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Översikt

Den här guiden visar hur du utför vanliga scenarier med Azure Queue Storage. Exemplen skrivs med hjälp av Node.js-API: et. De scenarier som beskrivs är att infoga, granska, hämta och ta bort Kömeddelanden. Lär dig också att skapa och ta bort köer.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Om du vill skapa en tom Node.js-app, se [skapa en Node.js webbapp i Azure App Service](../../app-service/quickstart-nodejs.md), [skapa och distribuera ett Node.js program till Azure Cloud Services](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) med PowerShell eller [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för åtkomst till lagring

[Azure Storage klient bibliotek för Java Script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript) innehåller en uppsättning av praktiska bibliotek som kommunicerar med lagrings rest tjänsterna.

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Hämta paketet med hjälp av Node Pack Manager (NPM)

1. Använd ett kommando rads gränssnitt som PowerShell (Windows), Terminal (Mac) eller bash (UNIX) och navigera till mappen där du skapade exempel programmet.

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

1. Skriv `npm install @azure/storage-queue` i kommando fönstret.

1. Kontrol lera att en `node_modules` mapp har skapats. I den mappen hittar du `@azure/storage-queue` paketet, som innehåller klient biblioteket som du behöver för att komma åt lagrings utrymmet.

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

1. Skriv `npm install azure-storage` i kommando fönstret.

1. Kontrol lera att en `node_modules` mapp har skapats. I den mappen hittar du `azure-storage` paketet, som innehåller de bibliotek som du behöver för att komma åt lagringen.

---

### <a name="import-the-package"></a>Importera paketet

Använd kod redigeraren och Lägg till följande i den övre JavaScript-filen där du tänker använda köer.

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Så skapar du en resurs

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Följande kod hämtar värdet för en miljö variabel som kallas `AZURE_STORAGE_CONNECTION_STRING` och använder den för att skapa ett [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) objekt. Objektet används sedan för att skapa ett [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) objekt som gör att du kan arbeta med en speciell kö.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Om kön redan finns genereras ett undantag.

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Azure-modulen läser miljövariablerna `AZURE_STORAGE_ACCOUNT` och `AZURE_STORAGE_ACCESS_KEY` , eller `AZURE_STORAGE_CONNECTION_STRING` information som krävs för att ansluta till ditt Azure Storage-konto. Om de här miljövariablerna inte har angetts måste du ange konto informationen när du anropar `createQueueService` .

Följande kod skapar ett `QueueService` -objekt som gör att du kan arbeta med köer.

```javascript
var queueSvc = azure.createQueueService();
```

Anropa `createQueueIfNotExists` metoden för att skapa en ny kö med det angivna namnet eller returnera kön om den redan finns.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Om kön har skapats `result.created` är sant. Om kön finns `result.created` är false.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här infogar du ett meddelande i en kö

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Anropa metoden för att lägga till ett meddelande i en kö [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Om du vill infoga ett meddelande i en kö anropar du `createMessage` metoden för att skapa ett nytt meddelande och lägger till det i kön.

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Granska nästa meddelande

Du kan granska meddelanden i kön utan att ta bort dem från kön genom att anropa- `peekMessages` metoden.

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Som standard [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) tittar vi på ett enskilt meddelande. Följande exempel tittar på de första fem meddelandena i kön. Om färre än fem meddelanden visas returneras bara de synliga meddelandena.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Som standard `peekMessages` tittar vi på ett enskilt meddelande.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result`Innehåller meddelandet.

---

Anrop `peekMessages` när det inte finns några meddelanden i kön returnerar inte något fel. Inga meddelanden returneras dock.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett köade meddelande

I följande exempel uppdateras texten i ett meddelande.

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Ändra innehållet i ett meddelande på plats i kön genom att anropa [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Ändra innehållet i ett meddelande på plats i kön genom att anropa `updateMessage` .

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Ta ett meddelande ur kö

Att köa ett meddelande är en process i två steg:

1. Hämta meddelandet.

1. Ta bort meddelandet.

Följande exempel hämtar ett meddelande och tar sedan bort det.

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Anropa metoden för att få ett meddelande [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) . Anropet gör meddelandena osynliga i kön, så inga andra klienter kan bearbeta dem. När ditt program har bearbetat ett meddelande, anropa [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) att ta bort det från kön.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Som standard är ett meddelande endast dolt i 30 sekunder. Efter 30 sekunder är det synligt för andra klienter. Du kan ange ett annat värde genom att ställa in [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) när du anropar `receiveMessages` .

Anrop `receiveMessages` när det inte finns några meddelanden i kön returnerar inte något fel. Inga meddelanden kommer dock att returneras.

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Anropa metoden för att få ett meddelande `getMessages` . Anropet gör meddelandena osynliga i kön, så inga andra klienter kan bearbeta dem. När ditt program har bearbetat ett meddelande, anropa `deleteMessage` att ta bort det från kön.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Som standard är ett meddelande endast dolt i 30 sekunder. Efter 30 sekunder är det synligt för andra klienter. Du kan ange ett annat värde med hjälp `options.visibilityTimeout` av `getMessages` .

`getMessages`Om du använder när det inte finns några meddelanden i kön returneras inget fel. Inga meddelanden kommer dock att returneras.

---

## <a name="additional-options-for-dequeuing-messages"></a>Ytterligare alternativ för demsmq-meddelanden

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Det finns två sätt att anpassa meddelande hämtning från en kö:

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): Hämta en batch med meddelanden (upp till 32).
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): Ange en längre eller kortare tids gräns för insikt.

I följande exempel används `receiveMessages` metoden för att hämta fem meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `for` loop. Den anger också timeout för insikter till fem minuter för alla meddelanden som returneras av den här metoden.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Det finns två sätt att anpassa meddelande hämtning från en kö:

- `options.numOfMessages`: Hämta en batch med meddelanden (upp till 32).
- `options.visibilityTimeout`: Ange en längre eller kortare tids gräns för insikt.

I följande exempel används `getMessages` metoden för att få 15 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `for` loop. Den anger också timeout för insikter till fem minuter för alla meddelanden som returneras av den här metoden.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>Så här hämtar du Kölängd

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

[`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-)Metoden returnerar metadata om kön, inklusive det ungefärliga antalet meddelanden som väntar i kön.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

`getQueueMetadata`Metoden returnerar metadata om kön, inklusive det ungefärliga antalet meddelanden som väntar i kön.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Så här listar du köer

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Anropa om du vill hämta en lista över köer [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) . Om du vill hämta en lista som filtrerats efter ett särskilt prefix anger du [Options. prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) i ditt anrop till `listQueues` .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Använd om du vill hämta en lista över köer `listQueuesSegmented` . Använd om du vill hämta en lista som filtrerats efter ett angivet prefix `listQueuesSegmentedWithPrefix` .

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Om det inte går att returnera alla köer, pass `result.continuationToken` som den första parametern för `listQueuesSegmented` eller den andra parametern för `listQueuesSegmentedWithPrefix` att hämta fler resultat.

---

## <a name="how-to-delete-a-queue"></a>Så här tar du bort en kö

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) metoden på `QueueClient` objektet.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Om du vill rensa alla meddelanden från en kö utan att ta bort den, anropa [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) .

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du `deleteQueue` metoden i objektet köobjekt.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Om du vill rensa alla meddelanden från en kö utan att ta bort den, anropa `clearMessages` .

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagrings uppgifter.

- Besök [Azure Storage teamets blogg](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog) och lär dig vad som är nytt
- Besök [Azure Storage klient bibliotek för Java Script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript) -lagringsplats på GitHub
