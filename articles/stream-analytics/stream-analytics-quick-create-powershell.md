---
title: Snabb start – skapa ett Stream Analytics jobb med Azure PowerShell
description: Den här snabbstarten demonstrerar hur du använder Azure PowerShell-modulen för att distribuera och köra ett Azure Stream Analytics-jobb.
author: enkrumah
ms.author: ebnkruma
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 377abef31dbc4364f37161194923bbf74d272d80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012299"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Snabb start: skapa ett Stream Analytics jobb med Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser med hjälp av PowerShell-cmdletar eller -skript. Den här snabbstarten beskriver hur du använder Azure PowerShell-modulen för att distribuera och köra ett Azure Stream Analytics-jobb.

Exempeljobbet läser strömmande data från en IoT Hub-enhet. Indata genereras av en Raspberry Pi-onlinesimulator. Därefter transformerar Stream Analytics-jobbet data med hjälp av Stream Analytics-frågespråket att filtrera meddelanden med en temperatur över 27°. Slutligen skriver den resulterande utdatahändelser till en fil i Blob Storage.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto.](https://azure.microsoft.com/free/)

* Den här snabbstarten kräver Azure PowerShell-modulen. Hitta versionen som är installerad på den lokala datorn genom att köra `Get-Module -ListAvailable Az`. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

* Vissa IoT Hub åtgärder stöds inte av Azure PowerShell och måste slutföras med hjälp av Azure CLI version 2.0.70 eller senare och IoT-tillägget för Azure CLI. [Installera Azure CLI](/cli/azure/install-azure-cli) och använd `az extension add --name azure-iot` för att installera IoT-tillägget.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och ange dina autentiseringsuppgifter för Azure i webbläsarens popup-fönster:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Om du har fler än en prenumeration väljer du den prenumeration som du vill använda för den här snabbstarten genom att köra följande cmdletar. Ersätt `<your subscription name>` med namnet på prenumerationen:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>Förbereda indata

Innan du definierar Stream Analytics-jobbet förbereder du de data som har konfigurerats som indata för jobbet.

Följande Azure CLI-kodblock utför många kommandon som förbereder de indata som krävs för jobbet. Läs avsnitten för att förstå koden.

1. I PowerShell-fönstret kör du kommandot [az login](/cli/azure/authenticate-azure-cli) för att logga in på ditt Azure-konto.

    När du har loggat in returnerar Azure CLI en lista över dina prenumerationer. Kopiera den prenumeration som du använder för den här snabbstarten och kör kommandot [az account set](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription) för att välja den prenumerationen. Välj samma prenumeration som du valde i föregående avsnitt med PowerShell. Ersätt `<your subscription name>` med namnet på prenumerationen.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Skapa en IoT-hubb med hjälp av kommandot [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub). I det här exemplet skapas en IoT-hubb som heter **MyASAIoTHub**. Eftersom namn på IoT-hubbar är unika behöver du skapa ett eget IoT-hubbnamn. Ange SKU:n till F1 för att använda den kostnadsfria nivån om den är tillgänglig med din prenumeration. Annars väljer du nästa lägsta nivå.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    När IoT-hubben har skapats hämtar du anslutningssträngen för IoT-hubben med hjälp av kommandot [az iot hub show-connection-string](/cli/azure/iot/hub). Kopiera hela anslutningssträngen och spara den för användning när du lägger till IoT-hubben som indata i ditt Stream Analytics-jobb.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Lägg till en enhet i IoT-hubben med hjälp av kommandot [az iothub device-identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). I det här exemplet skapas en enhet med namnet **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Hämta enhetsanslutningssträngen med hjälp av kommandot [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string). Kopiera hela anslutningssträngen och spara den för användning när du skapar Raspberry Pi-simulatorn.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Utdataexempel:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>Skala bloblagring

Följande Azure PowerShell-kodblock använder kommandon för att skapa bloblagring som används för jobbutdata. Läs avsnitten för att förstå koden.

1. Skapa ett allmänt lagringskonto för generell användning med hjälp av cmdleten [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount).  I det här exemplet skapas ett lagringskonto med namnet **myasaquickstartstorage** med lokalt redundant lagring (LRS) och blobkryptering (aktiverat som standard).

2. Hämta lagringskontokontexten `$storageAccount.Context` som definierar det lagringskonto som ska användas. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger.

3. Skapa en lagringscontainer med hjälp av [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer).

4. Kopiera den lagringsnyckel som koden ger som utdata och spara nyckeln för att skapa utdata för strömningsjobbet senare.

    ```powershell
    $storageAccountName = "myasaquickstartstorage"
    $storageAccount = New-AzStorageAccount `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName `
      -Location $location `
      -SkuName Standard_LRS `
      -Kind Storage

    $ctx = $storageAccount.Context
    $containerName = "container1"

    New-AzStorageContainer `
      -Name $containerName `
      -Context $ctx

    $storageAccountKey = (Get-AzStorageAccountKey `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName).Value[0]

    Write-Host "The <storage account key> placeholder needs to be replaced in your output json files with this key value:"
    Write-Host $storageAccountKey -ForegroundColor Cyan
    ```

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Skapa ett Stream Analytics-jobb med cmdleten [New-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/new-azstreamanalyticsjob). Den här cmdleten tar jobbnamnet, resursgruppens namn och jobbdefinitionen som parametrar. Jobbnamnet kan vara ett eget namn som identifierar jobbet. Det får enbart innehålla alfanumeriska tecken, bindestreck och understreck och måste vara mellan 3 och 63 tecken långt. Jobbdefinitionen är en JSON-fil som innehåller de egenskaper som krävs för att skapa ett jobb. Skapa en fil med namnet `JobDefinition.json` på den lokala datorn och lägg till följande JSON-data i den:

```json
{
  "location":"WestUS2",
  "properties":{
    "sku":{
      "name":"standard"
    },
    "eventsOutOfOrderPolicy":"adjust",
    "eventsOutOfOrderMaxDelayInSeconds":10,
    "compatibilityLevel": 1.1
  }
}
```

Kör sedan cmdleten `New-AzStreamAnalyticsJob`. Ersätt värdet för variabeln `jobDefinitionFile` med den sökväg där du har sparat jobbdefinitionens JSON-fil.

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force
```

