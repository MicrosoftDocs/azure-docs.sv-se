---
title: Återställa med Azure Application konsekvent ögonblicks bilds verktyg för Azure NetApp Files | Microsoft Docs
description: Innehåller en guide för att köra kommandot Restore för Azure Application konsekventa ögonblicks bild verktyget som du kan använda med Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 793b4da8fcf46ba4d5618f8ada86f9c3c8026ffd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865272"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Återställa med Azure Application konsekvent ögonblicks bild verktyget (för hands version)

Den här artikeln innehåller en guide för att köra kommandot Restore i Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.

## <a name="introduction"></a>Introduktion

Det görs en volym återställning från en ögonblicks bild med hjälp av `azacsnap -c restore` kommandot.

> [!IMPORTANT]
> Detta utför inte en databas återställning, bara en återställning av volym (er) enligt beskrivningen för var och en av alternativen nedan.

## <a name="command-options"></a>Kommando alternativ

`-c restore`Kommandot har följande alternativ:

- `--restore snaptovol` Skapar en ny volym baserat på den senaste ögonblicks bilden på mål volymen. Det här kommandot skapar en ny "klonad" volym baserat på den konfigurerade mål volymen, med den senaste ögonblicks bilden av volymen som bas för att skapa den nya volymen.  Det här kommandot avbryter inte lagrings replikeringen från primär till sekundär. Kloner av den senaste tillgängliga ögonblicks bilden skapas i stället på DR-platsen och rekommenderade fil system mountpoints för de klonade volymerna presenteras. Det här kommandot ska köras på Azures stora instans system **i Dr-regionen** (det vill säga målets återställnings system).

- `--restore revertvolume` Återställer mål volymen till ett tidigare tillstånd baserat på den senaste ögonblicks bilden.  Använd det här kommandot som en del av DR-redundans i den kopplade DR-regionen. Det här kommandot **stoppar** lagrings replikering från den primära platsen till den sekundära platsen och återställer MÅLETs Dr-volym (er) till sin senaste tillgängliga ögonblicks bild på Dr-volymerna tillsammans med det rekommenderade fil systemet mountpoints för de ÅTERSTÄLLda Dr-volymerna. Det här kommandot ska köras på Azures stora instans system **i Dr-regionen** (det vill säga målets återställnings system).
    > [!NOTE]
    > Sub-Command ( `--restore revertvolume` ) är endast tillgängligt för Azures stora instanser och är inte tillgängligt för Azure NetApp Files.
- `--dbsid <SAP HANA SID>` är SAP HANA SID som väljs från konfigurations filen för att tillämpa volym återställnings kommandona på.

- `[--configfile <config filename>]` är en valfri parameter som tillåter anpassade konfigurations fil namn.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>Utföra en katastrof-redundansväxling `azacsnap -c restore --restore snaptovol`

Det här kommandot liknar kommandot "fullständig" DR-redundans ( `--restore restorevolume` ), men i stället för att dela upp replikeringen mellan den primära platsen och haveri beredskaps platsen, skapas en klon volym av Disaster Recovery-volymerna så att du kan återställa den senaste ögonblicks bilden på Dr-platsen. De klonade volymerna kan sedan användas av kunden för att testa haveri beredskap utan att behöva köra en komplett redundansväxling av sin HANA-miljö som bryter mot replikeringstrafiken mellan den primära platsen och haveri återställnings platsen.

- Flera olika återställnings punkter kan testas på det här sättet, var och en med sin egen återställnings punkt.
- Klonen anges av tidstämpeln vid när kommandot kördes och representerar de senaste data och andra ögonblicks bilder som är tillgängliga när de körs.

> [!IMPORTANT]
> Den här åtgärden gäller endast för en stor Azure-instans.
>
> - När det här kommandot körs kräver det att kontakta e-postmeddelandet för åtgärder för att samar beta med innan kloningen tas bort efter 4 veckor.
> - Varje körning av det här kommandot skapar en ny klon som måste tas bort av Microsoft-åtgärder när testet avslutas.
> - Alla klonade volymer som skapas tas automatiskt bort efter 4 veckor.

Konfigurations filen (till exempel `DR.json` ) ska endast innehålla Dr-volymer och inte produktions volymer, annars kan produktions volymerna ha kloner skapats.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>`azacsnap -c restore --restore snaptovol`Kommandots utdata (för Single-Node scenario)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --dbsid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> Utmatningen "visar monterings punkter efter volym" skiljer sig från de olika scenarierna.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Utföra fullständig DR-redundans `azacsnap -c restore --restore revertvolume`

Det här kommandot **stoppar** lagrings replikering från den primära platsen till den sekundära platsen, återställer den senaste ögonblicks bilden på Dr-volymerna och ger MOUNTPOINTS för Dr-volymer.

Det här kommandot måste utföras på DR-servern med hjälp av en konfigurations fil (till exempel `DR.json` ) med enbart Dr-volymer.

Utför en redundansväxling till DR-platsen genom att köra kommandot `azacsnap -c restore --restore revertvolume` .  Det här kommandot kräver ett SID som ska läggas till som en parameter. Detta är SID för HANA-instansen som måste återställas på DR-platsen.

> [!IMPORTANT]
> Kör bara det här kommandot om du planerar att utföra DR-övningen eller ett test. Det här kommandot delar upp replikeringen. Du måste kontakta Microsoft-åtgärder för att återaktivera replikering.

På hög nivå finns följande steg för att köra en DR-redundans:

- Du måste stänga av HANA-instansen på den **primära** platsen. Den här åtgärden behövs bara om du gör redundansväxlingen till DR-platsen för att undvika inkonsekvenser i data.
- Stäng av HANA-instansen på DR-noden för produktions-SID.
- Kör kommandot `azacsnap -c restore --restore revertvolume` på Dr-noden med sid som ska återställas.
  - Kommandot delar länken Storage Replication (replikering) från den primära platsen till DR-platsen
  - Kommandot återställer de "data" och "andra" volymer enligt konfigurationen.  Den här åtgärden är vanligt vis för volymerna för- `/hana/data` och- `/hana/logbackups` fil systemet.  `/hana/shared`Fil systemet återställs inte, utan använder i stället det befintliga `/hana/shared` för-sid på Dr-platsen.
  - Montera- `/hana/data` och- `/hana/logbackups` volymer – se till att de läggs till i `/etc/fstab` filen
- Återställ SYSTEMDB-ögonblicksbilden HANA. HANA Studio visar bara den senaste HANA-ögonblicksbilden som är tillgänglig under lagrings ögonblicks bilden som återställts som en del av ögonblicks bild kommandot `azacsnap -c restore --restore revertvolume` .
- Återställa klient databasen.
- Starta HANA-instansen på DR-platsen för produktions-SID (exempel: H80 i det här fallet).
- Utför alla databas test.

## <a name="next-steps"></a>Nästa steg

- [Gör en säkerhets kopia](azacsnap-cmd-ref-backup.md)
- [Hämta information om ögonblicks bild](azacsnap-cmd-ref-details.md)
