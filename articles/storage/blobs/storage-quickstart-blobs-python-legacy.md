---
title: 'Snabb start: Azure Blob Storage klient bibliotek v 2.1 för python'
description: I den här snabbstarten skapar du ett lagringskonto och en container i objektlagring (Blob). Sedan använder du lagrings klient bibliotek v 2.1 för python och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en behållare.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 34205caf03d2f2d7255f75ea6203c5572c4c429b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95523315"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Snabb start: hantera blobbar med python v 2.1 SDK

I den här snabb starten lär du dig att hantera blobbar med hjälp av python. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och Arkiv data. Du laddar upp, laddar ned och listar blobbar och skapar och tar bort behållare.

> [!NOTE]
> I den här snabb starten används en äldre version av klient biblioteket för Azure Blob Storage. Information om hur du kommer igång med den senaste versionen finns i [snabb start: hantera blobbar med python V12 SDK](storage-quickstart-blobs-python.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagrings konto](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [Azure Storage SDK för python](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) i den här snabbstarten är ett grundläggande Python-program.  

Använd följande [git](https://git-scm.com/) -kommando för att ladda ned programmet till utvecklings miljön. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Om du vill granska python-programmet öppnar du *example.py* -filen i lagrings platsens rot.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

I programmet måste du ange ditt lagringskontonamn och kontonyckel för att skapa ett `BlockBlobService`-objekt.

1. Öppna filen *example.py* från Solution Explorer i IDE.

1. Ersätt `accountname` värdena och `accountkey` med ditt lagrings konto namn och nyckel:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Spara och stäng filen.

## <a name="run-the-sample"></a>Kör exemplet

Exempel programmet skapar en test fil i mappen *dokument* , laddar upp filen till Blob Storage, listar blobarna i filen och laddar ned filen med ett nytt namn.

1. Installera beroendena:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Gå till exempel programmet:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Kör exemplet:

    ```console
    python example.py
    ```

    Meddelandena ser ut ungefär som i följande utdata:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Gå till mappen *dokument* och Sök efter de två filerna innan du fortsätter.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_ \<universally-unique-identifier\> _DOWNLOADED*

1. Du kan öppna dem och se att de är samma.

    Du kan också använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com). Det är lämpligt att visa filerna i Blob Storage. Azure Storage Explorer är ett kostnads fritt plattforms oberoende verktyg som ger dig åtkomst till lagrings konto informationen. 

1. När du har tittat på filerna trycker du på valfri tangent för att slutföra exemplet och ta bort testfilerna.

## <a name="learn-about-the-sample-code"></a>Lär dig mer om exempel koden

Nu när du vet vad exemplet gör kan du öppna *example.py* -filen och titta på koden.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten

I det här avsnittet skapar du instanser av objekten, skapar en ny container och anger sedan behörigheter för containern så att blobarna är offentliga. Du anropar behållaren `quickstartblobs` . 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Först skapar du referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra, och vart och ett används av nästa i listan.

* Skapa en instans av objektet **BlockBlobService**, som pekar mot Blob Service i lagringskontot. 

* Skapa en instans av objektet **CloudBlobContainer**, som representerar den container du får åtkomst till. Systemet använder behållare för att organisera dina blobbar, som du använder mappar på datorn för att organisera dina filer.

När du har molnblobcontainern kan du instansiera objektet **CloudBlockBlob** som pekar mot den specifika blob du är intresserad av. Du kan sedan ladda upp, hämta och kopiera bloben efter behov.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om behållare och blob-namn finns i [namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i containern

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar kan vara så stora som 4,7 TB och kan vara allt från Excel-kalkylblad till stora videofiler. Du kan använda Lägg till blobar för loggning när du vill skriva till en fil och sedan fortsätta lägga till mer information. Page blobbar används främst för virtuella hård diskar (VHD) som backar upp infrastruktur som virtuella datorer i tjänsten (IaaS VM). Blockblobar är de vanligaste. I den här snabb starten används block-blobbar.

Om du vill överföra en fil till en blob hämtar du den fullständiga sökvägen genom att slå ihop katalognamnet och filnamnet på den lokala enheten. Du kan sedan ladda upp filen till angiven sökväg med hjälp av metoden `create_blob_from_path`. 

Exempel koden skapar en lokal fil som systemet använder för att ladda upp och ladda ned, lagra filen som systemet laddar upp som *full_path_to_file* och namnet på blobben som *local_file_name*. I det här exemplet överförs filen till din behållare med namnet `quickstartblobs` :

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Det går att använda flera uppladdningsmetoder med Blob Storage. Om du till exempel har en minnesström kan du kan använda metoden `create_blob_from_stream` snarare än `create_blob_from_path`. 

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Följande kod skapar en `generator` för- `list_blobs` metoden. Koden går igenom listan över blobbar i behållaren och skriver ut deras namn till-konsolen.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Ladda ned blobarna


Ladda ned blobar till din lokala disk med hjälp av `get_blob_to_path` metoden.
Följande kod laddar ned bloben som du laddade upp tidigare. Systemet lägger till *_DOWNLOADED* till BLOB-namnet så att du kan se båda filerna på den lokala disken.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver blobarna som laddades upp i denna snabbstart kan du ta bort hela containern med hjälp av metoden `delete_container`. Om du vill ta bort enskilda filer använder du i stället metoden `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Resurser för att utveckla Python-tillämpningar med blobar

Mer information om python-utveckling med Blob Storage finns i följande ytterligare resurser:

### <a name="binaries-and-source-code"></a>Binärfiler och källkod

- Visa, ladda ned och installera [Python-klientens bibliotekskällkod](https://github.com/Azure/azure-storage-python) för Azure Storage på GitHub.

### <a name="client-library-reference-and-samples"></a>Referens och exempel för klientbiblioteket

- Mer information om python-klient biblioteket finns i [Azure Storage bibliotek för python](/python/api/overview/azure/storage).
- Utforska [Bloblagringsexempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) som skrivits med Python-klientbiblioteket.

## <a name="next-steps"></a>Nästa steg
 
I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med Python. 

Mer information om Storage Explorer och blobbar finns i [Hantera Azure Blob Storage-resurser med Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).