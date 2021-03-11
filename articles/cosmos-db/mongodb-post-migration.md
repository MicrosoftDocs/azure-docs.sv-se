---
title: Optimerings steg efter migreringen med Azure Cosmos DBs API för MongoDB
description: Det här dokumentet innehåller optimerings tekniker efter migrering från MongoDB till Azure Cosmos DB s APi för mongo DB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 02/14/2021
ms.author: chrande
ms.openlocfilehash: ce0facaba43e9cc8e37b19fbd3dfc840067d278f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560403"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Optimerings steg efter migreringen när du använder Azure Cosmos DBs API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

När du har migrerat data som lagras i MongoDB-databasen till API för Azure Cosmos DB för MongoDB kan du ansluta till Azure Cosmos DB och hantera data. Den här guiden innehåller anvisningar att överväga efter migreringen. Mer information om hur du [migrerar MongoDB finns i avsnittet migrera till Azure Cosmos DB s API för MongoDB](../dms/tutorial-mongodb-cosmos-db.md) .

I den här guiden får du lära dig hur du:

- [Anslut ditt program](#connect-your-application)
- [Optimera indexerings principen](#optimize-the-indexing-policy)
- [Konfigurera global distribution för Azure Cosmos DB s API för MongoDB](#globally-distribute-your-data)
- [Ange konsekvens nivå](#set-consistency-level)

> [!NOTE]
> Det enda obligatoriska steget efter migrering på program nivån är att ändra anslutnings strängen i programmet så att den pekar på ditt nya Azure Cosmos DB-konto. Alla andra steg i migreringen är rekommenderade optimeringar.
>

## <a name="connect-your-application"></a>Anslut ditt program

1. Logga in på [Azure Portal](https://www.portal.azure.com/) i ett nytt fönster
2. Från [Azure Portal](https://www.portal.azure.com/)öppnar du menyn **alla resurser** i den vänstra rutan och letar upp det Azure Cosmos DB konto som du har migrerat dina data till.
3. Öppna bladet **anslutnings sträng** . Den högra rutan innehåller all information som du behöver för att ansluta till ditt konto.
4. Använd anslutnings informationen i programmets konfiguration (eller andra relevanta platser) för att återspegla Azure Cosmos DBens API för MongoDB-anslutning i din app.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="Skärm bild som visar inställningarna för en anslutnings sträng.":::

Mer information finns på sidan [Anslut ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md) .

## <a name="optimize-the-indexing-policy"></a>Optimera indexerings principen

Alla data fält indexeras automatiskt som standard under migreringen av data till Azure Cosmos DB. I många fall är den här standard indexerings principen acceptabel. I allmänhet optimerar borttagning av index Skriv förfrågningar och har standard indexerings principen (dvs. automatisk indexering) optimerar Läs begär Anden.

Mer information om indexering finns [i data indexering i Azure Cosmos DBS API för MongoDB](mongodb-indexing.md) samt [indexering i Azure Cosmos DB](index-overview.md) artiklar.

## <a name="globally-distribute-your-data"></a>Distribuera dina data globalt

Azure Cosmos DB är tillgängligt i alla [Azure-regioner](https://azure.microsoft.com/regions/#services) över hela världen. När du har valt standard konsekvens nivå för ditt Azure Cosmos DB konto kan du associera en eller flera Azure-regioner (beroende på dina globala distributions behov). För hög tillgänglighet och affärs kontinuitet rekommenderar vi alltid att du kör i minst två regioner. Du kan läsa tipsen för att [optimera kostnaderna för distributioner i flera regioner i Azure Cosmos DB](optimize-cost-regions.md).

Om du vill distribuera dina data globalt kan du läsa [Distribuera data globalt på Azure Cosmos DBS API för MongoDB](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Ange konsekvens nivå

Azure Cosmos DB erbjuder 5 väldefinierade [konsekvens nivåer](consistency-levels.md). Läs [konsekvens nivåer och Azure Cosmos DBS-API: er](./consistency-levels.md)för att läsa om mappningen mellan MongoDB och Azure Cosmos DB konsekvens nivåer. Standard konsekvens nivån är konsekvens nivån för sessionen. Att ändra konsekvens nivån är valfritt och du kan optimera den för din app. Ändra konsekvens nivå med Azure Portal:

1. Gå till bladet **standard konsekvens** under Inställningar.
2. Välj din [konsekvens nivå](consistency-levels.md)

De flesta användare lämnar sin konsekvens nivå i standardinställningen för konsekvens för sessioner. Det finns dock [tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](./consistency-levels.md).

## <a name="next-steps"></a>Nästa steg

* [Ansluta ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md)
* [Anslut till Azure Cosmos DB konto med Studio 3T](mongodb-mongochef.md)
* [Distribuera läsningar globalt med Azure Cosmos DB s API för MongoDB](mongodb-readpreference.md)
* [Ta bort data med Azure Cosmos DB:s API för MongoDB](mongodb-time-to-live.md)
* [Konsekvens nivåer i Azure Cosmos DB](consistency-levels.md)
* [Indexering i Azure Cosmos DB](index-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)