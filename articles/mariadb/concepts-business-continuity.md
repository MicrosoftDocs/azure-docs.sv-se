---
title: Verksamhets kontinuitet – Azure Database for MariaDB
description: Lär dig mer om verksamhets kontinuitet (tidpunkts återställning, avbrott i Data Center, geo-återställning) när du använder Azure Database for MariaDB-tjänsten.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: e4bdbd7dc9a751b6ea332ef760e5f016496c615c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306746"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mariadb"></a>Översikt över affärs kontinuitet med Azure Database for MariaDB

Den här artikeln beskriver de funktioner som Azure Database for MySQL tillhandahåller för verksamhets kontinuitet och haveri beredskap. Lär dig mer om alternativ för att komma åt från störande händelser som kan leda till data förlust eller orsaka att databasen och programmet blir otillgängligt. Lär dig vad du ska göra när ett användar-eller program fel påverkar data integriteten, att en Azure-region har ett avbrott eller att programmet kräver underhåll.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funktioner som du kan använda för att ge affärs kontinuitet

När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter störnings händelsen – detta är ditt återställnings tids mål (RTO). Du måste också förstå den maximala mängden senaste data uppdateringar (tidsintervall) som programmet kan tolerera vid återställning efter en störnings händelse – detta är återställnings punkt målet.

Azure Database for MariaDB tillhandahåller funktioner för verksamhets kontinuitet och haveri beredskap som innehåller geo-redundanta säkerhets kopieringar med möjligheten att initiera geo-återställning och distribuera Läs repliker i en annan region. Var och en har olika egenskaper för återställnings tiden och eventuell data förlust. Med funktionen [geo-återställning](concepts-backup.md) skapas en ny server med hjälp av de säkerhetskopierade data som replikeras från en annan region. Den totala tid det tar att återställa och återställa beror på databasens storlek och mängden loggar som ska återställas. Den totala tiden för att upprätta servern varierar från några minuter till några timmar. Med [Läs repliker](concepts-read-replicas.md)strömmas transaktions loggar från den primära asynkront till repliken. Om det uppstår ett avbrott i den primära databasen på grund av ett fel på zon nivå eller ett fel på regions nivå, är det en kortare RTO och minskad data förlust.

> [!NOTE]
> Fördröjningen mellan den primära och repliken beror på svars tiden mellan platserna, mängden data som ska överföras och viktigast av den primära serverns Skriv arbets belastning. Tung Skriv arbets belastningar kan generera betydande fördröjning. 
>
> På grund av asynkron typ av replikering som används för Read-Replicas **bör de inte** betraktas som en lösning för hög tillgänglighet eftersom den högre lags kan betyda högre RTO och återställnings takt. Endast för arbets belastningar där fördröjningen är mindre genom högsta och låg belastnings tid för arbets belastningen kan läsa repliker fungera som ett alternativ. Annars är Läs repliker avsedda för äkta Läs skala för klara tunga arbets belastningar och för DR-scenarier (haveri beredskap).

I följande tabell jämförs RTO och återställnings punkt i ett **typiskt arbets belastnings** scenario:

| **Kapacitet** | **Basic** | **Generell användning** | **Minnesoptimerad** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Återställning till tidpunkt från säkerhetskopia | Alla återställnings punkter inom kvarhållningsperioden <br/> RTO – varierar <br/>Återställnings < 15 min| Alla återställnings punkter inom kvarhållningsperioden <br/> RTO – varierar <br/>Återställnings < 15 min | Alla återställnings punkter inom kvarhållningsperioden <br/> RTO – varierar <br/>Återställnings < 15 min |
| Geo-återställning från geo-replikerade säkerhets kopieringar | Stöds inte | RTO – varierar <br/>Återställnings < 1 h | RTO – varierar <br/>Återställnings < 1 h |
| Skrivskyddade repliker | RTO-minuter * <br/>Återställnings < 5 min * | RTO-minuter * <br/>Återställnings < 5 min *| RTO-minuter * <br/>Återställnings < 5 min *|

 \* RTO och återställningen **kan vara mycket högre** i vissa fall beroende på olika faktorer, inklusive svars tider mellan platser, mängden data som ska överföras och primärt primär databas Skriv arbets belastning.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Återställa en server efter ett användar-eller program fel

