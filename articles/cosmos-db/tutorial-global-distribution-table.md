---
title: Självstudie om global distribution i Azure Cosmos DB för tabell-API
description: Lär dig hur global distribution fungerar i Azure Cosmos DB Tabell-API konton och hur du konfigurerar önskad lista över regioner
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: f06fbe482327d01917f648ccdec34baa15c46e80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073918"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Konfigurera global distribution i Azure Cosmos DB med tabell-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med [tabell-API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Ansluta till en önskad region med hjälp av tabell-API

För att kunna dra nytta av den [globala distributionen](distribute-data-globally.md)bör klient programmen ange den aktuella platsen där programmet körs. Detta görs genom att ange `CosmosExecutorConfiguration.CurrentRegion` egenskapen. `CurrentRegion`Egenskapen ska innehålla en enda plats. Varje klient instans kan ange sin egen region för läsningar med låg latens. Regionen måste namnges med hjälp av deras [visnings namn](/previous-versions/azure/reference/gg441293(v=azure.100)) som "västra USA". 

Azure Cosmos DB Tabell-API SDK väljer automatiskt den bästa slut punkten för att kommunicera med baserat på konto konfigurationen och den aktuella regionala tillgängligheten. Den prioriterar den närmaste regionen för att ge bättre svars tid till klienter. När du har angett den aktuella `CurrentRegion` egenskapen dirigeras Läs-och skriv förfrågningar enligt följande:

* **Läs begär Anden:** Alla Läs begär Anden skickas till den konfigurerade `CurrentRegion` . Baserat på närhet väljer SDK automatiskt en geo-replikerad reserv region för hög tillgänglighet.

* **Skriv begär Anden:** SDK: n skickar automatiskt alla Skriv förfrågningar till den aktuella Skriv regionen. I ett konto med skrivningar i flera regioner kommer den aktuella regionen också att betjäna Skriv förfrågningar. Baserat på närhet väljer SDK automatiskt en geo-replikerad reserv region för hög tillgänglighet.

Om du inte anger `CurrentRegion` egenskapen använder SDK den aktuella Skriv regionen för alla åtgärder.

Till exempel om ett Azure Cosmos-konto finns i regionerna "västra USA" och "östra USA". Om "västra USA" är Skriv regionen och programmet finns i "USA, östra". Om egenskapen CurrentRegion inte har kon figurer ATS dirigeras alla Läs-och skriv förfrågningar alltid till regionen "västra USA". Om egenskapen CurrentRegion har kon figurer ATS betjänas alla Läs förfrågningar från regionen "USA, östra".

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med tabell-API:n i Azure Cosmos DB