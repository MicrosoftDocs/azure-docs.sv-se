---
title: Azure snabbstart – Skapa en blob i objektlagring med hjälp av PHP | Microsoft Docs
description: Lär dig snabbt att överföra objekt till och från Azure Blob Storage med hjälp av PHP. Ladda upp, ladda ned och lista block blobbar i en behållare i Azure Blob Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: af35927abfa491891dffe2c2397d6daf8c1ccde2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95543127"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>Överföra objekt till och från Azure Blob Storage med hjälp av PHP
I den här snabbstarten får du lära dig att använda PHP för att ladda upp, ladda ned och lista blockblobar i en container i Azure Blob-lagring. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Kontrollera att du har följande ytterligare krav installerade:

* [PHP](https://php.net/downloads.php)
* [Azure Storage SDK för PHP](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet
[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) som används i den här snabbstarten är ett enkelt PHP-program.  

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Om du vill öppna PHP-exempelprogrammet ska du leta efter mappen storage-blobs-php-quickstart och öppna filen phpqs.php.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
I programmet måste du ange ditt lagringskontonamn och kontonyckeln för att skapa **BlobRestProxy**-instansen för programmet. Vi rekommenderar att du lagrar de här identifierarna inom en miljövariabel på den lokala datorn där programmet körs. Använd något av följande exempel beroende på vilket operativsystem som används för att skapa miljövariabeln. Ersätt värdena **youraccountname** och **youraccountkey** med ditt kontonamn och din nyckel.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>Konfigurera din miljö
Flytta mappen från din lokala git-mapp och placera den i en katalog som hanteras av PHP-servern. Öppna sedan en kommandotolk som har begränsats till samma katalog och ange: `php composer.phar install`

## <a name="run-the-sample"></a>Kör exemplet
Det här exemplet skapar en testfil i mappen ”.”. Exempelprogrammet laddar upp testfilen till Blob Storage, listar blobarna i containern och laddar ned filen med ett nytt namn. 

Kör exemplet. Följande utdata är ett exempel på utdata som returneras när programmet körs:
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
När du trycker på tangenten som visas för att fortsätta tar exempelprogrammet bort containern och filerna. Kontrollera att de två filerna finns i serverns mapp innan du fortsätter. Du kan öppna dem och se att de är identiska.

Du kan också använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com) för att visa filerna i blobblagringen. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot. 

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen example.rb och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten
Det första du ska göra är att skapa referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra, och vart och ett används av nästa i listan.

* Skapa en instans av **BlobRestProxy** av Azure Storage för att konfigurera autentiseringsuppgifter för anslutning. 
* Skapa objektet **BlobService**, som pekar mot Blob Service i lagringskontot. 
* Skapa objektet **Container**, som representerar den behållare du får åtkomst till. Containrar används för att organisera dina blobar på samma sätt som du använder mappar på datorn för att organisera dina filer.

När du har containerobjektet **blobClient** kan du skapa blobobjektet **Block** som pekar mot den specifika blob du är intresserad av. Du kan sedan utföra åtgärder som att ladda upp, ladda ned och kopiera.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om containrar och blobnamn finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

I det här avsnittet skapar du en instans av Azure Storage-klienten samt av blobtjänstobjektet, skapar en ny container och anger behörigheter för containern så att blobarna är offentliga. Containern heter **quickstartblobs**. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i containern

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är vanligast och används i denna snabbstart.  

Om du vill överföra en fil till en blob hämtar du filens fullständiga sökväg genom att slå ihop katalognamnet och filnamnet på den lokala enheten. Du kan sedan ladda upp filen till angiven sökväg med hjälp av metoden **createblockblob()**. 

Exempelkoden tar en lokal fil och laddar upp den till Azure. Filen lagras som **myfile** och namnet på bloben som **fileToUpload** i koden. I följande exempel överförs filen till containern med namnet **quickstartblobs**.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Om du vill utföra en deluppdatering av innehållet i en blockblob använder du metoden **createblocklist()**. Blockblobar kan vara så stora som 4,7 TB och kan vara allt från Excel-kalkylblad till stora videofiler. Sidblobar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Tilläggsblobar används för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. Tilläggsblobar ska användas i en enskild skrivmodell. De flesta objekt som lagras i Blob Storage är blockblobar.

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Du kan hämta en lista över filer i containern med hjälp av metoden **listBlobs()**. Följande kod hämtar listan över blobar och går sedan igenom dem och visar namnen på de blobar som har påträffats i en container.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Hämta dina blobars innehåll

Hämta innehållet i din blobar med hjälp av metoden **getBlob()**. Följande kod visar innehållet i bloben som laddades upp i föregående avsnitt.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver blobarna som laddades upp i den här snabbstarten kan du ta bort hela behållaren med hjälp av metoden **deleteContainer()**. Om du inte längre behöver filerna som skapades använder du metoden **deleteBlob()** för att ta bort filerna.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Resurser för utveckling av PHP-program med blobbar

Se följande ytterligare resurser för PHP-utveckling med blobblagring:

- Visa, ladda ned och installera [PHP-klientbibliotekets källkod](https://github.com/Azure/azure-storage-php) för Azure Storage på GitHub.
- Utforska [Blobblagringsexempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob) som skrivits med PHP-klientbiblioteket.

## <a name="next-steps"></a>Nästa steg
 
I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med PHP. Om du vill veta mer om hur du kan arbeta med PHP så gå till vårt PHP Developer Center.

> [!div class="nextstepaction"]
> [PHP Developer Center](https://azure.microsoft.com/develop/php/)


Mer information om Storage Explorer och blobar finns i [Hantera Azure Blob Storage-resurser med Storage Explorer ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).