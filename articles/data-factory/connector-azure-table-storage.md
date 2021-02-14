---
title: Kopiera data till och från Azure Table Storage
description: Lär dig hur du kopierar data från käll arkiv som stöds till Azure Table Storage, eller från Table Storage till mottagar mottagar lager med hjälp av Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/20/2020
ms.openlocfilehash: 1448bb3ebf541af047759bc886b2c174d89c1c50
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383780"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopiera data till och från Azure Table Storage med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-table-connector.md)
> * [Aktuell version](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory till att kopiera data från och till Azure Table Storage. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Table Storage-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från alla käll data lager som stöds till Table Storage. Du kan också kopiera data från Table Storage till alla mottagar data lager som stöds. En lista över data lager som stöds som källor eller mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder Azure Table Connector kopiering av data med hjälp av autentisering med konto nyckel och autentisering med delad åtkomst för signaturer.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Table Storage.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

### <a name="use-an-account-key"></a>Använd en konto nyckel

Du kan skapa en Azure Storage länkad tjänst genom att använda konto nyckeln. Den ger data fabriken global åtkomst till lagringen. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureTableStorage**. |Ja |
| Begär | Ange den information som behövs för att ansluta till lagringen för egenskapen connectionString. <br/>Du kan också ange konto nyckeln i Azure Key Vault och hämta `accountKey` konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Inga |

>[!NOTE]
>Om du använder den länkade tjänsten "AzureStorage"-typ, stöds den fortfarande som den är, men du rekommenderas att använda den här nya länkade tjänst typen "AzureTableStorage".

**Exempel:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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

**Exempel: lagrings konto nyckel i Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-shared-access-signature-authentication"></a>Använd autentisering med signatur för delad åtkomst

Du kan också skapa en länkad lagrings tjänst genom att använda en signatur för delad åtkomst. Den tillhandahåller data fabriken med begränsad/tidsbegränsad åtkomst till alla/vissa resurser i lagrings platsen.

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagrings konto. Du kan använda den för att ge en klient begränsad behörighet till objekt i ditt lagrings konto under en angiven tid och med en angiven uppsättning behörigheter. Du behöver inte dela dina konto åtkomst nycklar. Signaturen för delad åtkomst är en URI som omfattar den information som krävs för autentiserad åtkomst till en lagrings resurs i dess frågeparametrar. För att få åtkomst till lagrings resurser med signaturen för delad åtkomst måste klienten bara skicka in signaturen för delad åtkomst till lämplig konstruktor eller metod. Mer information om signaturer för delad åtkomst finns i signaturer för delad åtkomst [: förstå signatur modellen för delad åtkomst](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Data Factory stöder nu både signaturer för **delad åtkomst för tjänsten** och **kontots delade åtkomst-signaturer**. Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md). 

> [!TIP]
> Om du vill skapa en signatur för delad åtkomst för tjänsten för ditt lagrings konto kan du köra följande PowerShell-kommandon. Ersätt plats hållarna och bevilja nödvändig behörighet.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Följande egenskaper stöds om du vill använda autentisering med signaturer för delad åtkomst.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureTableStorage**. |Ja |
| sasUri | Ange SAS-URI för signatur-URI: n för delad åtkomst till tabellen. <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ställa in SAS-token i Azure Key Vault för att dra nytta av automatisk rotation och ta bort token-delen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller den lokala Integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Inga |

>[!NOTE]
>Om du använder den länkade tjänsten "AzureStorage"-typ, stöds den fortfarande som den är, men du rekommenderas att använda den här nya länkade tjänst typen "AzureTableStorage".

**Exempel:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagrings konto nyckel i Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

När du skapar en signatur-URI för delad åtkomst bör du tänka på följande:

- Ange lämpliga Läs-/skriv behörigheter för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsa/skriva) används i din data fabrik.
- Ange **förfallo tid** på lämpligt sätt. Se till att åtkomsten till lagrings objekt upphör att gälla inom den aktiva perioden för pipelinen.
- URI: n måste skapas på rätt tabell nivå baserat på behovet.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Table-datauppsättningen.

Om du vill kopiera data till och från Azure-tabellen anger du egenskapen type för data uppsättningen till **AzureTable**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **AzureTable**. |Ja |
| tableName |Namnet på tabellen i den tabell lagrings databas instans som den länkade tjänsten refererar till. |Ja |

**Exempel:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema efter Data Factory

Data Factory härleder schemat på något av följande sätt för schema fria data lager, till exempel Azure Table:

* Om du anger kolumn mappningen i kopierings aktiviteten, kan Data Factory använda kolumn listan på käll sidan för att hämta data. I det här fallet anges ett null-värde för det om en rad inte innehåller något värde för en kolumn.
* Om du inte anger kolumn mappningen i kopierings aktiviteten härleder Data Factory schemat genom att använda den första raden i data. I detta fall, om den första raden inte innehåller det fullständiga schemat (t. ex. om vissa kolumner har null-värde), saknas vissa kolumner i resultatet av kopierings åtgärden.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azures tabell källa och mottagare.