## <a name="configure-input-to-the-job"></a>Konfigurera indata för jobbet

Lägg till indata för jobbet med hjälp av cmdleten [New-AzStreamAnalyticsInput](/powershell/module/az.streamanalytics/new-azstreamanalyticsinput). Den här cmdleten tar jobbnamnet, jobbindatanamnet, resursgruppens namn och jobbindatadefinitionen som parametrar. Jobbets indatadefinition är en JSON-fil som innehåller de egenskaper som krävs för att konfigurera jobbets indata. I det här exemplet skapar du en bloblagring som indata.

Skapa en fil med namnet `JobInputDefinition.json` på den lokala datorn och lägg till följande JSON-data i den. Ersätt värdet för `accesspolicykey` med `SharedAccessKey`-delen i den anslutningssträng för IoT Hub som du sparade i ett tidigare avsnitt.

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Devices/IotHubs",
            "properties": {
                "iotHubNamespace": "MyASAIoTHub",
                "sharedAccessPolicyName": "iothubowner",
                "sharedAccessPolicyKey": "accesspolicykey",
                "endpoint": "messages/events",
                "consumerGroupName": "$Default"
                }
        },
        "compression": {
            "type": "None"
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "IoTHubInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Kör sedan cmdleten `New-AzStreamAnalyticsInput` och ersätt värdet för variabeln `jobDefinitionFile` med den sökväg där du har sparat JSON-filen med definition för jobbindata.

```powershell
$jobInputName = "IoTHubInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName
```

## <a name="configure-output-to-the-job"></a>Konfigurera utdata för jobbet

Lägg till utdata för jobbet med hjälp av cmdleten [New-AzStreamAnalyticsOutput](/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput). Den här cmdleten tar jobbnamnet, jobbutdatanamnet, resursgruppens namn och jobbutdatadefinitionen som parametrar. Jobbets utdatadefinition är en JSON-fil som innehåller de egenskaper som krävs för att konfigurera jobbets utdata. Det här exemplet använder bloblagring som utdata.

Skapa en fil med namnet `JobOutputDefinition.json` på den lokala datorn och lägg till följande JSON-data i den. Ersätt värdet för `accountKey` med åtkomstnyckeln för ditt lagringskonto, vilket är det värde som lagras i $storageAccountKey.

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "asaquickstartstorage",
                      "accountKey": "<storage account key>"
                    }
                ],
                "container": "container1",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "BlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Kör sedan cmdleten `New-AzStreamAnalyticsOutput`. Ersätt värdet för variabeln `jobOutputDefinitionFile` med sökvägen där du har sparat jobbdefinitionens JSON-fil för utdata.

```powershell
$jobOutputName = "BlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force
```

## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

Lägg till en transformation för jobbet med hjälp av cmdleten [New-AzStreamAnalyticsTransformation](/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation). Den här cmdleten tar jobbnamnet, jobbtransformationsnamnet, resursgruppens namn och jobbtransformationsdefinitionen som parametrar. Skapa en fil med namnet `JobTransformationDefinition.json` på den lokala datorn och lägg till följande JSON-data i den. JSON-filen innehåller en frågeparameter som definierar transformationsfrågan:

```json
{
    "name":"MyTransformation",
    "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",
    "properties":{
        "streamingUnits":1,
        "script":null,
        "query":" SELECT * INTO BlobOutput FROM IoTHubInput HAVING Temperature > 27"
    }
}
```

Kör sedan cmdleten `New-AzStreamAnalyticsTransformation`. Ersätt värdet för variabeln `jobTransformationDefinitionFile` med den sökväg där du har sparat JSON-filen med definition för jobbtransformering.

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="run-the-iot-simulator"></a>Köra IoT-simulatorn

1. Öppna [Raspberry Pi Azure IoT-onlinesimulatorn](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Ersätt platshållaren på rad 15 med hela Azure IoT Hub-enhetens anslutningssträng, som du sparade i ett tidigare avsnitt.

3. Klicka på **Kör**. Utdata bör visas de sensordata och meddelanden som skickas till din IoT-hubb.

    ![Raspberry Pi Azure IoT-onlinesimulator](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

Starta jobbet med hjälp av cmdleten [Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob). Den här cmdleten tar jobbnamnet, resursgruppens namn, startläget för utdata och starttiden som parametrar. `OutputStartMode` godkänner värden för `JobStartTime`, `CustomTime` eller `LastOutputEventTime`. Mer information om vad dessa värden refererar till finns i avsnittet om [parametrar](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) i PowerShell-dokumentationen.

När du har kört följande cmdlet returnerar den `True` som utdata om jobbet startar. En utdatamapp skapas i lagringscontainern med de data som har transformerats.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du låta bli att ta bort det och stoppa det just nu. Om du inte planerar att fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten genom att köra följande cmdlet:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett enkelt Stream Analytics-jobb med PowerShell. Du kan även distribuera Stream Analytics-jobb med [Azure-portalen](stream-analytics-quick-create-portal.md) och [Visual Studio](stream-analytics-quick-create-vs.md).

Om du vill se hur du konfigurerar andra indatakällor och utför realtidsidentifiering fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Identifiering av bedrägerier i realtid med hjälp av Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)