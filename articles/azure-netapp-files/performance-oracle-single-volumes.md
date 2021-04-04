---
title: Prestanda för Oracle Database på Azure NetApp Files enskild volym | Microsoft Docs
description: Beskriver prestanda test resultat för en Azure NetApp Files enskild volym på Oracle Database.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571412"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Prestanda för Oracle-databas på enskilda Azure NetApp-volymer

Den här artikeln behandlar följande avsnitt om Oracle i molnet. Dessa ämnen kan vara särskilt intressanta för en databas administratör, en moln arkitekt eller en lagrings arkitekt:   

* När du driver en OLTP-arbets belastning (Online Transaction Processing) (mest slumpmässiga I/O) eller en Online Analytical Processing (OLAP)-arbets belastning (främst sekventiella I/O), vad kommer prestanda att se ut?   
* Vad är skillnaden i prestanda mellan den vanliga kNFS-klienten (Linux kernel NFS) och Oracles egen Direct NFS-klient?
* När det gäller bandbredden är prestandan för en enda Azure NetApp Files volym tillräckligt?

## <a name="testing-environment-and-components"></a>Testa miljö och komponenter

Följande diagram illustrerar miljön som används för testning. För konsekvens och enkelhet användes Ansible spel böcker för att distribuera alla element i test sängen.