### <a name="azure-table-as-a-source-type"></a>Azure-tabell som käll typ

Om du vill kopiera data från Azure-tabellen anger du käll typen i kopierings aktiviteten till **AzureTableSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **AzureTableSource**. |Ja |
| azureTableSourceQuery |Använd den anpassade tabell lagrings frågan för att läsa data.<br/>Käll frågan är en direkt karta från `$filter` frågealternativet som stöds av Azure Table Storage, lär dig mer om syntaxen från [det här dokumentet](/rest/api/storageservices/querying-tables-and-entities#supported-query-options)och se exemplen i följande azureTableSourceQuery- [exempel avsnitt](#azuretablesourcequery-examples). |Inga |
| azureTableSourceIgnoreTableNotFound |Anger om undantag för tabellen inte finns.<br/>Tillåtna värden är **True** och **false** (standard). |Inga |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-exempel

>[!NOTE]
>Azure Table Query-åtgärden nådde tids gränsen på 30 sekunder som [tillämpas av azure Table service](/rest/api/storageservices/setting-timeouts-for-table-service-operations). Lär dig hur du optimerar frågan från [design för att fråga](../storage/tables/table-storage-design-for-query.md) artikeln.

I Azure Data Factory, om du vill filtrera data mot en kolumn av typen datetime, se följande exempel:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Om du vill filtrera data mot en sträng typ kolumn, se följande exempel:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Om du använder pipeline-parametern, omvandla datetime-värdet till korrekt format enligt föregående exempel.

### <a name="azure-table-as-a-sink-type"></a>Azure-tabell som mottagar typ

Om du vill kopiera data till Azure-tabellen ställer du in mottagar typen i kopierings aktiviteten till **AzureTableSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **AzureTableSink**. |Ja |
| azureTableDefaultPartitionKeyValue |Standardvärdet för partitionerings nyckel som kan användas av mottagaren. |Inga |
| azureTablePartitionKeyName |Ange namnet på den kolumn vars värden används som partitionsnyckel. Om inget anges används "AzureTableDefaultPartitionKeyValue" som partitionsnyckel. |Inga |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumn värden används som rad nyckel. Om inget anges ska du använda ett GUID för varje rad. |Inga |
| azureTableInsertType |Det läge där data ska infogas i Azure-tabellen. Den här egenskapen anger om befintliga rader i utdatatabellen med matchande partition och rad nycklar har ersatts eller slås samman. <br/><br/>Tillåtna värden är **sammanfoga** (standard) och **Ersätt**. <br/><br> Den här inställningen gäller för rad nivån inte tabell nivån. Inget alternativ tar bort rader i den utgående tabell som inte finns i indata. Information om hur inställningarna för att sammanfoga och ersätta fungerar finns i [Infoga eller sammanfoga entitet](/rest/api/storageservices/Insert-Or-Merge-Entity) och [Infoga eller Ersätt entitet](/rest/api/storageservices/Insert-Or-Replace-Entity). |Inga |
| writeBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout har nåtts.<br/>Tillåtna värden är heltal (antal rader). |Nej (standard är 10 000) |
| writeBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout har nåtts.<br/>Tillåtna värden är TimeSpan. Ett exempel är "00:20:00" (20 minuter). |Nej (standard är 90 sekunder, lagrings klientens standard-timeout) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Mappa en käll kolumn till en mål kolumn med hjälp av egenskapen **"Translator"** innan du kan använda mål kolumnen som azureTablePartitionKeyName.

I följande exempel mappas käll kolumnens DivisionID till mål kolumnen DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" har angetts som partitionsnyckel.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Data typs mappning för Azure-tabell

När du kopierar data från och till Azure-tabellen används följande mappningar från Azure Table data types för att Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren finns i [schema-och data typs mappningar](copy-activity-schema-and-type-mapping.md).

När du flyttar data till och från Azure-tabellen används följande [mappningar som definieras av Azure](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) Table från OData-typer i Azure-typ till .net-typ och vice versa.

| Data typen Azure Table | Data Factory data typen Interim | Information |
|:--- |:--- |:--- |
| EDM. Binary |byte[] |En matris med byte upp till 64 KB. |
| Edm.Boolean |boolesk |Ett booleskt värde. |
| EDM. DateTime |DateTime |Ett 64-bitars värde uttryckt som UTC (Coordinated Universal Time). Det DateTime-intervall som stöds påbörjar midnatt, 1 januari 1601 A.D. (C.E.), UTC. Intervallet slutar den 31 december 9999. |
| Edm.Double |double |Ett 64-bitars flytt ALS värde. |
| EDM. GUID |GUID |En 128-bitars globalt unik identifierare. |
| Edm.Int32 |Int32 |Ett 32-bitars heltal. |
| Edm.Int64 |Int64 |Ett 64-bitars heltal. |
| Edm.String |Sträng |Ett UTF-16-kodat värde. Sträng värden kan vara upp till 64 KB. |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).