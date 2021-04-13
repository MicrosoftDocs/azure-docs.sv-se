---
title: Vad är Azure Cosmos DB-analysarkiv?
description: Lär dig Azure Cosmos DB transaktionsbaserat (radbaserat) och analysarkiv (kolumnbaserat). Fördelar med analysarkiv, prestandapåverkan för storskaliga arbetsbelastningar och automatisk synkronisering av data från transaktionslager till analysarkiv
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: eaabc663ba243423bddf7ef6abfe41182e06b4f9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364618"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Vad är Azure Cosmos DB analysarkiv?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB analysarkiv är ett helt isolerat kolumnlager för att möjliggöra storskalig analys mot driftdata i din Azure Cosmos DB, utan att påverka dina transaktionsarbetsbelastningar. 

Azure Cosmos DB transaktionsarkiv är schemaoberoende och gör att du kan iterera i dina transaktionsprogram utan att behöva hantera schema- eller indexhantering. Till skillnad från detta Azure Cosmos DB schemat för analysarkivet för att optimera prestanda för analytiska frågor. Den här artikeln beskriver i detalj analyslagring.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Utmaningar med storskalig analys av driftdata

Driftdata för flera modeller i en Azure Cosmos DB lagras internt i ett indexerat radbaserat "transaktionslager". Radarkivformatet är utformat för att tillåta snabba transaktionsläsningar och skrivningar i svarstiderna för millisekunder och driftfrågor. Om datamängden växer kan komplexa analysfrågor bli dyra när det gäller etablerat dataflöde för de data som lagras i det här formatet. Hög förbrukning av etablerat dataflöde påverkar i sin tur prestandan för transaktionsarbetsbelastningar som används av dina realtidsprogram och tjänster.

För att analysera stora mängder data extraheras traditionellt driftdata från Azure Cosmos DB transaktionslager och lagras i ett separat datalager. Till exempel lagras data i ett informationslager eller en datasjö i ett lämpligt format. Dessa data används senare för storskalig analys och analyseras med hjälp av beräkningsmotorn, till exempel Apache Spark kluster. Den här uppdelningen av analytiska lagrings- och beräkningslager från driftdata resulterar i ytterligare svarstider, eftersom pipelines för ETL(extrahering, transformering, inläsning) körs mindre ofta för att minimera den potentiella påverkan på dina transaktionsarbetsbelastningar.

ETL-pipelines blir också komplexa när du hanterar uppdateringar av driftdata jämfört med att endast hantera nyligen inadelade driftdata. 

## <a name="column-oriented-analytical-store"></a>Kolumnorienterat analysarkiv

Azure Cosmos DB analysarkivet hanterar de komplexitets- och svarstidsutmaningar som uppstår med traditionella ETL-pipelines. Azure Cosmos DB analysarkiv kan automatiskt synkronisera dina driftdata till ett separat kolumnlager. Kolumnarkivformat är lämpligt för storskaliga analysfrågor som ska utföras på ett optimerat sätt, vilket förbättrar svarstiden för sådana frågor.

Med Azure Synapse Link kan du nu skapa HTAP-lösningar utan ETL genom att länka direkt till Azure Cosmos DB analysarkiv från Azure Synapse Analytics. Det gör att du kan köra storskalig analys i nära realtid på dina driftdata.

## <a name="features-of-analytical-store"></a>Funktioner i analysarkiv 

När du aktiverar analysarkiv på Azure Cosmos DB container skapas ett nytt kolumnlager internt baserat på driftdata i containern. Det här kolumnarkivet bevaras separat från det radorienterade transaktionsarkivet för den containern. Infogningar, uppdateringar och borttagningar av dina driftdata synkroniseras automatiskt till analysarkivet. Du behöver inte ändringsflödet eller ETL för att synkronisera data.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Kolumnlager för analytiska arbetsbelastningar på driftdata

Analytiska arbetsbelastningar omfattar vanligtvis aggregeringar och sekventiella genomsökningar av valda fält. Genom att lagra data i en kolumn större ordning gör analysarkivet att en grupp värden för varje fält kan serialiseras tillsammans. Det här formatet minskar den IOPS som krävs för att skanna eller beräkna statistik över specifika fält. Det förbättrar avsevärt frågesvarstiderna för genomsökningar över stora datamängder. 

Till exempel om dina operativa tabeller har följande format:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Exempel på åtgärdstabell" border="false":::

