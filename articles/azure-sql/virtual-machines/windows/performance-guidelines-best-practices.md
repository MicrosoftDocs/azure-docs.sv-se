---
title: Rikt linjer för prestanda för SQL Server i Azure | Microsoft Docs
description: Innehåller rikt linjer för att optimera SQL Server prestanda i Microsoft Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ba30436b363353ad183396e07111b33ca912dbf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737446"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Prestandariktlinjer för SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller rikt linjer för att optimera SQL Server prestanda i Microsoft Azure Virtual Machines.

## <a name="overview"></a>Översikt

När du kör SQL Server på Azure-Virtual Machines rekommenderar vi att du fortsätter att använda samma databas prestanda justerings alternativ som gäller för SQL Server i lokala Server miljöer. Prestanda för en relationsdatabas i ett offentligt moln beror dock på många faktorer, till exempel storleken på en virtuell dator och konfigurationen av datadiskar.

[SQL Server avbildningar som har skapats i Azure Portal följer de](sql-vm-create-portal-quickstart.md) allmänna [metod tipsen](storage-configuration.md)för lagrings konfiguration. Efter etableringen bör du överväga att använda andra optimeringar som diskuteras i den här artikeln. Basera dina val på arbets belastningen och kontrol lera genom att testa.

> [!TIP]
> Det finns vanligt vis en kompromiss mellan optimering av kostnader och optimering för prestanda. Den här artikeln fokuserar på att få *bästa möjliga* prestanda för SQL Server på Azure Virtual Machines. Om din arbets belastning är mindre krävande kanske du inte behöver varje optimering i listan nedan. Överväg dina prestanda behov, kostnader och arbets belastnings mönster när du utvärderar dessa rekommendationer.

## <a name="quick-checklist"></a>Snabb check lista

Följande är en snabb check lista för optimala prestanda för SQL Server på Azure Virtual Machines:

| Område | Optimeringar |
| --- | --- |
| [VM-storlek](#vm-size-guidance) | -Använd VM-storlekar med 4 eller fler vCPU som [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)eller [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) eller högre. <br/><br/> -Använd [minnesoptimerade](../../../virtual-machines/sizes-memory.md) storlekar för virtuella datorer för att få bästa möjliga prestanda för SQL Server arbets belastningar. <br/><br/> – [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) -serien, [M-](../../../virtual-machines/m-series.md)och [Mv2-](../../../virtual-machines/mv2-series.md) serien erbjuder optimalt minne-till-vCore-förhållande som krävs för OLTP-arbetsbelastningar. Båda virtuella datorerna i M-serien erbjuder det högsta vCore-förhållandet som krävs för verksamhets kritiska arbets belastningar och är också idealiskt för arbets belastningar för data lager. <br/><br/> – Ett högre vCore-förhållande kan krävas för verksamhets kritiska och informations lager arbets belastningar. <br/><br/> – Utnyttja Azures Marketplace-avbildningar för virtuella datorer som SQL Server inställningar och lagrings alternativ har kon figurer ATS för optimal SQL Server prestanda. <br/><br/> – Samla in mål arbets Belastningens prestanda egenskaper och Använd dem för att fastställa lämplig VM-storlek för ditt företag.|
| [Storage](#storage-guidance) | – För detaljerad testning av SQL Server prestanda i Azure Virtual Machines med TPC-E och TPC_C benchmarks, se bloggen [optimera OLTP-prestanda](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> – Använd [Premium-SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) för bästa pris/prestanda-fördelar. Konfigurera [skrivskyddad cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) för datafiler och ingen cache för logg filen. <br/><br/> – Använd [Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) om mindre än 1 MS-svars tids fördröjning krävs av arbets belastningen. Mer information finns i [migrera till Ultra disk](storage-migrate-to-ultradisk.md) . <br/><br/> – Samla in krav på lagrings fördröjning för SQL Server data, logg och tillfälliga DB-filer genom att [övervaka programmet](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) innan du väljer disk typen. Om < 1-MS-tidssvars tider krävs, använder du Ultra disks, annars använder du Premium SSD. Om låg latens bara krävs för logg filen och inte för datafiler, [etablerar du den Ultra-disken](../../../virtual-machines/disks-enable-ultra-ssd.md) vid nödvändiga IOPS-och data flödes nivåer för logg filen. <br/><br/>  – Standard lagring rekommenderas endast för utvecklings-och test syfte eller för säkerhets kopiering av filer och bör inte användas för produktions arbets belastningar. <br/><br/> -Behåll [lagrings kontot](../../../storage/common/storage-account-create.md) och SQL Server VM i samma region.<br/><br/> -Inaktivera Azure [Geo-redundant lagring](../../../storage/common/storage-redundancy.md) (geo-replikering) på lagrings kontot.  |
| [Diskar](#disks-guidance) | – Använd minst 2 [Premium SSD-diskar](../../../virtual-machines/disks-types.md#premium-ssd) (1 för logg filen och 1 för datafiler). <br/><br/> – För arbets belastningar som kräver < 1-MS IO-fördröjning, aktivera Skriv Accelerator för M-serien och Överväg att använda Ultra SSD diskar för ES och DS-serien. <br/><br/> -Aktivera [skrivskyddad cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching) på diskarna som är värdar för datafilerna.<br/><br/> – Lägg till ytterligare 20% Premium IOPS/data flödes kapacitet än vad arbets belastningen kräver när du [konfigurerar lagring för SQL Server data-, logg-och tempdb-filer](storage-configuration.md) <br/><br/> – Undvik att använda operativ system eller temporära diskar för databas lagring eller loggning.<br/><br/> – Aktivera inte cachelagring på disk (er) som är värd för logg filen.  **Viktigt**: stoppa SQL Server-tjänsten när du ändrar cache-inställningarna för en Azure Virtual Machines-disk.<br/><br/> – Stripa flera Azure-datadiskar för att få större lagrings data flöde.<br/><br/> -Format med dokumenterade fördelnings storlekar. <br/><br/> -Placera TempDB på den lokala SSD- `D:\` enheten för verksamhets kritiska SQL Server arbets belastningar (efter att ha valt rätt storlek på virtuell dator). Om du skapar den virtuella datorn från Azure Portal eller Azures snabb starts mallar och [placerar Temp-databasen på den lokala disken](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)behöver du inte vidta några ytterligare åtgärder. i alla andra fall följer du stegen i bloggen för  [att använda SSD för att lagra tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) för att förhindra problem efter omstarter. Om den lokala enhetens kapacitet inte räcker för den tillfälliga databas storleken ska du placera Temp DB i en lagringspool som är [stripad](../../../virtual-machines/premium-storage-performance.md) på Premium SSD-diskar med [skrivskyddad cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching). |
| [I/O](#io-guidance) |-Aktivera komprimering av databas sidan.<br/><br/> -Aktivera omedelbar fil initiering för datafiler.<br/><br/> – Begränsa den ökande storleken på databasen.<br/><br/> -Inaktivera autokrympning av databasen.<br/><br/> – Flytta alla databaser till data diskar, inklusive system databaser.<br/><br/> – Flytta SQL Server fel logg och spåra fil kataloger till data diskar.<br/><br/> – Konfigurera standard platser för säkerhets kopiering och databas fil.<br/><br/> - [Aktivera låsta sidor i minnet](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> – Utvärdera och tillämpa de [senaste kumulativa uppdateringarna](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) för den installerade versionen av SQL Server. |
| [Funktions-/regionsspecifika](#feature-specific-guidance) | – Säkerhetskopiera direkt till Azure Blob Storage.<br/><br/>-Använd [säkerhets kopior av fil ögonblicks bilder](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) för databaser som är större än 12 TB. <br/><br/>-Använd flera tillfälliga DB-filer, 1 fil per kärna, upp till 8 filer.<br/><br/>– Ange max server minne på 90% eller upp till 50 GB kvar för operativ systemet. <br/><br/>-Aktivera mjuk NUMA. |


<br/>
Mer information om *hur* och *varför* du kan göra dessa optimeringar finns i informationen och rikt linjerna i följande avsnitt.
<br/><br/>

## <a name="getting-started"></a>Komma igång

Om du skapar en ny SQL Server på en virtuell Azure-dator och inte migrerar ett aktuellt käll system, skapar du en ny SQL Server VM utifrån dina leverantörs krav.  Leverantörs kraven för en SQL Server VM är desamma som för det du skulle distribuera lokalt. 

Om du skapar en ny SQL Server VM med ett nytt program som skapats för molnet kan du enkelt ändra din SQL Server VM när dina data-och användnings krav utvecklas.
Starta utvecklings miljöerna med den lägre nivån D-serien, B-serien eller AV2-serien och utveckla din miljö över tid. 

Det rekommenderade minimivärdet för en produktions-OLTP-miljö är 4 vCore, 32 GB minne och ett förhållandet mellan minne och vCore på 8. För nya miljöer börjar du med 4 vCore-datorer och skalar till 8, 16, 32 virtuella kärnor eller mer när dina data-och beräknings krav har ändrats. För OLTP-genomflöde är målet SQL Server virtuella datorer som har 5000 IOPS för varje vCore. 

Använd SQL Server VM Marketplace-avbildningar med lagrings konfigurationen i portalen. Detta gör det enklare att skapa lagringspooler som krävs för att få den storlek, IOPS och data flöde som krävs för dina arbets belastningar. Det är viktigt att välja SQL Server virtuella datorer som har stöd för Premium Storage och Premium Storage-cachelagring. Mer information finns i avsnittet [lagring](#storage-guidance) . 

SQL Server data lager och verksamhets kritiska miljöer behöver ofta skalas bortom 8-till-vCore-förhållandet. För medel stora miljöer kanske du vill välja ett 16-till-minne-förhållande och ett förhållandet 32 kärnor till minne för större data lager miljöer. 

SQL Server data lager miljöer har ofta nytta av parallell bearbetning av större datorer. Av den anledningen är M-serien och Mv2-serien starka alternativ för större data lager miljöer.

## <a name="vm-size-guidance"></a>Vägledning för VM-storlek

Använd vCPU och minnes konfiguration från käll datorn som en bas linje för att migrera en aktuell lokal SQL Server databas till SQL Server på virtuella Azure-datorer. Ta din kärn licens till Azure för att dra nytta av [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) och spara på SQL Server licens kostnader.

**Microsoft rekommenderar en minnes-till-vCore-kvot på 8 som utgångs punkt för produktion SQL Server arbets belastningar.** Mindre kvoter är acceptabla för arbets belastningar som inte är produktions belastningar. 

Välj ett [minnesoptimerade](../../../virtual-machines/sizes-memory.md), [generellt](../../../virtual-machines/sizes-general.md), [lagrings optimerat](../../../virtual-machines/sizes-storage.md)eller [begränsat vCore](../../../virtual-machines/constrained-vcpu.md) storlek för virtuell dator som är mest optimal för SQL Server prestanda baserat på din arbets belastning (OLTP eller informations lager). 

### <a name="memory-optimized"></a>Minnesoptimerad

De [minnesoptimerade storlekarna för virtuella](../../../virtual-machines/sizes-memory.md) datorer är ett primärt mål för SQL Server virtuella datorer och det rekommenderade valet av Microsoft. De minnesoptimerade virtuella datorerna ger starkare minnes-till-CPU-förhållande och cache-alternativ mellan medel och stora. 

#### <a name="m-and-mv2-series"></a>M-och Mv2-serien

[M-serien](../../../virtual-machines/m-series.md) erbjuder vCore-räkningar och-minne för några av de största SQL Server arbets belastningarna.  

[Mv2-serien](../../../virtual-machines/mv2-series.md) har högsta antal vCore och minne och rekommenderas för verksamhets kritiska och informations lager arbets belastningar. Mv2-seriens instanser är minnesoptimerade VM-storlekar som ger oöverträffade data prestanda för att stödja stora minnes databaser och arbets belastningar med ett högt förhållande mellan minne och CPU som är perfekt för Relations databas servrar, stora cacheminnen och minnes intern analys.

[Standard_M64ms](../../../virtual-machines/m-series.md) har ett 28-till-vCore-förhållande till exempel.

Några av funktionerna i M-och Mv2-serien är attraktiva för SQL Server prestanda, till exempel [Premium Storage](../../../virtual-machines/premium-storage-performance.md) och [Premium Storage caching](../../../virtual-machines/premium-storage-performance.md#disk-caching) support, stöd för [mycket disk](../../../virtual-machines/disks-enable-ultra-ssd.md) och [Skriv acceleration](../../../virtual-machines/how-to-enable-write-accelerator.md).

#### <a name="edsv4-series"></a>Edsv4-serien

[Edsv4-serien](../../../virtual-machines/edv4-edsv4-series.md) är utformad för minnes intensiva program. De här virtuella datorerna har en stor lokal Storage SSD-kapacitet, stark lokal disk-IOPS, upp till 504 GiB RAM-minne och förbättrad beräkning jämfört med de tidigare Ev3/Esv3-storlekarna med Gen2-VM: ar. Det finns ett nästan konsekvent vCore-förhållande på 8 på dessa virtuella datorer, vilket är idealiskt för standard SQL Server arbets belastningar. 

Den här VM-serien är idealisk för minnes intensiva företags program och program som drar nytta av låg latens, lokal lagring med hög hastighet.

De virtuella datorerna i Edsv4-serien har stöd för [Premium Storage](../../../virtual-machines/premium-storage-performance.md)och [Premium Storage-cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching).

#### <a name="dsv2-series-11-15"></a>DSv2-serien 11-15

[DSv2-serien 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) har samma minnes-och diskkonfigurationer som tidigare D-serien. Den här serien har ett konsekvent minnes-till-CPU-förhållande på 7 över alla virtuella datorer. 

[DSv2-serien 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) har stöd för [Premium Storage](../../../virtual-machines/premium-storage-performance.md) och [Premium Storage-cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching), vilket rekommenderas för optimala prestanda.

### <a name="general-purpose"></a>Generell användning

Storleken på den [virtuella datorn för generell användning](../../../virtual-machines/sizes-general.md) är utformad för att tillhandahålla balanserade vCore-förhållande för mindre ingångs nivå arbets belastningar som utveckling och testning, webb servrar och mindre databas servrar. 

På grund av de mindre förhållandet mellan vCore och de flesta virtuella datorer är det viktigt att noggrant övervaka minnesbaserade prestanda räknare för att säkerställa att SQL Server kan hämta det minne som krävs för buffertens cacheminne. Mer information finns i [bas linje för minnes prestanda](#memory) . 

Eftersom start rekommendationen för produktions arbets belastningar är en minnes-till-vCore-kvot på 8, är den minsta rekommenderade konfigurationen för en allmän virtuell dator som kör SQL Server 4 vCPU och 32 GB minne. 

#### <a name="ddsv4-series"></a>Ddsv4-serien

[Ddsv4-serien](../../../virtual-machines/ddv4-ddsv4-series.md) erbjuder en rättvis kombination av vCPU, minne och temporär disk, men med mindre stöd för minne till vcore. 

De virtuella Ddsv4-datorerna omfattar lägre svars tid och lokal lagring med högre hastighet.

De här datorerna är idealiska för SQL-och app-distributioner sida vid sida som kräver snabb åtkomst till tillfälliga lagrings-och avdelnings Relations databaser. Det finns ett standard förhållandet mellan vCore och 4 för alla virtuella datorer i den här serien. 

Därför rekommenderar vi att du utnyttjar D8ds_v4 som den virtuella start datorn i den här serien, som har 8 virtuella kärnor och 32 GB minne. Den största datorn är D64ds_v4, som har 64 virtuella kärnor och 256 GB minne.

De virtuella datorerna i [Ddsv4-serien](../../../virtual-machines/ddv4-ddsv4-series.md) har stöd för [Premium Storage](../../../virtual-machines/premium-storage-performance.md) och [Premium Storage-cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> [Ddsv4-serien](../../../virtual-machines/ddv4-ddsv4-series.md) har inte vCore-förhållandet 8 som rekommenderas för SQL Server arbets belastningar. Därför bör du överväga att använda dessa virtuella datorer för mindre program-och utvecklings arbets belastningar.

#### <a name="b-series"></a>B-serien

Storleken på virtuella datorer med [Burstable i B-serien](../../../virtual-machines/sizes-b-series-burstable.md) är idealisk för arbets belastningar som inte behöver konsekventa prestanda, till exempel POC-koncept och mycket små program-och utvecklings servrar. 

De flesta storlekar för virtuella datorer med [burst-serien](../../../virtual-machines/sizes-b-series-burstable.md) har ett vCore-förhållande på 4. Det största av de här datorerna är [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) med 20 virtuella kärnor och 80 GB minne.

Den här serien är unik eftersom apparna har möjlighet att använda **burst** under kontors tid med stöd för stora krediter baserat på dator storlek. 

När krediterna är uttömda återgår den virtuella datorn till bas linjens dator prestanda.

Fördelen med B-serien är de besparingar som du kan uppnå jämfört med andra VM-storlekar i andra serier, särskilt om du behöver processor kraft sparsamt under dagen.

Den här serien stöder [Premium Storage](../../../virtual-machines/premium-storage-performance.md), men stöder **inte** [cachelagring av Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> Den [Burstable B-serien](../../../virtual-machines/sizes-b-series-burstable.md) har inte vCore-förhållandet 8 som rekommenderas för SQL Server-arbetsbelastningar. Därför bör du överväga att använda dessa virtuella datorer för mindre program, webb servrar och utvecklings arbets belastningar.

#### <a name="av2-series"></a>Av2-serien

De virtuella datorerna i [AV2-serien](../../../virtual-machines/av2-series.md) är bäst lämpade för arbets belastningar på ingångs nivå som utveckling och testning, webb servrar för låg trafik, små till medel stora app-databaser och proof-of-Concepts.

Endast [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 virtuella kärnor och 16GBs minne), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuella kärnor och 32GBs) och [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 virtuella kärnor och 64GBs) har ett bra minnes-till-vCore-förhållande på 8 för de tre översta virtuella datorerna. 

De här virtuella datorerna är både lämpliga alternativ för mindre utvecklings-och test SQL Server datorer. 

8-vCore [Standard_A8m_v2](../../../virtual-machines/av2-series.md) kan också vara ett utmärkt alternativ för små program och webb servrar.

> [!NOTE] 
> AV2-serien har inte stöd för Premium Storage, vilket innebär att det inte rekommenderas för produktion SQL Server arbets belastningar även med de virtuella datorer som har en minnes-till-vCore-kvot på 8.

### <a name="storage-optimized"></a>Lagringsoptimerad

De [optimerade VM-storlekarna för lagring](../../../virtual-machines/sizes-storage.md) är för vissa användnings fall. Dessa virtuella datorer är särskilt utformade med optimerade disk data flöde och IO. Den här serien med virtuella datorer är avsedd för stora data scenarier, data lager hantering och stora transaktions databaser. 

#### <a name="lsv2-series"></a>Lsv2-serien

[Lsv2-serien](../../../virtual-machines/lsv2-series.md) innehåller högt data flöde, låg latens och lokal NVMe-lagring. De virtuella datorerna i Lsv2-serien är optimerade för att använda den lokala disken på noden som är ansluten direkt till den virtuella datorn i stället för att använda varaktiga data diskar 

De här virtuella datorerna är starka alternativ för Big data-, data lager-, rapporterings-och ETL-arbetsbelastningar. Det stora genomflödet och IOPs i den lokala NVMe-lagringen är ett användbart användnings fall för bearbetning av filer som kommer att läsas in i databasen och andra scenarier där källdata kan återskapas från käll systemet eller andra databaser, till exempel Azure Blob Storage eller Azure Data Lake. [Lsv2-serien](../../../virtual-machines/lsv2-series.md) Virtuella datorer kan också överföra disk prestanda i upp till 30 minuter i taget.

De här virtuella datorernas storlek från 8 till 80 vCPU med 8 GiB minne per vCPU och för varje 8-virtuella processorer finns 1,92 TB NVMe SSD. Det innebär att den största virtuella datorn för den här serien, [L80s_v2](../../../virtual-machines/lsv2-series.md), det finns 80 vCPU och 640 BIB minne med 10X 1.92 TB för NVMe-lagring.  Det finns ett konsekvent minnes-till-vCore-förhållande på 8 över alla de virtuella datorerna.

NVMe-lagringen är tillfällig innebär att data går förlorade på diskarna om du startar om den virtuella datorn.

Lsv2-och LS-serien stöder [Premium Storage](../../../virtual-machines/premium-storage-performance.md), men inte Premium Storage-cachelagring. Det finns inte stöd för att skapa en lokal cache för att öka IOPs. 

> [!WARNING]
> Lagring av dina datafiler på den tillfälliga NVMe-lagringen kan leda till data förlust när den virtuella datorn frigörs. 

### <a name="constrained-vcores"></a>Begränsade virtuella kärnor

Hög prestanda SQL Server arbets belastningar behöver ofta större mängder minne, IO och data flöde utan att det högre antalet vCore. 

De flesta OLTP-arbetsbelastningar är program databaser som styrs av ett stort antal mindre transaktioner. Med OLTP-arbetsbelastningar är det bara en liten mängd data som läses eller ändras, men volymer med transaktioner som används av användar antal är mycket högre. Det är viktigt att SQL Server-minnet är tillgängligt för cachelagring av planer, lagra nyligen använda data för prestanda och se till att fysiska läsningar kan läsas snabbt i minnet. 

Dessa OLTP-miljöer behöver högre mängd minne, snabb lagring och den I/O-bandbredd som krävs för att utföra optimalt. 

För att upprätthålla den här prestanda nivån utan högre SQL Server licensierings kostnader erbjuder Azure VM-storlekar med [begränsade vCPU antal](../../../virtual-machines/constrained-vcpu.md). 

Detta hjälper dig att kontrol lera licensierings kostnaderna genom att minska de tillgängliga virtuella kärnor samtidigt som du behåller samma minne, lagring och I/O-bandbredd för den överordnade virtuella datorn.

Antalet vCPU kan begränsas till en halv fjärdedel av den ursprungliga storleken på den virtuella datorn. Genom att minska virtuella kärnor som är tillgängliga för den virtuella datorn får du högre förhållandet mellan minne och vCore.

Dessa nya VM-storlekar har ett suffix som anger antalet aktiva virtuella processorer som gör det lättare att identifiera dem. 

Till exempel kräver [M64-32ms](../../../virtual-machines/constrained-vcpu.md) endast licensiering 32 SQL Server virtuella kärnor med minne, IO och data flöde för [M64ms](../../../virtual-machines/m-series.md) och [M64-16Ms](../../../virtual-machines/constrained-vcpu.md) kräver endast licensiering 16 virtuella kärnor.  Även om [M64-16ms](../../../virtual-machines/constrained-vcpu.md) har ett kvartal av SQL Server licens kostnaden för M64ms, kommer beräknings kostnaden för den virtuella datorn att vara densamma.

> [!NOTE] 
> - Medel stora till stora data lager arbets belastningar kan fortfarande dra nytta av [begränsade virtuella vCore-datorer](../../../virtual-machines/constrained-vcpu.md), men arbets belastningar för data lager kännetecknas ofta av färre användare och processer som kan hantera större data mängder med hjälp av fråge planer som körs parallellt. 
> - Beräknings kostnaden, som omfattar operativ Systems licensiering, är densamma som den överordnade virtuella datorn. 

## <a name="storage-guidance"></a>Minnesriktlinjer

För detaljerad testning av SQL Server prestanda på Azure Virtual Machines med TPC-E och TPC-C-benchmarks, se bloggen [optimera OLTP-prestanda](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Azure Blob-cache med Premium-SSD rekommenderas för alla produktions arbets belastningar. 

> [!WARNING]
> Standard hård diskar och SSD har varierande latens och bandbredd och rekommenderas bara för arbets belastningar för utveckling/testning. Arbets belastningar för produktion bör använda Premium-SSD.

Dessutom rekommenderar vi att du skapar ditt Azure Storage-konto i samma data Center som SQL Server virtuella datorer för att minska överförings fördröjningarna. När du skapar ett lagrings konto är det inte säkert att inaktivera geo-replikering som konsekvent Skriv ordning på flera diskar. I stället kan du konfigurera en SQL Server katastrof återställnings teknik mellan två Azure-datacenter. Mer information finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Vägledning för diskar

Det finns tre typer av huvud diskar på virtuella Azure-datorer:

* **OS-disk**: när du skapar en virtuell Azure-dator ansluter plattformen minst en disk (märkt som **C** -enheten) till den virtuella datorn för operativ system disken. Den här disken är en virtuell hård disk som lagras som en sid-BLOB i lagring.
* **Temporär disk**: virtuella Azure-datorer innehåller en annan disk som kallas temporär disk (märkt som **D**: Drive). Det här är en disk på noden som kan användas för scratch Space.
* **Data diskar**: du kan också koppla ytterligare diskar till den virtuella datorn som data diskar och de lagras i lagrings utrymmet som Page blobbar.

I följande avsnitt beskrivs rekommendationer för att använda de olika diskarna.

### <a name="operating-system-disk"></a>Operativsystemdisk

En operativ system disk är en virtuell hård disk som du kan starta och montera som en aktiv version av ett operativ system och som är märkt som **C** -enheten.

Standard principen för cachelagring på operativ system disken är **läsa/skriva**. För prestanda känsliga program rekommenderar vi att du använder data diskar i stället för operativ system disken. Se avsnittet på data diskar nedan.

### <a name="temporary-disk"></a>Tillfällig disk

Den tillfälliga lagrings enheten, märkt som **D** -enheten, är inte beständig i Azure Blob Storage. Lagra inte dina användar databas filer eller loggfiler för användar transaktioner på enheten **D**:.

Placera TempDB på den lokala SSD- `D:\` enheten för verksamhets kritiska SQL Server arbets belastningar (efter att ha valt rätt storlek på virtuell dator). Om du skapar den virtuella datorn från Azure Portal eller Azures snabb starts mallar och [placerar Temp-databasen på den lokala disken](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)behöver du inte vidta några ytterligare åtgärder. i alla andra fall följer du stegen i bloggen för  [att använda SSD för att lagra tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) för att förhindra problem efter omstarter. Om den lokala enhetens kapacitet inte räcker för den tillfälliga databas storleken ska du placera Temp DB i en lagringspool som är [stripad](../../../virtual-machines/premium-storage-performance.md) på Premium SSD-diskar med [skrivskyddad cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching).

För virtuella datorer som stöder Premium-SSD kan du också lagra TempDB på en disk som har stöd för Premium-SSD med Read caching Enabled.


### <a name="data-disks"></a>Datadiskar

* **Använd Premium SSD-diskar för data-och loggfiler**: om du inte använder disk ränder använder du två Premium SSD-diskar där en disk innehåller logg filen och den andra innehåller data. Varje Premium SSD innehåller ett antal IOPS och bandbredd (MB/s) beroende på dess storlek, som visas i artikeln, [Välj en disktyp](../../../virtual-machines/disks-types.md). Om du använder en disk Rands teknik, till exempel lagrings utrymmen, uppnår du optimala prestanda genom att ha två pooler, en för loggfilerna och den andra för datafilerna. Men om du planerar att använda SQL Server-instanser för redundanskluster (FCI), måste du konfigurera en pool eller använda [Premium-filresurser](failover-cluster-instance-premium-file-share-manually-configure.md) i stället.

   > [!TIP]
   > - För test resultat av olika konfigurationer av diskar och arbets belastningar kan du läsa följande blogg inlägg: [rikt linjer för lagrings konfiguration för SQL Server på Azure Virtual Machines](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm).
   > - För uppdrags kritiska prestanda för SQL-servrar som behöver ~ 50 000 IOPS bör du överväga att ersätta 10-P30 diskar med en Ultra SSD. Mer information finns i följande blogg inlägg: [verksamhets kritisk prestanda med Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > När du etablerar en SQL Server VM i portalen har du möjlighet att redigera lagrings konfigurationen. Beroende på din konfiguration konfigurerar Azure en eller flera diskar. Flera diskar kombineras till en enda lagringspool med striping. Både data-och loggfilerna finns tillsammans i den här konfigurationen. Mer information finns i [lagrings konfiguration för SQL Server virtuella datorer](storage-configuration.md).

* **Disk-striping**: för mer data flöde kan du lägga till ytterligare data diskar och använda disk ränder. Om du vill fastställa antalet data diskar måste du analysera antalet IOPS och bandbredd som krävs för dina loggfiler, och för dina data och TempDB-fil (er). Observera att olika storlekar på virtuella datorer har olika begränsningar för antalet IOPs och bandbredd som stöds, se tabellerna på IOPS per [VM-storlek](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Använd följande riktlinjer:

  * För Windows 8/Windows Server 2012 eller senare använder du [lagrings utrymmen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) med följande rikt linjer:

      1. Ange överlagring (stripe-storlek) till 64 KB (65 536 byte) för OLTP-arbetsbelastningar och 256 KB (262 144 byte) för arbets belastningar för data lager för att undvika prestanda påverkan på grund av feljustering av partitionen. Detta måste anges med PowerShell.
      2. Ange kolumn antal = antal fysiska diskar. Använd PowerShell när du konfigurerar fler än 8 diskar (inte Serverhanteraren UI). 

    Följande PowerShell skapar till exempel en ny lagringspool med Överlagrings storleken till 64 KB och antalet kolumner som motsvarar mängden fysisk disk i lagringspoolen:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * För Windows 2008 R2 eller tidigare kan du använda dynamiska diskar (OS Striped-volymer) och stripe-storleken är alltid 64 KB. Det här alternativet är föråldrat från och med Windows 8/Windows Server 2012. Mer information finns i support policyn på [Virtual Disk Service över gång till Windows Storage Management API](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).

  * Om du använder [Lagringsdirigering (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) med [SQL Server kluster instanser för växling vid fel](failover-cluster-instance-storage-spaces-direct-manually-configure.md)måste du konfigurera en enda pool. Även om olika volymer kan skapas på samma pool, kommer alla att dela samma egenskaper, till exempel samma princip för cachelagring.

  * Fastställ antalet diskar som är kopplade till lagringspoolen baserat på dina belastnings förväntningar. Tänk på att olika storlekar på virtuella datorer tillåter olika antal anslutna data diskar. Mer information finns i [storlekar för virtuella datorer](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Om du inte använder Premium-SSD (utvecklings-och test scenarier) är rekommendationen att lägga till det maximala antalet data diskar som stöds av [storleken](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) på den virtuella datorn och använda disk ränder.

* **Princip för cachelagring**: Observera följande rekommendationer för cachelagring av principer beroende på lagrings konfigurationen.

  * Om du använder separata diskar för data-och loggfiler aktiverar du cachelagring på data diskarna som är värdar för dina datafiler och TempDB-datafiler. Detta kan resultera i en betydande prestanda förmån. Aktivera inte cachelagring på disken som innehåller logg filen eftersom detta orsakar en mindre minskning av prestanda.

  * Om du använder disk ränder i en enda lagringspool kommer de flesta arbets belastningarna att ha nytta av cachelagring av läsningar. Om du har separata lagringspooler för logg-och datafilerna aktiverar du enbart cachelagring på lagringspoolen för datafilerna. I vissa tunga Skriv arbets belastningar kan bättre prestanda uppnås utan cachelagring. Detta kan bara bestämmas genom testning.

  * De tidigare rekommendationerna gäller för Premium-SSD. Om du inte använder Premium-SSD ska du inte aktivera cachelagring på några data diskar.

  * Instruktioner för hur du konfigurerar diskcachelagring finns i följande artiklar. För den klassiska (ASM) distributions modellen se: [set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) och [set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Azure Resource Manager distributions modell finns i: [set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) och [set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Stoppa SQL Server-tjänsten när du ändrar cache-inställningen för Azure Virtual Machines disks för att undvika risken för att databasen skadas.

* **Storlek på NTFS-allokeringsenhet**: när du formaterar data disken rekommenderar vi att du använder en storlek på 64 KB-allokeringsenhet för data-och loggfiler samt tempdb. Om TempDB placeras på den temporära disken (D:\ enhet) den prestanda som uppnås genom att använda den här enheten kräver en storlek på 64 KB-allokeringsenhet. 

* **Metod tips för disk hantering**: när du tar bort en data disk eller ändrar dess cachestorlek stoppar du SQL Server tjänsten under ändringen. När cacheinställningar ändras på OS-disken stoppar Azure den virtuella datorn, ändrar cacheplatsen och startar om den virtuella datorn. När cache-inställningarna för en datadisk ändras, stoppas inte den virtuella datorn, men datadisken kopplas bort från den virtuella datorn under ändringen och sedan återkopplas.

  > [!WARNING]
  > Om du inte stoppar SQL Server tjänsten under dessa åtgärder kan databasen skadas.


## <a name="io-guidance"></a>I/O-vägledning

* De bästa resultaten med Premium-SSD uppnås när du parallellisera ditt program och begär Anden. Premium-SSD är utformade för scenarier där IO-ködjup är större än 1, så att du kan se små eller inga prestanda vinster för en enkel tråds seriella begär Anden (även om de är minnes krävande). Detta kan till exempel påverka de enkla test resultaten för prestanda analys verktyg, till exempel SQLIO.

* Överväg att använda [databasens sid komprimering](/sql/relational-databases/data-compression/data-compression) för att förbättra prestanda för i/O-intensiva arbets belastningar. Data komprimeringen kan dock öka processor förbrukningen på databas servern.

* Överväg att aktivera omedelbar fil initiering för att minska den tid som krävs för den första filallokeringen. Om du vill dra nytta av omedelbar fil initiering beviljar du tjänst kontot SQL Server (MSSQLSERVER) med SE_MANAGE_VOLUME_NAME och lägger till det i säkerhets principen **utföra volym underhålls aktiviteter** . Om du använder en SQL Server plattforms avbildning för Azure läggs inte standard tjänst kontot (NT Service\MSSQLSERVER) till i säkerhets principen **utför volym underhålls aktiviteter** . Med andra ord är omedelbar fil initiering inte aktive rad i en SQL Server Azures plattforms avbildning. När du har lagt till SQL Server tjänst kontot i säkerhets principen **utföra volym underhålls aktiviteter** startar du om SQL Server tjänsten. Det kan finnas säkerhets överväganden för att använda den här funktionen. Mer information finns i [databas filens initiering](/sql/relational-databases/databases/database-instant-file-initialization).

* Tänk på att **autoväxer** enbart anses vara en katastrof för oväntad tillväxt. Hantera inte dina data och logga in på en dags-till-dags-basis med automatisk tillväxt. Om automatisk utökning används kan du i förväg förstora filen med storleks växeln.

* Se till att **autokrympning** är inaktiverat för att undvika onödiga kostnader som kan påverka prestanda negativt.

* Flytta alla databaser till data diskar, inklusive system databaser. Mer information finns i [Flytta system databaser](/sql/relational-databases/databases/move-system-databases).

* Flytta SQL Server fel logg och spåra fil kataloger till data diskar. Det kan du göra i Konfigurationshanteraren för SQL Server genom att högerklicka på SQL Server instansen och välja egenskaper. Inställningarna för fel logg och spårnings fil kan ändras på fliken **Start parametrar** . Katalogen dump anges på fliken **Avancerat** . Följande skärm bild visar var du ska leta efter start parametern för fel loggen.

    ![Skärm bild för SQL-logg](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Konfigurera standard platser för säkerhets kopiering och databas fil. Använd rekommendationerna i den här artikeln och gör ändringarna i fönstret Server egenskaper. Instruktioner finns i [Visa eller ändra standard platserna för data-och loggfiler (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files). Följande skärm bild visar var du kan göra dessa ändringar.

    ![SQL-datalogg-och säkerhets kopierings filer](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Aktivera låsta sidor för att minska IO-och växlings aktiviteter. Mer information finns i [Aktivera alternativet Lås sidor i minnet (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* Om du kör SQL Server 2012 installerar du Service Pack 1 kumulativ uppdatering 10. Uppdateringen innehåller en korrigering av dåliga prestanda för I/O när du kör Välj i tillfälligt tabell uttryck i SQL Server 2012. Mer information finns i den här [artikeln i kunskaps basen](https://support.microsoft.com/kb/2958012).

* Överväg att komprimera datafiler när du överför in/ut ur Azure.

## <a name="feature-specific-guidance"></a>Funktions bestämd vägledning

Vissa distributioner kan få ytterligare prestanda för delar med mer avancerade konfigurations tekniker. I följande lista beskrivs några SQL Server funktioner som kan hjälpa dig att uppnå bättre prestanda:

### <a name="back-up-to-azure-storage"></a>Säkerhetskopiera till Azure Storage
När du säkerhetskopierar SQL Server som körs i Azure Virtual Machines kan du använda [SQL Server säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url). Den här funktionen är tillgänglig från och med SQL Server 2012 SP1-CU2 och rekommenderas för säkerhets kopiering till anslutna data diskar. När du säkerhetskopierar/återställer till/från Azure Storage följer du rekommendationerna som anges i [SQL Server säkerhetskopiera till URL-rekommenderade metoder och fel sökning och återställning från säkerhets kopior som lagras i Azure Storage](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting). Du kan också automatisera säkerhets kopieringarna med [Automatisk säkerhets kopiering för SQL Server på Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Innan du SQL Server 2012 kan du använda [SQL Server säkerhets kopiering till Azure-verktyget](https://www.microsoft.com/download/details.aspx?id=40740). Det här verktyget kan hjälpa till att öka säkerhets kopieringen genom att använda flera säkerhets kopierings stripe-mål.

### <a name="sql-server-data-files-in-azure"></a>SQL Server datafiler i Azure

Den här nya funktionen [SQL Server datafiler i Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)är tillgänglig från och med SQL Server 2014. Att köra SQL Server med datafiler i Azure visar jämförbara prestanda egenskaper som att använda Azure Data disks.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instans av kluster för växling vid fel och lagrings utrymmen

Om du använder lagrings utrymmen, när du lägger till noder i klustret på **bekräftelse** sidan, avmarkerar du kryss rutan **Lägg till alla tillgängliga lagrings enheter i klustret**. 

![Avmarkera berättigat lagrings utrymme](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Om du använder lagrings utrymmen och inte avmarkerar **Lägg till alla tillgängliga lagrings enheter i klustret**, kopplar Windows bort de virtuella diskarna under kluster processen. Därför visas de inte i disk hanteraren eller i Utforskaren förrän lagrings utrymmena har tagits bort från klustret och återkopplas med hjälp av PowerShell. Lagrings utrymmen grupper flera diskar i lagringspooler. Mer information finns i [lagrings utrymmen](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Flera instanser 

Tänk på följande rekommendationer när du distribuerar flera SQL Server-instanser till en enda virtuell dator: 

- Ange det maximala Server minnet för varje SQL Server instans, och se till att det finns minne kvar för operativ systemet. Se till att uppdatera minnes begränsningarna för SQL Server instanserna om du ändrar hur mycket minne som allokeras till den virtuella datorn. 
- Ha separata LUN för data, loggar och TempDB eftersom de har olika arbets belastnings mönster och du inte vill att de ska påverka varandra. 
- Testa din miljö noggrant under tung produktions-liknande arbets belastningar för att säkerställa att den kan hantera högsta arbets belastnings kapacitet inom din program service avtal. 

Tecken på överbelastade system kan inkludera, men är inte begränsade till, arbets trådens överbelastning, långsamma svars tider och/eller stoppade dispatcher system minne. 



## <a name="collect-performance-baseline"></a>Samla in prestanda bas linje

Om du vill ha en mer metod kan du samla in prestanda räknare med PerfMon/LogMan och Capture SQL Server vänta statistik för att bättre förstå allmänna belastningar och potentiella Flask halsar i käll miljön. 

Börja med att samla in processor, minne, [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [data flöde](../../../virtual-machines/premium-storage-performance.md#throughput)och [svars tid](../../../virtual-machines/premium-storage-performance.md#latency) för käll arbets belastningen vid hög belastnings tider enligt [Check listan för program prestanda](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist). 

Samla in data under perioder med hög belastning, till exempel arbets belastningar under din normala arbets dag, men även andra hög belastnings processer, till exempel arbets belastningen på arbets dagen och eventuella ETL-arbetsbelastningar. Överväg att skala upp dina resurser för atypically stora arbets belastningar, t. ex. slut på kvartals bearbetning, och skala sedan klart när arbets belastningen har slutförts. 

Använd prestanda analysen för att välja den [VM-storlek](../../../virtual-machines/sizes-memory.md) som kan skalas till arbets Belastningens prestanda krav.


### <a name="iops-and-throughput"></a>IOPS och data flöde

SQL Server prestanda beror kraftigt på i/O-undersystemet. Om din databas inte passar in i fysiskt minne, SQL Server ständigt databas sidor in och ut ur bufferten. Datafilerna för SQL Server bör behandlas på olika sätt. Åtkomst till loggfiler är sekventiella, förutom när en transaktion måste återställas där datafiler, inklusive TempDB, är slumpmässigt använda. Om du har ett långsamt I/O-undersystem kan användarna uppleva prestanda problem som långsamma svars tider och uppgifter som inte slutförs på grund av timeout. 

Virtuella Azure Marketplace-datorer har loggfiler på en fysisk disk som är separat från datafilerna som standard. Antalet TempDB-datafiler och storlek uppfyller bästa praxis och är riktade till den tillfälliga D:/ enhet.. 

Följande PerfMon-räknare kan hjälpa dig att verifiera det IO-flöde som krävs av din SQL Server: 
* **\LogicalDisk\Disk läsningar/s** (Läs-och skriv-IOPS)
* **\LogicalDisk\Disk skrivningar/SEK** (Läs-och skriv-IOPS) 
* **\LogicalDisk\Disk byte/s** (data flödes krav för data-, logg-och tempdb-filer)

Använd IOPS-och data flödes krav på högsta nivå för att utvärdera VM-storlekar som matchar kapaciteten från dina mått. 

Om din arbets belastning kräver 20 K Läs IOPS och 10K Skriv-IOPS, kan du antingen välja E16s_v3 (med upp till 32 kB cache-lagrad och 25600 Uncached IOPS) eller M16_s (med upp till 20 K cachelagrat och 10 000 Uncached IOPS) med 2 P30 diskar stripas med hjälp av lagrings utrymmen. 

Se till att förstå både genomflödet och IOPS-krav för arbets belastningen eftersom de virtuella datorerna har olika skalnings gränser för IOPS och data flöde.

### <a name="memory"></a>Minne

Spåra både det externa minnet som används av operativ systemet och det minne som används internt av SQL Server. Att identifiera trycket för en komponent bidrar till att öka storleken på virtuella datorer och identifiera möjligheter för justering. 

Följande PerfMon-räknare kan hjälpa dig att validera minnes hälsan för en SQL Server virtuell dator: 
* [\Memory\Available MB](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: minne Manager\Target server minne (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: minne Manager\Total server minne (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Lazy skrivningar/s](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer bufferthanteraren Life förväntad](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>Beräkning/bearbetning

Compute i Azure hanteras annorlunda än lokalt. Lokala servrar är byggda för de senaste flera åren utan en uppgradering på grund av hanterings kostnader och kostnader för att förvärva ny maskin vara. Virtualiseringen minskar vissa av dessa problem, men programmen är optimerade för att dra största möjliga nytta av den underliggande maskin varan, vilket innebär att en betydande förändring av resursanvändningen kräver en ombalansering av hela den fysiska miljön. 

Detta är inte en utmaning i Azure där en ny virtuell dator på en annan maskin varu serie, och även i en annan region, är lätt att uppnå. 

I Azure vill du dra nytta av så mycket av de virtuella dator resurserna som möjligt, och därför bör virtuella Azure-datorer konfigureras för att hålla den genomsnittliga CPU: n så hög som möjligt utan att arbets belastningen påverkas. 

Följande PerfMon-räknare kan hjälpa till att verifiera beräknings hälsan för en SQL Server virtuell dator:
* **\Processor information (_Total) \% processor tid**
* **\Process (Sqlservr) \% processor tid**

> [!NOTE] 
> Vi rekommenderar att du strävar efter att använda 80% av din beräkning, med toppar över 90% men inte till 100% under en varaktig tids period. För det grundläggande vill du bara tillhandahålla den beräkning som krävs för programmet och sedan planera för att skala upp eller ned efter behov. 

## <a name="next-steps"></a>Nästa steg

Rekommenderade säkerhets metoder finns i [säkerhets överväganden för SQL Server på Azure Virtual Machines](security-considerations-best-practices.md).

Granska andra SQL Server virtuella dator artiklar på [SQL Server på Azure Virtual Machines-översikt](sql-server-on-azure-vm-iaas-what-is-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).