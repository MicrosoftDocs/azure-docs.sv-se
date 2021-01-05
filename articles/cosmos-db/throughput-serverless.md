---
title: Välja mellan ett insamlat data flöde och Server lös Azure Cosmos DB
description: Lär dig mer om hur du väljer mellan allokerat data flöde och Server lös för din arbets belastning.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/23/2020
ms.openlocfilehash: a6f8b79ecc8dcac71dc8f5d1be2bb58a0288a307
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760342"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Välja mellan ett allokerat data flöde och Server lös
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB finns i två olika kapacitets lägen: [etablerade data flöden](set-throughput.md) och [Server](serverless.md)lös. Du kan utföra exakt samma databas åtgärder i båda lägena, men det sätt som du debiteras för dessa åtgärder är radikalt annorlunda. I följande videoklipp förklaras de grundläggande skillnaderna mellan dessa lägen och hur de passar olika typer av arbets belastningar:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>Detaljerad jämförelse

| Kriterie | Etablerat dataflöde | Utan server |
| --- | --- | --- |
| Status | Allmänt tillgänglig | I förhandsversion |
| Passar bäst för | Verksamhets kritiska arbets belastningar som kräver förutsägbara prestanda | Små till medel stora arbets belastningar med lätt och intermittent trafik som är svåra att förutsäga |
| Så här fungerar det | För var och en av dina behållare tillhandahåller du viss mängd data flöde som uttrycks i [enheter för programbegäran](request-units.md) per sekund. Varje sekund är den här mängden enheter för programbegäran tillgänglig för dina databas åtgärder. Det etablerade data flödet kan uppdateras manuellt eller justeras automatiskt med [autoskalning](provision-throughput-autoscale.md). | Du kör dina databas åtgärder mot dina behållare utan att behöva etablera någon kapacitet. |
| Geo-distribution | Tillgängligt (obegränsat antal Azure-regioner) | Otillgänglig (Server lös konton kan bara köras i 1 Azure-region) |
| Maximalt lagrings utrymme per behållare | Obegränsat | 50 GB |
| Prestanda | 99,99% till 99,999% tillgänglighet som omfattas av SLA<br>< 10 MS-svars tid för punkt läsningar och skrivningar som omfattas av SLA<br>99,99% garanterat data flöde som omfattas av SLA | 99,9% till 99,99% tillgänglighet som omfattas av SLA<br>< 10 MS-svars tid för punkt läsningar och < 30 ms för skrivningar som omfattas av service nivå mål<br>95% burst-överföring som omfattas av service nivå mål |
| Faktureringsmodell | Faktureringen görs per timme för RU/s etablerad, oavsett hur många ru: er som för bruk ATS. | Faktureringen görs per timme för den mängd ru: er som förbrukas av dina databas åtgärder. |

> [!IMPORTANT]
> Några av Server lösa begränsningar kan vara minskat eller tas bort när Server lös blir allmänt tillgängligt och **din feedback** hjälper oss att avgöra! Kontakta oss och berätta mer om din server lös upplevelse: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="estimating-your-expected-consumption"></a>Uppskattar den förväntade förbrukningen

I vissa situationer kan det vara oklart om det etablerade data flödet eller Server lösa ska väljas för en viss arbets belastning. För att få hjälp med det här beslutet kan du uppskatta den **förväntade förbrukningen**, det vill säga det totala antalet ru: er som du kan använda under en månad (du kan beräkna detta med hjälp av tabellen som visas [här](plan-manage-costs.md#estimating-serverless-costs))

**Exempel 1**: en arbets belastning förväntas överföras till maximalt 500 ru/s och förbruka totalt 20 000 000 ru: er under en månad.

- I etablerat data flödes läge skulle du tillhandahålla en behållare med 500 RU/s för en månatlig kostnad av: $0,008 * 5 * 730 = **$29,20**
- I Server fritt läge betalar du för den förbrukade ru: er: $0,25 * 20 = **$5,00**

**Exempel 2**: en arbets belastning förväntas överföras till maximalt 500 ru/s och förbruka totalt 250 000 000 ru: er under en månad.

- I etablerat data flödes läge skulle du tillhandahålla en behållare med 500 RU/s för en månatlig kostnad av: $0,008 * 5 * 730 = **$29,20**
- I Server fritt läge betalar du för den förbrukade ru: er: $0,25 * 250 = **$62,50**

(de här exemplen redovisas inte för lagrings kostnaden, vilket är detsamma mellan de två lägena)

> [!NOTE]
> Kostnaderna som visas i föregående exempel är endast i demonstrations syfte. På [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) finns den senaste pris informationen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [konfigurerar data flödet på Azure Cosmos DB](set-throughput.md)
- Läs mer om [Azure Cosmos DB utan server](serverless.md)
- Bekanta dig med begreppet enheter för [programbegäran](request-units.md)
