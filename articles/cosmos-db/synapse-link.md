---
title: Azure Synapse-länk för Azure Cosmos DB, förmåner och när du ska använda den
description: Lär dig mer om Azure Synapse-länken för Azure Cosmos DB. Med Synapse-länken kan du köra nära real tids analys (HTAP) med Azure Synapse Analytics över drift data i Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: c22a4030bb44c1499995071849ca8942ae2110c3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864354"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Vad är Azure Synapse Link för Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse-länken för Azure Cosmos DB är en molnbaserad hybrid transaktions-och analys bearbetnings funktion (HTAP) som gör att du kan köra nära real tids analys över drifts data i Azure Cosmos DB. Azure Synapse-länken skapar en tätt sömlös integrering mellan Azure Cosmos DB och Azure Synapse Analytics.

Med hjälp av [Azure Cosmos DB Analytical Store](analytical-store-introduction.md), ett fullständigt isolerat kolumn lager, ger Azure Synapse-länken ingen analys av Extract-Transformation-load (ETL) i [Azure Synapse-analys](../synapse-analytics/overview-what-is.md) mot dina operativa data i stor skala. Affärsanalytiker, data tekniker och data experter kan nu använda Synapse Spark eller Synapse SQL för att köra nära real tids Business Intelligence, analyser och maskin inlärnings pipeliner. Du kan åstadkomma detta utan att påverka prestanda för dina transaktionsarbetsbelastningar på Azure Cosmos DB. 

Följande bild visar Azure Synapse Link integration med Azure Cosmos DB och Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Arkitektur diagram för integrering med Azure Synapse Analytics med Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Funktioner

För att analysera stora drift data uppsättningar och minimera påverkan på prestandan hos verksamhets kritiska transaktions arbets belastningar, kommer de tidigare i Azure Cosmos DB att extraheras och bearbetas av ETL-pipelinen (Extract-Transform-Load). ETL-pipeline kräver många lager av data förflyttning, vilket leder till mycket drifts komplexitet och prestanda påverkan på dina transaktions arbets belastningar. Det ökar också svars tiden för att analysera drift data från tidpunkten för ursprunget.

Jämfört med traditionella ETL-baserade lösningar erbjuder Azure Synapse-länken för Azure Cosmos DB flera fördelar som:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Minskad komplexitet utan ETL-jobb att hantera

Med Azure Synapse-länken kan du direkt komma åt Azure Cosmos DB analys lager med hjälp av Azure Synapse Analytics utan komplex data förflyttning. Alla uppdateringar som görs i användnings data visas i analys lagret i nära real tid utan ETL eller ändra feed-jobb. Du kan köra storskalig analys mot analys lager från Azure Synapse Analytics utan ytterligare datatransformering.

### <a name="near-real-time-insights-into-your-operational-data"></a>Insikter i nära real tid i dina användnings data

Nu kan du få omfattande insikter om dina drift data i nära real tid med hjälp av Azure Synapse-länken. ETL-baserade system tenderar att ha högre latens för att analysera dina drift data, på grund av många lager som behövs för att extrahera, transformera och läsa in drift data. Med inbyggd integrering av Azure Cosmos DB analys lager med Azure Synapse Analytics kan du analysera drift data i nära real tid och aktivera nya affärs scenarier. 

### <a name="no-impact-on-operational-workloads"></a>Ingen påverkan på drift arbets belastningar

Med Azure Synapse-länken kan du köra analytiska frågor mot ett Azure Cosmos DB analys lager (ett separat kolumn lager) medan transaktionerna bearbetas med hjälp av ett allokerat data flöde för transaktions arbets belastningen (ett rad-baserat transaktions lager).  Analytiska arbets belastningar behandlas oberoende av transaktions arbets belastningen utan att något av de data flöde som har allokerats för dina drift data förbrukas.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Optimerad för storskaliga analys arbets belastningar

Azure Cosmos DB Analytical Store är optimerat för att ge skalbarhet, elastiskhet och prestanda för analytiska arbets belastningar utan något beroende på beräknings körnings tiden. Lagrings tekniken är själv hanterad för att optimera dina analys arbets belastningar. Med inbyggt stöd i Azure Synapse Analytics ger åtkomst till det här lagrings lagret enkelhet och höga prestanda.

### <a name="cost-effective"></a>Kostnadseffektiv

Med Azure Synapse-länken kan du få en kostnads optimerad, helt hanterad lösning för drift analys. Den eliminerar extra lager av lagring och data bearbetning som krävs i traditionella ETL-pipelines för att analysera drift data. 

Azure Cosmos DB Analytical Store följer en förbruknings pris modell som baseras på data lagring och analys av Läs-och skriv åtgärder och frågor som körs. Du behöver inte etablera några data flöden, som du gör i dag för transaktionernas arbets belastningar. Genom att komma åt dina data med mycket elastiska beräknings motorer från Azure Synapse Analytics blir den totala kostnaden för att köra lagring och beräkning mycket effektiv.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analys för lokalt tillgängliga, globalt distribuerade, flera olika region skrivningar

