---
title: Övervaka Server sidans svars tid för åtgärder i Azure Cosmos DB
description: Lär dig hur du övervakar Server svars tid för åtgärder i Azure Cosmos DB konto eller en behållare. Ägare av ett Azure Cosmos DB konto kan förstå problem med svars tiden på Server sidan med dina Azure Cosmos-konton.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: ec6a9db63504958640137fcd0fcfc904eb01afa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93074737"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Övervaka Server sidans svars tid för åtgärder i en Azure Cosmos DB behållare eller ett konto
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor för Azure Cosmos DB ger en mått vy för att övervaka ditt konto och skapa instrument paneler. De Azure Cosmos DB måtten samlas in som standard, men den här funktionen kräver inte att du aktiverar eller konfigurerar något explicit. Måttet på Server sidans svars tid används för att Visa svars tiden på Server sidan för en åtgärd. Azure Cosmos DB tillhandahåller service avtal på mindre än 10 ms för punkt-Läs/skriv-åtgärder med direkt anslutning. För punkt läsnings-och skriv åtgärder beräknas service avtal så som beskrivs i [SLA-dokumentet](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Du kan övervaka Server sidans svars tid om du ser ovanligt hög latens för punkt åtgärder som:

* En GET-eller SET-åtgärd med partitionsnyckel och ID i läget för direkt anslutning
* En Läs-eller Skriv åtgärd eller
* En fråga

Du kan söka efter Diagnostic-loggen för att se storleken på de data som returneras. Om du ser en varaktig hög latens för frågor, bör du leta upp diagnostikloggar för högre [data flöde eller ru/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) som används. Svars tiden på Server sidan visar hur lång tid som ägnats åt Server dels infrastrukturen innan data returnerades till klienten. Det är viktigt att titta på det här måttet för att utesluta eventuella svars tids fördröjnings problem.

## <a name="view-the-server-side-latency-metric"></a>Visa fördröjnings mått på Server Sidan

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **övervaka** i det vänstra navigerings fältet och välj **mått**.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Mått fönstret i Azure Monitor":::

1. I fönstret **mått** > väljer du **en resurs** > väljer den nödvändiga **prenumerationen** och **resurs gruppen**. För **resurs typen** väljer du **Azure Cosmos DB konton**, väljer något av dina befintliga Azure Cosmos-konton och väljer **Använd**.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Välj Azure Cosmos DB konto om du vill visa mått":::

1. Välj nästa **svars tid på Server sidan**  från listan över tillgängliga mått. Mer information om alla tillgängliga mått i den här listan finns i artikeln [mått per kategori](monitor-cosmos-db-reference.md) . I det här exemplet väljer vi **svars tid på Server sidan** och **Genomsnittligt** som agg regerings värde. Förutom dessa uppgifter kan du också välja **tidsintervallet** och **tids kornig het** för måtten. Som Max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på ditt filter. Du kan se svars tiden på Server sidan per minut för den valda perioden.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Välj måttet Server-Side svars tid från Azure Portal":::

## <a name="filters-for-server-side-latency"></a>Filter för svars tid på Server Sidan

Du kan också filtrera mått och hämta diagram som visas av en speciell **samlings**-, **ConnectionMode**-, **databasename**-, **OperationType**-, **region**-och **PublicAPIType**. 

Om du vill filtrera måtten väljer du **Lägg till filter** och väljer den obligatoriska egenskapen som **PublicAPIType** och väljer värdet **SQL**. Lägg till ett annat filter för **OperationType**. Diagrammet visar sedan svars tiden på Server sidan för olika åtgärder under den valda perioden. De åtgärder som utförs via den lagrade proceduren loggas inte, så de är inte tillgängliga i OperationType-måttet.

**Server sidans svars** värden för varje åtgärd visas enligt följande bild:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Filter för svars värden på Server Sidan":::

Du kan också gruppera måtten med hjälp av alternativet **Använd delning** .  

## <a name="next-steps"></a>Nästa steg

* Övervaka Azure Cosmos DB data med hjälp av [diagnostiska inställningar](cosmosdb-monitor-resource-logs.md) i Azure.
* [Granska Azure Cosmos DB kontroll Plans åtgärder](audit-control-plane-logs.md)
