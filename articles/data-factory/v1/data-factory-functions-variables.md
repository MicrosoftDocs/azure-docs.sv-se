---
title: Data Factory funktioner och systemvariabler
description: Innehåller en lista över Azure Data Factory funktioner och systemvariabler
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 498f40a1783903b5dca0a2fe3204cc6aa25a2fec
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786438"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory-Functions och systemvariabler
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Systemvariabler i Data Factory](../control-flow-system-variables.md).

Den här artikeln innehåller information om funktioner och variabler som stöds av Azure Data Factory.

## <a name="data-factory-system-variables"></a>Data Factory systemvariabler

| Variabelnamn | Beskrivning | Objekt omfång | JSON-omfång och användnings fall |
| --- | --- | --- | --- |
| WindowStart |Början på tidsintervallet för den aktuella aktivitets körnings perioden |aktivitet |<ol><li>Ange data urvals frågor. Se de kopplings artiklar som refereras i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) .</li> |
| WindowEnd |Tidsintervall för pågående aktivitets körnings fönster |aktivitet |samma som WindowStart. |
| SliceStart |Början av tidsintervall för data sektorn som skapas |aktivitet<br/>data uppsättning |<ol><li>Ange dynamiska sökvägar till mappar och fil namn när du arbetar med [data uppsättningar](data-factory-onprem-file-system-connector.md)för [Azure Blob](data-factory-azure-blob-connector.md) och fil systemet.</li><li>Ange indata-beroenden med Data Factory-funktioner i indata-samlingen.</li></ol> |
| SliceEnd |Slutet av tidsintervallet för den aktuella data sektorn. |aktivitet<br/>data uppsättning |samma som SliceStart. |

> [!NOTE]
> För närvarande kräver Data Factory att schemat som anges i aktiviteten exakt matchar det schema som anges i tillgänglighet för data uppsättningen för utdata. Därför mappar WindowStart, WindowEnd och SliceStart och SliceEnd alltid till samma tids period och en enda utgående sektor.
> 

### <a name="example-for-using-a-system-variable"></a>Exempel på användning av en system variabel
I följande exempel extraheras år, månad, dag och tid för **SliceStart** till separata variabler som används av **FolderPath** -och **filename** -egenskaperna.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Data Factory funktioner
Du kan använda funktioner i Data Factory tillsammans med systemvariabler i följande syfte:

1. Ange frågor för data urval (se kopplings artiklar som refereras till i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) ).
   
   Syntaxen för att anropa en Data Factory-funktion är: **$$\<function>** för data urvals frågor och andra egenskaper i aktiviteten och data uppsättningarna.  
2. Ange indata-beroenden med Data Factory-funktioner i indata-samlingen.
   
    $ $ behövs inte för att ange indata beroende uttryck.     

I följande exempel tilldelas egenskapen **sqlReaderQuery** i en JSON-fil ett värde som returneras av `Text.Format` funktionen. Det här exemplet använder också en system variabel med namnet **WindowStart**, som representerar start tiden för aktivitets körnings fönstret.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Se avsnittet [anpassade datum-och tids format strängar](/dotnet/standard/base-types/custom-date-and-time-format-strings) som beskriver olika formateringsalternativ som du kan använda (till exempel: Ay vs. åååå). 

### <a name="functions"></a>Functions
I följande tabeller visas alla funktioner i Azure Data Factory:

