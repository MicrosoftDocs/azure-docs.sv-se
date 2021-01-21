---
title: Det går inte att starta den virtuella Windows-datorn på grund av Windows Boot Manager
description: Den här artikeln innehåller steg för att lösa problem där Windows Boot Manager förhindrar start av en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5f83f4871d5cde23194ff51a90a22031b526cf91
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632571"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows VM kan inte starta på grund av Windows Boot Manager

Den här artikeln innehåller steg för att lösa problem där Windows Boot Manager förhindrar start av en virtuell Azure-dator (VM).

## <a name="symptom"></a>Symptom

Den virtuella datorn har fastnat i väntan på en användar-prompt och startar inte om du inte har fått instruktioner manuellt.

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar Windows Boot Manager med meddelandet *Välj ett operativ system att starta eller trycker på TABB för att välja ett verktyg:*.

Bild 1
 
![Windows Boot Manager säger "Välj ett operativ system att starta eller tryck på TAB för att välja ett verktyg:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Orsak

Felet beror på en BCD-flaggan *displaybootmenu* i Windows Boot Manager. När flaggan är aktive rad, ställer Windows Boot Manager till användaren, under start processen, för att välja vilken laddare de vill köra, vilket orsakar en start fördröjning. I Azure kan den här funktionen läggas till i den tid det tar att starta en virtuell dator.

## <a name="solution"></a>Lösning

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

Process översikt:

1. Konfigurera för snabbare start tid med hjälp av en serie konsol.
2. Skapa och få åtkomst till en reparations-VM.
3. Konfigurera för snabbare start på en virtuell reparations dator.
4. **Rekommenderas**: innan du återskapar den virtuella datorn aktiverar du serie konsolen och samling av minnes dum par.
5. Återskapa den virtuella datorn.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Konfigurera för snabbare start tid med hjälp av en serie konsol

Om du har åtkomst till en seriell konsol finns det två sätt att uppnå snabbare start tider. Förkorta antingen *displaybootmenu* vänte tid eller ta bort flaggan helt.

1. Följ anvisningarna för att komma åt [Azures serie konsol för Windows](./serial-console-windows.md) för att få åtkomst till den textbaserade konsolen.

   > [!NOTE]
   > Om du inte kan komma åt en seriell konsol kan du gå vidare till [skapa och få åtkomst till en reparations-VM](#create-and-access-a-repair-vm).

2. **Alternativ A**: minska vänte tid

   a. Vänte tiden anges till 30 sekunder som standard, men kan ändras till en snabbare tid (t. ex. 5 sekunder).

   b. Använd följande kommando i serie konsolen för att justera timeout-värdet:

      `bcdedit /set {bootmgr} timeout 5`

3. **Alternativ B**: ta bort BCD-flaggan

   a. Ange följande kommando för att förhindra att Start menyn i skärm bilden visas:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Om du inte kan använda serie konsolen för att konfigurera en snabbare start tid i stegen ovan, kan du i stället fortsätta med följande steg. Nu ska du felsöka i offline-läge för att lösa det här problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
2. Använd Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Konfigurera för snabbare start på en virtuell reparations dator

1. Öppna en kommando tolk med förhöjd behörighet.
2. Ange följande för att aktivera DisplayBootMenu:

   Använd det här kommandot för **virtuella datorer i generation 1**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Använd det här kommandot för **virtuella datorer i generation 2**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Ersätt större än eller mindre än symboler samt texten i dem, t. ex. "< text här >".

3. Ändra timeout-värdet till 5 sekunder:

   Använd det här kommandot för **virtuella datorer i generation 1**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Använd det här kommandot för **virtuella datorer i generation 2**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Ersätt större än eller mindre än symboler samt texten i dem, t. ex. "< text här >".

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