Du kan köra analytiska frågor effektivt mot den närmaste regionala kopian av data i Azure Cosmos DB. Azure Cosmos DB tillhandahåller den avancerade kapaciteten för att köra de globalt distribuerade analytiska arbets belastningarna tillsammans med transaktions arbets belastningar på ett aktivt och aktivt sätt.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Aktivera HTAP-scenarier för dina användnings data

Synapse-länken sammanställer Azure Cosmos DB analys lager med Azure Synapse Analytics runtime support. Med den här integreringen kan du skapa inbyggda Cloud HTAP-lösningar (hybrid transaktion/analys bearbetning) som genererar insikter baserade på real tids uppdateringar av dina drift data över stora data uppsättningar. Det låser upp nya affärs scenarier för att generera aviseringar baserat på Live-trender, bygga nära real tids instrument paneler och affärs upplevelser baserat på användarens beteende.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analys lager

Azure Cosmos DB Analytical Store är en kolumn-orienterad representation av dina drift data i Azure Cosmos DB. Det här analys lagret är lämpligt för snabba, kostnads effektiva frågor om stora användnings data uppsättningar, utan att kopiera data och påverka prestandan för dina transaktions arbets belastningar.

Analys lagringen hämtar automatiskt höga frekvens infogningar, uppdateringar och borttagningar i dina transaktions arbets belastningar i nära real tid, som en fullständigt hanterad funktion ("automatisk synkronisering") av Azure Cosmos DB. Ingen ändrings-feed eller ETL krävs. 

Om du har ett globalt distribuerat Azure Cosmos DB konto kommer det att vara tillgängligt i alla regioner för det kontot när du har aktiverat analytiskt lagrings utrymme för en behållare. Mer information om analys lagret finns i artikeln [Översikt över Azure Cosmos DB analys lager](analytical-store-introduction.md) .

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integrering med Azure Synapse Analytics

Med Synapse-länken kan du nu ansluta direkt till dina Azure Cosmos DB behållare från Azure Synapse Analytics och komma åt analys lagret utan separata anslutningar. Azure Synapse Analytics stöder för närvarande Synapse-länk med [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) och [Server lös SQL-pool](../synapse-analytics/sql/on-demand-workspace-overview.md).

Du kan fråga data från Azure Cosmos DB analys lager samtidigt, med interop över olika analys tider som stöds av Azure Synapse Analytics. Inga ytterligare data omvandlingar krävs för att analysera användnings data. Du kan fråga och analysera analys data med hjälp av:

* Synapse Apache Spark med fullständigt stöd för Scala, python, SparkSQL och C#. Synapse Spark är central för data teknik och data vetenskaps scenarier

* SQL-pool utan server med T-SQL-språk och stöd för välbekanta BI-verktyg (till exempel Power BI Premium osv.)

> [!NOTE]
> Från Azure Synapse Analytics kan du komma åt både analys-och transaktions lager i din Azure Cosmos DB-behållare. Men om du vill köra storskalig analys eller skanningar på dina användnings data rekommenderar vi att du använder analytisk lagring för att undvika prestanda påverkan på transaktions arbets belastningar.

> [!NOTE]
> Du kan köra analyser med låg latens i en Azure-region genom att ansluta din Azure Cosmos DB-behållare till Synapse runtime i den regionen.

Den här integrationen möjliggör följande HTAP-scenarier för olika användare:

* En BI-tekniker som vill modellera och publicera en Power BI rapport för att komma åt direkt användnings data i Azure Cosmos DB direkt via Synapse SQL.

* En dataanalytiker som vill härleda insikter från användnings data i en Azure Cosmos DB-behållare genom att fråga den med Synapse SQL, läsa data i skala och kombinera dessa resultat med andra data källor.

* En data expert som vill använda Synapse Spark för att hitta en funktion för att förbättra sin modell och träna den modellen utan att göra komplexa data tekniker. De kan också skriva resultatet av modellen efter att du har gått över till Azure Cosmos DB i real tid med data genom Spark-Synapse.

* En data tekniker som vill göra data tillgängliga för konsumenter genom att skapa SQL-eller Spark-tabeller över Azure Cosmos DB behållare utan manuella ETL-processer.

