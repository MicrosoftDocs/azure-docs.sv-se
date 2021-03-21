---
title: Självstudie – konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI
description: Självstudie – konfigurera meddelanderoutning för Azure IoT Hub med hjälp av Azure CLI. Beroende på egenskaperna i meddelandet dirigerar du antingen till ett lagrings konto eller en Service Bus kö.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a1cce7264e56b16d285685392186a05e2f075439
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199772"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Självstudie: Använd Azure CLI för att konfigurera IoT Hub meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Hämta skriptet (valfritt)

För den andra delen av den här självstudien kan du hämta och köra ett Visual Studio-program för att skicka meddelanden till IoT Hub. Det finns en mapp i nedladdningen som innehåller Azure Resource Manager mall och parameter filen, samt Azure CLI-och PowerShell-skript.

Om du vill visa det färdiga skriptet laddar du ned [Azure IoT C#-exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Zippa upp master.zip-filen. Azure CLI-skriptet är i/iot-hub/Tutorials/Routing/SimulatedDevice/resources/som **iothub_routing_cli. azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Använd Azure CLI för att skapa resurser

Kopiera och klistra in skriptet nedan i Cloud Shell och tryck på RETUR. Den kör skriptet en rad i taget. I det första avsnittet av skriptet skapas bas resurserna för den här självstudien, inklusive lagrings kontot, IoT Hub, Service Bus namn området och Service Bus kön. När du går igenom resten av självstudien kopierar du varje skript block och klistrar in det i Cloud Shell för att köra det.

> [!TIP]
> Ett tips om fel sökning: det här skriptet använder fortsättnings symbolen (det omvända snedstrecket `\` ) för att göra skriptet mer läsbart. Om du har problem med att köra skriptet kontrollerar du att Cloud Shell-sessionen körs `bash` och att det inte finns några blank steg efter något av omvänt snedstrecken.
> 

Det finns flera resurs namn som måste vara globalt unika, till exempel IoT Hub namn och lagrings konto namn. För att göra det enklare läggs dessa resurs namn till med ett slumpmässigt alfanumeriskt värde som kallas *randomValue*. RandomValue skapas en gång överst i skriptet och läggs till i resurs namnen vid behov i hela skriptet. Om du inte vill att den ska vara slumpmässig, kan du ange den som en tom sträng eller ett angivet värde. 

> [!IMPORTANT]
> Variablerna som anges i det inledande skriptet används också av skriptet för routning, så kör hela skriptet i samma Cloud Shell-session. Om du öppnar en ny session för att köra skriptet för att konfigurera routningen kommer flera av variablerna att sakna värden.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Nu när bas resurserna har kon figurer ATS kan du konfigurera meddelanderoutning.

## <a name="set-up-message-routing"></a>Konfigurera meddelandedirigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Om du vill skapa en Dirigerings slut punkt använder du [AZ IoT Hub routing-Endpoint Create](/cli/azure/iot/hub/routing-endpoint#az-iot-hub-routing-endpoint-create). Om du vill skapa meddelande vägen för slut punkten använder du [AZ IoT Hub Route Create](/cli/azure/iot/hub/route#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Dirigera till ett lagrings konto

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Börja med att konfigurera slut punkten för lagrings kontot och ställ sedan in vägen. 

Detta är de variabler som används av skriptet som måste anges inom Cloud Shell-sessionen:

**storageConnectionString**: det här värdet hämtas från det lagrings konto som har kon figurer ATS i föregående skript. Den används av meddelanderoutning för att komma åt lagrings kontot.

  **resourceGroup**: det finns två förekomster av resurs gruppen – ange dem till din resurs grupp.

**slut punkts subscriptionId**: det här fältet har angetts till Azure subscriptionID för slut punkten. 

**endpointType**: det här fältet är typen av slut punkt. Värdet måste anges till,, `azurestoragecontainer` `eventhub` `servicebusqueue` eller `servicebustopic` . För dina behov här ställer du in den på `azurestoragecontainer` .

**iotHubName**: det här fältet är namnet på den hubb som ska utföra dirigeringen.

**containerName**: det här fältet är namnet på behållaren i det lagrings konto som data ska skrivas till.

**encoding**: det här fältet är antingen `avro` eller `json` . Detta anger formatet för lagrade data.

**routeName**: det här fältet är namnet på den väg som du ställer in. 

**endpointName**: det här fältet är namnet som identifierar slut punkten. 

**aktive rad**: det här fältet är standardvärdet `true` och anger att meddelande flödet ska aktive ras efter att det har skapats.

**villkor**: det här fältet är den fråga som används för att filtrera meddelanden som skickas till den här slut punkten. Frågevillkor för meddelanden som dirigeras till lagring är `level="storage"` .

Kopiera skriptet och klistra in det i Cloud Shell-fönstret och kör det.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

Nästa steg är att skapa Dirigerings slut punkten för lagrings kontot. Du kan också ange den behållare där resultaten ska lagras. Behållaren skapades tidigare när lagrings kontot skapades.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Skapa sedan vägen för lagrings slut punkten. Meddelande vägen anger vart meddelanden som uppfyller frågans specifikation ska skickas. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Dirigera till en Service Bus kö

Konfigurera nu routning för Service Bus-kön. Om du vill hämta anslutnings strängen för Service Bus kön måste du skapa en auktoriseringsregel som har rätt rättigheter definierade. Följande skript skapar en auktoriseringsregel för Service Bus kön som kallas `sbauthrule` , och anger rättigheterna till `Listen Manage Send` . När den här auktoriseringsregeln har definierats kan du använda den för att hämta anslutnings strängen för kön.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Använd nu auktoriseringsregeln för att hämta anslutnings strängen till Service Bus kön.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Nu konfigurerar du Dirigerings slut punkten och meddelande vägen för Service Bus kön. Detta är de variabler som används av skriptet som måste anges inom Cloud Shell-sessionen:

**endpointName**: det här fältet är namnet som identifierar slut punkten. 

**endpointType**: det här fältet är typen av slut punkt. Värdet måste anges till,, `azurestoragecontainer` `eventhub` `servicebusqueue` eller `servicebustopic` . För dina behov här ställer du in den på `servicebusqueue` .

**routeName**: det här fältet är namnet på den väg som du ställer in. 

**villkor**: det här fältet är den fråga som används för att filtrera meddelanden som skickas till den här slut punkten. Frågevillkor för de meddelanden som dirigeras till Service Bus kön är `level="critical"` .

Här är Azure CLI för cirkulations slut punkten och meddelande vägen för Service Bus kön.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Visa meddelanderoutning i portalen

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat resurserna och meddelande vägarna har kon figurer ATS kan du gå vidare till nästa självstudie för att lära dig hur du skickar meddelanden till IoT-hubben och ser att de dirigeras till olika destinationer. 

> [!div class="nextstepaction"]
> [Del 2 – Visa resultatet av meddelanderoutning](tutorial-routing-view-message-routing-results.md)