Radlagret sparar ovanstående data i ett serialiserat format, per rad, på disken. Det här formatet möjliggör snabbare transaktionsläsningar, skrivningar och driftfrågor, till exempel "Returnera information om Produkt1". Men när datauppsättningen växer och om du vill köra komplexa analysfrågor på data kan det vara dyrt. Om du till exempel vill hämta "försäljningstrender för en produkt under kategorin "Utrustning" för olika affärsenheter och månader, måste du köra en komplex fråga. Stora genomsökningar av den här datauppsättningen kan bli dyra när det gäller etablerat dataflöde och kan även påverka prestanda för transaktionsarbetsbelastningar som driver dina realtidsprogram och tjänster.

Analysarkiv, som är ett kolumnlager, passar bättre för sådana frågor eftersom det serialiserar liknande datafält tillsammans och minskar diskens IOPS.

Följande bild visar transaktionsradarkiv jämfört med analyskolumnarkiv i Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transaktionsradarkiv jämfört med analyskolumnarkiv i Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Fristående prestanda för analytiska arbetsbelastningar

Prestandan för dina transaktionsarbetsbelastningar påverkas inte på grund av analytiska frågor, eftersom analysarkivet är separat från transaktionslagret.  Analysarkivet behöver inte allokera separata enheter för begäran (RU:er).

### <a name="auto-sync"></a>Automatisk synkronisering

Automatisk synkronisering syftar på den fullständigt hanterade funktionen i Azure Cosmos DB där infogningar, uppdateringar och borttagningar av driftdata automatiskt synkroniseras från transaktionslager till analysarkiv nästan i realtid. Svarstiden för automatisk synkronisering är vanligtvis inom 2 minuter. I fall med en databas med delat dataflöde med ett stort antal containrar kan svarstiden för automatisk synkronisering av enskilda containrar vara högre och ta upp till 5 minuter. Vi vill veta mer om hur den här svarstiden passar dina scenarier. För att göra det kan du kontakta [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com).

Funktionen för automatisk synkronisering tillsammans med analysarkiv ger följande viktiga fördelar:

### <a name="scalability--elasticity"></a>Skalbarhet & elasticitet

Genom att använda horisontell partitionering Azure Cosmos DB kan du elastiskt skala lagringen och dataflödet utan avbrott. Horisontell partitionering i transaktionslagret ger skalbarhet & elasticitet vid automatisk synkronisering för att säkerställa att data synkroniseras till analysarkivet nästan i realtid. Datasynkronisering sker oavsett transaktionstrafikens dataflöde, oavsett om det är 1 000 åtgärder/sek eller 1 miljon åtgärder/sek, och det påverkar inte det etablerade dataflödet i transaktionslagret. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Hantera schemauppdateringar automatiskt

Azure Cosmos DB är schemaoberoende och gör att du kan iterera i dina transaktionella program utan att behöva hantera schema eller index. Till skillnad från detta Azure Cosmos DB ett analysarkiv schematiserat för att optimera för analytiska frågeprestanda. Med funktionen för automatisk synkronisering Azure Cosmos DB hanterar schemainferensen över de senaste uppdateringarna från transaktionslagret.  Den hanterar även schemarepresentationen i analysarkivet out-of-the-box, vilket innefattar hantering av kapslade datatyper.

Allt eftersom schemat utvecklas och nya egenskaper läggs till med tiden visar analysarkivet automatiskt ett unioniserat schema för alla historiska scheman i transaktionslagret.

#### <a name="schema-constraints"></a>Schemabegränsningar

Följande begränsningar gäller för driftdata i Azure Cosmos DB när du aktiverar analysarkiv för att automatiskt dra slutsatser om och representera schemat korrekt:

* Du kan ha högst 1 000 egenskaper på en kapslingsnivå i schemat och ett maximalt kapslingsdjup på 127.
  * Endast de första 1 000 egenskaperna visas i analysarkivet.
  * Endast de första 127 kapslade nivåerna representeras i analysarkivet.

