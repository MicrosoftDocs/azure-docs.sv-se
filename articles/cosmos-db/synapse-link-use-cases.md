---
title: Real tids analys användnings fall med Azure Synapse-länk för Azure Cosmos DB
description: Lär dig hur Azure Synapse-länken för Azure Cosmos DB används i tillhandahållar kedja, Prognosticering, rapportering, anpassning i real tid och IOT Maintenance förutsägande underhåll.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 253257564ffc96108f282d7de3dbd9f5e8ce68fd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869879"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Azure Synapse Link för Azure Cosmos DB: Användningsfall för analys i nästan realtid
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse-länken](synapse-link.md) för Azure Cosmos DB är en inbyggd hybrid transaktions-och analys bearbetnings funktion (HTAP) i molnet som gör det möjligt att köra nära real tids analys över drift data. Synapse-länken skapar en tätt sömlös integrering mellan Azure Cosmos DB och Azure Synapse Analytics.

Du kanske undrar över vilka bransch användnings fall som kan utnyttja den här moln inbyggda HTAP-funktionen för nära real tids analys över drifts data. Här är tre vanliga användnings fall för Azure Synapse-länken för Azure Cosmos DB:

* Tillhandahålla kedje analys, Prognosticering & rapportering
* Anpassning i real tid
* Förutsägelse underhåll, avvikelse identifiering i IOT-scenarier

> [!NOTE]
> Azure Synapse-länk för Azure Cosmos DB rikta in dig på scenariot där företags grupper ska köras nära analys i real tid. Dessa analyser körs utan ETL över drift data som genereras i transaktions program som bygger på Azure Cosmos DB. Detta ersätter inte behovet av ett separat informations lager när det finns traditionella informations lager krav som arbets belastnings hantering, hög samtidighet, beständiga mängder över flera data källor.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Tillhandahålla kedje analys, Prognosticering & rapportering

Forsknings studier visar att inbäddning av stor data analys i leverans kedjas åtgärder leder till förbättringar av leverans tider för beställning till cykel och effektivitet för leverans kedja.

Tillverkare integreras med molnbaserade tekniker för att ta bort begränsningar av äldre versioner av ERP (Enterprise Resource Planning) och SCM-system (Supply Chain Management). Med leverans kedjor som genererar ökande volymer av operativa data varje minut (order, leverans, transaktions data) behöver tillverkarna en Använd databas. Den här operativa databasen ska skalas för att hantera data volymer och en analys plattform för att komma till en nivå av Sammanhangs beroende information i real tid för att ligga före kurvan.

Följande arkitektur visar kraften i att använda Azure Cosmos DB som den molnbaserade operativa databasen och Synapse-länken i tillhandahållar kedje analys:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Azure Synapse-länk för Azure Cosmos DB i tillhandahållar kedje analys " border="false":::

Baserat på tidigare arkitektur kan du uppnå följande användnings fall med Synapse-länken för Azure Cosmos DB:

* **Förbered & träna förutsägande pipeline:** Generera insikter över drifts data över leverans kedjan med Machine Learning översenade. På så sätt kan du sänka inventeringen, drifts kostnaderna och minska beställnings tiden för kunderna.

  Med Synapse-länken kan du analysera ändrade drift data i Azure Cosmos DB utan några manuella ETL-processer. Du slipper ytterligare kostnad, svars tid och drifts komplexitet. Synapse-länken gör det möjligt för data tekniker och data forskare att bygga robusta förutsägbara pipelines:

  * Fråga om operativa data från Azure Cosmos DB analytisk lagring genom att använda inbyggd integrering med Apache Spark pooler i Azure Synapse Analytics. Du kan köra frågor mot data i en interaktiv bärbar dator eller schemalagda Fjärrjobb utan komplex data teknik.

  * Bygg Machine Learning (ML) modeller med Spark ML-algoritmer och Azure ML-integrering i Azure Synapse Analytics.

  * Skriv tillbaka resultaten efter det att du har förväntat en modell i Azure Cosmos DB för drift i real tid.

* **Funktions rapportering:** Team för leverans kedja behöver flexibla och anpassade rapporter över real tids data. Dessa rapporter krävs för att få en Snapshot-vy över effektivitet, lönsamhet och produktivitet för leverans kedjan. Det gör det möjligt för data analytiker och andra viktiga intressenter att kontinuerligt utvärdera verksamheten och identifiera områden för att minska drifts kostnaderna. 

  Synapse-länk för Azure Cosmos DB möjliggör omfattande Business Intelligence (BI)/Reporting-scenarier:

  * Fråga om operativa data från Azure Cosmos DB analys lager med hjälp av inbyggd integrering med Server lös SQL-pool och fullständig Expressiveness av T-SQL-språk.

  * Modeller och publicera automatiska uppdateringar av BI-instrumentpaneler över Azure Cosmos DB via stöd för server utan SQL-pool för välkända BI-verktyg. Till exempel Azure Analysis Services, Power BI Premium osv.

