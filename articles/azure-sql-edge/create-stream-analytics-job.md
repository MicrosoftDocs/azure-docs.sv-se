---
title: Skapa ett T-SQL-strömmande jobb i Azure SQL Edge
description: Lär dig mer om att skapa Stream Analytics jobb i Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 97189fd7a232c2467981b23dc20da51ebef08252
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656350"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>Skapa ett data strömnings jobb i Azure SQL Edge 

Den här artikeln beskriver hur du skapar ett T-SQL streaming-jobb i Azure SQL Edge. Du skapar indata och utdata för det externa Stream-objektet, och sedan definierar du direkt uppspelnings jobb frågan som en del av direkt uppspelnings jobbet.

## <a name="configure-the-external-stream-input-and-output-objects"></a>Konfigurera indata och utdata för det externa Stream-objektet

T-SQL streaming använder den externa data käll funktionen i SQL Server för att definiera de data källor som är associerade med de externa data strömmarnas indata och utdata från strömnings jobbet. Använd följande T-SQL-kommandon för att skapa ett indata-eller utdata-objekt för extern ström:

- [Skapa externt filformat (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)

- [Skapa extern datakälla (Transact-SQL)](/sql/t-sql/statements/create-external-data-source-transact-sql)

- [Skapa extern ström (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Om Azure SQL Edge, SQL Server eller Azure SQL Database används som utdataström måste du dessutom använda [Transact-SQL (Create Database begränsade autentiseringsuppgifter)](/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Det här T-SQL-kommandot definierar de autentiseringsuppgifter som krävs för att komma åt databasen.

### <a name="supported-input-and-output-stream-data-sources"></a>Data källor för indata och utdataström som stöds

Azure SQL Edge stöder för närvarande endast följande data källor som indata och utdata.

| Typ av data Källa | Indata | Resultat | Beskrivning |
|------------------|-------|--------|------------------|
| Azure IoT Edge hubb | Y | Y | Data källa för att läsa och skriva strömmande data till en Azure IoT Edge hubb. Mer information finns i [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL Database | N | Y | Anslutning till data källa för att skriva strömmande data till SQL Database. Databasen kan vara en lokal databas i Azure SQL Edge eller en fjärrdatabas i SQL Server eller Azure SQL Database.|
| Kafka | Y | N | Data källa för att läsa strömmande data från ett Kafka-ämne. Det här kortet är för närvarande endast tillgängligt för Intel-eller AMD-versioner av Azure SQL Edge. Den är inte tillgänglig för ARM64-versionen av Azure SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Exempel: skapa ett externt Stream-indata/utdata-objekt för Azure IoT Edge hubb

I följande exempel skapas ett externt Stream-objekt för Azure IoT Edge Hub. Om du vill skapa en extern data källa för data ström/utdata för Azure IoT Edge hubb måste du först skapa ett externt fil format för layouten för data som läses eller skrivs.

1. Skapa ett externt fil format av typen JSON.

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Skapa en extern data källa för Azure IoT Edge Hub. Följande T-SQL-skript skapar en anslutning till en data källa till en IoT Edge hubb som körs på samma Docker-värd som Azure SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Skapa det externa Stream-objektet för Azure IoT Edge Hub. Följande T-SQL-skript skapar ett Stream-objekt för IoT Edge Hub. Om det är ett IoT Edge Hub Stream-objekt är parametern LOCATION namnet på det IoT Edge Hub-ämne eller-kanal som läses eller skrivs till.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Exempel: skapa ett externt Stream-objekt för att Azure SQL Database

I följande exempel skapas ett externt Stream-objekt till den lokala databasen i Azure SQL Edge. 

1. Skapa en huvud nyckel för databasen. Detta krävs för att kryptera hemligheten för autentiseringsuppgiften.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Skapa en databas med begränsade autentiseringsuppgifter för att komma åt SQL Server källan. I följande exempel skapas en autentiseringsuppgift för den externa data källan, med identitet = username och SECRET = Password.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Skapa en extern data källa med skapa extern DATA källa. Följande exempel:

    * Skapar en extern data källa med namnet *LocalSQLOutput*.
    * Identifierar den externa data källan (LOCATION = ' <vendor> :// <server> [: <port> ] '). I exemplet pekar den på en lokal instans av Azure SQL Edge.
    * Använder de autentiseringsuppgifter som skapades tidigare.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. Skapa det externa Stream-objektet. I följande exempel skapas ett externt Stream-objekt som pekar på en tabell *dbo. TemperatureMeasurements*, i databasen *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>Exempel: skapa ett externt Stream-objekt för Kafka

I följande exempel skapas ett externt Stream-objekt till den lokala databasen i Azure SQL Edge. Det här exemplet förutsätter att Kafka-servern har kon figurer ATS för anonym åtkomst. 

1. Skapa en extern data källa med skapa extern DATA källa. Följande exempel:

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Skapa ett externt fil format för Kafka-indata. I följande exempel skapades ett JSON-filformat med GZipped-komprimering. 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```

3. Skapa det externa Stream-objektet. I följande exempel skapas ett externt Stream-objekt som pekar på Kafka-ämnet `*TemperatureMeasurement*` .

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Skapa direkt uppspelnings jobbet och strömmande frågor

Använd den `sys.sp_create_streaming_job` systemlagrade proceduren för att definiera strömnings frågorna och skapa direkt uppspelnings jobbet. Den `sp_create_streaming_job` lagrade proceduren tar följande parametrar:

- `job_name`: Namnet på direkt uppspelnings jobbet. Strömmande jobb namn är unika i instansen.
- `statement`: [Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)-baserade frågeuttryck.

I följande exempel skapas ett enkelt strömnings jobb med en direkt uppspelnings fråga. Den här frågan läser in indata från IoT Edge Hub och skriver till `dbo.TemperatureMeasurements` i-databasen.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

I följande exempel skapas ett mer komplext strömnings jobb med flera olika frågor. Dessa frågor innehåller en som använder den inbyggda `AnomalyDetection_ChangePoint` funktionen för att identifiera avvikelser i temperatur data.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Starta, stoppa, släppa och övervaka strömmande jobb

Starta ett strömmande jobb i Azure SQL Edge genom att köra den `sys.sp_start_streaming_job` lagrade proceduren. Den lagrade proceduren kräver att namnet på det strömmande jobbet startar, som indata.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Kör den lagrade proceduren för att stoppa ett strömmande jobb `sys.sp_stop_streaming_job` . Den lagrade proceduren kräver att namnet på det strömmande jobbet stoppas, som indata.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Om du vill ta bort (eller ta bort) ett strömmande jobb kör du den `sys.sp_drop_streaming_job` lagrade proceduren. Den lagrade proceduren kräver att namnet på det strömmande jobbet släpps som indata.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Kör den lagrade proceduren för att hämta aktuell status för ett strömmande jobb `sys.sp_get_streaming_job` . Den lagrade proceduren kräver att namnet på det strömmande jobbet släpps som indata. Den matar ut namnet och aktuell status för direkt uppspelnings jobbet.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256),
       error nvarchar(256)
       )
)
```

Direkt uppspelnings jobbet kan ha någon av följande statusar:

| Status | Beskrivning |
|--------| ------------|
| Skapad | Direkt uppspelnings jobbet skapades men har ännu inte startats. |
| Startar | Streaming-jobbet är i start fasen. |
| Inaktiv | Direkt uppspelnings jobbet körs, men det finns inga indata att bearbeta. |
| Bearbetar | Direkt uppspelnings jobbet körs och bearbetar indata. Det här tillståndet anger ett felfritt tillstånd för strömnings jobbet. |
| Degraderad | Direkt uppspelnings jobbet körs, men det fanns några icke-allvarliga fel under bearbetningen av indata. Inmatnings jobbet kommer att fortsätta köras, men kommer att släppa indata som stöter på fel. |
| Stoppad | Direkt uppspelnings jobbet har stoppats. |
| Misslyckad | Streaming-jobbet misslyckades. Detta är vanligt vis ett tecken på ett allvarligt fel under bearbetningen. |

## <a name="next-steps"></a>Nästa steg

- [Visa metadata som är associerade med strömmande jobb i Azure SQL Edge](streaming-catalog-views.md) 
- [Skapa en extern ström](create-external-stream-transact-sql.md)