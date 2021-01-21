---
title: Startfel – "det här är inte en startbar disk"
description: Den här artikeln innehåller steg för att lösa problem där disken inte är startbar på en virtuell Azure-dator
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 14da41815e177ece64c72ac27a7cb126e69fdc62
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633196"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Startfel – det här är inte en startbar disk

Den här artikeln innehåller steg för att lösa problem där disken inte är startbar på en virtuell Azure-dator (VM).

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar en prompt med meddelandet "Detta är inte en startbar disk. Sätt in en startbar diskett och tryck på valfri tangent för att försöka igen....

   Bild 1

   ![Bild 1 visar meddelandet * "det här är inte en startbar disk. Sätt in en startbar diskett och tryck på valfri tangent för att försöka igen... "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Orsak

Det här fel meddelandet innebär att start processen för operativ systemet inte kunde hitta en aktiv systempartition. Det här felet kan också innebära att det finns en referens som saknas i Boot Configuration Datas arkivet (BCD), vilket förhindrar att den hittar Windows-partitionen.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. Skapa och få åtkomst till en reparations-VM.
2. Ange partitionerings status till aktiv.
3. Åtgärda diskpartitionen.
4. **Rekommenderas**: innan du återskapar den virtuella datorn aktiverar du serie konsolen och samling av minnes dum par.
5. Återskapa den ursprungliga virtuella datorn.

   > [!NOTE]
   > Gäst operativ systemet fungerar inte när det här start felet påträffas. Du ska felsöka i offline-läge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
2. Använda Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="set-partition-status-to-active"></a>Ange partitionerings status till aktiv

Virtuella datorer i generation 1 bör först kontrol lera att OS-partitionen som innehåller BCD-arkivet är markerad som *aktiv*. Om du har en virtuell dator i generation 2 kan du gå vidare till [Reparera diskpartitionen](#fix-the-disk-partition), eftersom *status* flaggan var inaktuell i senare generation.

1. Öppna en kommando tolk med förhöjd behörighet *(cmd.exe)*.
2. Starta DiskPart-verktyget genom att ange *DiskPart* .
3. Ange *list disk* för att visa en lista över diskarna i systemet och identifiera den anslutna OS-VHD: n.
4. När den anslutna OS-VHD: n finns anger du markera disk *#* för att välja disken.  Se bild 2, där disk 1 är den anslutna OS-VHD: n.

   Bild 2

   ![Bild 2 visar fönstret * DISKPART * som visar utdata från list disk kommandot, disk 0 och disk 1 som visas i tabellen.  Visar även utdata från kommandot Markera disk 1, disk 1 är den valda disken](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. När disken har valts anger du *list partition* för att visa en lista över partitionerna för den valda disken.
6. När startpartitionen har identifierats anger du markera *partition #* för att välja partitionen.  Vanligt vis är startpartitionen cirka 350 MB stor.  Se bild 3, där partition 1 är startpartitionen.

   Bild 3

   ![Bild 3 visar fönstret * DISKPART * med utdata från kommandot * List partition *. Partition 1 och partition 2 visas i tabellen. Det visar även utdata för kommandot * selected partition 1 * när partition 1 är den valda disken.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Ange "Detail partition" för att kontrol lera partitionens status. Se bild 4, där partitionen är *aktiv: Nej* eller bild 5, där partitionen är "aktiv: Ja".

   Bild 4

   ![Bild 4 visar fönstret * DISKPART * med utdata från kommandot * detail partition * när partition 1 har angetts till * Active: Nej *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Figur 5

   ![Bild 5 visar fönstret * DISKPART * med utdata från kommandot * detail partition * när partition 1 har angetts till * Active: Yes *.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Om partitionen inte är **aktiv** anger du *aktiv* för att ändra den *aktiva* flaggan.
9. Kontrol lera att status ändringen har gjorts korrekt genom att skriva in *information partition*.

   Bild 6

   ![Bild 6 visar DiskPart-fönstret med utdata från * detail partition * kommandot när partition 1 har angetts till * Active: Yes *](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Välj *Avsluta* för att stänga DiskPart-verktyget och spara konfigurations ändringarna.

### <a name="fix-the-disk-partition"></a>Åtgärda diskpartitionen

1. Öppna en kommando tolk med förhöjd behörighet (cmd.exe).
2. Använd följande kommando för att köra *chkdsk* på diskarna och åtgärda fel:

   `chkdsk <DRIVE LETTER>: /f`

   Om du lägger till kommando alternativet/f åtgärdas eventuella fel på disken. Ersätt <DRIVE LETTER> med bokstaven för den anslutna OS-VHD: n.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Rekommenderas: innan du återskapar den virtuella datorn aktiverar du serie konsolen och samling av minnes dum par

Om du vill aktivera samlings-och serie konsolen för minnes dum par kör du följande skript:

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör).
2. Kör följande kommandon:

   Aktivera serie konsol

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Kontrol lera att det lediga utrymmet på OS-disken är så mycket som minnes storleken (RAM) på den virtuella datorn.

   Om det inte finns tillräckligt med utrymme på OS-disken, bör du ändra platsen där minnesdumpen skapas och se om det finns en datadisk som är ansluten till den virtuella datorn som har tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter du "% SystemRoot%" med enhets beteckningen (till exempel "F:") för data disken i följande kommandon.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Föreslagen konfiguration för att aktivera OS-dump

**Läs in trasig OS-disk**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Aktivera på ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Aktivera på ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Ta bort skadad OS-disk:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Återskapa den ursprungliga virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att bygga upp den virtuella datorn igen.
