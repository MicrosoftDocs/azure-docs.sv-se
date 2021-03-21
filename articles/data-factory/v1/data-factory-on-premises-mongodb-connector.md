---
title: Flytta data från MongoDB
description: Lär dig mer om hur du flyttar data från MongoDB Database med Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: cedb0b99f04df00763a3ee83287eec90bd5fb45d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387520"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Flytta data från MongoDB med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-on-premises-mongodb-connector.md)
> * [Version 2 (aktuell version)](../connector-mongodb.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [MongoDB Connector i v2](../connector-mongodb.md).


Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en lokal MongoDB-databas. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från ett lokalt MongoDB-data lager till alla mottagar data lager som stöds. En lista över data lager som stöds som mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory har för närvarande endast stöd för att flytta data från ett MongoDB data lager till andra data lager, men inte för att flytta data från andra data lager till ett MongoDB-datalager.

## <a name="prerequisites"></a>Förutsättningar
För att Azure Data Factorys tjänsten ska kunna ansluta till din lokala MongoDB-databas måste du installera följande komponenter:

- De MongoDB-versioner som stöds är: 2,4, 2,6, 3,0, 3,2, 3,4 och 3,6.
- Data Management Gateway på samma dator som är värd för-databasen eller på en annan dator för att undvika att det går att konkurrera till resurser med databasen. Data Management Gateway är en program vara som ansluter lokala data källor till moln tjänster på ett säkert och hanterat sätt. Se [Data Management Gateway](data-factory-data-management-gateway.md) artikel för information om data Management Gateway. Se avsnittet [Flytta data från en lokal plats till molnet](data-factory-move-data-between-onprem-and-cloud.md) för stegvisa instruktioner om hur du konfigurerar gatewayen en datapipeline för att flytta data.

    När du installerar gatewayen installeras automatiskt en Microsoft MongoDB ODBC-drivrutin som används för att ansluta till MongoDB.

    > [!NOTE]
    > Du måste använda gatewayen för att ansluta till MongoDB även om den finns i virtuella Azure IaaS-datorer. Om du försöker ansluta till en instans av MongoDB som finns i molnet kan du också installera Gateway-instansen i den virtuella datorn IaaS.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från ett lokalt MongoDB data lager med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet.  Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från ett lokalt MongoDB data lager finns i [JSON-exempel: kopiera data från MongoDB till Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för MongoDB-källan:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är speciella för **OnPremisesMongoDB** -länkade tjänster.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **OnPremisesMongoDb** |Ja |
| server |IP-adressen eller värd namnet för MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter klient anslutningar. |Valfritt värde: 27017 |
| authenticationType |Basic eller Anonymous. |Ja |
| användarnamn |Användar konto för åtkomst till MongoDB. |Ja (om grundläggande autentisering används). |
| password |Lösenordet för användaren. |Ja (om grundläggande autentisering används). |
| authSource |Namnet på MongoDB-databasen som du vill använda för att kontrol lera autentiseringsuppgifterna för autentisering. |Valfritt (om grundläggande autentisering används). standard: använder administratörs kontot och den databas som anges med egenskapen databaseName. |
| Databas |Namnet på MongoDB-databasen som du vill komma åt. |Ja |
| gatewayName |Namnet på den gateway som har åtkomst till data lagret. |Ja |
| encryptedCredential |Autentiseringsuppgifterna har krypterats av gateway. |Valfritt |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet typeProperties för data uppsättningen av typen **MongoDbCollection** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Samling |Namnet på samlingen i MongoDB-databasen. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

Vilka egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten å andra sidan varierar med varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

När källan är av typen **MongoDbSource** finns följande egenskaper i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-92-frågesträng. Exempel: Välj * från tabellen tabell. |Nej (om **samlings** - **dataset** har angetts) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-exempel: kopiera data från MongoDB till Azure-Blob
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det visar hur du kopierar data från en lokal MongoDB till en Azure-Blob Storage. Data kan dock kopieras till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

Exemplet har följande data Factory-entiteter:

1. En länkad tjänst av typen [OnPremisesMongoDb](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [MongoDbCollection](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [MongoDbSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från ett frågeresultat i MongoDB-databasen till en BLOB varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

Det första steget är att konfigurera data Management Gateway enligt anvisningarna i [Data Management Gateway](data-factory-data-management-gateway.md) artikeln.

**MongoDB länkad tjänst:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**MongoDB för data uppsättning:** Inställningen "extern": "true" informerar den Data Factory tjänsten att tabellen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Data uppsättning för Azure Blob-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopiera aktivitet i en pipeline med MongoDB-källa och blob-mottagare:**

Pipelinen innehåller en kopierings aktivitet som kon figurer ATS för att använda ovanstående data uppsättningar och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **MongoDbSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som anges för egenskapen **fråga** väljer data under den senaste timmen som ska kopieras.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Schema efter Data Factory
Azure Data Factory tjänst härleder schemat från en MongoDB-samling med de senaste 100 dokumenten i samlingen. Om dessa 100-dokument inte innehåller fullständigt schema kan vissa kolumner ignoreras under kopieringen.

## <a name="type-mapping-for-mongodb"></a>Typ mappning för MongoDB
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från käll typer till mottagar typer med följande 2-steg-metod:

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När du flyttar data till MongoDB används följande mappningar från MongoDB-typer till .NET-typer.

| Typ av MongoDB | .NET Framework typ |
| --- | --- |
| Binär |Byte [] |
| Boolesk |Boolesk |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Sträng |
| Sträng |Sträng |
| UUID |GUID |
| Objekt |Förnormaliserad till att förenkla kolumner med "_" som kapslad avgränsare |

> [!NOTE]
> Information om stöd för matriser som använder virtuella tabeller finns i avsnittet [stöd för komplexa typer med hjälp av virtuella tabeller](#support-for-complex-types-using-virtual-tables) nedan.

För närvarande stöds inte följande MongoDB-data typer: DBPointer, Java Script, max/min-nyckel, reguljärt uttryck, symbol, tidsstämpel, odefinierad

## <a name="support-for-complex-types-using-virtual-tables"></a>Stöd för komplexa typer med hjälp av virtuella tabeller
Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från MongoDB-databasen. För komplexa typer, till exempel matriser eller objekt med olika typer i dokument, normaliserar driv rutinen data till motsvarande virtuella tabeller. Mer specifikt, om en tabell innehåller sådana kolumner, genererar driv rutinen följande virtuella tabeller:

* En **bas tabell** som innehåller samma data som den verkliga tabellen förutom de komplexa typ kolumnerna. Bas tabellen använder samma namn som den verkliga tabell som den representerar.
* En **virtuell tabell** för varje komplex typ kolumn som utökar de kapslade data. De virtuella tabellerna namnges med hjälp av namnet på den verkliga tabellen, avgränsaren "_" och namnet på matrisen eller objektet.

Virtuella tabeller refererar till datan i den verkliga tabellen, vilket gör att driv rutinen kan komma åt denormaliserade data. Se exempel avsnittet nedan. Du kan komma åt innehållet i MongoDB-matriser genom att fråga och ansluta till de virtuella tabellerna.

Du kan använda [guiden Kopiera](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) för att intuitivt Visa listan över tabeller i MongoDB-databasen, inklusive de virtuella tabellerna och för hands versionen av data i. Du kan också skapa en fråga i guiden Kopiera och validera för att se resultatet.

### <a name="example"></a>Exempel
Till exempel är "ExampleTable" nedan en MongoDB-tabell med en kolumn med en matris med objekt i varje cell – fakturor och en kolumn med en matris av skalära typer – klassificeringar.

| _id | Kundnamn | Fakturor | Servicenivå | Klassificeringar |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", item: "toaster", Price: "456", Discount: "0,2"}, {invoice_id: "124", item: "ugn", pris: "1235", rabatt: "0,2"}] |Silver |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135", item: "kyl skåp", Price: "12543", Discount: "0,0"}] |Guld |[1, 2] |

Driv rutinen skulle generera flera virtuella tabeller som representerar den här enskilda tabellen. Den första virtuella tabellen är bas tabellen med namnet "ExampleTable", som visas nedan. Bas tabellen innehåller alla data i den ursprungliga tabellen, men data från matriserna har utelämnats och expanderats i de virtuella tabellerna.

| _id | Kundnamn | Servicenivå |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Guld |

Följande tabeller visar de virtuella tabeller som representerar de ursprungliga matriserna i exemplet. Tabellerna innehåller följande:

* En referens tillbaka till den ursprungliga primär nyckel kolumnen som motsvarar raden i den ursprungliga matrisen (via kolumnen _id)
* En indikation på positionen för data i den ursprungliga matrisen
* De expanderade data för varje element i matrisen

Tabell ExampleTable_Invoices:

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | objekt | price | Rabatt |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0,2 |
| 1111 |1 |124 |ugnen |1235 |0,2 |
| 2222 |0 |135 |kyl skåp |12543 |0.0 |

Tabell ExampleTable_Ratings:

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner i käll data uppsättningen till kolumner i data uppsättning för mottagare finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från Relations källor
När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera principer för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor körs på annat sätt måste du se till att samma data är lästa oavsett hur många gånger en sektor körs. Se [repeterbar läsning från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se [Flytta data mellan lokala och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar för stegvisa instruktioner för att skapa en datapipeline som flyttar data från ett lokalt data lager till ett Azure-datalager.
