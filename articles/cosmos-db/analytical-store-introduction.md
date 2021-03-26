---
title: Vad är Azure Cosmos DB Analytical Store?
description: Lär dig mer om Azure Cosmos DB transaktionell (rad-och kolumnbaserade) och analys (kolumnbaserade). Fördelar med analytisk lagring, prestanda påverkan för storskaliga arbets belastningar och automatisk synkronisering av data från transaktions lagring till analytisk lagring
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: 450514541a90a01ea6b70f77491f116adb404887
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046221"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Vad är Azure Cosmos DB Analytical Store?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB Analytical Store är ett fullständigt isolerat kolumn lager för att möjliggöra storskalig analys av drift data i din Azure Cosmos DB, utan att påverka dina transaktions arbets belastningar. 

Azure Cosmos DB transaktions lager är schema-oberoende, och det gör att du kan iterera i dina transaktions program utan att behöva hantera schema-eller index hantering. I motsats till detta är Azure Cosmos DB Analytical Store schematiserade för att optimera prestanda för analytiska frågor. Den här artikeln beskriver i detalj om analytisk lagring.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Utmaningar med storskalig analys av drift data

Användnings data för flera modeller i en Azure Cosmos DB-behållare lagras internt i en indexerad rad baserad "transaktions lagring". Rad lagrings formatet är utformat för att tillåta snabba och skrivskyddade transaktions läsningar och skriv åtgärder i svars tiderna för svars tid i millisekunder och operativa frågor. Om din data uppsättning ökar stora kan komplexa analytiska frågor vara dyra när det gäller data flöde för data som lagras i det här formatet. Hög konsumtion av etablerade data flöden i sin tur påverkar prestanda för transaktions arbets belastningar som används av dina program och tjänster i real tid.

Traditionellt, för att analysera stora mängder data, extraheras drift data från Azure Cosmos DB transaktions lager och lagras i ett separat data lager. Data lagras till exempel i ett data lager eller data Lake i ett lämpligt format. Dessa data används senare för storskalig analys och analyseras med hjälp av beräknings motorn, till exempel Apache Spark kluster. Den här separationen av analys lagrings-och beräknings lager från användnings data resulterar i ytterligare svars tider, eftersom ETL-pipelinen (Extract, Transform, Load) körs mindre ofta för att minimera potentiell påverkan på dina transaktions arbets belastningar.

ETL-pipelinen blir också komplexa vid hantering av uppdateringar av användnings data jämfört med hantering av enbart nyligen inmatade drift data. 

## <a name="column-oriented-analytical-store"></a>Kolumn-orienterad analys lager

Azure Cosmos DB Analytical Store tar itu med de komplexitets-och latens utmaningar som inträffar med de traditionella ETL-pipelinen. Azure Cosmos DB Analytical Store kan automatiskt synkronisera dina drift data till ett separat kolumn lager. Formatet för kolumn lagring är lämpligt för storskaliga analys frågor som ska utföras på ett optimerat sätt, vilket leder till att förbättra svars tiderna för sådana frågor.

Med Azure Synapse-länken kan du nu skapa inga ETL HTAP-lösningar genom att länka direkt till Azure Cosmos DB analys lager från Azure Synapse Analytics. Med den kan du köra storskalig storskalig analys i real tid på dina användnings data.

## <a name="features-of-analytical-store"></a>Funktioner i analys lager 

När du aktiverar analytisk lagring på en Azure Cosmos DB behållare skapas en ny kolumn lagring internt baserat på drift data i din behållare. Det här kolumn arkivet är bestående separat från det radbaserade transaktions arkivet för den behållaren. Infogningar, uppdateringar och borttagningar i dina användnings data synkroniseras automatiskt till analytisk lagring. Du behöver inte ändra feed eller ETL för att synkronisera data.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Kolumn lagring för analytiska arbets belastningar på operativa data

Analytiska arbets belastningar inkluderar vanligt vis agg regeringar och sekventiella genomsökningar av valda fält. Genom att lagra data i en kolumn – huvud ordning kan du med analys lagret en grupp värden för varje fält serialiseras tillsammans. Det här formatet minskar de IOPS som krävs för att skanna eller beräkna statistik över vissa fält. Det förbättrar kraftigt svars tiderna för genomsökningar över stora data mängder. 

Om dina operativa tabeller till exempel är i följande format:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Exempel på drift tabell" border="false":::

