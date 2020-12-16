---
title: Felsöka återställning av virtuella Azure-filer
description: Felsök problem när du återställer filer och mappar från en virtuell Azure-säkerhetskopiering.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: bd369577e320cf6dca510183948f41e6cf91779b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605300"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Fel söknings problem vid fil återställning av virtuell Azure-säkerhetskopiering

Den här artikeln innehåller fel söknings steg som kan hjälpa dig att lösa Azure Backup fel som rör problem när du återställer filer och mappar från en virtuell Azure-säkerhetskopiering.

## <a name="common-error-messages"></a>Vanliga felmeddelanden

### <a name="exception-caught-while-connecting-to-target"></a>Ett undantag inträffade vid anslutning till målet

**Möjlig orsak**: skriptet kan inte komma åt återställnings punkten.

**Rekommenderad åtgärd**: kontrol lera om datorn uppfyller alla [åtkomst krav](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>Målet har redan loggats in via en iSCSI-session

**Möjlig orsak**: skriptet har redan körts på samma dator och enheterna har anslutits.

**Rekommenderad åtgärd**: volymerna för återställnings punkten har redan bifogats. De kan inte monteras med samma enhets beteckning på den ursprungliga virtuella datorn. Bläddra igenom alla tillgängliga volymer i Utforskaren.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Skriptet är ogiltigt eftersom diskarna har demonterats via portalen/överskridit gränsen på 12 timmar. Hämta ett nytt skript från portalen

**Möjlig orsak**: diskarna har demonterats från portalen eller så överskreds gränsen på 12 timmar.

**Rekommenderad åtgärd**: skriptet är ogiltigt efter 12 timmar från den tid det hämtades och kan inte köras. Besök portalen och hämta ett nytt skript för att fortsätta med fil återställningen.

## <a name="troubleshooting-common-scenarios"></a>Felsöka vanliga scenarier

Det här avsnittet innehåller anvisningar för att felsöka problem som kan uppstå när du laddar ned och kör skriptet för fil återställning.

### <a name="cant-download-the-script"></a>Det går inte att hämta skriptet

Rekommenderad åtgärd:

1. Se till att du har alla [behörigheter som krävs för att ladda ned skriptet](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
1. Se till att det finns en anslutning till Azure Target-IP: er

Verifiera anslutningen genom att köra något av följande kommandon från en upphöjd kommando tolk.

`nslookup download.microsoft.com`

eller

`ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Skriptet har laddats ned, men kan inte köras

#### <a name="for-sles-12-sp4-os-linux"></a>För SLES 12 SP4 OS (Linux)

**Fel**: iscsi_tcp_module inte hittas

**Möjlig orsak**: när python-skriptet kördes för återställning på objekt nivå (ILR) på SUSE Linux OS-12sp4, Miss lyckas det med felet **_iscsi_tcp modulen inte kan läsas in_* _.

ILR-modulen använder _ *iscsi_tcp** för att upprätta en TCP-anslutning till säkerhets kopierings tjänsten. Som en del av 12SP4-versionen tar SUSE bort **iscsi_tcp** från Open-iSCSI-paketet, så ILR-åtgärden Miss lyckas.

**Rekommenderad åtgärd**: skript körning för fil återställning stöds inte på SUSE 12SP4-VM: ar. Försök utföra återställningen på en äldre version av SUSE 12SP4.

### <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>Skriptet körs men det gick inte att ansluta till iSCSI-målet

**Fel**: ett undantag inträffade vid anslutning till målet

1. Se till att datorn där skriptet körs uppfyller alla [åtkomst krav](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
1. Kontrol lera anslutningen till Azures mål-IP.
Verifiera anslutningen genom att köra något av följande kommandon från en upphöjd kommando tolk.

   `nslookup download.microsoft.com`

   eller

   `ping download.microsoft.com`
1. Se till att du har åtkomst till den utgående iSCSI-porten 3260.
1. Se till att det inte finns någon brand vägg eller NSG som blockerar trafik till Azure Target IP-adresser eller Recovery Service-webbadresser.
1. Kontrol lera om antivirus programmet blockerar skript körningen.

### <a name="connected-to-recovery-point-but-disks-didnt-get-attached"></a>Ansluten till återställnings punkten men diskarna kopplades inte

Kontrol lera att du har [rätt dator för att köra skriptet](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

#### <a name="on-a-windows-vm"></a>På en virtuell Windows-dator

**Lagringspoolen på den virtuella datorn är i skrivskyddat läge**: på Windows 2012 R2 och Windows 2016 (med lagringspooler) när du kör skriptet för första gången, kan lagringspoolen som är kopplad till den virtuella datorn försättas i ett skrivskyddat tillstånd.

För att lösa det här problemet måste vi manuellt ange Read-Write åtkomst till lagringspoolen för första gången och koppla de virtuella diskarna. Följ stegen nedan:

1. Ange Read-Write åtkomst.

   Navigera till **Serverhanteraren**  >  lagrings enheter för **fil-och lagrings tjänster**  >    >  .

   ![Windows-lagring](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. I fönstret **lagringspool** högerklickar du på den tillgängliga lagringspoolen och väljer alternativet **Ange Read-Write åtkomst** .

   ![Skriv skydd i Windows Storage](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. När lagringspoolen har tilldelats Läs-/Skriv behörighet ansluter du den virtuella disken.

   Navigera till **SERVERHANTERAREN UI**. Under avsnittet **virtuella diskar** > Högerklicka för att välja alternativet för att **bifoga virtuell disk** .

   ![Virtuell disk för Server hanteraren](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="on-a-linux-vm"></a>På en virtuell Linux-dator

##### <a name="file-recovery-fails-to-auto-mount-because-disk-doesnt-contain-volumes"></a>Fil återställning kan inte monteras automatiskt eftersom disken inte innehåller volymer

När fil återställning utförs identifierar säkerhets kopierings tjänsten volymer och monteringar. Men om de säkerhetskopierade diskarna har RAW-partitioner monteras inte dessa diskar och du kan inte se data disken för återställning.

Lös problemet genom att följa stegen som beskrivs i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

##### <a name="the-os-couldnt-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>Operativ systemet kunde inte identifiera det fil system som gör att Linux-filåterställningen inte kunde köras medan monteringar diskar

När fil återställnings skriptet kördes, gick det inte att ansluta data disken med följande fel:

"Det gick inte att montera följande partitioner eftersom operativ systemet inte kunde identifiera fil systemet"

Lös problemet genom att kontrol lera om volymen är krypterad med ett program från tredje part. Om den är krypterad visas inte disken eller den virtuella datorn som krypterad på portalen.

1. Logga in på den säkerhetskopierade virtuella datorn och kör kommandot:

   `*lsblk -f*`

   ![Disk utan volym](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Verifiera fil systemet och krypteringen.
3. Om volymen är krypterad stöds inte fil återställning. [Läs mer](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

### <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Diskarna är anslutna, men det går inte att montera volymer

- Se till att du har [rätt dator för att köra skriptet](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

#### <a name="on-windows-vms"></a>På virtuella Windows-datorer

När du kör fil återställnings skriptet för Windows är det ett meddelande om att ***0 volymer är kopplade** _. Men diskarna identifieras i disk hanterings konsolen. När du kopplar volymer via iSCSI, kan vissa volymer som identifierats gå till offline-tillstånd. När iSCSI-kanalen kommunicerar mellan den virtuella datorn och tjänsten, identifierar den dessa volymer och ansluter dem online, men de monteras inte.

   ![Disken är inte ansluten](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Utför följande steg för att identifiera och lösa problemet:

1. Gå till _ *disk hantering** genom att köra kommandot **diskmgmt** i cmd-fönstret.
1. Kontrol lera om ytterligare diskar visas. I följande exempel är disk 2 en ytterligare disk.

   ![Disk management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Högerklicka på den **nya volymen** och välj **ändra enhets beteckning och sökvägar**.

   ![Disk management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. I fönstret **Lägg till enhets beteckning eller sökväg** väljer du **tilldela följande enhets beteckning** och tilldelar en tillgänglig enhet och väljer **OK**.

   ![Disk management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. I Utforskaren visar du den enhets beteckning som du har valt och utforskar filerna.

#### <a name="on-linux-vms"></a>Virtuella Linux-datorer

- Se till att du har [rätt dator för att köra skriptet](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Om den skyddade virtuella Linux-datorn använder LVM-eller RAID-matriser följer du stegen som beskrivs i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

### <a name="cant-copy-the-files-from-mounted-volumes"></a>Det går inte att kopiera filerna från monterade volymer

Det kan hända att kopieringen Miss känner till felet **0x80070780: det går inte att komma åt filen i systemet.** I det här fallet kontrollerar du om käll servern har disk-deduplicering aktive rad. Se sedan till att återställnings servern också har deduplicering aktive rad på enheterna. Du kan lämna Deduplicerings rollen avkonfigurerad så att du inte deduplicerar enheterna på återställnings servern.

## <a name="next-steps"></a>Nästa steg

- [Återställa filer och mappar från säkerhets kopiering av virtuella Azure-datorer](backup-azure-restore-files-from-vm.md)
