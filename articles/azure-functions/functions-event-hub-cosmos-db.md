---
title: 'Självstudie: använda Java-funktioner med Azure Cosmos DB och Event Hubs'
description: Den här självstudien visar hur du använder händelser från Event Hubs för att göra uppdateringar i Azure Cosmos DB med hjälp av en funktion som skrivits i Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6be6ee87f760eec01dc9883f0e4ca39f73aaf050
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602503"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Självstudie: skapa en funktion i Java med en Event Hub-utlösare och en Azure Cosmos DB utgående bindning

Den här självstudien visar hur du använder Azure Functions för att skapa en Java-funktion som analyserar en kontinuerlig ström av temperatur-och tryck data. Event Hub-händelser som representerar sensor avläsningar utlöser funktionen. Funktionen bearbetar händelse data och lägger sedan till status poster till en Azure Cosmos DB.

I den här självstudien får du:

> [!div class="checklist"]
> * Skapa och konfigurera Azure-resurser med hjälp av Azure CLI.
> * Skapa och testa Java-funktioner som interagerar med dessa resurser.
> * Distribuera dina funktioner till Azure och övervaka dem med Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien måste du ha följande installerat:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare
- [Azure Functions Core tools](https://www.npmjs.com/package/azure-functions-core-tools) version 2.6.666 eller senare [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!IMPORTANT]
> `JAVA_HOME`Miljövariabeln måste anges till installations platsen för JDK för att slutföra den här självstudien.

Om du föredrar att använda koden för den här självstudien direkt, se exempel lagrings platsen för [Java-Functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) .

## <a name="create-azure-resources"></a>Skapa Azure-resurser

I den här självstudien behöver du följande resurser:

* En resurs grupp som innehåller de andra resurserna
* En Event Hubs namnrymd, händelsehubben och auktoriseringsregel
* Ett Cosmos DB konto, databas och samling
* En Function-app och ett lagrings konto som värd för den

I följande avsnitt visas hur du skapar de här resurserna med hjälp av Azure CLI.

### <a name="set-environment-variables"></a>Ange miljövariabler

Skapa sedan några miljövariabler för namn och plats för de resurser som du ska skapa. Använd följande kommandon och Ersätt `<value>` plats hållarna med värden som du väljer. Värdena bör följa [namngivnings reglerna och begränsningarna för Azure-resurser](/azure/architecture/best-practices/resource-naming). `LOCATION`Använd ett av värdena som skapas av kommandot för variabeln `az functionapp list-consumption-locations` .

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set EVENT_HUB_NAMESPACE=<value>
set EVENT_HUB_NAME=<value>
set EVENT_HUB_AUTHORIZATION_RULE=<value>
set COSMOS_DB_ACCOUNT=<value>
set STORAGE_ACCOUNT=<value>
set FUNCTION_APP=<value>
set LOCATION=<value>
```

---

Resten av den här självstudien använder dessa variabler. Tänk på att dessa variabler endast sparas under den aktuella Azure CLI-eller Cloud Shell-sessionen. Du måste köra dessa kommandon igen om du använder ett annat lokalt terminalfönster eller om tids gränsen för Cloud Shell-sessionen är slut.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure använder resurs grupper för att samla in alla relaterade resurser i ditt konto. På så sätt kan du visa dem som en enhet och ta bort dem med ett enda kommando när du är klar med dem.

Använd följande kommando för att skapa en resurs grupp:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group create ^
    --name %RESOURCE_GROUP% ^
    --location %LOCATION%
```

---

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Skapa sedan ett namn område, en händelsehubben och en auktoriseringsregel för Azure Event Hubs med följande kommandon:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventhubs namespace create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAMESPACE%
az eventhubs eventhub create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --message-retention 1
az eventhubs eventhub authorization-rule create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_AUTHORIZATION_RULE% ^
    --eventhub-name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --rights Listen Send
```

---

Event Hubs-namnrymden innehåller den faktiska händelsehubben och dess auktoriseringsregel. Auktoriseringsregeln gör det möjligt för dina funktioner att skicka meddelanden till hubben och lyssna efter motsvarande händelser. En funktion skickar meddelanden som representerar telemetridata. En annan funktion lyssnar efter händelser, analyserar händelse data och lagrar resultaten i Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Skapa ett Azure Cosmos DB-konto

Skapa sedan ett Azure Cosmos DB konto, en databas och en samling med hjälp av följande kommandon:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb sql database create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --name TelemetryDb
az cosmosdb sql container create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --database-name TelemetryDb \
    --name TelemetryInfo \
    --partition-key-path '/temperatureStatus'
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az cosmosdb create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %COSMOS_DB_ACCOUNT%
az cosmosdb sql database create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --name TelemetryDb
az cosmosdb sql container create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --database-name TelemetryDb ^
    --name TelemetryInfo ^
    --partition-key-path "/temperatureStatus"
```

---

`partition-key-path`Värdet partitionerar dina data baserat på `temperatureStatus` värdet för varje objekt. Med hjälp av partitionsnyckel kan Cosmos DB öka prestandan genom att dela upp data i distinkta del mängder som den kan komma åt oberoende av varandra.

### <a name="create-a-storage-account-and-function-app"></a>Skapa ett lagrings konto och en Function-app

Skapa sedan ett Azure Storage konto, vilket krävs av Azure Functions och skapa sedan Function-appen. Använd följande kommandon:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java \
    --functions-version 2
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az storage account create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %STORAGE_ACCOUNT% ^
    --sku Standard_LRS
az functionapp create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --storage-account %STORAGE_ACCOUNT% ^
    --consumption-plan-location %LOCATION% ^
    --runtime java ^
    --functions-version 2
```

---

När `az functionapp create` kommandot skapar din Function-app skapas även en Application Insights-resurs med samma namn. Function-appen konfigureras automatiskt med en inställning `APPINSIGHTS_INSTRUMENTATIONKEY` som heter som ansluter den till Application Insights. Du kan visa app-telemetri när du har distribuerat dina funktioner till Azure, enligt beskrivningen längre fram i den här självstudien.

## <a name="configure-your-function-app"></a>Konfigurera din Function-app

Din Function-app måste ha åtkomst till de andra resurserna för att fungera korrekt. I följande avsnitt visas hur du konfigurerar din Function-app så att den kan köras på den lokala datorn.

### <a name="retrieve-resource-connection-strings"></a>Hämta resurs anslutnings strängar

Använd följande kommandon för att hämta lagring, händelsehubben och Cosmos DB anslutnings strängar och spara dem i miljövariabler:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query 'connectionStrings[0].connectionString' \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
FOR /F "delims=" %X IN (' ^
    az storage account show-connection-string ^
        --name %STORAGE_ACCOUNT% ^
        --query connectionString ^
        --output tsv') DO SET AZURE_WEB_JOBS_STORAGE=%X
FOR /F "delims=" %X IN (' ^
    az eventhubs eventhub authorization-rule keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %EVENT_HUB_AUTHORIZATION_RULE% ^
        --eventhub-name %EVENT_HUB_NAME% ^
        --namespace-name %EVENT_HUB_NAMESPACE% ^
        --query primaryConnectionString ^
        --output tsv') DO SET EVENT_HUB_CONNECTION_STRING=%X
FOR /F "delims=" %X IN (' ^
    az cosmosdb keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %COSMOS_DB_ACCOUNT% ^
        --type connection-strings ^
        --query connectionStrings[0].connectionString ^
        --output tsv') DO SET COSMOS_DB_CONNECTION_STRING=%X
```

---

Dessa variabler anges till värden som hämtats från Azure CLI-kommandon. Varje kommando använder en JMESPath-fråga för att extrahera anslutnings strängen från den JSON-nyttolast som returnerades. Anslutnings strängarna visas också med `echo` så att du kan bekräfta att de har hämtats.

### <a name="update-your-function-app-settings"></a>Uppdatera inställningarna för Function-appen

Använd sedan följande kommando för att överföra anslutnings sträng värden till appinställningar i ditt Azure Functions-konto:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az functionapp config appsettings set ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --settings ^
        AzureWebJobsStorage=%AZURE_WEB_JOBS_STORAGE% ^
        EventHubConnectionString=%EVENT_HUB_CONNECTION_STRING% ^
        CosmosDBConnectionString=%COSMOS_DB_CONNECTION_STRING%
```

---

Dina Azure-resurser har nu skapats och kon figurer ATS så att de fungerar korrekt tillsammans.

## <a name="create-and-test-your-functions"></a>Skapa och testa dina funktioner

Därefter skapar du ett projekt på din lokala dator, lägger till Java-kod och testar det. Du ska använda kommandon som fungerar med Azure Functions-plugin-programmet för maven och Azure Functions Core Tools. Dina funktioner kommer att köras lokalt, men använder de molnbaserade resurser som du har skapat. När du har använt funktionerna som fungerar lokalt kan du använda Maven för att distribuera dem till molnet och titta på dina data och analyser.

Om du använde Cloud Shell för att skapa dina resurser kommer du inte att ansluta till Azure lokalt. I det här fallet använder du `az login` kommandot för att starta den webbläsarbaserade inloggnings processen. Ange vid behov standard prenumerationen med `az account set --subscription` följt av prenumerations-ID: t. Kör slutligen följande kommandon för att återskapa vissa miljövariabler på den lokala datorn. Ersätt `<value>` plats hållarna med samma värden som du använde tidigare.

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set FUNCTION_APP=<value>
```

---

### <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

Använd följande maven-kommando för att skapa ett Functions-projekt och lägga till nödvändiga beroenden.

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DappRegion=$LOCATION \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate --batch-mode ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype ^
    -DappName=%FUNCTION_APP% ^
    -DresourceGroup=%RESOURCE_GROUP% ^
    -DappRegion=%LOCATION% ^ 
    -DgroupId=com.example ^
    -DartifactId=telemetry-functions
```

---

Det här kommandot genererar flera filer i en `telemetry-functions` mapp:

* En `pom.xml` fil som ska användas med maven
* En `local.settings.json` fil som innehåller appinställningar för lokal testning
* En `host.json` fil som aktiverar paket för Azure Functions tillägg som krävs för att Cosmos DB utgående bindning i data analys funktionen
* En `Function.java` fil som innehåller en standard funktions implementering
* Några testfiler som den här kursen inte behöver

För att undvika kompileringsfel måste du ta bort testfilerna. Kör följande kommandon för att navigera till den nya projektmappen och ta bort mappen test:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
cd telemetry-functions
rm -r src/test
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd telemetry-functions
rmdir /s /q src\test
```

---

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Hämta dina funktions program inställningar för lokal användning

För lokal testning behöver ditt funktions projekt de anslutnings strängar som du har lagt till i din Function-app i Azure tidigare i den här självstudien. Använd följande Azure Functions Core Tools kommando, som hämtar alla funktioner i appen som lagras i molnet och lägger till dem i din `local.settings.json` fil:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
func azure functionapp fetch-app-settings %FUNCTION_APP%
```

---

### <a name="add-java-code"></a>Lägg till Java-kod

Öppna sedan `Function.java` filen och ersätt innehållet med följande kod.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Som du kan se innehåller den här filen två funktioner `generateSensorData` och `processSensorData` . `generateSensorData`Funktionen simulerar en sensor som skickar temperatur-och tryck läsningar till Event Hub. En timer-utlösare kör funktionen var tionde sekund och en utgående bindning för Event Hub skickar returvärdet till händelsehubben.

När händelsehubben tar emot meddelandet genererar den en händelse. `processSensorData`Funktionen körs när händelsen tas emot. Den bearbetar sedan händelse data och använder en Azure Cosmos DB utgående bindning för att skicka resultaten till Azure Cosmos DB.

De data som används av dessa funktioner lagras i en klass `TelemetryItem` som kallas, som du behöver implementera. Skapa en ny fil `TelemetryItem.java` med namnet på samma plats som `Function.java` och Lägg till följande kod:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Lokal körning

Nu kan du skapa och köra funktionerna lokalt och se data som visas i Azure Cosmos DB.

Använd följande maven-kommandon för att skapa och köra funktionerna:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn clean package
mvn azure-functions:run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn clean package
mvn azure-functions:run
```

---

Efter vissa build-och start meddelanden visas utdata som liknar följande exempel för varje gång funktionerna körs:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Du kan sedan gå till [Azure Portal](https://portal.azure.com) och navigera till ditt Azure Cosmos DB-konto. Välj **datautforskaren**, expandera **TelemetryInfo** och välj sedan **objekt** för att visa dina data när de tas emot.

![Cosmos DB Datautforskaren](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Distribuera till Azure och Visa app-telemetri

Slutligen kan du distribuera din app till Azure och kontrol lera att den fortsätter att fungera på samma sätt som den gjorde lokalt.

Distribuera projektet till Azure med hjälp av följande kommando:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn azure-functions:deploy
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn azure-functions:deploy
```

---

Funktionerna körs nu i Azure och fortsätter att samla in data i din Azure Cosmos DB. Du kan visa din distribuerade Function-app i Azure Portal och Visa app-telemetri via den anslutna Application Insights resursen, som visas i följande skärm bilder:

**Live Metrics Stream:**

![Application Insights Live Metrics Stream](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Historik**

![Bladet Application Insights prestanda](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med de Azure-resurser du har skapat i den här självstudien kan du ta bort dem med hjälp av följande kommando:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group delete --name %RESOURCE_GROUP%
```

---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en Azure-funktion som hanterar Event Hub-händelser och uppdaterar en Cosmos DB. Mer information finns i [Azure Functions Java Developer Guide](./functions-reference-java.md). Information om anteckningarna som används finns i [com. Microsoft. Azure. functions. Annotation](/java/api/com.microsoft.azure.functions.annotation) reference.

Den här självstudien använde miljövariabler och program inställningar för att lagra hemligheter som anslutnings strängar. Information om hur du lagrar dessa hemligheter i Azure Key Vault finns i [använda Key Vault referenser för app service och Azure Functions](../app-service/app-service-key-vault-references.md).

Nu ska du lära dig hur du använder Azure pipelines CI/CD för automatisk distribution:

> [!div class="nextstepaction"]
> [Bygg och distribuera Java till Azure Functions](/azure/devops/pipelines/ecosystems/java-function)
