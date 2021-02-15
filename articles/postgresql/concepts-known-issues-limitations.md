---
title: Kända problem och begränsningar – Azure Database for PostgreSQL-enskild server och flexibel Server (för hands version)
description: Visar en lista över kända problem som kunderna bör vara medvetna om.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106426"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL kända problem och begränsningar

Den här sidan innehåller en lista över kända problem i Azure Database for PostgreSQL som kan påverka ditt program. Det visar även eventuella begränsningar och rekommendationer för att lösa problemet.

## <a name="intelligent-performance---query-store"></a>Intelligenta prestanda – Frågearkivet

Gäller för Azure Database for PostgreSQL-enskild server.

| Distribution | Orsak | Åtgärder|
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Att aktivera server parametern `pg_qs.replace_parameter_placeholders` kan leda till att servern stängs av i sällsynta fall. | Via Azure Portal, avsnittet Server parametrar, aktiverar du parametervärdet `pg_qs.replace_parameter_placeholders` till `OFF` och sparar.   | 

## <a name="server-parameters"></a>Serverparametrar

Gäller för Azure Database for PostgreSQL-enskild server och flexibel Server

| Distribution | Orsak | Åtgärder| 
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Att ändra server parametern `max_locks_per_transaction` till ett högre värde än vad som [rekommenderas](https://www.postgresql.org/docs/11/kernel-resources.html) kan leda till att servern inte kan komma efter en omstart. | Lämna det till standardvärdet (32 eller 64) eller ändra till ett rimligt värde per PostgreSQL- [dokumentation](https://www.postgresql.org/docs/11/kernel-resources.html). <br> <br> Från tjänst sidan arbetar det här för att begränsa det höga värdet baserat på SKU: n.  | 

## <a name="next-steps"></a>Nästa steg
- Se [metod tips](./concepts-query-store-best-practices.md) för Query Store
