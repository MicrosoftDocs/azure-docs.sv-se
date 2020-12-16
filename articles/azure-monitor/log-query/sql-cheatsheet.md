---
title: SQL för att Azure Monitor lathund-bladet för logg frågor | Microsoft Docs
description: Hjälp för användare som är bekanta med SQL i skriva logg frågor i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: e9f937323f4e14b5c8c3f30c94bf9d2daef0baea
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606699"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL till Azure Monitor lathund blad för logg frågor 

Tabellen nedan hjälper användare som är bekanta med SQL att lära sig Kusto frågespråk för att skriva logg frågor i Azure Monitor. Titta på T-SQL-kommandot för att lösa vanliga scenarier och motsvarande i en Azure Monitor logg fråga.

## <a name="sql-to-azure-monitor"></a>SQL till Azure Monitor

Beskrivning |SQL-fråga | Azure Monitor logg fråga
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Markera alla data från en tabell |`SELECT * FROM dependencies` |<code>dependencies</code>
Markera vissa kolumner i en tabell |`SELECT name, resultCode FROM dependencies` |<code>dependencies <br>&#124; project name, resultCode</code>
Välj 100 poster från en tabell |`SELECT TOP 100 * FROM dependencies` |<code>dependencies <br>&#124; take 100</code>
Null-utvärdering |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL` |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Sträng jämförelse: likhet |`SELECT * FROM dependencies WHERE name = "abcde"` |<code>dependencies <br>&#124; where name == "abcde"</code>
Sträng jämförelse: del sträng |`SELECT * FROM dependencies WHERE name like "%bcd%"` |<code>dependencies <br>&#124; where name contains "bcd"</code>
Sträng jämförelse: jokertecken |`SELECT * FROM dependencies WHERE name like "abc%"` |<code>dependencies <br>&#124; where name startswith "abc"</code>
Datum jämförelse: senaste 1 dagen |`SELECT * FROM dependencies WHERE timestamp > getdate()-1` |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Datum jämförelse: datum intervall |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'` |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Boolesk jämförelse |`SELECT * FROM dependencies WHERE !(success)` |<code>dependencies <br>&#124; where success == "False" </code>
Sortera |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc` |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct |`SELECT DISTINCT name, type  FROM dependencies` |<code>dependencies <br>&#124; summarize by name, type </code>
Gruppering, agg regering |`SELECT name, AVG(duration) FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Kolumnalias, utöka |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Främsta n poster per mått |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc` |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union |`SELECT * FROM dependencies UNION SELECT * FROM exceptions` |<code>union dependencies, exceptions</code>
Union: med villkor |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5` |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Slå ihop |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Nästa steg

- Gå igenom lektionerna när du [skriver logg frågor i Azure Monitor](get-started-queries.md).
