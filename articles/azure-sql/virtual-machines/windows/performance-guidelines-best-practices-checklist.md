---
title: 'Check lista: metod tips för prestanda & rikt linjer'
description: Ger en snabb check lista för att granska dina bästa metoder och rikt linjer för att optimera prestanda för dina SQL Server på Azure Virtual Machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 84f2f4f679de80cd9b5fc986d40e084bae8a4cad
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313767"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Check lista: metod tips för prestanda för SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln ger en snabb check lista som en serie bästa praxis och rikt linjer för att optimera prestanda för dina SQL Server på Azure Virtual Machines (VM). 

Utförlig information finns i de andra artiklarna i den här serien: [VM-storlek](performance-guidelines-best-practices-vm-size.md), [lagring](performance-guidelines-best-practices-storage.md), [samla in bas linje](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="overview"></a>Översikt

När du kör SQL Server på Azure Virtual Machines fortsätter du att använda samma databas prestanda justerings alternativ som gäller för SQL Server i lokala Server miljöer. Dock är prestandan för en Relations databas i ett offentligt moln beroende av många faktorer, till exempel storleken på en virtuell dator och konfigurationen av data diskarna.

Det finns vanligt vis en kompromiss mellan optimering av kostnader och optimering för prestanda. Den här prestanda serien för bästa metoder fokuserar på att få *bästa möjliga* prestanda för SQL Server på Azure Virtual Machines. Om din arbets belastning är mindre krävande kanske du inte behöver varje rekommenderad optimering. Överväg dina prestanda behov, kostnader och arbets belastnings mönster när du utvärderar dessa rekommendationer.

## <a name="vm-size"></a>Storlek på virtuell dator

Följande är en snabb check lista över metod tips för VM-storlek för att köra din SQL Server på den virtuella Azure-datorn: 

- Använd VM-storlekar med 4 eller fler vCPU som [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)eller [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) eller högre. 
- Använd [minnesoptimerade](../../../virtual-machines/sizes-memory.md) storlekar på virtuella datorer för att få bästa möjliga prestanda för SQL Server arbets belastningar. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) -serien, [M-](../../../virtual-machines/m-series.md)och [Mv2-](../../../virtual-machines/mv2-series.md) serien erbjuder optimalt minne-till-vCore-förhållande som krävs för OLTP-arbetsbelastningar. Båda virtuella datorerna i M-serien erbjuder det högsta vCore-förhållandet som krävs för verksamhets kritiska arbets belastningar och är också idealiska för arbets belastningar för data lager. 
- Överväg ett högre förhållandet mellan minne och vCore för verksamhets kritiska och informations lager arbets belastningar. 
- Använd Azure Virtual Machine Marketplace-avbildningarna eftersom SQL Server inställningar och lagrings alternativ har kon figurer ATS för optimal SQL Server prestanda. 
- Samla in mål arbets Belastningens prestanda egenskaper och Använd dem för att fastställa lämplig VM-storlek för ditt företag.

Mer information finns i [metod tips](performance-guidelines-best-practices-vm-size.md)för omfattande VM-storlek. 

## <a name="storage"></a>Storage

Följande är en snabb lista över metod tips för lagrings konfiguration för att köra din SQL Server på den virtuella Azure-datorn: 

