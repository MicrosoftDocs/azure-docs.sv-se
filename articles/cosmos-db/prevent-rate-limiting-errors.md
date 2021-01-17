---
title: Förhindra Rate-Limiting-fel för Azure Cosmos DB-API för MongoDB-åtgärder.
description: Lär dig att förhindra Azure Cosmos DB-API för MongoDB-åtgärder från att uppfylla fel vid begränsning av fel med funktionen SSR (Server på Server sidan).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540736"
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


## <a name="next-steps"></a>Nästa steg

Mer information om fel sökning av vanliga fel finns i den här artikeln:

* [Felsök vanliga problem i Azure Cosmos DBs API för MongoDB](mongodb-troubleshoot.md)
