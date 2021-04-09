---
title: 'Snabb start: skapa en Azure Data Factory med Azure CLI'
description: Den här snabb starten skapar en Azure Data Factory, inklusive en länkad tjänst, data uppsättningar och en pipeline. Du kan köra pipelinen för att göra en fil kopierings åtgärd.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: 9af5f276e49e9eb2756dc544db353c75c99bc5a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105938070"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Snabb start: skapa en Azure Data Factory med Azure CLI

I den här snabb starten beskrivs hur du använder Azure CLI för att skapa en Azure Data Factory. Pipelinen som du skapar i den här data fabriken kopierar data från en mapp till en annan i ett Azure-Blob Storage. Information om hur du omvandlar data med hjälp av Azure Data Factory finns [i transformera data i Azure Data Factory](transform-data.md).

En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna deltagare eller ägare, eller vara administratör för Azure-prenumerationen. Mer information finns i [Azure-roller](quickstart-create-data-factory-powershell.md#azure-roles).

## <a name="prepare-a-container-and-test-file"></a>Förbereda en behållare och test fil

I den här snabb starten används ett Azure Storage-konto, som innehåller en behållare med en fil.

1. Om du vill skapa en resurs grupp med namnet `ADFQuickStartRG` använder du kommandot [AZ Group Create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Skapa ett lagrings konto med kommandot [AZ Storage Account Create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Skapa en behållare med namnet `adftutorial` med hjälp av kommandot [AZ Storage container Create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. I den lokala katalogen skapar du en fil med namnet `emp.txt` att ladda upp. Om du arbetar i Azure Cloud Shell kan du hitta den aktuella arbets katalogen med hjälp av `echo $PWD` kommandot bash. Du kan använda vanliga bash-kommandon som `cat` till exempel för att skapa en fil:

   ```console
   cat > emp.txt
   This is text.
   ```

   Använd **CTRL + D** för att spara den nya filen.

1. Om du vill överföra den nya filen till din Azure Storage-behållare använder du kommandot [AZ Storage BLOB upload](/cli/azure/storage/blob#az_storage_blob_upload) :

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Det här kommandot överför till en ny mapp med namnet `input` .

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Skapa en Azure-datafabrik genom att köra kommandot [AZ DataFactory Factory Create](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_create) :

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Ersätt `ADFTutorialFactory` med ett globalt unikt data fabriks namn, till exempel ADFTutorialFactorySP1127.

Du kan se data fabriken som du skapade med kommandot [AZ DataFactory Factory show](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) :

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Skapa en länkad tjänst och data uppsättningar

Skapa sedan en länkad tjänst och två data uppsättningar.

1. Hämta anslutnings strängen för ditt lagrings konto med hjälp av kommandot [AZ Storage Account show-Connection-sträng](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) :

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. I din arbets katalog skapar du en JSON-fil med det här innehållet, som innehåller din egen anslutnings sträng från föregående steg. Namnge filen `AzureStorageLinkedService.json` :

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. Skapa en länkad tjänst med namnet med `AzureStorageLinkedService` hjälp av kommandot [AZ DataFactory Linked-service Create](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_create) :

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. I din arbets katalog skapar du en JSON-fil med det här innehållet med namnet `InputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Skapa en data uppsättning med namnet `InputDataset` med hjälp av kommandot [AZ DataFactory data uppsättning Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. I din arbets katalog skapar du en JSON-fil med det här innehållet med namnet `OutputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Skapa en data uppsättning för utdata med namnet `OutputDataset` med hjälp av kommandot [AZ DataFactory data uppsättning Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Skapa och kör pipelinen

Slutligen skapar och kör du pipelinen.

1. I din arbets katalog skapar du en JSON-fil med det här innehållet med namnet `Adfv2QuickStartPipeline.json` :

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. Skapa en pipeline med namnet `Adfv2QuickStartPipeline` med hjälp av kommandot [AZ DataFactory pipeline Create](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create) :

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Köra pipelinen med hjälp av kommandot [AZ DataFactory pipeline Create-Run](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create_run) :

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Det här kommandot returnerar ett körnings-ID. Kopiera den för användning i nästa kommando.

1. Verifiera att pipelinen har körts genom att använda kommandot [AZ DataFactory pipeline-Run show](/cli/azure/ext/datafactory/datafactory/pipeline-run#ext_datafactory_az_datafactory_pipeline_run_show) :

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Du kan också kontrol lera att din pipeline kördes som förväntat med hjälp av [Azure Portal](https://portal.azure.com/). Mer information finns i [Granska distribuerade resurser](quickstart-create-data-factory-powershell.md#review-deployed-resources).

## <a name="clean-up-resources"></a>Rensa resurser

Alla resurser i den här snabb starten ingår i samma resurs grupp. Om du vill ta bort alla använder du kommandot [AZ Group Delete](/cli/azure/group#az_group_delete) :

```azurecli
az group delete --name ADFQuickStartRG
```

Om du använder den här resurs gruppen för något annat kan du i stället ta bort enskilda resurser. Om du till exempel vill ta bort den länkade tjänsten använder du kommandot [AZ DataFactory Link-service Delete](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_delete) .

I den här snabb starten skapade du följande JSON-filer:

- AzureStorageLinkedService.jspå
- InputDataset.jspå
- OutputDataset.jspå
- Adfv2QuickStartPipeline.jspå

Ta bort dem med hjälp av standard bash-kommandon.

## <a name="next-steps"></a>Nästa steg

- [Pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md)
- [Det finns inte några länkade tjänster i Azure Data Factory](concepts-linked-services.md)
- [Datauppsättningar i Azure Data Factory](concepts-datasets-linked-services.md)
- [Transformera data i Azure Data Factory](transform-data.md)
