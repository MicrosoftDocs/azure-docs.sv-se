---
title: Optimera fråga statistik samling – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du kan optimera insamling av frågor på en Azure Database for PostgreSQL-enskild server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bc731f6f6a5a60bce0851bf8fe5874f7149f3899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90901461"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Optimera frågan statistik samling på en Azure Database for PostgreSQL-enskild server
I den här artikeln beskrivs hur du optimerar frågans statistik samling på en Azure Database for PostgreSQL-Server.

## <a name="use-pg_stats_statements"></a>Använd pg_stats_statements
**Pg_stat_statements** är ett postgresql-tillägg som är aktiverat som standard i Azure Database for PostgreSQL. Tillägget är ett sätt att spåra körnings statistik för alla SQL-uttryck som körs av en server. Den här modulen hookar i varje frågekörning och levereras med en icke-trivial prestanda kostnad. När du aktiverar **pg_stat_statements** tvingar det att skriva text till filer på disk.

Om du har unika frågor med lång frågetext eller om du inte aktivt övervakar **pg_stat_statements** kan du inaktivera **pg_stat_statements** för bästa prestanda. Det gör du genom att ändra inställningen till `pg_stat_statements.track = NONE` .

Vissa kund arbets belastningar har sett upp till en 50 procents prestanda förbättring när **pg_stat_statements** har inaktiverats. Den kompromiss som du gör när du inaktiverar pg_stat_statements är möjligheten att felsöka prestanda problem.

Så här anger du `pg_stat_statements.track = NONE` :

- I Azure Portal går du till [resurs hanterings sidan för postgresql och väljer bladet Server parametrar](howto-configure-server-parameters-using-portal.md).

  :::image type="content" source="./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png" alt-text="PostgreSQL Server parameter blad":::

- Använd [Azure CLI](howto-configure-server-parameters-using-cli.md) -AZ postgres-serverkonfigurationen `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE` .

## <a name="use-the-query-store"></a>Använda Frågearkivet 
Funktionen [query Store](concepts-query-store.md) i Azure Database for PostgreSQL ger en mer effektiv metod för att spåra statistik för frågor. Vi rekommenderar den här funktionen som ett alternativ till att använda *pg_stats_statements*. 

## <a name="next-steps"></a>Nästa steg
Överväg att ange `pg_stat_statements.track = NONE` i [Azure Portal](howto-configure-server-parameters-using-portal.md) eller med hjälp av [Azure CLI](howto-configure-server-parameters-using-cli.md).

Mer information finns i: 
- [Användnings scenarier för Query Store](concepts-query-store-scenarios.md) 
- [Metod tips för Query Store](concepts-query-store-best-practices.md) 
