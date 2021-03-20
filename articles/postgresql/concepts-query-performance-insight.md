---
title: Query Performance Insight-Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs Query Performance Insight funktionen i Azure Database for PostgreSQL-enskild server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: babf2c8208732a194184549dfa5ed3228b376d0f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91710268"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Gäller för:** Azure Database for PostgreSQL-enskild server version 9,6, 10, 11

Query Performance Insight hjälper dig att snabbt identifiera vad dina längsta kör frågor är, hur de ändras med tiden och vilka vänte tider som påverkar dem.

## <a name="permissions"></a>Behörigheter
Behörighet för **ägare** eller **deltagare** krävs för att visa texten för frågorna i Query Performance Insight. **Läsaren** kan visa diagram och tabeller men inte frågetext.

## <a name="prerequisites"></a>Förutsättningar
För att Query Performance Insight ska fungera måste data finnas i [frågearkivet](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visa prestanda insikter
På [Query Performance Insight](concepts-query-performance-insight.md)-vyn i Azure-portalen visas visualiseringar av nyckelinformation från Query Store. 

På Portal-sidan på Azure Database for PostgreSQL-servern väljer du **fråga prestanda insikter** under avsnittet **intelligent prestanda** i meny raden.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-landing-page.png" alt-text="Query Performance Insight tids krävande frågor":::

På fliken **tids krävande frågor** visas de fem främsta frågorna med genomsnittlig varaktighet per körning, sammanställt i 15-minuters intervall. Du kan visa fler frågor genom att välja dem i den nedrullningsbara listrutan för **Antal frågor**. Diagrammets färger kan ändras för ett specifikt fråge-ID när du gör detta.

Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster. Du kan också använda ikonerna zooma in och ut för att visa en kortare eller större tids period.

Tabellen nedanför diagrammet innehåller mer information om tids krävande frågor i fönstret.

Välj fliken **Väntestatistik** för att visa motsvarande visualiseringar av vänttillfällen på servern.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png" alt-text="Query Performance Insight väntar på statistik":::

## <a name="considerations"></a>Överväganden
* Query Performance Insight är inte tillgängligt för [läsning av repliker](concepts-read-replicas.md).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for PostgreSQL.


