---
title: 'Snabb start: Azure Queue Storage klient bibliotek V12 – python'
description: Lär dig hur du använder Azure-Queue Storage klient bibliotek V12 för python för att skapa en kö och lägga till meddelanden till den. Lär dig sedan att läsa och ta bort meddelanden från kön. Du lär dig också hur du tar bort en kö.
author: twooley
ms.author: twooley
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-python
ms.openlocfilehash: 1881af372c1f4e1c5cbb4ea7be0ede4c96bbf4ee
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276152"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Snabb start: Azure Queue Storage klient bibliotek V12 för python

Kom igång med Azure-Queue Storage klient bibliotek V12 för python. Azure Queue Storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.

Använd Azure-Queue Storage klient bibliotek V12 för python för att:

- Skapa en kö
- Lägga till meddelanden i en kö
- Granska meddelanden i en kö
- Uppdatera ett meddelande i en kö
- Ta emot meddelanden från en kö
- Ta bort meddelanden från en kö
- Ta bort en kö

Ytterligare resurser:

- [Referensdokumentation för API](/python/api/azure-storage-queue/index)
- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Paket (python-paket index)](https://pypi.org/project/azure-storage-queue/)
- [Exempel](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- Azure Storage konto – [skapa ett lagrings konto](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) för ditt operativ system – 2,7 eller 3,6 +

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du förbereder ett projekt så att det fungerar med Azure-Queue Storage klient biblioteks V12 för python.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett python-program med namnet `queues-quickstart-v12` .

1. Skapa en ny katalog för projektet i ett konsol fönster (till exempel cmd, PowerShell eller bash).

    ```console
    mkdir queues-quickstart-v12
    ```

1. Växla till den nyligen skapade `queues-quickstart-v12` katalogen.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Installera paketet

Installera Azure Blob Storage-klient biblioteket för python-paket med hjälp av `pip install` kommandot.

```console
pip install azure-storage-queue
```

Det här kommandot installerar Azure Queue Storage-klient biblioteket för python-paket och alla bibliotek som det är beroende av. I det här fallet är det bara Azures kärn bibliotek för python.

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

1. Öppna en ny textfil i kod redigeraren
1. Lägg till `import` instruktioner
1. Skapa strukturen för programmet, inklusive mycket grundläggande undantags hantering

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

1. Spara den nya filen som `queues-quickstart-v12.py` i `queues-quickstart-v12` katalogen.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objekt modell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett Queue-meddelande kan vara upp till 64 KB stort. En kö kan innehålla miljon tals meddelanden, upp till den totala kapacitets gränsen för ett lagrings konto. Köer används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront. Queue Storage erbjuder tre typer av resurser:

- Lagrings kontot
- En kö i lagrings kontot
- Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över kö Storage-arkitektur](./media/storage-queues-introduction/queue1.png)

Använd följande python-klasser för att interagera med dessa resurser:

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): Med `QueueServiceClient` kan du hantera alla köer i ditt lagrings konto.
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` Klassen låter dig hantera och ändra en enskild kö och dess meddelanden.
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` Klassen representerar de enskilda objekt som returneras vid anrop [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) i en kö.

## <a name="code-examples"></a>Kodexempel

I de här exempel kods tycken visar vi hur du utför följande åtgärder med Azure Queue Storage-klient biblioteket för python:

- [Hämta anslutningssträngen](#get-the-connection-string)
- [Skapa en kö](#create-a-queue)
- [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
- [Granska meddelanden i en kö](#peek-at-messages-in-a-queue)
- [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
- [Ta emot meddelanden från en kö](#receive-messages-from-a-queue)
- [Ta bort meddelanden från en kö](#delete-messages-from-a-queue)
- [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Följande kod hämtar anslutnings strängen för lagrings kontot. Anslutnings strängen lagras i den miljö variabel som skapades i avsnittet [Konfigurera din lagrings anslutnings sträng](#configure-your-storage-connection-string) .

Lägg till den här koden inuti `try` blocket:

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

Välj ett namn för den nya kön. Följande kod lägger till ett UUID-värde till könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemena bokstäver, siffror och bindestreck, och måste börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste vara mellan 3 och 63 tecken långt. Mer information finns i [namnge köer och metadata](/rest/api/storageservices/naming-queues-and-metadata).

Skapa en instans av [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) klassen. Anropa sedan [`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) metoden för att skapa kön i ditt lagrings konto.

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

Följande kodfragment lägger till meddelanden i kön genom att anropa- [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) metoden. Den sparar även den som [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) returneras från det tredje `send_message` anropet. `saved_message`Används för att uppdatera meddelande innehållet senare i programmet.

Lägg till den här koden i slutet av `try` blocket:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Granska meddelanden i en kö

Titta på meddelandena i kön genom att anropa- [`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) metoden. Den här metoden hämtar ett eller flera meddelanden från början av kön, men ändrar inte synligheten för meddelandet.

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

Uppdatera innehållet i ett meddelande genom att anropa- [`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metoden. Den här metoden kan ändra ett meddelandes Synlighets-timeout och innehåll. Meddelande innehållet måste vara en kodad UTF-8-sträng som är upp till 64 KB stor. Förutom det nya innehållet skickar du värden från meddelandet som sparades tidigare i koden. `saved_message`Värdena identifierar vilket meddelande som ska uppdateras.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Hämta tidigare tillagda meddelanden genom att anropa- [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metoden.

Lägg till den här koden i slutet av `try` blocket:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Ta bort meddelanden från en kö

Ta bort meddelanden från kön när de har tagits emot och bearbetats. I det här fallet visar bearbetningen bara meddelandet i-konsolen.

Appen pausar indata från användaren genom att anropa `input` innan den bearbetar och tar bort meddelandena. Kontrol lera i [Azure Portal](https://portal.azure.com) att resurserna har skapats korrekt innan de tas bort. Eventuella meddelanden som inte tas bort kommer att bli synliga i kön igen för en annan chans att bearbeta dem.

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

Följande kod rensar resurserna som skapas av appen genom att ta bort kön med hjälp av- [`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metoden.

Lägg till den här koden i slutet av `try` blocket och spara filen:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. I koden visas meddelanden i kön, sedan hämtas och tas de bort innan kön tas bort.

I konsol fönstret navigerar du till den katalog som innehåller `queues-quickstart-v12.py` filen och använder sedan följande `python` kommando för att köra appen.

```console
python queues-quickstart-v12.py
```

Utdata från appen liknar följande exempel:

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

När appen pausar innan meddelanden tas emot kontrollerar du ditt lagrings konto i [Azure Portal](https://portal.azure.com). Kontrol lera att meddelandena finns i kön.

Tryck på knappen `Enter` för att ta emot och ta bort meddelandena. När du uppmanas till det trycker du på `Enter` nyckeln igen för att ta bort kön och avsluta demonstrationen.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en kö och lägger till meddelanden i den med hjälp av python-kod. Sedan har du lärt dig att granska, hämta och ta bort meddelanden. Slutligen har du lärt dig hur du tar bort en meddelandekö.

För självstudier, exempel, snabb starter och annan dokumentation går du till:

> [!div class="nextstepaction"]
> [Azure för Python-utvecklare](/azure/python/)

- Mer information finns i [Azure Storage bibliotek för python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
- Mer information om Azure Queue Storage Sample-appar finns i [azure Queue Storage-klient bibliotek V12 för python-samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
