---
title: Utöka stödet för SQL Server 2008 & 2008 R2
description: Utöka stödet för SQL Server 2008 och SQL Server 2008 R2 genom att migrera din SQL Server instans till Azure eller köpa utökad support för att hålla instanserna lokalt.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c25adc16d14b4a5fb72ae0103ca05b193b40499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359174"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Utöka stödet för SQL Server 2008 och SQL Server 2008 R2 med Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2008 och SQL Server 2008 R2 har båda nått [slutet av sin support (EOS) livs cykel](https://www.microsoft.com/sql-server/sql-server-2008). Eftersom många kunder fortfarande använder båda versionerna, erbjuder vi flera alternativ för att fortsätta att få support. Du kan migrera dina lokala SQL Server-instanser till Azure Virtual Machines (VM), migrera till Azure SQL Database eller hålla lokala och köpa utökade säkerhets uppdateringar.

Till skillnad från en hanterad instans kräver migreringen till en virtuell Azure-dator inte att programmen certifieras på annat sätt. Till skillnad från att hålla lokala, får du kostnads fria utökade säkerhets korrigeringar genom att migrera till en virtuell Azure-dator.

Resten av den här artikeln innehåller information om hur du migrerar SQL Server-instansen till en virtuell Azure-dator.

Mer information om slut på support alternativ finns i [slutet av supporten](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Etablering

Du betalar per användning **SQL Server 2008 R2 på Windows Server 2008 R2** -avbildningen som är tillgänglig på Azure Marketplace.

Kunder som är på SQL Server 2008 måste antingen själv installera eller uppgradera till SQL Server 2008 R2. På samma sätt måste kunder på Windows Server 2008 antingen distribuera sin virtuella dator från en anpassad virtuell hård disk eller uppgradera till Windows Server 2008 R2.

Avbildningar som distribueras via Azure Marketplace levereras med SQL IaaS-tillägget förinstallerat. SQL IaaS-tillägget är ett krav för flexibel licensiering och automatiserad uppdatering. Kunder som distribuerar självinstallerade virtuella datorer måste installera SQL IaaS-tillägget manuellt. SQL IaaS-tillägget stöds inte på Windows Server 2008.

> [!NOTE]
> Även om SQL Server **skapa** och **Hantera** blad fungerar med SQL Server 2008 R2-avbildningen i Azure Portal, _stöds inte_ följande funktioner: automatisk säkerhets kopiering, Azure Key Vault integrering, R-tjänster och lagrings konfiguration.

## <a name="licensing"></a>Licensiering
SQL Server 2008 R2-distributioner enligt principen betala per användning kan konverteras till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).

Om du vill konvertera en Software Assurance-baserad licens för att betala per användning bör kunderna registreras med [SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md). Efter registreringen blir SQL-licensservern utbytbar mellan Azure Hybrid-förmån och betala per användning.

Självinstallerade SQL Server 2008-eller SQL Server 2008 R2-instanser på en virtuell Azure-dator kan registreras med SQL IaaS agent-tillägget och konvertera sin licens typ till "betala per användning".

## <a name="migration"></a>Migrering
Du kan migrera EOS SQL Server instanser till en virtuell Azure-dator med manuella säkerhets kopierings-/återställnings metoder. Detta är den vanligaste migreringsprocessen från lokala datorer till en virtuell Azure-dator.

### <a name="azure-site-recovery"></a>Azure Site Recovery

För Mass migrering rekommenderar vi tjänsten [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) . Med Azure Site Recovery kan kunder replikera hela den virtuella datorn, inklusive SQL Server från lokala datorer till virtuella Azure-datorer.

SQL Server kräver programkonsekventa Azure Site Recovery ögonblicks bilder för att garantera återställningen. Azure Site Recovery stöder programkonsekventa ögonblicks bilder med ett intervall av minst 1 timme. Det minsta antalet återställnings punkt mål som är möjligt för SQL Server med Azure Site Recovery migreringar är 1 timme. Återställnings tids målet (RTO) är 2 timmar plus SQL Server återställnings tid.

### <a name="database-migration-service"></a>Database Migration Service

[Azure Database migration service](../../../dms/dms-overview.md) är ett alternativ för kunder om de migreras från lokala datorer till en virtuell Azure-dator genom att uppgradera SQL Server till 2012-versionen eller senare.

## <a name="disaster-recovery"></a>Haveriberedskap

Katastrof återställnings lösningar för EOS SQL Server på en virtuell Azure-dator är följande:

- **SQL Server säkerhets kopieringar**: Använd Azure Backup för att skydda din EOS SQL Server 2008 och 2008 R2 mot utpressnings tro Jan, oavsiktlig borttagning och skada med 15 minuters återställnings punkt och tidpunkts återställning. Mer information finns i [den här artikeln](../../../backup/sql-support-matrix.md#scenario-support).
- **Logg överföring**: du kan skapa en logg överförings replik i en annan zon eller Azure-region med kontinuerliga återställningar för att minska RTO. Du måste konfigurera logg överföring manuellt.
- **Azure Site Recovery**: du kan REPLIKERA den virtuella datorn mellan zoner och regioner via Azure Site Recovery replikering. SQL Server kräver programkonsekventa ögonblicks bilder för att garantera återställning i händelse av en katastrof. Azure Site Recovery erbjuder en lägsta återställnings period på 1 timme och en 2-timmars återställnings tid (plus SQL Server återställnings tid) RTO för EOS SQL Server haveri beredskap.

## <a name="security-patching"></a>Säkerhets korrigering

Utökade säkerhets uppdateringar för SQL Server virtuella datorer levereras via Microsoft Update kanaler när SQL Server VM har registrerats med [SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md). Korrigeringsfiler kan hämtas manuellt eller automatiskt.

*Automatisk uppdatering* är aktiverat som standard. Med inställningen Automatisk uppdatering kan Azure korrigera SQL Server och operativsystemet automatiskt. Du kan ange en dag i veckan, tiden och varaktigheten för ett underhålls fönster om SQL Server IaaS-tillägget har installerats. Azure utför uppdateringar under den här underhållsperioden. Den virtuella datorns lokala tid används för underhållsperiodens schema. Mer information finns i [automatiserad uppdatering för SQL Server på Azure Virtual Machines](automated-patching.md).


## <a name="next-steps"></a>Nästa steg

Migrera din SQL Server VM till Azure:

* [Migrera en SQL Server-databas till SQL Server på en virtuell Azure-dator](migrate-to-vm-from-sql-server.md)

Kom igång med SQL Server på Azure Virtual Machines:

* [Skapa en virtuell SQL Server-dator på Azure Portal](sql-vm-create-portal-quickstart.md)

Få svar på vanliga frågor om SQL Server virtuella datorer:

* [Vanliga frågor och svar om SQL Server på Azure Virtual Machines](frequently-asked-questions-faq.md)

Lär dig mer om slut på support alternativ och utökade säkerhets uppdateringar:

* [Slut på support](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)  &  [Utökade säkerhets uppdateringar](/sql/sql-server/end-of-support/sql-server-extended-security-updates)