---
title: Haveri beredskap med Azure Application enhetligt ögonblicks bilds verktyg för Azure NetApp Files | Microsoft Docs
description: Förklarar hur du utför haveri beredskap när du använder Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 554fa394179e7cfc5b86a2b50eb754547d137a44
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870404"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Haveri beredskap med Azure Application konsekvent ögonblicks bild verktyget (förhands granskning)

Den här artikeln förklarar hur du utför haveri beredskap när du använder Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.

> [!IMPORTANT]
> Den här åtgärden gäller enbart för **Azures stora instanser** .

## <a name="introduction"></a>Introduktion

Azures stora instans plattform kan också ha en plats för katastrof återställning där ögonblicks bilder av lagrings volymer kan replikeras till.  Om ögonblicks bilder har kon figurer ATS korrekt med en sådan konfiguration är det möjligt att utföra en haveri beredskap på den här platsen.  Det här dokumentet är avsett att vara en guide för att utföra haveri beredskap för den här installationen.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Krav för installation av haveri beredskap

Följande krav måste uppfyllas innan du planerar redundansväxlingen av haveri beredskap.

- Du har en DR-nod etablerad på DR-platsen. Det finns två alternativ för DR. Det ena är normal DR och andra är en DR-funktion.
- Storage-replikering fungerar. Microsoft Operations-teamet utför konfigurationen av Storage Replication vid tidpunkten för automatisk etablering. Du kan övervaka Storage-replikeringen med hjälp av kommandot `azacsnap -c details --details replication` på Dr-platsen.
- Du har konfigurerat och konfigurerat lagrings ögonblicks bilder på den primära platsen.
- Du har en HANA-instans installerad på DR-platsen för den primära med samma SID som den primära instansen.
- Du läser och förstår proceduren för DR-redundans som beskrivs i [SAP HANA – stora instanser hög tillgänglighet och haveri beredskap på Azure](../virtual-machines/workloads/sap/hana-failover-procedure.md)
- Du har konfigurerat och konfigurerat lagrings ögonblicks bilder på DR-platsen.
- En konfigurations fil (till exempel `DR.json` ) har skapats med Dr-lagringsenheten och associerad information på Dr-servern.
- Du har slutfört stegen på DR-webbplatsen för att:
  - Aktivera kommunikation med lagring.
  - Aktivera kommunikation med SAP HANA.

## <a name="set-up-disaster-recovery"></a>Konfigurera haveriberedskap

Microsoft stöder replikering på lagrings nivå för DR-återställning. Det finns två sätt att konfigurera DR.

Den ena är **Normal** och annan är **multisyfte**. I den **normala** Dr har du en dedikerad instans på platsen för Dr för redundans. I ett interaktivt **Dr-** scenario har du en annan frågor eller utveckling Hana-instans som körs på den stora volymen i Hana-instansen på Dr-platsen. Men du installerar även en förinstallerad HANA-instans som är vilande och har samma SID som den HANA-instans som du vill redundansväxla till den HANA stora instans enheten. Microsoft-åtgärder konfigurerar miljön för dig inklusive lagrings replikering baserat på de indata som tillhandahålls i service förfrågnings formuläret (SRF) vid tidpunkten för onboarding.

> [!IMPORTANT]
> Se till att alla krav uppfylls för DR-konfigurationen.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Övervaka datareplikering från primär till DR-plats

