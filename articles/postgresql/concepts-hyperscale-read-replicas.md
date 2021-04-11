---
title: Läsa repliker – Azure Database for PostgreSQL-Scale (citus)
description: I den här artikeln beskrivs funktionen Läs replik i Azure Database for PostgreSQL-storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cf8c64bf3858f0d3b7d633b94bc7302fbe563f87
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024001"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Läsa repliker i Azure Database for PostgreSQL-storskalig (citus)

> [!IMPORTANT]
> Läs repliker i storskaliga (citus) är för närvarande en för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [för hands versions funktioner för skalning (citus)](hyperscale-preview-features.md).

Med funktionen Läs replik kan du replikera data från en storskalig (citus) Server grupp till en skrivskyddad Server grupp. Repliker uppdateras **asynkront** med postgresql-teknik för fysisk replikering. Du kan replikera från den primära servern till ett obegränsat antal repliker.

Repliker är nya Server grupper som du hanterar liknar citus-servergrupper (Regular-Server grupper). För varje Läs replik debiteras du för den etablerade beräkningen i virtuella kärnor och lagring i GB/månad.

Lär dig hur du [skapar och hanterar repliker](howto-hyperscale-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>När du ska använda en Läs replik

Funktionen Läs replik hjälper till att förbättra prestanda och skalning för Läs intensiva arbets belastningar. Läs arbets belastningar kan isoleras till replikerna, medan Skriv arbets belastningar kan dirigeras till den primära.

Ett vanligt scenario är att låta BI och analytiska arbets belastningar använda Läs repliken som data källa för rapportering.

Eftersom repliker är skrivskyddade kan de inte direkt minska Skriv kapacitets bördan på den primära.

### <a name="considerations"></a>Överväganden

Funktionen är avsedd för scenarier där replikeringens fördröjning är acceptabel och är avsedd för att avlasta frågor. Den är inte avsedd för synkrona replikeringar där replik data förväntas vara uppdaterade. Det kommer att bli en mätbar fördröjning mellan den primära servern och repliken. Fördröjningen kan vara minuter eller till och med timmar beroende på arbets belastningen och fördröjningen mellan den primära och repliken.  Data på repliken kommer slutligen att bli konsekventa med data på den primära. Använd den här funktionen för arbets belastningar som kan hantera denna fördröjning. 

## <a name="create-a-replica"></a>Skapa en replik

När du startar arbets flödet skapa replik skapas en tom citus-Server grupp. Den nya gruppen fylls med de data som fanns på den primära server gruppen. Hur lång tid det tar att skapa beror på mängden data som är primär och tiden sedan den senaste veckovis fullständiga säkerhets kopieringen. Tiden kan vara från några minuter till flera timmar.

Funktionen Läs replik använder PostgreSQL fysisk replikering, inte logisk replikering. Standard läget är strömmande replikering med hjälp av Replikerings-platser.
Vid behov används logg överföring för att komma igång.

Lär dig hur du [skapar en Läs replik i Azure Portal](howto-hyperscale-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Anslut till en replik

När du skapar en replik ärver den inte brand Väggs regler den primära server gruppen. Dessa regler måste konfigureras separat för repliken.

Repliken ärver administratörs kontot ("citus") från den primära server gruppen.
Alla användar konton replikeras till läsa repliker. Du kan bara ansluta till en Läs replik med hjälp av de användar konton som är tillgängliga på den primära servern.

Du kan ansluta till replikens koordinator-nod genom att använda dess värdnamn och ett giltigt användar konto, precis som i en citus-Server grupp.
För en server med namnet **min replik** med administratörs användar namnet **citus** kan du ansluta till koordinator-noden för repliken med hjälp av psql:

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

Ange lösen ordet för användar kontot vid prompten.

## <a name="considerations"></a>Överväganden

I det här avsnittet sammanfattas överväganden om funktionen Läs replik.

### <a name="new-replicas"></a>Nya repliker

En Läs replik skapas som en ny citus-servergrupp (Server grupp). Det går inte att göra en befintlig Server grupp till en replik. Du kan inte skapa en replik av en annan Läs replik.

### <a name="replica-configuration"></a>Replik konfiguration

En replik skapas med hjälp av samma inställningar för beräknings-, lagrings-och Worker-noden som primär. När en replik har skapats kan flera inställningar ändras, inklusive lagrings-och bevarande period för säkerhets kopior. Det går inte att ändra andra inställningar i repliker, till exempel lagrings storlek och antal arbetsnoder.

Kom ihåg att hålla replikerna tillräckligt starka för att hålla de ändringar som kommer från den primära. Till exempel, se till att öka beräknings kraften i repliker om du skalar den på den primära datorn.

Brand Väggs regler och parameter inställningar ärvs inte från den primära servern till repliken när repliken skapas eller efteråt.

### <a name="regions"></a>Regioner

Citus-Server grupper stöder bara replikering av samma region.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar och hanterar Läs repliker i Azure Portal](howto-hyperscale-read-replicas-portal.md).
