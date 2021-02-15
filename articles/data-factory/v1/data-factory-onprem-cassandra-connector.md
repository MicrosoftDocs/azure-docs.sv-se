---
title: Flytta data från Cassandra med hjälp av Data Factory
description: Lär dig mer om hur du flyttar data från en lokal Cassandra-databas med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 005fd85a152ee2765facda0d961bd9119d1598e8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387418"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Flytta data från en lokal Cassandra-databas med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-cassandra-connector.md)
> * [Version 2 (aktuell version)](../connector-cassandra.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Cassandra Connector i v2](../connector-cassandra.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en lokal Cassandra-databas. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från ett lokalt Cassandra-data lager till alla mottagar data lager som stöds. En lista över data lager som stöds som mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory har för närvarande endast stöd för att flytta data från ett Cassandra data lager till andra data lager, men inte för att flytta data från andra data lager till ett Cassandra-datalager.

## <a name="supported-versions"></a>Versioner som stöds
Cassandra-anslutningen har stöd för följande versioner av Cassandra: 2. x och 3. x. För att aktiviteter som körs på egen värd Integration Runtime, stöds Cassandra 3. x sedan IR version 3,7 och senare.

## <a name="prerequisites"></a>Förutsättningar
För att Azure Data Factorys tjänsten ska kunna ansluta till din lokala Cassandra-databas måste du installera en Data Management Gateway på samma dator som är värd för-databasen eller på en annan dator för att undvika att konkurrera till resurser med databasen. Data Management Gateway är en komponent som ansluter lokala data källor till moln tjänster på ett säkert och hanterat sätt. Se [Data Management Gateway](data-factory-data-management-gateway.md) artikel för information om data Management Gateway. Se avsnittet [Flytta data från en lokal plats till molnet](data-factory-move-data-between-onprem-and-cloud.md) för stegvisa instruktioner om hur du konfigurerar gatewayen en datapipeline för att flytta data.

Du måste använda gatewayen för att ansluta till en Cassandra-databas även om databasen finns i molnet, till exempel på en virtuell Azure IaaS-dator. Y du kan ha en gateway på samma virtuella dator som är värd för databasen eller på en separat virtuell dator så länge gatewayen kan ansluta till databasen.

När du installerar gatewayen installeras automatiskt en Microsoft Cassandra ODBC-drivrutin som används för att ansluta till Cassandra-databasen. Därför behöver du inte installera någon driv rutin manuellt på gateway-datorn när du kopierar data från Cassandra-databasen.

> [!NOTE]
> Mer information om fel sökning av problem med anslutning/Gateway finns i [Felsöka Gateway-problem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från ett lokalt Cassandra data lager med hjälp av olika verktyg/API: er.

- Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.
- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från ett lokalt Cassandra data lager finns i [JSON-exempel: kopiera data från Cassandra till Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för ett Cassandra-data lager:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är speciella för Cassandra-länkade tjänster.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **OnPremisesCassandra** |Ja |
| värd |En eller flera IP-adresser eller värd namn för Cassandra-servrar.<br/><br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn för att ansluta till alla servrar samtidigt. |Ja |
| port |TCP-porten som Cassandra-servern använder för att lyssna efter klient anslutningar. |Nej, standardvärde: 9042 |
| authenticationType |Basic eller Anonymous |Ja |
| användarnamn |Ange användar namn för användar kontot. |Ja, om authenticationType har angetts till Basic. |
| password |Ange lösen ordet för användar kontot. |Ja, om authenticationType har angetts till Basic. |
| gatewayName |Namnet på den gateway som används för att ansluta till den lokala Cassandra-databasen. |Ja |
| encryptedCredential |Autentiseringsuppgifterna har krypterats av gatewayen. |Inga |

>[!NOTE]
>Anslutning till Cassandra som använder TLS stöds inte.

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet typeProperties för data uppsättningen av typen **CassandraTable** har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| keyspace |Namnet på det här utrymmet eller schemat i Cassandra-databasen. |Ja (om **fråga** för **CassandraSource** inte har definierats). |
| tableName |Namnet på tabellen i Cassandra-databasen. |Ja (om **fråga** för **CassandraSource** inte har definierats). |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

När källan är av typen **CassandraSource** finns följande egenskaper i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-92 fråga eller CQL-fråga. Se [referens för CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-fråga anger du namnet på det **. tabell namn** som ska representera den tabell som du vill fråga. |Nej (om tableName och disk utrymme på data mängden har definierats). |
| consistencyLevel |Konsekvens nivån anger hur många repliker som måste svara på en Read-begäran innan data returneras till klient programmet. Cassandra kontrollerar det angivna antalet repliker för data för att uppfylla Read-begäran. |ETT, TVÅ, TRE, KVORUM, ALLA, LOCAL_QUORUM, EACH_QUORUM LOCAL_ONE. Mer information finns i [Konfigurera data konsekvens](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Nej. Standardvärdet är ett. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-exempel: kopiera data från Cassandra till Azure-Blob
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det visar hur du kopierar data från en lokal Cassandra-databas till en Azure-Blob Storage. Data kan dock kopieras till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-kodfragment. Det innehåller inga steg-för-steg-instruktioner för att skapa data fabriken. Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar för steg-för-steg-instruktioner.

Exemplet har följande data Factory-entiteter:

* En länkad tjänst av typen [OnPremisesCassandra](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [CassandraTable](#dataset-properties).
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [CassandraSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra länkad tjänst:**

I det här exemplet används den länkade tjänsten **Cassandra** . Se avsnittet om [länkade tjänster i Cassandra](#linked-service-properties) för de egenskaper som stöds av den här länkade tjänsten.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage länkad tjänst:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Cassandra för data uppsättning:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Om du anger **external** till **True** informerar Data Factory tjänsten att data uppsättningen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

**Data uppsättning för Azure Blob-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopiera aktivitet i en pipeline med Cassandra-källa och blob-mottagare:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **CassandraSource** och **mottagar** typ är inställd på **BlobSink**.

Se [RelationalSource typ egenskaper](#copy-activity-properties) för listan över egenskaper som stöds av RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Typ mappning för Cassandra
| Typ av Cassandra | .NET-baserad typ |
| --- | --- |
| ASCII |Sträng |
| BIGINT |Int64 |
| BLOB |Byte [] |
| BOOLESKT |Boolesk |
| DECIMAL |Decimal |
| DOUBLE |Double |
| FLYTA |Enkel |
| INET |Sträng |
| INT |Int32 |
| TEXT |Sträng |
| TIMESTAMP |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Sträng |
| VARINT |Decimal |

> [!NOTE]
> För samlings typer (karta, uppsättning, lista osv.), se [arbeta med Cassandra-samlings typer med hjälp av virtuell tabell](#work-with-collections-using-virtual-table) avsnitt.
>
> Användardefinierade typer stöds inte.
>
> Längden på binära kolumn-och sträng kolumn längder får inte vara större än 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Arbeta med samlingar med hjälp av virtuell tabell
Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från Cassandra-databasen. För samlings typer, inklusive karta, uppsättning och lista, normaliserar driv rutinen data till motsvarande virtuella tabeller. Mer specifikt, om en tabell innehåller alla samlings kolumner, genererar driv rutinen följande virtuella tabeller:

* En **bas tabell** som innehåller samma data som den verkliga tabellen, förutom samlings kolumnerna. Bas tabellen använder samma namn som den verkliga tabell som den representerar.
* En **virtuell tabell** för varje samlings kolumn som utökar de kapslade data. De virtuella tabellerna som representerar samlingar namnges med hjälp av namnet på den verkliga tabellen, en avgränsare "*VT*" och namnet på kolumnen.

Virtuella tabeller refererar till datan i den verkliga tabellen, vilket gör att driv rutinen kan komma åt denormaliserade data. Mer information finns i avsnittet exempel. Du kan komma åt innehållet i Cassandra-samlingar genom att fråga och ansluta till de virtuella tabellerna.

Du kan använda [guiden Kopiera](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) för att intuitivt Visa listan över tabeller i Cassandra-databasen, inklusive de virtuella tabellerna och för hands versionen av data i. Du kan också skapa en fråga i guiden Kopiera och validera för att se resultatet.

### <a name="example"></a>Exempel
Till exempel är följande "ExampleTable" en Cassandra-databas tabell som innehåller en heltals primär nyckel kolumn med namnet "pk_int", en text kolumn med namnet värde, en List kolumn, en kart kolumn och en Set-kolumn (med namnet "StringSet").

| pk_int | Värde | Lista | Karta | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"exempel värde 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"exempel värde 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Driv rutinen skulle generera flera virtuella tabeller som representerar den här enskilda tabellen. Sekundär nyckel kolumnerna i de virtuella tabellerna refererar till primär nyckel kolumnerna i den verkliga tabellen och anger vilken verklig tabell rad den virtuella tabell raden motsvarar.

Den första virtuella tabellen är bas tabellen med namnet "ExampleTable" som visas i följande tabell. Bas tabellen innehåller samma data som den ursprungliga databas tabellen förutom samlingarna, som utelämnas från den här tabellen och expanderas i andra virtuella tabeller.

| pk_int | Värde |
| --- | --- |
| 1 |"exempel värde 1" |
| 3 |"exempel värde 3" |

I följande tabeller visas de virtuella tabeller som normaliserar data från kolumnerna List, Map och StringSet. Kolumnerna med namn som slutar med "_index" eller "_key" visar positionen för data i den ursprungliga listan eller kartan. Kolumnerna med namn som slutar med "_value" innehåller utökade data från samlingen.

#### <a name="table-exampletable_vt_list"></a>Tabell ExampleTable_vt_List:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabell ExampleTable_vt_Map:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Tabell ExampleTable_vt_StringSet:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner i käll data uppsättningen till kolumner i data uppsättning för mottagare finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från Relations källor
När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera principer för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor körs på annat sätt måste du se till att samma data är lästa oavsett hur många gånger en sektor körs. Se [repeterbar läsning från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.
