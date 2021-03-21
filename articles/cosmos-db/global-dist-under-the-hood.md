---
title: Global distribution med Azure Cosmos DB – under huven
description: Den här artikeln innehåller teknisk information om global distribution av Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 1b47ad27abbe59eceabd15d091f88f4659d8dad6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486394"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Global data distribution med Azure Cosmos DB – under huven
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB är en grundläggande tjänst i Azure, så den distribueras i alla Azure-regioner över hela världen, inklusive offentlig, suverän, departementet försvar (DoD) och myndigheter. I ett Data Center distribuerar och hanterar vi Azure Cosmos DB på massiv stämpel av datorer, var och en med dedikerad lokal lagring. I ett Data Center har Azure Cosmos DB distribuerats över flera kluster, som var och en kan köra flera generationer av maskin vara. Datorer i ett kluster sprids vanligt vis över 10-20 fel domäner för hög tillgänglighet inom en region. Följande bild visar Cosmos DB global distribution system sto pol Ogin:

:::image type="content" source="./media/global-dist-under-the-hood/distributed-system-topology.png" alt-text="System sto pol Ogin" border="false":::

**Global distribution i Azure Cosmos DB är nyckel färdiga:** När som helst, med några klick eller program mässigt med ett enda API-anrop, kan du lägga till eller ta bort de geografiska regioner som är kopplade till Cosmos-databasen. En Cosmos-databas, i sin tur, består av en uppsättning Cosmos-behållare. I Cosmos DB fungerar behållare som logiska enheter för distribution och skalbarhet. Samlingarna, tabellerna och graferna som du skapar är (internt) bara Cosmos behållare. Behållare är fullständigt schema-oberoende och anger en omfattning för en fråga. Data i en Cosmos-behållare indexeras automatiskt vid inmatning. Med automatisk indexering kan användare fråga data utan besvär i schema-eller index hantering, särskilt i en globalt distribuerad installation.  

- I en specifik region distribueras data i en behållare med hjälp av en partitionsnyckel som du anger och som hanteras transparent av de underliggande fysiska partitionerna (*lokal distribution*).  

- Varje fysisk partition replikeras också över geografiska regioner (*global distribution*). 

När en app som använder Cosmos DB elastiskt skala data flödet i en Cosmos-behållare eller använder mer lagrings utrymme, hanterar Cosmos DB transparent hanterings åtgärder för partitioner (dela, klona och ta bort) över alla regioner. Oberoende av skalan, distributionen eller felen fortsätter Cosmos DB att tillhandahålla en enda system avbildning av data i behållarna, som globalt distribueras över valfritt antal regioner.  

Som du ser i följande bild distribueras data i en behållare längs två dimensioner – inom en region och mellan regioner, globalt:  

:::image type="content" source="./media/global-dist-under-the-hood/distribution-of-resource-partitions.png" alt-text="fysiska partitioner" border="false":::

En fysisk partition implementeras av en grupp repliker, som kallas *replik uppsättning*. Varje dator är värd för hundratals repliker som motsvarar olika fysiska partitioner i en fast uppsättning processer som visas i bilden ovan. Repliker som motsvarar de fysiska partitionerna placeras och bal anse ras dynamiskt på datorerna i ett kluster och data Center inom en region.  

En replik som unikt tillhör en Azure Cosmos DB klient. Varje replik är värd för en instans av Cosmos DB [databas motor](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)som hanterar resurserna samt associerade index. Cosmos-databasmotorn använder sig av en-baserad (Atom-Record-Sequence)-baserad typ system. Motorn är oberoende till begreppet schema, vilket gör att det går att minska gränserna mellan struktur-och instans värden för poster. Cosmos DB uppnår fullständig schema-Agnosticism genom att automatiskt indexera allt vid inmatning på ett effektivt sätt, vilket gör att användarna kan fråga sina globalt distribuerade data utan att behöva hantera schema-eller index hantering.

Cosmos-databasmotorn består av komponenter, inklusive implementering av flera koordinerande primitiver, språk körningar, frågeprocessorn och de lagrings-och indexerings under system som ansvarar för transaktions lagring och indexering av data. För att tillhandahålla hållbarhet och hög tillgänglighet behåller databas motorn sina data och index på SSD och replikerar den mellan databas motor instanserna i replikerna. Större klienter motsvarar högre skalbarhet och lagrings utrymme och har antingen större eller fler repliker eller både och. Varje komponent i systemet är helt asynkron – inga trådar någonsin blockeras, och varje tråd utför kortvarigt arbete utan att det medför onödiga tråd byten. Hastighets begränsning och mottryck rör sig över hela stacken från åtkomst kontrollen till alla I/O-sökvägar. Cosmos-databasmotorn är utformad för att utnyttja detaljerad samtidighet och leverera högt data flöde samtidigt som du arbetar inom Frugal-mängder system resurser.