![Oracle test miljö](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Konfiguration av virtuell dator

Testerna använde följande konfiguration för den virtuella datorn:
* Operativ system:   
    RedHat Enterprise Linux 7,8 (Wle-ora01)
* Instans typer:   
    Två modeller användes vid testning – D32s_v3 och D64s_v3
* Antal nätverks gränssnitt:   
    Ett (1) placerat i undernät 3  
* Disk   
    Oracle-binärfiler och OS placerades på en enda Premium-disk

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp Files konfiguration
Testerna använde följande Azure NetApp Files konfiguration:   

* Storlek på pool med kapacitet:  
    Olika storlekar för poolen har kon figurer ATS: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Service nivå:  
    Ultra (128 MiB/s med bandbredd per 1 TiB av allokerad volym kapacitet)
* Enheter  
    Ett och två volym test utvärderades

### <a name="workload-generator"></a>Arbets belastnings Generator 

De tester som används för att skapa SLOB-2.5.4.2. SLOB (Silly lite Oracle benchmark) är en välkänd arbets belastnings Generator i Oracle-rymden som är utformad för att stressa och testa i/O-undersystemet med en SGA fysisk I/O-belastning.  

SLOB-2.5.4.2 har inte stöd för den plugg bara databasen (PDB). Därför lades en ändring till i- `setup.sh` och- `runit.sh` skripten för att lägga till PDB-stöd till den.  

Variablerna SLOB som används i testerna beskrivs i följande avsnitt.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>Arbets belastning 80% Välj, 20% uppdatering | Slumpmässiga I/O – `slob.conf` variabler   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>Arbets belastning 100% Välj | Sekventiella I/O – `slob.conf` variabler

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>Databas

Oracle-versionen som används för testerna är Oracle Database Enterprise Edition 19.3.0.0.

Oracle-parametrarna är följande:  
* `sga_max_size`: 4096
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: sant
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

En PDB-databas har skapats för SLOB-databasen.

Följande diagram visar det tabell namn som heter PERFIO med 600 GB i storlek (20 datafiler, 30 GB vardera) som skapats för att vara värd för fyra SLOB användar scheman. Varje användar schema var 125 GB i storlek.

![Oracle-databas](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Prestandamått

Målet var att rapportera IO-prestandan som den fick av programmet. Därför använder alla diagram i den här artikeln mått som rapporter ATS av Oracle-databasen via sina automatiska AWR-rapporter (arbets belastnings lager). Måtten som används i diagrammen är följande:   

* **Genomsnittligt antal IO-begäranden per sekund**   
    Motsvarar summan av genomsnittliga läsnings-IO-begäranden per sekund och Genomsnittligt antal Skriv-IO-begäranden per sekund från belastnings profil avsnittet
* **Genomsnittligt i/o MB/SEK**   
    Motsvarar summan av genomsnittligt Read IO MB/SEK och genomsnittligt Skriv-IO MB/SEK från belastnings profil avsnittet
* **Genomsnittlig Läs fördröjning**   
    Motsvarar den genomsnittliga svars tiden för Oracle wait Event "DB-filens sekventiella läsning" i mikrosekunder
* **Antal trådar/schema**   
    Motsvarar antalet SLOB-trådar per användar schema

## <a name="performance-measurement-results"></a>Prestanda mätnings resultat  

I det här avsnittet beskrivs resultatet av prestanda mätningen.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS-klient jämfört med Oracle Direct NFS

Det här scenariot kördes på en virtuell Azure-dator Standard_D32s_v3 (Intel E5-2673 v4 @ 2,30 GHz). Arbets belastningen är 75% Välj och 25% uppdatering, de flesta slumpmässiga I/O, och med en träff på Database-buffert på ~ 7,5%. 

Som du ser i följande diagram levererade Oracle DNFS-klienten upp till 2,8 x mer data flöde än den vanliga Linux kNFS-klienten:  

![Linux kNFS-klient jämfört med Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

Följande diagram visar svars kurvan för Läs åtgärder. I det här sammanhanget är Flask halsen för kNFS-klienten den enda NFS TCP socket-anslutningen som upprättats mellan klienten och NFS-servern (Azure NetApp Files volym).  

![Linux kNFS-klient jämfört med svars kurvan för Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

DNFS-klienten kunde skicka fler IO-begäranden per sekund på grund av dess möjlighet att skapa hundratals TCP-socketanslutningar, och därför dra nytta av parallellitet. Som beskrivs i [Azure NetApp Files konfiguration](#anf_config), tillåter varje ytterligare TIB av kapacitet för ytterligare 128MiB/s bandbredd. DNFS topped på 1 GiB/s av data flödet, vilket är den gräns som anges av kapacitets valet 8-TiB. Mer kapacitet, mer data flöde skulle ha varit drivet.

Data flödet är bara ett av överväganden. Ett annat övervägande är svars tid, som har den främsta påverkan på användar upplevelsen. I följande diagram visas kan fördröjnings ökningar förväntas snabbt med kNFS än med DNFS. 

![Linux kNFS-klienten jämförd med Läs fördröjningen Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-read-latency.png)  

Histogram ger utmärkta insikter om databas fördröjning. Följande diagram ger en fullständig vy från perspektivet för den registrerade "DB-filen sekventiellt läst", samtidigt som du använder DNFS på den högsta samtidighets data punkten (32 trådar/schema). Som du ser i följande diagram har 47% av alla Läs åtgärder lösts mellan 512 mikrosekunder och 1000 mikrosekunder, medan 90% av alla Läs åtgärder betjänades under en svars tid under 2 MS.

![Linux kNFS-klient jämfört med Oracle Direct NFS-histogram](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

I slut satsen är det klart att DNFS är en måste-ha när den kommer för att förbättra prestanda för en Oracle Database-instans på NFS.

### <a name="single-volume-performance-limits"></a>Prestanda begränsningar för enskilda volymer

I det här avsnittet beskrivs prestanda gränserna för en enskild volym med slumpmässig I/O och sekventiell I/O. 

#### <a name="random-io"></a>Slumpmässig I/O

DNFS kan använda mycket mer bandbredd än vad som tillhandahålls av en 8 TB Azure NetApp Files prestanda kvot. Genom att öka Azure NetApp Files volym kapacitet till 16 TiB, vilket är en momentan förändring, ökade bandbreddens bandbredd från 1024 MiB/s av 2X till 2048 MiB/s. 

I följande diagram visas en konfiguration för en 80%-Välj-och 20%-uppdaterings arbets belastning och med en träff grad på en databas som är 8%. SLOB kunde köra en enkel volym på 200 000 NFS-begäranden per sekund. Med tanke på att varje åtgärd är 8 KiB storlek kunde systemet under testet leverera ~ 200 000 IO-begäranden per sekund eller 1600 MiB/s.
 
![Oracle DNFS-dataflöde](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

Följande svars kurva för Läs fördröjning visar att när Läs data flödet ökar, ökar svars tiden jämnt under 1-MS-linjen och den träffar Knee i kurvan vid ~ 165 000 Genomsnittligt Läs-IO-begäranden per sekund vid den genomsnittliga Läs fördröjningen på ~ 1,3 MS.  Det här värdet är ett otroligt latens värde för en I/O-taxa som inte kan nås med nästan vilken annan teknik som helst i Azure-molnet. 

![Svars kurva för Oracle-DNFS](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>Sekventiell I/O  

Som du ser i följande diagram är inte all I/O slumpmässig i natur, med en RMAN säkerhets kopia eller en fullständig tabells ökning, till exempel som arbets belastningar som kräver så mycket bandbredd som de kan få.  Med samma konfiguration som tidigare beskrivits men med volymen ändrat storlek till 32 TiB, visar följande diagram att en enskild Oracle DB instans kan drivas uppåt av 3 900 MB/s genom strömning, mycket Azure NetApp Files nära volymens prestanda kvot på 32 TB (128 MB/s * 32 = 4096 MB/s).

![I/O för Oracle-DNFS](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

I sammanfattning Azure NetApp Files hjälper dig att ta dina Oracle-databaser till molnet. Den ger till gång till prestanda när databasen kräver det. Du kan när som helst ändra volym kvoten dynamiskt och icke-disruptively.

## <a name="next-steps"></a>Nästa steg

- [Rekommendationer från benchmark-prestandatest för Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md)
- [Prestandamått för Linux](performance-benchmarks-linux.md)