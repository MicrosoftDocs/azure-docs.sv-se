---
title: Introduktion till Cassandra-API:et för Azure Cosmos DB
description: Lär dig hur du kan använda Azure Cosmos DB för att "lyfta och byta" befintliga program och bygga nya program med hjälp av Cassandra-drivrutiner och CQL
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 11/25/2020
ms.openlocfilehash: 2a778c2e9a44bda148fd40112776858943cffe4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96188623"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Introduktion till Cassandra-API:et för Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra-API:et för Azure Cosmos DB kan användas som datalager för appar som är skrivna för [Apache Cassandra](https://cassandra.apache.org). Det betyder att ditt befintliga Cassandra-program, genom att använda befintliga [Apache-drivrutiner](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) som är kompatibla med CQLv4, nu kan kommunicera med Cassandra-API:et för Azure Cosmos DB. I många fall kan du växla från att använda Apache Cassandra för att använda Azure Cosmos DB API för Cassandra, genom att bara ändra en anslutnings sträng. 

Med API för Cassandra kan du interagera med data som lagras i Azure Cosmos DB med hjälp av CQL (), Cassandrabaserade verktyg (t. ex. cqlsh) och Cassandra klient driv rutiner som du redan är van vid.

> [!NOTE]
> [Läget för Server lös kapacitet](serverless.md) är nu tillgängligt på Azure Cosmos DB API för Cassandra.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Vad är fördelen med att använda Apache Cassandra API:t för Azure Cosmos DB?

**Ingen åtgärdshantering**: Som en fullständigt hanterad molntjänst eliminerar Cassandra-API:et för Azure Cosmos DB det tidskrävande arbetet med att hantera och övervaka en mängd olika inställningar i operativsystem, JVM och yaml-filer och deras interaktioner. Azure Cosmos DB erbjuder övervakning av dataflödet, svarstider, lagring, tillgänglighet och konfigurerbara aviseringar.

**Öppen källkod**: trots att den är en helt hanterad tjänst har API för Cassandra fortfarande stöd för en stor yta i det inbyggda [Apache Cassandra Wire-protokollet](cassandra-support.md), vilket gör att du kan skapa program på ett mycket använt och ett moln oberoende med öppen källkod.

**Prestandahantering**: Azure Cosmos DB tillhandahåller läsningar och skrivningar med garanterat korta svarstider inom den 99:e percentilen, backade av serviceavtalen. Användarna behöver inte bekymra sig om driftsomkostnader för att säkerställa höga prestanda och snabba läsningar och skrivningar. Det innebär att användarna inte behöver lösa schemaläggningsproblem, hantera tombstones eller skapa bloomfilter och repliker manuellt. Med Azure Cosmos DB slipper du ägna tid åt dessa problem så att du kan fokusera på programlogiken.

**Möjlighet att använda befintlig kod och verktyg**: Azure Cosmos DB erbjuder kompatibilitet på trådprotokollsnivå med befintliga SDK:er och verktyg för Cassandra. Den här kompatibiliteten garanterar att du kan använda din befintliga kodbas med Cassandra-API:et för Azure Cosmos DB med minimala ändringar.

**Elastiskhet för data flöde och lagring**: Azure Cosmos DB tillhandahåller data flöde i alla regioner och kan skala det etablerade data flödet med Azure Portal-, PowerShell-eller CLI-åtgärder. Du kan skala lagring och data flöde [elastiskt](manage-scale-cassandra.md) i dina tabeller efter behov med förutsägbar prestanda.

**Global distribution och tillgänglighet**: Med Azure Cosmos DB kan du distribuera data över alla Azure-regioner globalt och tillhandahålla data lokalt med snabb dataåtkomst och hög tillgänglighet. Azure Cosmos DB erbjuder 99,99 % hög tillgänglighet inom en region och 99,999 % läs- och skrivtillgänglighet i flera regioner utan ytterligare driftkostnader. Läs mer i artikeln [Distribuera data globalt](distribute-data-globally.md). 

**Val av konsekvensnivåer:** Azure Cosmos DB låter dig välja mellan fem väldefinierade konsekvensnivåer för bästa möjliga balans mellan konsekvens och prestanda. Dessa konsekvensnivåer är stark, begränsat föråldrad, session, konsekvent prefix och eventuell. Dessa väldefinierade, praktiska och intuitiva konsekvensnivåer hjälper utvecklare att göra exakta avvägningarna mellan konsekvens, tillgänglighet och svarstider. Läs mer i artikeln om [konsekvensnivåer](consistency-levels.md). 

**Enterprise-klass**: Azure Cosmos DB erbjuder [kompatibilitetscertifieringar](https://www.microsoft.com/trustcenter) så att användare kan använda plattformen på ett säkert sätt. Azure Cosmos DB erbjuder även kryptering i vila och i rörelse, IP-brandvägg och granskningsloggar för kontrollplansaktiviteter.

**Händelse källa**: API för Cassandra ger åtkomst till en beständig ändrings logg, [ändrings flödet](cassandra-change-feed.md), som kan under lätta händelse källan direkt från databasen. I Apache Cassandra är den enda motsvarigheten för registrering av ändrings data (CDC), som bara är en mekanism för att flagga vissa tabeller för arkivering, samt för att avvisa skrivningar till dessa tabeller när en konfigurerbar storlek-på disk för CDC-loggen nås (dessa funktioner är överflödiga i Cosmos DB att de relevanta aspekterna regleras automatiskt).

## <a name="next-steps"></a>Nästa steg

* Du kan snabbt komma igång med att utveckla följande språkspecifika appar för att skapa och hantera data för Cassandra-API:et:
  - [Node.js-app](create-cassandra-nodejs.md)
  - [.NET-app](create-cassandra-dotnet.md)
  - [Python-app](create-cassandra-python.md)

* Kom igång med att [skapa ett API för Cassandra konto, en databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program.

* [Läs in exempeldata i tabellen för Cassandra-API:et](cassandra-api-load-data.md) med hjälp av ett Java-program.

* [Fråga efter data från kontot för Cassandra-API:et](cassandra-api-query-data.md) med hjälp av ett Java-program.

* Mer information om Apache Cassandra-funktioner som stöds av Cassandra-API:et för Azure Cosmos DB finns i artikeln om [Cassandra-stöd](cassandra-support.md).

* Läs våra [vanliga frågor och svar](cassandra-faq.md).
