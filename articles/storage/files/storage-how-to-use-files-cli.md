---
title: Snabbstart för hantering av Azure-filresurser med hjälp av Azure CLI
description: I den här snabbstarten lär du dig hur du använder Azure CLI för att hantera Azure Files. Skapa en resursgrupp och ett lagringskonto och skapa och använd sedan en Azure-filresurs.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9aec838aa762cd018a96b2f17e80cc04ea95795d
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738998"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Snabbstart: Skapa och hantera Azure-filresurser med hjälp av Azure CLI
Den här guiden vägleder dig igenom grunderna i att arbeta med [Azure-filresurser](storage-files-introduction.md) med hjälp av Azure CLI. Azure-filresurser är precis som andra filresurser men lagras i molnet och täcks av Azure-plattformen. Azure-filresurser stöder SMB-protokollet (industry standard Server Message Block), NFS-protokollet (Network File System) (förhandsversion) och möjliggör fildelning över flera datorer, program och instanser. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.4 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

- Som standard returnerar Azure CLI-kommandon JavaScript Object Notation (JSON). JSON är standardmetoden för att skicka och ta emot meddelanden från REST API:er. För att underlätta arbetet med JSON-svaren, så använder vissa av exemplen i den här artikeln *frågeparametern* för Azure CLI-kommandona. Den här parametern använder [JMESPath-frågespråket](http://jmespath.org/) för JSON-parsning. Om du vill lära dig mer om hur man hanterar Azure CLI-kommandonas resultat genom att följa JMESPath-frågespråket kan du ta en titt på [JMESPath-självstudiekursen](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Om du inte redan har en Azure-resursgrupp kan du skapa en ny med kommandot [az group create](/cli/azure/group). 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup på* platsen *USA, västra 2:*

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
Ett lagringskonto är en delad lagringspool i vilken du kan distribuera en Azure-filresurs eller andra lagringsresurser, t.ex. blobar eller köer. Ett lagringskonto kan innehålla ett obegränsat antal filresurser. En resurs kan lagra ett obegränsat antal filer, upp till kapacitetsbegränsningen för lagringskontot.

I följande exempel skapas ett lagringskonto med kommandot [az storage account](/cli/azure/storage/account) create. Lagringskontonamn måste vara unika så använd `$RANDOM` för att lägga till ett tal till namnet som gör det unikt.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> Resurser som är större än 5 TiB (upp till 100 TiB per resurs) är bara tillgängliga i lokalt redundanta (LRS) och zonredundant (ZRS) lagringskonton. Om du vill skapa ett geo-redundant (GRS) eller ett geo-zonredundant (GZRS) lagringskonto tar du bort `--enable-large-file-share` parametern .

### <a name="get-the-storage-account-key"></a>Hämta lagringskontonyckeln
Lagringskontonycklar styr åtkomsten till resurser i ett lagringskonto. Nycklarna skapas automatiskt när du skapar ett lagringskonto. Du kan hämta lagringskontonycklarna till lagringskontot med kommandot [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Nu kan du skapa din första Azure-filresurs. Skapa filresurser med hjälp av [kommandot az storage share-rm create.](/cli/azure/storage/share-rm?view=azure-cli-latest&preserve-view=false#az_storage_share_rm_create) I det här exemplet skapas en Azure-filresurs som heter *myshare*: 

```azurecli-interactive
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck (men får inte inledas med bindestreck). Fullständig information om namngivning av filresurser och filer finns i Namnge och referera [till resurser, kataloger, filer och metadata.](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)

## <a name="use-your-azure-file-share"></a>Använda Azure-filresursen
Azure Files har två metoder för att arbeta med filer och mappar i din Azure-filresurs: branschstandardprotokollen [SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) och [fil-REST](/rest/api/storageservices/file-service-rest-api). 

Om du vill montera en filresurs med SMB läser du följande dokument baserat på ditt operativsystem:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Använda en Azure-filresurs med fil-REST-protokollet 
Det är möjligt att arbeta direkt med fil-REST-protokollet (själv skapa REST HTTP-anrop för hand), men det vanligaste sättet att använda fil-REST-protokollet är att använda Azure CLI, [Azure PowerShell-modulen](storage-how-to-use-files-powershell.md)eller ett Azure Storage SDK. Alla ger en bra omser runt fil-REST-protokollet i val av skript-/programmeringsspråk.  

Vi förväntar att de flesta som använder Azure Files vill arbeta med sin Azure-filresurs via SMB-protokollet, eftersom det gör att de kan använda de befintliga programmen och verktygen som de förväntar sig att kunna använda. Men det finns flera anledningar till varför det är fördelaktigt att använda fil-REST-API:et istället för SMB, till exempel:

- Du bläddrar efter filresursen från Azure Bash Cloud Shell (som inte kan montera filresurser via SMB).
- Du utnyttjar serverlösa resurser såsom [Azure Functions](../../azure-functions/functions-overview.md). 
- Du skapar en värdetilläggstjänst som interagerar med många Azure-filresurser, till exempel säkerhetskopiering eller virusgenomsökningar.

I följande exempel visas hur du använder Azure CLI till att ändra din Azure-filresurs med fil-REST-protokollet. 

### <a name="create-a-directory"></a>Skapa en katalog
Om du vill skapa en ny katalog *med namnet myDirectory* i roten av Azure-filresursen använder du [`az storage directory create`](/cli/azure/storage/directory) kommandot :

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Ladda upp en fil
Om du vill visa hur du laddar upp en fil med kommandot skapar du först en fil som ska [`az storage file upload`](/cli/azure/storage/file) laddas upp Cloud Shell den scratch-enheten. I följande exempel ska du skapa och ladda upp filen:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Om du använder Azure CLI lokalt kan du ersätta `~/clouddrive` med en sökväg som finns på din dator.

När du har överfört filen kan du använda kommandot för att [`az storage file list`](/cli/azure/storage/file) kontrollera att filen har laddats upp till azure-filresursen:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Ladda ned en fil
Du kan använda kommandot [`az storage file download`](/cli/azure/storage/file) för att ladda ned en kopia av filen som du laddade upp till den Cloud Shell enheten:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Kopiera filer
En vanlig uppgift är att kopiera filer från en filresurs till en annan filresurs. Om du vill visa den här funktionen skapar du en ny resurs. Kopiera filen du laddade upp till den här nya resursen med kommandot [az storage file copy](/cli/azure/storage/file/copy): 

```azurecli-interactive
otherShareName="myshare2"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $otherShareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Du bör nu se den kopierade filen om du visar filerna i den nya resursen:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Även om kommandot är praktiskt för filflyttningar mellan Azure-filresurser, för migreringar och större dataförflyttningar, rekommenderar vi på `az storage file copy start` `rsync` macOS och Linux `robocopy` och i Windows. `rsync` och `robocopy` använd SMB för att utföra dataförflyttningar i stället för FileREST-API:et.

## <a name="create-and-manage-share-snapshots"></a>Skapa och hantera ögonblicksbilder
Ytterligare en användbar uppgift som du kan göra med en Azure-filresurs är att skapa resursögonblicksbilder. En ögonblicksbild bevarar en kopia vid en viss tidpunkt av en Azure-filresurs. Ögonblicksbilder av resurser liknar vissa av de operativsystemtekniker som du kanske redan är bekant med:

- [Lvm-ögonblicksbilder (Logical Volume Manager)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) för Linux-system.
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) ögonblicksbilder för macOS.
- [tjänsten Volume Shadow Copy (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) för Windows-filsystem, till exempel NTFS och ReFS.
 
Du kan skapa en resursögonblicksbild med hjälp av [`az storage share snapshot`](/cli/azure/storage/share) kommandot :

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Söka i resursögonblicksbilders innehåll
Du kan söka i en resursögonblicksbilds innehåll genom att skicka resursögonblicksbildens tidsstämpel som vi tog i variabeln `$snapshot` till kommandot `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder
Du kan se listan över ögonblicksbilder som du har tagit för din resurs med följande kommando:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Återställ från en resursögonblicksbild
Du kan återställa en fil med hjälp av kommandot `az storage file copy start` som du använde tidigare. Först tar du bort filen SampleUpload.txt som du laddade upp, så att du kan återställa den från ögonblicksbilden:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild
Du kan ta bort en resursögonblicksbild med hjälp av [`az storage share delete`](/cli/azure/storage/share) kommandot . Använd variabeln som innehåller referensen `$SNAPSHOT` till parametern `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar kan du använda kommandot [`az group delete`](/cli/azure/group) för att ta bort resursgruppen och alla relaterade resurser: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

Du kan även ta bort resurser individuellt.
- Så här tar du bort de Azure-filresurser som du skapade för den här artikeln:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Ta bort själva lagringskontot. (Detta tar implicit bort Azure-filresurserna du skapade, och eventuella andra lagringsresurser som du kan ha skapat, som en Azure Blob Storage-container.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Vad är Azure Files?](storage-files-introduction.md)