---
title: Fel vid Azure Stream Analytics resurs logg data
description: I den här artikeln förklaras de olika fel i indata och utdata som kan uppstå när du använder Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 2ca289cd52b9a406e486ee9c186be683e71f02d0
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020119"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics data fel

Datafel är fel som uppstår vid bearbetning av data.  Dessa fel inträffar oftast under data avserialisering, serialisering och skriv åtgärder.  När data fel inträffar skriver Stream Analytics detaljerad information och exempel händelser till resurs loggarna. Använd diagnostikloggar i jobbet för att få ytterligare information. I vissa fall tillhandahålls en sammanfattning av den här informationen även via portal meddelanden.

Den här artikeln beskriver de olika fel typerna, orsakerna och resurs loggs informationen för fel i indata och utdata.

## <a name="resource-logs-schema"></a>Schema för resurs loggar

Se [felsöka Azure Stream Analytics med hjälp](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) av diagnostikloggar för att se schemat för resurs loggar. Följande JSON är ett exempel värde för **egenskaps** fältet i en resurs logg för ett data fel.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Indata-fel

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Orsak: den angivna typen av typ komprimering matchar inte data.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Effekt: meddelanden med eventuella deserialiserings fel inklusive ogiltig komprimerings typ tas bort från indatamängden.
* Logg information
   * Meddelande-ID för indatamängd. För Event Hub är identifieraren PartitionId, offset och Sequence Number.

**Felmeddelande**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Orsak: rubriken på indata är ogiltig. En CSV-fil har till exempel kolumner med dubbla namn.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Effekt: meddelanden med eventuella deserialiserings fel, inklusive ogiltigt sidhuvud, tas bort från indatamängden.
* Logg information
   * Meddelande-ID för indatamängd. 
   * Faktisk nytto Last upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Orsak: de angivna kolumnerna som definierats med CREATE TABLE eller genom tidsstämpeln finns inte.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Effekt: händelser med saknade kolumner tas bort från indatamängden.
* Logg information
   * Meddelande-ID för indatamängd. 
   * Namnen på kolumnerna som saknas. 
   * Faktisk nytto Last upp till några kilobyte.

**Felmeddelanden**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Orsak: det gick inte att konvertera indatamängden till den typ som anges i instruktionen CREATE TABLE.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Påverkan: händelser med typ konverterings fel tas bort från indatamängden.
* Logg information
   * Meddelande-ID för indatamängd. 
   * Namnet på kolumnen och den förväntade typen.

**Felmeddelanden**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Orsak: indata är inte i rätt format. Indatatypen är till exempel inte giltig JSON.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Effekt: alla händelser i meddelandet efter att ett ogiltigt data fel har påträffats ignoreras från indata.
* Logg information
   * Meddelande-ID för indatamängd. 
   * Faktisk nytto Last upp till några kilobyte.

**Felmeddelanden**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Orsak: det går inte att konvertera värdet för TIMESTAMP BY-uttrycket till DateTime.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Påverkan: händelser med ogiltig tidsstämpel tas bort från inmatade värden.
* Logg information
   * Meddelande-ID för indatamängd. 
   * Fel meddelande. 
   * Faktisk nytto Last upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Orsak: värdet för TIMESTAMP BY timestampColumn är NULL.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Effekt: händelser med ogiltig tids stämpling för inmatade tidsstämplar tas bort från indatamängden.
* Logg information
   * Den faktiska nytto lasten upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Orsak: skillnaden mellan kopplings tid och införsel tid är större än fönstret för sent införsel tolerans.
