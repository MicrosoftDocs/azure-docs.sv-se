---
title: Övervaka data flödes användningen för en åtgärd i Azure Cosmos DB
description: Lär dig hur du övervakar genomflödet eller begär enhets användningen för en åtgärd i Azure Cosmos DB. Ägare av ett Azure Cosmos DB konto kan förstå vilka åtgärder som tar fler enheter för programbegäran.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: d6f0f30d7437b6f8ecc1d915eb3d3195f2504fec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93098254"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Så här övervakar du genomflödet eller begär enhets användning av en åtgärd i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor för Azure Cosmos DB ger en mått vy för att övervaka ditt konto och skapa instrument paneler. De Azure Cosmos DB måtten samlas in som standard, men den här funktionen kräver inte att du aktiverar eller konfigurerar något explicit. Det totala måttet för **enhets begär** Anden används för att hämta enhets användning för olika typer av åtgärder. Senare kan du analysera vilka åtgärder som har använt merparten av data flödet. Data flödes data sammanställs som standard med ett minuters intervall. Du kan dock ändra agg regerings enheten genom att ändra alternativet tids kornig het.

Det finns två sätt att analysera användnings data för begär ande enheter:

* Inom det aktuella tidsintervallet som åtgärder tar fler enheter för programbegäran.
* Vilka åtgärder i allmänhet dominerar din arbets belastning genom att förbruka fler enheter för programbegäran.
Med den här analysen kan du fokusera på åtgärder som Infoga, upsert och titta på deras indexering. Du kan ta reda på om du har indexerat vissa fält eller om du ändrar [indexerings principen](index-policy.md#include-exclude-paths) så att Sök vägarna tas med eller undantas.

Om du märker att vissa frågor tar fler enheter för programbegäran kan du vidta åtgärder, till exempel:

* Överväg om du begär rätt mängd data.
* Ändra frågan så att index med filter-satsen används.
* Utför mindre dyra UDF-funktions anrop.
* Definiera partitionsnyckel för att minimera fläkten från fråga till olika partitioner.
* Du kan också använda de frågesträngar som returnerades i anrops svaret, diagnostikinformation och [läsa artikeln om frågekörning för](sql-api-query-metrics.md) att läsa mer om frågekörningen.
* Du kan börja från sum och sedan titta på genomsnittlig användning med rätt dimension.

## <a name="view-the-total-request-unit-usage-metric"></a>Visa den totala användnings måttet för begär ande enheter

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **övervaka** i det vänstra navigerings fältet och välj **mått**.

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Mått fönstret i Azure Monitor":::

1. I fönstret **mått** > väljer du **en resurs** > väljer den nödvändiga **prenumerationen** och **resurs gruppen**. För **resurs typen** väljer du **Azure Cosmos DB konton**, väljer något av dina befintliga Azure Cosmos-konton och väljer **Använd**.

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="Välj Azure Cosmos DB konto om du vill visa mått":::

1. Välj sedan den **totala mått enhets** måttet i listan över tillgängliga mått. Mer information om alla tillgängliga mått i den här listan finns i artikeln [mått per kategori](monitor-cosmos-db-reference.md) . I det här exemplet väljer vi **Totalt antal enheter för programbegäran** och **AVG** som agg regerings värde. Förutom dessa uppgifter kan du också välja **tidsintervallet** och **tids kornig het** för måtten. Som Max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på ditt filter. Du kan se det genomsnittliga antalet förbrukade enheter för programbegäran per minut för den valda perioden.  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Välj ett mått från Azure Portal":::

## <a name="filters-for-request-unit-usage"></a>Filter för att begära enhets användning

Du kan också filtrera mått och hämta diagram som visas av en speciell **samlings**-, **databasename**-, **OperationType**-, **region**, **status** och **StatusCode**. Med alternativen **Lägg till filter** och **Använd delning** kan du filtrera enhets användningen för begäran och gruppera måtten.

Om du vill hämta den begärda enhets användningen för varje åtgärd, antingen av total summan (sum) eller medelvärdet väljer du **tillämpa delning** och väljer **Åtgärds typ** och filter värde enligt följande bild:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Cosmos DB enheter för programbegäran för åtgärder i Azure Monitor":::

Om du vill se användningen av enhets enheter per samling väljer du **Använd delning** och väljer samlings namnet som ett filter. Du kommer att se en chatt som följer med ett urval av samlingar i instrument panelen. Du kan sedan välja ett angivet samlings namn om du vill visa mer information:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Cosmos DB enheter för programbegäran för alla åtgärder av samlingen i Azure Monitor":::

## <a name="next-steps"></a>Nästa steg

* Övervaka Azure Cosmos DB data med hjälp av [diagnostiska inställningar](cosmosdb-monitor-resource-logs.md) i Azure.
* [Granska Azure Cosmos DB kontroll Plans åtgärder](audit-control-plane-logs.md)
