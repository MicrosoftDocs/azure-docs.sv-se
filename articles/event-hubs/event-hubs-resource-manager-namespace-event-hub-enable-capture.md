---
title: Skapa en händelsehubb med Capture aktiverat – Azure Event Hubs | Microsoft Docs
description: Skapa ett namnområde för Azure Event Hubs med en händelsehubb och aktivera avbildningsfunktionen med hjälp av Azure Resource Manager-mallar
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658defcef5c9d145d499eb72ae372d1a7edfb740
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705429"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>Skapa en namnrymd med händelsehubb och aktivera avbildning med hjälp av en mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar ett namnområde för [Event Hubs](./event-hubs-about.md) med en händelsehubbinstans och även aktiverar [avbildningsfunktionen](event-hubs-capture-overview.md) på händelsehubben. Artikeln beskriver hur du definierar vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

Den här artikeln visar även hur du anger att händelser ska samlas in i Azure Storage Blobs eller en Azure Data Lake Store, utifrån det mål som du väljer.

Mer information om att skapa mallar finns i [Redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates]. Den JSON-syntax och de egenskaper som ska användas i en mall finns i avsnittet om [Microsoft.EventGrid-resurstyper](/azure/templates/microsoft.eventhub/allversions).

Mer information om mönster och praxis för namnkonventioner för Azure-resurser finns i [Azure Resources Naming Conventions][Azure Resources naming conventions] (Namnkonventioner för Azure-resurser).

För fullständiga mallar, klicka på följande GitHub-länkar:

- [Händelsehubb och aktivera mallen Capture to Storage (Avbildning till Storage)][Event Hub and enable Capture to Storage template] 
- [Händelsehubb och aktivera mallen Capture to Azure Data Lake Store (Avbildning till Azure Data Lake Store)][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> Om du vill söka efter de senaste mallarna kan du gå till galleriet [Azure-snabbstartsmallar][Azure Quickstart Templates] och söka efter Event Hubs.
> 
> 

## <a name="what-will-you-deploy"></a>Vad vill du distribuera?

Med den här mallen distribuerar du ett namnområde för Event Hubs med en händelsehubb och aktiverar även [Event Hubs Capture](event-hubs-capture-overview.md). Med Event Hubs Capture kan du automatiskt leverera strömmande data i Event Hubs till Azure Blob Storage eller Azure Data Lake Store inom en angiven tid eller ett visst storleksintervall som du kan välja själv. Klicka på följande knapp om du vill aktivera Event Hubs Capture i Azure Storage:

[![Distribuera till Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Klicka på följande knapp om du vill aktivera Event Hubs Capture i Azure Data Lake Store:

[![Distribuera till Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter `Parameters` och som innehåller alla parametervärden. Du bör definiera en parameter för de värden som varierar utifrån det projekt som du distribuerar eller utifrån den miljö som du distribuerar till. Definiera inte parametrar för värden som aldrig ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Namnet på namnområdet för Event Hubs som ska skapas.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Namnet på händelsehubben som skapats i namnområdet för Event Hubs.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

Antalet dagar som meddelanden lagras i händelsehubben. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Antalet partitioner som ska skapas i händelsehubben.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

Aktiverar avbildning på händelsehubben.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

Kodningsformatet som du anger för att serialisera händelsedata.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

Det tidsintervall inom vilket Event Hubs Capture börjar samla in data.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
Det storleksintervall inom vilket avbildningsfunktionen börjar samla in data.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

Det namnformat som används av Event Hubs Capture för att skriva Avro-filer. Observera att namnformatet för avbildningsfunktionen måste innehålla fälten `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` och `{Second}`. Dessa kan ordnas i valfri ordning, med eller utan avgränsare.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

API-versionen av mallen.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Använd följande parametrar om du väljer Azure Storage som mål.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

För avbildningsfunktionen krävs ett resurs-ID för ett Azure Storage-konto för att aktivera avbildning till ditt önskade Storage-konto.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

Blobcontainern dit du kan avbilda dina händelsedata.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Använd följande parametrar om du väljer Azure Data Lake Store gen 1 som mål. Du måste ange behörigheter för den Data Lake Store-sökväg som du vill avbilda händelsen på. Om du vill ange behörigheter, se [samla in data till Azure Data Lake Storage gen 1](event-hubs-capture-enable-through-portal.md#capture-data-to-azure-data-lake-storage-gen-1).

### <a name="subscriptionid"></a>subscriptionId

Prenumerations-ID för namnområdet för Event Hubs och Azure Data Lake Store. Båda dessa resurser måste vara under samma prenumerations-ID.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

Azure Data Lake Store-namnet för de avbildade händelserna.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

Sökvägen till målmappen för de avbildade händelserna. Detta är mappen i din Data Lake Store dit händelserna ska skickas under avbildningen. Se [Använda Data Lake Store för att hämta data från Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md) för att ange behörigheter för den här mappen.

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Resurser att distribuera för Azure Storage som mål för avbildade händelser

Skapar ett namnområde av typen **EventHub** med en händelsehubb och möjliggör avbildning till Azure Blob Storage.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Resurser att distribuera för Azure Data Lake Store som mål

Skapar ett namnområde av typen **EventHub** med en händelsehubb och möjliggör avbildning till Azure Data Lake Store.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> Du kan aktivera eller inaktivera tomma filer om inga händelser inträffar i fönstret Hämta med hjälp av egenskapen **skipEmptyArchives**. 

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Distribuera din mall för att aktivera Event Hubs Capture i Azure Storage:
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Distribuera din mall för att aktivera Event Hubs Capture i Azure Data Lake Store:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

Azure Blob Storage som mål:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Azure Data Lake Store som mål:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Nästa steg

Du kan även konfigurera Event Hubs Capture via [Azure Portal](https://portal.azure.com). Mer information finns i [Enable Event Hubs Capture using the Azure portal](event-hubs-capture-enable-through-portal.md) (Aktivera Event Hubs Capture med hjälp av Azure Portal).

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](./event-hubs-about.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
