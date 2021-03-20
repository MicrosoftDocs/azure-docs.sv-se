---
title: Optimera Mass infogningar – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du kan optimera Mass infognings åtgärder på en Azure Database for PostgreSQL-enskild server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4d10f06577738364e3f4a0ea40221d37ebfb31c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86116292"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Optimera Mass infogningar och använda tillfälliga data på en Azure Database for PostgreSQL-enskild server 
Den här artikeln beskriver hur du kan optimera Mass infognings åtgärder och använda tillfälliga data på en Azure Database for PostgreSQL-Server.

## <a name="use-unlogged-tables"></a>Använd ej loggade tabeller
Om du har arbets belastnings åtgärder som involverar tillfälliga data eller lägger till stora data mängder i bulk bör du överväga att använda ej loggade tabeller.

Ej loggade tabeller är en PostgreSQL-funktion som kan användas effektivt för att optimera Mass infogningar. PostgreSQL använder Write-Ahead loggning (WAL). Den ger atomiska och tålighet som standard. Atomicitet, konsekvens, isolering och hållbarhet utgör egenskaperna för syror. 

Infogning i en ej loggad tabell innebär att PostgreSQL infogar utan att skriva till transaktions loggen, som själva är en I/O-åtgärd. Därför är tabellerna betydligt snabbare än vanliga tabeller.

Använd följande alternativ för att skapa en ej loggad tabell:
- Skapa en ny ej loggad tabell med hjälp av syntaxen `CREATE UNLOGGED TABLE <tableName>` .
- Konvertera en befintlig loggad tabell till en ej loggad tabell med hjälp av syntaxen `ALTER TABLE <tableName> SET UNLOGGED` .  

Använd syntaxen om du vill ångra processen `ALTER TABLE <tableName> SET LOGGED` .

## <a name="unlogged-table-tradeoff"></a>Inloggad tabell kompromiss
Ej loggade tabeller är inte krasch säkra. En ej loggad tabell trunkeras automatiskt efter en krasch eller genomgår en ren avstängning. Innehållet i en ej loggad tabell replikeras inte heller till vänte läges servrar. Alla index som skapats i en ej loggad tabell loggas inte automatiskt. När INSERT-åtgärden har slutförts konverterar du tabellen så att den loggas så att infogningen är hållbar.

Vissa kund arbets belastningar har en över cirka 15 procent till 20 procents prestanda förbättringar när ej loggade tabeller användes.

## <a name="next-steps"></a>Nästa steg
Granska din arbets belastning för användning av tillfälliga data och stora Mass infogningar. Se följande PostgreSQL-dokumentation:
 
- [Skapa tabell SQL-kommandon](https://www.postgresql.org/docs/current/static/sql-createtable.html)
