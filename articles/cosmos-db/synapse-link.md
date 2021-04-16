---
title: Azure Synapse Link för Azure Cosmos DB, fördelar och när du ska använda den
description: Läs mer om Azure Synapse Link för Azure Cosmos DB. Synapse Link kan du köra analys i nära realtid (HTAP) med hjälp av Azure Synapse Analytics över driftdata i Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 123c443e1afaf8eaded7021b963b68b3d8a8f554
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483783"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Vad är Azure Synapse Link för Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse Link för Azure Cosmos DB är en molnbaserad funktion för hybridtransaktions- och analysbearbetning (HTAP) som gör att du kan köra analys i nära realtid över driftdata i Azure Cosmos DB. Azure Synapse Link skapar en nära sömlös integrering mellan Azure Cosmos DB och Azure Synapse Analytics.

Med [Azure Cosmos DB](analytical-store-introduction.md)ett helt isolerat kolumnlager med hjälp av Azure Synapse Link kan du inte analysera ETL-analys (Extract-Transform-Load) [i Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) mot dina driftdata i stor skala. Affärsanalytiker, datatekniker och dataexperter kan nu använda Synapse Spark eller Synapse SQL synonymt för att köra pipelines för business intelligence, analys och maskininlärning nästan i realtid. Du kan åstadkomma detta utan att påverka prestanda för dina transaktionsarbetsbelastningar på Azure Cosmos DB. 

Följande bild visar Azure Synapse Link-integrering med Azure Cosmos DB och Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Arkitekturdiagram för Azure Synapse Analytics integrering med Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Fördelar

För att analysera stora driftdatamängder samtidigt som effekten på prestandan för verksamhetskritiska transaktionsarbetsbelastningar minimeras extraheras och bearbetas driftdata i Azure Cosmos DB av ETL-pipelines (Extract-Transform-Load). ETL-pipelines kräver många lager av dataförflyttning, vilket ger mycket driftskomplexitet och prestandapåverkan på dina transaktionsarbetsbelastningar. Det ökar också svarstiden för att analysera driftdata från ursprungstiden.

Jämfört med traditionella ETL-baserade lösningar erbjuder Azure Synapse Link för Azure Cosmos DB flera fördelar, till exempel:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Minskad komplexitet utan ETL-jobb att hantera

Azure Synapse Link kan du direkt komma åt Azure Cosmos DB analysarkiv med hjälp Azure Synapse Analytics utan komplex dataförflyttning. Alla uppdateringar som görs i driftdata visas i analysarkivet nästan i realtid utan ETL- eller ändringsflödesjobb. Du kan köra storskalig analys mot analysarkiv, från Azure Synapse Analytics, utan ytterligare datatransformering.

### <a name="near-real-time-insights-into-your-operational-data"></a>Insikter i nära realtid om dina driftdata

Nu kan du få omfattande insikter om dina driftdata nästan i realtid med hjälp av Azure Synapse Link. ETL-baserade system tenderar att ha högre svarstid för att analysera dina driftdata, på grund av många lager som behövs för att extrahera, transformera och läsa in driftdata. Med intern integrering av Azure Cosmos DB analysarkiv med Azure Synapse Analytics kan du analysera driftdata i nära realtid och möjliggöra nya affärsscenarier. 

### <a name="no-impact-on-operational-workloads"></a>Ingen inverkan på operativa arbetsbelastningar

Med Azure Synapse Link kan du köra analysfrågor mot ett Azure Cosmos DB-analysarkiv (ett separat kolumnarkiv) medan transaktionella åtgärder bearbetas med etablerat dataflöde för transaktionsarbetsbelastningen (ett radbaserat transaktionsarkiv).  Den analytiska arbetsbelastningen betjänas oberoende av transaktionsarbetsbelastningens trafik utan att förbruka något dataflöde som etablerats för dina driftdata.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Optimerad för storskaliga analysarbetsbelastningar

Azure Cosmos DB analysarkiv är optimerat för att ge skalbarhet, elasticitet och prestanda för analytiska arbetsbelastningar utan beroende av beräkningskörningarna. Lagringstekniken är själv hanterad för att optimera dina analysarbetsbelastningar. Med inbyggt stöd i Azure Synapse Analytics ger åtkomst till det här lagringslagret enkelhet och höga prestanda.

### <a name="cost-effective"></a>Kostnadseffektiv

Med Azure Synapse Link kan du få en kostnadsoptimerad, fullständigt hanterad lösning för driftsanalys. Det eliminerar de extra lager av lagring och beräkning som krävs i traditionella ETL-pipelines för analys av driftdata. 

Azure Cosmos DB analysarkiv följer en förbrukningsbaserad prismodell som baseras på datalagring och analytiska läs-/skrivåtgärder och frågor som körs. Det kräver inte att du etablerar något dataflöde, vilket du gör i dag för transaktionsarbetsbelastningarna. Att komma åt dina data med mycket elastiska beräkningsmotorer från Azure Synapse Analytics gör den totala kostnaden för att köra lagring och beräkning mycket effektiv.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analys för lokalt tillgängliga, globalt distribuerade skrivningar för flera regioner