* Portal meddelande har angetts: Nej
* Resurs logg nivå: information
* Påverkan: sena ingångs händelser hanteras enligt inställningen hantera andra händelser i avsnittet händelse ordning i jobb konfigurationen. Mer information finns i [tids hanterings principer](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Logg information
   * Tid för program och tid för införsel. 
   * Faktisk nytto Last upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Orsak: skillnaden mellan program tid och införsel tid är större än 5 minuter.
* Portal meddelande har angetts: Nej
* Resurs logg nivå: information
* Påverkan: tidiga ingångs händelser hanteras enligt inställningen hantera andra händelser i avsnittet händelse ordning i jobb konfigurationen. Mer information finns i [tids hanterings principer](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Logg information
   * Tid för program och tid för införsel. 
   * Faktisk nytto Last upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Orsak: händelsen anses vara i ordning enligt den definierade tolerans perioden.
* Portal meddelande har angetts: Nej
* Resurs logg nivå: information
* Påverkan: händelser som inte är i ordning hanteras enligt inställningen hantera andra händelser i avsnittet händelse ordning i jobb konfigurationen. Mer information finns i [tids hanterings principer](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Logg information
   * Faktisk nytto Last upp till några kilobyte.

**Felmeddelande**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Fel vid utdata

Azure Stream Analytics kan identifiera fel i utgående data med eller utan en I/O-begäran till den utgående sinken beroende på konfigurationen. Till exempel saknas en obligatorisk kolumn, t. ex  `PartitionKey` . När du använder Azure Table-utdata som kan identifieras utan I/O-begäran. Begränsnings överträdelser i SQL-utdata kräver dock en I/O-begäran.

Det finns flera datafel som bara kan identifieras efter ett anrop till utgående mottagare, vilket kan sakta ned bearbetningen. Lös detta genom att ändra jobbets konfiguration eller den fråga som orsakar data felet.

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Orsak: kolumnen som krävs för utdata finns inte. Till exempel finns en kolumn som definierats som Azure Table PartitionKey does't.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Påverkan: alla fel konverterings fel, inklusive saknade obligatoriska kolumner, hanteras enligt princip inställningen för [utdata](./stream-analytics-output-error-policy.md) .
* Logg information
   * Kolumnens namn och antingen post-ID eller del av posten.

**Felmeddelande**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Orsak: kolumnens värde stämmer inte med utdata. Kolumn namnet är till exempel inte en giltig Azure Table-kolumn.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Påverkan: alla fel vid konvertering av utdata, inklusive Ogiltigt kolumn namn hanteras enligt princip inställningen för [utdata](./stream-analytics-output-error-policy.md) .
* Logg information
   * Kolumnens namn och antingen post-ID eller del av posten.

**Felmeddelande**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Orsak: det går inte att konvertera en kolumn till en giltig typ i utdata. Värdet för kolumnen är till exempel inte kompatibelt med begränsningar eller typ som definierats i SQL-tabellen.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Påverkan: alla fel vid konvertering av utdata, inklusive typ konverterings fel, hanteras enligt [princip](./stream-analytics-output-error-policy.md) inställningen för utdata.
* Logg information
   * Kolumnens namn.
   * Antingen post-ID eller del av posten.

**Felmeddelande**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Orsak: meddelandets värde är större än den tillåtna storleken för utdata. En post är till exempel större än 1 MB för en Event Hub-utdata.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Effekt: alla fel vid utgående data konvertering, inklusive poster som överskrider storleks gränsen, hanteras enligt princip inställningen för [utdata](./stream-analytics-output-error-policy.md) .
* Logg information
   * Antingen post-ID eller del av posten.

**Felmeddelande**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Orsak: en post innehåller redan en kolumn med samma namn som en system kolumn. Till exempel är CosmosDB-utdata med en kolumn med namnet ID när ID kolumnen är till en annan kolumn.
* Portal meddelande har angetts: Ja
* Resurs logg nivå: varning
* Påverkan: alla fel vid konvertering av utdata, inklusive dubblettnyckel, hanteras enligt princip inställningen för [utdata](./stream-analytics-output-error-policy.md) .
* Logg information
   * Kolumnens namn.
   * Antingen post-ID eller del av posten.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure Stream Analytics med hjälp av diagnostikloggar](stream-analytics-job-diagnostic-logs.md)

* [Förstå Stream Analytics jobb övervakning och övervaka frågor](stream-analytics-monitoring.md)