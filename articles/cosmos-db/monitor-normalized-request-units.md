---
title: Övervaka normaliserade RU/s för en Azure Cosmos-behållare eller ett konto
description: Lär dig hur du övervakar den normaliserade enheten för begäran om användning av en åtgärd i Azure Cosmos DB. Ägare av ett Azure Cosmos DB konto kan förstå vilka åtgärder som förbrukar fler enheter för programbegäran.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 01/07/2021
ms.openlocfilehash: ec82532b54e7834b62fcc03d3ee7de1345a0f546
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027816"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Övervaka normaliserade RU/s för en Azure Cosmos-behållare eller ett konto
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor för Azure Cosmos DB ger en mått vy för att övervaka ditt konto och skapa instrument paneler. De Azure Cosmos DB måtten samlas in som standard, men den här funktionen kräver inte att du aktiverar eller konfigurerar något explicit.

Det **normaliserade ru-förbruknings** måttet används för att se hur väl mättad partitionens nyckel intervall är vad gäller trafiken. Azure Cosmos DB distribuerar data flödet jämnt över alla partitionerings nyckel intervall. Det här måttet ger en vy av den maximala data flödes användningen för partitionerings intervall i en andra vy. Använd det här måttet för att beräkna RU/s-användningen över partitionerings intervallet för den aktuella behållaren. Om du använder det här måttet, bör du öka data flödet för att uppfylla arbets Belastningens behov, om du ser hög procent andel av förfrågans enhets användningar för alla partitionstyper i Azure Monitor. Exempel – normaliserad användning definieras som Max för RU/s-användningen för alla partitionerings nyckel intervall. Anta till exempel att ditt högsta data flöde är 20 000 RU/s och att du har två nyckel intervall, P_1 och P_2, som var och en kan skalas till 10 000 RU/s. Om P_1 har använt 6000 ru: er och P_2 8000 ru: er är den normaliserade användningen MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Vad som ska förväntas och göra när normaliserade RU/s är högre

När den normaliserade RU/s-förbrukningen når 100% för det angivna partitionsnumret, och om en klient fortfarande skickar begär anden i tidsfönstret på 1 sekund till det särskilda partitionsnyckel, får det ett begränsat fel. Klienten bör respektera den föreslagna vänte tiden och försöka utföra begäran igen. SDK gör det enkelt att hantera den här situationen genom att försöka med förkonfigurerade tider på lämpligt sätt.  Det är inte nödvändigt att du ser begränsningen för RU-frekvensen eftersom den normaliserade RU har nått 100%. Det beror på att normaliserat RU är ett enda värde som representerar Max användningen för alla partitionsnyckel, men ett nyckel intervall kan vara upptaget, men de andra partition nyckel intervallen kan hantera begär Anden utan problem. Till exempel kan en enskild åtgärd, till exempel en lagrad procedur som förbrukar alla RU/s på ett nyckel intervall, leda till en kort insamling i den normaliserade RU/s-förbrukningen. I dessa fall uppstår inga omedelbara hastighetsbegränsningsfel om begärandefrekvensen är låg eller om begäranden görs till andra partitioner med olika partitionsnyckelintervall. 

Azure Monitor måtten hjälper dig att hitta åtgärder per status kod för SQL-API med hjälp av måttet **Totalt antal begär Anden** . Senare kan du filtrera efter dessa förfrågningar med status koden 429 och dela upp dem efter **Åtgärds typ**.  

För att hitta begär Anden, som är begränsade, är det rekommenderade sättet att hämta den här informationen via diagnostikloggar.

Om det finns kontinuerlig hög belastning på 100% normaliserade RU/s-förbrukning eller nära 100% över flera nyckel intervall, rekommenderar vi att du ökar data flödet. Du kan ta reda på vilka åtgärder som är stora och deras högsta användning genom att använda Azure Monitor-mått och Azure Monitor-diagnostikloggar.

I sammanfattning används det **normaliserade ru-förbruknings** måttet för att se vilka nyckel intervall som är mer varma i användnings villkor. Det innebär att du kan skeva data flödet mot ett nyckel intervall. Senare kan du följa upp för att se **PartitionKeyRUConsumption** -loggen i Azure Monitor loggar för att få information om vilka logiska partitionsnyckel som är frekventa när det gäller användningen. Detta leder till ändringar i antingen partitionsfunktionen eller ändringen i program logiken. Du kan lösa hastighets begränsningen genom att distribuera data belastningen på flera partitioner eller bara öka i data flödet eftersom det är obligatoriskt. 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Visa den normaliserade förbruknings måttet för begär ande enheter

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **övervaka** i det vänstra navigerings fältet och välj **mått**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Mått fönstret i Azure Monitor":::

3. I fönstret **mått** > väljer du **en resurs** > väljer den nödvändiga **prenumerationen** och **resurs gruppen**. För **resurs typen** väljer du **Azure Cosmos DB konton**, väljer något av dina befintliga Azure Cosmos-konton och väljer **Använd**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Välj ett Azure Cosmos-konto om du vill visa mått":::

4. Sedan kan du välja ett mått i listan över tillgängliga mått. Du kan välja mått som är speciella för att begära enheter, lagring, svars tid, tillgänglighet, Cassandra och andra. Mer information om alla tillgängliga mått i den här listan finns i artikeln [mått per kategori](monitor-cosmos-db-reference.md) . I det här exemplet väljer vi **normaliserat ru-förbruknings** mått och **Max** som agg regerings värde.

   Förutom dessa uppgifter kan du också välja **tidsintervallet** och **tids kornig het** för måtten. Som Max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på ditt filter.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Välj ett mått från Azure Portal":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filter för normaliserad förbrukning av begär ande enheter

Du kan också filtrera mått och diagrammet som visas av en speciell **samlings**-, **databasename**-, **PartitionKeyRangeID**-och **region**. Om du vill filtrera måtten väljer du **Lägg till filter** och väljer den obligatoriska egenskapen, till exempel **samlings** namn och motsvarande värde som du är intresse rad av. Diagrammet visar sedan de normaliserade RU-konsumtions enheter som förbrukas för behållaren för den valda perioden.  

Du kan gruppera mått med hjälp av alternativet **Använd delning** . För delade data flödes databaser visar det normaliserade RU-måttet endast data i databasens granularitet, utan visar data per samling. För den delade data flödes databasen ser du inga data när du tillämpar delning efter samlings namn.

Den normaliserade förbruknings måttet för begäran för varje behållare visas enligt följande bild:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Använd filter för normaliserat förbruknings mått för begär ande enhet":::

## <a name="next-steps"></a>Nästa steg

* Övervaka Azure Cosmos DB data med hjälp av [diagnostiska inställningar](cosmosdb-monitor-resource-logs.md) i Azure.
* [Granska Azure Cosmos DB kontroll Plans åtgärder](audit-control-plane-logs.md)
