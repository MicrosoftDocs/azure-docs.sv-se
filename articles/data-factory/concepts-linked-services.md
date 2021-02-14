---
title: Det finns inte några länkade tjänster i Azure Data Factory
description: Lär dig mer om länkade tjänster i Data Factory. Länkade tjänster länkar beräknings-/data lager till Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 768af9e8568a3842b642a3ebd447d9df8064e840
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389934"
---
# <a name="linked-services-in-azure-data-factory"></a>Det finns inte några länkade tjänster i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuell version](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs vilka länkade tjänster som är, hur de är definierade i JSON-format och hur de används i Azure Data Factory pipelines.

Om du inte har använt Data Factory, se [Introduktion till Azure Data Factory](introduction.md) för en översikt.

## <a name="overview"></a>Översikt

En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en aktivitet. Aktiviteterna i en pipeline definierar åtgärder som ska utföras på dina data. Du kan till exempel använda en kopierings aktivitet för att kopiera data från SQL Server till Azure Blob Storage. Sedan kan du använda en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster för att bearbeta data från Blob Storage för att skapa utdata. Slutligen kan du använda en andra kopierings aktivitet för att kopiera utmatnings data till Azure Synapse Analytics, som bygger på de rapporterande lösningarna för Business Intelligence (BI). Mer information om pipelines och aktiviteter finns i [pipeline och aktiviteter](concepts-pipelines-activities.md) i Azure Data Factory.

Nu är en **data uppsättning** en namngiven vy av data som bara pekar eller refererar till de data som du vill använda i dina **aktiviteter** som indata och utdata.

Innan du skapar en data uppsättning måste du skapa en **länkad tjänst** för att länka ditt data lager till data fabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Tänk på det på det här sättet. data uppsättningen representerar strukturen för data i de länkade data lagringarna och den länkade tjänsten definierar anslutningen till data källan. Till exempel länkar en Azure Storage länkad tjänst ett lagrings konto till data fabriken. En Azure Blob-datauppsättning representerar BLOB-behållaren och mappen i Azure Storage-kontot som innehåller de blobar för indata som ska bearbetas.

Här är ett exempel scenario. Om du vill kopiera data från Blob Storage till en SQL Database skapar du två länkade tjänster: Azure Storage och Azure SQL Database. Skapa sedan två data uppsättningar: Azure Blob-datauppsättningen (som refererar till den Azure Storage länkade tjänsten) och data uppsättningen för Azure SQL-tabellen (som refererar till den Azure SQL Database länkade tjänsten). De länkade tjänsterna Azure Storage och Azure SQL Database innehåller anslutnings strängar som Data Factory använder vid körning för att ansluta till dina Azure Storage respektive Azure SQL Database. Azure Blob-datauppsättningen anger BLOB-behållaren och blob-mappen som innehåller blobar för indata i blob-lagringen. Data uppsättningen för Azure SQL-tabellen anger den SQL-tabell i SQL Database som data ska kopieras till.

Följande diagram visar relationerna mellan pipeline, aktivitet, data uppsättning och länkad tjänst i Data Factory:

![Relation mellan pipeline, aktivitet, data uppsättning, länkade tjänster](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Länkad tjänst-JSON

En länkad tjänst i Data Factory definieras i JSON-format enligt följande:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

I följande tabell beskrivs egenskaperna i ovanstående JSON:

Egenskap | Beskrivning | Obligatorisk |
-------- | ----------- | -------- |
name | Namnet på den länkade tjänsten. Se [Azure Data Factory namngivnings regler](naming-rules.md). |  Ja |
typ | Typ av länkad tjänst. Till exempel: AzureBlobStorage (data Store) eller AzureBatch (Compute). Se beskrivningen av typeProperties. | Ja |
typeProperties | Typ egenskaperna är olika för varje data lager eller beräkning. <br/><br/> Information om vilka data lagrings typer som stöds och deras typ egenskaper finns i artikeln [Översikt över koppling](copy-activity-overview.md#supported-data-stores-and-formats) . Gå till artikeln data Store Connector om du vill veta mer om typ egenskaper som är speciella för ett data lager. <br/><br/> För beräknings typer som stöds och deras typ egenskaper, se [Compute-länkade tjänster](compute-linked-services.md). | Ja |
connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure Integration Runtime. | Inga

## <a name="linked-service-example"></a>Exempel på länkad tjänst

Följande länkade tjänst är en länkad Azure Blob Storage-tjänst. Observera att typen är inställt på Azure Blob Storage. Typ egenskaperna för den länkade Azure Blob Storage-tjänsten innehåller en anslutnings sträng. Den Data Factory tjänsten använder den här anslutnings strängen för att ansluta till data lagret vid körning.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Skapa länkade tjänster

Länkade tjänster kan skapas i Azure Data Factory UX via [hanterings hubben](author-management-hub.md) och eventuella aktiviteter, data uppsättningar eller data flöden som refererar till dem.

Du kan skapa länkade tjänster med något av dessa verktyg eller SDK: [er: .NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager mall och Azure Portal.


## <a name="data-store-linked-services"></a>Länkade tjänster för data lager

Du hittar listan över data lager som stöds av Data Factory från [anslutnings översikt](copy-activity-overview.md#supported-data-stores-and-formats) artikeln. Klicka på ett data lager om du vill veta vilka anslutnings egenskaper som stöds.

## <a name="compute-linked-services"></a>Länkade tjänster för Compute

Referens [beräknings miljöer som stöds](compute-linked-services.md) för information om olika beräknings miljöer som du kan ansluta till från din data fabrik samt de olika konfigurationerna.

## <a name="next-steps"></a>Nästa steg

I följande självstudie finns stegvisa anvisningar för hur du skapar pipelines och data uppsättningar med hjälp av något av dessa verktyg eller SDK: er.

- [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md)
- [Snabb start: skapa en data fabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md)
- [Snabb start: skapa en data fabrik med hjälp av REST API](quickstart-create-data-factory-rest-api.md)
- [Snabb start: skapa en data fabrik med hjälp av Azure Portal](quickstart-create-data-factory-portal.md)
