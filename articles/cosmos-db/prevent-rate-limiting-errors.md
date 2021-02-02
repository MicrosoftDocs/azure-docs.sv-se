---
title: Förhindra Rate-Limiting-fel för Azure Cosmos DB-API för MongoDB-åtgärder.
description: Lär dig att förhindra Azure Cosmos DB-API för MongoDB-åtgärder från att uppfylla fel vid begränsning av fel med funktionen SSR (Server på Server sidan).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: e1ccf55d38a9a3a5a1d0a3622c90dd7b51e5e477
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258498"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Förhindra Rate-Limiting-fel för Azure Cosmos DB-API för MongoDB-åtgärder
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB-API för MongoDB-åtgärder kan Miss lyckas med Rate Limiting (16500/429)-fel om de överskrider en samlings data flödes gräns (ru: er). 

Du kan aktivera funktionen för att försöka igen med Server sidan (SSR) och låta servern göra om dessa åtgärder automatiskt. Begär Anden görs om efter en kort fördröjning för alla samlingar i ditt konto. Den här funktionen är ett bekvämt alternativ till att hantera hastighets begränsning av fel i klient programmet.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till ditt Azure Cosmos DB-API för MongoDB-konto.

1. Gå till rutan **funktioner** under avsnittet **Inställningar** .

1. Välj **nytt försök på Server sidan**.

1. Klicka på **Aktivera** för att aktivera den här funktionen för alla samlingar i ditt konto.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Skärm bild av funktionen försök igen på Server sidan för Azure Cosmos DB-API för MongoDB":::

## <a name="use-the-azure-cli"></a>Använda Azure CLI

1. Kontrol lera om SSR redan har Aktiver ATS för ditt konto:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Aktivera** SSR för alla samlingar i ditt databas konto. Det kan ta upp till 15min för att ändringen ska börja gälla.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
Följande kommando kommer att **inaktivera** SSR för alla samlingar i ditt databas konto. Det kan ta upp till 15min för att ändringen ska börja gälla.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
* Hur görs nya försök?
    * Begär Anden provas kontinuerligt (över och över igen) tills en tids gräns på 60 sekund har nåtts. Om tids gränsen uppnås får klienten ett [ExceededTimeLimit-undantag (50)](mongodb-troubleshoot.md).
*  Hur kan jag övervaka effekterna av SSR?
    *  Du kan visa frekvens begränsnings fel (429s) som görs om Server sidan i fönstret Cosmos DB mått. Tänk på att dessa fel inte går till klienten när SSR är aktive rad, eftersom de hanteras och provas på Server sidan. 
    *  Du kan söka efter logg poster som innehåller "estimatedDelayFromRateLimitingInMilliseconds" i dina [Cosmos DB resurs loggar](cosmosdb-monitor-resource-logs.md).
*  Kommer SSR att påverka min konsekvens nivå?
    *  SSR påverkar inte konsekvensen för en begäran. Begär Anden görs om på Server sidan om de är begränsade (med ett 429-fel). 
*  Påverkar SSR vilken typ av fel som klienten kan få?
    *  Nej, SSR påverkar bara hastighets begränsnings fel (429s) genom att försöka igen på Server sidan. Den här funktionen gör att du inte behöver hantera hastighets begränsning av fel i klient programmet. Alla [andra fel](mongodb-troubleshoot.md) skickas till klienten. 

## <a name="next-steps"></a>Nästa steg

Mer information om fel sökning av vanliga fel finns i den här artikeln:

* [Felsök vanliga problem i Azure Cosmos DBs API för MongoDB](mongodb-troubleshoot.md)
