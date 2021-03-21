---
title: SQL Server data som ska SQL Database med Azure Data Factory team data science process
description: Konfigurera en ADF-pipeline som består av två data migrerings aktiviteter som tillsammans flyttar data dagligen mellan databaser lokalt och i molnet.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e477e4bb3b31477f9407e981d4c8da2340411f55
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615745"
---
# <a name="move-data-from-a-sql-server-database-to-sql-database-with-azure-data-factory"></a>Flytta data från en SQL Server databas till SQL Database med Azure Data Factory

Den här artikeln visar hur du flyttar data från en SQL Server databas till Azure SQL Database via Azure Blob Storage med hjälp av Azure Data Factory (ADF): den här metoden är en äldre metod som stöds och som har fördelarna med en replikerad mellanlagrings kopia, men [vi föreslår att titta på vår datamigrerings sida för de senaste alternativen](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

En tabell som sammanfattar olika alternativ för att flytta data till en Azure SQL Database finns i [Flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Introduktion: Vad är ADF och när ska den användas för att migrera data?
Azure Data Factory är en helt hanterad molnbaserad data integrerings tjänst som dirigerar och automatiserar flytt och transformering av data. Det viktigaste konceptet i ADF-modellen är pipelinen. En pipeline är en logisk gruppering av aktiviteter som definierar de åtgärder som ska utföras på data som finns i data uppsättningar. Länkade tjänster används för att definiera den information som krävs för att Data Factory ska kunna ansluta till data resurserna.

Med ADF kan befintliga data bearbetnings tjänster bestå av data pipelines som är hög tillgängliga och som hanteras i molnet. De här data pipelinen kan schemaläggas för att mata in, förbereda, transformera, analysera och publicera data, och ADF hanterar och dirigerar komplexa data och bearbetnings beroenden. Lösningar kan snabbt skapas och distribueras i molnet, så att du kan ansluta ett växande antal lokala och molnbaserade data källor.

Överväg att använda ADF:

* När data måste migreras kontinuerligt i ett hybrid scenario som har åtkomst till både lokala och molnbaserade resurser
* När data behöver transformationer eller om affärs logik ska läggas till i den när den migreras.

Med ADF kan du schemalägga och övervaka jobb med hjälp av enkla JSON-skript som hanterar data flytten på regelbunden basis. ADF har också andra funktioner, till exempel stöd för komplexa åtgärder. Mer information om ADF finns i dokumentationen på [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="the-scenario"></a><a name="scenario"></a>Scenariot
Vi konfigurerar en ADF-pipeline som består av två data migrerings aktiviteter. Tillsammans flyttar de data dagligen mellan en SQL Server databas och Azure SQL Database. De två aktiviteterna är:

* Kopiera data från en SQL Server-databas till ett Azure Blob Storage-konto
* Kopiera data från Azure Blob Storage-kontot till Azure SQL Database.

> [!NOTE]
> Stegen som visas här har anpassats från den mer detaljerade självstudien som tillhandahålls av ADF-teamet: [Kopiera data från en SQL Server databas till Azure Blob Storage](../../data-factory/tutorial-hybrid-copy-portal.md) -referenser till de relevanta avsnitten i det här avsnittet, om det är lämpligt.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Förutsättningar
Den här självstudien förutsätter att du har:

* En **Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/pricing/free-trial/).
* Ett **Azure Storage-konto**. Du använder ett Azure Storage-konto för att lagra data i den här självstudien. Om du inte har ett Azure Storage-konto går du till artikeln [skapa ett lagrings konto](../../storage/common/storage-account-create.md) . När du har skapat lagrings kontot måste du hämta den konto nyckel som används för att få åtkomst till lagringen. Se [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).
* Åtkomst till en **Azure SQL Database**. Om du måste konfigurera en Azure SQL Database innehåller avsnittet [komma igång med Microsoft Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md) information om hur du etablerar en ny instans av en Azure SQL Database.
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

> [!NOTE]
> Den här proceduren använder [Azure Portal](https://portal.azure.com/).
>
>

## <a name="upload-the-data-to-your-sql-server-instance"></a><a name="upload-data"></a> Ladda upp data till SQL Server-instansen
Vi använder [NYC taxi-datauppsättningen](https://chriswhong.com/open-data/foil_nyc_taxi/) för att demonstrera migreringsprocessen. NYC taxi-datauppsättningen är tillgänglig, enligt vad som anges i det inlägget, på Azure Blob Storage [NYC taxi-data](https://www.andresmh.com/nyctaxitrips/). Data har två filer, trip_data.csv-filen, som innehåller information om resan och trip_far.csv-filen, som innehåller information om avgiften som betalats för varje resa. Ett exempel på och en beskrivning av dessa filer finns i [Beskrivning av NYC taxi TRIPs-data uppsättning](sql-walkthrough.md#dataset).

Du kan antingen anpassa proceduren som visas här till en uppsättning egna data eller följa stegen som beskrivs i använda NYC taxi-datauppsättningen. Om du vill överföra NYC taxi-datauppsättningen till SQL Server databasen följer du proceduren som beskrivs i [Mass import av data i SQL Server Database](sql-walkthrough.md#dbload).

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a> Skapa en Azure Data Factory
Instruktioner för att skapa en ny Azure Data Factory och en resurs grupp i [Azure Portal](https://portal.azure.com/) anges [skapa en Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Namnge den nya ADF-instansen *adfdsp* och namnge resurs gruppen som skapade *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Installera och konfigurera Azure Data Factory Integration Runtime
Integration Runtime är en kundhanterad infrastruktur för data integrering som används av Azure Data Factory för att tillhandahålla funktioner för data integrering i olika nätverks miljöer. Den här körningen kallades tidigare för Data Management Gateway.

Konfigurera genom att [följa anvisningarna för att skapa en pipeline](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Skapa länkade tjänster för att ansluta till data resurserna
En länkad tjänst definierar den information som krävs för att Azure Data Factory ska kunna ansluta till en data resurs. Vi har tre resurser i det här scenariot för vilka länkade tjänster behövs:

1. Lokala SQL Server
2. Azure Blob Storage
3. Azure SQL Database

Steg för steg-proceduren för att skapa länkade tjänster finns i [Skapa länkade tjänster](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Definiera och skapa tabeller för att ange hur du ska få åtkomst till data uppsättningarna
Skapa tabeller som anger strukturen, platsen och tillgängligheten för data uppsättningarna med följande skriptbaserade procedurer. JSON-filer används för att definiera tabeller. Mer information om strukturen för dessa filer finns i [data uppsättningar](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Du bör köra `Add-AzureAccount` cmdleten innan du kör cmdleten [New-AzureDataFactoryTable](/previous-versions/azure/dn835096(v=azure.100)) för att bekräfta att rätt Azure-prenumeration har valts för kommando körningen. Dokumentation av den här cmdleten finns i [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount).
>
>

De JSON-baserade definitionerna i tabellerna använder följande namn:

* **tabell namnet** i SQL Server är *nyctaxi_data*
* **behållar namnet** i Azure Blob Storage-kontot är *ContainerName*

Tre tabell definitioner krävs för den här ADF-pipeline:

1. [SQL lokalt tabell](#adf-table-onprem-sql)
2. [BLOB-tabell](#adf-table-blob-store)
3. [SQL Azure tabell](#adf-table-azure-sql)

> [!NOTE]
> Dessa procedurer använder Azure PowerShell för att definiera och skapa ADF-aktiviteter. Men dessa uppgifter kan också utföras med hjälp av Azure Portal. Mer information finns i [skapa data uppsättningar](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>SQL lokalt tabell
Tabell definitionen för SQL Server anges i följande JSON-fil:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Kolumn namnen ingår inte här. Du kan välja att markera kolumn namnen genom att inkludera dem här (mer information finns i artikeln om [dokumentation om ADF](../../data-factory/copy-activity-overview.md) .

Kopiera JSON-definitionen för tabellen till en fil med namnet *onpremtabledef.jspå* filen och spara den på en känd plats (här förutsätts vara *C:\temp\onpremtabledef.jspå*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json
```


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>BLOB-tabell
Definitionen för tabellen för BLOB-platsen för utdata är i följande (detta mappar inmatade data från lokalt till Azure-bloben):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopiera JSON-definitionen för tabellen till en fil med namnet *bloboutputtabledef.jspå* filen och spara den på en känd plats (här förutsätts vara *C:\temp\bloboutputtabledef.jspå*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json
```

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>SQL Azure tabell
Definitionen för tabellen för SQL Azure utdata finns i följande (det här schemat mappar data som kommer från blobben):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopiera JSON-definitionen för tabellen till en fil med namnet *AzureSqlTable.jspå* filen och spara den på en känd plats (här förutsätts vara *C:\temp\AzureSqlTable.jspå*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json
```


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>Definiera och skapa pipelinen
Ange de aktiviteter som tillhör pipelinen och skapa pipelinen med följande skriptbaserade procedurer. En JSON-fil används för att definiera egenskaperna för pipelinen.

* Skriptet förutsätter att **pipelin namnet** är *AMLDSProcessPipeline*.
* Observera också att vi anger periodiciteten för pipelinen som ska köras dagligen och använder standard körnings tiden för jobbet (12 FM UTC).

> [!NOTE]
> I följande procedurer används Azure PowerShell för att definiera och skapa en ADF-pipeline. Men den här uppgiften kan också utföras med hjälp av Azure Portal. Mer information finns i [skapa pipeline](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Med de tabell definitioner som tillhandahölls tidigare, anges pipelinen för ADF enligt följande:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has two activities: the first one copies data from SQL Server to Azure Blob, and the second one copies from Azure Blob to Azure Database Table",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from SQL Server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Kopiera den här JSON-definitionen för pipelinen till en fil *med namnetpipelinedef.jspå* filen och spara den på en känd plats (här förutsätts vara *C:\temp\pipelinedef.jspå*). Skapa pipelinen i ADF med följande Azure PowerShell-cmdlet:

```azurepowershell
New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json
```


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>Starta pipelinen
Pipelinen kan nu köras med följande kommando:

```azurepowershell
Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline
```

Parameter värden för *StartDate* och *EndDate* måste ersättas med de faktiska datum som du vill att pipelinen ska köras mellan.

När pipelinen har körts bör du kunna se vilka data som visas i den behållare som valts för blobben, en fil per dag.

Vi har inte utnyttjat funktionerna från ADF för att skicka data stegvis. Mer information om hur du gör detta och andra funktioner som tillhandahålls av ADF finns i [dokumentationen till ADF](https://azure.microsoft.com/services/data-factory/).