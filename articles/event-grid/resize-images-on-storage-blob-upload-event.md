---
title: 'Självstudie: använda Azure Event Grid för att automatisera storleks ändring av överförda bilder'
description: 'Självstudie: Azure Event Grid kan utlösa vid BLOB-uppladdningar i Azure Storage. Du kan använda det här till att skicka bildfiler som laddats upp till Azure Storage till andra tjänster, som Azure Functions, för storleksändring och andra förbättringar.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.openlocfilehash: ca231fc65162fe38f4dcb8b8d5677ef42c7807bb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550528"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Självstudie: automatisera storleks ändring av överförda bilder med hjälp av Event Grid

[Azure Event Grid](overview.md) är en händelsetjänst för molnet. Med Event Grid kan du skapa prenumerationer på händelser som genereras av Azure-tjänster eller resurser från tredje part.  

Den här självstudien är del två i en serie med Storage-självstudier. Den bygger vidare på [föregående Storage-självstudie][previous-tutorial] och lägger till serverfri och automatisk generering av miniatyrbilder med Azure Event Grid och Azure Functions. Event Grid gör att [Azure Functions](../azure-functions/functions-overview.md) kan svara på [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-händelser och generera miniatyrbilder av bilder som laddats upp. En händelseprenumeration skapas mot skapandehändelsen i Blob Storage. När en blob läggs till i en viss Blob-lagringscontainer anropas en funktionsslutpunkt. Data som skickas till funktionsbindningen från Event Grid används till att få åtkomst till bloben och generera miniatyrbilden.

Du kan använda Azure CLI och Azure-portalen till att lägga till funktionen för storleksändring i en befintlig app för uppladdning av bilder.

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

![Skärm bild som visar en publicerad webbapp i en webbläsare för \. net V12 SDK.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

![Skärm bild som visar en publicerad webbapp i en webbläsare för \. net v10 SDK.](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure Storage-konto
> * Distribuera serverfri kod med Azure Functions
> * Skapa en prenumeration på en Blob Storage-händelse i Event Grid

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att slutföra den här kursen behöver du:

Du måste ha slutfört föregående Blob Storage-självstudie: [Överför bilddata i molnet med Azure Storage][previous-tutorial].

Du behöver en [Azure-prenumeration](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). Den här själv studie kursen fungerar inte med den **kostnads fria** prenumerationen. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0.14 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du inte använder Cloud Shell måste du först logga in med `az login`.

Om du inte redan har registrerat resursprovidern Event Grid i din prenumeration kontrollerar du att den är registrerad.

```bash
az provider register --namespace Microsoft.EventGrid
```

```powershell
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

För Azure Functions krävs ett allmänt lagringskonto. Förutom Blob Storage-kontot du skapade i den tidigare självstudien, skapar du ett separat allmänt lagringskonto i resursgruppen med hjälp av kommandot [az storage account create](/cli/azure/storage/account). Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

1. Ange en variabel som ska innehålla namnet på resursgruppen som du skapade i den föregående självstudien.

    ```bash
    resourceGroupName="myResourceGroup"
    ```

    ```powershell
    $resourceGroupName="myResourceGroup"
    ```

1. Ange en variabel som ska innehålla platsen för resurser som ska skapas. 

    ```bash
    location="eastus"
    ```

    ```powershell
    $location="eastus"
    ```

1. Ange en variabel för namnet på det nya lagringskonto som Azure Functions kräver.

    ```bash
    functionstorage="<name of the storage account to be used by the function>"
    ```

    ```powershell
    $functionstorage="<name of the storage account to be used by the function>"
    ```

1. Skapa lagringskonto för Azure-funktionen.

    ```bash
    az storage account create --name $functionstorage --location $location \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

    ```powershell
    az storage account create --name $functionstorage --location $location `
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>Skapa en funktionsapp  

Du måste ha en funktionsapp som värd för körning av funktionen. Funktionsappen är en miljö för serverfri körning av funktionskoden. Skapa en funktionsapp med kommandot [az functionapp create](/cli/azure/functionapp).

I följande kommando anger du ditt unika namn på funktionsappen. Funktionsappens namn används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure.

1. Ange ett namn för funktionsappen som ska skapas.

    ```bash
    functionapp="<name of the function app>"
    ```

    ```powershell
    $functionapp="<name of the function app>"
    ```

1. Skapa Azure-funktionen.

    ```bash
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location $location \
      --functions-version 2
    ```

    ```powershell
    az functionapp create --name $functionapp --storage-account $functionstorage `
      --resource-group $resourceGroupName --consumption-plan-location $location `
      --functions-version 2
    ```

Nu ska du konfigurera Function-appen så att den ansluter till Blob Storage-kontot som du skapade i [föregående självstudie][previous-tutorial].

## <a name="configure-the-function-app"></a>Konfigurera funktionsappen

Funktionen behöver autentiseringsuppgifter för Blob Storage-kontot. Dessa läggs till i programinställningarna för funktionsappen med kommandot [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

```bash
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

```powershell
$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails `
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

```bash
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName `
  -n $blobStorageAccount --query [0].value --output tsv)

$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails `
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey `
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

Inställningen `FUNCTIONS_EXTENSION_VERSION=~2` får funktionsappen att köra version 2.x av Azure Functions-körningen.

Nu kan du distribuera ett funktionskodprojekt till den här funktionsappen.

## <a name="deploy-the-function-code"></a>Distribuera funktionskoden 

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

Storleksändringsfunktionen för C#-exemplet finns tillgängligt på [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Distribuera kodprojektet till funktionsappen med kommandot [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```bash
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

```powershell
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Storleksändringsfunktionen för exemplet Node.js är tillgängligt på [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10). Distribuera funktionskodprojektet till funktionsappen med kommandot [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```bash
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

```powershell
az functionapp deployment source config --name $functionapp `
  --resource-group $resourceGroupName --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

---

Funktionen för att ändra bildstorlek utlöses av HTTP-förfrågningar som skickats till den från tjänsten Event Grid. Du kan ange för Event Grid att du vill hämta meddelandena via funktionens URL genom att skapa en händelseprenumeration. För den här självstudien prenumererar du på blob-skapade händelser.

Data som skickas till funktionen från Event Grid-meddelandet inkluderar blobens URL. URL:en skickas sedan till indatabindningen för att hämta den uppladdade avbildningen från Blob Storage. Funktionen genererar en miniatyrbild och skriver den resulterande dataströmmen till en separat container i Blob Storage.

I det här projektet används `EventGridTrigger` som typ av utlösare. Det är bättre att använda Event Grid-utlösaren än någon allmän HTTP-utlösare. Event Grid verifierar automatiskt Event Grid Function-utlösare. Med allmänna HTTP-utlösare måste du implementera [verifieringssvaret](security-authentication.md).

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

Mer information om den här funktionen finns i [filerna function.json och run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Mer information om den här funktionen finns i [filerna function.json och index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

Funktionsprojektkoden distribueras direkt från den offentliga exempeldatabasen. Mer information om distributionsalternativen för Azure Functions finns i [Kontinuerlig distribution för Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Skapa en händelseprenumeration

En händelseprenumeration anger vilka provider-genererade händelser du vill skicka till en viss slutpunkt. I det här fallet exponeras slutpunkten av din funktion. Använd följande steg till att skapa en händelseprenumeration som skickar meddelanden till din funktion i Azure-portalen:

1. I [Azure Portal](https://portal.azure.com)längst upp på sidan söker du efter och väljer `Function App` den Function-app som du nyss skapade. Välj **Functions** och välj **miniatyr** funktionen.

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="Välj miniatyr funktionen i portalen":::

1.  Välj Välj **integrering** och välj sedan **Event Grid utlösare** och välj **Skapa event Grid prenumeration**.

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="Navigera för att lägga till Event Grid prenumeration i Azure Portal" :::

1. Använd de inställningar för händelseprenumerationen som anges i tabellen.
    
    ![Skapa händelseprenumeration från funktionen i Azure-portalen](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **Namn** | imageresizersub | Namn som identifierar din nya händelseprenumeration. |
    | **Typ av ämne** | Lagringskonton | Välj händelseprovidern för Storage-kontot. |
    | **Prenumeration** | Din Azure-prenumeration | Som standard välj den aktuella Azure-prenumerationen. |
    | **Resursgrupp** | myResourceGroup | Välj **Använd befintlig** och välj den resursgrupp du har använt i den här självstudien. |
    | **Resurs** | Ditt Blob Storage-konto | Välj det Blob Storage-konto du skapade. |
    | **Namn på system ämne** | imagestoragesystopic | Ange ett namn på system ämne. Information om system ämnen finns i [Översikt över system avsnitt](system-topics.md). |    
    | **Händelsetyper** | Blob skapas | Avmarkera alla typer utom **Blob skapas**. Det är bara händelsetyper för `Microsoft.Storage.BlobCreated` som skickas till funktionen. |
    | **Slut punkts typ** | genereras automatiskt | Fördefinierad som **Azure Function**. |
    | **Slutpunkt** | genereras automatiskt | Namnet på funktionen. I det här fallet är det **miniatyr bilden**. |

1. Växla till fliken **filter** och utför följande åtgärder:
    1. Välj alternativet **Aktivera ämnesfiltrering**.
    1. För **ämne börjar med** anger du följande värde: **/blobServices/default/containers/images/**.

        ![Ange filter för händelseprenumeration](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. Välj **Skapa** för att lägga till händelseprenumerationen. Detta skapar en händelse prenumeration som utlöser `Thumbnail` funktionen när en BLOB läggs till i `images` behållaren. Funktionen ändrar storlek på avbildningarna och lägger till dem till containern `thumbnails`.

Nu när tjänsterna på serversidan har konfigurerats ska du testa funktionen för storleksändring i exempelwebbappen.

## <a name="test-the-sample-app"></a>Testa exempelappen

När du ska testa storleksändring i webbappen bläddrar du till URL-adressen för din publicerade app. Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

Klicka på regionen **Upload photos** (Ladda upp foton) för att välja och ladda upp en fil. Du kan också dra ett foto till den här regionen.

Observera att när den överförda bilden försvinner visas en kopia av den uppladdade bilden i karusellen för **genererade miniatyrer** . Den här bildens storlek ändrades av funktionen. Därefter lades den till i containern med *miniatyrer* och laddades ned av webbklienten.

![Skärm bild som visar en publicerad webbapp med namnet "ImageResizer" i en webbläsare för \. net V12 SDK.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Klicka på **Välj fil** för att välja en fil och klicka sedan på **Ladda upp bild**. När uppladdningen är klar navigerar webbläsaren till en sida som visar att åtgärden lyckades. Klicka på länken för att återgå till startsidan. En kopia av den överförda bilden visas i det **skapade miniatyr bilders** fältet. (Om bilden inte visas överst försöker du att läsa in sidan igen.) Den här bildens storlek ändrades av funktionen, läggs till i behållaren för *miniatyr bilder* och laddades ned av webb klienten.

![Publicerad webbapp i webbläsaren](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett allmänt Azure Storage-konto
> * Distribuera serverfri kod med Azure Functions
> * Skapa en prenumeration på en Blob Storage-händelse i Event Grid

Gå vidare till del tre i Storage-självstudien om du vill lära dig om säker åtkomst till lagringskontot.

> [!div class="nextstepaction"]
> [Säker åtkomst till programdata i molnet](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Mer information om Event Grid finns i [En introduktion till Azure Event Grid](overview.md).
+ Om du vill prova en annan självstudie om Azure Functions kan du läsa [Skapa en funktion som kan integreras med Azure Logic Apps](../azure-functions/functions-twitter-email.md).

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