Cosmos DB global distribution är beroende av två nyckel abstraktioner – *replik uppsättningar* och *partitionsuppsättningar*. En replik uppsättning är ett modulärt LEGO-block för samordning, och en partitionsuppsättning är ett dynamiskt överlägg av en eller flera geografiskt distribuerade fysiska partitioner. För att förstå hur global distribution fungerar måste vi förstå dessa två nyckel abstraktioner. 

## <a name="replica-sets"></a>Replik – uppsättningar

En fysisk partition är materialiserad som en självhanterad och dynamiskt belastningsutjämnad grupp av repliker som sprids över flera fel domäner, som kallas replik uppsättning. Den här uppsättningen implementerar det replikerade tillstånds maskin protokollet för att göra data i den fysiska partitionen hög tillgänglig, varaktig och konsekvent. Replikens medlemskap *N* är dynamisk – det håller på att variera mellan *Nmin.* och *nmax.* baserat på fel, administrativa åtgärder och tiden för misslyckade repliker för att återskapa/återställa. Baserat på medlemskaps ändringar konfigurerar protokollet för replikering också storleken på Läs-och skriv-kvorum. För att enhetligt distribuera det data flöde som har tilldelats till en specifik fysisk partition, använder vi två idéer: 

- Först är kostnaden för att bearbeta Skriv förfrågningarna på ledaren högre än kostnaden för att tillämpa uppdateringarna på följaren. På motsvarande sätt budgeteras ledaren mer system resurser än följarna. 

- För det andra så långt det är möjligt består Read-kvorumet för en specifik konsekvens nivå uteslutande av följande repliker. Vi rekommenderar att du inte kontaktar chefen för att betjäna läsningar om det inte behövs. Vi använder ett antal idéer från forskningen som är gjord på förhållandet mellan [belastning och kapacitet](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) i de kvorumbaserade systemen för de [fem konsekvens modeller](consistency-levels.md) som Cosmos DB stöder.  

## <a name="partition-sets"></a>Partitioner – uppsättningar

En grupp med fysiska partitioner, en från var och en av de som kon figurer ATS med Cosmos-databas regionerna, är sammansatt för att hantera samma uppsättning nycklar som replikeras i alla konfigurerade regioner. Denna högre samordning är en *partitions uppsättning* – ett geografiskt distribuerat dynamiskt överlägg med fysiska partitioner som hanterar en specifik uppsättning nycklar. Även om en angiven fysisk partition (en replik uppsättning) är begränsad i ett kluster, kan en partitionsuppsättning spänna över kluster, data Center och geografiska regioner som visas på bilden nedan:  

:::image type="content" source="./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png" alt-text="Partitionsuppsättningar" border="false":::

Du kan se att en partitionsuppsättning är en geografiskt utspridd "Super Replica-uppsättning", som består av flera replik uppsättningar som äger samma uppsättning nycklar. På samma sätt som en replik uppsättning är en partitions uppsättnings medlemskap också dynamiskt – den varierar baserat på implicita hanterings åtgärder för fysiska partitioner för att lägga till/ta bort nya partitioner i/från en angiven partitionsuppsättning (till exempel när du skalar upp data flödet i en behållare, lägger till/tar bort en region i Cosmos-databasen eller när fel uppstår). Genom att ha var och en av partitionerna (av en partitionsuppsättning) hanterar du medlemskapet för partitionsuppsättningen i sin egen replik uppsättning, är medlemskapet helt decentraliserat och med hög tillgänglighet. Under omkonfigurationen av en partitionsuppsättning upprättas även topologin för överlägget mellan fysiska partitioner. Topologin väljs dynamiskt baserat på konsekvens nivå, geografiskt avstånd och tillgänglig nätverks bandbredd mellan käll-och mål-fysiska partitionerna.  

Med tjänsten kan du konfigurera dina Cosmos-databaser med antingen en enda Skriv region eller flera Skriv regioner, och beroende på vad du väljer, är partitions uppsättningar konfigurerade för att godkänna skrivningar i exakt en eller alla regioner. Systemet använder ett kapslat samförstånds protokoll på två nivåer – en nivå fungerar inom replikerna för en replik uppsättning av en fysisk partition som accepterar skrivningar, och den andra fungerar på samma nivå som en partitions uppsättning för att ge fullständiga ordnings garantier för alla genomförda skrivningar inom partitionsuppsättningen. Denna kapslade, kapslade enighet är avgörande för implementeringen av vår stränga service avtal för hög tillgänglighet, samt implementering av konsekvens modeller som Cosmos DB erbjudanden till sina kunder.  

## <a name="conflict-resolution"></a>Konfliktlösning

