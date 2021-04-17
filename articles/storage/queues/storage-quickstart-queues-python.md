---
title: 'Snabbstart: Azure Queue Storage klientbibliotek v12 – Python'
description: Lär dig hur du använder Azure Queue Storage-klientbiblioteket v12 för Python för att skapa en kö och lägga till meddelanden i den. Lär dig sedan hur du läser och tar bort meddelanden från kön. Du får också lära dig hur du tar bort en kö.
author: twooley
ms.author: twooley
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 68f68c32b160757e82d59b1dd5ee4847a06ec698
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534362"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Snabbstart: Azure Queue Storage klientbibliotek v12 för Python

Kom igång med Azure Queue Storage klientbibliotek v12 för Python. Azure Queue Storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ de här stegen för att installera paketet och prova exempelkoden för grundläggande uppgifter.

Använd Azure Queue Storage-klientbiblioteket v12 för Python för att:

- Skapa en kö
- Lägga till meddelanden i en kö
- Titta på meddelanden i en kö
- Uppdatera ett meddelande i en kö
- Ta emot meddelanden från en kö
- Ta bort meddelanden från en kö
- Ta bort en kö

Ytterligare resurser:

- [Referensdokumentation för API](/python/api/azure-storage-queue/index)
- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Paket (Python-paketindex)](https://pypi.org/project/azure-storage-queue/)
- [Exempel](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/)
- Azure Storage konto – skapa [ett lagringskonto](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) för ditt operativsystem – 2.7 eller 3.6+

## <a name="setting-up"></a>Inrätta

Det här avsnittet går igenom hur du förbereder ett projekt för att arbeta Azure Queue Storage klientbiblioteket v12 för Python.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Python-program med namnet `queues-quickstart-v12` .

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för projektet.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Växla till den nyligen skapade `queues-quickstart-v12` katalogen.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Installera paketet

Installera Azure Blob Storage-klientbiblioteket för Python-paketet med hjälp av `pip install` kommandot .

```console
pip install azure-storage-queue
```

Det här kommandot installerar Azure Queue Storage för Python-paketet och alla bibliotek som det är beroende av. I det här fallet är det bara Azure-kärnbiblioteket för Python.

### <a name="set-up-the-app-framework"></a>Konfigurera appramverket

1. Öppna en ny textfil i kodredigeraren
1. Lägg till `import` instruktioner
1. Skapa strukturen för programmet, inklusive mycket grundläggande undantagshantering

    Här är koden:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Spara den nya filen `queues-quickstart-v12.py` som i `queues-quickstart-v12` katalogen .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett kömeddelande kan vara upp till 64 KB stort. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning för arbete som ska bearbetas asynkront. Queue Storage erbjuder tre typer av resurser:

- Lagringskontot
- En kö i lagringskontot
- Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över arkitekturen för Queue Storage](./media/storage-queues-introduction/queue1.png)

Använd följande Python-klasser för att interagera med dessa resurser:

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` gör att du kan hantera alla köer i ditt lagringskonto.
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): Med `QueueClient` klassen kan du hantera och ändra en enskild kö och dess meddelanden.
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): Klassen `QueueMessage` representerar de enskilda objekt som returneras vid anrop till en [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) kö.

## <a name="code-examples"></a>Kodexempel

De här exempelkodfragmenten visar hur du gör följande med Azure Queue Storage klientbibliotek för Python:

- [Hämta anslutningssträngen](#get-the-connection-string)
- [Skapa en kö](#create-a-queue)
- [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
- [Titta på meddelanden i en kö](#peek-at-messages-in-a-queue)
- [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
- [Ta emot meddelanden från en kö](#receive-messages-from-a-queue)
- [Ta bort meddelanden från en kö](#delete-messages-from-a-queue)
- [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Följande kod hämtar anslutningssträngen för lagringskontot. Anslutningssträngen lagras miljövariabeln som skapades i [avsnittet Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den här koden i `try` -blocket:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Skapa en kö

Bestäm ett namn för den nya kön. Följande kod lägger till ett UUID-värde i könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemener, siffror och bindestreck och måste börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste också vara mellan 3 och 63 tecken långt. Mer information finns i [Namngivning av köer och metadata](/rest/api/storageservices/naming-queues-and-metadata).

Skapa en instans av [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) klassen . Anropa sedan metoden [`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) för att skapa kön i ditt lagringskonto.

Lägg till den här koden i slutet av `try` blocket:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Lägga till meddelanden i en kö

Följande kodfragment lägger till meddelanden i kön genom att anropa [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) metoden . Den sparar också det [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) returnerade från det tredje `send_message` anropet. `saved_message`används för att uppdatera meddelandeinnehållet senare i programmet.

Lägg till den här koden i slutet av `try` blocket:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Granska meddelanden i en kö

Granska meddelandena i kön genom att anropa [`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) metoden . Den här metoden hämtar ett eller flera meddelanden från kön, men ändrar inte meddelandets synlighet.

Lägg till den här koden i slutet av `try` blocket:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Uppdatera ett meddelande i en kö

Uppdatera innehållet i ett meddelande genom att anropa [`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metoden . Den här metoden kan ändra tidsgränsen och innehållet för ett meddelandes synlighet. Meddelandeinnehållet måste vara en UTF-8-kodad sträng som är upp till 64 kB stor. Tillsammans med det nya innehållet skickar du in värden från meddelandet som sparades tidigare i koden. Värdena `saved_message` identifierar vilket meddelande som ska uppdateras.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Ladda ned tidigare tillagda meddelanden genom att anropa [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metoden .

Lägg till den här koden i slutet av `try` blocket:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Ta bort meddelanden från en kö

Ta bort meddelanden från kön när de har tagits emot och bearbetats. I det här fallet visar bearbetningen bara meddelandet i konsolen.

Appen pausar för användarindata genom att `input` anropa innan den bearbetar och tar bort meddelanden. Kontrollera i [Azure Portal](https://portal.azure.com) att resurserna har skapats korrekt innan de tas bort. Meddelanden som inte uttryckligen tas bort blir så småningom synliga i kön igen för en annan möjlighet att bearbeta dem.

Lägg till den här koden i slutet av `try` blocket:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Ta bort en kö

Följande kod rensar de resurser som appen skapade genom att ta bort kön med hjälp av [`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metoden .

Lägg till den här koden i slutet `try` av blocket och spara filen:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. Koden listar meddelandena i kön och hämtar och tar sedan bort dem innan de slutligen tar bort kön.

I konsolfönstret navigerar du till katalogen som innehåller `queues-quickstart-v12.py` filen och använder sedan följande kommando för att köra `python` appen.

```console
python queues-quickstart-v12.py
```

Appens utdata liknar följande exempel:

```output
Azure Queue Storage client library v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

När appen pausar innan du tar emot meddelanden kontrollerar du ditt lagringskonto i [Azure Portal](https://portal.azure.com). Kontrollera att meddelandena finns i kön.

Tryck på `Enter` tangenten för att ta emot och ta bort meddelandena. När du uppmanas till det trycker `Enter` du på tangenten igen för att ta bort kön och avsluta demonstrationen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en kö och lägger till meddelanden i den med hjälp av Python-kod. Sedan lärde du dig att granska, hämta och ta bort meddelanden. Slutligen lärde du dig hur du tar bort en meddelandekö.

Självstudier, exempel, snabbstarter och annan dokumentation finns här:

> [!div class="nextstepaction"]
> [Azure för Python-utvecklare](/azure/python/)

- Mer information finns i Azure Storage [för Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
- Mer information Azure Queue Storage exempelappar finns i [Azure Queue Storage-klientbiblioteket v12 för Python – exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
