---
title: Säkerhets kopiering och återställning – storskalig (citus) – Azure Database for PostgreSQL
description: Skydda data från oavsiktlig skada eller borttagning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 9656e6ab608665e325ac5f772e06df9ac296c521
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92485383"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Säkerhets kopiering och återställning i Azure Database for PostgreSQL-storskalig (citus)

Azure Database for PostgreSQL – storskalig (citus) skapar automatiskt säkerhets kopior av varje nod och lagrar dem i lokalt redundant lagring. Säkerhets kopieringar kan användas för att återställa ditt citus-kluster till en angiven tid. Säkerhetskopiering och återställning är en viktig del i strategin för affärskontinuitet, eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backups"></a>Säkerhetskopior

Minst en gång per dag tar Azure Database for PostgreSQL ögonblicks bilder av datafiler och databas transaktions loggen. Med säkerhets kopiorna kan du återställa en server till alla tidpunkter inom kvarhållningsperioden. (Kvarhållningsperioden är för närvarande 35 dagar för alla kluster.) Alla säkerhets kopior krypteras med AES 256-bitars kryptering.

I Azure-regioner som har stöd för tillgänglighets zoner lagras ögonblicks bilder av säkerhets kopior i tre tillgänglighets zoner. Så länge minst en tillgänglighets zon är online är citus-klustret återställas.

Det går inte att exportera säkerhets kopior. De kan bara användas för återställnings åtgärder i Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>Reserv lagrings kostnad

För nuvarande prissättning för lagring av säkerhets kopior, se [prissättnings sidan](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)Azure Database for PostgreSQL-citus).

## <a name="restore"></a>Återställ

I Azure Database for PostgreSQL skapar återställer ett citus-kluster ett nytt kluster från de ursprungliga nodernas säkerhets kopior. 

> [!IMPORTANT]
>Du kan bara återställa skalnings klustret (citus) i samma prenumeration och resurs grupp, och med ett annat kluster namn.


> [!IMPORTANT]
> Det går inte att återställa citus-kluster (borttagna storskaliga). Om du tar bort klustret tas alla noder som tillhör klustret bort och kan inte återställas. För att skydda kluster resurser, efter distribution, från oavsiktlig borttagning eller oväntade ändringar, kan administratörer utnyttja [hanterings lås](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore-pitr"></a>Återställning av tidpunkt (PITR)

Du kan återställa ett kluster till en viss tidpunkt inom de senaste 35 dagarna.
Återställning av tidpunkt är användbart i flera scenarier. Till exempel om en användare oavsiktligt raderar data, tar bort en viktig tabell eller databas, eller om en app av misstag skriver över korrekta data med felaktiga data.

Återställnings processen skapar ett nytt kluster i samma Azure-region, prenumeration och resurs grupp som originalet. Klustret har den ursprungliga konfigurationen: samma antal noder, antal virtuella kärnor, lagrings storlek, användar roller, PostgreSQL-version och version av citus-tillägget.

Brand Väggs inställningar och parametrar för PostgreSQL-servern bevaras inte från den ursprungliga Server gruppen, de återställs till standardvärdena. Brand väggen kommer att förhindra alla anslutningar. Du måste justera inställningarna manuellt efter återställningen.

> [!IMPORTANT]
> Du måste öppna en supportbegäran för att kunna utföra återställning vid olika tidpunkter för ditt citus-kluster.

### <a name="post-restore-tasks"></a>Post-restore tasks

Efter en återställning från återställnings metoden bör du göra följande för att få dina användare och program att säkerhetskopiera och köra:

* Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klient program till den nya servern
* Se till att det finns tillräckligt med brand Väggs-och VNet-regler på server nivå för att användarna ska kunna ansluta. De här reglerna kopieras inte från den ursprungliga Server gruppen.
* Justera PostgreSQL-Server parametrarna efter behov. Parametrarna kopieras inte från den ursprungliga Server gruppen.
* Se till att lämpliga inloggningar och behörigheter på databas nivå är på plats
* Konfigurera aviseringar efter behov

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [tillgänglighets zoner i Azure](../availability-zones/az-overview.md).
* Ange [föreslagna aviseringar](./howto-hyperscale-alert-on-metric.md#suggested-alerts) på citus-servergrupper (-Server grupper).