- Övervaka programmet och [fastställa krav på lagrings bandbredd och latens](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) för SQL Server data, logg och tempdb-filer innan du väljer disk typen. 
- Du kan optimera lagrings prestanda genom att planera för högsta otillgängliga IOPS och använda Datacachen som en prestanda funktion för data läsningar samtidigt som du undviker [capping/begränsning för virtuella datorer och diskar](../../../virtual-machines/premium-storage-performance.md#throttling).
- Placera data, logg och tempdb-filer på separata enheter.
    - För data enheten använder du endast [Premium-P30 och P40-diskar](../../../virtual-machines/disks-types.md#premium-ssd) för att säkerställa tillgängligheten för cache-stöd
    - För logg enhets planen för kapacitets-och test prestanda jämfört med kostnaderna vid utvärdering av [Premium P30-P80-diskar](../../../virtual-machines/disks-types.md#premium-ssd).
      - Om det krävs en lagrings fördröjning i millisekunder, använder du [Azure Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) för transaktions loggen. 
      - För distribution av virtuella datorer i M-serien bör du överväga [Skrivningsaccelerator](../../../virtual-machines/how-to-enable-write-accelerator.md) över att använda Azure Ultra disks.
    - Placera [tempdb](/sql/relational-databases/databases/tempdb-database) på den lokala tillfälliga SSD- `D:\` enheten för de flesta SQL Server arbets belastningar när du har valt den optimala storleken för virtuella datorer. 
      - Om den lokala enhetens kapacitet inte räcker för tempdb bör du överväga att ändra storlek på den virtuella datorn. Mer information finns i [principer för cachelagring av data filer](performance-guidelines-best-practices-storage.md#data-file-caching-policies) .
- Stripa flera Azure-datadiskar med [lagrings utrymmen](/windows-server/storage/storage-spaces/overview) för att öka i/O-bandbredden upp till den virtuella mål DATORns IOPS-och data flödes gränser.
- Ange att [cachelagring av värden](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) ska vara skrivskyddat för data fil diskar.
- Ange [cachelagring av värden](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) till ingen för logg fil diskar.
    - Aktivera inte cachelagring för läsning/skrivning på diskar som innehåller SQL Server filer. 
    - Stoppa alltid SQL Server tjänsten innan du ändrar cache-inställningarna för disken.
- För utvecklings-och test arbets belastningar kan du använda standard lagring. Vi rekommenderar inte att du använder Standard HDD/SDD för produktions arbets belastningar.
- [Kreditbaserade disk burst](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) bör endast beaktas för mindre arbets belastningar för utveckling/testning och avdelnings system.
- Etablera lagrings kontot i samma region som SQL Server VM. 
- Inaktivera Azure Geo-redundant lagring (geo-replikering) och Använd LRS (lokalt redundant lagring) på lagrings kontot.
- Formatera datadisken så att den använder storleken 64 KB för allokeringsenheter för alla datafiler som placerats på en annan enhet än den temporära `D:\` enheten (som har ett standardvärde på 4 KB). SQL Server virtuella datorer som distribueras via Azure Marketplace levereras med data diskar som är formaterade med storlek och överlagring för allokeringsenhet och överlagring för lagringspoolen inställd på 64 KB. 

Mer information finns i [metod tips](performance-guidelines-best-practices-storage.md)för omfattande lagring. 


## <a name="azure--sql-feature-specific"></a>SQL Feature för Azure &

Följande är en snabb genom gång av metod tips för SQL Server och Azure-/regionsspecifika konfigurationer när du kör SQL Server på Azure VM: 

- Registrera dig för [SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md) för att låsa upp ett antal [funktions förmåner](sql-server-iaas-agent-extension-automate-management.md#feature-benefits). 
- Aktivera komprimering av databas sidan.
- Aktivera omedelbar fil initiering för datafiler.
- Begränsa den här databasens ökande storlek.
- Inaktivera autokrympning av databasen.
- Flytta alla databaser till data diskar, inklusive system databaser.
- Flytta SQL Server fel logg och spåra fil kataloger till data diskar.
- Konfigurera standard platser för säkerhets kopiering och databas fil.
- [Aktivera låsta sidor i minnet](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Utvärdera och Använd de [senaste kumulativa uppdateringarna](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) för den installerade versionen av SQL Server.
- Säkerhetskopiera direkt till Azure Blob Storage.
- Använd flera [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) -filer, 1 fil per kärna, upp till 8 filer.



## <a name="next-steps"></a>Nästa steg

Mer information finns i de andra artiklarna i den här serien:
- [VM-storlek](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Samla in bas linje](performance-guidelines-best-practices-collect-baseline.md)

Rekommenderade säkerhets metoder finns i [säkerhets överväganden för SQL Server på Azure Virtual Machines](security-considerations-best-practices.md).

Granska andra SQL Server virtuella dator artiklar på [SQL Server på Azure Virtual Machines-översikt](sql-server-on-azure-vm-iaas-what-is-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).
