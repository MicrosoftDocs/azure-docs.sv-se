---
title: 'Snabbstart: Skapa en Azure Data Factory med Azure CLI'
description: Den här snabbstarten skapar Azure Data Factory, inklusive en länkad tjänst, datauppsättningar och en pipeline. Du kan köra pipelinen för att göra en filkopieringsåtgärd.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: b40407f4c4fb81bbf76bd0b552f3c9f2c827232a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871544"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Snabbstart: Skapa en Azure Data Factory med Azure CLI

Den här snabbstarten beskriver hur du använder Azure CLI för att skapa Azure Data Factory. Pipelinen som du skapar i den här datafabriken kopierar data från en mapp till en annan mapp i en Azure Blob Storage. Information om hur du transformerar data med Azure Data Factory finns i [Transformera data i Azure Data Factory](transform-data.md).

En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna deltagare eller ägare, eller vara administratör för Azure-prenumerationen. Mer information finns i [Azure-roller.](quickstart-create-data-factory-powershell.md#azure-roles)

## <a name="prepare-a-container-and-test-file"></a>Förbereda en container och en testfil

Den här snabbstarten använder Azure Storage ett konto, som innehåller en container med en fil.

1. Om du vill skapa en `ADFQuickStartRG` resursgrupp med namnet använder du [kommandot az group create:](/cli/azure/group#az_group_create)

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Skapa ett lagringskonto med kommandot [az storage account](/cli/azure/storage/container#az_storage_container_create) create:

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Skapa en container med `adftutorial` namnet med kommandot az storage container [create:](/cli/azure/storage/container#az_storage_container_create)

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. I den lokala katalogen skapar du en fil med namnet `emp.txt` för uppladdning. Om du arbetar i Azure Cloud Shell du hitta den aktuella arbetskatalogen med hjälp av `echo $PWD` Bash-kommandot. Du kan använda bash-standardkommandon som `cat` för att skapa en fil:

   ```console
   cat > emp.txt
   This is text.
   ```

   Använd **Ctrl + D** för att spara den nya filen.

1. Om du vill ladda upp den nya filen till Azure Storage-containern använder du [kommandot az storage blob upload:](/cli/azure/storage/blob#az_storage_blob_upload)

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Det här kommandot laddar upp till en ny mapp med namnet `input` .

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Skapa en Azure-datafabrik genom att köra [kommandot az datafactory factory create:](/cli/azure/datafactory/factory#az_datafactory_factory_create)

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Ersätt `ADFTutorialFactory` med ett globalt unikt datafabriksnamn, till exempel ADFTutorialFactorySP1127.

Du kan se datafabriken som du skapade med kommandot [az datafactory factory show:](/cli/azure/datafactory/factory#az_datafactory_factory_show)

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Skapa en länkad tjänst och datauppsättningar

Skapa sedan en länkad tjänst och två datauppsättningar.

1. Hämta anslutningssträngen för ditt lagringskonto med hjälp av [kommandot az storage account show-connection-string:](/cli/azure/datafactory/factory#az_datafactory_factory_show)

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. I arbetskatalogen skapar du en JSON-fil med det här innehållet, som innehåller din egen anslutningssträng från föregående steg. Ge filen namnet `AzureStorageLinkedService.json` :

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

1. Skapa en länkad tjänst med `AzureStorageLinkedService` namnet med hjälp av kommandot az [datafactory linked-service create:](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create)

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. I arbetskatalogen skapar du en JSON-fil med det här innehållet med namnet `InputDataset.json` :

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

1. Skapa en indatauppsättning med `InputDataset` namnet med kommandot az [datafactory dataset](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) create:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. I arbetskatalogen skapar du en JSON-fil med det här innehållet med namnet `OutputDataset.json` :

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

1. Skapa en utdatauppsättning med `OutputDataset` namnet med kommandot az [datafactory dataset](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) create:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Skapa och köra pipelinen

Skapa och kör slutligen pipelinen.

1. I arbetskatalogen skapar du en JSON-fil med det här innehållet med namnet `Adfv2QuickStartPipeline.json` :

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

1. Skapa en pipeline med `Adfv2QuickStartPipeline` namnet med kommandot az [datafactory pipeline](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create) create:

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Kör pipelinen med kommandot [az datafactory pipeline create-run:](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run)

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Det här kommandot returnerar ett körnings-ID. Kopiera den för användning i nästa kommando.

1. Kontrollera att pipelinekörningen lyckades med kommandot [az datafactory pipeline-run show:](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show)

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Du kan också kontrollera att pipelinen kördes som förväntat med hjälp av [Azure Portal](https://portal.azure.com/). Mer information finns i [Granska distribuerade resurser.](quickstart-create-data-factory-powershell.md#review-deployed-resources)

## <a name="clean-up-resources"></a>Rensa resurser

Alla resurser i den här snabbstarten ingår i samma resursgrupp. Om du vill ta bort alla använder du [kommandot az group delete:](/cli/azure/group#az_group_delete)

```azurecli
az group delete --name ADFQuickStartRG
```

Om du använder den här resursgruppen för något annat tar du i stället bort enskilda resurser. Om du till exempel vill ta bort den länkade tjänsten använder du [kommandot az datafactory linked-service delete.](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete)

I den här snabbstarten skapade du följande JSON-filer:

- AzureStorageLinkedService.jspå
- InputDataset.jspå
- OutputDataset.jspå
- Adfv2QuickStartPipeline.jspå

Ta bort dem med hjälp av bash-standardkommandon.

## <a name="next-steps"></a>Nästa steg

- [Pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md)
- [Det finns inte några länkade tjänster i Azure Data Factory](concepts-linked-services.md)
- [Datauppsättningar i Azure Data Factory](concepts-datasets-linked-services.md)
- [Transformera data i Azure Data Factory](transform-data.md)
