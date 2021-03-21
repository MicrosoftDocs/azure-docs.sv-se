---
title: Skapa extern ström (Transact-SQL) – Azure SQL Edge
description: Lär dig mer om CREATE EXTERNAL STREAM-instruktionen i Azure SQL Edge
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 203abe2b6def478dc1747dd4ce638b5b62707612
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659230"
---
# <a name="create-external-stream-transact-sql"></a>Skapa extern ström (Transact-SQL)

Det externa STREAM-objektet har ett dubbelt syfte av både indata och utdataström. Det kan användas som indata för att fråga strömmande data från händelse inmatnings tjänster som Azure Event Hub, Azure IoT Hub (eller Edge Hub) eller Kafka, eller så kan den användas som utdata för att ange var och hur resultaten ska lagras från en strömmande fråga.

En extern data ström kan också anges och skapas som både utdata och indata för tjänster som Event Hub eller Blob Storage. Detta underlättar länknings scenarier där en strömmande fråga behåller resultat till den externa data strömmen som utdata och en annan strömmande fråga som läser från samma externa ström som indata.

Azure SQL Edge stöder för närvarande endast följande data källor som indata och utdata.

| Typ av data Källa | Indata | Resultat | Beskrivning |
|------------------|-------|--------|------------------|
| Azure IoT Edge hubb | Y | Y | Data källa för att läsa och skriva strömmande data till en Azure IoT Edge hubb. Mer information finns i [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL Database | N | Y | Anslutning till data källa för att skriva strömmande data till SQL Database. Databasen kan vara en lokal databas i Azure SQL Edge eller en fjärrdatabas i SQL Server eller Azure SQL Database.|
| Kafka | Y | N | Data källa för att läsa strömmande data från ett Kafka-ämne. Kafka-stöd är inte tillgängligt för ARM64-versionen av Azure SQL Edge.|



## <a name="syntax"></a>Syntax

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Argument

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Plats**: anger namnet på faktiska data eller plats i data källan. 
   - För Edge Hub-eller Kafka Stream-objekt anger platsen namnet på det Edge Hub-eller Kafka-ämne som ska läsas från eller skrivas till.
   - För SQL Stream-objekt (SQL Server, Azure SQL Database eller Azure SQL Edge) anger du namnet på tabellen. Om data strömmen har skapats i samma databas och schema som mål tabellen, är det bara tabell namnet som är tillräckligt. Annars måste du fullständigt kvalificera (<database_name. schema_name. table_name) tabell namnet.
   - För objekt platsen Azure Blob Storage Stream refererar till det Sök vägs mönster som ska användas i BLOB-behållaren. Mer information om den här funktionen hittar du i (/articles/Stream-Analytics/Stream-Analytics-define-outputs.MD # Blob-Storage-and-Azure-Data-Lake-Gen2)

- **INPUT_OPTIONS**: Ange alternativ som nyckel/värde-par för tjänster som Kafka, IoT Edge hubb som är indata till strömnings frågor
    - PARTITIONER: antalet partitioner som definierats för ett ämne. Det maximala antalet partitioner som kan användas är begränsat till 32.
      - Gäller för Kafka-indataströmmen
    - CONSUMER_GROUP: händelse-och IoT-hubbar begränsar antalet läsare i en konsument grupp (till 5). Om du lämnar det här fältet tomt används konsument gruppen $Default.
      - Reserverad för framtida användning. Gäller inte för Azure SQL Edge.  
    - TIME_POLICY: beskriver om du vill släppa händelser eller justera händelse tiden när sena händelser eller händelser som inte är i ordning överför sina tolerans värden.
      - Reserverad för framtida användning. Gäller inte för Azure SQL Edge.
    - LATE_EVENT_TOLERANCE: den maximala acceptabla tids fördröjningen. Fördröjningen representerar skillnaden mellan händelsens tidsstämpel och system klockan.
      - Reserverad för framtida användning. Gäller inte för Azure SQL Edge.
    - OUT_OF_ORDER_EVENT_TOLERANCE: händelser kan tas ur ordning efter att de har gjort resan från indata till strömnings frågan. Dessa händelser kan godkännas som de är, eller så kan du välja att pausa under en angiven period för att ändra ordning på dem.
      - Reserverad för framtida användning. Gäller inte för Azure SQL Edge.
        
- **OUTPUT_OPTIONS**: Ange alternativ som nyckel/värde-par för tjänster som stöds och som är utdata till strömmande frågor 
  - REJECT_POLICY: DROP | FÖRSÖK igen med de data fel som hanterar principer när data konverterings fel inträffar. 
    - Gäller alla utdata som stöds 
  - MINIMUM_ROWS:  
    Minsta antal rader som krävs per batch som skrivs till utdata. För Parquet skapar varje batch en ny fil. 
    - Gäller alla utdata som stöds 
  - MAXIMUM_TIME:  
    Maximal vänte tid i minuter per batch. Efter den här tiden kommer batchen att skrivas till utdata även om minimi kravet på rader är uppfyllt. 
    - Gäller alla utdata som stöds 
  - PARTITION_KEY_COLUMN:  
    Den kolumn som används för partitionsnyckel. 
    - Reserverad för framtida användning. Gäller inte för Azure SQL Edge.
  - PROPERTY_COLUMNS:  
    En kommaavgränsad lista över namnen på utdatakolumner som ska bifogas till meddelanden som anpassade egenskaper om de anges.  
    - Reserverad för framtida användning. Gäller inte för Azure SQL Edge. 
  - SYSTEM_PROPERTY_COLUMNS:  
    En JSON-formaterad samling med namn/värde-par av namn-/värdepar för system egenskaps namn och utdatakolumner som ska fyllas i Service Bus meddelanden. t. ex. {"MessageId": "column1", "PartitionKey": "column2"} 
    - Reserverad för framtida användning. Gäller inte för Azure SQL Edge. 
  - PARTITION_KEY:  
    Namnet på kolumnen utdata som innehåller partitionsnyckel. Partitionsnyckel är en unik identifierare för partitionen i en specifik tabell som utgör den första delen av en entitets primära nyckel. Det är ett sträng värde som kan vara upp till 1 KB stort. 
    - Reserverad för framtida användning. Gäller inte för Azure SQL Edge.
  - ROW_KEY:  
    Namnet på kolumnen utdata som innehåller rad nyckeln. Rad nyckeln är en unik identifierare för en entitet inom en specifik partition. Den utgör den andra delen av en entitets primära nyckel. Rad nyckeln är ett sträng värde som kan vara upp till 1 KB stort. 
    - Reserverad för framtida användning. Gäller inte för Azure SQL Edge.
  - BATCH_SIZE:  
    Detta representerar antalet transaktioner för Table Storage där det maximala antalet kan gå upp till 100 poster. För Azure Functions representerar detta batchstorleken i byte som skickas till funktionen per anrop-standard är 256 kB. 
    - Reserverad för framtida användning. Gäller inte för Azure SQL Edge. 
  - MAXIMUM_BATCH_COUNT:  
    Maximalt antal händelser som skickats till funktionen per anrop för Azure Function-standard är 100. För SQL Database representerar detta det maximala antalet poster som skickas med varje Mass infognings transaktion – standardvärdet är 10 000. 
    - Gäller alla SQL-baserade utdata 
  - STAGING_AREA: objektet för extern datakälla till Blob Storage mellanlagringsområdet för data inmatning med stora data flöden i Azure Synapse Analytics 
    - Reserverad för framtida användning. Gäller inte för Azure SQL Edge.


## <a name="examples"></a>Exempel

### <a name="example-1---edgehub"></a>Exempel 1 – EdgeHub

Typ: indata eller utdata<br>

Syntax:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = JSON, 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Exempel 2 – Azure SQL Database, Azure SQL Edge, SQL Server

Typ: utdata<br>

Syntax:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>Exempel 3 – Kafka

Typ: inmatat<br>

Syntax:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Se även

- [SLÄPP extern ström (Transact-SQL)](drop-external-stream-transact-sql.md)