Mer information om stöd för Azure Synapse Analytics runtime för Azure Cosmos DB finns i [Azure Synapse Analytics for Cosmos DB support](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="security"></a>Säkerhet

Med Synapse-länken kan du köra nära real tids analys över dina verksamhets kritiska data i Azure Cosmos DB. Det är viktigt att se till att kritiska affärs data lagras säkert i både transaktions-och analys lager. Azure Synapse-länken för Azure Cosmos DB är utformad för att hjälpa till att uppfylla dessa säkerhets krav genom följande funktioner:

* **Nätverks isolering med privata slut punkter** – du kan styra nätverks åtkomsten till data i transaktions-och analys butikerna oberoende av varandra. Nätverks isolering görs med hjälp av separata hanterade privata slut punkter för varje butik, inom hanterade virtuella nätverk i Azure Synapse-arbetsytor. Mer information finns i så här [konfigurerar du privata slut punkter för analys av](analytical-store-private-endpoints.md) artikel.

* **Data kryptering med Kundhanterade nycklar** – du kan smidigt kryptera data i transaktions-och analys lager med samma Kundhanterade nycklar på ett automatiskt och transparent sätt. Mer information finns i artikeln så här [konfigurerar du kund hanterade nycklar](how-to-setup-cmk.md) .

* **Säker nyckel hantering** – åtkomst till data i analys lager från Synapse Spark-och Synapse-server utan SQL-pooler kräver hantering av Azure Cosmos DB nycklar inom Synapse Analytics-arbetsytor. I stället för att använda Azure Cosmos DB konto nycklar infogade i Spark-jobb eller SQL-skript ger Azure Synapse-länken fler säkra funktioner.

  * När du använder Synapse-server utan SQL-pooler kan du fråga Azure Cosmos DB Analytical Store genom att i förväg skapa SQL-autentiseringsuppgifter som lagrar konto nycklarna och refererar till dem i `OPENROWSET` funktionen. Mer information finns i [fråga med en server lös SQL-pool i Azure Synapse Link](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) -artikeln.

  * När du använder Synapse Spark kan du lagra konto nycklarna i länkade tjänst objekt som pekar på en Azure Cosmos DB-databas och referera till detta i Spark-konfigurationen vid körning. Mer information finns i [Kopiera data till en dedikerad SQL-pool med hjälp av Apache Spark](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) artikel.

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>När ska jag använda Azure Synapse-länken för Azure Cosmos DB?

Synapse-länk rekommenderas i följande fall:

* Om du är Azure Cosmos DB kund och vill köra analyser, BI och maskin inlärning över dina användnings data. I sådana fall ger Synapse-länken en mer integrerad analys upplevelse utan att påverka det etablerade data flödet i transaktions arkivet. Exempel:

  * Om du kör Analytics eller BI på Azure Cosmos DB operativa data direkt med hjälp av separata anslutningar idag, eller

  * Om du kör ETL-processer för att extrahera drift data till ett separat analys system.
 
I sådana fall ger Synapse-länken en mer integrerad analys upplevelse utan att påverka det etablerade data flödet i transaktions arkivet.

Synapse-länk rekommenderas inte om du söker efter traditionella informations lager krav som hög samtidighet, arbets belastnings hantering och persistence av agg regeringar över flera data källor. Mer information finns i [vanliga scenarier som kan användas med Azure Synapse-länken för Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Begränsningar

* Azure Synapse Link for Azure Cosmos DB stöds för SQL API och Azure Cosmos DB API for MongoDB. Det finns inte stöd för Gremlin-API, API för Cassandra och Tabell-API.

* Analys lager kan bara aktive ras för nya behållare. Om du vill använda analytisk lagring för befintliga behållare migrerar du data från dina befintliga behållare till nya behållare med hjälp av [Azure Cosmos DB Migreringsverktyg](cosmosdb-migrationchoices.md). Du kan aktivera Synapse-länk på nya och befintliga Azure Cosmos DB-konton.

* Automatisk säkerhets kopiering och återställning av dina data i det analytiska arkivet stöds inte för närvarande för behållarna med analytisk lagring aktiverat. När Synapse-länken är aktive rad på ett databas konto kommer Azure Cosmos DB fortsätta att automatiskt [ta säkerhets kopior](./online-backup-and-restore.md) av dina data i transaktions arkivet (endast) i behållare vid schemalagd säkerhets kopierings intervall, som alltid. Det är viktigt att Observera att när en behållare med ett analytiskt arkiv som är aktive rad återställs till ett nytt konto, kommer behållaren att återställas med enbart transaktions lagring och inget analytiskt Arkiv har Aktiver ATS.

* Det går inte att komma åt Azure Cosmos DB Analytics Store med SQL-etableringen för Synapse.

## <a name="pricing"></a>Priser

I fakturerings modellen för Azure Synapse-länken ingår kostnader som uppstår med hjälp av Azure Cosmos DB analys lager och Synapse Runtime. Mer information finns i artikeln om priser för [Azure Cosmos DB analys lager](analytical-store-introduction.md#analytical-store-pricing) och [Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/) .

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande dokument:

* [Översikt över Azure Cosmos DB analys lager](analytical-store-introduction.md)

* [Kom igång med Azure Synapse Link för Azure Cosmos DB](configure-synapse-link.md)
 
* [Vad stöds i Azure Synapse Analytics-körning](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Vanliga frågor och svar om Azure Synapse Link för Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Användningsfall för Azure Synapse Link för Azure Cosmos DB](synapse-link-use-cases.md)