Rad arkivet behåller ovanstående data i ett serialiserat format, per rad, på disken. Det här formatet möjliggör snabbare transaktionella läsningar, skrivningar och operativa frågor, till exempel "returnera information om Product1". Men eftersom data mängden växer stor och om du vill köra komplexa analys frågor på data kan det vara dyrt. Om du till exempel vill hämta "försäljnings trender för en produkt under kategorin" utrustning "i olika affär senheter och månader" måste du köra en komplex fråga. Stora genomsökningar i den här data uppsättningen kan bli dyra när det gäller data flöde och kan också påverka prestandan för transaktions arbets belastningar som påverkar dina real tids program och-tjänster.

Analys lager, som är en kolumn lagring, passar bättre för sådana frågor eftersom det serialiserar likartade data fält tillsammans och minskar disken IOPS.

Följande bild visar transaktions rad lagring jämfört med analytisk kolumn lagring i Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transaktions rad lagring vs analytisk kolumn lagring i Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Frikopplad prestanda för analytiska arbets belastningar

Det påverkar inte prestandan hos dina transaktions arbets belastningar på grund av analytiska frågor, eftersom analys lagret är skilt från transaktions arkivet.  Ett analys lager behöver inte separera ru: er-enheter för att allokeras.

### <a name="auto-sync"></a>Automatisk synkronisering

Automatisk synkronisering syftar på den fullständigt hanterade funktionen i Azure Cosmos DB där infogningar, uppdateringar och borttagningar av drifts data automatiskt synkroniseras från transaktions lagring till analytisk lagring i nära real tid. Svars tiden för automatisk synkronisering är vanligt vis inom 2 minuter. I händelse av en delad data flödes databas med ett stort antal behållare kan svars tiden för automatisk synkronisering av enskilda behållare vara högre och ta upp till 5 minuter. Vi vill veta mer om hur svars tiden passar dina scenarier. För det kan du kontakta [Azure Cosmos DB-teamet](mailto:cosmosdbsynapselink@microsoft.com).

Funktionen för automatisk synkronisering tillsammans med analytiskt lager ger följande viktiga fördelar:

### <a name="scalability--elasticity"></a>Skalbarhets & elastiskhet

Genom att använda vågrät partitionering kan Azure Cosmos DB transaktions lager elastiskt skala lagringen och data flödet utan avbrott. Horisontell partitionering i transaktions arkivet ger skalbarhet & elastiskhet i automatisk synkronisering för att säkerställa att data synkroniseras med analys lagret i nära real tid. Datasynkroniseringen sker oavsett transaktions trafik genom strömning, om det är 1000 åtgärder/SEK eller 1 000 000 åtgärder/SEK, och det inte påverkar det etablerade data flödet i transaktions arkivet. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Hantera schema uppdateringar automatiskt

Azure Cosmos DB transaktions lager är schema-oberoende, och det gör att du kan iterera i dina transaktions program utan att behöva hantera schema-eller index hantering. I motsats till detta är Azure Cosmos DB Analytical Store schematiserade för att optimera prestanda för analytiska frågor. Med funktionen för automatisk synkronisering hanterar Azure Cosmos DB schemats härledning över de senaste uppdateringarna från transaktions arkivet.  Den hanterar också schema representationen i den analytiska lagringen som ingår i den här typen av hantering av kapslade data typer.

När schemat utvecklas och nya egenskaper läggs till med tiden visar analys arkivet automatiskt ett uppdelat schema över alla historiska scheman i transaktions arkivet.

#### <a name="schema-constraints"></a>Schema begränsningar

Följande begränsningar gäller för användnings data i Azure Cosmos DB när du aktiverar analytisk lagring så att det automatiskt härleds och representerar schemat korrekt:

* Du kan ha högst 1000 egenskaper på valfri kapslings nivå i schemat och ett maximalt kapslings djup på 127.
  * Endast de första 1000 egenskaperna visas i analys lagret.
  * Endast de första 127 kapslade nivåerna visas i analys lagret.

