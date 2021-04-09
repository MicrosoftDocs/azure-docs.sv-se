---
title: Global distribution av Azure Cosmos DB
description: Lär dig att replikera data globalt med Azure Cosmos-DB i Azure Portal
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: 58788d6194454c8bd40730c9c350aa901924ba3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027335"
---
## <a name="add-global-database-regions-using-the-azure-portal"></a><a id="addregion"></a>Lägg till globala databasregioner med Azure Portal
Azure Cosmos DB är tillgängligt i alla [Azure-regioner][azureregions] över hela världen. När du har valt standardkonsekvensnivå för ditt databaskonto, så kan du associera en eller flera regioner (beroende på ditt val av standardkonsekvensnivå och globala distributionsbehov).

1. Klicka på **Azure Cosmos DB** i fältet till vänster i [Azure Portal](https://portal.azure.com/).
2. Markera det databaskonto du vill ändra på sidan **Azure Cosmos DB**.
3. Klicka på **Replikera data globalt** på menyn på kontosidan.
4. Välj de regioner som ska läggas till eller tas bort genom att klicka på regioner på kartan på sidan **Replikera data globalt** och klicka sedan på **Spara**. Det kostar att lägga till regioner. Mer information finns på sidan om [prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/) eller i artikeln [Distribuera data globalt med Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).
   
    ![Lägg till eller ta bort regioner på kartan genom att klicka på dem][1]
    
När du lägger till en andra region aktiveras alternativet **manuell redundans** på sidan **Replikera data globalt** i portalen. Du kan använda det här alternativet för att testa redundansprocessen eller ändra den primära skrivningsregionen. När du lägger till en tredje region aktiveras alternativet **Redundansprioriteringar** på samma sida, så att du kan ändra redundansordningen för läsningar.  

### <a name="selecting-global-database-regions"></a>Välja globala databasregioner
Det finns två vanliga scenarier när det gäller att konfigurera två eller flera regioner:

1. Leverera åtkomst med låg latens till data för slutanvändare oavsett var i världen de befinner sig
2. Lägga till regional återhämtning till affärskontinuitet och haveriberedskap (BCDR)

När det gäller att leverera låg latens till slutanvändare bör du distribuera både programmet och Azure Cosmos DB i de regioner som motsvarar de platser där programmets användare finns.

När det gäller affärskontinuitet och haveriberedskap bör du lägga till regioner utifrån de regionpar som beskrivs i artikeln [Affärskontinuitet och haveriberedskap (BCDR): parade Azure-regioner][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: ../articles/best-practices-availability-paired-regions.md
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/