---
title: Skicka eller ta emot händelser från Azure Event Hubs med python (senaste)
description: Den här artikeln innehåller en genom gång av hur du skapar ett python-program som skickar/tar emot händelser till/från Azure Event Hubs med det senaste Azure-eventhub-paketet.
ms.topic: quickstart
ms.date: 02/11/2020
ms.openlocfilehash: 4850dc9119f6ef43e687f476f66928f13713f051
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105959"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub"></a>Skicka händelser till eller ta emot händelser från Event Hub med python (Azure-eventhub)
Den här snabb starten visar hur du skickar händelser till och tar emot händelser från en händelsehubben med hjälp av **Azure-eventhub python-** paketet.

## <a name="prerequisites"></a>Förutsättningar
Om du inte har använt Azure Event Hubs tidigare, se [Event Hubs översikt](event-hubs-about.md) innan du gör den här snabb starten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- Python 2,7 eller 3,5 eller senare, med PIP installerat och uppdaterat.
- Python-paketet för Event Hubs. 

    Installera paketet genom att köra det här kommandot i en kommando tolk med python i sökvägen:

    ```cmd
    pip install azure-eventhub
    ```

    Installera följande paket för att ta emot händelser med hjälp av Azure Blob Storage som kontroll punkts Arkiv:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutnings strängen för Event Hubs namn området genom att** följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutnings strängen senare i den här snabb starten.

## <a name="send-events"></a>Skicka händelser
I det här avsnittet skapar du ett Python-skript för att skicka händelser till händelsehubben som du skapade tidigare.

1. Öppna din favorit-eller python-redigerare, t. ex. [Visual Studio Code](https://code.visualstudio.com/).
2. Skapa ett skript med namnet *send.py*. Det här skriptet skickar en batch med händelser till händelsehubben som du skapade tidigare.
3. Klistra in följande kod i *send.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > För den fullständiga käll koden, inklusive informations kommentarer, går du till [sidan GitHub send_async. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).
    

## <a name="receive-events"></a>Ta emot händelser
Den här snabb starten använder Azure Blob Storage som ett kontroll punkts arkiv. Kontroll punkts arkivet används för att bevara kontroll punkter (det vill säga de senaste Läs positionerna).  


> [!WARNING]
> Om du kör den här koden på Azure Stack hubb får du körnings fel om du inte riktar in dig på en viss lagrings-API-version. Det beror på att Event Hubs SDK använder det senaste tillgängliga Azure Storage API som är tillgängligt i Azure och som kanske inte är tillgängligt på din Azure Stack Hub-plattform. Azure Stack Hub kan stödja en annan version av Storage BLOB SDK än vad som normalt är tillgängligt på Azure. Om du använder Azure blogg Storage som kontroll punkts Arkiv, kontrollerar du [Azure Storage API-versionen som stöds för din Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) -version och aktiverar den versionen i din kod. 
>
> Om du till exempel kör på Azure Stack Hub version 2005 är den högsta tillgängliga versionen för lagrings tjänsten version 2019-02-02. Som standard använder klient biblioteket för Event Hubs SDK den senaste tillgängliga versionen på Azure (2019-07-07 vid tidpunkten för lanseringen av SDK: n). I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod som mål för Storage Service API-versionen 2019-02-02. Ett exempel på hur du riktar in en speciell Storage API-version finns i [synkrona](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) och [asynkrona](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) exempel på GitHub. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Skapa ett Azure Storage-konto och en BLOB-behållare
Skapa ett Azure Storage-konto och en BLOB-behållare i det genom att utföra följande steg:

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Skapa en blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md)

Se till att du registrerar anslutnings strängen och behållar namnet för senare användning i mottagar koden.


### <a name="create-a-python-script-to-receive-events"></a>Skapa ett Python-skript för att ta emot händelser

I det här avsnittet skapar du ett Python-skript för att ta emot händelser från händelsehubben:

1. Öppna din favorit-eller python-redigerare, t. ex. [Visual Studio Code](https://code.visualstudio.com/).
2. Skapa ett skript med namnet *Recv.py*.
3. Klistra in följande kod i *Recv.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method. Read from the beginning of the partition (starting_position: "-1")
            await client.receive(on_event=on_event,  starting_position="-1")

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > För den fullständiga käll koden, inklusive ytterligare informations kommentarer, går du till [sidan GitHub recv_with_checkpoint_store_async. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Köra mottagar appen

Kör skriptet genom att öppna en kommando tolk som har python i sökvägen och kör sedan följande kommando:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Köra avsändar programmet

Kör skriptet genom att öppna en kommando tolk som har python i sökvägen och kör sedan följande kommando:

```bash
python send.py
```

Mottagar fönstret ska visa de meddelanden som skickats till händelsehubben.


## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du skickat och tagit emot händelser asynkront. Information om hur du skickar och tar emot händelser synkront finns på [sidan GitHub sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

För alla exempel (både synkrona och asynkrona) på GitHub går du till [Azure Event Hubs-klient bibliotek för python-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