Du kan använda tjänstens säkerhets kopieringar för att återställa en server från olika störande händelser. En användare kan oavsiktligt ta bort vissa data, oavsiktligt släppa en viktig tabell eller till och med släppa en hel databas. Ett program kan av misstag skriva över bra data med felaktiga data på grund av ett program fel och så vidare.

Du kan utföra en tidpunkts återställning för att skapa en kopia av servern till en känd lämplig tidpunkt. Den här tidpunkten måste ligga inom den bevarande period för säkerhets kopior som du har konfigurerat för servern. När data har återställts till den nya servern kan du antingen ersätta den ursprungliga servern med den nyligen återställda servern eller kopiera nödvändiga data från den återställda servern till den ursprungliga servern.

> [!IMPORTANT]
> Borttagna servrar kan bara återställas inom **fem dagar** från borttagningen efter vilken säkerhets kopian tas bort. Säkerhets kopian av databasen kan bara nås och återställas från Azure-prenumerationen som är värd för-servern. Om du vill återställa en släppt Server, se [dokumenterade steg](howto-restore-dropped-server.md). För att skydda server resurser, efter distribution, från oavsiktlig borttagning eller oväntade ändringar, kan administratörer utnyttja [hanterings lås](../azure-resource-manager/management/lock-resources.md).

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Återställa från ett Azure regional Data Center-avbrott

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott inträffar kan det orsaka ett verksamhets avbrott som bara tar några minuter, men som kan ha varit under några timmar.

Ett alternativ är att vänta tills servern kommer tillbaka online när avbrott i data centret är över. Detta fungerar för program som kan ge servern att vara offline under en viss tids period, till exempel en utvecklings miljö. När data Center har ett avbrott vet du inte hur lång tid det tar för avbrottet, så det här alternativet fungerar bara om du inte behöver servern en stund.

## <a name="geo-restore"></a>Geo-återställning

Funktionen geo-Restore återställer servern med geo-redundanta säkerhets kopieringar. Säkerhets kopiorna finns i serverns [kopplade region](../best-practices-availability-paired-regions.md). Dessa säkerhets kopior är tillgängliga även när den region som servern finns i är offline. Du kan återställa från dessa säkerhets kopior till en annan region och ta servern online igen. Lär dig mer om geo-återställning från [artikeln säkerhets kopiering och återställning av begrepp](concepts-backup.md).

> [!IMPORTANT]
> Geo-återställning är bara möjlig om du har upprättat servern med Geo-redundant lagring av säkerhets kopior. Om du vill växla från lokalt redundant till Geo-redundant säkerhets kopiering för en befintlig server måste du ta en dump med mysqldump av din befintliga server och återställa den till en nyskapad server som kon figurer ATS med geo-redundanta säkerhets kopieringar.

## <a name="cross-region-read-replicas"></a>Läs repliker i flera regioner

Du kan använda en oberoende region för att läsa och förbättra verksamhets kontinuiteten och Disaster Recovery-planeringen. Läs repliker uppdateras asynkront med MySQL: s binära logg teknik för replikering. Lär dig mer om Läs repliker, tillgängliga regioner och hur du växlar över från [artikeln Läs repliker](concepts-read-replicas.md). 

## <a name="faq"></a>Vanliga frågor

### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Var lagrar Azure Database for MySQL kund information?
Som standard flyttar Azure Database for MySQL eller lagrar inte kund information från den region som den distribueras i. Kunder kan dock välja att aktivera [geo-redundanta säkerhets kopieringar](concepts-backup.md#backup-redundancy-options) eller skapa en [skrivskyddad replik i flera regioner](concepts-read-replicas.md#cross-region-replication) för att lagra data i en annan region.


## <a name="next-steps"></a>Nästa steg

- Läs mer om de [automatiska säkerhets kopieringarna i Azure Database for MariaDB](concepts-backup.md).
- Lär dig hur du återställer med hjälp [av Azure Portal](howto-restore-server-portal.md) eller [Azure CLI](howto-restore-server-cli.md).
- Läs mer om att [läsa repliker i Azure Database for MariaDB](concepts-read-replicas.md).
