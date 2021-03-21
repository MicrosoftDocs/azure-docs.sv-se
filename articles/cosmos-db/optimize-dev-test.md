---
title: Optimering för utveckling och testning i Azure Cosmos DB
description: I den här artikeln förklaras hur Azure Cosmos DB erbjuder flera alternativ för utveckling och testning av tjänsten kostnads fritt.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 3ddae808fbb2e3dcfe20909c8b3d0c5a20bb04bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247530"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimera utvecklings- och testkostnaden i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Den här artikeln beskriver de olika alternativen för att använda Azure Cosmos DB för utveckling och testning kostnads fritt, samt tekniker för att optimera kostnaderna för utveckling eller test konton.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB emulator (lokalt hämtnings bar version)

[Azure Cosmos DB emulator](local-emulator.md) är en lokal nedladdnings bar version som imiterar Azure Cosmos DB moln tjänsten. Du kan skriva och testa kod som använder Azure Cosmos DB-API: er även om du inte har någon nätverks anslutning och utan att behöva betala några kostnader. Azure Cosmos DB emulator är en lokal miljö i utvecklings syfte med hög kvalitet på moln tjänsten. Du kan utveckla och testa ditt program lokalt utan att skapa en Azure-prenumeration. När du är redo att distribuera ditt program till molnet uppdaterar du anslutnings strängen för att ansluta till Azure Cosmos DB slut punkten i molnet. inga andra ändringar krävs. Du kan också skapa [en CI/CD-pipeline med åtgärden Azure Cosmos DB emulator-](tutorial-setup-ci-cd.md) skapande i Azure DevOps för att köra tester. Du kan komma igång genom att gå till [Azure Cosmos DB emulator](local-emulator.md) -artikeln.

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB kostnads fri nivå

Azure Cosmos DB kostnads fri nivå är det enkelt att komma igång, utveckla och testa dina program, eller till och med köra små produktions arbets belastningar utan kostnad. När den kostnads fria nivån har Aktiver ATS för ett konto får du de första 400 RU/s och 5 GB lagrings utrymme i kontot kostnads fritt. Du kan också skapa en delad data flödes databas med 25 behållare som delar 400 RU/s på databas nivå, alla som omfattas av den kostnads fria nivån (gräns 5 delade data flödes databaser på ett kostnads fritt nivå konto). När du använder den kostnads fria nivån, om du etablerar en delad databas med ett lägsta data flöde på 400 RU/s, kan alla behållarna i databasen dela data flödet. Alla nya databaser med delat data flöde eller behållare med dedikerat data flöde faktureras enligt den vanliga prissättningen.

> [!NOTE]
> Den kostnads fria nivån är endast tillgänglig i ett allokerat data flödes läge.

Den kostnads fria nivån är oändlig för kontots livstid och levereras med alla [fördelar och funktioner](introduction.md#key-benefits) i ett vanligt Azure Cosmos DB konto, inklusive obegränsad lagring och data flöde (ru/s), service avtal, hög tillgänglighet, nyckel färdig global distribution i alla Azure-regioner med mera. Du kan ha upp till ett konto med en kostnads fri nivå per Azure-prenumeration och måste välja när du skapar kontot. Kom igång genom att [skapa ett nytt konto i Azure Portal med kostnads fri nivå aktive rad](create-cosmosdb-resources-portal.md) eller använda en [arm-mall](./manage-with-templates.md#free-tier). Mer information finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="try-azure-cosmos-db-for-free"></a>Testa Azure Cosmos DB kostnadsfritt

[Prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) är en kostnads fri upplevelse som gör att du kan experimentera med Azure Cosmos db i molnet utan att registrera dig för ett Azure-konto eller använda ditt kredit kort. Försök Azure Cosmos DB-konton är tillgängliga under en begränsad tid, för närvarande 30 dagar. Du kan förnya dem när du vill. Försök Azure Cosmos DB konton gör det enkelt att utvärdera Azure Cosmos DB, bygga och testa ett program eller använda snabb starter eller självstudier. Du kan också skapa en demonstration, utföra enhets testning eller till och med skapa ett konto med flera regioner och köra en app på den utan att behöva göra några kostnader. I ett try Azure Cosmos DB-konto kan du ha en delad data flödes databas med högst 25 behållare och 20 000 RU/s av data flödet, eller en behållare med upp till 5000 RU/s. Kom igång genom att läsa [testa Azure Cosmos DB kostnads fri](https://azure.microsoft.com/try/cosmosdb/) sida.

## <a name="azure-free-account"></a>Kostnadsfritt Azure-konto

Azure Cosmos DB ingår i det [kostnads fria Azure-kontot](https://azure.microsoft.com/free), vilket ger Azure-krediter och resurser kostnads fritt under en viss tids period. Specifikt för Azure Cosmos DB erbjuder det här kostnads fria kontot 25 GB lagring och 400 ru: er data flöde för hela året. Den här upplevelsen gör det möjligt för utvecklare att enkelt testa funktionerna i Azure Cosmos DB eller integrera den med andra Azure-tjänster på noll kostnader. Med Azures kostnads fria konto får du en $200 kredit att spendera under de första 30 dagarna. Du debiteras inte, även om du börjar använda tjänsterna tills du väljer att uppgradera. Gå till sidan [kostnads fritt Azure-konto](https://azure.microsoft.com/free) för att komma igång.

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB utan Server

Med [Azure Cosmos DB server utan server](serverless.md) kan du använda ditt Azure Cosmos-konto i en förbruknings punkt där du bara debiteras för de enheter för programbegäran som används av databas åtgärderna och det lagrings utrymme som förbrukas av dina data. Det finns ingen minimi avgift när du använder Azure Cosmos DB i Server fritt läge. Eftersom det eliminerar begreppet tillhandahållen kapacitet, är det bäst att utveckla eller testa aktiviteter specifikt när databasen är inaktiv i det mesta av tiden.

## <a name="use-shared-throughput-databases"></a>Använda delade data flödes databaser

I en [delad data flödes databas](set-throughput.md#set-throughput-on-a-database)delar alla behållare i databasen det etablerade data flödet (ru/s) i databasen. Om du till exempel etablerar en databas med 400 RU/s och har fyra behållare, kommer alla fyra behållare att dela 400 RU/s. I en utvecklings-eller test miljö, där varje behållare kan nås mindre ofta och därför kräver mindre än minst 400 RU/s, kan du använda behållare i en delad data flödes databas för att optimera kostnaderna.

Anta till exempel att ditt utvecklings-eller test konto har fyra behållare. Om du skapar fyra behållare med dedikerat data flöde (minst 400 RU/s) blir din totala RU/s 1600 RU/s. Om du däremot skapar en delad data flödes databas (minst 400 RU/s) och använder dina behållare där, är din totala RU/s bara 400 RU/s. I allmänhet är delade data flödes databaser fantastiska för scenarier där du inte behöver garanterat data flöde på någon enskild behållare.  Lär dig mer om [delade data flödes databaser.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Nästa steg

Du kan komma igång med att använda emulatorn eller de kostnads fria Azure Cosmos DBs kontona med följande artiklar:

* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om [Azure Cosmos DB utan server](serverless.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](./optimize-cost-reads-writes.md)
* Läs mer om hur [du optimerar kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)