---
title: 'Snabb start: Azure Blob Storage klient bibliotek-ruby'
description: Skapa ett lagrings konto och en behållare i Azure Blob Storage. Använd lagrings klient biblioteket för ruby för att skapa en BLOB, hämta en blob och lista blobarna i en behållare.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ec3fc490466f5fce36b67b2f3744e4ee5cc0ae79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96781104"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Snabb start: Azure Blob Storage klient bibliotek för ruby

Lär dig hur du använder ruby för att skapa, ladda ned och lista blobar i en behållare i Microsoft Azure Blob Storage.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Kontrollera att du har följande ytterligare krav installerade:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Azure Storage bibliotek för ruby](https://github.com/azure/azure-storage-ruby)med RubyGem- [paketet](https://rubygems.org/gems/azure-storage-blob):

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) som används i den här snabbstarten är ett enkelt Ruby-program.

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklings miljön. Det här kommandot klonar lagrings platsen till den lokala datorn:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Gå till mappen *Storage-blobs-ruby-snabb start* och öppna filen *example. RB* i kod redigeraren.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

Ange ditt lagrings konto namn och din konto nyckel för att skapa en [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) -instans för ditt program.

Följande kod i *exempel. RB* -filen instansierar ett nytt [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) -objekt. Ersätt värdena *accountname* och *accountkey* med ditt kontonamn och din nyckel.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Kör exemplet

Exemplet skapar en behållare i Blob Storage, skapar en ny BLOB i behållaren, listar blobarna i behållaren och laddar ned blobben till en lokal fil.

Kör exemplet. Här är ett exempel på utdata från att köra programmet:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

När du trycker på RETUR för att fortsätta tar exempel programmet bort lagrings behållaren och den lokala filen. Innan du fortsätter kontrollerar du den hämtade filen i mappen *dokument* .

Du kan också använda [Azure Storage Explorer](https://storageexplorer.com) för att visa filerna i ditt lagrings konto. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot.

När du har verifierat filerna trycker du på RETUR-tangenten för att ta bort testfilerna och avsluta demonstrationen. Öppna filen *example. RB* och titta på koden.

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempel koden så att du förstår hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten

Det första du ska göra är att skapa instanser av de objekt som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra. Varje objekt används av nästa i listan.

- Skapa en instans av [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) av Azure Storage för att konfigurera autentiseringsuppgifter för anslutning.
- Skapa [behållarobjektet](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) som representerar den behållare som du har åtkomst till. Containrar används för att organisera dina blobar på samma sätt som du använder mappar på datorn för att organisera dina filer.

När du har behållar objektet kan du skapa ett [block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) -BLOB-objekt som pekar på en angiven blob som du är intresse rad av. Använd [block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) -objektet för att skapa, ladda ned och kopiera blobbar.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om behållare och blob-namn finns i [namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Följande exempel kod:

- Skapar en ny behållare
- Anger behörigheter för behållaren så att blobarna är offentliga. Behållaren heter *quickstartblobs* med ett unikt ID som lagts till.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Skapa en BLOB i behållaren

Blob Storage stöder block-blobbar, tillägg av blobbar och sid-blobar. Om du vill skapa en BLOB anropar du [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) -metoden genom att skicka in data för blobben.

I följande exempel skapas en blob med namnet *QuickStart_* med ett unikt ID och fil namns tillägget *. txt* i behållaren som du skapade tidigare.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

Block blobbar kan vara så stora som 4,7 TB och kan vara allt från kalkyl blad till stora videofiler. Page blobbar används främst för VHD-filer som backar upp virtuella IaaS-datorer. Lägg till blobbar används ofta för loggning, till exempel när du vill skriva till en fil och sedan fortsätta lägga till mer information.

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Hämta en lista över filer i containern med hjälp av metoden [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method). Följande kod hämtar listan över blobbar och visar sedan deras namn.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Ladda ned en blob

Ladda ned en blob till din lokala disk med hjälp av metoden [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) . Följande kod laddar ned bloben som skapades i ett föregående avsnitt.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Rensa resurser

Om en BLOB inte längre behövs kan du använda [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) för att ta bort den. Ta bort en hel behållare med hjälp av metoden [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) . Om du tar bort en behållare raderas även eventuella blobbar som lagras i behållaren.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Resurser för utveckling av Ruby-program med blobbar

Se följande ytterligare resurser för ruby-utveckling:

- Visa och ladda ned [Ruby-klientbibliotekets källkod](https://github.com/Azure/azure-storage-ruby) för Azure Storage på GitHub.
- Utforska [Azure-exempel](/samples/browse/?products=azure&languages=ruby) som skrivits med hjälp av ruby-klient biblioteket.
- [Exempel: Komma igång med Azure Storage i ruby](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du överför filer mellan Azure Blob Storage och en lokal disk med hjälp av ruby. Om du vill veta mer om hur du arbetar med Blob Storage kan du fortsätta till översikten över lagrings kontot.

> [!div class="nextstepaction"]
> [Översikt över lagringskonto](../common/storage-account-overview.md)

Mer information om Storage Explorer och blobbar finns i [Hantera Azure Blob Storage-resurser med Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