Nedan följer några rikt linjer för data integrering för batch & strömma data till Azure Cosmos DB:

* **Integration av batch-data &:** Med hjälp av den här informationen kan du få mer komplexa data plattformar för tillhandahållaren som ska integreras med olika data källor och format. Azure Synapse innehåller inbyggda data integrerings motor och upplevelser som Azure Data Factory. Den här integrationen gör det möjligt för data tekniker att skapa omfattande datapipelines utan en separat Orchestration-motor:

  * Flytta data från 85 + data källor som stöds till [Azure Cosmos dB med Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

  * Skriva kod fri ETL-pipelines till Azure Cosmos DB [, inklusive Relations-till-hierarkiska och hierarkiska-hierarkiska mappningar med mappnings data flöden](../data-factory/how-to-sqldb-to-cosmosdb.md).

* **Bearbetning av data integrerings & för strömning:** Med en tillväxt av industriella IoT (sensorer som spårar till gångar från "golv till butik", anslutna logistik flottar osv.), finns det en nedbrytning av data i real tid som genereras i en strömmande miljö som måste integreras med traditionell långsam flytt av data för att generera insikter. Azure Stream Analytics är en rekommenderad tjänst för att strömma ETL och bearbeta i Azure med en [mängd olika scenarier](../stream-analytics/streaming-technologies.md). Azure Stream Analytics stöder [Azure Cosmos DB som en intern data mottagare](../stream-analytics/stream-analytics-documentdb-output.md).

## <a name="real-time-personalization"></a>Anpassning i real tid

Detaljister idag måste bygga säkra och skalbara e-handelslösningar som uppfyller kraven för både kunder och företag. Dessa e-handelslösningar behöver engagera kunder genom anpassade produkter och erbjudanden, bearbeta transaktioner snabbt och säkert och fokusera på uppfyllelse och kund tjänst. Azure Cosmos DB tillsammans med den senaste Synapse-länken för Azure Cosmos DB gör det möjligt för åter försäljare att skapa anpassade rekommendationer för kunder i real tid. De använder konsekvens inställningar med låg latens och justerbara för omedelbara insikter som du ser i följande arkitektur:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Azure Synapse-länk för Azure Cosmos DB i real tids anpassning" border="false":::

Synapse-länk för Azure Cosmos DB användnings fall:

* **Förbered & träna förutsägande pipeline:** Du kan generera insikter över drift data över dina affär senheter eller kund segment med hjälp av Synapse Spark-och Machine Learning-modeller. Detta översätter till anpassad leverans till kund segment, förutsägande slut användar upplevelser och riktad marknadsföring för att passa dina slut användar krav.

## <a name="iot-predictive-maintenance"></a>Förutsägbart underhåll av IOT

Industriella IOT-innovationer har drastiskt minskat drift stopp i maskiner och ökat övergripande effektivitet i alla bransch områden. En av dessa innovationer är förebyggande underhålls analys för maskiner i molnets kant.

Följande är en arkitektur som utnyttjar molnets inbyggda HTAP-funktioner i Azure Synapse-länken för Azure Cosmos DB i förutsägbart underhåll av IoT:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="Azure Synapse-länk för Azure Cosmos DB i förutsägande underhåll i IOT" border="false" :::

Synapse-länk för Azure Cosmos DB användnings fall:

* **Förbered & träna förutsägande pipeline:** Historiska drift data från IoT-enhetens sensorer kan användas för att träna förutsägelse modeller, till exempel avvikande detektorer. Dessa avvikelsedetektorer distribueras sedan tillbaka till gränsen för övervakning i realtid. En sådan positiv användnings-slinga möjliggör kontinuerlig omskolning av förutsägande modeller.

* **Funktions rapportering:** Med tillväxten av digitala dubbla initiativ samlar företag stora mängder drift data från ett stort antal sensorer för att bygga en digital kopia av varje dator. Dessa data befogenheter BI måste förstå trender för historiska data utöver real tids program över senaste frekventa data.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Exempel scenario: HTAP för Azure Cosmos DB

För nästan en tio års tid har Azure Cosmos DB använts av tusentals kunder för verksamhets kritiska program som kräver elastisk skala, nyckel färdig global distribution, Write-replikering i flera regioner för låg latens och hög tillgänglighet för både läsning & skrivningar i sina transaktions arbets belastningar.
 
I följande lista visas en översikt över de olika arbets belastnings mönster som stöds med användnings data med hjälp av Azure Cosmos DB:

* Appar i real tid & tjänster
* Bearbetning av händelseströmmar
* BI-instrumentpaneler
* Stordataanalyser
* Maskininlärning

Med Azure dataSynapses-länken kan Azure Cosmos DB inte bara driva transaktions arbets belastningar utan även utföra analytiska arbets belastningar i nära real tid över historiska drift data. Det sker utan ETL-krav och garanterad prestanda isolering från transaktions arbets belastningar.

Följande bild visar arbets belastnings mönster med Azure Cosmos DB: :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Azure Synapse-länk för Azure Cosmos DB arbets belastnings mönster" border="false":::

Låt oss ta ett exempel på ett e-handelsföretags CompanyXYZ med globala åtgärder i 20 länder/regioner för att illustrera fördelarna med att välja Azure Cosmos DB som enkel real tids databas med både transaktions-och analys krav för en inventerings hanterings plattform.

* CompanyXYZ-kärnan är beroende av inventerings hanterings systemet – därför är tillgänglighets & tillförlitlighet viktiga krav i pelaren. Fördelar med att använda Azure Cosmos DB:

  * Genom djup integrering med Azure-infrastrukturen och transparent flera regioner-skrivningar, global replikering, Azure Cosmos DB ger branschledande [99,999% hög tillgänglighet](high-availability.md) mot regionala avbrott.

* CompanyXYZs partner för leverans kedjan kan finnas på separata geografiska platser, men de kan behöva se en enskild vy över produkt inventeringen över hela världen för att stödja sina lokala åtgärder. Detta inkluderar behovet av att kunna läsa uppdateringar som görs av andra partners partner i real tid. Och kan göra uppdateringar utan att oroa dig för konflikter med andra partner i högt data flöde. Fördelar med att använda Azure Cosmos DB:

  * Med dess unika lagrings protokoll för flera regioner och spärr fri, skrivbar transaktions lagring, Azure Cosmos DB garanterar mindre än 10 ms fördröjning för både indexerade läsningar och skrivningar vid 99 percentil globalt.

  * Högt data flöde inmatning av båda batch-& strömma datafeeds med [real tids indexering](index-policy.md) i transaktions lagring.

  * Azure Cosmos DB transaktions lager ger tre fler alternativ än de två extrema nivåerna av starka och slutliga konsekvens nivåer för att uppnå [tillgänglighet jämfört med prestanda kompromisser](./consistency-levels.md) som är närmast affärs behovet.

* CompanyXYZs partner för leverans kedjan har mycket varierande trafik mönster som sträcker sig från hundratals till miljon tals begär Anden/s och därför måste inventerings hanterings plattformen hantera oväntade burstiness i trafiken.  Fördelar med att använda Azure Cosmos DB:

  * Azure Cosmos DB transaktions lager har stöd för elastisk skalbarhet för lagring och data flöde med vågrät partitionering. Behållare och databaser som kon figurer ATS i autopilot-läge kan automatiskt skala det etablerade data flödet baserat på programmets behov utan att påverka tillgängligheten, svars tiden, data flödet eller prestandan för arbets belastningen globalt.

* CompanyXYZ måste upprätta en säker analys plattform för att ta del av hela systemets historiska inventerings data för att möjliggöra analyser och insikter över partner för leverans kedja, affär senheter och funktioner. Analys plattformen måste möjliggöra samarbete över systemet, traditionella BI/rapporterings användnings fall, avancerade analys användnings fall och förutsägelse intelligenta lösningar över drift inventerings data. Fördelar med att använda Synapse-länken för Azure Cosmos DB:

  * Genom att använda [Azure Cosmos DB Analytical Store](analytical-store-introduction.md), är det ett fullständigt isolerat kolumn lager, Synapse-länk som gör det möjligt att använda ETL-analys (Extract-Transform-Load) i [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) mot globalt distribuerade drift data i stor skala.  Affärsanalytiker, data tekniker och data experter kan nu använda Synapse Spark-eller Synapse SQL på ett samverkande sätt för att köra real tids Business Intelligence, analyser och maskin inlärnings pipelines utan att påverka prestandan hos sina transaktions arbets belastningar på Azure Cosmos DB. Se [fördelarna med Synapse-länken i Azure Cosmos DB](synapse-link.md) för mer information.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande dokument:

* [Azure Synapse Link för Azure Cosmos DB](synapse-link.md) 

* [Azure Cosmos DB-analysarkiv](analytical-store-introduction.md)

* [Arbeta med Azure Synapse-länk för Azure Cosmos DB](configure-synapse-link.md)

* [Vanliga frågor och svar om Azure Synapse Link för Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Apache Spark i Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [Stöd för SQL-adresspool utan server i Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)