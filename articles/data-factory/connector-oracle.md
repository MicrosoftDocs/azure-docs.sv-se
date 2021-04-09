---
title: Kopiera data till och från Oracle med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från käll arkiv som stöds till en Oracle-databas, eller från Oracle till Sink-lager som stöds, genom att använda Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: 9e6be88af13d5dd7ddceba32ec08cab54ca5e3a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587295"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiera data från och till Oracle med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuell version](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från och till en Oracle-databas. Den bygger på [Översikt över kopierings aktiviteten](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Oracle-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från en Oracle-databas till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till en Oracle-databas. En lista över data lager som stöds som källor eller mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här Oracle-anslutningen:

- Följande versioner av en Oracle-databas:
    - Oracle 19c R1 (19,1) och högre
    - Oracle 18c R1 (18,1) och högre
    - Oracle 12C R1 (12,1) och högre
    - Oracle 11g R1 (11,1) och högre
    - Oracle 10g R1 (10,1) och högre
    - Oracle 9i R2 (9,2) och högre
    - Oracle 8i R3 (8.1.7) och högre
    - Oracle Database Cloud Exadata service
- Parallell kopiering från en Oracle-källa. Mer information finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) .

> [!Note]
> Oracle-proxyservern stöds inte.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Integrerings körningen innehåller en inbyggd Oracle-drivrutin. Därför behöver du inte installera en driv rutin manuellt när du kopierar data från och till Oracle.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Oracle-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Den länkade Oracle-tjänsten stöder följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **Oracle**. | Ja |
| Begär | Anger den information som krävs för att ansluta till Oracle Database-instansen. <br/>Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Läs följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) med mer information. <br><br>**Anslutnings typ som stöds**: du kan använda **Oracle sid** eller **Oracle-tjänstens namn** för att identifiera databasen:<br>-Om du använder SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>– Om du använder tjänst namn: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>För avancerade Oracle-anslutnings alternativ kan du välja att lägga till en post i [Tnsnames. ORA](http://www.orafaq.com/wiki/Tnsnames.ora) -fil på Oracle-servern och i ADF Oracle-länkad tjänst väljer du att använda anslutnings typen Oracle service Name och konfigurerar motsvarande tjänst namn. | Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

>[!TIP]
>Om du får ett fel meddelande, "ORA-01025: UPI-parameter utanför intervallet", och din Oracle-version är 8i, Lägg till `WireProtocolMode=1` i anslutnings strängen. Försök sedan igen.

Om du har flera Oracle-instanser för redundans kan du skapa Oracle-länkade tjänster och fylla i den primära värden, port, användar namn, lösen ord och så vidare och lägga till en ny "**ytterligare anslutnings egenskap**" med egenskaps namn som `AlternateServers` och värde som `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` – saknar hakparenteser och ta hänsyn till kolon ( `:` ) som avgränsare. Följande värde för alternativa servrar definierar till exempel två alternativa databas servrar för redundans av anslutning: `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)` .

Fler anslutnings egenskaper som du kan ange i anslutnings strängen per ärende:

| Egenskap | Beskrivning | Tillåtna värden |
|:--- |:--- |:--- |
| ArraySize |Antalet byte som anslutningen kan hämta i en enda nätverks tur. T. ex., `ArraySize=‭10485760‬` .<br/><br/>Större värden ökar data flödet genom att minska antalet gånger för att hämta data över nätverket. Mindre värden ökar svars tiden eftersom det finns mindre fördröjning i väntan på att servern ska överföra data. | Ett heltal mellan 1 och 4294967296 (4 GB). Standardvärdet är `60000`. Värdet 1 definierar inte antalet byte, men indikerar att allokera utrymme för exakt en rad med data. |

Om du vill aktivera kryptering på Oracle-anslutning har du två alternativ:

-   Om du vill använda **3DES (triple-des Encryption) och Advanced Encryption Standard (AES)** på Oracle-server sidan går du till Oracle Advanced Security (oas) och konfigurerar krypterings inställningarna. Mer information finns i den här [Oracle-dokumentationen](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Program varan för Oracle Application Development Framework (ADF) förhandlar automatiskt om krypterings metoden för att använda den som du konfigurerar i OAS när du upprättar en anslutning till Oracle.

-   Så här använder du **TLS**:

    1.  Hämta information om TLS/SSL-certifikatet. Hämta den Distinguished Encoding Rules (DER)-kodade certifikat informationen för TLS/SSL-certifikatet och spara utdata (-----starta certifikat... Avsluta certifikat-----) som en textfil.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exempel:** Extrahera certifikat information från DERcert. cer och spara sedan utdata till cert.txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Bygg `keystore` eller `truststore` . Följande kommando skapar `truststore` filen, med eller utan lösen ord, i PKCS-12-format.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exempel:** Skapa en PKCS12 `truststore` -fil med namnet MyTrustStoreFile med ett lösen ord.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Placera `truststore` filen på IR-datorn med egen värd. Placera till exempel filen på C:\MyTrustStoreFile.
    4.  I Azure Data Factory konfigurerar du Oracle-anslutningssträngen med `EncryptionMethod=1` och motsvarande `TrustStore` / `TrustStorePassword` värde. Till exempel `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exempel:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra lösen ord i Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
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
## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle-datauppsättningen. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md). 

Om du vill kopiera data från och till Oracle anger du egenskapen type för data uppsättningen till `OracleTable` . Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för data mängden måste anges till `OracleTable` . | Ja |
| schema | Schemats namn. |Nej för källa, Ja för mottagare  |
| tabell | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd och för ny arbets `schema` belastning `table` . | Nej för källa, Ja för mottagare |

**Exempel:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle-källan och mottagare. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle som källa

>[!TIP]
>Om du vill läsa in data från Oracle effektivt genom att använda data partitionering kan du läsa mer från [parallell kopiering från Oracle](#parallel-copy-from-oracle).

Om du vill kopiera data från Oracle anger du käll typen i kopierings aktiviteten till `OracleSource` . Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste vara inställd på `OracleSource` . | Ja |
| oracleReaderQuery | Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`.<br>När du aktiverar partitionerad belastning måste du koppla alla motsvarande inbyggda partitionsalternativ i frågan. Exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Inga |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från Oracle. <br>Tillåtna värden är: **ingen** (standard), **PhysicalPartitionsOfTable** och **DynamicRange**.<br>När ett partitions alternativ är aktiverat (dvs. inte `None` ), kontrol leras graden av parallellitet för att samtidigt läsa in data från en Oracle-databas med [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. | Inga |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när alternativet partition inte är det `None` . | Inga |
| partitionNames | Listan över fysiska partitioner som behöver kopieras. <br>Använd när alternativet partition är `PhysicalPartitionsOfTable` . Om du använder en fråga för att hämta källdata, Hook `?AdfTabularPartitionName` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Inga |
| partitionColumnName | Ange namnet på den käll kolumn **i Integer-typ** som ska användas av intervall partitionering för parallell kopiering. Om detta inte anges identifieras primär nyckeln för tabellen automatiskt och används som partition-kolumn. <br>Använd när alternativet partition är `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook  `?AdfRangePartitionColumnName` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Inga |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för att kopiera data. <br>Använd när alternativet partition är `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionUpbound` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Inga |
| partitionLowerBound | Det minimala värdet för kolumnen partition som ut data ska kopieras. <br>Använd när alternativet partition är `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionLowbound` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Oracle](#parallel-copy-from-oracle) . | Inga |

**Exempel: kopiera data med en grundläggande fråga utan partition**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle som mottagare

Om du vill kopiera data till Oracle ställer du in mottagar typen i kopierings aktiviteten till `OracleSink` . Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till `OracleSink` . | Ja |
| writeBatchSize | Infogar data i SQL-tabellen när buffertstorleken når sig `writeBatchSize` .<br/>Tillåtna värden är heltal (antal rader). |Nej (standard är 10 000) |
| writeBatchTimeout | Vänte tiden för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes.<br/>Tillåtna värden är TimeSpan. Ett exempel är 00:30:00 (30 minuter). | Inga |
| preCopyScript | Ange en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till Oracle i varje körning. Du kan använda den här egenskapen för att rensa de förinstallerade data. | Inga |
| maxConcurrentConnections |Den övre gränsen för samtidiga anslutningar som upprättats till data lagret under aktivitets körningen. Ange bara ett värde om du vill begränsa samtidiga anslutningar.| Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Parallell kopiering från Oracle

Data Factory Oracle-anslutaren tillhandahåller inbyggd data partitionering för att kopiera data från Oracle parallellt. Du kan hitta alternativ för data partitionering på fliken **källa** i kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-oracle/connector-oracle-partition-options.png)

När du aktiverar partitionerad kopiering körs Data Factory parallella frågor mot din Oracle-källa för att läsa in data efter partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från Oracle-databasen.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från Oracle-databasen. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager, skrivs de om för att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell med fysiska partitioner.          | **Partitions alternativ**: fysiska partitioner i tabell. <br><br/>Under körningen identifierar Data Factory automatiskt de fysiska partitionerna och kopierar data efter partitioner. |
| Fullständig belastning från stor tabell, utan fysiska partitioner, med en heltals kolumn för data partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data. Om inget anges används primär nyckel kolumn. |
| Läs in en stor mängd data med hjälp av en anpassad fråga med fysiska partitioner. | **Partitions alternativ**: fysiska partitioner i tabell.<br>**Fråga**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>` .<br>**Partitionsnamn**: Ange namnen på de partitioner som data ska kopieras från. Om detta inte anges identifierar Data Factory automatiskt de fysiska partitionerna i tabellen som du angav i Oracle-datauppsättningen.<br><br>Under körningen ersätts Data Factory `?AdfTabularPartitionName` med det faktiska partitionsnamnet och skickas till Oracle. |
| Läs in en stor mängd data med hjälp av en anpassad fråga, utan fysiska partitioner, med en heltals kolumn för data partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med data typen Integer.<br>**Partitionens övre gränser** och **partition nedre gränser**: Ange om du vill filtrera mot partition-kolumnen för att bara hämta data mellan det nedre och övre intervallet.<br><br>Under körningen ersätts Data Factory `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` och `?AdfRangePartitionLowbound` med det faktiska kolumn namnet och värde intervallet för varje partition och skickas till Oracle. <br>Om till exempel din partitionstabell "ID" är inställt på den nedre gränser som 1 och den övre gränser som 80, med parallell kopierings uppsättning som 4, Data Factory hämtar data med fyra partitioner. Deras ID: n är mellan [1, 20], [21, 40], [41, 60] och [61, 80]. |

> [!TIP]
> När du kopierar data från en icke-partitionerad tabell kan du använda partition alternativet "dynamiskt intervall" för att partitionera mot en heltals kolumn. Om dina källdata inte har sådan typ av kolumn kan du utnyttja [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) -funktionen i käll frågan för att generera en kolumn och använda den som partition kolumn.

**Exempel: fråga med fysisk partition**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Exempel: fråga med Dynamic Range-partition**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Data typs mappning för Oracle

När du kopierar data från och till Oracle gäller följande mappningar. Information om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren finns i [schema-och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| Oracle-datatyp | Data Factory data typen Interim |
|:--- |:--- |
| BFILE |Byte [] |
| BLOB |Byte []<br/>(stöds endast på Oracle 10g och högre) |
| CHAR |Sträng |
| CLOB |Sträng |
| DATE |DateTime |
| FLYTA |Decimal, sträng (om precision > 28) |
| INTEGER |Decimal, sträng (om precision > 28) |
| SOM |Sträng |
| LÅNG RAW |Byte [] |
| NCHAR |Sträng |
| NCLOB |Sträng |
| NUMMER (p, s) |Decimal, sträng (om p > 28) |
| TAL utan precision och skala |Double |
| NVARCHAR2 |Sträng |
| OUTSPÄDD |Byte [] |
| ROWID |Sträng |
| TIMESTAMP |DateTime |
| TIDSSTÄMPEL MED LOKAL TIDSZON |Sträng |
| TIDSSTÄMPEL MED TIDSZON |Sträng |
| OSIGNERAT HELTAL |Antal |
| VARCHAR2 |Sträng |
| XML |Sträng |

> [!NOTE]
> Data typerna INTERVAL år till månad och intervall dag till sekund stöds inte.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