* Även om JSON-dokument (Cosmos DB samlingar/containrar) är fallkänsliga ur ett unikhetsperspektiv är analysarkivet inte det.

  * **I samma dokument:** Egenskapsnamn på samma nivå ska vara unika när det inte är känsligt för jämförelse. Följande JSON-dokument har till exempel "Namn" och "namn" på samma nivå. Även om det är ett giltigt JSON-dokument uppfyller det inte unikhetsbegränsningen och representeras därför inte helt i analysarkivet. I det här exemplet är "Name" och "name" desamma när de jämförs på ett okänsligt sätt. Endast `"Name": "fred"` representeras i analysarkivet eftersom det är den första förekomsten. Och `"name": "john"` representeras inte alls.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **I olika dokument:** Egenskaper på samma nivå och med samma namn, men i olika fall, representeras i samma kolumn med hjälp av namnformatet för den första förekomsten. Följande JSON-dokument har till exempel `"Name"` `"name"` och på samma nivå. Eftersom det första dokumentformatet `"Name"` är används detta för att representera egenskapsnamnet i analysarkivet. Kolumnnamnet i analysarkivet blir med andra ord `"Name"` . Både `"fred"` `"john"` och visas i `"Name"` kolumnen .


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Det första dokumentet i samlingen definierar det första analysarkivschemat.
  * Egenskaper på den första nivån i dokumentet representeras som kolumner.
  * Dokument med fler egenskaper än det ursprungliga schemat genererar nya kolumner i analysarkivet.
  * Kolumner kan inte tas bort.
  * Borttagningen av alla dokument i en samling återställer inte analysarkivschemat.
  * Det finns ingen schemaversionshantering. Den senaste versionen som härförs från transaktionsarkivet är det som visas i analysarkivet.

* För närvarande stöder vi inte Azure Synapse Spark-läsning av kolumnnamn som innehåller tomma värden (blanksteg).

* Förvänta dig olika beteende vad gäller explicita `null` värden:
  * Spark-pooler i Azure Synapse läser dessa värden som `0` (noll).
  * SQL-serverlösa pooler i Azure Synapse dessa värden som om det första dokumentet i samlingen har ett värde med `NULL` en datatyp för `non-numeric` samma egenskap.
  * SQL-serverlösa pooler i Azure Synapse dessa värden som (noll) om det första dokumentet i samlingen har ett värde med en datatyp för `0` `numeric` samma egenskap.

* Förvänta dig olika beteende när det gäller saknade kolumner:
  * Spark-pooler Azure Synapse representerar dessa kolumner som `undefined` .
  * SQL-serverlösa pooler i Azure Synapse dessa kolumner som `NULL` .

#### <a name="schema-representation"></a>Schemarepresentation

Det finns två lägen för schemarepresentation i analysarkivet. De här lägena utgör en kompromiss mellan enkelheten i kolumnrepresentation, hanteringen av polymorfiska scheman och enkelheten i frågeupplevelsen:

* Väldefinierad schemarepresentation
* Schemarepresentation med fullständig återgivning

> [!NOTE]
> När analysarkiv är aktiverat för SQL-API-konton (Core) är standardschemarepresentationen i analysarkivet väldefinierad. För Azure Cosmos DB API för MongoDB-konton är standardschemarepresentationen i analysarkivet en fullständig schemarepresentation. Om du har scenarier som kräver en annan schemarepresentation än standarderbjudandet för var och en av dessa API:er kan du [kontakta Azure Cosmos DB att](mailto:cosmosdbsynapselink@microsoft.com) aktivera det.

**Väldefinierad schemarepresentation**

Den väldefinierade schemarepresentationen skapar en enkel tabellrepresentation av schemaoberoende data i transaktionslagret. Den väldefinierade schemarepresentationen har följande överväganden:

* En egenskap har alltid samma typ för flera objekt.
* Vi tillåter endast 1 typändring, från null till andra datatyper. Den första förekomsten som inte är null definierar kolumndatatypen.

  * Har till `{"a":123} {"a": "str"}` exempel inget väldefinierat schema eftersom ibland `"a"` är en sträng och ibland ett tal. I det här fallet registrerar analysarkivet datatypen för som datatyp för i det första objektet i `"a"` `“a”` containern. Dokumentet kommer fortfarande att ingå i analysarkivet, men objekt där datatypen skiljer sig åt kommer `"a"` inte att ingå.
  
    Det här villkoret gäller inte för null-egenskaper. Till exempel är `{"a":123} {"a":null}` fortfarande väldefinierad.

* Matristyper måste innehålla en enda upprepad typ.

  * Är till exempel `{"a": ["str",12]}` inte ett väldefinierat schema eftersom matrisen innehåller en blandning av heltals- och strängtyper.

> [!NOTE]
> Om Azure Cosmos DB analysarkiv följer den väldefinierade schemarepresentationen och specifikationen ovan överträds av vissa objekt, inkluderas inte dessa objekt i analysarkivet.

* Förvänta dig olika beteende vad gäller olika typer i väldefinierade scheman:
  * Spark-pooler i Azure Synapse representerar dessa värden som `undefined` .
  * SQL-serverlösa pooler i Azure Synapse dessa värden som `NULL` .


