---
title: Azure Advisor för PostgreSQL
description: Lär dig mer om Azure Advisor rekommendationer för PostgreSQL.
author: alau-ms
ms.author: alau
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 9d736fe77e41b80abaf76d13e918b7d51bde09c0
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251039"
---
# <a name="azure-advisor-for-postgresql"></a>Azure Advisor för PostgreSQL
Lär dig mer om hur Azure Advisor tillämpas på Azure Database for PostgreSQL och få svar på vanliga frågor.
## <a name="what-is-azure-advisor-for-postgresql"></a>Vad är Azure Advisor för PostgreSQL?
Azure Advisor systemet använder telemetri för att utfärda rekommendationer för prestanda och tillförlitlighet för PostgreSQL-databasen. Rekommendationer för Advisor delas mellan våra PostgreSQL-databas erbjudanden:
* Azure Database for PostgreSQL – enskild server
* Azure Database for PostgreSQL-flexibel Server
* Azure Database for PostgreSQL – Hyperskala (Citus)

Vissa rekommendationer är gemensamma för flera produkt erbjudanden, medan andra rekommendationer baseras på produktspecifika optimeringar.
## <a name="where-can-i-view-my-recommendations"></a>Var kan jag se mina rekommendationer?
Rekommendationerna är tillgängliga från **översikts** navigerings panelen i Azure Portal. En förhands granskning visas som ett informations meddelande och information kan visas i avsnittet **meddelanden** som finns under diagrammet resursanvändning.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Skärm bild av Azure Portal som visar en Azure Advisor rekommendation.":::

## <a name="recommendation-types"></a>Rekommendations typer
Azure Database for PostgreSQL prioritera följande typer av rekommendationer:
* **Prestanda**: för att förbättra postgresql-serverns hastighet. Detta inkluderar processor användning, minnes belastning, anslutningspoolen, disk användning och produktspecifika Server parametrar. Mer information finns i [rekommendationer för Advisor-prestanda](../advisor/advisor-performance-recommendations.md).
* **Tillförlitlighet**: för att säkerställa och förbättra kontinuiteten i verksamhets kritiska databaser. Detta inkluderar lagrings gränser, anslutnings gränser och rekommendationer för storskalig data distribution. Mer information finns i [rekommendationer för Advisor-tillförlitlighet](../advisor/advisor-high-availability-recommendations.md).
* **Kostnad**: för att optimera och minska dina totala Azure-utgifter. Detta inkluderar rekommendationer för Server rätt storlek. Mer information finns i [rekommendationer för Advisor-kostnader](../advisor/advisor-cost-recommendations.md).

## <a name="understanding-your-recommendations"></a>Förstå dina rekommendationer
* **Dags schema**: för Azure postgresql-databaser kontrollerar vi telemetri för servrar och utfärdar rekommendationer enligt ett dagligt schema. Om du gör en ändring i Server konfigurationen förblir befintliga rekommendationer synliga tills vi förnyar telemetri på följande dag. 
* **Prestanda historik**: några av våra rekommendationer baseras på prestanda historik. De här rekommendationerna visas bara när en server har varit i drift med samma konfiguration i sju dagar. På så sätt kan vi identifiera mönster av tung användning (t. ex. hög processor aktivitet eller hög anslutnings volym) under en varaktig tids period. Om du etablerar en ny server eller byter till en ny vCore-konfiguration så pausas dessa rekommendationer tillfälligt. Detta förhindrar att äldre telemetri utlöser rekommendationer på en nyligen konfigurerad server. Det innebär dock också att rekommendationer för prestanda historik inte kan identifieras direkt.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Azure Advisor översikt](../advisor/advisor-overview.md).