| Kategori | Funktion | Parametrar | Beskrivning |
| --- | --- | --- | --- |
| Tid |AddHours (X, Y) |X: DateTime <br/><br/>Y: int |Lägger till Y-timmar på den aktuella tiden X. <br/><br/>Exempel: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Tid |AddMinutes (X, Y) |X: DateTime <br/><br/>Y: int |Lägger till Y minuter till X.<br/><br/>Exempel: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Tid |StartOfHour (X) |X: datetime |Hämtar start tiden för den timme som representeras av Tim komponenten i X. <br/><br/>Exempel: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Datum |AddDays (X, Y) |X: DateTime<br/><br/>Y: int |Lägger till Y-dagar till X. <br/><br/>Exempel: 9/15/2013 12:00:00 PM + 2 dagar = 9/17/2013 12:00:00 PM.<br/><br/>Du kan subtrahera dagar genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Datum |AddMonths (X, Y) |X: DateTime<br/><br/>Y: int |Lägger till Y-månader till X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Du kan subtrahera månader genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Datum |AddQuarters (X, Y) |X: DateTime <br/><br/>Y: int |Lägger till Y * 3 månader till X.<br/><br/>Exempel: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Datum |AddWeeks (X, Y) |X: DateTime<br/><br/>Y: int |Lägger till Y * 7 dagar till X<br/><br/>Exempel: 9/15/2013 12:00:00 PM + 1 vecka = 9/22/2013 12:00:00 PM<br/><br/>Du kan subtrahera veckor genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Datum |AddYears (X, Y) |X: DateTime<br/><br/>Y: int |Lägger till Y-år till X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Du kan subtrahera år genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Datum |Dag (X) |X: DateTime |Hämtar dags komponenten för X.<br/><br/>Exempel: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Datum |DayOfWeek (X) |X: DateTime |Hämtar dag i veckan-komponenten i X.<br/><br/>Exempel: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Datum |DayOfYear (X) |X: DateTime |Hämtar den dag under året som representeras av års komponenten för X.<br/><br/>Exempel:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Datum |DaysInMonth (X) |X: DateTime |Hämtar de dagar i månaden som representeras av månads komponenten för parametern X.<br/><br/>Exempel: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Datum |EndOfDay (X) |X: DateTime |Hämtar datum/tid som representerar slutet på dagen (dag komponenten) för X.<br/><br/>Exempel: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Datum |EndOfMonth (X) |X: DateTime |Hämtar slutet på månaden som representeras av month-komponenten för parametern X. <br/><br/>Exempel: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (datum och tid som motsvarar slutet av månad i september) |
| Datum |StartOfDay (X) |X: DateTime |Hämtar början på den dag som representeras av dags komponenten för parametern X.<br/><br/>Exempel: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |Från (X) |X: sträng |Parsa sträng X till en datum tid. |
| DateTime |Tick (X) |X: DateTime |Hämtar egenskapen ticks för parametern X. Ett skal är lika med 100 nanosekunder. Värdet för den här egenskapen representerar antalet Tick som har förflutit sedan 12:00:00 midnatt, 1 januari 0001. |
| Text |Format (X) |X: sträng variabel |Formaterar texten (Använd `\\'` kombination till Escape `'` -tecken).|

> [!IMPORTANT]
> När du använder en funktion i en annan funktion behöver du inte använda **$$** prefix för funktionen Inner. Exempel: $ $Text. format (' PartitionKey EQ \\ ' my_pkey_filter_value \\ ' och RowKey ge \\ ' {0: åååå-mm-dd hh: mm: SS} \\ ', Time. AddHours (SliceStart,-6)). I det här exemplet ser **$$** du att prefixet inte används för funktionen **Time. AddHours** . 

#### <a name="example"></a>Exempel
I följande exempel bestäms indata-och utdataparametrar för Hive-aktiviteten med hjälp av `Text.Format` system variabeln Function och SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Exempel 2

I följande exempel bestäms DateTime-parametern för den lagrade procedur aktiviteten genom att använda texten. Funktionen format och variabeln SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Exempel 3
Om du vill läsa data från föregående dag i stället för dagen som representeras av SliceStart använder du funktionen AddDays som visas i följande exempel: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Se avsnittet [anpassade datum-och tids format strängar](/dotnet/standard/base-types/custom-date-and-time-format-strings) som beskriver olika formateringsalternativ som du kan använda (till exempel: åå vs. åååå).