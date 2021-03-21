---
title: Språk referens för Query acceleration SQL
titleSuffix: Azure Storage
description: Lär dig hur du använder SQL-syntax för frågekörning.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: 2eda67e377a3b61e696e732b916d788c00a18eae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95908786"
---
# <a name="query-acceleration-sql-language-reference"></a>Språk referens för Query acceleration SQL

Query acceleration stöder ett ANSI SQL-liknande språk för att uttrycka frågor över BLOB-innehåll.  SQL-dialekten för frågekörning är en delmängd av ANSI SQL, med en begränsad uppsättning data typer, operatorer osv., men den utökas också på ANSI SQL för att ge stöd för frågor över hierarkiskt strukturerade data format, t. ex. JSON. 

## <a name="select-syntax"></a>Välj syntax

Det enda SQL-uttryck som stöds av frågans acceleration är SELECT-instruktionen. Det här exemplet returnerar alla rader för vilka uttrycket returnerar true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

För CSV-formaterade data måste *tabellen* vara `BlobStorage` .  Det innebär att frågan kommer att köras mot den angivna blobben i REST-anropet.
För JSON-formaterade data är *tabellen* en "Tabell Beskrivning".   Se avsnittet [tabell beskrivningar](#table-descriptors) i den här artikeln.

I följande exempel, för varje rad där WHERE- *uttrycket* returnerar true, returnerar den här instruktionen en ny rad som görs från utvärderingen av varje projektions uttryck.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

I följande exempel returneras en mängd beräkning (till exempel medelvärdet för en viss kolumn) över varje rad som *uttrycket* returnerar true. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

I följande exempel returneras lämpliga förskjutningar för delning av en CSV-formaterad blob.  Se avsnittet [sys. Split](#sys-split) i den här artikeln.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>Datatyper

|Datatyp|Beskrivning|
|---------|-------------------------------------------|
|INT      |64-bitars heltal.                     |
|FLYTA    |64-bitars ("dubbel precision") svävande punkt.|
|NOLLÄNGD   |Unicode-sträng med variabel längd.            |
|TIMESTAMP|En tidpunkt.                           |
|BOOLESKT  |Sant eller falskt.                             |

När du läser värden från CSV-formaterade data läses alla värden som strängar.  Sträng värden kan konverteras till andra typer med hjälp av CAST-uttryck.  Värden kan vara implicit omvandlas till andra typer beroende på kontext. Mer information finns i [data typs prioritet (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql).

## <a name="expressions"></a>Uttryck

### <a name="referencing-fields"></a>Referens fält

För JSON-formaterade data, eller CSV-formaterade data med en rubrik rad, kan fält refereras till efter namn.  Fält namn kan anges i citat tecken eller citat tecken. Fält namn som omges av dubbla citat tecken ("), kan innehålla blank steg och är Skift läges känsliga.  Ej citerade fält namn är Skift läges okänsliga och får inte innehålla specialtecken.

I CSV-formaterade data kan fält också refereras till av ordnings tal, som föregås av ett under streck (_).  Till exempel kan det första fältet refereras som _1, eller så kan det elfte fältet refereras som _11.  Att referera till fält efter ordnings tal är användbart för CSV-formaterade data som inte innehåller någon rubrik rad, i vilket fallet är det enda sättet att referera till ett visst fält enligt ordnings tal.

### <a name="operators"></a>Operatorer

Följande standard-SQL-operatorer stöds:

|Operator|Beskrivning|
|--|--|
|[=](/sql/t-sql/language-elements/equals-transact-sql)    |Jämför likheten mellan två uttryck (en jämförelse operator).|
|[!=](/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |Testar om ett uttryck inte är lika med ett annat uttryck (en jämförelse operator).|
|[<>](/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |Jämför två uttryck som inte är lika med (en jämförelse operator).|
|[<](/sql/t-sql/language-elements/less-than-transact-sql)    |Jämför två uttryck för mindre än (en jämförelse operator).|
|[<=](/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |Jämför två uttryck för mindre än eller lika med (en jämförelse operator).|
|[>](/sql/t-sql/language-elements/greater-than-transact-sql)    |Jämför två uttryck för större än (en jämförelse operator). |
|[>=](/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |Jämför två uttryck för större än eller lika med (en jämförelse operator).|
|[+](/sql/t-sql/language-elements/add-transact-sql)    |Adderar två tal. Denna additiona aritmetiska operator kan också lägga till ett tal, i dagar, till ett datum.|
|[-](/sql/t-sql/language-elements/subtract-transact-sql)    |Subtraherar två tal (en aritmetisk under traktions operator). |
|[/](/sql/t-sql/language-elements/divide-transact-sql)    |Dividerar ett tal med ett annat (en aritmetisk divisions operator).|
|[*](/sql/t-sql/language-elements/multiply-transact-sql)    |Multiplicerar två uttryck (en aritmetisk operator för multiplikation).|
|[%](/sql/t-sql/language-elements/modulo-transact-sql)    |Returnerar resten av ett tal dividerat med ett annat.|
|[AND](/sql/t-sql/language-elements/bitwise-and-transact-sql)    |Utför en bitvis logisk och åtgärd mellan två heltals värden.|
|[OR](/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Utför en bitvis logisk eller åtgärd mellan två angivna heltals värden som översatta till binära uttryck i Transact-SQL-uttryck.|
|[NOT](/sql/t-sql/language-elements/not-transact-sql)    |Negerar en boolesk Indatatyp.|
|[CAST](/sql/t-sql/functions/cast-and-convert-transact-sql)    |Konverterar ett uttryck med en datatyp till en annan.|
|[DELAS](/sql/t-sql/language-elements/between-transact-sql)    |Anger ett intervall som ska testas.|
|[FÖR](/sql/t-sql/language-elements/in-transact-sql)    |Anger om ett angivet värde matchar ett värde i en under fråga eller en lista.|
|[NULLIF](/sql/t-sql/language-elements/nullif-transact-sql)    |Returnerar ett null-värde om de två angivna uttrycken är lika.|
|[COALESCE](/sql/t-sql/language-elements/coalesce-transact-sql)    |Utvärderar argumenten i ordning och returnerar det aktuella värdet för det första uttrycket som ursprungligen inte utvärderas till NULL.|

Om data typer till vänster och höger om en operator skiljer sig åt kommer den automatiska konverteringen att utföras enligt de regler som anges här: [data typs prioritet (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql).

SQL-språket för Query acceleration stöder bara en liten del av de data typer som beskrivs i artikeln.  Se avsnittet [data typer](#data-types) i den här artikeln.

### <a name="casts"></a>Sändningar

SQL-språket för frågespråk stöder CAST-operatorn, enligt reglerna här: [data typs konvertering (databas motor)](/sql/t-sql/data-types/data-type-conversion-database-engine).  

SQL-språket för frågespråk stöder bara en liten delmängd av de data typer som beskrivs i artikeln.  Se avsnittet [data typer](#data-types) i den här artikeln.

### <a name="string-functions"></a>Strängfunktioner

SQL-språket för frågespråk stöder följande SQL-standardfunktioner:

|Funktion|Beskrivning|
|--|--|
|CHAR_LENGTH    | Returnerar längden i tecken i sträng uttrycket, om sträng uttrycket är av en tecken data typ; annars returneras längden i byte för sträng uttrycket (det minsta heltal som inte är mindre än antalet bitar dividerat med 8). (Den här funktionen är samma som CHARACTER_LENGTH-funktionen.)|
|CHARACTER_LENGTH    |Returnerar längden i tecken i sträng uttrycket, om sträng uttrycket är av en tecken data typ; annars returneras längden i byte för sträng uttrycket (det minsta heltal som inte är mindre än antalet bitar dividerat med 8). (Den här funktionen är samma som funktionen CHAR_LENGTH|
|[LOWER](/sql/t-sql/functions/lower-transact-sql)    |Returnerar ett tecken uttryck efter konvertering av versaler till gemener.|
|[UPPER](/sql/t-sql/functions/upper-transact-sql)    |Returnerar ett tecken uttryck med gemena tecken som konverterats till versaler.|
|[UNDER sträng](/sql/t-sql/functions/substring-transact-sql)    |Returnerar en del av ett tecken-, binär-, text-eller bild uttryck i SQL Server.|
|[TRIM](/sql/t-sql/functions/trim-transact-sql)    |Tar bort blank steg tecken (32) eller andra angivna tecken från början och slutet av en sträng.|
|LEDANDE    |Beskrivning|
|AVSLUTANDE    |Beskrivning|

Här är några exempel:

|Funktion|Exempel|Resultat|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>Datumfunktioner

Följande SQL-datum stöds i standard:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

För närvarande konverterar vi alla [datum format för standard-IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>DATE_ADD funktion

SQL-språket för frågespråk stöder år, månad, dag, timme, minut, sekund för ``DATE_ADD`` funktionen.

Exempel:

SQL DATE_ADD (DatumDel, Quantity, Quantity) DATE_ADD (minut, 1, CAST (' 2017-01-02T03:04:05.006 Z "som tidsstämpel)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRAHERA funktion

För att EXTRAHERA andra än den datum del som stöds för ``DATE_ADD`` funktionen stöder FRÅGESPRÅKET SQL-språket timezone_hour och timezone_minute som datum del.

Exempel:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING funktion

Exempel:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

I den här tabellen beskrivs strängar som du kan använda för att ange utdataformatet för ``TO_STRING`` funktionen.

|Formatsträng    |Utdata                               |
|-----------------|-------------------------------------|
|yy               |År i 2-siffrigt format – 1999 som "99"|
|y                |År med 4 siffer format               |
|yyyy             |År med 4 siffer format               |
|M                |Månad på år – 1                    |
|MM               |Noll utfylld månad – 01               |
|MMM              |Förkortning. månad på år – JAN            |
|MMMM             |Fullständig månad – kan                      |
|d                |Dag i månaden (1-31)                  |
|dd               |Noll i utfylld dag i månad (01-31)     |
|a                |FM eller EM                             |
|h                |Timme på dagen (1-12)                   |
|hh               |Noll utfyllda timmar OD-dagen (01-12)     |
|H                |Timme på dagen (0-23)                   |
|HH               |Noll i utfylld timme på dagen (00-23)      |
|m                |Minut i timmen (0-59)                |
|mm               |Noll i utfyllt minut (00-59)           |
|s                |Sekund i minuter (0-59)             |
|ss               |Noll utfyllnad i sekunder (00-59)          |
|S                |Bråkdel av sekunder (0,1 – 0,9)        |
|SS               |Del av sekunder (0,01-0,99)      |
|LAGRING              |Bråkdel av sekunder (0,001-0.999)    |
|X                |Förskjutning i timmar                      |
|XX eller XXXX       |Förskjutning i timmar och minuter (+ 0430)  |
|XXX eller XXXXX     |Förskjutning i timmar och minuter (-07:00) |
|x                |Förskjutning i timmar (7)                  |
|xx eller xxxx       |Förskjutning i timma och minut (+ 0530)    |
|XXX eller xxxxx     |Förskjutning i timma och minut (+ 05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP funktion

Endast IS08601-format stöds.

Exempel:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Du kan också använda ``UTCNOW`` funktionen för att hämta system tiden.


## <a name="aggregate-expressions"></a>Mängd uttryck

Ett SELECT-uttryck kan innehålla antingen ett eller flera projektioner eller ett enda agg regerings uttryck.  Följande agg regerings uttryck stöds:

|Uttryck|Beskrivning|
|--|--|
|[COUNT ( \* )](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Returnerar antalet poster som matchade predikatet-uttrycket.|
|[COUNT (uttryck)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Returnerar antalet poster för vilka uttrycket inte är null.|
|[MEDEL (uttryck)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |Returnerar medelvärdet av uttryck som inte är null-värden.|
|[MIN (uttryck)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |Returnerar det minsta värdet för uttrycket som inte är null.|
|[MAX (uttryck](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |Returnerar det högsta värdet för uttrycket som inte är null.|
|[SUM (uttryck)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |Returnerar summan av alla värden som inte är null i uttrycket.|

### <a name="missing"></a>INGEN

``IS MISSING``Operatorn är den enda icke-standard som stöds av FRÅGESPRÅKET SQL-språket.  För JSON-data, om ett fält saknas i en viss inmatnings post, kommer uttrycks fältet ``IS MISSING`` att utvärderas till det booleska värdet true.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Tabell beskrivningar

Tabell namnet är alltid för CSV-data `BlobStorage` .  Exempel:

```sql
SELECT * FROM BlobStorage
```

För JSON-data finns ytterligare alternativ:

```sql
SELECT * FROM BlobStorage[*].path
```

Detta tillåter frågor över del mängder av JSON-data.

För JSON-frågor kan du nämna sökvägen i en del av FROM-satsen. Dessa sökvägar kommer att hjälpa till att parsa delmängd av JSON-data. Dessa sökvägar kan referera till JSON-matris-och objekt värden.

Låt oss ta ett exempel på att förstå detta i mer detalj.

Detta är våra exempel data:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Du kanske bara är intresse rad av `warehouses` JSON-objektet från ovanstående data. `warehouses`Objektet är en JSON-mat ris typ, så du kan nämna detta i from-satsen. Exempel frågan kan se ut ungefär så här.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

Frågan hämtar alla fält men väljer bara latitud.

Om du bara vill komma åt `dimensions` JSON-cellvärdet kan du använda referera till objektet i din fråga. Exempel:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Detta begränsar också åtkomsten till medlemmar i `dimensions` objektet. Om du vill komma åt andra medlemmar i JSON-fält och inre värden för JSON-objekt kan du använda en fråga som visas i följande exempel:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage och BlobStorage [ \* ] refererar båda till hela objektet. Men om du har en sökväg i FROM-satsen måste du använda BlobStorage [ \* ]. Path

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys. Split

Detta är en särskild form av SELECT-instruktionen, som endast är tillgänglig för CSV-formaterade data.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Använd den här instruktionen i fall där du vill hämta och bearbeta CSV-dataposter i batchar. På så sätt kan du bearbeta poster parallellt i stället för att behöva ladda ned alla poster på en gång. Den här instruktionen returnerar inte poster från CSV-filen. I stället returnerar den en samling batch-storlekar. Du kan sedan använda varje batch-storlek för att hämta en batch med data poster. 

Använd parametern *split_size* för att ange antalet byte som du vill att varje batch ska innehålla. Om du till exempel bara vill bearbeta 10 MB data i taget skulle du se så här: `SELECT sys.split(10485760)FROM BlobStorage` eftersom 10 MB är lika med 10 485 760 byte. Varje batch kommer att innehålla så många poster som får plats i de 10 MB. 

I de flesta fall är storleken på varje grupp något högre än det tal som du anger. Det beror på att en batch inte kan innehålla en partiell post. Om den sista posten i en batch startar före slutet av ditt tröskelvärde blir batchen större så att den kan innehålla hela posten. Storleken på den senaste batchen kommer förmodligen att vara mindre än den storlek som du anger.

>[!NOTE]
> Split_size måste vara minst 10 MB (10485760).

## <a name="see-also"></a>Se även

- [Azure Data Lake Storage fråga om acceleration](data-lake-storage-query-acceleration.md)
- [Filtrera data med hjälp av Azure Data Lake Storage fråga om acceleration](data-lake-storage-query-acceleration-how-to.md)