Vår design för uppdaterings spridning, konflikt lösning och orsakssamband är inspirerad från föregående arbete på [algoritmer för epidemier](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) och [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) -systemet. Även om kärnan i idéerna har överleva och innehåller en lämplig referens ram för att kommunicera Cosmos DBens system design, har de också genomgått en betydande transformering som vi tillämpade dem på Cosmos DB systemet. Detta krävdes, eftersom de tidigare systemen inte har utformats än med resurs styrningen eller den skala som Cosmos DB behöver för att fungera, eller för att tillhandahålla funktioner (till exempel begränsat föråldrad konsekvens) och de stränga och omfattande service avtal som Cosmos DB levererar till sina kunder.  

Kom ihåg att en partitionsuppsättning är fördelad i flera regioner och följer Cosmos databaser (Multi-region skrivningar) för att replikera data mellan de fysiska partitionerna med en angiven partitions uppsättning. Varje fysisk partition (av en partitionsuppsättning) accepterar skrivningar och hanterar läsningar vanligt vis till de klienter som är lokala i den regionen. Skrivningar som godkänns av en fysisk partition inom en region är varaktigt allokerade och har hög tillgänglighet i den fysiska partitionen innan de bekräftas till klienten. Dessa är preliminära skrivningar och sprids till andra fysiska partitioner i partitionsuppsättningen med hjälp av en kanal för att skapa en-entropi. Klienter kan begära antingen preliminära eller bekräftade skrivningar genom att skicka ett begär ande huvud. Spridningen av en entropi (inklusive frekvensen för spridning) är dynamisk, baserat på topologin för partitionens uppsättning, regionala närhet av de fysiska partitionerna och den konsekvens nivå som kon figurer ATS. I en partitionsuppsättning Cosmos DB efter ett primärt genomförande schema med en dynamiskt vald Arbiter-partition. Arbiter-valet är dynamiskt och är en del av omkonfigurationen av partitionsuppsättningen baserat på överläggets topologi. Genomförda skrivningar (inklusive flera rader/batch-uppdateringar) garanteras vara beställda. 

Vi använder kodade vektor klockor (som innehåller regions-ID och logiska klockor som motsvarar varje nivå av konsensus vid replik-och partitionsuppsättningen) för att identifiera och lösa uppdaterings konflikter. Topologin och algoritmen för peer-val är utformade för att säkerställa fast och minimal lagring och minimal nätverks kapacitet för versions vektorer. Algoritmen garanterar den strikta konvergens egenskapen.  

För Cosmos-databaser som kon figurer ATS med flera Skriv regioner erbjuder systemet ett antal flexibla lösnings principer för automatisk konflikt som utvecklare kan välja bland, inklusive: 

- **Senaste-skrivning-WINS (LWW)**, som standard använder en systemdefinierad timestamp-egenskap (som baseras på Time-Synchronize Clock-protokollet). Med Cosmos DB kan du också ange andra anpassade numeriska egenskaper som ska användas för konflikt lösning.  
- **Programdefinierad (anpassad) konflikt lösnings princip** (uttrycks via sammanslagnings procedurer) som är utformad för programdefinierad semantik för konflikter. De här procedurerna anropas vid identifiering av de Skriv-och skriv åtgärder som uppstår under överinseende av en databas transaktion på Server sidan. Systemet ger exakt en garanti för körning av en sammanfognings procedur som en del av åtagande protokollet. Det finns [flera olika lösnings exempel för konflikter](how-to-manage-conflicts.md) som du kan spela med.  

## <a name="consistency-models"></a>Konsekvens modeller

Oavsett om du konfigurerar Cosmos-databasen med en eller flera Skriv regioner kan du välja bland de fem väldefinierade konsekvens modellerna. Med flera Skriv regioner är följande några viktiga aspekter av konsekvens nivåerna:  

Den begränsade inaktuella inkonsekventa konsekvensen garanterar att alla läsningar ligger inom *n* -prefix eller *T* sekunder från den senaste skrivningen i någon av regionerna. Dessutom garanteras läsning med utjämnad föråldrad konsekvens som enkel och konsekvent prefix. Protokollet mot entropi fungerar på ett sätt som är begränsat och säkerställer att prefixen inte ackumuleras och att det inte går att använda baktrycket på Skriv åtgärder. Konsekvens i sessioner garanterar enkel och enkel läsning, enkel och enkel skrivning, läsning av dina egna skrivningar, Skriv följande Läs och konsekventa prefix-garantier, globalt. För databaserna som kon figurer ATS med stark konsekvens gäller fördelarna (låg Skriv fördröjning, hög Skriv fördröjning) för flera Skriv regioner, på grund av synkron replikering mellan regioner.

Semantiken för de fem konsekvens modellerna i Cosmos DB beskrivs [här](consistency-levels.md)och matematiskt beskrivs med en tla +-specifikationer på hög nivå [här](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Nästa steg

Härnäst lär du dig hur du konfigurerar global distribution med hjälp av följande artiklar:

* [Lägga till/ta bort regioner från ditt databaskonto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Så här skapar du en anpassad lösnings princip för konflikt](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
