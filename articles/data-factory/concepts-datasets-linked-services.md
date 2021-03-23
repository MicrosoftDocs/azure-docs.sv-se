---
title: Datauppsättningar
description: Lär dig mer om data uppsättningar i Data Factory. Data uppsättningar representerar indata/utdata.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 167fb0014f2f0a9e7a2530fe276289f94347146e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785792"
---
# <a name="datasets-in-azure-data-factory"></a>Datauppsättningar i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuell version](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


I den här artikeln beskrivs vilka data uppsättningar som är, hur de definieras i JSON-format och hur de används i Azure Data Factory pipelines.

Om du är nybörjare på Data Factory, se [Introduktion till Azure Data Factory](introduction.md) för en översikt.

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en aktivitet. Aktiviteterna i en pipeline definierar åtgärder som ska utföras på dina data. Nu är en **data uppsättning** en namngiven vy av data som bara pekar eller refererar till de data som du vill använda i dina **aktiviteter** som indata och utdata. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. En Azure Blob-datauppsättning anger till exempel blobcontainern och mappen i Blob Storage som aktiviteten ska läsa data från.

Innan du skapar en data uppsättning måste du skapa en [**länkad tjänst**](concepts-linked-services.md) för att länka ditt data lager till data fabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Tänk på det på det här sättet. data uppsättningen representerar strukturen för data i de länkade data lagringarna och den länkade tjänsten definierar anslutningen till data källan. Till exempel länkar en Azure Storage länkad tjänst ett lagrings konto till data fabriken. En Azure Blob-datauppsättning representerar BLOB-behållaren och mappen i Azure Storage-kontot som innehåller de blobar för indata som ska bearbetas.

Här är ett exempel scenario. Om du vill kopiera data från Blob Storage till en SQL Database skapar du två länkade tjänster: Azure Blob Storage och Azure SQL Database. Skapa sedan två data uppsättningar: avgränsad text data uppsättning (som refererar till Azure Blob Storage länkade tjänsten, förutsatt att du har textfiler som källa) och data uppsättningen för Azure SQL-tabellen (som refererar till den Azure SQL Database länkade tjänsten). Azure Blob Storage-och Azure SQL Database länkade tjänster innehåller anslutnings strängar som Data Factory använder vid körning för att ansluta till dina Azure Storage respektive Azure SQL Database. Den avgränsade text data uppsättningen anger BLOB-behållaren och blob-mappen som innehåller blobar för indata i blob-lagringen, tillsammans med formatbaserade inställningar. Data uppsättningen för Azure SQL-tabellen anger den SQL-tabell i SQL Database som data ska kopieras till.

Följande diagram visar relationerna mellan pipeline, aktivitet, data uppsättning och länkad tjänst i Data Factory:

![Relation mellan pipeline, aktivitet, data uppsättning, länkade tjänster](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Data mängds-JSON
En data uppsättning i Data Factory definieras i följande JSON-format:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
I följande tabell beskrivs egenskaperna i ovanstående JSON:

Egenskap | Beskrivning | Obligatorisk |
-------- | ----------- | -------- |
name | Data uppsättningens namn. Se [Azure Data Factory namngivnings regler](naming-rules.md). |  Ja |
typ | Typ av data uppsättning. Ange en av de typer som stöds av Data Factory (till exempel: DelimitedText, AzureSqlTable). <br/><br/>Mer information finns i [data uppsättnings typer](#dataset-type). | Ja |
schema | Schemat för data uppsättningen representerar den fysiska data typen och formen. | Inga |
typeProperties | Typ egenskaperna är olika för varje typ. Mer information om de typer som stöds och deras egenskaper finns i [data uppsättnings typ](#dataset-type). | Ja |

När du importerar schemat för data uppsättningen väljer du knappen **Importera schema** och väljer att importera från källan eller från en lokal fil. I de flesta fall importerar du schemat direkt från källan. Men om du redan har en lokal schema fil (en Parquet-fil eller en CSV-fil med rubriker) kan du direkt Data Factory för att basera schemat på filen.

I kopierings aktiviteten används data uppsättningar i källa och mottagare. Schemat som definierats i data uppsättningen är valfritt som referens. Om du vill använda mappning mellan kolumner och fält mellan källa och mottagare, se [schema och typ mappning](copy-activity-schema-and-type-mapping.md).

I data flöde används data uppsättningar i käll-och mottagar omvandlingar. Data uppsättningarna definierar de grundläggande data scheman. Om dina data saknar schema kan du använda schema avvikelser för din källa och mottagare. Metadata från data uppsättningarna visas i käll omvandlingen som käll projektion. Projektionen i käll omvandlingen representerar data flödes data med definierade namn och typer.

## <a name="dataset-type"></a>Data uppsättnings typ

Azure Data Factory stöder många olika typer av data uppsättningar, beroende på vilka data lager du använder. Du hittar listan över data lager som stöds av Data Factory från [anslutnings översikt](connector-overview.md) artikeln. Klicka på ett data lager om du vill lära dig hur du skapar en länkad tjänst och en data uppsättning.

För en avgränsad text data uppsättning anges till exempel data uppsättnings typen till **DelimitedText** som visas i följande JSON-exempel:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>Skapa datauppsättningar
Du kan skapa data uppsättningar genom att använda något av dessa verktyg eller SDK: [er: .NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager mall och Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Aktuella versioner jämfört med version 1-datauppsättningar

Här följer några skillnader mellan Data Factory och Data Factory data uppsättningar för version 1:

- Den externa egenskapen stöds inte i den aktuella versionen. Den ersätts av en [utlösare](concepts-pipeline-execution-triggers.md).
- Egenskaperna policy och Availability stöds inte i den aktuella versionen. Start tiden för en pipeline beror på [utlösare](concepts-pipeline-execution-triggers.md).
- Data uppsättningar som har definierats i en pipeline stöds inte i den aktuella versionen.

## <a name="next-steps"></a>Nästa steg
I följande självstudie finns stegvisa anvisningar för hur du skapar pipelines och data uppsättningar med hjälp av något av dessa verktyg eller SDK: er.

- [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md)
- [Snabb start: skapa en data fabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md)
- [Snabb start: skapa en data fabrik med hjälp av REST API](quickstart-create-data-factory-rest-api.md)
- [Snabb start: skapa en data fabrik med hjälp av Azure Portal](quickstart-create-data-factory-portal.md)
