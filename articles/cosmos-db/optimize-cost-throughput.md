---
title: Optimera data flödes kostnaden i Azure Cosmos DB
description: Den här artikeln förklarar hur du optimerar data flödes kostnader för data som lagras i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c95fc9e416399b5c8fe032e0d3af0c3b7f9cf6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433581"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Genom att erbjuda en etablerad data flödes modell erbjuder Azure Cosmos DB förutsägbar prestanda i vilken skala som helst. Reservering eller etablering av genom strömning i förväg eliminerar den "störningarnas grann effekt" i prestandan. Du anger den exakta mängden data flöde som du behöver och Azure Cosmos DB garanterar det konfigurerade genomflödet, som backas upp av SLA.

Du kan börja med ett minsta data flöde på 400 RU/s och skala upp till flera miljoner begär Anden per sekund eller till och med. Varje begäran som du skickar till din Azure Cosmos-behållare eller-databas, till exempel en Read-begäran, skrivbegäran, fråge förfrågning, lagrade procedurer har en motsvarande kostnad som dras av från ditt etablerade data flöde. Om du etablerar 400 RU/s och utfärdar en fråga som kostar 40 ru: er kommer du att kunna skicka 10 frågor per sekund. Alla begär Anden utöver detta får en hastighets begränsning och du bör försöka utföra begäran igen. Om du använder klient driv rutiner stöder de automatiskt logiken för omförsök.

Du kan etablera dataflöden för databaser och containrar, och de olika strategierna kan ge besparingar beroende på scenariot.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimera genom att tillhandahålla data flöde på olika nivåer

* Om du etablerar data flöde för en databas kan alla behållare, till exempel samlingar/tabeller/grafer i den databasen, dela data flödet baserat på belastningen. Data flödet som reserver ATS på databas nivå delas ojämnt, beroende på arbets belastningen på en speciell uppsättning behållare.

* Om du etablerar data flöde på en behållare, garanteras data flödet för den behållaren, som backas upp av service avtalet. Valet av logisk partitionsnyckel är avgörande för jämn fördelning av belastningen över alla logiska partitioner i en behållare. Mer information finns i artiklar om [partitionering](partitioning-overview.md) och [horisontell skalning](partitioning-overview.md) .

Här följer några rikt linjer som du kan välja för en etablerad data flödes strategi:

**Överväg att tillhandahålla data flöde för en Azure Cosmos-databas (som innehåller en uppsättning behållare) om**:

1. Du har några dussin Azure Cosmos-behållare och vill dela data flödet i vissa eller alla. 

2. Du migrerar från en databas med en enda klient som är utformad för att köras på virtuella datorer med IaaS eller lokalt, till exempel NoSQL eller Relations databaser för att Azure Cosmos DB. Och om du har många samlingar/tabeller/grafer och du inte vill göra några ändringar i din data modell. Obs! Du kan behöva kompromissa med några av fördelarna som Azure Cosmos DB om du inte uppdaterar din data modell när du migrerar från en lokal databas. Vi rekommenderar att du alltid får till gång till din data modell för att få ut mesta möjliga av prestanda och också för att optimera kostnaderna. 

3. Du vill absorbera oplanerade toppar i arbets belastningar genom att använda poolbaserade data flöde på databas nivå som omfattas av oväntad insamling i arbets belastningen. 

4. I stället för att ställa in specifikt data flöde på enskilda behållare, är det viktigt att du hämtar det sammanställda data flödet i en uppsättning behållare i databasen.

**Överväg att konfigurera data flödet på en enskild behållare om:**

1. Du har några Azure Cosmos-behållare. Eftersom Azure Cosmos DB är schema-oberoende kan en behållare innehålla objekt som har heterogena scheman och inte kräver att kunder skapar flera behållar typer, en för varje entitet. Det är alltid ett alternativ att överväga om gruppering av separata 10-20-behållare i en enda behållare är meningsfull. Med en ru: er på minst 400 för behållare kan pooler med alla 10-20-behållare till en vara mer kostnads effektivt. 

