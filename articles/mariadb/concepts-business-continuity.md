---
title: Verksamhets kontinuitet – Azure Database for MariaDB
description: Lär dig mer om verksamhets kontinuitet (tidpunkts återställning, avbrott i Data Center, geo-återställning) när du använder Azure Database for MariaDB-tjänsten.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 6439a63b74b6f627b62741c145f763dc3d9552d3
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98659860"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Förstå affärs kontinuitet i Azure Database for MariaDB

Den här artikeln beskriver de funktioner som Azure Database for MariaDB tillhandahåller för verksamhets kontinuitet och haveri beredskap. Lär dig mer om alternativ för att komma åt från störande händelser som kan leda till data förlust eller orsaka att databasen och programmet blir otillgängligt. Lär dig vad du ska göra när ett användar-eller program fel påverkar data integriteten, att en Azure-region har ett avbrott eller att programmet kräver underhåll.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funktioner som du kan använda för att ge affärs kontinuitet

Azure Database for MariaDB tillhandahåller funktioner för affärs kontinuitet som inkluderar automatiserade säkerhets kopieringar och användarens möjlighet att initiera geo-återställning. Varje har olika egenskaper för den beräknade återställnings tiden (ERT) och potentiell data förlust. När du har förstått de här alternativen kan du välja bland dem och använda dem tillsammans för olika scenarier. När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter störnings händelsen – detta är ditt återställnings tids mål (RTO). Du måste också förstå den maximala mängden senaste data uppdateringar (tidsintervall) som programmet kan tolerera vid återställning efter en störnings händelse – detta är återställnings punkt målet.

I följande tabell jämförs ERT och återställnings punkt för de tillgängliga funktionerna:

| **Kapacitet** | **Basic** | **Generell användning** | **Minnesoptimerad** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Återställning till tidpunkt från säkerhetskopia | Alla återställnings punkter inom kvarhållningsperioden | Alla återställnings punkter inom kvarhållningsperioden | Alla återställnings punkter inom kvarhållningsperioden |
| Geo-återställning från geo-replikerade säkerhets kopieringar | Stöds inte | ERT < 12 h<br/>Återställnings < 1 h | ERT < 12 h<br/>Återställnings < 1 h |

> [!IMPORTANT]
> Om du tar bort servern raderas även alla databaser som finns i servern och kan inte återställas. Det går inte att återställa en borttagen Server.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Återställa en server efter ett användar-eller program fel

Du kan använda tjänstens säkerhets kopieringar för att återställa en server från olika störande händelser. En användare kan oavsiktligt ta bort vissa data, oavsiktligt släppa en viktig tabell eller till och med släppa en hel databas. Ett program kan av misstag skriva över bra data med felaktiga data på grund av ett program fel och så vidare.

Du kan utföra en tidpunkts återställning för att skapa en kopia av servern till en känd lämplig tidpunkt. Den här tidpunkten måste ligga inom den bevarande period för säkerhets kopior som du har konfigurerat för servern. När data har återställts till den nya servern kan du antingen ersätta den ursprungliga servern med den nyligen återställda servern eller kopiera nödvändiga data från den återställda servern till den ursprungliga servern.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Återställa från ett Azure regional Data Center-avbrott

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott inträffar kan det orsaka ett verksamhets avbrott som bara tar några minuter, men som kan ha varit under några timmar.

Ett alternativ är att vänta tills servern kommer tillbaka online när avbrott i data centret är över. Detta fungerar för program som kan ge servern att vara offline under en viss tids period, till exempel en utvecklings miljö. När data Center har ett avbrott vet du inte hur lång tid det tar för avbrottet, så det här alternativet fungerar bara om du inte behöver servern en stund.

Det andra alternativet är att använda Azure Database for MariaDB ' geo-Restore-funktionen som återställer servern med geo-redundanta säkerhets kopieringar. Dessa säkerhets kopior är tillgängliga även när den region som servern finns i är offline. Du kan återställa från dessa säkerhets kopior till en annan region och ta servern online igen.

> [!IMPORTANT]
> Geo-återställning är bara möjlig om du har upprättat servern med Geo-redundant lagring av säkerhets kopior.

## <a name="cross-region-read-replicas"></a>Läs repliker i flera regioner

Du kan använda en oberoende region för att läsa och förbättra verksamhets kontinuiteten och Disaster Recovery-planeringen. Läs repliker uppdateras asynkront med hjälp av MariaDB-teknik för binär replikering. Lär dig mer om Läs repliker, tillgängliga regioner och hur du växlar över från [artikeln Läs repliker](concepts-read-replicas.md). 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
### <a name="where-does-azure-database-for-mariadb-store-customer-data"></a>Var lagrar Azure Database for MariaDB kund information?
Som standard flyttar Azure Database for MariaDB eller lagrar inte kund information från den region som den distribueras i. Kunder kan dock välja att aktivera [geo-redundanta säkerhets kopieringar](concepts-backup.md#backup-redundancy-options) eller skapa en [skrivskyddad replik i flera regioner](concepts-read-replicas.md#cross-region-replication) för att lagra data i en annan region.

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [automatiska säkerhets kopieringarna i Azure Database for MariaDB](concepts-backup.md).
- Lär dig hur du återställer med hjälp [av Azure Portal](howto-restore-server-portal.md) eller [Azure CLI](howto-restore-server-cli.md).
- Läs mer om att [läsa repliker i Azure Database for MariaDB](concepts-read-replicas.md).