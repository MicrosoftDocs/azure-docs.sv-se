---
title: Använd Resource Manager-mallar i Data Factory
description: Lär dig hur du skapar och använder Azure Resource Manager mallar för att skapa Data Factory entiteter.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c17ef939b8d27035e5b28f4a651b9be2db8f2279
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382947"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Använda mallar för att skapa Azure Data Factory entiteter
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

## <a name="overview"></a>Översikt
När du använder Azure Data Factory för dina data integrerings behov kan du behöva återanvända samma mönster i olika miljöer eller implementera samma uppgift upprepade gånger inom samma lösning. Med hjälp av mallar kan du implementera och hantera dessa scenarier på ett enkelt sätt. Mallar i Azure Data Factory är idealiska för scenarier som involverar åter användning och upprepning.

Ta hänsyn till den situation där en organisation har 10 tillverknings anläggningar över hela världen. Loggarna från varje anläggning lagras i en separat SQL Server databas. Företaget vill bygga ett enda informations lager i molnet för ad hoc-analys. Det vill också ha samma logik men olika konfigurationer för utvecklings-, test-och produktions miljöer.

I det här fallet måste en uppgift upprepas inom samma miljö, men med olika värden i de 10 data fabrikerna för varje tillverknings anläggning. I praktiken finns **upprepning** . Mall tillåter abstraktion av detta generiska flöde (det vill säga pipelines med samma aktiviteter i varje data fabrik), men använder en separat parameter fil för varje tillverknings anläggning.

I takt med att organisationen vill distribuera dessa 10 data fabriker flera gånger i olika miljöer, kan mallarna använda den här **återanvändningen** genom att använda separata parameter-filer för utveckling, testning och produktions miljöer.

## <a name="templating-with-azure-resource-manager"></a>Mall med Azure Resource Manager
[Azure Resource Manager mallar](../../azure-resource-manager/templates/overview.md) är ett bra sätt att uppnå mall i Azure Data Factory. Resource Manager-mallar definierar infrastrukturen och konfigurationen av din Azure-lösning via en JSON-fil. Eftersom Azure Resource Manager mallar fungerar med alla/de flesta Azure-tjänster, kan det vara mycket enkelt att hantera alla resurser i dina Azure-tillgångar. Läs mer om Resource Manager-mallarna i allmänhet i [redigera Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md) .

## <a name="tutorials"></a>Självstudier
I följande själv studie kurser finns stegvisa anvisningar för hur du skapar Data Factory entiteter med hjälp av Resource Manager-mallar:

* [Självstudie: skapa en pipeline för att kopiera data med hjälp av Azure Resource Manager mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Självstudie: skapa en pipeline för att bearbeta data med hjälp av Azure Resource Manager mall](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory mallar på GitHub
Kolla in följande Azure snabb starts mallar på GitHub:

* [Skapa en data fabrik för att kopiera data från Azure Blob Storage till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Skapa en data fabrik med Hive-aktivitet i Azure HDInsight-kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Skapa en data fabrik för att kopiera data från Salesforce till Azure-blobbar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Skapa en data fabrik som bevarar aktiviteter: kopierar data från en FTP-server till Azure-blobbar, anropar ett Hive-skript på ett HDInsight-kluster på begäran för att transformera data och kopiera resultatet till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Du kan dela Azure Data Factory mallar i [Azure snabb start](https://azure.microsoft.com/documentation/templates/). Se [bidrags hand boken](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) när du utvecklar mallar som kan delas via den här lagrings platsen.

I följande avsnitt finns information om hur du definierar Data Factory resurser i en Resource Manager-mall.

## <a name="defining-data-factory-resources-in-templates"></a>Definiera Data Factory resurser i mallar
Mallen på den översta nivån för att definiera en data fabrik är:

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definiera en datafabrik
Du definierar en datafabrik i Resource Manager-mallen enligt följande exempel:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
DataFactoryName definieras i "variabler" som:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definiera länkade tjänster

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Mer information om JSON-egenskaperna för den länkade tjänst som du vill distribuera finns i länkade tjänster för [lagring](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller Compute- [tjänster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Parametern "dependsOn" anger namnet på motsvarande data fabrik. Ett exempel på att definiera en länkad tjänst för Azure Storage visas i följande JSON-definition:

### <a name="define-datasets"></a>Definiera data uppsättningar

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Se [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) för information om JSON-egenskaperna för den angivna data mängds typ som du vill distribuera. Observera att parametern "dependsOn" anger namnet på den motsvarande länkade tjänsten för Data Factory och lagring. Ett exempel på hur du definierar data uppsättnings typ för Azure Blob Storage visas i följande JSON-definition:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Definiera pipeliner

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

I [definiera pipeliner](data-factory-create-pipelines.md#pipeline-json) finns mer information om JSON-egenskaperna för att definiera den aktuella pipelinen och de aktiviteter som du vill distribuera. Observera att parametern "dependsOn" anger namnet på data fabriken och eventuella motsvarande länkade tjänster eller data uppsättningar. Ett exempel på en pipeline som kopierar data från Azure Blob Storage till Azure SQL Database visas i följande JSON-kodfragment:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Parametriserad Data Factory mall
Metod tips för hur du använder parametrar finns i [metod tips för att skapa Azure Resource Manager mallar](../../azure-resource-manager/templates/template-best-practices.md). I allmänhet bör parameter användningen minimeras, särskilt om variabler kan användas i stället. Ange endast parametrar i följande scenarier:

* Inställningarna varierar beroende på miljö (exempel: utveckling, testning och produktion)
* Hemligheter (till exempel lösen ord)

Om du behöver hämta hemligheter från [Azure Key Vault](../../key-vault/general/overview.md) när du distribuerar Azure Data Factory entiteter med hjälp av mallar, anger du **nyckel valvet** och det **hemliga namnet** som visas i följande exempel:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Det finns för närvarande inte stöd för att exportera mallar för befintliga data fabriker, men det finns i Works.
>
>