* JSON-dokument (och Cosmos DB samlingar/behållare) är Skift läges känsliga från det unika perspektivet, vilket inte är analytiskt lagrings utrymme.

  * **I samma dokument:** Egenskaps namn på samma nivå måste vara unika vid jämförelse av Skift läges okänslighet. Följande JSON-dokument har till exempel "name" och "name" på samma nivå. Även om det är ett giltigt JSON-dokument uppfyller det inte unikhetsvillkoret och kommer därför inte att visas fullständigt i analys lagret. I det här exemplet är "namn" och "namn" samma vid jämförelse i ett skift läges okänsligt sätt. Visas endast `"Name": "fred"` i analys lager, eftersom det är den första förekomsten. Och `"name": "john"` visas inte alls.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **I olika dokument:** Egenskaper på samma nivå och med samma namn, men i olika fall, visas i samma kolumn med namn formatet för den första förekomsten. Till exempel har följande JSON-dokument `"Name"` och `"name"` på samma nivå. Eftersom det första dokument formatet är `"Name"` , är det här det som kommer att användas för att representera egenskaps namnet i analys arkivet. Med andra ord är kolumn namnet i analys lager `"Name"` . Både `"fred"` och `"john"` kommer att visas i `"Name"` kolumnen.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Det första dokumentet i samlingen definierar det första analys lagrings schemat.
  * Egenskaperna i dokumentets första nivå visas som kolumner.
  * Dokument med fler egenskaper än det ursprungliga schemat genererar nya kolumner i ett analys lager.
  * Det går inte att ta bort kolumner.
  * Om du tar bort alla dokument i en samling återställs inte analys lagrets schema.
  * Det finns ingen schema versions hantering. Den senaste versionen som härleds från transaktions arkivet är vad du ser i analys lager.

* För närvarande har vi inte stöd för att läsa kolumn namn för Azure Synapse Spark som innehåller blank steg (blank steg).

* Ett annat förväntat beteende i förhållande till explicita `null` värden:
  * Spark-pooler i Azure Synapse kommer att läsa dessa värden som `0` (noll).
  * SQL Server-pooler i Azure Synapse kommer att läsa dessa värden som `NULL` om det första dokumentet i samlingen har, för samma egenskap, ett värde med en `non-numeric` datatyp.
  * SQL Server-pooler i Azure Synapse kommer att läsa dessa värden som `0` (noll) om det första dokumentet i samlingen har, för samma egenskap, ett värde med en `numeric` datatyp.

* Förväntar dig olika beteenden i samband med saknade kolumner:
  * Spark-pooler i Azure Synapse kommer att representera dessa kolumner som `undefined` .
  * SQL Server-pooler i Azure Synapse kommer att representera dessa kolumner som `NULL` .

#### <a name="schema-representation"></a>Schema representation

Det finns två lägen för schemarepresentation i analysarkivet. De här lägena utgör en kompromiss mellan enkelheten i kolumnrepresentation, hanteringen av polymorfiska scheman och enkelheten i frågeupplevelsen:

* Väldefinierad schema representation
* Schema representation med fullständig åter givning

> [!NOTE]
> För SQL (Core) API-konton, när analys lager är aktiverat, är standard schema representationen i analys lagret väl definierad. För Azure Cosmos DB-API för MongoDB-konton är standard schema representationen i analys lagret en fullständig åter givning av schema. Om du har scenarier som kräver en annan schema representation än standard erbjudandet för var och en av dessa API: er, kan du kontakta [Azure Cosmos DB-teamet](mailto:cosmosdbsynapselink@microsoft.com) för att aktivera det.

**Väldefinierad schema representation**

Den väldefinierade schema representationen skapar en enkel tabell representation av schema-oberoende data i transaktions arkivet. Den väldefinierade schema representationen har följande överväganden:

* En egenskap har alltid samma typ för flera objekt.

  * Har till exempel `{"a":123} {"a": "str"}` inte ett väldefinierat schema eftersom det `"a"` ibland är en sträng och ibland ett tal. I det här fallet registrerar analys arkivet data typen `"a"` som data typen för `“a”` i det första objektet i behållarens livs längd. Dokumentet kommer fortfarande att ingå i ett analys lager, men objekt där data typen `"a"` skiljer sig.
  
    Det här villkoret gäller inte för null-egenskaper. Är till exempel `{"a":123} {"a":null}` fortfarande väl definierat.

* Mat ris typer måste innehålla en enskild upprepad typ.

  * Är till exempel `{"a": ["str",12]}` inte ett väldefinierat schema eftersom matrisen innehåller en blandning av heltals-och sträng typer.

> [!NOTE]
> Om Azure Cosmos DB Analytical Store följer den väldefinierade schema representationen och specifikationen ovan överträds av vissa objekt, kommer dessa objekt inte att tas med i analys lagret.

* Förväntar dig olika beteenden i förhållande till olika typer i väl definierat schema:
  * Spark-pooler i Azure Synapse representerar dessa värden som `undefined` .
  * SQL Server-pooler i Azure-Synapse representerar dessa värden som `NULL` .


**Schema representation med fullständig åter givning**

Schema representationen full Fidelity är utformad för att hantera en hel bredd av polymorfa scheman i schemat-oberoende drift data. I den här schema representationen tas inga objekt bort från analys lagret även om de väldefinierade schema begränsningarna (som inte är blandade med blandade data typer eller data mat ris) överträds.

