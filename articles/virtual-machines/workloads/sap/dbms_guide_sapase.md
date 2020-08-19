---
title: SAP ASE Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning | Microsoft Docs
description: DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 82dbb73da06097407d91f23d4d372aaa4cc76e99
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510903"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines

I det här dokumentet omfattar flera olika områden att tänka på när du distribuerar SAP-ASE i Azure IaaS. Som ett villkor för det här dokumentet bör du läsa dokument [överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](dbms_guide_general.md) och andra guider i [SAP-arbetsbelastningen i Azure-dokumentationen](./get-started.md). Det här dokumentet beskriver SAP-ASE som körs på Linux och Windows-operativsystem. Den lägsta version som stöds på Azure är SAP ASE 16.0.02 (version 16 support Pack 2). Vi rekommenderar att du distribuerar den senaste versionen av SAP och den senaste korrigerings nivån.  Som minst SAP ASE 16.0.03.07 (version 16 support Pack 3 korrigerings nivå 7) rekommenderas.  Den senaste versionen av SAP finns i [mål ASE 16,0-versions schema och CR List-information](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Ytterligare information om versions stöd med SAP-program eller installations medie platser finns i avsnittet om produkt tillgänglighet för SAP på följande platser:

- [#2134316 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2134316)
- [#1941500 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1941500)
- [#1590719 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1590719)
- [#1973241 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1973241)

Ändra: i hela dokumentationen i och utanför SAP World, refereras namnet på produkten som Sybase ASE eller SAP ASE eller i vissa fall båda. För att hålla konsekvent använder vi namnet **SAP ASE** i den här dokumentationen.

## <a name="operating-system-support"></a>Stöd för operativ system
Matrisen SAP Product Availability innehåller de kombinationer av operativ system och SAP-kernel som stöds för varje SAP-program.  Linux-distributioner SUSE 12. x, SUSE 15. x, Red Hat 7. x stöds fullt ut.  Oracle Linux som operativ system för SAP-ASE stöds inte.  Vi rekommenderar att du använder de senaste Linux-versionerna som är tillgängliga. Windows-kunder bör använda Windows Server 2016 eller Windows Server 2019-versioner.  Äldre versioner av Windows, till exempel Windows 2012, är tekniskt kompatibla, men den senaste versionen av Windows rekommenderas alltid.


## <a name="specifics-to-sap-ase-on-windows"></a>Information om SAP-ASE i Windows
Från och med Microsoft Azure kan du migrera dina befintliga SAP ASE-program till Azure Virtual Machines. Med SAP ASE på en virtuell Azure-dator kan du minska den totala ägande kostnaden för distribution, hantering och underhåll av företags bredd program genom att enkelt migrera programmen till Microsoft Azure. Med SAP ASE på en virtuell Azure-dator kan administratörer och utvecklare fortfarande använda samma utvecklings-och administrations verktyg som finns lokalt.

Microsoft Azure erbjuder flera olika typer av virtuella datorer som gör att du kan köra minsta SAP-system och lagra upp till stora SAP-system och landskap med tusentals användare. SAP-storlek SAPS nummer för de olika SAP-certifierade VM SKU: er finns i [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Dokumentation för att installera SAP ASE i Windows finns i [installations guiden för SAP ASE för Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Lås sidor i minnet är en inställning som förhindrar att SAP ASE-databasens buffert stängs av.  Den här inställningen är användbar för stora upptagna system med mycket minne. Kontakta BC-DB-SYB för mer information. 


## <a name="linux-operating-system-specific-settings"></a>Vissa inställningar för Linux-operativsystem
På virtuella Linux-datorer kör du `saptune` med profil SAP-ASE Linux enorma sidor ska vara aktiverade som standard och kan verifieras med kommandot  

`cat /proc/meminfo` 

Sid storleken är vanligt vis 2048 KB. Mer information finns i artikeln [enorma sidor på Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Rekommendationer för VM och disk struktur för SAP ASE-distributioner

SAP ASE for SAP NetWeaver-program stöds på alla VM-typer som anges i [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533) typiska VM-typer som används för medel stora SAP-ASE databas servrar är Esv3.  Stora databaser i flera terabyte kan utnyttja VM-typer i M-serien. Det kan vara bättre att skriva prestanda för SAP ASE-transaktions logg disk genom att aktivera M-seriens Skrivningsaccelerator. Skrivningsaccelerator bör testas noggrant med SAP-ASE på grund av det sätt som SAP-ASE utför logg skrivningar.  Granska [SAP support note #2816580](../../how-to-enable-write-accelerator.md) och Överväg att köra ett prestanda test.  
Skrivningsaccelerator är endast avsedd för transaktions logg diskar. Cachen på disk nivå ska vara inställd på ingen. Bli inte förvånad om Azure Skrivningsaccelerator inte visar liknande förbättringar som med andra DBMS. Beroende på hur SAP ASE skriver till transaktions loggen kan det bero på att det inte finns någon acceleration av Azure Skrivningsaccelerator.
Separata diskar rekommenderas för data enheter och logg enheter.  System databaserna sybsecurity och `saptools` kräver inte dedikerade diskar och kan placeras på de diskar som innehåller data och logg enheter för SAP-databasen 

![Lagrings konfiguration för SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Fil system, stripe-storlek & IO-balansering 
SAP ASE skriver data sekventiellt till disk lagrings enheter om inget annat konfigureras. Det innebär att en tom SAP ASE-databas med fyra enheter endast skriver data till den första enheten.  De andra disk enheterna kommer bara att skrivas till när den första enheten är full.  Mängden Läs-och skriv-IO till varje SAP ASE-enhet är förmodligen annorlunda. För att balansera disk-i/o över alla tillgängliga Azure-diskar måste du använda antingen Windows Storage Spaces eller Linux LVM2. I Linux rekommenderar vi att du använder XFS File System för att formatera diskarna. LVM stripe-storlek ska testas med ett prestanda test. 128 KB stripe-storlek är en lämplig start punkt. I Windows bör storleken på NTFS-allokeringsenheten (Australien) testas. 64 KB kan användas som ett start värde. 

Vi rekommenderar att du konfigurerar automatisk databas expansion enligt beskrivningen i artikeln [Konfigurera automatisk expansion av databas utrymme i SAP anpassningsbar Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)  och [sap support NOTE #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Exempel på SAP-ASE på virtuella Azure-datorer, disk-och fil system konfigurationer 
I mallarna nedan visas exempel konfigurationer för både Linux och Windows. Innan du bekräftar den virtuella datorn och disk konfigurationen kontrollerar du att kvoterna för nätverks-och lagrings bandbredden för den enskilda virtuella datorn är tillräckliga för att uppfylla affärs kraven. Tänk också på att olika typer av virtuella Azure-datorer har olika högsta antal diskar som kan anslutas till den virtuella datorn. Till exempel har en E4s_v3 VM en gräns på 48 MB/s Storage IO-dataflöde. Om lagrings data flödet som krävs av säkerhets kopierings aktiviteten för databasen kräver mer än 48 MB/SEK kan en större VM-typ med mer data flöde för lagrings bandbredd undvikas. När du konfigurerar Azure Storage måste du också vara medveten om att i synnerhet med [Azure Premium Storage](../../windows/premium-storage-performance.md) kan data flödet och IOPS per GB-kapacitet ändras. Mer information finns i artikeln [vilka disk typer är tillgängliga i Azure?](../../windows/disks-types.md). Kvoterna för vissa typer av virtuella Azure-datorer dokumenteras i artikel [minnet optimerade storlekar för virtuella datorer](../../sizes-memory.md) och artiklar som är länkade till den. 

> [!NOTE]
>  Om ett DBMS-system flyttas från lokal plats till Azure, rekommenderar vi att du övervakar den virtuella datorn och bedömer data flödet CPU, minne, IOPS och lagring. Jämför de högsta värdena som observeras med de kvot gränser för virtuella datorer som beskrivs i artiklarna ovan

Exemplen nedan är avsedda för olika ändamål och kan ändras baserat på enskilda behov. På grund av utformningen av SAP-ASE är antalet data enheter inte lika kritiskt som med andra databaser. Antalet data enheter som beskrivs i det här dokumentet är endast en guide. 

Ett exempel på en konfiguration för en liten SAP ASE DB-server med en databas storlek på mellan 50 GB – 250 GB, till exempel SAP Solution Manager, kan se ut så här:

| Konfiguration | Windows | Linux | Kommentarer |
| --- | --- | --- | --- |
| Typ av virtuell dator | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Accelererat nätverk | Aktivera | Aktivera | ---|
| SAP ASE-version | 16.0.03.07 eller högre | 16.0.03.07 eller högre | --- |
| antal data enheter | 4 | 4 | ---|
| antal logg enheter | 1 | 1 | --- |
| antal temporära enheter | 1 | 1 | mer för SAP BW arbets belastning |
| Operativsystem | Windows Server 2019 | SUSE 12 SP4/15 SP1 eller RHEL 7,6 | --- |
| Disk agg regering | Lagringsutrymmen | LVM2 | --- |
| Filsystem | NTFS | XFS |
| Format block storlek | behöver arbets belastnings testning | behöver arbets belastnings testning | --- |
| # och typen av data diskar | Premium Storage: 2 x P10 (RAID0) | Premium Storage: 2 x P10 (RAID0)| Cache = skrivskyddad |
| # och typen av logg diskar | Premium-lagring: 1 x P20  | Premium-lagring: 1 x P20 | Cache = ingen |
| ASE MaxMemory-parameter | 90% av fysiskt RAM-minne | 90% av fysiskt RAM-minne | förutsätter en enskild instans |
| antal säkerhets kopierings enheter | 4 | 4| --- |
| # och typ av säkerhets kopierings diskar | 1 | 1 | --- |


Ett exempel på en konfiguration för en medels Tor SAP ASE DB-server med en databas storlek på mellan 250 GB – 750 GB, till exempel ett mindre SAP Business Suite-system, kan se ut så här:

| Konfiguration | Windows | Linux | Kommentarer |
| --- | --- | --- | --- |
| Typ av virtuell dator | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Accelererat nätverk | Aktivera | Aktivera | ---|
| SAP ASE-version | 16.0.03.07 eller högre | 16.0.03.07 eller högre | --- |
| antal data enheter | 8 | 8 | ---|
| antal logg enheter | 1 | 1 | --- |
| antal temporära enheter | 1 | 1 | mer för SAP BW arbets belastning |
| Operativsystem | Windows Server 2019 | SUSE 12 SP4/15 SP1 eller RHEL 7,6 | --- |
| Disk agg regering | Lagringsutrymmen | LVM2 | --- |
| Filsystem | NTFS | XFS |
| Format block storlek | behöver arbets belastnings testning | behöver arbets belastnings testning | --- |
| # och typen av data diskar | Premium Storage: 4 x P20 (RAID0) | Premium Storage: 4 x P20 (RAID0)| Cache = skrivskyddad |
| # och typen av logg diskar | Premium-lagring: 1 x P20  | Premium-lagring: 1 x P20 | Cache = ingen |
| ASE MaxMemory-parameter | 90% av fysiskt RAM-minne | 90% av fysiskt RAM-minne | förutsätter en enskild instans |
| antal säkerhets kopierings enheter | 4 | 4| --- |
| # och typ av säkerhets kopierings diskar | 1 | 1 | --- |

Ett exempel på en konfiguration för en liten SAP ASE DB-server med en databas storlek på mellan 750 GB – 2000 GB, till exempel ett större SAP Business Suite-system, kan se ut så här:

| Konfiguration | Windows | Linux | Kommentarer |
| --- | --- | --- | --- |
| Typ av virtuell dator | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Accelererat nätverk | Aktivera | Aktivera | ---|
| SAP ASE-version | 16.0.03.07 eller högre | 16.0.03.07 eller högre | --- |
| antal data enheter | 16 | 16 | ---|
| antal logg enheter | 1 | 1 | --- |
| antal temporära enheter | 1 | 1 | mer för SAP BW arbets belastning |
| Operativsystem | Windows Server 2019 | SUSE 12 SP4/15 SP1 eller RHEL 7,6 | --- |
| Disk agg regering | Lagringsutrymmen | LVM2 | --- |
| Filsystem | NTFS | XFS |
| Format block storlek | behöver arbets belastnings testning | behöver arbets belastnings testning | --- |
| # och typen av data diskar | Premium Storage: 4 x P30 (RAID0) | Premium Storage: 4 x P30 (RAID0)| Cache = skrivskyddad |
| # och typen av logg diskar | Premium-lagring: 1 x P20  | Premium-lagring: 1 x P20 | Cache = ingen |
| ASE MaxMemory-parameter | 90% av fysiskt RAM-minne | 90% av fysiskt RAM-minne | förutsätter en enskild instans |
| antal säkerhets kopierings enheter | 4 | 4| --- |
| # och typ av säkerhets kopierings diskar | 1 | 1 | --- |


Ett exempel på en konfiguration för en liten SAP ASE DB-server med databas storleken 2 TB +, till exempel ett större globalt använt SAP Business Suite-system, kan se ut så här:

| Konfiguration | Windows | Linux | Kommentarer |
| --- | --- | --- | --- |
| Typ av virtuell dator | M-serien (1,0 till 4,0 TB RAM)  | M-serien (1,0 till 4,0 TB RAM) | --- |
| Accelererat nätverk | Aktivera | Aktivera | ---|
| SAP ASE-version | 16.0.03.07 eller högre | 16.0.03.07 eller högre | --- |
| antal data enheter | 32 | 32 | ---|
| antal logg enheter | 1 | 1 | --- |
| antal temporära enheter | 1 | 1 | mer för SAP BW arbets belastning |
| Operativsystem | Windows Server 2019 | SUSE 12 SP4/15 SP1 eller RHEL 7,6 | --- |
| Disk agg regering | Lagringsutrymmen | LVM2 | --- |
| Filsystem | NTFS | XFS |
| Format block storlek | behöver arbets belastnings testning | behöver arbets belastnings testning | --- |
| # och typen av data diskar | Premium Storage: 4 + x P30 (RAID0) | Premium Storage: 4 + x P30 (RAID0)| Cache = skrivskyddad, Överväg Azure Ultra disk |
| # och typen av logg diskar | Premium-lagring: 1 x P20  | Premium-lagring: 1 x P20 | Cache = ingen, Överväg Azure Ultra disk |
| ASE MaxMemory-parameter | 90% av fysiskt RAM-minne | 90% av fysiskt RAM-minne | förutsätter en enskild instans |
| antal säkerhets kopierings enheter | 16 | 16 | --- |
| # och typ av säkerhets kopierings diskar | 4 | 4 | Använd LVM2/lagrings utrymmen |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Säkerhets kopiering & återställnings överväganden för SAP ASE på Azure
Om du ökar antalet data och säkerhets kopierings enheter ökar prestandan för säkerhets kopiering och återställning. Vi rekommenderar att du tar bort de Azure-diskar som är värdar för SAP ASE backup-enheten som visas i tabellerna som visas ovan. Var noga med att balansera antalet säkerhets kopierings enheter och diskar och se till att genomflödet i säkerhets kopieringen inte får överstiga 40%-50% av den totala kvoten för data flödet för virtuella datorer. Vi rekommenderar att du använder komprimering av SAP backup som standard. Mer information finns i artiklarna:

- [#1588316 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1588316)
- [#1801984 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1801984)
- [#1585981 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1585981) 

Använd inte enhets D:\ eller/Temp utrymme som databas eller logg dumpnings mål.

### <a name="impact-of-database-compression"></a>Påverkan av databas komprimering
I konfigurationer där I/O-bandbredden kan bli en begränsnings faktor kan åtgärder som minskar IOPS bidra till att sträcka ut arbets belastningen som kan köras i ett IaaS-scenario som Azure. Därför rekommenderar vi att du kontrollerar att SAP ASE Compression används innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen att tillämpa komprimering innan överföring till Azure erhålls av flera skäl:

* Mängden data som ska överföras till Azure är lägre
* Komprimerings Körningens varaktighet är kortare än att en kan använda starkare maskin vara med fler processorer eller högre I/O-bandbredd eller mindre I/O-latens lokalt
* Mindre storlek på databaser kan leda till lägre kostnader för diskallokering

Data-och LOB-komprimering fungerar på en virtuell dator som finns i Azure Virtual Machines eftersom det sker lokalt. Mer information om hur du kontrollerar om komprimering redan används i en befintlig SAP ASE-databas finns i [SAP support anmärkning 1750510](https://launchpad.support.sap.com/#/notes/1750510). Mer information om SAP-ASE för databas komprimering finns i [SAP support note #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Hög tillgänglighet för SAP ASE på Azure 
HADR Users guide innehåller information om installation och konfiguration av en ASE-lösning (Always on) för två noder.  Dessutom stöds även en tredje återställnings nod. SAP ASE stöder många konfigurationer med hög tillgänglighet, inklusive delad disk och ursprungligt OS-kluster (flytande IP). Den enda konfiguration som stöds på Azure använder fel hanteraren utan flytande IP-adress.  Den flytande IP-metoden fungerar inte i Azure.  SAP-kerneln är en "HA medveten" applikation och vet om de primära och sekundära SAP ASE-servrarna. Det finns inga nära integreringar mellan SAP-ASE och Azure, den interna belastningsutjämnaren i Azure används inte. Därför bör den vanliga SAP ASE-dokumentationen följas från och med [SAP ASE hadr Users guide](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> Den enda konfiguration som stöds på Azure använder fel hanteraren utan flytande IP-adress.  Den flytande IP-metoden fungerar inte i Azure. 

### <a name="third-node-for-disaster-recovery"></a>Tredje nod för haveri beredskap
Förutom att använda SAP ASE Always on för lokal hög tillgänglighet kanske du vill utöka konfigurationen till en asynkront replikerad nod i en annan Azure-region. Dokumentation för sådana scenarier finns [här](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE Database Encryption & SSL 
SAP Software Provisioning Manager (SWPM) ger ett alternativ för att kryptera databasen under installationen.  Om du vill använda kryptering rekommenderar vi att du använder fullständig SAP-databas kryptering.  Se information som dokumenteras i:

- [#2556658 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2556658)
- [#2224138 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2224138)
- [#2401066 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2401066)
- [#2593925 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Om en SAP ASE-databas är krypterad så fungerar inte komprimering av säkerhets kopierings dumpning. Se även [SAP support note #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Check lista för SAP ASE på Azure Deployment
 
- Distribuera SAP ASE 16.0.03.07 eller högre
- Uppdatera till den senaste versionen och korrigeringarna av FaultManager och SAPHostAgent
- Distribuera på senaste certifierade operativ system som Windows 2019, SUSE 15,1 eller RedHat 7,6 eller högre
- Använd SAP-certifierade VM: er med hög kapacitet för Azure VM-SKU: er, till exempel Es_v3 eller för virtuella datorer med x-stor system M-serien rekommenderas
- Matcha diskens IOPS och total mängd data flöde för virtuella datorer för den virtuella datorn med disk designen.  Distribuera tillräckligt många diskar
- Aggregera diskar med hjälp av Windows lagrings utrymmen eller Linux-LVM2 med rätt rand storlek och fil system
- Skapa tillräckligt många enheter för data-, logg-, temp-och säkerhets kopierings syfte
- Överväg att använda UltraDisk för x-stora system 
- Kör `saptune` SAP-ASE på Linux OS 
- Skydda databasen med DB-kryptering – lagra nycklar manuellt i Azure Key Vault 
- Slutför [Check listan för SAP på Azure](./sap-deployment-checklist.md) 
- Konfigurera logg säkerhets kopiering och fullständig säkerhets kopiering 
- Test HA/DR, säkerhets kopiering och återställning och genomför stress & volym test 
- Bekräfta att det automatiska databas tillägget fungerar 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Använda DBACockpit för att övervaka databas instanser
För SAP-system, som använder SAP ASE som databas plattform, är DBACockpit tillgängligt som inbäddad webbläsare i transaktion DBACockpit eller som WebDynpro. Alla funktioner för att övervaka och administrera databasen är dock bara tillgängliga i WebDynpro-implementeringen av DBACockpit.

Precis som med lokala system krävs flera steg för att aktivera alla SAP NetWeaver-funktioner som används av WebDynpro-implementeringen av DBACockpit. Följ [#1245200 för SAP-supporten](https://launchpad.support.sap.com/#/notes/1245200) om du vill aktivera användningen av webdynpros och generera de nödvändiga dem. När du följer anvisningarna i ovanstående anmärkningar konfigurerar du även Internet Communication Manager ( `ICM` ) tillsammans med de portar som ska användas för http-och HTTPS-anslutningar. Standardinställningen för http ser ut så här:

> ICM/server_port_0 = PROT = HTTP, PORT = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = PROT = HTTPS, PORT = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

och länkarna som genereras i Transaction DBACockpit ser ut ungefär så här:

> https: \/ / \<fullyqualifiedhostname> : 44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> : 8000/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> 

Beroende på hur den virtuella Azure-datorn som är värd för SAP-systemet är ansluten till AD och DNS måste du se till att ICM använder ett fullständigt kvalificerat värdnamn som kan lösas på den dator där du öppnar DBACockpit från. Se [SAP support note #773830](https://launchpad.support.sap.com/#/notes/773830) för att förstå hur ICM avgör det fullständigt kvalificerade värd namnet baserat på profil parametrar och ange parametern ICM/host_name_full uttryckligen vid behov.

Om du har distribuerat den virtuella datorn i ett moln scenario utan anslutning mellan olika platser och Azure måste du definiera en offentlig IP-adress och en `domainlabel` . Formatet på det offentliga DNS-namnet på den virtuella datorn ser ut så här:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com
> 
> 

Mer information om DNS-namnet finns [här] [Virtual-Machines-azurerm-kontra-azuresm].

Ange SAP Profile-parametern ICM/host_name_full till DNS-namnet på den virtuella Azure-datorn som länken kan se ut ungefär så här:

> https: \/ /mydomainlabel.westeurope.cloudapp.net:44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.net:8000/SAP/BC/WebDynpro/SAP/dba_cockpit

I detta fall måste du se till att:

* Lägg till regler för inkommande trafik i nätverks säkerhets gruppen i Azure Portal för TCP/IP-portarna som används för att kommunicera med ICM
* Lägg till inkommande regler i konfigurationen för Windows-brandväggen för TCP/IP-portarna som används för att kommunicera med ICM

För en automatiserad import av alla korrigeringar som är tillgängliga rekommenderar vi att du regelbundet använder korrigerings samlingen SAP-notering som gäller för din SAP-version:

* [#1558958 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1558958)
* [#1619967 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1619967)
* [#1882376 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1882376)

Mer information om ASE-cockpit för SAP finns i följande SAP-anteckningar:

* [#1605680 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1605680)
* [#1757924 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1757924)
* [#1757928 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1757928)
* [#1758182 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1758182)
* [#1758496 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1758496)    
* [#1814258 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1814258)
* [#1922555 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1922555)
* [#1956005 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Användbara länkar, anteckningar & Whitepapers för SAP ASE
Start sidan för [SAP ASE 16.0.03.07-dokumentationen](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) innehåller länkar till olika dokument som dokumenten innehåller:

- SAP ASE Learning res-administration & övervakning
- SAP ASE Learning res-installation & uppgradering

är användbart. Ett annat användbart dokument är [SAP-program på anpassningsbara SAP Server Enterprise-rekommenderade metoder för migrering och körning](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Andra användbara SAP support-kommentarer är:

- [#2134316 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2134316) 
- [#1748888 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1748888) 
- [#2588660 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2588660) 
- [#1680803 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1680803) 
- [#1724091 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1724091) 
- [#1775764 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1775764) 
- [#2162183 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2162183) 
- [#1928533 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1928533)
- [#2015553 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2015553)
- [#1750510 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1750510) 
- [#1752266 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/1752266) 
- [#2162183 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/2162183) 
- [#1588316 för SAP-support anteckning](https://launchpad.support.sap.com/#/notes/158831) 

Annan information publiceras på 

- [SAP-program på SAP adaptiv Server Enterprise](https://community.sap.com/topics/applications-on-ase)
- [SAP ASE-Infocenter](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Always in with installation av tredje DR-nod](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Ett månatligt nyhets brev publiceras via [SAP support note #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Nästa steg
Läs artikeln [SAP-arbetsbelastningar på Azure: planering och distribution check lista](./sap-deployment-checklist.md)
