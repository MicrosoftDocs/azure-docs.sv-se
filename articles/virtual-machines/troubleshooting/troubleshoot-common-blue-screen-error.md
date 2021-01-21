---
title: Blå skärms fel vid start av en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker problemet att det blå skärms felet tas emot vid start av | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 9a95ddf882e5edba9daa8ff91c02d1df1f50bceb
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632984"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows visar blå skärms fel vid start av en virtuell Azure-dator
I den här artikeln beskrivs blå skärms fel som kan uppstå när du startar en virtuell Windows-dator (VM) i Microsoft Azure. Den innehåller steg som hjälper dig att samla in data för ett support ärende. 


## <a name="symptom"></a>Symptom 

En virtuell Windows-dator startar inte. När du kontrollerar start skärmarna i [startdiagnostik](./boot-diagnostics.md)kan du se något av följande fel meddelanden på en blå skärm:

- DATORN stötte på ett problem och måste startas om. Vi har bara samlat in fel information och sedan kan du starta om.
- DATORN stötte på ett problem och måste startas om.

Det här avsnittet innehåller vanliga fel meddelanden som du kan stöta på när du hanterar virtuella datorer:

## <a name="cause"></a>Orsak

Det kan finnas flera orsaker till varför du får ett stoppfel. De vanligaste orsakerna är:

- Problem med en driv rutin
- Skadad system fil eller minne
- Ett program får åtkomst till en förbjuden sektor i minnet

## <a name="collect-memory-dump-file"></a>Samla in minnesdump

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

För att lösa det här problemet måste du först samla in dumpfilen-filen för kraschen och kontakta supporten med dump-filen. Samla in dumpfilen genom att följa dessa steg:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. Ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla OS-disken till en virtuell dator för återställning](./troubleshoot-recovery-disks-portal-windows.md). 
3. Fjärr skrivbord till den virtuella återställnings datorn.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Hitta en dumpfil och skicka ett support ärende

1. På den virtuella datorn för återställning går du till Windows-mappen på den anslutna OS-disken. Om den driv rutins beteckning som har tilldelats till den anslutna OS-disken är F, måste du gå till F:\Windows.
2. Leta upp filen Memory. dmp och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med dumpfilen. 

Om du inte hittar dumpfilen går du vidare till nästa steg för att aktivera dumpa logg och seriell konsol.

### <a name="enable-dump-log-and-serial-console"></a>Aktivera dumpa logg och seriell konsol

Kör följande skript för att aktivera dumpa logg och seriell konsol.

1. Öppna en upphöjd kommando tolk session (kör som administratör).
2. Kör följande skript:

    I det här skriptet antar vi att enhets beteckningen som är kopplad till den anslutna OS-disken är F.  Ersätt det med lämpligt värde i den virtuella datorn.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Kontrol lera att det finns tillräckligt med utrymme på disken för att allokera så mycket minne som RAM-minnet, vilket beror på den storlek som du väljer för den här virtuella datorn.
    2. Om det inte finns tillräckligt med utrymme eller om det här är en stor virtuell dator (G, GS eller E-serien) kan du sedan ändra den plats där filen kommer att skapas och se om det finns någon annan datadisk som är kopplad till den virtuella datorn. För att göra detta måste du ändra följande nyckel:

    ```config-reg
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

    reg unload HKLM\BROKENSYSTEM
    ```

3. [Koppla från OS-disken och återanslut sedan OS-disken till den berörda virtuella datorn](./troubleshoot-recovery-disks-portal-windows.md).
4. Starta den virtuella datorn för att återskapa problemet, så genereras en dumpfil.
5. Koppla OS-disken till en virtuell dator för återställning, samla in dumpfilen och [skicka sedan ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med dumpfilen.