Du kan köra analytiska frågor effektivt mot närmaste regionala kopia av data i Azure Cosmos DB. Azure Cosmos DB ger den senaste möjligheten att köra globalt distribuerade analytiska arbetsbelastningar tillsammans med transaktionsarbetsbelastningar på ett aktivt-aktivt sätt.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Aktivera HTAP-scenarier för dina driftdata

Synapse Link samman Azure Cosmos DB analysarkiv med stöd Azure Synapse för analyskörning. Med den här integreringen kan du skapa molnbaserade HTAP-lösningar (hybridtransaktionsbaserad/analytisk bearbetning) som genererar insikter baserat på realtidsuppdateringar av dina driftdata över stora datamängder. Den låser upp nya affärsscenarier för att skapa aviseringar baserat på livetrender, skapa instrumentpaneler i nära realtid och affärsupplevelser baserat på användarbeteende.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analysarkiv

Azure Cosmos DB analysarkiv är en kolumnorienterad representation av dina driftdata i Azure Cosmos DB. Det här analysarkivet lämpar sig för snabba och kostnadseffektiva frågor om stora driftdatamängder, utan att kopiera data och påverka prestandan för dina transaktionsarbetsbelastningar.

Analysarkiv hämtar automatiskt infogningar, uppdateringar och borttagningar i transaktionsarbetsbelastningar nästan i realtid som en fullständigt hanterad funktion ("automatisk synkronisering") av Azure Cosmos DB. Ingen ändringsfeed eller ETL krävs. 

Om du har ett globalt Azure Cosmos DB-konto blir det tillgängligt i alla regioner för det kontot när du aktiverar analysarkiv för en container. Mer information om analysarkivet finns i [översiktsartikeln Azure Cosmos DB Analysarkiv.](analytical-store-introduction.md)

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integrering med Azure Synapse Analytics

Med Synapse Link kan du nu ansluta direkt till dina Azure Cosmos DB containrar från Azure Synapse Analytics och komma åt analysarkivet utan separata anslutningsappar. Azure Synapse Analytics stöder för närvarande Synapse Link med [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) [och serverlös SQL-pool.](../synapse-analytics/sql/on-demand-workspace-overview.md)

Du kan köra frågor mot data från Azure Cosmos DB analysarkiv samtidigt, med interop mellan olika analyskörningstider som stöds av Azure Synapse Analytics. Inga ytterligare datatransformationer krävs för att analysera driftdata. Du kan köra frågor mot och analysera analysarkivdata med hjälp av:

* Synapse Apache Spark med fullständigt stöd för Scala, Python, SparkSQL och C#. Synapse Spark är centralt för datateknik- och datavetenskapsscenarier

* Serverlös SQL-pool med T-SQL-språk och stöd för välbekanta BI-verktyg (till exempel Power BI Premium osv.)

> [!NOTE]
> Från Azure Synapse Analytics kan du komma åt både analys- och transaktionslager i din Azure Cosmos DB container. Men om du vill köra storskaliga analyser eller genomsökningar på dina driftdata rekommenderar vi att du använder analysarkiv för att undvika prestandapåverkan på transaktionsarbetsbelastningar.

> [!NOTE]
> Du kan köra analys med kort svarstid i en Azure-region genom att ansluta din Azure Cosmos DB till Synapse-körningen i den regionen.

Den här integreringen möjliggör följande HTAP-scenarier för olika användare:

* En BI-tekniker som vill modellera och publicera en Power BI-rapport för att få åtkomst till driftdata i Azure Cosmos DB direkt via Synapse SQL.

* En dataanalytiker som vill härleda insikter från driftdata i en Azure Cosmos DB-container genom att fråga den med Synapse SQL, läsa data i stor skala och kombinera dessa resultat med andra datakällor.

* En dataexpert som vill använda Synapse Spark för att hitta en funktion för att förbättra sin modell och träna modellen utan att göra komplex datateknik. De kan också skriva resultatet av modellens efterinferens till Azure Cosmos DB för realtidsbedömning av data via Spark Synapse.

* En datatekniker som vill göra data tillgängliga för konsumenter genom att skapa SQL- eller Spark-tabeller över Azure Cosmos DB utan manuella ETL-processer.