2. Du vill styra data flödet för en specifik behållare och få det garanterade data flödet på en specifik behållare som backas upp av SLA.

**Överväg en hybrid av ovanstående två strategier:**

1. Som tidigare nämnts kan du med Azure Cosmos DB blanda och matcha de två strategierna ovan, så du kan nu ha vissa behållare i Azure Cosmos Database, som kan dela det data flöde som har skapats i databasen samt vissa behållare i samma databas, som kan ha dedikerade mängder etablerade data flöden. 

2. Du kan använda ovanstående strategier för att komma igång med en hybrid konfiguration där du har ett data flöde med en databas nivå som har allokerats med vissa behållare som har dedikerat data flöde.

Som du ser i följande tabell, beroende på valet av API, kan du etablera data flöde med olika granularitet.

|API|För **delat** data flöde konfigurerar du |För **dedikerat** data flöde konfigurerar du |
|----|----|----|
|API för SQL|Databas|Container|
|API för Azure Cosmos DB för MongoDB|Databas|Samling|
|Cassandra-API|Keyspace|Tabell|
|Gremlin-API|Databaskonto|Graph|
|Tabell-API|Databaskonto|Tabell|

Genom att tillhandahålla data flöde på olika nivåer kan du optimera dina kostnader baserat på arbets Belastningens egenskaper. Som tidigare nämnts kan du program mässigt och när som helst öka eller minska ditt etablerade data flöde för antingen enskilda behållare eller kollektivt i en uppsättning behållare. Genom att elastiskt skala data flöde när din arbets belastning ändras betalar du bara för det data flöde som du har konfigurerat. Om din behållare eller en uppsättning behållare distribueras över flera regioner, garanteras det data flöde som du konfigurerar på behållaren eller en uppsättning behållare som görs tillgängliga i alla regioner.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimera genom att hastighetsbegränsa dina förfrågningar

För arbets belastningar som inte är känsliga för svars tider kan du etablera mindre genom strömning och låta program hantera begränsningen när det faktiska data flödet överskrider det etablerade data flödet. Servern kommer att förebyggande syfte avsluta begäran med `RequestRateTooLarge` (HTTP-statuskod 429) och returnera `x-ms-retry-after-ms` rubriken som visar hur lång tid i millisekunder som användaren måste vänta innan begäran försöker igen. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Omprövnings logik i SDK: er 

De ursprungliga SDK: erna (.NET/.NET Core, Java, Node.js och python) fångar implicit detta svar, och den server-specificerade återförsöket-efter-rubriken och försök sedan utföra begäran igen. Om ditt konto inte kan nås samtidigt av flera klienter kommer nästa försök att lyckas.

Om du har mer än en klient ackumulerad på ett konsekvent sätt över begär ande frekvensen, kanske standard antalet nya försök, som för närvarande är 9, inte räcker. I sådana fall genererar klienten en `RequestRateTooLargeException` med status kod 429 till programmet. Standard antalet återförsök kan ändras genom att ställa in `RetryOptions` på ConnectionPolicy-instansen. Som standard `RequestRateTooLargeException` returneras med status kod 429 efter en ackumulerad vänte tid på 30 sekunder om begäran fortsätter att köras över begär ande frekvensen. Detta inträffar även om det aktuella antalet återförsök är mindre än max antalet försök, måste det vara standardvärdet 9 eller ett användardefinierat värde. 

[MaxRetryAttemptsOnThrottledRequests](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests) är inställt på 3, så i det här fallet, om en begär ande åtgärd är begränsad genom att överskrida det reserverade data flödet för behållaren, försöker åtgärden tre gånger innan undantaget utlöses till programmet. [MaxRetryWaitTimeInSeconds](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) är inställt på 60, så i det här fallet om den ackumulerade återförsöks tiden i sekunder sedan den första begäran överskrider 60 sekunder, genereras undantaget.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Partitioneringsstrategi och kostnader för etablerat dataflöde

