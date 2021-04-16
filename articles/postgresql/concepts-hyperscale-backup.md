---
title: Säkerhetskopiering och återställning – Hyperskala (Citus) – Azure Database for PostgreSQL
description: Skydda data från oavsiktlig skada eller borttagning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520181"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Säkerhetskopiera och återställa i Azure Database for PostgreSQL – Hyperskala (Citus)

Azure Database for PostgreSQL – Hyperskala (Citus) automatiskt säkerhetskopior av varje nod och lagrar dem i lokalt redundant lagring. Säkerhetskopior kan användas för att återställa din Hyperskala (Citus) servergrupp till en angiven tid.
Säkerhetskopiering och återställning är en viktig del i strategin för affärskontinuitet, eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backups"></a>Säkerhetskopior

Minst en gång om dagen Azure Database for PostgreSQL säkerhetskopior av datafiler och databastransaktionsloggen. Med säkerhetskopiorna kan du återställa en server till valfri tidpunkt inom kvarhållningsperioden. (Kvarhållningsperioden är för närvarande 35 dagar för alla servergrupper.) Alla säkerhetskopior krypteras med AES 256-bitarskryptering.

I Azure-regioner som stöder tillgänglighetszoner lagras ögonblicksbilder av säkerhetskopior i tre tillgänglighetszoner. Så länge minst en tillgänglighetszon är online kan Hyperskala (Citus) servergruppen återställas.

Säkerhetskopierade filer kan inte exporteras. De kan bara användas för återställningsåtgärder i Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>Lagringskostnad för säkerhetskopiering

Aktuella priser för lagring av säkerhetskopior finns på Azure Database for PostgreSQL – Hyperskala (Citus) [prissättningssidan.](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)

## <a name="restore"></a>Återställ

Du kan återställa en Hyperskala (Citus) servergrupp till valfri tidpunkt under de senaste 35 dagarna.  Återställning till tidpunkt är användbart i flera scenarier. Till exempel om en användare oavsiktligt raderar data, tar bort en viktig tabell eller databas, eller om en app av misstag skriver över korrekta data med felaktiga data.

> [!IMPORTANT]
> Borttagna Hyperskala (Citus) servergrupper kan inte återställas. Om du tar bort servergruppen tas alla noder som tillhör servergruppen bort och kan inte återställas. Administratörer kan använda hanteringslås för att skydda servergruppresurser efter distributionen från oavsiktlig borttagning eller [oväntade ändringar.](../azure-resource-manager/management/lock-resources.md)

Återställningsprocessen skapar en ny servergrupp i samma Azure-region, prenumeration och resursgrupp som den ursprungliga. Servergruppen har den ursprungliga konfigurationen: samma antal noder, antal virtuella kärnor, lagringsstorlek, användarroller, PostgreSQL-version och version av Citus-tillägget.

Brandväggsinställningar och PostgreSQL-serverparametrar bevaras inte från den ursprungliga servergruppen, de återställs till standardvärdena. Brandväggen förhindrar alla anslutningar. Du måste justera inställningarna manuellt efter återställningen. I allmänhet kan du se vår lista över föreslagna [åtgärder efter återställning.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Nästa steg

* Se stegen för att [återställa en servergrupp](howto-hyperscale-restore-portal.md) i Azure Portal.
* Läs mer om [Tillgänglighetszoner i Azure.](../availability-zones/az-overview.md)
