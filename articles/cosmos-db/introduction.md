---
title: Introduktion till Azure Cosmos DB
description: Läs om Azure Cosmos DB. Den här globalt distribuerade databasen med flera modeller har skapats för låg svarstid, elastisk skalbarhet och hög tillgänglighet, och den ger inbyggt stöd för NoSQL-data.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 9df5c63ef175d0cf736ce16036466158ab6565f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618312"
---
# <a name="welcome-to-azure-cosmos-db"></a>Välkommen till Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dagens program måste ha korta svarstider och alltid vara online. Instanser av dessa program måste distribueras i datacenter som ligger nära användarna för att uppnå kort svarstid och hög tillgänglighet. Programmen behöver kunna svara i realtid på stora förändringar av användningen vid hög belastning, lagra ständigt ökande datavolymer och göra dessa data tillgängliga för användare på millisekunder.

Azure Cosmos DB är en fullständigt hanterad NoSQL-databas för modern app-utveckling. Ensiffriga svars tider i millisekunder och automatisk och omedelbar skalbarhet, garantera hastighet i vilken skala som helst. Verksamhets kontinuitet är garanterat med [SLA-baserad](https://azure.microsoft.com/support/legal/sla/cosmos-db) tillgänglighet och säkerhet i företags klass. Utveckling av appar är snabbare och mer produktiv tack vare nyckel för data distribution i flera regioner var som helst i världen, API: er och SDK: er med öppen källkod för populära språk. Som en helt hanterad tjänst kan Azure Cosmos DB sköta databas administration med automatisk hantering, uppdateringar och uppdatering. Den hanterar också kapacitets hantering med kostnads effektiva alternativ för Server lös och automatisk skalning som svarar på program måste matcha kapacitet med efter frågan.

> [!NOTE]
> Vill du hjälpa till att förbättra Azure Cosmos DB dokument genom att delta i en användar studie? Ta några minuter för att fylla i den här undersökningen om 5 minuters [gallring](https://aka.ms/cosmosdb-documentation-screener-survey). Om du kvalificerar dig omdirigeras du till en Scheduler där du kan boka en plats för att ansluta till en interaktiv Research session. Inga personliga data samlas in under den här processen enligt vår [sekretess policy](https://go.microsoft.com/fwlink/?LinkId=521839).

Du kan [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden eller använda den [Azure Cosmos DB kostnads fria nivån](optimize-dev-test.md#azure-cosmos-db-free-tier) för att få ett konto med de första 400 ru/s och 5 GB ledigt lagrings utrymme.

> [!div class="nextstepaction"]
> [Prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB är en fullständigt hanterad NoSQL-databas för modern app-utveckling." border="false":::

## <a name="key-benefits"></a>Viktiga fördelar

### <a name="guaranteed-speed-at-any-scale"></a>Garanterad hastighet i valfri skala

Få oöverträffade [SLA-säkerhetskopierade](https://azure.microsoft.com/support/legal/sla/cosmos-db) hastigheter och data flöden, snabb global åtkomst och direkt elastiskhet.

- Real tids åtkomst med snabb Läs-och skriv fördröjning globalt och genom strömning och konsekvens som backas upp av [service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db)
- Skrivningar till flera regioner och data distribution till valfri Azure-region med en knapp klickning.
- Oberoende och elastisk skalning av lagring och data flöde i alla Azure-regioner – även vid oförutsägbara trafik burst – för obegränsad skalning över hela världen.

### <a name="simplified-application-development"></a>Förenklad program utveckling

Bygg snabbt med API: er för öppen källkod, flera SDK: er, schemabaserade data och ingen ETL-analys över drift data.

- Djupt integrerad med viktiga Azure-tjänster som används i modern (molnbaserad) app-utveckling, inklusive Azure Functions, IoT Hub, AKS (Azure Kubernetes service), App Service med mera.
- Välj mellan flera databas-API: er inklusive API: t för intern kärna (SQL), API för MongoDB, API för Cassandra, Gremlin API och Tabell-API.
- Bygg appar på Core-API (SQL) med de språk som du väljer med SDK: er för .NET, Java, Node.js och python. Eller ditt val av driv rutiner för alla andra databas-API: er.
- Kör ingen-ETL-analys över de nära real tids operativa data som lagras i Azure Cosmos DB med Azure Synapse Analytics.
- Genom att ändra feed är det enkelt att spåra och hantera ändringar i databas behållare och skapa utlösta händelser med Azure Functions.
- Azure Cosmos DB schema-mindre tjänst indexerar automatiskt alla dina data, oavsett data modellen, för att leverera blixt snabba snabb frågor.

### <a name="mission-critical-ready"></a>Redo för verksamhetskritiskt

Garantera affärs kontinuitet, 99,999% tillgänglighet och säkerhet på företags nivå för varje program.

- Azure Cosmos DB erbjuder en omfattande uppsättning [service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db) , inklusive branschledande tillgänglighet över hela världen.
- Distribuera enkelt data till alla Azure-regioner med automatisk datareplikering. Njut av noll stillestånd med skrivningar i flera regioner eller återställnings punkt 0 när du använder stark konsekvens.
- Njut av kryptering i företags klass med självhanterade nycklar.
- Rollbaserad åtkomst kontroll i Azure skyddar dina data och erbjuder fin justerad kontroll.

### <a name="fully-managed-and-cost-effective"></a>Fullständigt hanterad och kostnads effektiv

Databas hantering från slut punkt till slut punkt med Server lös och automatisk skalning som matchar ditt program och TCO behöver

- Fullständigt hanterad databas tjänst. Automatisk, ingen touch, underhåll, uppdatering och uppdateringar, sparar tid och pengar för utvecklare.
- Kostnads effektiva alternativ för oförutsedda eller sporadiska arbets belastningar i alla storlekar och skalor, vilket gör att utvecklare kan komma igång enkelt utan att behöva planera eller hantera kapacitet.
- Utan server modell erbjuder hög arbets belastning och tjänst som svarar automatiskt för att hantera burst-trafik på begäran.
- Automatisk skalning av allokerat data flöde automatiskt och skalar snabbt kapacitet för oförutsägbara arbets belastningar, samtidigt som [service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db)upprätthålls.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Lösningar som har nytta av Azure Cosmos DB

Alla [webb-, mobil-, spel-och IoT-program](use-cases.md) som behöver hantera stora mängder data, läsningar och skrivningar i en [global skala](distribute-data-globally.md) med nästan faktiska svars tider för en mängd olika data kommer att ha nytta av Cosmos DB [garanterad hög tillgänglighet](https://azure.microsoft.com/support/legal/sla/cosmos-db/), högt data flöde, låg latens och justerbara konsekvens. Lär dig mer om hur Azure Cosmos DB kan användas för att skapa [IoT och telematik](use-cases.md#iot-and-telematics), [handel och marknadsföring](use-cases.md#retail-and-marketing), [spel](use-cases.md#gaming) samt [webb- och mobilprogram](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

- [Komma igång med SQL-API för Azure Cosmos DB](create-sql-api-dotnet.md)
- [Kom igång med Azure Cosmos DB-API:n för Azure MongoDB](create-mongodb-nodejs.md)
- [Kom igång med Cassandra-API för Azure Cosmos DB](create-cassandra-dotnet.md)
- [Komma igång med Gremlin API för Azure Cosmos DB](create-graph-dotnet.md)
- [Komma igång med Tabell-API för Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)