**Fullständig återgivning av återgivning av schema**

Schemarepresentationen med fullständig återgivning är utformad för att hantera hela bredden av polymorfiska scheman i schemaoberoende driftdata. I den här schemarepresentationen tas inga objekt bort från analysarkivet även om väldefinierade schemabegränsningar (det vill säga inga blandade datatypfält eller matriser med blandade datatyper) överträds.

Detta uppnås genom att översätta lövegenskaperna för driftdata till analysarkivet med distinkta kolumner baserat på datatypens värden i egenskapen . Lövegenskapsnamnen utökas med datatyper som suffix i analysarkivschemat så att de kan vara frågor utan tvetydighet.

Låt oss till exempel ta följande exempeldokument i transaktionsarkivet:

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

`streetNo`Lövegenskapen i det `address` kapslade objektet representeras i analysarkivschemat som en kolumn `address.object.streetNo.int32` . Datatypen läggs till som ett suffix i kolumnen. På så sätt, om ett annat dokument läggs till i transaktionslagret där värdet för lövegenskapen är "123" (observera att det är en sträng), utvecklas schemat för analysarkivet automatiskt utan att ändra typen av en tidigare skriven `streetNo` kolumn. En ny kolumn läggs till i analysarkivet som `address.object.streetNo.string` där värdet "123" lagras.

**Datatyp till suffixkarta**

Här är en karta över alla egenskapsdatatyper och deras suffixrepresentationer i analysarkivet:

|Ursprunglig datatyp  |Suffix  |Exempel  |
|---------|---------|---------|
| Double |  ".float64" |    24.99|
| Matris | ".array" |    ["a", "b"]|
|Binär | ".binary" |0|
|Boolesk    | ".bool"   |Sant|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Null   | ".null"   | null|
|Sträng|    ".string" | "ABC"|
|Timestamp |    ".timestamp" |  Tidsstämpel(0, 0)|
|DateTime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|Dokument   |".object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Kostnadseffektiv arkivering av historiska data

Datanivåindelning syftar på uppdelning av data mellan lagringsinfrastrukturer som är optimerade för olika scenarier. Därmed förbättras den övergripande prestandan och kostnadseffektiviteten för datastacken från slutet till slut. Med analysarkiv har Azure Cosmos DB nu stöd för automatisk nivåindelad data från transaktionslagret till analysarkiv med olika datalayouter. Med analysarkiv som är optimerat vad gäller lagringskostnader jämfört med transaktionslagret kan du behålla mycket längre horisonter för driftdata för historisk analys.

När analysarkivet har aktiverats kan du, baserat på datalagringsbehoven för transaktionsarbetsbelastningarna, konfigurera egenskapen "Transactional Store Time to Live (Transactional TTL)" så att poster tas bort automatiskt från transaktionslagret efter en viss tidsperiod. På samma sätt gör "Time To Live för analysarkiv (TTL för analys) att du kan hantera livscykeln för data som lagras i analysarkivet oberoende av transaktionslagret. Genom att aktivera analysarkiv och konfigurera TTL-egenskaper kan du sömlöst nivåindelad och definiera datalagringsperioden för de två arkiven.

### <a name="global-distribution"></a>Global distribution

Om du har ett globalt distribuerat Azure Cosmos DB-konto blir det tillgängligt i alla regioner i det kontot när du aktiverar analysarkiv för en container.  Ändringar av driftdata replikeras globalt i alla regioner. Du kan köra analysfrågor effektivt mot närmaste regionala kopia av dina data i Azure Cosmos DB.

### <a name="security"></a>Säkerhet

Autentisering med analysarkivet är samma som transaktionslagret för en viss databas. Du kan använda primära eller skrivskyddade nycklar för autentisering. Du kan använda länkad tjänst i Synapse Studio för att förhindra att Azure Cosmos DB i Spark-notebook-datorer. Åtkomst till den här länkade tjänsten är tillgänglig för alla som har åtkomst till arbetsytan.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Stöd för Azure Synapse Analytics körningar

Analysarkivet är optimerat för skalbarhet, elasticitet och prestanda för analytiska arbetsbelastningar utan beroende av beräkningskörningarna. Lagringstekniken är själv hanterad för att optimera dina analysarbetsbelastningar utan manuella åtgärder.

Genom att frikoppla analyslagringssystemet från det analytiska beräkningssystemet kan data i Azure Cosmos DB-analysarkivet frågas samtidigt från de olika analyskörningar som stöds av Azure Synapse Analytics. Från och med idag har Azure Synapse Analytics stöd Apache Spark serverlös SQL-pool med Azure Cosmos DB analysarkiv.

