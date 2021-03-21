---
title: Query Performance Insight-Azure Database for MariaDB
description: I den här artikeln beskrivs Query Performance Insight funktionen i Azure Database for MariaDB
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: b643ba3305736480e06d7c10d594b2271839038f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664239"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Information om frågeprestanda i Azure Database for MariaDB

**Gäller för:** Azure Database for MariaDB 10,2

Query Performance Insight hjälper dig att snabbt identifiera vad dina längsta kör frågor är, hur de ändras med tiden och vilka vänte tider som påverkar dem.

## <a name="common-scenarios"></a>Vanliga scenarier

### <a name="long-running-queries"></a>Långvariga frågor

- Identifiera de längsta körnings frågorna de senaste X timmarna
- Identifiera de N främsta frågorna som väntar på resurser
 
### <a name="wait-statistics"></a>Vänta med statistik

- Förstå vänte natur för en fråga
- Förstå trender för resurs väntar och där resurs innehåll finns

## <a name="permissions"></a>Behörigheter

Behörighet för **ägare** eller **deltagare** krävs för att visa texten för frågorna i Query Performance Insight. **Läsaren** kan visa diagram och tabeller men inte frågetext.

## <a name="prerequisites"></a>Förutsättningar

För att Query Performance Insight ska fungera måste data finnas i [frågearkivet](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visa prestanda insikter

På [Query Performance Insight](concepts-query-performance-insight.md)-vyn i Azure-portalen visas visualiseringar av nyckelinformation från Query Store.

På Portal-sidan på Azure Database for MariaDB-servern väljer du **query Performance Insight** under avsnittet **intelligent prestanda** i meny raden.

### <a name="long-running-queries"></a>Långvariga frågor

På fliken **tids krävande frågor** visas de 5 främsta frågorna med genomsnittlig varaktighet per körning, sammanställt i 15-minuters intervall. Du kan visa fler frågor genom att välja från List rutan **antal frågor** . Diagrammets färger kan ändras för ett specifikt fråge-ID när du gör detta.

Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster. Du kan också använda ikonerna zooma in och ut för att visa en kortare eller längre tids period.

![Query Performance Insight tids krävande frågor](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Vänta med statistik 

> [!NOTE]
> Väntande statistik är avsedd för fel sökning av problem med frågans prestanda. Vi rekommenderar att du bara aktive ras i fel söknings syfte. <br>Om du får fel meddelandet i Azure Portal "*problemet upptäcktes för" Microsoft. DBforMariaDB "; Det går inte att slutföra begäran. Kontakta supporten med den här informationen om problemet fortsätter eller är oväntat.*" Använd en kortare tids period när du visar väntande statistik.

Vänta med statistik är en vy över de väntande händelser som inträffar när en speciell fråga körs. Läs mer om händelse typerna wait i [MySQL Engine-dokumentationen](https://go.microsoft.com/fwlink/?linkid=2098206).

Välj fliken **Väntestatistik** för att visa motsvarande visualiseringar av vänttillfällen på servern.

Frågor som visas i vyn vänta med statistik grupperas efter de frågor som uppvisar störst vänte tid under det angivna tidsintervallet.

![Query Performance Insight väntar på statistik](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for MariaDB.