En bra partitionerings strategi är viktig för att optimera kostnader i Azure Cosmos DB. Se till att det inte finns någon sned partition, som exponeras genom lagrings mått. Se till att det inte finns någon sned data flöde för en partition, som exponeras med data flödes mått. Se till att det inte finns någon snedhet mot särskilda partitionsnyckel. Dominerande nycklar i lagringen exponeras med hjälp av mått, men nyckeln kommer att vara beroende av ditt program åtkomst mönster. Det är bäst att tänka på rätt logisk partitionsnyckel. En lämplig partitionsnyckel förväntas ha följande egenskaper:

* Välj en partitionsnyckel som sprider arbets belastningen jämnt över alla partitioner och jämnt över tid. Med andra ord får du inte ha några nycklar till med merparten av data och vissa nycklar med mindre eller inga data. 

* Välj en partitionsnyckel som gör att åtkomst mönster kan spridas jämnt över logiska partitioner. Arbets belastningen är rimlig med alla nycklar. Merparten av arbets belastningen bör med andra ord vara fokuserad på några få olika nycklar. 

* Välj en partitionsnyckel som har en mängd olika värden. 

Den grundläggande idén är att sprida data och aktiviteten i din behållare över en uppsättning logiska partitioner, så att resurser för data lagring och data flöde kan distribueras mellan de logiska partitionerna. Kandidater för partitionsalternativ kan innehålla de egenskaper som visas ofta som ett filter i dina frågor. Frågor kan dirigeras effektivt genom att du tar med partitionsnyckeln i filterpredikatet. Med en sådan partitionerings strategi är det mycket enklare att optimera det etablerade data flödet. 

### <a name="design-smaller-items-for-higher-throughput"></a>Utforma mindre objekt för högre data flöde 

Begär ande avgiften eller begärans bearbetnings kostnad för en specifik åtgärd är direkt korrelerad med objektets storlek. Åtgärder på stora objekt kommer att kosta mer än åtgärder på mindre objekt. 

## <a name="data-access-patterns"></a>Data åtkomst mönster 

Det är alltid en bra idé att logiskt separera dina data till logiska kategorier baserat på hur ofta du kommer åt dina data. Genom att kategorisera den som frekvent, medelhög eller kall data kan du finjustera förbrukad lagring och det data flöde som krävs. Beroende på åtkomst frekvensen kan du placera data i separata behållare (till exempel tabeller, grafer och samlingar) och finjustera det etablerade data flödet på dem för att tillgodose behoven hos det data segmentet. 

Om du använder Azure Cosmos DB och du vet att du inte kommer att söka efter vissa data värden eller sällan kommer åt dem, bör du lagra de komprimerade värdena för dessa attribut. Med den här metoden sparar du på lagrings utrymme, index utrymme och etablerade data flöden och ger lägre kostnader.

## <a name="optimize-by-changing-indexing-policy"></a>Optimera genom att ändra indexerings princip 

