---
title: Felsöka återställning av virtuella Azure-filer
description: Felsök problem som är specifika för återställning av filer och mappar från en virtuell Azure-säkerhetskopiering.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513412"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Fel söknings problem vid fil återställning av virtuell Azure-säkerhetskopiering

Den här artikeln innehåller fel söknings steg som kan hjälpa dig att lösa Azure Backup fel som är relaterade till problem som är specifika för återställning av filer och mappar från en virtuell Azure-säkerhetskopiering. 

## <a name="exception-caught-while-connecting-to-target"></a>Ett undantag inträffade vid anslutning till målet

Möjlig orsak: skriptet kan inte komma åt den rekommenderade åtgärden för återställnings punkt: kontrol lera om datorn uppfyller alla [åtkomst krav](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>Målet har redan loggats in via en iSCSI-session

Möjlig orsak: skriptet har redan körts på samma dator och enheterna har anslutits.
Rekommenderad åtgärd: volymerna för återställnings punkten har redan bifogats. De kan inte monteras med samma enhets beteckning på den ursprungliga virtuella datorn. Bläddra igenom alla tillgängliga volymer i Utforskaren.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Skriptet är ogiltigt eftersom diskarna har demonterats via portalen/överskridit gränsen på 12 timmar. Hämta ett nytt skript från portalen

Möjlig orsak: diskarna har demonterats från portalen eller så överskreds gränsen på 12 timmar.
Rekommenderad åtgärd: skriptet är ogiltigt efter 12 timmar från den tid det hämtades och kan inte köras. Besök portalen och hämta ett nytt skript för att fortsätta med fil återställningen.

## <a name="troubleshooting-common-issues"></a>Felsöka vanliga problem

Det här avsnittet innehåller anvisningar för att felsöka problem som uppstår när du laddar ned och kör skriptet för fil återställning.

## <a name="cannot-download-the-script"></a>Det går inte att hämta skriptet

Rekommenderad åtgärd:

1. Se till att du har alla [behörigheter som krävs för att ladda ned skriptet](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
2. Se till att det finns en anslutning till Azure Target IP (s).
Verifiera anslutningen genom att köra följande kommando från en upphöjd kommando tolk. 
    - *nslookup-Download.Microsoft.com* eller
    - *pinga download.microsoft.com*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>Skriptet har laddats ned, men kan inte köras

### <a name="for-sles-12-sp4-os-linux"></a>För SLES 12 SP4 OS (Linux)

Fel: iscsi_tcp_module inte hittas

Möjlig orsak: när python-skriptet kördes för återställning på objekt nivå på SUSE Linux OS-12sp4, Miss lyckas det med ett fel ***iscsi_tcp modulen inte kan läsas in** _. ILR-modulen använder iscsi_tcp för att upprätta en TCP-anslutning till säkerhets kopierings tjänsten, SUSE som en del av 12SP4-versionen togs bort iscsi_tcp från Open-iSCSI-paketet, därför att ILR-åtgärden Miss lyckas.

Rekommenderad åtgärd: körning av fil återställnings skript stöds inte på virtuella SUSE 12SP4-datorer. försök att återställa på en äldre version av SUSE 12SP4.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>Skriptet körs men det gick inte att ansluta till iSCSI-målet

Fel: ett undantag inträffade vid anslutning till målet

1. Se till att datorn där skriptet körs uppfyller alla [åtkomst krav](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
2. Kontrol lera anslutningen till Azures mål-IP (s).
Verifiera anslutningen genom att köra följande kommando från en upphöjd kommando tolk. 
    - _nslookup download.microsoft.com * eller<br>
    - *pinga download.microsoft.com*
3. Se till att iSCSI utgående port 3260 är tillgänglig.
4. Se till att det inte finns någon brand vägg/NSG som blockerar trafik till Azure Target IP (s) eller återställnings tjänst-URL: er.
5. Kontrol lera om antivirus programmet blockerar skript körningen.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>Ansluten till återställnings punkten men diskarna kopplades inte

Kontrol lera att du har [rätt dator för att köra skriptet](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

### <a name="on-windows-vm"></a>På virtuell Windows-dator

**Lagringspoolen på VM kopplas i skrivskyddat läge** För Windows 2012 R2 och Windows 2016 (med lagringspoolen) när du kör skriptet för första gången kan lagringspoolen som är kopplad till den virtuella datorn gå in i skrivskyddat läge.

För att lösa det här problemet måste vi manuellt ange Read-Write åtkomst till lagringspoolen för första gången och koppla de virtuella diskarna genom att följa stegen nedan:

1. Ange Read-Write åtkomst.
Navigera till Serverhanteraren > fil-och lagrings tjänster > volymer > lagringspooler.

   ![Windows-lagring](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. I fönstret **lagringspool** högerklickar du på den tillgängliga lagringspoolen och väljer **Ange Read-Write åtkomst** alternativ.

   ![Skriv skydd i Windows Storage](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. Vi måste koppla den virtuella disken när lagringspoolen har tilldelats Läs-/Skriv behörighet.
Navigera till **Serverhanteraren användar gränssnitt**, under avsnittet virtuella diskar > Högerklicka för att välja alternativet för att **bifoga virtuell disk** .

   ![Virtuell disk för Server hanteraren](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>På virtuella Linux-datorer

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>Fil återställning kan inte monteras automatiskt eftersom disken inte innehåller några volymer

När fil återställning utförs identifierar säkerhets kopierings tjänsten volymer och automatisk montering. Men om de säkerhetskopierade diskarna har RAW-partitioner, monteras inte dessa diskar automatiskt och det går inte att se data disken för återställning.

Lös problemet genom att följa stegen som beskrivs i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>Operativ systemet kunde inte identifiera fil systemet som gör att Linux-filåterställningen inte kunde köras medan monteringar diskar

När det inte gick att ansluta med skriptet för fil återställnings skriptet gick det inte att ansluta med följande fel: det *gick inte att montera följande partitioner eftersom operativ systemet inte kunde identifiera fil systemet*

- Lös problemet genom att kontrol lera om volymen är krypterad med ett program från tredje part. Om den är krypterad visas inte disken eller den virtuella datorn som krypterad på portalen.
1. Logga in på den säkerhetskopierade virtuella datorn och kör kommandot: *lsblk-f*<br>

   ![Disk utan volym](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Verifiera fil systemet och krypteringen.
3. Om volymen är krypterad stöds inte fil återställning. [Läs mer](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Diskarna är anslutna, men det går inte att montera volymer

- Se till att du har [rätt dator för att köra skriptet](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="on-windows-vm"></a>På virtuell Windows-dator

När du kör fil återställnings skriptet för Windows finns ett meddelande om att ***0 volymer är anslutna** _, men diskarna identifieras i disk hanterings konsolen. När du kopplar volymer via iSCSI, kan vissa volymer som identifieras gå till offline-tillstånd. När iSCSI-kanalen kommunicerar mellan den virtuella datorn och tjänsten identifierar den dessa volymer och tar dem online, men de är inte monterade.

   ![Disken är inte ansluten](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Utför följande steg för att identifiera och lösa problemet:

1. Gå till _ *disk hantering** genom att köra kommandot **diskmgmt** i cmd-fönstret.
2. Kontrol lera om ytterligare diskar visas. I exemplet nedan är disk 2 en ytterligare disk.

   ![Disk management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. Högerklicka på **volymen** och välj **ändra enhets beteckning och sökvägar**.

   ![Disk management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. I fönstret **Lägg till enhets beteckning eller sökväg** väljer du **tilldela följande enhets beteckning** och tilldelar en tillgänglig enhet och klickar på **OK**. 

   ![Disk management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. I Utforskaren kan du Visa enheten.

   ![Disk management3](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. De nya volymerna ska bifogas.  

   ![Disken monteras inte](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. I Utforskaren visas de nya volymerna när skriptet har körts igen.

   ![Disken är inte mounting1](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![Disken är inte mounting2](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Virtuella Linux-datorer 

- Se till att du har [rätt dator för att köra skriptet](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Om den skyddade virtuella Linux-datorn använder LVM-och/eller RAID-matriser följer du stegen som beskrivs i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>Det går inte att kopiera filerna från monterade volymer

Det kan hända att kopieringen Miss fungerar med ett fel 0x80070780: det går inte att komma åt filen i systemet. I det här fallet kontrollerar du om käll servern har disk-deduplicering aktive rad. Se sedan till att återställnings servern också har deduplicering aktive rad på enheterna. Du kan lämna Deduplicerings rollen avkonfigurerad så att du inte deduplicerar enheterna på återställnings servern.