> [!NOTE]
> Du kan bara läsa från analysarkiv med Azure Synapse Analytics körtid. Du kan skriva tillbaka data till ditt transaktionslager som ett betjänande lager.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Prissättning

Analysarkivet följer en förbrukningsbaserad prismodell där du debiteras för:

* Lagring: Mängden data som lagras i analysarkivet varje månad, inklusive historiska data som definieras av TTL för analys.

* Analytiska skrivåtgärder: den fullständigt hanterade synkroniseringen av uppdateringar av driftdata till analysarkivet från transaktionslagret (automatisk synkronisering)

* Åtgärder för analysläsning: läsåtgärder som utförs mot analysarkivet från Azure Synapse Analytics Spark-pool och serverlösa SQL-poolkörningstider.

Prissättningen för analysarkiv är separat från transaktionsbutiksprismodellen. Det finns inget begrepp för etablerade RU:er i analysarkivet. Se [Azure Cosmos DB för fullständig](https://azure.microsoft.com/pricing/details/cosmos-db/)information om prismodellen för analysarkiv.

För att få en kostnadsuppskattning på hög nivå för att aktivera analysarkiv på en Azure Cosmos DB-container kan du använda [Azure Cosmos DB Capacity Planner](https://cosmos.azure.com/capacitycalculator/) och få en uppskattning av dina kostnader för analyslagring och skrivåtgärder. Kostnaderna för analytiska läsåtgärder beror på analysarbetsbelastningens egenskaper, men som en uppskattning på hög nivå resulterar genomsökning på 1 TB data i analysarkiv vanligtvis i 130 000 analytiska läsåtgärder och resulterar i en kostnad på 0,065 USD.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> TTL (Time to Live) för analys

TTL för analys anger hur länge data ska behållas i analysarkivet, för en container. 

Om analysarkiv är aktiverat synkroniseras infogningar, uppdateringar och borttagningar av driftdata automatiskt från transaktionslager till analysarkiv, oavsett TTL-konfiguration för transaktioner. Kvarhållningen av dessa driftdata i analysarkivet kan styras av TTL-värdet för analys på containernivå enligt beskrivningen nedan:

TTL-analys för en container anges med hjälp av `AnalyticalStoreTimeToLiveInSeconds` egenskapen :

* Om värdet är inställt på "0", saknas (eller anges till null): är analysarkivet inaktiverat och inga data replikeras från transaktionsarkivet till analysarkivet

* Om det finns och värdet är inställt på "-1": analysarkivet behåller alla historiska data, oavsett kvarhållning av data i transaktionslagret. Den här inställningen anger att analysarkivet har obegränsad kvarhållning av dina driftdata

* Om det finns och värdet är inställt på något positivt tal "n": objekten upphör att gälla från analysarkivet "n" sekunder efter den senaste ändringstiden i transaktionslagret. Den här inställningen kan användas om du vill behålla dina driftdata under en begränsad tidsperiod i analysarkivet, oavsett kvarhållning av data i transaktionslagret

Några saker att tänka på:

*   När analysarkivet har aktiverats med ett TTL-värde för analys kan det uppdateras till ett annat giltigt värde senare. 
*   Transaktions-TTL kan anges på container- eller objektnivå, men TTL för analys kan bara anges på containernivå för närvarande.
*   Du kan uppnå längre kvarhållning av dina driftdata i analysarkivet genom att ange TTL-värdet >= transaktions-TTL på containernivå.
*   Analysarkivet kan göras för att spegla transaktionslagret genom att ange TTL för analys = transaktions-TTL.

När du aktiverar analysarkiv på en container:

* Från Azure Portal anges TTL-alternativet för analys till standardvärdet -1. Du kan ändra det här värdet till "n" sekunder genom att gå till containerinställningarna under Datautforskaren. 
 
* Från Azure SDK, PowerShell eller CLI kan du aktiveras TTL-alternativet för analys genom att ange det till antingen -1 eller "n". 

Mer information finns i konfigurera [TTL för analys på en container.](configure-synapse-link.md#create-analytical-ttl)

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande dokument:

* [Azure Synapse Link för Azure Cosmos DB](synapse-link.md)

* [Kom igång med Azure Synapse Link för Azure Cosmos DB](configure-synapse-link.md)

* [Vanliga frågor och svar om Synapse Link för Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Användningsfall för Azure Synapse Link för Azure Cosmos DB](synapse-link-use-cases.md)
