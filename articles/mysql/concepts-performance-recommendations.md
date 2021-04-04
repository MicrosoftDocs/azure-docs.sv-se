---
title: Prestanda rekommendationer – Azure Database for MySQL
description: Den här artikeln beskriver prestanda rekommendations funktionen i Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1445ad3d6be020326c34d60c555c24c435548fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94537653"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Prestandarekommendationer i Azure Database for MySQL

**Gäller för:** Azure Database for MySQL 5,7, 8,0

Funktionen prestanda rekommendationer analyserar dina databaser för att skapa anpassade förslag för bättre prestanda. För att skapa rekommendationerna tittar analysen på olika databas egenskaper, inklusive schema. Aktivera [frågearkivet](concepts-query-store.md) på servern för att utnyttja funktionen prestanda rekommendationer fullt ut. Om prestanda schema är inaktiverat aktiverar Query Store performance_schema och en delmängd av de prestanda schema instrument som krävs för funktionen. När du har implementerat en prestanda rekommendation bör du testa prestanda för att utvärdera effekterna av ändringarna.

## <a name="permissions"></a>Behörigheter

Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer

Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

Öppna **prestanda rekommendationer** från avsnittet **intelligent prestanda** i meny raden på Azure Portal sidan för MySQL-servern.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Landningssida för prestandarekommendationer":::

Välj **analysera** och välj en databas som kommer att påbörja analysen. Det kan ta flera minuter att slutföra analysen, beroende på din arbets belastning. När analysen är klar, visas ett meddelande i portalen. Analysen utför en djup granskning av din databas. Vi rekommenderar att du utför analyser under perioder med låg belastning.

I fönstret **rekommendationer** visas en lista med rekommendationer om de hittades och det relaterade fråge-ID som skapade rekommendationen. Med fråge-ID kan du använda vyn [MySQL.query_store](concepts-query-store.md#mysqlquery_store) för att lära dig mer om frågan.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Ny sida med prestanda rekommendationer":::

Rekommendationerna tillämpas inte automatiskt. Om du vill tillämpa rekommendationen kopierar du frågetexten och kör den från din valda klient. Kom ihåg att testa och övervaka för att utvärdera rekommendationen.

## <a name="recommendation-types"></a>Rekommendations typer

### <a name="index-recommendations"></a>Index rekommendationer

*Skapa index* rekommendationer föreslå nya index för att påskynda de vanligaste frågorna för körning eller tids krävande frågor i arbets belastningen. Den här rekommendations typen kräver att [query Store](concepts-query-store.md) är aktiverat. Query Store samlar in frågedata och ger detaljerad information om fråge körning och frekvens statistik som används av analysen för att göra rekommendationen.

### <a name="query-recommendations"></a>Fråga rekommendationer

Fråga rekommendationer föreslår optimeringar och skriv om för frågor i arbets belastningen. Genom att identifiera MySQL-frågas anti-mönster och åtgärda dem syntaktiskt kan du förbättra prestanda för tids krävande frågor. Den här rekommendations typen kräver att Query Store är aktiverat. Query Store samlar in frågedata och ger detaljerad information om fråge körning och frekvens statistik som används av analysen för att göra rekommendationen.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for MySQL.