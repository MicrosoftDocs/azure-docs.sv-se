---
title: Säkerhetskopiering och återställning – Hyperskala (Citus) – Azure Database for PostgreSQL
description: Skydda data från oavsiktlig skada eller borttagning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 8dfc82ce79f33553be5220b52a1e415d99c26518
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483919"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Säkerhetskopiera och återställa i Azure Database for PostgreSQL – Hyperskala (Citus)

Azure Database for PostgreSQL – Hyperskala (Citus) automatiskt säkerhetskopior av varje nod och lagrar dem i lokalt redundant lagring. Säkerhetskopior kan användas för att återställa Hyperskala (Citus) kluster till en angiven tid. Säkerhetskopiering och återställning är en viktig del i strategin för affärskontinuitet, eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backups"></a>Säkerhetskopior

Minst en gång om dagen Azure Database for PostgreSQL säkerhetskopior av datafiler och databastransaktionsloggen. Med säkerhetskopiorna kan du återställa en server till valfri tidpunkt inom kvarhållningsperioden. (Kvarhållningsperioden är för närvarande 35 dagar för alla kluster.) Alla säkerhetskopior krypteras med AES 256-bitarskryptering.

I Azure-regioner som stöder tillgänglighetszoner lagras ögonblicksbilder av säkerhetskopior i tre tillgänglighetszoner. Så länge minst en tillgänglighetszon är online kan Hyperskala (Citus) klustret återställas.

Säkerhetskopierade filer kan inte exporteras. De kan bara användas för återställningsåtgärder i Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>Lagringskostnad för säkerhetskopiering

Aktuella priser för lagring av säkerhetskopior finns på Azure Database for PostgreSQL – Hyperskala (Citus) [prissättningssidan.](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

## <a name="restore"></a>Återställ

När Azure Database for PostgreSQL säkerhetskopiering av ett Hyperskala (Citus) kluster skapas ett nytt kluster från de ursprungliga noderna. 

> [!IMPORTANT]
>Du kan bara återställa Hyperskala (Citus) i samma prenumeration och resursgrupp och med ett annat klusternamn.


> [!IMPORTANT]
> Borttagna Hyperskala (Citus) kluster kan inte återställas. Om du tar bort klustret tas alla noder som tillhör klustret bort och kan inte återställas. Administratörer kan använda hanteringslås för att skydda klusterresurser efter distributionen från oavsiktlig borttagning eller [oväntade ändringar.](../azure-resource-manager/management/lock-resources.md)

### <a name="point-in-time-restore-pitr"></a>Återställning till tidpunkt (PITR)

Du kan återställa ett kluster till valfri tidpunkt under de senaste 35 dagarna.
Återställning till tidpunkt är användbart i flera scenarier. Till exempel om en användare oavsiktligt raderar data, tar bort en viktig tabell eller databas, eller om en app av misstag skriver över korrekta data med felaktiga data.

Återställningsprocessen skapar ett nytt kluster i samma Azure-region, prenumeration och resursgrupp som originalet. Klustret har den ursprungliga konfigurationen: samma antal noder, antal virtuella kärnor, lagringsstorlek, användarroller, PostgreSQL-version och version av Citus-tillägget.

Brandväggsinställningar och PostgreSQL-serverparametrar bevaras inte från den ursprungliga servergruppen, de återställs till standardvärdena. Brandväggen förhindrar alla anslutningar. Du måste justera inställningarna manuellt efter återställningen. I allmänhet kan du se vår lista över föreslagna [uppgifter efter återställning.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Nästa steg

* Se stegen för att [återställa en servergrupp](howto-hyperscale-restore-portal.md) i Azure Portal.
* Läs mer om [Tillgänglighetszoner i Azure.](../availability-zones/az-overview.md)