Microsoft Operations-teamet hanterar och övervakar redan DR-länken från den primära platsen till DR-platsen.
Du kan övervaka datareplikeringen från den primära servern till DR-servern med hjälp av ögonblicks bild kommandot `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>Utföra en redundansväxling till en DR-plats

Kör kommandot redundans på DR-platsen ( `azacsnap -c restore --restore revertvolume` ).

> [!IMPORTANT]
> `azacsnap -c restore --restore revertvolume`Kommandot delar upp lagrings replikeringen från produktions platsen till Dr-platsen. Du måste kontakta Microsoft-åtgärderna för att kunna konfigurera replikering igen. När replikeringen har Aktiver ATS på nytt kommer alla data i DR-lagring för detta SID att initieras. Kommandot som utför redundansväxlingen gör det möjligt att använda den senast replikerade lagrings ögonblicks bilden. Om du behöver återställa tillbaka till en äldre ögonblicks bild kan du öppna en supportbegäran så att du kan få en tidigare ögonblicks bild återställd på DR-platsen.

Här är de steg som du följer för DR-redundans på hög nivå:

- Du måste stänga av HANA-instansen på den **primära** platsen. Den här åtgärden krävs bara om du utför redundansväxlingen till DR-platsen så att du inte har data inkonsekvenser.
- Stäng av HANA-instansen på DR-noden för produktions-SID.
- Kör kommandot `azacsnap -c restore --restore revertvolume` på Dr-noden med sid som ska återställas
  - Kommandot delar länken Storage Replication (replikering) från den primära platsen till DR-platsen
  - Kommandot återställer endast/data-och/logbackups-volymen, men/Shared volym återställs inte, utan använder sig av den befintliga/Shared för SID på DR-platsen.
  - Montera/data-och/logbackups-volymen – se till att lägga till den till fstab-filen
- Återställ SYSTEMDB-ögonblicksbilden HANA. HANA Studio visar bara den senaste HANA-ögonblicksbilden som är tillgänglig under lagrings ögonblicks bilden som återställs som en del av kommando `azacsnap -c restore --restore revertvolume` körningen.
- Återställa klient databasen.
- Starta HANA-instansen på DR-platsen för produktions-SID (exempel: H80 i det här fallet).
- Utför testning.

### <a name="example-performing-disaster-recovery"></a>Exempel på katastrof återställning

I det här underavsnittet beskrivs de detaljerade stegen för en redundansväxling till katastrof återställnings platsen.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>Steg 1: Hämta volym informationen för DR-noden

Kör kommandot `df –h` för att lista de fil system och associerade volymer som ska referera till efter redundansväxlingen.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>Steg 2: Stäng av HANA på den primära platsen

Om du utför en fullständig redundansväxling av produktions arbets belastningar och det är möjligt att ansluta till den primära produktions platsen stänger du SAP HANA instanserna som växlas över till DR.

Om t. ex. är inloggad som rot i följande exempel visas hur SAP HANA kan stängas av.  Ersätt <sid> med SAP HANA sid.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>Steg 3: Stäng av HANA på DR-platsen

Det är viktigt att stänga SAP HANA på DR-platsen innan du återställer volymerna.

Om t. ex. är inloggad som rot i följande exempel visas hur SAP HANA kan stängas av.  Ersätt <sid> med SAP HANA sid.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Se till att HANA-instanserna på DR-platsen är offline innan du återställer några volymer.

#### <a name="step-4-restore-the-volumes"></a>Steg 4: återställa volymerna

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**_Utdata från kommandot Dr-redundans_**.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> Stegen i slutet av konsolens visning måste utföras för att slutföra lagrings förberedelserna för en DR-redundans.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>Steg 5: Demontera onödiga fil system

Kör kommandot `umount` för att demontera de fil system/volymer som inte behövs.

```bash
umount <Mount point>
```

Demontera data-och mountpoints för säkerhets kopiering. Du kan ha flera data monterings punkt i det skalbara scenariot.

#### <a name="step-6-configure-the-mount-points"></a>Steg 6: Konfigurera monterings punkterna

Ändra filen `/etc/fstab` för att kommentera ut poster för data och logg säkerhets kopior för primärt sid (i det här EXEMPLET sid = H80) och Lägg till de nya monterings punkts poster som skapats från den primära platsen Dr-volymer. De nya monterings punkts posterna anges i kommandots utdata.

- Kommentera ut befintliga monterings punkter som körs på DR-platsen med följande `#` :

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Lägg till följande rader i `/etc/fstab`
  > Detta bör vara samma utdata från kommandot

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>Steg 7: montera återställnings volymerna

Kör kommandot `mount –a` för att montera alla monterings punkter.

```bash
mount -a
```

Om du kör `df –h` bör du nu se vilka volymer som är `*_dp` monterade.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>Steg 8: återställa SYSTEMDB

Från HANA Studio högerklickar du på SYSTEMDB-instansen och väljer "säkerhets kopiering och återställning" och sedan "Återställ system databas"

Se guiden för att återställa en databas från en ögonblicks bild, särskilt SYSTEMDB.

#### <a name="step-9-recover-the-tenant-database"></a>Steg 9: återställa klient databasen

Från HANA Studio högerklickar du på SYSTEMDB-instansen och väljer "säkerhets kopiering och återställning" och sedan "Återställ klient databas".

Se guiden för att återställa en databas från en ögonblicks bild, särskilt klient databaserna.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Kör `azacsnap -c backup` på Dr-platsen

Om du kör ögonblicks bilds säkerhets kopieringar på Dr-platsen ska det Hana-servernamn som kon figurer ATS i `azacsnap` konfigurations filen på Dr-platsen vara samma som namnet på produktions servern.

> [!IMPORTANT]
> Om du kör `azacsnap -c backup` kan skapa lagrings ögonblicks bilder på Dr-platsen, replikeras de inte automatiskt till en annan plats.  Arbeta med Microsoft-åtgärder för att bättre förstå att returnera filer eller data tillbaka till den ursprungliga produktions platsen.

## <a name="next-steps"></a>Nästa steg

- [Hämta information om ögonblicks bild](azacsnap-cmd-ref-details.md)
- [Gör en säkerhets kopia](azacsnap-cmd-ref-backup.md)
