---
title: Läs Azure Event Hubs fångade data från en python-app (senaste)
description: Den här artikeln visar hur du skriver python-kod för att avbilda data som skickas till en händelsehubben och läsa insamlade händelse data från ett Azure Storage-konto.
ms.topic: quickstart
ms.date: 01/04/2021
ms.openlocfilehash: acc2ce04add5fd837e9edc789e9616a9f04fb4b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97883205"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub"></a>Avbilda Event Hubs data i Azure Storage och läsa den med hjälp av python (Azure-eventhub)

Du kan konfigurera en Event Hub så att data som skickas till en Event Hub samlas in i ett Azure Storage-konto eller Azure Data Lake Storage gen 1 eller gen 2. Den här artikeln visar hur du skriver python-kod för att skicka händelser till en händelsehubben och läsa insamlade data från **Azure Blob Storage**. Mer information om den här funktionen finns i [Översikt över Event Hubs avbildnings funktionen](event-hubs-capture-overview.md).

I den här snabb starten används [Azure python SDK](https://azure.microsoft.com/develop/python/) för att demonstrera insamlings funktionen. *Sender.py* -appen skickar simulerad miljö telemetri till Event Hub i JSON-format. Händelsehubben är konfigurerad att använda funktionen Capture för att skriva dessa data till Blob Storage i batchar. *Capturereader.py* -appen läser dessa blobbar och skapar en tilläggs fil för varje enhet. Appen skriver sedan data till CSV-filer.

I den här snabbstarten kommer du att göra följande: 

> [!div class="checklist"]
> * Skapa ett Azure Blob Storage-konto och-behållare i Azure Portal.
> * Skapa ett Event Hubs namn område med hjälp av Azure Portal.
> * Skapa en Event Hub med insamlings funktionen aktive rad och Anslut den till ditt lagrings konto.
> * Skicka data till händelsehubben med hjälp av ett Python-skript.
> * Läsa och bearbeta filer från Event Hubs avbildning med hjälp av ett annat Python-skript.

## <a name="prerequisites"></a>Förutsättningar

- Python med PIP och följande paket installerade. Koden i den här artikeln har testats mot dessa versioner. 
    - Python 3.7
    - Azure-eventhub-5.2.0
    - Azure-Storage – BLOB-12.6.0
    - Avro – python3 1.10.1
- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.  
- Ett aktivt Event Hubs namn område och händelsehubben.
[Skapa ett Event Hubs-namnområde och en händelsehubben i namn området](event-hubs-create.md). Registrera namnet på Event Hubs namn området, namnet på händelsehubben och den primära åtkomst nyckeln för namn området. Information om hur du hämtar åtkomst nyckeln finns i [Hämta en Event Hubs anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Standard nyckel namnet är *RootManageSharedAccessKey*. I den här snabb starten behöver du bara den primära nyckeln. Du behöver inte anslutnings strängen.  
- Ett Azure Storage-konto, en BLOB-behållare i lagrings kontot och en anslutnings sträng till lagrings kontot. Gör så här om du inte har dessa objekt:  
    1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [Skapa en blob-container i lagringskontot](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md)

    Se till att du registrerar anslutnings strängen och behållar namnet för senare användning i den här snabb starten.  
- Aktivera funktionen Capture för Event Hub. Det gör du genom att följa anvisningarna i [aktivera Event Hubs avbildning med hjälp av Azure Portal](event-hubs-capture-enable-through-portal.md). Välj det lagrings konto och den BLOB-behållare som du skapade i föregående steg. Du kan också aktivera funktionen när du skapar en Event Hub.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Skapa ett Python-skript för att skicka händelser till händelsehubben
I det här avsnittet skapar du ett Python-skript som skickar 200-händelser (10 enheter * 20 händelser) till en Event Hub. Dessa händelser är ett exempel på en miljö läsning som skickas i JSON-format. 

1. Öppna din favorit-eller python-redigerare, t. ex. [Visual Studio Code][Visual Studio Code].
2. Skapa ett skript med namnet *Sender.py*. 
3. Klistra in följande kod i *Sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Ersätt följande värden i skripten:  
    * Ersätt `EVENT HUBS NAMESPACE CONNECTION STRING` med anslutnings strängen för din Event Hubs-namnrymd.  
    * Ersätt `EVENT HUB NAME` med namnet på händelsehubben.  
5. Kör skriptet för att skicka händelser till händelsehubben.  
6. I Azure Portal kan du kontrol lera att händelsehubben har tagit emot meddelandena. Växla till vyn **meddelanden** i avsnittet **mått** . Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan sidan visar att meddelandena har mottagits. 

    [![Verifiera att händelsehubben tog emot meddelanden](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Skapa ett Python-skript för att läsa dina insamlingsfiler
I det här exemplet lagras insamlade data i Azure Blob Storage. Skriptet i det här avsnittet läser insamlade datafiler från ditt Azure Storage-konto och genererar CSV-filer så att du enkelt kan öppna och visa dem. 10 filer visas i den aktuella arbets katalogen i programmet. De här filerna kommer att innehålla miljö avläsningar för 10 enheter. 

1. Skapa ett skript med namnet *capturereader.py* i python-redigeraren. Det här skriptet läser de fångade filerna och skapar en fil för varje enhet för att skriva data enbart för den enheten.
2. Klistra in följande kod i *capturereader.py*. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Ersätt `AZURE STORAGE CONNECTION STRING` med anslutnings strängen för ditt Azure Storage-konto. Namnet på den behållare som du skapade i den här snabb starten är *infångad*. Om du har använt ett annat namn på behållaren ersätter du *fånga* med namnet på behållaren i lagrings kontot. 

## <a name="run-the-scripts"></a>Kör skripten
1. Öppna en kommando tolk med python i sökvägen och kör sedan dessa kommandon för att installera python-nödvändiga paket:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```

    > [!NOTE]
    > Koden i den här artikeln har testats mot dessa versioner. 
    > - Python 3.7
    > - Azure-eventhub-5.2.0
    > - Azure-Storage – BLOB-12.6.0
    > - Avro – python3 1.10.1
2. Ändra katalogen till den katalog där du sparade *Sender.py* och *capturereader.py* och kör följande kommando:
   
   ```
   python sender.py
   ```
   
   Det här kommandot startar en ny python-process för att köra avsändaren.
3. Vänta några minuter tills avbildningen har körts och ange sedan följande kommando i det ursprungliga kommando fönstret:
   
   ```
   python capturereader.py
   ```

   Den här avbildnings processorn använder den lokala katalogen för att ladda ned alla blobbar från lagrings kontot och behållaren. Den bearbetar alla som inte är tomma och skriver resultatet som CSV-filer i den lokala katalogen.

## <a name="next-steps"></a>Nästa steg
Kolla [in python-exempel på GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
