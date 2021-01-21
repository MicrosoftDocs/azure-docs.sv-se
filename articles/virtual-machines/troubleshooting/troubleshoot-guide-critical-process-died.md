---
title: Windows Stop-fel –
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 8279aee4a0df8155b23e6f984d259bf92555dc95
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632841"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows Stop-fel-#0x000000EF "kritisk process har dött"

Den här artikeln innehåller steg för att lösa problem där en kritisk process överlider under start i en virtuell Azure-dator.

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar fel *#0x000000EF* med meddelandets *kritiska process*.

!["Datorn stötte på ett problem och måste startas om. Vi har bara samlat in fel information och sedan kan du starta om. (# #% Complete) Om du vill veta mer kan du söka online senare för det här felet: 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Orsak

Detta beror vanligt vis på att en kritisk system process slutar fungera under start. Du kan läsa mer om kritiska process problem på "[bugg check 0xEF: CRITICAL_PROCESS_DIED](/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt:

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. Skapa och få åtkomst till en reparations-VM.
2. Åtgärda eventuella fel i operativ systemet.
3. **Rekommenderas**: innan du återskapar den virtuella datorn aktiverar du serie konsolen och samling av minnes dum par.
4. Återskapa den virtuella datorn.

> [!NOTE]
> Gäst operativ systemet fungerar inte när det här start felet påträffas. Du kommer att felsöka i offline-läge för att lösa det här problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
2. Använda Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="fix-any-os-corruption"></a>Åtgärda eventuella fel i operativ systemet

1. Öppna en kommando tolk med förhöjd behörighet.
2. Kör följande kommando för system fils kontroll (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Där < start DISK enhet > är start volymen för den virtuella reparations datorn (vanligt vis "C:") och < trasig DISK DRIVE > blir enhets beteckningen för den anslutna disken från den skadade virtuella datorn. Ersätt större än/mindre än symboler samt texten som finns i dem, t. ex. "< text här >", med lämplig bokstav.

3. Använd sedan [steg 5 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att sätta samman den virtuella datorn och se om den startas.
4. Om den virtuella datorn fortfarande inte startar kan du fortsätta att samla in minnesdumpen.

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpen

Om problemet kvarstår efter att du kört SFC, krävs analyser av en minnesdumpfil för att fastställa orsaken till problemet. Följ dessa steg om du vill samla in minnesdumpen:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny virtuell reparations dator

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en ny reparations-VM.
2. Använda Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka in ett support ärende

3. På den reparera virtuella datorn går du till Windows-mappen på den anslutna OS-disken. Om den driv rutins beteckning som är kopplad till den anslutna OS-disken är *F* måste du gå till *F:\Windows*.
4. Leta upp filen *Memory. dmp* och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpen.

   > [!NOTE]
   > Om du inte kan hitta dumpfilen slutför du stegen nedan för att aktivera samlings-och serie konsolen för minnes dum par och går sedan tillbaka till det här avsnittet och upprepar stegen i uppgiften ovan för att samla in minnesdumpen.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Rekommenderas: innan du återskapar den virtuella datorn aktiverar du serie konsolen och samling av minnes dum par

Om du vill aktivera samlings-och serie konsolen för minnes dum par kör du följande skript:

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör).
2. Kör följande kommandon:

   Aktivera serie konsol

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Ersätt större än eller mindre än symboler samt texten i dem, t. ex. "< text här >".

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