Detta uppnås genom att översätta löv egenskaperna för användnings data till analys lagret med distinkta kolumner baserat på data typen för värden i egenskapen. Löv egenskaps namnen utökas med data typer som suffix i analys lagrets schema, så att de kan vara frågor utan tvetydighet.

Låt oss till exempel ta följande exempel dokument i transaktions arkivet:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

Egenskapen lövnod `streetNo` i det kapslade objektet visas `address` i analys lagrings schema som en kolumn `address.object.streetNo.int32` . Data typen läggs till som suffix till kolumnen. På så sätt, om ett annat dokument läggs till i transaktions lagret där värdet för egenskapen lövnod `streetNo` är "123" (Observera att det är en sträng), utvecklas schemat för analys lagret automatiskt utan att ändra typen av en tidigare skriven kolumn. En ny kolumn som läggs till i analys arkivet, så som `address.object.streetNo.string` värdet "123" lagras.

**Data typen för mappning av suffix**

Här är en karta över alla egenskaps data typer och deras suffix i analys lagret:

|Ursprunglig datatyp  |Huvudnamnssuffix  |Exempel  |
|---------|---------|---------|
| Double |  ".float64" |    24,99|
| Matris | ". matris" |    ["a", "b"]|
|Binär | ". Binary" |0|
|Boolesk    | ". bool"   |Sant|
|Int32  | ". Int32"  |123|
|Int64  | ". Int64"  |255486129307|
|Null   | ". null"   | null|
|Sträng|    ". sträng" | "ABC"|
|Timestamp |    ". timestamp" |  Tidsstämpel (0, 0)|
|DateTime   |". datum"    | ISODate ("2020-08-21T07:43:07.375 Z")|
|ObjectId   |". objectId"    | ObjectId ("5f3f7b59330ec25c132623a2")|
|Dokument   |". objekt" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Kostnads effektiv arkivering av historiska data

Data skiktning avser separering av data mellan lagrings infrastrukturer som är optimerade för olika scenarier. Därigenom förbättra den övergripande prestandan och kostnads effektiviteten hos data stacken från slut punkt till slut punkt. Med analys lagring har Azure Cosmos DB nu stöd för automatisk data nivåer från transaktions lagringen till analytisk lagring med olika datalayouter. Med analys lagrings utrymme som är optimerat i förhållande till lagrings kostnaden jämfört med transaktions lagret, kan du behålla mycket längre horisonter av drift data för historisk analys.

När analys lagret har Aktiver ATS, baserat på data lagrings behoven för transaktions arbets belastningar, kan du konfigurera egenskapen transaktions Arkiv Time to Live (transaktions-TTL) så att poster tas bort automatiskt från transaktions arkivet efter en viss tids period. På samma sätt kan du med "analytiskt lagrings tid för Live (analytiskt TTL)" hantera livs cykeln för data som bevaras i analys lagret, oberoende av transaktions arkivet. Genom att aktivera analytisk lagring och konfigurera TTL-egenskaperna kan du smidigt och definiera data lagrings perioden för de två butikerna.

### <a name="global-distribution"></a>Global distribution

Om du har ett globalt distribuerat Azure Cosmos DB konto kommer det att vara tillgängligt i alla regioner i det kontot när du har aktiverat analytiskt lagrings utrymme för en behållare.  Ändringar i drift data replikeras globalt i alla regioner. Du kan köra analytiska frågor effektivt mot den närmaste regionala kopian av dina data i Azure Cosmos DB.

### <a name="security"></a>Säkerhet

Autentisering med analys lagret är detsamma som transaktions arkivet för en specifik databas. Du kan använda primära eller skrivskyddade nycklar för autentisering. Du kan utnyttja den länkade tjänsten i Synapse Studio för att förhindra att Azure Cosmos DB nycklar klistras in i Spark-anteckningsbokarna. Åtkomst till den här länkade tjänsten är tillgänglig för alla som har åtkomst till arbets ytan.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Stöd för flera Azure Synapse Analytics-körningar

Analys lagret är optimerat för att ge skalbarhet, elastiskhet och prestanda för analytiska arbets belastningar utan något beroende på beräknings körnings tiden. Lagrings tekniken är själv hanterad för att optimera dina analys arbets belastningar utan manuella åtgärder.

