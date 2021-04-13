---
title: Azure Advisor för MariaDB
description: Läs mer Azure Advisor rekommendationer för MariaDB.
author: alau-ms
ms.author: alau
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 60a0a5763085de38dbfd4dabcb65dc24b299b29a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368711"
---
# <a name="azure-advisor-for-mariadb"></a>Azure Advisor för MariaDB
Lär dig mer Azure Advisor tillämpas på Azure Database for MariaDB få svar på vanliga frågor.
## <a name="what-is-azure-advisor-for-mariadb"></a>Vad är Azure Advisor för MariaDB?
Systemet Azure Advisor använder telemetri för att utfärda rekommendationer för prestanda och tillförlitlighet för din MariaDB-databas. 

Vissa rekommendationer är gemensamma för flera produkterbjudanden, medan andra rekommendationer baseras på produktspecifika optimeringar.
## <a name="where-can-i-view-my-recommendations"></a>Var kan jag visa mina rekommendationer?
Rekommendationerna finns i **sidofältet** Översikt i Azure Portal. En förhandsgranskning visas som ett banderollmeddelande och information kan visas i avsnittet **Meddelanden** som finns precis under diagrammen för resursanvändning.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Skärmbild av Azure Portal visar en Azure Advisor rekommendation.":::

## <a name="recommendation-types"></a>Rekommendationstyper
Azure Database for MariaDB prioritera följande typer av rekommendationer:
* **Prestanda:** För att förbättra MariaDB-serverns hastighet. Detta inkluderar processoranvändning, minnesbelastning, diskanvändning och produktspecifika serverparametrar. Mer information finns i [Advisor Performance recommendations (Prestandarekommendationer för Advisor).](../advisor/advisor-performance-recommendations.md)
* **Tillförlitlighet:** För att säkerställa och förbättra kontinuiteten i dina affärskritiska databaser. Detta inkluderar rekommendationer för lagringsgräns och anslutningsgräns. Mer information finns i [Advisor Reliability recommendations (Advisors tillförlitlighetsrekommendationer).](../advisor/advisor-high-availability-recommendations.md)
* **Kostnad:** Optimera och minska dina totala Azure-utgifter. Detta inkluderar rekommendationer för rätt storlek för servern. Mer information finns i [Advisor Cost recommendations (Kostnadsrekommendationer för Advisor).](../advisor/advisor-cost-recommendations.md)

## <a name="understanding-your-recommendations"></a>Förstå dina rekommendationer
* **Dagligt** schema: För Azure MariaDB-databaser kontrollerar vi servertelemetri och utfärdar rekommendationer enligt ett dagligt schema. Om du gör en ändring i serverkonfigurationen förblir befintliga rekommendationer synliga tills vi undersöker telemetrin igen följande dag. 
* **Prestandahistorik:** Några av våra rekommendationer baseras på prestandahistorik. Dessa rekommendationer visas bara när en server har varit i drift med samma konfiguration i 7 dagar. På så sätt kan vi identifiera mönster för hög användning (t.ex. hög CPU-aktivitet eller hög anslutningsvolym) under en längre tidsperiod. Om du etablerar en ny server eller ändrar till en ny vCore-konfiguration pausas dessa rekommendationer tillfälligt. Detta förhindrar att äldre telemetri utlöser rekommendationer på en nyligen konfigurerad server. Detta innebär dock också att rekommendationer som baseras på prestandahistorik kanske inte identifieras omedelbart.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Azure Advisor Översikt.](../advisor/advisor-overview.md)
