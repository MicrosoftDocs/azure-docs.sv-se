---
title: Självstudie – konfigurera meddelanderoutning för Azure IoT Hub att använda en Azure Resource Manager mall
description: Självstudie – konfigurera meddelanderoutning för Azure IoT Hub att använda en Azure Resource Manager mall
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53217340b0d91f3de77e5e0d8c0a82e30599d6ed
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621435"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Självstudie: Använd en Azure Resource Manager-mall för att konfigurera IoT Hub meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Meddelanderoutning

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Ladda ned mallen mall och parametrar

För den andra delen av den här självstudien kan du hämta och köra ett Visual Studio-program för att skicka meddelanden till IoT Hub. Det finns en mapp i den hämtade filen som innehåller Azure Resource Manager mall och parameter filen, samt Azure CLI-och PowerShell-skript.

Gå vidare och hämta [Azure IoT C#-exemplen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) nu. Zippa upp master.zip-filen. Resource Manager-mallen och parameter filen är i/iot-hub/Tutorials/Routing/SimulatedDevice/resources/som **template_iothub.jspå** och **template_iothub_parameters.jspå**.

## <a name="create-your-resources"></a>Skapa dina resurser

Du kommer att använda en Azure Resource Manager (RM)-mall för att skapa alla dina resurser. Azure CLI-och PowerShell-skripten kan köras några rader i taget. En RM-mall distribueras i ett enda steg. Den här artikeln visar avsnitten separat för att hjälpa dig att förstå var och en. Sedan visar den hur du distribuerar mallen och skapar den virtuella enheten för testning. När mallen har distribuerats kan du Visa konfigurationen för meddelanderoutning i portalen.

Det finns flera resurs namn som måste vara globalt unika, till exempel IoT Hub namn och lagrings konto namn. För att göra det lättare att namnge resurserna är dessa resurs namn inställda för att lägga till ett slumpmässigt alfanumeriskt värde som genereras från aktuellt datum/tid. 

Om du tittar på mallen ser du var variabler har kon figurer ATS för de här resurserna som tar parametern som skickas i och sammanfogar *randomValue* till parametern. 

I följande avsnitt beskrivs de parametrar som används.

### <a name="parameters"></a>Parametrar

De flesta av dessa parametrar har standardvärden. De som slutar med **_In** sammanfogas med *randomValue* för att göra dem globalt unika. 

**randomValue**: det här värdet genereras från aktuellt datum/tid när du distribuerar mallen. Det här fältet finns inte i parameter filen, eftersom det genereras i själva mallen.

**subscriptionId**: det här fältet har angetts till den prenumeration som du distribuerar mallen till. Det här fältet finns inte i parameter filen eftersom det har angetts för dig.

**IoTHubName_in**: det här fältet är bas IoT Hub namnet, som sammanfogas med randomValue som ska vara globalt unikt.

**plats**: det här fältet är den Azure-region som du distribuerar till, till exempel "väst".

**consumer_group**: det här fältet är konsument grupps uppsättningen för meddelanden som kommer genom Dirigerings slut punkten. Det används för att filtrera resultat i Azure Stream Analytics. Det finns till exempel hela strömmen där du får allt, eller om du har data som kommer till med consumer_group har angetts till **contoso**, kan du skapa en Azure Stream Analytics data ström (och Power BI rapport) så att bara de posterna visas. Det här fältet används i del 2 av den här självstudien.

**sku_name**: det här fältet är skalningen för IoT Hub. Värdet måste vara S1 eller högre. en kostnads fri nivå fungerar inte för den här självstudien eftersom den inte tillåter flera slut punkter.

**sku_units**: det här fältet går till **sku_name** och är antalet IoT Hub enheter som kan användas.

**d2c_partitions**: det här fältet är antalet partitioner som används för händelse strömmen.

**storageAccountName_in**: det här fältet är namnet på det lagrings konto som ska skapas. Meddelanden dirigeras till en behållare i lagrings kontot. Det här fältet kombineras med randomValue för att göra det globalt unikt.

**storageContainerName**: det här fältet är namnet på behållaren där meddelandena som dirigeras till lagrings kontot lagras.

**storage_endpoint**: det här fältet är namnet på lagrings konto slut punkten som används av meddelanderoutning.

**service_bus_namespace_in**: det här fältet är namnet på det Service Bus namn område som ska skapas. Det här värdet sammanfogas med randomValue för att göra det globalt unikt.

**service_bus_queue_in**: det här fältet är namnet på den Service Bus kö som används för att dirigera meddelanden. Det här värdet sammanfogas med randomValue för att göra det globalt unikt.

**AuthRules_sb_queue**: det här fältet är auktoriseringsregler för Service Bus-kön som används för att hämta anslutnings strängen för kön.

### <a name="variables"></a>Variabler

Dessa värden används i mallen och är mest härledda från parametrar.

**queueAuthorizationRuleResourceId**: det här fältet är ResourceID för auktoriseringsregeln för Service Bus kön. ResourceId används i sin tur för att hämta anslutnings strängen för kön.

**iotHubName**: det här fältet är namnet på IoT Hub efter att ha randomValue sammanfogats. 

**storageAccountName**: det här fältet är namnet på lagrings kontot efter att randomValue har länkats. 

**service_bus_namespace**: det här fältet är namn området efter att ha randomValue sammanfogats.

**service_bus_queue**: det här fältet är namnet på Service Bus kön efter att ha randomValue sammanfogats.

**sbVersion**: den version av Service Bus-API som ska användas. I det här fallet är det "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Resurser: lagrings konto och behållare

Den första resursen som skapas är lagrings kontot, tillsammans med den behållare som meddelanden dirigeras till. Behållaren är en resurs under lagrings kontot. Den har en `dependsOn` sats för lagrings kontot som kräver att lagrings kontot skapas före behållaren.

Det här avsnittet ser ut så här:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Resurser: Service Bus namnrymd och kö

Den andra resursen som skapas är Service Bus namnrymd, tillsammans med den Service Bus kö som meddelanden dirigeras till. SKU: n är inställd på standard. API-versionen hämtas från variablerna. Den är också inställd på att aktivera Service Bus namn området när det distribuerar det här avsnittet (status: aktiv). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

I det här avsnittet skapas Service Bus kön. Den här delen av skriptet innehåller en- `dependsOn` sats som säkerställer att namn området skapas före kön.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Resurser: IoT-hubb och meddelanderoutning

Nu när lagrings kontot och Service Bus kön har skapats skapar du IoT Hub som dirigerar meddelanden till dem. RM-mallen använder `dependsOn` satser så att den inte försöker skapa navet innan Service Bus resurser och lagrings kontot har skapats. 

Här är den första delen av avsnittet IoT Hub. Den här delen av mallen konfigurerar beroendena och börjar med egenskaperna.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

Nästa avsnitt är avsnittet för konfigurationen av meddelanderoutning för IoT Hub. Första avsnittet för slut punkterna. Den här delen av mallen konfigurerar Dirigerings slut punkter för Service Bus kön och lagrings kontot, inklusive anslutnings strängarna.

Om du vill skapa anslutnings strängen för kön behöver du queueAuthorizationRulesResourcedId, som hämtas infogas. För att skapa anslutnings strängen för lagrings kontot hämtar du den primära lagrings nyckeln och använder den sedan i formatet för anslutnings strängen.

Slut punkts konfigurationen är också där du ställer in BLOB-formatet på `AVRO` eller `JSON` .

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Det här nästa avsnitt gäller för meddelande vägar till slut punkterna. Det finns en inställning för varje slut punkt, så det finns en för Service Bus kö och en för lagrings konto behållaren.

Kom ihåg att frågevillkor för de meddelanden som dirigeras till Storage är `level="storage"` och villkoren för de meddelanden som dirigeras till Service Bus kön är `level="critical"` .

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Detta JSON visar resten av IoT Hub-avsnittet som innehåller standard information och SKU för hubben.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Resurser: Service Bus auktoriseringsregler för kön

Auktoriseringsregeln för Service Bus kön används för att hämta anslutnings strängen för Service Bus kön. Den använder en- `dependsOn` sats för att säkerställa att den inte skapas före Service Bus-namnrymden och Service Bus kön.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Resurser: konsument grupp

I det här avsnittet skapar du en konsument grupp för de IoT Hub data som ska användas av Azure Stream Analytics i den andra delen av den här självstudien.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Resurser: utdata

Om du vill skicka ett värde tillbaka till distributions skriptet som ska visas använder du ett utdata-avsnitt. Den här delen av mallen returnerar anslutnings strängen för Service Bus kön. Att returnera ett värde är inte obligatoriskt, det ingår som ett exempel på hur du returnerar resultat till det anropande skriptet.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Distribuera RM-mallen

Distribuera mallen till Azure genom att överföra mallen och parameter filen till Azure Cloud Shell och sedan köra ett skript för att distribuera mallen. Öppna Azure Cloud Shell och logga in. I det här exemplet används PowerShell.

Om du vill överföra filerna väljer du ikonen **Ladda upp/ladda ned filer** på Meny raden och väljer sedan ladda upp.

![Skärm bild som visar ikonen Ladda upp/ladda ned filer.](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Använd Utforskaren som öppnas för att hitta filerna på den lokala disken och markera dem och välj sedan **Öppna**.

När filerna har laddats upp visas en resultat dialog ruta som liknar följande bild.

![Cloud Shell meny raden med resultat för uppladdning/hämtning markerad](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Filerna överförs till resursen som används av Cloud Shell-instansen. 

Kör skriptet för att utföra distributionen. Den sista raden i det här skriptet hämtar variabeln som har kon figurer ATS för att returneras--Service Bus Queue-anslutningssträng.

Skriptet anger och använder dessa variabler:

**$RGName** är namnet på den resurs grupp som mallen ska distribueras till. Det här fältet skapas innan du distribuerar mallen.

**$location** är Azure-platsen som ska användas för mallen, till exempel "väst".

**deploymentname** är ett namn som du tilldelar distributionen för att hämta det returnerade variabelvärdet.

Här är PowerShell-skriptet. Kopiera det här PowerShell-skriptet och klistra in det i Cloud Shell fönstret och tryck sedan på RETUR för att köra det.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Om du har skript fel kan du redigera skriptet lokalt, ladda upp det igen till Cloud Shell och köra skriptet igen. När skriptet har körts klart fortsätter du till nästa steg.

## <a name="create-simulated-device"></a>Skapa simulerad enhet

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Visa meddelanderoutning i portalen

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat alla resurser och meddelande vägarna har kon figurer ATS kan du gå vidare till nästa självstudie för att lära dig att bearbeta och Visa information om de dirigerade meddelandena.

> [!div class="nextstepaction"]
> [Del 2 – Visa resultatet av meddelanderoutning](tutorial-routing-view-message-routing-results.md)
