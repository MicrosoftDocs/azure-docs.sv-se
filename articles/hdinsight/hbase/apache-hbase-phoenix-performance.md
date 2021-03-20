---
title: Phoenix-prestanda i Azure HDInsight
description: Metod tips för att optimera Apache Phoenix prestanda för Azure HDInsight-kluster
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: ffba3b986b35c375d0404d9d2bae5af79f93c54f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944800"
---
# <a name="apache-phoenix-performance-best-practices"></a>Metodtips för prestanda för Apache Phoenix

Den viktigaste aspekten av [Apache Phoenix](https://phoenix.apache.org/) prestanda är att optimera de underliggande [Apache-HBase](https://hbase.apache.org/). Phoenix skapar en Relations data modell ovanpå-HBase som konverterar SQL-frågor till HBase-åtgärder, till exempel genomsökningar. Utformningen av ditt tabell schema, urvalet och ordningen av fälten i din primära nyckel och användningen av index påverkar prestanda i Linköping.

## <a name="table-schema-design"></a>Tabell schema design

När du skapar en tabell i Phoenix lagras tabellen i en HBase-tabell. Tabellen HBase innehåller grupper med kolumner (kolumn familjer) som kan nås tillsammans. En rad i Phoenix-tabellen är en rad i HBase-tabellen där varje rad består av versions celler som är associerade med en eller flera kolumner. Logiskt sett är en enda HBase-rad en samling nyckel/värde-par som var och en har samma rowkey-värde. Det vill säga varje nyckel/värde-par har ett rowkey-attribut och värdet för det rowkey-attributet är detsamma för en viss rad.

Schema designen för en Phoenix-tabell innehåller primär nyckel design, kolumn serie design, individuell kolumn design och hur data partitioneras.

### <a name="primary-key-design"></a>Primär nyckel design

Den primära nyckeln som definieras i en tabell i Phoenix avgör hur data lagras i rowkey i den underliggande HBase-tabellen. I HBase är det enda sättet att komma åt en viss rad med rowkey. Dessutom sorteras data som lagras i en HBase-tabell av rowkey. Phoenix skapar rowkey-värdet genom att sammanfoga värdena för varje kolumn i raden, i den ordning som de är definierade i primär nyckeln.

En tabell för kontakter har till exempel förnamn, efter namn, telefonnummer och adress, allt i samma kolumn serie. Du kan definiera en primär nyckel baserat på ett ökande ordnings nummer:

|rowkey|       adress|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San-Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San-Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Men om du ofta frågar efter lastName kanske den primära nyckeln inte fungerar bra eftersom varje fråga kräver en fullständig tabells ökning för att läsa värdet för varje efter namn. I stället kan du definiera en primär nyckel på kolumnerna lastName, firstName och social security number. Den sista kolumnen är att disambiguate två invånare på samma adress med samma namn, till exempel en far och son.

|rowkey|       adress|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San-Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San-Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Med den nya primära nyckeln skulle rad nycklarna som genereras av Phoenix vara:

|rowkey|       adress|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San-Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San-Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

På den första raden ovan representeras data för rowkey som visas:

|rowkey|       key|   värde|
|------|--------------------|---|
|  Dole-John-111|adress |1111 San-Gabriel Dr.|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111|

I den här rowkey lagras nu en kopia av data. Tänk på storlek och antal kolumner som du inkluderar i primär nyckeln, eftersom det här värdet ingår i varje cell i den underliggande HBase-tabellen.

Om den primära nyckeln har värden som är monotont ökande bör du skapa tabellen med *salt buckets* för att undvika att skapa Skriv hotspots – se [partitioners data](#partition-data).

### <a name="column-family-design"></a>Design av kolumn serie

Om vissa kolumner används oftare än andra bör du skapa flera kolumn familjer för att avgränsa de ofta använda kolumnerna från kolumner som sällan används.

Om vissa kolumner ofta är tillgängliga tillsammans, så Lägg till dessa kolumner i samma kolumn serie.

### <a name="column-design"></a>Kolumn design

* Behåll VARCHAR-kolumner under ungefär 1 MB på grund av I/O-kostnaderna för stora kolumner. Vid bearbetning av frågor materialiserar HBase celler helt innan de skickas till klienten och klienten får dem fullständigt innan de lämnar dem till program koden.
* Lagra kolumn värden med ett kompakt format, till exempel protobuf, Avro, msgpack eller BSON. JSON rekommenderas inte, eftersom den är större.
* Överväg att komprimera data före lagring för att minska svars tid och I/O-kostnader.

### <a name="partition-data"></a>Partitionera data

Med Phoenix kan du styra antalet regioner där dina data ska distribueras, vilket avsevärt ökar Läs-och skriv prestanda. När du skapar en Phoenix-tabell kan du antingen använda salt eller för att dela upp dina data.

För att salta en tabell när den skapas anger du antalet salt buckets:

```sql
CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16
```

Den här saltningen delar upp tabellen längs värdena för primära nycklar och väljer värdena automatiskt. 

För att kontrol lera var tabell delningarna sker, kan du i förväg dela tabellen genom att ange de intervall värden som delningen sker tillsammans. Om du till exempel vill skapa en tabell delning längs tre regioner:

```sql
CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')
```

## <a name="index-design"></a>Indexdesign

Ett Phoenix-index är en HBase-tabell som lagrar en kopia av vissa eller alla data från den indexerade tabellen. Ett index ger bättre prestanda för vissa typer av frågor.

När du har definierat flera index och sedan frågar en tabell, väljer Phoenix automatiskt det bästa indexet för frågan. Det primära indexet skapas automatiskt baserat på de primära nycklar som du väljer.

För förväntade frågor kan du också skapa sekundära index genom att ange deras kolumner.

När du designar dina index:

* Skapa bara de index du behöver.
* Begränsa antalet index för tabeller som uppdateras ofta. Uppdateringar av en tabell översätts till skrivningar till både huvud tabellen och index tabellerna.

## <a name="create-secondary-indexes"></a>Skapa sekundära index

Sekundära index kan förbättra Läs prestanda genom att aktivera vad som skulle vara en fullständig tabells ökning till en punkts ökning, med kostnaden för lagrings utrymme och skriv hastighet. Sekundära index kan läggas till eller tas bort när tabellen har skapats och inte kräver ändringar i befintliga frågor – frågor körs snabbare. Beroende på dina behov kan du överväga att skapa skyddade index, funktionella index eller både och.

### <a name="use-covered-indexes"></a>Använd skyddade index

Index som omfattas är index som innehåller data från raden förutom de värden som indexeras. När du har hittat den önskade index posten behöver du inte komma åt den primära tabellen.

I exempel kontakt tabellen kan du till exempel skapa ett sekundärt index för bara kolumnen socialSecurityNum. Det här sekundära indexet påskyndar frågor som filtrerar efter socialSecurityNum värden, men om du hämtar andra fält värden krävs en annan läsning mot huvud tabellen.

|rowkey|       adress|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San-Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San-Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Men om du vanligt vis vill söka efter firstName och lastName som har tilldelats socialSecurityNum, kan du skapa ett index som inkluderar firstName och lastName som faktiska data i index tabellen:

```sql
CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);
```

Det här skyddade indexet gör att följande fråga kan hämta alla data genom att läsa från tabellen som innehåller det sekundära indexet:

```sql
SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;
```

### <a name="use-functional-indexes"></a>Använda funktionella index

Med funktionella index kan du skapa ett index för ett godtyckligt uttryck som du förväntar dig att använda i frågor. När du har ett funktionellt index på plats och en fråga använder uttrycket kan indexet användas för att hämta resultatet i stället för data tabellen.

Du kan till exempel skapa ett index så att du kan göra Skift läges okänsliga sökningar i det kombinerade förnamnet och efter namnet på en person:

```sql
CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));
```

## <a name="query-design"></a>Frågans design

Huvud övervägandena i frågans design är:

* Förstå frågeplanen och verifiera förväntat beteende.
* Anslut effektivt.

### <a name="understand-the-query-plan"></a>Förstå fråge planen

I [SQLLine](http://sqlline.sourceforge.net/)kan du använda förklaring följt av din SQL-fråga för att visa den plan med åtgärder som Phoenix kommer att utföra. Kontrol lera att planen:

* Använder primär nyckeln vid behov.
* Använder lämpliga sekundära index i stället för data tabellen.
* Använder SÖKNINGs intervall eller hoppar över genomsökning när det är möjligt, i stället för TABELLs ökning.

#### <a name="plan-examples"></a>Planera exempel

Anta till exempel att du har en tabell som heter flygningar som lagrar information om flyg fördröjning.

För att välja alla flygningar med en airlineid av `19805` , där airlineid är ett fält som inte finns i primär nyckeln eller i något index:

```sql
select * from "FLIGHTS" where airlineid = '19805';
```

Kör kommandot förklaring enligt följande:

```sql
explain select * from "FLIGHTS" where airlineid = '19805';
```

Fråge planen ser ut så här:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
   SERVER FILTER BY AIRLINEID = '19805'
```

I den här planen noterar du frasen fullständig sökning över flygningar. Den här frasen anger att körningen gör en TABELLs ökning över alla rader i tabellen, i stället för att använda det mer effektiva INTERVALLET för genomsökning eller SKIP-sökalternativ.

Anta nu att du vill fråga efter flygningar den 2 januari 2014 för operatören `AA` där dess flightnum var större än 1. Vi antar att kolumnerna Year, Month, DAYOFMONTH, transport och flightnum finns i exempel tabellen och är en del av den sammansatta primär nyckeln. Frågan skulle se ut så här:

```sql
select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Vi ska undersöka planen för den här frågan med:

```sql
explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Den resulterande planen är:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]
```

Värdena i hakparenteser är värde intervallet för de primära nycklarna. I det här fallet åtgärdas värdena för intervallet med år 2014, månad 1 och DAYOFMONTH 2, men tillåt värden för flightnum som börjar med 2 och på upp ( `*` ). Den här fråge planen bekräftar att den primära nyckeln används som förväntat.

Skapa sedan ett index i tabellen flygningar med namnet `carrier2_idx` som bara finns i fältet transport företags. Indexet inkluderar även flightdate, tailnum, Origin och flightnum som täckta kolumner vars data också lagras i indexet.

```sql
CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);
```

Anta att du vill hämta transport företaget tillsammans med flightdate och tailnum, som i följande fråga:

```sql
explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';
```

Du bör se att det här indexet används:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']
```

En fullständig lista över de objekt som kan visas i förklarings resultaten finns i avsnittet förklarings planer i [Apache Phoenix justerings guiden](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Anslut effektivt

I allmänhet vill du undvika kopplingar om inte en sida är liten, särskilt för vanliga frågor.

Om det behövs kan du göra stora kopplingar med `/*+ USE_SORT_MERGE_JOIN */` tipset, men en stor koppling är en dyr åtgärd över ett stort antal rader. Om den totala storleken på alla högra tabell sidor skulle överskrida det tillgängliga minnet använder du `/*+ NO_STAR_JOIN */` tipset.

## <a name="scenarios"></a>Scenarier

Följande rikt linjer beskriver några vanliga mönster.

### <a name="read-heavy-workloads"></a>Läs-tunga arbets belastningar

Kontrol lera att du använder index för att läsa tunga användnings fall. Du kan dessutom överväga att skapa skyddade index om du vill spara Läs tiden.

### <a name="write-heavy-workloads"></a>Skriv tunga arbets belastningar

För Skriv tunga arbets belastningar där den primära nyckeln är monotont ökande, skapar du salt buckets för att undvika Skriv-hotspots, vid kostnaden för det övergripande läsnings data flödet på grund av de ytterligare genomsökningarna som behövs. När du använder UPSERT för att skriva ett stort antal poster inaktiverar du sedan autocommning och batch-poster.

### <a name="bulk-deletes"></a>Mass borttagningar

När du tar bort en stor data uppsättning aktiverar du autocommning innan du utfärdar BORTTAGNINGs frågan, så att klienten inte behöver komma ihåg rad nycklarna för alla borttagna rader. AUTOCOMMIT förhindrar att klienten buffrar raderna som påverkas av BORTTAGNINGen, så att de kan ta bort dem direkt på region servrarna utan att kostnaden för att returnera dem skickas till klienten.

### <a name="immutable-and-append-only"></a>Oåterkalleliga och enbart tillagda

Om ditt scenario prioriterar skriv hastigheten över data integriteten bör du överväga att inaktivera Skriv loggen när du skapar dina tabeller:

```sql
CREATE TABLE CONTACTS (...) DISABLE_WAL=true;
```

Mer information om det här och andra alternativ finns i [Apache Phoenix grammatik](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Nästa steg

* [Apache Phoenix justerings guide](https://phoenix.apache.org/tuning_guide.html)
* [Sekundära index](https://phoenix.apache.org/secondary_indexing.html)