Mer information om hur du Azure Synapse Analytics runtime-stöd för Azure Cosmos DB finns [i Azure Synapse Analytics för Cosmos DB support](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>När ska jag använda Azure Synapse Link för Azure Cosmos DB?

Synapse Link rekommenderas i följande fall:

* Om du är en Azure Cosmos DB kund och du vill köra analyser, BI och maskininlärning över dina driftdata. I sådana fall Synapse Link ger en mer integrerad analysupplevelse utan att påverka transaktionslagrets etablerade dataflöde. Exempel:

  * Om du kör analys eller BI på Azure Cosmos DB använda data direkt via separata anslutningsappar i dag, eller

  * Om du kör ETL-processer för att extrahera driftdata till ett separat analyssystem.
 
I sådana fall Synapse Link ger en mer integrerad analysupplevelse utan att påverka transaktionslagrets etablerade dataflöde.

Synapse Link rekommenderas inte om du letar efter traditionella informationslagerkrav som hög samtidighet, arbetsbelastningshantering och beständighet av aggregeringar över flera datakällor. Mer information finns i [vanliga scenarier som kan drivas med Azure Synapse Link för Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Begränsningar

* Azure Synapse Link for Azure Cosmos DB stöds för SQL API och Azure Cosmos DB API for MongoDB. Det stöds inte för Gremlin API, API för Cassandra och Tabell-API.

* Analysarkiv kan bara aktiveras för nya containrar. Om du vill använda analysarkiv för befintliga containrar migrerar du data från dina befintliga containrar till nya [containrar Azure Cosmos DB migreringsverktyg](cosmosdb-migrationchoices.md). Du kan aktivera Synapse Link på nya och befintliga Azure Cosmos DB konton.

* För containrar med analysarkiv aktiverat stöds inte automatisk säkerhetskopiering och återställning av data i analysarkivet just nu. När Synapse Link är aktiverat på ett databaskonto fortsätter Azure Cosmos DB automatiskt att göra säkerhetskopior av dina data i transaktionslagret (endast) av containrar vid schemalagda [säkerhetskopieringsintervall,](./online-backup-and-restore.md) som alltid. Det är viktigt att observera att när en container med analysarkiv aktiverat återställs till ett nytt konto, återställs containern med endast transaktionsarkiv och inget analysarkiv aktiverat.

* Åtkomst till Azure Cosmos DB analytics-arkivet med Synapse SQL är inte tillgänglig för närvarande.

## <a name="security"></a>Säkerhet

Synapse Link kan du köra analys i nära realtid över dina verksamhetskritiska data i Azure Cosmos DB. Det är viktigt att se till att kritiska affärsdata lagras på ett säkert sätt i både transaktionslager och analyslager. Azure Synapse Link för Azure Cosmos DB har utformats för att uppfylla dessa säkerhetskrav med hjälp av följande funktioner:

* **Nätverksisolering med privata slutpunkter** – Du kan styra nätverksåtkomsten till data i transaktions- och analysarkiven oberoende av varandra. Nätverksisolering görs med hjälp av separata hanterade privata slutpunkter för varje butik i hanterade virtuella nätverk i Azure Synapse arbetsytor. Mer information finns i artikeln Konfigurera [privata slutpunkter för analysarkiv.](analytical-store-private-endpoints.md)

* **Datakryptering med kund** hanterade nycklar – Du kan sömlöst kryptera data i transaktions- och analysarkiv med samma kund hanterade nycklar på ett automatiskt och transparent sätt. Mer information finns i artikeln Konfigurera [kund hanterade](how-to-setup-cmk.md) nycklar.

* **Säker nyckelhantering** – Åtkomst till data i analysarkiv från Synapse Spark- och Synapse-serverlösa SQL-pooler kräver hantering av Azure Cosmos DB nycklar i Synapse Analytics arbetsytor. I stället för att Azure Cosmos DB infogade kontonycklar i Spark-jobb eller SQL-skript ger Azure Synapse Link säkrare funktioner.

  * När du använder Synapse-serverlösa SQL-pooler kan du fråga Azure Cosmos DB-analysarkivet genom att skapa SQL-autentiseringsuppgifter som lagrar kontonycklarna i förväg och referera till dem i `OPENROWSET` funktionen. Mer information finns i artikeln Query with a serverless SQL pool in Azure Synapse Link (Fråga med en [serverlös SQL-pool Azure Synapse Link).](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

  * När du använder Synapse Spark kan du lagra kontonycklarna i länkade tjänstobjekt som pekar på en Azure Cosmos DB-databas och referera till detta i Spark-konfigurationen vid körning. Mer information finns i artikeln [Kopiera data till en dedikerad SQL-pool Apache Spark SQL-pool.](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md)


## <a name="pricing"></a>Priser

Faktureringsmodellen för Azure Synapse Link innehåller kostnader som uppstår vid användning av Azure Cosmos DB-analysarkivet och Synapse-körningen. Mer information finns i artikeln [om Azure Cosmos DB priser för analysarkiv](analytical-store-introduction.md#analytical-store-pricing) och Azure Synapse Analytics [prisartiklar.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande dokument:

* [Azure Cosmos DB översikt över analysarkiv](analytical-store-introduction.md)

* [Kom igång med Azure Synapse Link för Azure Cosmos DB](configure-synapse-link.md)
 
* [Vad stöds i Azure Synapse Analytics-körning](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Vanliga frågor och svar om Azure Synapse Link för Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Användningsfall för Azure Synapse Link för Azure Cosmos DB](synapse-link-use-cases.md)