Genom att frikoppla det analytiska lagrings systemet från analys beräknings systemet, kan data i Azure Cosmos DB analys lager frågas samtidigt från olika analys körningar som stöds av Azure Synapse Analytics. Från och med idag stöder Azure Synapse Analytics Apache Spark och Server lös SQL-poolen med Azure Cosmos DB analys lager.

> [!NOTE]
> Du kan bara läsa från analys lager med Azure Synapse Analytics-körnings tid. Du kan skriva tillbaka data till transaktions arkivet som ett betjänande lager.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Priset

Analytiskt lager följer en förbruknings pris modell där du debiteras för:

* Lagring: mängden data som behålls i analys lagret varje månad, inklusive historiska data som definieras av analys-TTL.

* Analytiska Skriv åtgärder: den fullständigt hanterade synkroniseringen av drift data uppdateringar till analys lagret från transaktions arkivet (automatisk synkronisering)

* Analytiska Läs åtgärder: Läs åtgärder som utförts mot analys lagret från Azure Synapse Analytics Spark-pool och körnings tider för SQL-poolen utan server.

Priset för analytiskt lager skiljer sig från pris modellen för transaktions lager. Det finns inget koncept för etablerade ru: er i analys lagret. Se [Azure Cosmos DB prissättnings sida](https://azure.microsoft.com/pricing/details/cosmos-db/)för fullständig information om pris modellen för analys lagring.

För att få en kostnads uppskattning på hög nivå för att aktivera analys av en Azure Cosmos DB behållare kan du använda [Azure Cosmos DB kapacitets planering](https://cosmos.azure.com/capacitycalculator/) och få en uppskattning av kostnaderna för analytisk lagring och skriv åtgärder. Kostnaderna för analys av Läs åtgärder beror på de analytiska arbets belastnings egenskaperna, men som en uppskattning på hög nivå, resulterar det vanligt vis i 130 000 analytiska Läs åtgärder, och resultatet kostar $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analytisk tid till Live (TTL)

TTL för analys anger hur länge data ska behållas i analysarkivet, för en container. 

Om analytiskt arkiv är aktiverat, kommer infogningar, uppdateringar och borttagningar av användnings data automatiskt att synkroniseras från transaktions lager till analytisk lagring, oavsett konfiguration av transaktions-TTL. Kvarhållning av dessa drift data i analys lagret kan styras av det analytiska TTL-värdet på behållar nivån, enligt vad som anges nedan:

Analytiskt TTL på en behållare anges med hjälp av `AnalyticalStoreTimeToLiveInSeconds` egenskapen:

* Om värdet är inställt på 0, saknas (eller är inställt på null): analys arkivet är inaktiverat och inga data replikeras från transaktions arkivet till ett analys lager

* Om det finns och värdet är inställt på "-1": analys arkivet behåller alla historiska data, oavsett lagring av data i transaktions arkivet. Den här inställningen anger att analys lagret har oändlig kvarhållning av dina användnings data

* Om detta är tillgängligt och värdet är inställt på ett positivt tal "n": objekt upphör att gälla från analys lagret "n" efter deras senaste ändrings tid i transaktions arkivet. Den här inställningen kan utnyttjas om du vill behålla dina användnings data under en begränsad tids period i analys lagret, oavsett lagring av data i transaktions arkivet

Några saker att tänka på:

*   När analys lagret har Aktiver ATS med ett analytiskt TTL-värde kan det uppdateras till ett annat giltigt värde senare. 
*   Även om transaktions-TTL kan anges på behållarens eller objekt nivån, kan analytiskt TTL endast anges på behållar nivån för närvarande.
*   Du kan få längre kvarhållning av dina drift data i analys lagret genom att ange analytiskt TTL->= transaktions-TTL på behållar nivån.
*   Analys lagret kan göras för att spegla transaktions lagret genom att ställa in analys-TTL = transaktions-TTL.

När du aktiverar analytisk lagring på en behållare:

* Från Azure Portal anges alternativet för analytiskt TTL till standardvärdet-1. Du kan ändra det här värdet till n sekunder genom att navigera till behållar inställningar under Datautforskaren. 
 
* I Azure SDK eller PowerShell eller CLI kan du aktivera alternativet för analys-TTL genom att ställa in det på antingen-1 eller n. 

Mer information finns i [så här konfigurerar du analytiskt TTL-värde på en behållare](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande dokument:

* [Azure Synapse Link för Azure Cosmos DB](synapse-link.md)

* [Kom igång med Azure Synapse Link för Azure Cosmos DB](configure-synapse-link.md)

* [Vanliga frågor och svar om Synapse Link för Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Användningsfall för Azure Synapse Link för Azure Cosmos DB](synapse-link-use-cases.md)