Som standard indexerar Azure Cosmos DB automatiskt varje egenskap för varje post. Detta är avsett för att under lätta utvecklingen och säkerställa utmärkta prestanda för många olika typer av ad hoc-frågor. Om du har stora poster med tusentals egenskaper kanske du betalar data flödes kostnaden för indexering av varje egenskap som inte är användbar, särskilt om du bara frågar mot 10 eller 20 av dessa egenskaper. I takt med att du får en referens till din speciella arbets belastning är vår vägledning att justera din index princip. Fullständig information om Azure Cosmos DB indexerings princip finns [här](index-policy.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Övervaka etablerade och förbrukade data flöden 

Du kan övervaka det totala antalet ru: er som tillhandahålls, antalet avgiftsbelagda begär Anden samt antalet ru: er som du har förbrukat i Azure Portal. Följande bild visar ett exempel på användnings mått:

:::image type="content" source="./media/optimize-cost-throughput/monitoring.png" alt-text="Övervaka enheter för programbegäran i Azure Portal":::

Du kan också ställa in aviseringar för att kontrol lera om antalet avgiftsbelagda begär Anden överskrider ett visst tröskelvärde. Mer information finns i [övervaka Azure Cosmos DB](use-metrics.md) -artikeln. De här aviseringarna kan skicka ett e-postmeddelande till konto administratörer eller anropa en anpassad HTTP-webhook eller en Azure-funktion för att automatiskt öka det etablerade data flödet. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Skala ditt genomflöde elastiskt och på begäran 

Eftersom du debiteras för det data flöde som har allokerats kan du med hjälp av det etablerade data flödet för dina behov hjälpa dig att undvika avgifter för det oanvända genomflödet. Du kan skala ditt etablerade data flöde upp eller ned när som helst, efter behov. Om dina data flödes behov är förutsägbara kan du använda Azure Functions och använda en timer-utlösare för att [öka eller minska data flödet enligt ett schema](scale-on-schedule.md). 

* Övervakning av förbrukningen av dina ru: er och förhållandet mellan avgiftsbelagda begär Anden kan avslöja att du inte behöver fortsätta att vara etablerad under hela dagen eller veckan. Du kan få mindre trafik på natten eller under helgen. Genom att använda antingen Azure Portal eller Azure Cosmos DB inbyggda SDK: er eller REST API kan du skala ditt etablerade data flöde när som helst. Azure Cosmos DB REST API tillhandahåller slut punkter för att program mässigt uppdatera prestanda nivån för dina behållare, vilket gör det enkelt att justera data flödet från din kod beroende på tidpunkten för dagen eller vecko dagen. Åtgärden utförs utan drift avbrott och börjar normalt gälla i mindre än en minut. 

* Ett av de områden som du bör skala data flödet i är när du matar in data i Azure Cosmos DB, till exempel under datamigrering. När du har slutfört migreringen kan du skala etablerad data flöde nedåt för att hantera lösningens stabila tillstånd.  

* Kom ihåg att faktureringen är i en timmes kornig het, så du kommer inte att spara pengar om du ändrar ditt etablerade data flöde oftare än en timme i taget.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Bestäm vilket data flöde som behövs för en ny arbets belastning 

Du kan använda följande steg för att fastställa det etablerade data flödet för en ny arbets belastning: 

1. Genomför en första, grov utvärdering med kapacitets planeraren och justera dina uppskattningar med hjälp av Azure Cosmos DB Explorer i Azure Portal. 

2. Vi rekommenderar att du skapar behållarna med högre data flöde än förväntat och sedan skalar ned efter behov. 

3. Vi rekommenderar att du använder en av de interna Azure Cosmos DB SDK: er för att dra nytta av automatiska återförsök när begär Anden får en begränsad hastighet. Om du arbetar på en plattform som inte stöds och använder Cosmos DB REST API, implementerar du din egen princip för återförsök med hjälp av `x-ms-retry-after-ms` rubriken. 

4. Kontrol lera att program koden har stöd för fallet när alla återförsök inte fungerar. 

5. Du kan konfigurera aviseringar från Azure Portal för att få meddelanden om hastighets begränsning. Du kan börja med restriktiva gränser som 10 Rate-begränsade begär Anden under de senaste 15 minuterna och växla till fler Eager-regler när du har fastställt den faktiska förbrukningen. Tillfälliga hastighets begränsningar är bra, de visar att du spelar med de gränser som du har angett och precis vad du vill göra. 

6. Använd övervakning för att förstå ditt trafik mönster så att du kan överväga att dynamiskt justera din data flödes etablering under dagen eller veckan. 

7. Övervaka ditt etablerade kontra förbrukade data flödes kvot regelbundet för att se till att du inte har allokerat mer än nödvändigt antal behållare och databaser. Att ha ett litet överallokerat data flöde är en lämplig säkerhets kontroll.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Metod tips för att optimera det etablerade data flödet 

Följande steg hjälper dig att göra dina lösningar mycket skalbara och kostnads effektivt när du använder Azure Cosmos DB.  

1. Om du har betydligt över ett insamlat data flöde i behållare och databaser bör du granska ru: er-etablerade vs-förbrukade ru: er och finjustera arbets belastningarna.  

2. En metod för att uppskatta mängden reserverat data flöde som krävs av ditt program är att registrera begär ande enhet RU-avgift som är kopplad till att köra vanliga åtgärder mot en representativ Azure Cosmos-behållare eller-databas som används av ditt program och sedan beräkna antalet åtgärder som du förväntar dig att utföra varje sekund. Se till att du mäter och inkluderar även vanliga frågor och deras användning. Information om hur du uppskattar RU-kostnader för frågor via programmering eller med hjälp av portalen finns i [optimera kostnaden för frågor](./optimize-cost-reads-writes.md). 

3. Ett annat sätt att få fram åtgärder och deras kostnader i ru: er är genom att aktivera Azure Monitor loggar, vilket ger dig en uppdelning av drift/varaktighet och begär ande avgiften. Azure Cosmos DB tillhandahåller en begär ande avgift för varje åtgärd, så varje åtgärds avgift kan lagras tillbaka från svaret och sedan användas för analys. 

4. Du kan skala upp och ned det etablerade data flödet elastiskt när du behöver för att tillgodose dina arbets belastnings behov. 

5. Du kan lägga till och ta bort regioner som är kopplade till ditt Azure Cosmos-konto när du behöver och kontrol lera kostnader. 

6. Kontrol lera att du har till och med distribution av data och arbets belastningar över logiska partitioner i dina behållare. Om du har en jämn partitions distribution kan detta medföra högre mängd data flöde än värdet som behövs. Om du upptäcker att du har en skevad fördelning rekommenderar vi att du distribuerar arbets belastningen jämnt över partitionerna eller partitionerar om data. 

7. Om du har många behållare och dessa behållare inte kräver service avtal kan du använda det databasbaserade erbjudandet för de fall där data flödet per container service avtal inte gäller. Du bör identifiera vilken av de Azure Cosmos-behållare som du vill migrera till data flödes erbjudandet på databas nivå och sedan migrera dem med hjälp av en ändra feed-baserad lösning. 

8. Överväg att använda "Cosmos DB kostnads fri nivå" (kostnads fritt i ett år), prova Cosmos DB (upp till tre regioner) eller nedladdnings bar Cosmos DB emulator för utvecklings-och test scenarier. Genom att använda de här alternativen för test-dev kan du avsevärt sänka kostnaderna.  

9. Du kan ytterligare utföra kostnads optimeringar för arbets belastningen, till exempel öka batch-storlek, belastnings Utjämnings läsningar i flera regioner och avduplicerade data, om tillämpligt.

10. Med Azure Cosmos DB reserverad kapacitet kan du få avsevärda rabatter i upp till 65% i tre år. Azure Cosmos DB reserverad kapacitets modell är ett överliggande åtagande för enheter med begär Anden som behövs över tid. Rabatterna är på nivå av, så att fler enheter för enheter som du använder under en längre period blir mer rabatt. Rabatterna tillämpas omedelbart. Alla ru: er som används ovanför dina etablerade värden debiteras baserat på den icke-reserverade kapacitets kostnaden. Mer information finns i [Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)). Överväg att köpa reserverad kapacitet för att ytterligare minska dina allokerade data flödes kostnader.  

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig mer om kostnads optimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](./optimize-cost-reads-writes.md)
* Läs mer om hur [du optimerar kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)
