---
title: Snabbstart – Skapa ett Azure Stream Analytics jobb med hjälp av Azure CLI
description: Den här snabbstarten visar hur du använder Azure CLI för att skapa Azure Stream Analytics jobb.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: 58dccf56cd493782a422b0ddf0386e31d4d87daf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765997"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Snabbstart: Skapa ett Azure Stream Analytics jobb med hjälp av Azure CLI

I den här snabbstarten använder du Azure CLI för att definiera ett Stream Analytics-jobb som filtrerar sensormeddelanden i realtid med en temperatur som är större än 27. Ditt Stream Analytics läser data från IoT Hub, transformerar data och skriver tillbaka data till en container i Blob Storage. De indata som används i den här snabbstarten genereras av en Raspberry Pi-onlinesimulator.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Skapa en resursgrupp. Alla Azure-resurser måste distribueras till en resursgrupp. Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.

   I den här snabbstarten skapar du en resursgrupp med namnet *streamanalyticsrg* på *platsen eastus* med följande [az group create-kommando:](/cli/azure/group#az_group_create)

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>Förbereda indata

Innan du definierar Stream Analytics jobb förbereder du de data som används för jobbets indata.

Följande Kodblock i Azure CLI är kommandon som förbereder de indata som krävs för jobbet. Läs avsnitten för att förstå koden.

1. Skapa en IoT-hubb med hjälp av kommandot [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub). I det här exemplet skapas en IoT-hubb som heter **MyASAIoTHub**. Eftersom namn på IoT-hubbar är unika behöver du skapa ett eget IoT-hubbnamn. Ange SKU:n till F1 för att använda den kostnadsfria nivån om den är tillgänglig med din prenumeration. Annars väljer du nästa lägsta nivå.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    När IoT-hubben har skapats hämtar du anslutningssträngen för IoT-hubben med hjälp av kommandot [az iot hub show-connection-string](/cli/azure/iot/hub). Kopiera hela anslutningssträngen och spara den för användning när du lägger till IoT-hubben som indata i ditt Stream Analytics-jobb.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Lägg till en enhet i IoT-hubben med hjälp av kommandot [az iothub device-identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). I det här exemplet skapas en enhet med namnet **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Hämta enhetsanslutningssträngen med hjälp av kommandot [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string). Kopiera hela anslutningssträngen och spara den för användning när du skapar Raspberry Pi-simulatorn.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Utdataexempel:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Skapa ett Blob Storage-konto

Följande Azure CLI-kodblock skapar ett bloblagringskonto som används för jobbutdata. Läs avsnitten för att förstå koden.

1. Skapa ett allmänt lagringskonto med kommandot [az storage account create](/cli/azure/storage/account). Det allmänna lagringskontot kan användas för alla fyra tjänsterna: blobar, filer, tabeller och köer.

   Kom ihåg att ersätta platshållarvärden inom vinkelparenteser med dina egna värden:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Hämta nyckeln för ditt lagringskonto genom att köra [kommandot az storage account keys list.](/cli/azure/storage/account/keys) Spara den här nyckeln för användning i nästa steg.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Skapa en behållare för att lagra blobar med kommandot [az storage container create](/cli/azure/storage/container). Du använder lagringskontonyckeln för att auktorisera åtgärden för att skapa containern. Mer information om hur du auktoriserar dataåtgärder med Azure CLI finns i [Auktorisera åtkomst till blob- eller ködata med Azure CLI.](../storage/blobs/authorize-data-operations-cli.md)

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Följande Azure CLI-kodblock skapar ett Stream Analytics jobb. Läs avsnitten för att förstå koden

1. Skapa ett Stream Analytics jobb med [kommandot az stream-analytics job create.](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create)

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>Konfigurera indata för jobbet

Lägg till indata i jobbet med hjälp av [cmdleten az stream-analytics input.](/cli/azure/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create) Den här cmdleten tar jobbnamnet, jobbindatanamnet, resursgruppens namn och jobbindatadefinitionen som parametrar. Jobbindatadefinitionen är en JSON-fil som innehåller de egenskaper som krävs för att konfigurera jobbets indata. I det här exemplet skapar du en IoT Hub som indata.

Skapa en fil med namnet `datasource.json` på den lokala datorn och lägg till följande JSON-data i den. Ersätt värdet för `sharedAccessPolicyKey` med `SharedAccessKey`-delen i den anslutningssträng för IoT Hub som du sparade i ett tidigare avsnitt.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

Skapa en fil med namnet `serialization.json` på den lokala datorn och lägg till följande JSON-data i den.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Kör sedan cmdleten `az stream-analytics input create`. Ersätt värdet för variabeln med sökvägen där du har lagrat JSON-filen för jobbindatadefinitionen och värdet för variabeln med sökvägen där du har lagrat `datasource` `serialization` JSON-filen för serialisering.

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>Konfigurera utdata för jobbet

Lägg till utdata i jobbet med hjälp av [cmdleten az stream-analytics output create.](/cli/azure/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create) Den här cmdleten tar jobbnamnet, jobbutdatanamnet, resursgruppens namn och jobbutdatadefinitionen som parametrar. Jobbutdatadefinitionen är en JSON-fil som innehåller de egenskaper som krävs för att konfigurera jobbets utdata. Det här exemplet använder bloblagring som utdata.

Skapa en fil med namnet `datasink.json` på den lokala datorn och lägg till följande JSON-data i den. Ersätt värdet för med lagringskontots åtkomstnyckel som är `accountKey` det värde som lagras i $storageAccountKey värdet.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Kör sedan cmdleten `az stream-analytics output`. Ersätt värdet för variabeln med sökvägen där du har lagrat JSON-filen för jobbdefinitionsdefinitionen och värdet för variabeln med sökvägen där du har lagrat `datasource` `serialization` JSON-filen för serialisering.

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

Lägg till en transformering för jobbet med hjälp [av cmdleten az stream-analytics transformation create.](/cli/azure/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create) Den här cmdleten tar jobbnamnet, jobbtransformationsnamnet, resursgruppens namn och jobbtransformationsdefinitionen som parametrar. 

Kör `az stream-analytics transformation create` cmdleten .

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>Köra IoT-simulatorn

1. Öppna [Raspberry Pi Azure IoT-onlinesimulatorn](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Ersätt platshållaren på rad 15 med hela Azure IoT Hub-enhetens anslutningssträng, som du sparade i ett tidigare avsnitt.

3. Klicka på **Kör**. Utdata bör visas de sensordata och meddelanden som skickas till din IoT-hubb.

    ![Raspberry Pi Azure IoT-onlinesimulator](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

Starta jobbet med hjälp av [cmdleten az stream-analytics job start.](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start) Den här cmdleten tar jobbnamnet, resursgruppens namn, startläget för utdata och starttiden som parametrar. `OutputStartMode` godkänner värden för `JobStartTime`, `CustomTime` eller `LastOutputEventTime`.

När du har kört följande cmdlet returnerar den `True` som utdata om jobbet startar. En utdatamapp skapas i lagringscontainern med de data som har transformerats.

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du låta bli att ta bort det och stoppa det just nu. Om du inte planerar att fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten genom att köra följande cmdlet:

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett enkelt Stream Analytics jobb med hjälp av Azure CLI. Du kan även distribuera Stream Analytics-jobb med [Azure-portalen](stream-analytics-quick-create-portal.md) och [Visual Studio](stream-analytics-quick-create-vs.md).

Om du vill se hur du konfigurerar andra indatakällor och utför realtidsidentifiering fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Identifiering av bedrägerier i realtid med hjälp av Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)