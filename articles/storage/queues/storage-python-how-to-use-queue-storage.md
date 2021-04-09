---
title: Använda Azure Queue Storage från python
description: Lär dig att använda Azure Queue Storage från python för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 02/16/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 8c30d05cf49162155f84961e1fd8a32361444ba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653269"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Använda Azure Queue Storage från python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Översikt

Den här artikeln visar vanliga scenarier med hjälp av Azure Queue Storage-tjänsten. De scenarier som beskrivs är att infoga, granska, hämta och ta bort Kömeddelanden. Kod för att skapa och ta bort köer omfattas också.

Exemplen i den här artikeln är skrivna i python och använder [azures Queue Storage klient bibliotek för python](https://github.com/azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue). Mer information om köer finns i avsnittet [Nästa steg](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Ladda ned och installera Azure Storage SDK för Python

[Azure Storage SDK för python](https://github.com/azure/azure-storage-python) kräver python v 2.7, v 3.3 eller senare.

### <a name="install-via-pypi"></a>Installera via PyPI

Om du vill installera via python-paket indexet (PyPI) skriver du:

# <a name="python-v12"></a>[Python-V12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för python v-0.36 eller tidigare avinstallerar du den äldre SDK: n med `pip uninstall azure-storage` innan du installerar det senaste paketet.

Alternativa installations metoder finns i [Azure SDK för python](https://github.com/Azure/Azure-SDK-for-Python).

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera ditt program till att få åtkomst till Queue Storage

# <a name="python-v12"></a>[Python-V12](#tab/python)

[`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient)Med objektet kan du arbeta med en kö. Lägg till följande kod nära överkanten av en python-fil där du vill få åtkomst till en Azure-kö program mässigt:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true)Med objektet kan du arbeta med köer. Följande kod skapar ett- `QueueService` objekt. Lägg till följande kod nära överkanten av en python-fil som du vill ha åtkomst till Azure Storage via programmering:

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

`os`Paketet ger stöd för att hämta en miljö variabel. `uuid`Paketet ger stöd för att skapa en unik identifierare för ett könamn.

## <a name="create-a-queue"></a>Skapa en kö

Anslutnings strängen hämtas från `AZURE_STORAGE_CONNECTION_STRING` miljövariabeln uppsättning tidigare.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Följande kod skapar ett- `QueueClient` objekt med hjälp av lagrings anslutnings strängen.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Följande kod skapar ett- `QueueService` objekt med hjälp av lagrings anslutnings strängen.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

Azure Queue-meddelanden lagras som text. Om du vill lagra binära data ställer du in base64-kodning och avkodnings funktioner innan du skickar ett meddelande i kön.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Konfigurera base64-kodning och avkodning av funktioner när du skapar ett klient objekt.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Konfigurera base64-kodning och avkodnings funktioner på Queue Storage-objekt.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="insert-a-message-into-a-queue"></a>Infoga ett meddelande i en kö

# <a name="python-v12"></a>[Python-V12](#tab/python)

Om du vill infoga ett meddelande i en kö använder du- [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) metoden.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Om du vill infoga ett meddelande i en kö använder du [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) metoden för att skapa ett nytt meddelande och lägger till det i kön.

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

## <a name="peek-at-messages"></a>Titta på meddelanden

# <a name="python-v12"></a>[Python-V12](#tab/python)

Du kan titta på meddelanden utan att ta bort dem från kön genom att anropa- [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) metoden. Den här metoden granskar som standard ett enskilt meddelande.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Du kan titta på meddelanden utan att ta bort dem från kön genom att anropa- [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) metoden. Den här metoden granskar som standard ett enskilt meddelande.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett meddelande i kön

Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en uppgift kan du använda den här funktionen för att uppdatera aktivitetens status.

# <a name="python-v12"></a>[Python-V12](#tab/python)

I följande kod används [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metoden för att uppdatera ett meddelande. Timeout för synlighet anges till 0, vilket innebär att meddelandet visas omedelbart och innehållet uppdateras.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

I följande kod används [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) metoden för att uppdatera ett meddelande. Timeout för synlighet anges till 0, vilket innebär att meddelandet visas omedelbart och innehållet uppdateras.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>Hämta kölängden

Du kan hämta en uppskattning av antalet meddelanden i en kö.

# <a name="python-v12"></a>[Python-V12](#tab/python)

[Get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) -metoden returnerar köegenskaper inklusive `approximate_message_count` .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-)Metoden returnerar köegenskaper `approximate_message_count` , inklusive.

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

Resultatet är bara ungefärlig eftersom meddelanden kan läggas till eller tas bort när tjänsten svarar på din begäran.

## <a name="dequeue-messages"></a>Ta bort meddelanden från kön

Ta bort ett meddelande från en kö i två steg. Om din kod inte kan bearbeta ett meddelande, säkerställer den här processen att du får samma meddelande och försöker igen. Anrop `delete_message` efter att meddelandet har bearbetats.

# <a name="python-v12"></a>[Python-V12](#tab/python)

När du anropar [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)får du nästa meddelande i kön som standard. Ett meddelande som returnerades från `receive_messages` blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-).

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

När du anropar [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)får du nästa meddelande i kön som standard. Ett meddelande som returnerades från `get_messages` blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-).

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande.

# <a name="python-v12"></a>[Python-V12](#tab/python)

I följande kod exempel används [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metoden för att hämta meddelanden i batchar. Sedan bearbetar den varje meddelande i varje batch med hjälp av en kapslad `for` slinga. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

I följande kod exempel används [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) metoden för att hämta 16 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `for` loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Ta bort en kö

# <a name="python-v12"></a>[Python-V12](#tab/python)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du- [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metoden.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du- [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) metoden.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Queue Storage kan du följa dessa länkar om du vill veta mer.

- [Azure Queue Storage python API-referens](/python/api/azure-storage-queue)
- [Python Developer Center](https://azure.microsoft.com/develop/python/)
- [Azure Storage REST API referens](/rest/api/storageservices/)
