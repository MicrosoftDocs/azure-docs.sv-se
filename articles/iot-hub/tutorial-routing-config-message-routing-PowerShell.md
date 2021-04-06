---
title: Självstudie – konfigurera meddelanderoutning för Azure IoT Hub med Azure PowerShell
description: Självstudie – konfigurera meddelanderoutning för Azure IoT Hub att använda Azure PowerShell. Beroende på egenskaperna i meddelandet dirigerar du antingen till ett lagrings konto eller en Service Bus kö.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: be1560bcc03ec7a26f4bc374392c746243cd731a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624079"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Självstudie: Använd Azure PowerShell för att konfigurera IoT Hub meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Hämta skriptet (valfritt)

För den andra delen av den här självstudien kan du hämta och köra ett Visual Studio-program för att skicka meddelanden till IoT Hub. Det finns en mapp i nedladdningen som innehåller Azure Resource Manager mall och parameter filen, samt Azure CLI-och PowerShell-skript. 

Om du vill visa det färdiga skriptet laddar du ned [Azure IoT C#-exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Zippa upp master.zip-filen. Azure CLI-skriptet är i/iot-hub/Tutorials/Routing/SimulatedDevice/resources/som **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Skapa dina resurser

Börja med att skapa resurser med PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Använd PowerShell för att skapa bas resurser

Kopiera och klistra in skriptet nedan i Cloud Shell och tryck på RETUR. Den kör skriptet en rad i taget. I det första avsnittet av skriptet skapas bas resurserna för den här självstudien, inklusive lagrings kontot, IoT Hub, Service Bus namn området och Service Bus kön. När du går igenom självstudien kopierar du varje skript block och klistrar in det i Cloud Shell för att köra det.

Det finns flera resurs namn som måste vara globalt unika, till exempel IoT Hub namn och lagrings konto namn. För att göra det enklare läggs dessa resurs namn till med ett slumpmässigt alfanumeriskt värde som kallas *randomValue*. RandomValue skapas en gång överst i skriptet och läggs till i resurs namnen vid behov i hela skriptet. Om du inte vill att den ska vara slumpmässig, kan du ange den som en tom sträng eller ett angivet värde. 

> [!IMPORTANT]
> Variablerna som anges i det inledande skriptet används också av skriptet för routning, så kör hela skriptet i samma Cloud Shell-session. Om du öppnar en ny session för att köra skriptet för att konfigurera routningen kommer flera av variablerna att sakna värden. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Skapa en simulerad enhet

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Nu när bas resurserna har kon figurer ATS kan du konfigurera meddelanderoutning.

## <a name="set-up-message-routing"></a>Konfigurera meddelandedirigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Använd [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint)för att skapa en cirkulations slut punkt. Använd [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute)för att skapa meddelande flödet för slut punkten.

### <a name="route-to-a-storage-account"></a>Dirigera till ett lagrings konto 

Börja med att konfigurera slut punkten för lagrings kontot och skapa sedan meddelande vägen.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Detta är de variabler som används av skriptet som måste anges inom Cloud Shell-sessionen:

**resourceGroup**: det finns två förekomster av detta fält – ange båda för resurs gruppen.

**namn**: det här fältet är namnet på IoT Hub som operationsföljden ska gälla för.

**endpointName**: det här fältet är namnet som identifierar slut punkten. 

**endpointType**: det här fältet är typen av slut punkt. Värdet måste anges till,, `azurestoragecontainer` `eventhub` `servicebusqueue` eller `servicebustopic` . För dina behov här ställer du in den på `azurestoragecontainer` .

**subscriptionID**: det här fältet har angetts till subscriptionID för ditt Azure-konto.

**storageConnectionString**: det här värdet hämtas från det lagrings konto som har kon figurer ATS i föregående skript. Den används av routningen för att komma åt lagrings kontot.

**containerName**: det här fältet är namnet på behållaren i det lagrings konto som data ska skrivas till.

**Encoding**: Ange det här fältet som antingen `AVRO` eller `JSON` . Detta anger formatet för lagrade data. Standardvärdet är AVRO.

**routeName**: det här fältet är namnet på den väg som du ställer in. 

**villkor**: det här fältet är den fråga som används för att filtrera meddelanden som skickas till den här slut punkten. Frågevillkor för meddelanden som dirigeras till lagring är `level="storage"` .

**aktive rad**: det här fältet är standardvärdet `true` och anger att meddelande flödet ska aktive ras efter att det har skapats.

Kopiera det här skriptet och klistra in det i Cloud Shell-fönstret.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Nästa steg är att skapa Dirigerings slut punkten för lagrings kontot. Du kan också ange den behållare där resultaten ska lagras. Behållaren skapades när lagrings kontot skapades.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Skapa sedan meddelande vägen för lagrings slut punkten. Meddelande vägen anger vart meddelanden som uppfyller frågans specifikation ska skickas.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Dirigera till en Service Bus kö

Konfigurera nu routning för Service Bus-kön. Om du vill hämta anslutnings strängen för Service Bus kön måste du skapa en auktoriseringsregel som har rätt rättigheter definierade. Följande skript skapar en auktoriseringsregel för Service Bus kön som kallas `sbauthrule` , och anger rättigheterna till `Listen Manage Send` . När den här auktoriseringsregeln har kon figurer ATS kan du använda den för att hämta anslutnings strängen för kön.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Använd nu auktoriseringsregeln för att hämta Service Bus Queue-nyckeln. Den här auktoriseringsregeln kommer att användas för att hämta anslutnings strängen senare i skriptet.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Nu konfigurerar du Dirigerings slut punkten och meddelande vägen för Service Bus kön. Detta är de variabler som används av skriptet som måste anges inom Cloud Shell-sessionen:

**endpointName**: det här fältet är namnet som identifierar slut punkten. 

**endpointType**: det här fältet är typen av slut punkt. Värdet måste anges till,, `azurestoragecontainer` `eventhub` `servicebusqueue` eller `servicebustopic` . För dina behov här ställer du in den på `servicebusqueue` .

**routeName**: det här fältet är namnet på den väg som du ställer in. 

**villkor**: det här fältet är den fråga som används för att filtrera meddelanden som skickas till den här slut punkten. Frågevillkor för de meddelanden som dirigeras till Service Bus kön är `level="critical"` .

Här är Azure PowerShell för meddelanderoutning för kön Service Bus.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Visa meddelanderoutning i portalen

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat resurserna och meddelande vägarna har kon figurer ATS kan du gå vidare till nästa självstudie för att lära dig hur du skickar meddelanden till IoT-hubben och ser att de dirigeras till olika destinationer. 

> [!div class="nextstepaction"]
> [Del 2 – Visa resultatet av meddelanderoutning](tutorial-routing-view-message-routing-results.md)
