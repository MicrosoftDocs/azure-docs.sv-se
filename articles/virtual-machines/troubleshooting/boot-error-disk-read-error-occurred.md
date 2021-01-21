---
title: Felsöka startfel – disk läsnings fel inträffade
description: Den här artikeln innehåller steg för att lösa problem där det inte går att läsa disken på en virtuell Azure-dator.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: 75d1cf8638f922bb0275322568eb1399db4f49e8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629733"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>Felsöka startfel – disk läsnings fel inträffade

Den här artikeln innehåller steg för att lösa problem där det inte går att läsa disken på en virtuell Azure-dator (VM).

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar en fråga med meddelandet "ett disk läsnings fel uppstod. Tryck på Ctrl+Alt+Del för att starta om".

   ![Fel meddelande: ett disk läsnings fel inträffade. Tryck på Ctrl + Alt + del för att starta om.](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>Orsak

Det här fel meddelandet anger att disk strukturen är skadad och oläslig. Om du använder en virtuell dator i generation 1, är det också möjligt att diskpartitionen som innehåller start konfigurations data inte är **aktive** rad.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. Skapa och få åtkomst till en reparations-VM.
1. Välj en lösning:
   - [Ange partitionerings status till aktiv](#set-partition-status-to-active)
   - [Åtgärda diskpartitionen](#fix-the-disk-partition)
1. Aktivera serie konsol och samling av minnes dum par.
1. Återskapa den virtuella datorn.

> [!NOTE]
> När det här startfelet påträffas fungerar inte gäst operativ systemet (OS). Du ska felsöka i offline-läge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
1. Anslut till den virtuella reparations datorn med hjälp av Anslutning till fjärrskrivbord.

### <a name="set-partition-status-to-active"></a>Ange partitionerings status till aktiv

Virtuella datorer i generation 1 bör först kontrol lera att den OS-partition som innehåller BCD-arkivet är markerad som **aktiv**. Om du har en virtuell dator i generation 2 kan du gå vidare till [Reparera diskpartitionen](#fix-the-disk-partition), eftersom status flaggan var inaktuell i senare generation.

1. Öppna en kommando tolk med förhöjd behörighet (cmd.exe).
1. Starta **DiskPart-verktyget genom** att ange **DiskPart** .
1. Ange **list disk** för att visa en lista över diskarna i systemet och identifiera den anslutna virtuella hård disken (VHD).
1. När den anslutna OS-VHD: n finns anger du markera disk **#** för att välja disken. I följande bild visas ett exempel på var disk 1 är den anslutna OS-VHD: n.

   ![DiskPart-fönstret med utdata från kommandot * * list disk * *, där disk 0 och disk 1 visas i tabellen. I fönstret visas också utdata från kommandot * * sökdisk 1 * *, där disk 1 är den valda disken](./media/disk-read-error-occurred/2.png)

1. När disken har valts anger du **list partition** för att visa en lista över partitionerna för den valda disken.
1. När startpartitionen har identifierats anger du markera **partition #** för att välja partitionen. Startpartitionen är ofta ungefär 350 MB.  Se följande bild till exempel där partition 1 är startpartitionen.

   ![DiskPart-fönstret med utdata från kommandot * * List partition * *, där partition 1 och partition 2 visas i tabellen. I fönstret visas också utdata från kommandot * * selected partition 1 * *, där partition 1 är den valda disken.](./media/disk-read-error-occurred/3.png)

1. Ange **information partition** för att kontrol lera partitionens status. Se följande skärm bilder om du har exempel på partitionen som är **aktiv: Nej** eller **aktiv: Ja**.

   **Aktiv: Nej**

   ![DiskPart-fönstret med utdata från kommandot * * detail partition * *, där partition 1 är inställt på * * Active: Nej * *.](./media/disk-read-error-occurred/4.png)

   **Aktiv: Ja**

   ![DiskPart-fönstret med utdata från kommandot * * detail partition * *, där partition 1 är inställt på * * Active: Yes * *.](./media/disk-read-error-occurred/5.png)

1. Om partitionen inte är **aktive** rad, anger du **aktiv** för att ändra den aktiva flaggan.
1. Ange **detaljerad partition** för att kontrol lera att status ändringen har slutförts korrekt och kontrol lera att utdata inkluderar **Active: Ja**. 
1. Välj **Avsluta** för att stänga DiskPart-verktyget och spara konfigurations ändringarna.

### <a name="fix-the-disk-partition"></a>Åtgärda diskpartitionen

1. Öppna en kommando tolk med förhöjd behörighet (cmd.exe).
1. Använd följande kommando för att köra **chkdsk** på diskarna och utföra fel korrigeringar:

   `chkdsk <DRIVE LETTER>: /f`

   Om du lägger till kommandot **/f** kan du åtgärda eventuella fel på disken. Se till att ersätta **< enhets BEteckningar >** med bokstaven för den anslutna OS-VHD: n.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Aktivera samlings konsolen och minnes dumpnings samlingen

**Rekommenderas**: innan du återskapar den virtuella datorn aktiverar du samlings konsolen och samlings dumpningen genom att köra följande skript:

1. Öppna en upphöjd kommando tolks session som administratör.
1. Kör följande kommandon:

   **Aktivera serie konsolen**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Kontrol lera att det lediga utrymmet på OS-disken är större än minnes storleken (RAM) på den virtuella datorn.

   Om det inte finns tillräckligt med utrymme på OS-disken, ändrar du den plats där minnesdumpen skapas och kontrollerar platsen till alla data diskar som är anslutna till den virtuella datorn med tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter du **% systemroot%** med enhets bokstaven för data disken, t. ex. **F:**, i följande kommandon.

   Föreslagen konfiguration för att aktivera OS-dump:

   **Läs in registrerings data fil från den trasiga OS-disken:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Aktivera på ControlSet001:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Aktivera på ControlSet002:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Ta bort skadad OS-disk:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Återskapa den virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att återskapa den virtuella datorn.
