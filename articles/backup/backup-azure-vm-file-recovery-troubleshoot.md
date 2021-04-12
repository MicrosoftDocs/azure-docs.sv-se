---
title: Felsöka återställning av virtuella Azure-filer
description: Felsök problem när du återställer filer och mappar från en virtuell Azure-säkerhetskopiering.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700311"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Felsöka problem vid fil återställning av en virtuell Azure-säkerhetskopiering

Den här artikeln innehåller fel söknings steg som kan hjälpa dig att lösa problem med återställning av filer och mappar från en säkerhets kopia av en virtuell Azure-dator (VM).

## <a name="common-error-messages"></a>Vanliga felmeddelanden

Det här avsnittet innehåller anvisningar för att felsöka fel meddelanden som kan visas.

### <a name="exception-caught-while-connecting-to-target"></a>"Undantag påträffades vid anslutning till målet"

**Möjlig orsak**: skriptet kan inte komma åt återställnings punkten.

**Rekommenderad åtgärd**: om du vill lösa det här problemet följer du stegen som anges i [skriptet, men anslutningen misslyckades](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"Målet har redan loggats in via en iSCSI-session"

**Möjlig orsak**: skriptet har redan körts på samma dator och enheterna har anslutits.

**Rekommenderad åtgärd**: volymerna för återställnings punkten har redan bifogats. De kan inte monteras med samma enhets beteckning på den ursprungliga virtuella datorn. Bläddra igenom de tillgängliga volymerna i Utforskaren.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"Det här skriptet är ogiltigt eftersom diskarna har demonterats via portalen/överskridit gränsen på 12 timmar. Hämta ett nytt skript från portalen "

**Möjlig orsak**: diskarna har demonterats från portalen eller så överskreds tids gränsen på 12 timmar.

**Rekommenderad åtgärd**: 12 timmar efter att du hämtat skriptet blir det ogiltigt och kan inte köras. Gå till portalen och ladda ned ett nytt skript för att fortsätta med fil återställningen.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>Det går inte att läsa in iscsi_tcp_module eller så går det inte att hitta modulen

**Rekommenderad åtgärd**: Lös problemet genom att följa stegen i [skript hämtningarna, men inte köra](#the-script-downloads-successfully-but-fails-to-run).

## <a name="common-problems"></a>Vanliga problem

Det här avsnittet innehåller anvisningar för att felsöka vanliga problem som kan uppstå när du laddar ned och kör skriptet för fil återställning.

### <a name="you-cant-download-the-script"></a>Du kan inte ladda ned skriptet

1. Se till att du har de [behörigheter som krävs för att ladda ned skriptet](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script).
1. Kontrol lera anslutningen till Azure-mål-IP-adresserna. Kör något av följande kommandon från en upphöjd kommando tolk:

   `nslookup download.microsoft.com`

    eller

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Skriptet har laddats ned, men kan inte köras

När du kör python-skriptet för återställning på objekt nivå (ILR) på SUSE Linux Enterprise Server 12 SP4 Miss lyckas fel meddelandet "det går inte att läsa in" iscsi_tcp-modulen "eller" iscsi_tcp_module inte hittas ".

**Möjlig orsak**: ILR-modulen använder **iscsi_tcp** för att upprätta en TCP-anslutning till säkerhets kopierings tjänsten. Som en del av SLES 12 SP4-versionen, tar SUSE bort **iscsi_tcp** från Open-iSCSI-paketet, så ILR-åtgärden Miss lyckas.

**Rekommenderad åtgärd**: skript körning för fil återställning stöds inte på SUSE 12 SP4-datorer. Försök utföra återställningen på en äldre version av SUSE 12 SP4.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>Skriptet körs men det gick inte att ansluta till iSCSI-målet

Du kan se fel meddelandet "undantag påträffades vid anslutning till mål".

1. Se till att datorn där skriptet körs uppfyller [åtkomst kraven](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script).
1. Kontrol lera anslutningen till Azure-mål-IP-adresserna. Kör något av följande kommandon från en upphöjd kommando tolk:

   `nslookup download.microsoft.com`

   eller

   `ping download.microsoft.com`
1. Se till att iSCSI utgående port 3260 används.
1. Sök efter en brand vägg eller NSG blockerar trafik till Azure Target IP-adresser eller återställnings tjänst-URL: er.
1. Kontrol lera att antivirus programmet inte blockerar skript körningen.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>Du är ansluten till återställnings punkten, men diskarna har inte anslutits

Lös problemet genom att följa stegen för ditt operativ system.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>Windows fil återställning fungerar inte på servern med lagringspooler

När du kör skriptet för första gången på Windows Server 2012 R2 och Windows Server 2016 (med lagringspooler) kan lagringspoolen vara kopplad till den virtuella datorn i skrivskyddat läge.

>[!Tip]
> Se till att du har [rätt dator för att köra skriptet](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Lös problemet genom att manuellt tilldela läs-och Skriv behörighet till lagringspoolen och koppla de virtuella diskarna:

1. Gå till **Serverhanteraren**  >  lagrings enheter för **fil-och lagrings tjänster**  >    >  .

   ![Skärm bild som visar alternativ för lagringspooler.](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. I fönstret **lagringspool** högerklickar du på den tillgängliga lagringspoolen och väljer **Ange Read-Write åtkomst**.

   ![Skärm bild som visar de högerklickna alternativen för en lagringspool.](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. När lagringspoolen har tilldelats Läs-och Skriv behörighet högerklickar du på avsnittet **virtuella diskar** och väljer sedan **Anslut virtuell disk**.

   ![Skärm bild som visar alternativ för att högerklicka på en virtuell disk.](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>Det går inte att montera Linux-filåterställning automatiskt eftersom disken inte innehåller några volymer

När du utför fil återställning identifierar säkerhets kopierings tjänsten volymer och automatisk montering. Men om de säkerhetskopierade diskarna har RAW-partitioner, monteras inte diskarna automatiskt och du kan inte se data disken för återställning.

Lös problemet genom att gå till [återställa filer från säkerhets kopiering av virtuella Azure-datorer](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>Det går inte att återställa Linux-filen eftersom operativ systemet inte kunde identifiera fil systemet

När du kör fil återställnings skriptet går det inte att ansluta data disken. Du ser att det inte gick att montera följande partitioner eftersom det inte gick att identifiera fil systemet "fel i operativ systemet".

Lös problemet genom att kontrol lera om volymen är krypterad med ett program från tredje part. Om den är krypterad visas inte disken eller den virtuella datorn som krypterad på portalen.

1. Logga in på den säkerhetskopierade virtuella datorn och kör det här kommandot:

   `lsblk -f`

   ![Skärm bild som visar resultatet av kommandot för att visa en lista över block enheter.](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Verifiera fil systemet och krypteringen. Om volymen är krypterad stöds inte fil återställning. Läs mer i [support mat ris för säkerhets kopiering av virtuella Azure-datorer](./backup-support-matrix-iaas.md#support-for-file-level-restore).

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>Diskar är anslutna, men volymerna är inte monterade

Lös problemet genom att följa stegen för ditt operativ system.

#### <a name="windows"></a>Windows

När du kör fil återställnings skriptet för Windows visas meddelandet "0 återställnings volymer är anslutna". Men diskarna identifieras i disk hanterings konsolen.

**Möjlig orsak**: när du har kopplat volymer via iSCSI, gick en del volymer som identifierades offline. När iSCSI-kanalen kommunicerar mellan den virtuella datorn och tjänsten, identifierar den dessa volymer och ansluter dem online, men de monteras inte.

   ![Skärm bild som visar de 0 återställnings volymer som är anslutna.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Utför följande steg för att identifiera och lösa problemet:

>[!Tip]
>Se till att du har [rätt dator för att köra skriptet](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

1. Öppna **disk hantering** genom att köra **diskmgmt** i **kommando** fönstret.
1. Sök efter eventuella ytterligare diskar. I följande exempel är **disk 2** en ytterligare disk.

   ![Skärm bild av disk hanterings fönstret med ytterligare disk.](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Högerklicka på **ny volym** och välj sedan **ändra enhets beteckning och sökvägar**.

   ![Skärm bild som visar de högerklickna alternativen på den extra disken.](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. I fönstret **ändra enhets beteckning eller sökväg** väljer du **tilldela följande enhets beteckning**, tilldelar en tillgänglig enhet och väljer sedan **OK**.

   ![Skärm bild av fönstret Ändra enhets beteckning eller sökväg.](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Öppna Utforskaren för att visa den enhet du valde och utforska filerna.

#### <a name="linux"></a>Linux

>[!Tip]
>Se till att du har [rätt dator för att köra skriptet](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Om den skyddade virtuella Linux-datorn använder LVM-eller RAID-matriser följer du stegen i [återställa filer från säkerhets kopian av virtuella Azure-datorer](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>Du kan inte kopiera filerna från monterade volymer

Kopieringen kanske inte fungerar med felet "0x80070780: det går inte att komma åt filen av systemet". 

Kontrol lera om disk deduplicering har Aktiver ATS på käll servern. Om det gör det, se till att återställnings servern också har deduplicering aktiverat på enheterna. Du kan lämna dedupliceringen avkonfigurerad så att du inte deduplicerar enheterna på återställnings servern.

## <a name="next-steps"></a>Nästa steg

- [Återställa filer och mappar från säkerhets kopiering av virtuella Azure-datorer](backup-azure-restore-files-from-vm.md)