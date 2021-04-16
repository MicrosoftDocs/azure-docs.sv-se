---
title: Resurser för att utveckla Synapse SQL funktioner
description: Utvecklingsbegrepp, designbeslut, rekommendationer och kodningstekniker för Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4d842414d3046692c982ca3203957a96f8a01b37
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377338"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Utforma beslut och kodningstekniker för Synapse SQL funktioner i Azure Synapse Analytics
I den här artikeln hittar du en lista över resurser för dedikerad SQL-pool och serverlösa SQL-poolfunktioner för Synapse SQL. De rekommenderade artiklarna är uppdelade i två avsnitt: Viktiga designbeslut samt tekniker för utveckling och kodning.

Målet med de här artiklarna är att hjälpa dig att utveckla den optimala tekniska metoden för Synapse SQL komponenter inom Azure Synapse Analytics.

## <a name="key-design-decisions"></a>Viktiga designbeslut
Artiklarna nedan visar begrepp och designbeslut för Synapse SQL utveckling:

| Artikel | dedikerad SQL-pool | serverlös SQL-pool |
| ------- | -------- | ------------- |
| [Anslutningar](connect-overview.md)                    | Ja | Ja |
| [Resursklasser och samtidighet](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ja    | Inga |
| [Transaktioner](develop-transactions.md)              | Ja | Inga |
| [Användardefinierade scheman](develop-user-defined-schemas.md) | Ja | Ja |
| [Table distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabelldistribution)                 | Ja | Inga |
| [Table indexes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabellindex)                           | Ja | Inga |
| [Tabellpartitioner](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Ja | Inga |
| [Statistik](develop-tables-statistics.md)            | Ja | Ja |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Ja | Inga |
| [Externa tabeller](develop-tables-external-tables.md) | Ja | Ja |
| [CETAS](develop-tables-cetas.md)                     | Ja | Ja |


## <a name="recommendations"></a>Rekommendationer

Nedan hittar du viktiga artiklar som betonar specifika kodningstekniker, tips och rekommendationer för utveckling:

| Artikel | dedikerad SQL-pool | serverlös SQL-pool |
| ------- | -------- | ------------- |
| [Lagrade procedurer](develop-stored-procedures.md)  | Ja                | Ja                      |
| [Etiketter](develop-label.md)                           | Ja                | Inga                      |
| [Vyer](develop-views.md)                             | Ja                | Ja                     |
| [Temporära tabeller](develop-tables-temporary.md)       | Ja                | Ja                     |
| [Dynamisk SQL](develop-dynamic-sql.md)                 | Ja                | Ja                     |
| [Loopar](develop-loops.md)                         | Ja                | Ja                     |
| [Gruppera efter alternativ](develop-group-by-options.md)       | Ja                | Inga                      |
| [Variabeltilldelning](develop-variable-assignment.md) | Ja                | Ja                     |

## <a name="next-steps"></a>Nästa steg
Mer referensinformation finns i [SQL-pool-T-SQL-instruktioner.](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

