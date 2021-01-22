---
title: Windows-stoppfel – maskinvarufel
description: Den här artikeln innehåller steg för att lösa problem där virtuella datorer med Windows Server 2008 kraschar med ett fel meddelande som anger att det uppstod ett maskin varu fel.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 89faa5b29e0a972f31ad51a7354635a53176541a
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661365"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Windows-stoppfel – maskinvarufel

Den här artikeln innehåller steg för att lösa problem där virtuella datorer med Windows Server 2008 kraschar med ett fel meddelande som anger att det uppstod ett maskin varu fel.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar en blå skärm med meddelandet:

**\*\*\* Maskin varu fel**

**Kontakta leverantören för support**

**\*\*\* Systemet har stoppats \*\*\***

#### <a name="blue-screen"></a>Blå skärm

![Skärm bilden visar en blå skärm maskin vara fel krasch.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Seriekonsol

![Skärm bilden visar meddelandet "maskin varu fel" i serie konsol funktionen om serie konsolen har Aktiver ATS.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Orsak

Den här skärmen visas när gäst operativ systemet inte har kon figurer ATS korrekt och ett icke-Maskbart avbrott (NMI) har skickats. Fel meddelandet anger att ett program i kernelläge genererade ett undantag, som hanteraren inte fångade. Du kan identifiera vilket undantag som genererades genom att samla in en minnesdump.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt 

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. Konfigurera register nyckeln för icke-Maskbart avbrott (NMI) 
2. Skapa och få åtkomst till en virtuell reparations dator 
3. Aktivera serie konsol och samling av minnes dum par 
4. Återskapa den virtuella datorn 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>Konfigurera register nyckeln för icke-Maskbart avbrott (NMI)

1. Med hjälp av Azure Portal startar du om den virtuella datorn så att gäst operativ systemet startar normalt. 
2. När en åtkomst till den virtuella datorn har återställts, öppnar du en kommando tolk med förhöjd behörighet (kör som administratör). 
3. Konfigurera register nyckeln NMI med följande kommando:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [Visa ytterligare information om kommandot REG ADD](/windows-server/administration/windows-commands/reg-add)
4. *(Valfritt)* Samling av minnes dum par:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Valfritt)* Konfigurera åtkomst till serie konsol:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [Visa ytterligare information om BCDEDIT-kommandot](/windows-server/administration/windows-commands/bcdedit)
6. Starta om den virtuella datorn med följande kommando:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [Visa ytterligare information om kommandot SHUTDOWN](/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> Problemet bör nu korrigeras!

> [!NOTE]
> När du har startat om testar du den virtuella datorn för att kontrol lera att den fungerar som vanligt. Om du fortfarande har problem kan du fortsätta till nästa avsnitt för ytterligare instruktioner.

> [!TIP]
> Vi rekommenderar att du konfigurerar register nyckeln för icke-Maskable-avbrott (NMI) i avsnittet ovan, men om den virtuella datorn inte startade normalt efteråt kan det hända att ändringar av gäst operativ system registret inte har skett. I så fall kan du följa anvisningarna nedan för att manuellt lägga till register inställningarna i stället.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
2. Anslut till den reparerade virtuella datorn med Anslutning till fjärrskrivbord.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Aktivera serie konsol och samling av minnes dum par

Innan du återskapar den virtuella datorn rekommenderar vi att du aktiverar samling av minnes dum par och seriell konsol. Det gör du genom att köra följande skript: 

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör). 
2. Visa en lista med data från BCD-arkivet och ta reda på Start programmets identifierare, som du kommer att använda i nästa steg. 
    1. För en virtuell dator i generation 1, anger du följande kommando och noterar identifieraren: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        I kommandot ersätter du `<BOOT PARTITON>` med bokstaven för partitionen på den anslutna disken som innehåller startmappen. 

        ![Skärm bilden visar resultatet av registreringen av BCD-arkivet i en virtuell dator i generation 1, som visas under Windows Boot Loader (identifierings numret).](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. För en virtuell dator i generation 2 anger du följande kommando och noterar identifieraren:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * I kommandot ersätter du `<LETTER OF THE EFI SYSTEM PARTITION>` med bokstaven för EFI-systempartitionen.
        * Det kan vara bra att starta disk hanterings konsolen för att identifiera lämplig systempartition som är märkt som *EFI-systempartition*.
        * Identifieraren kan vara ett unikt GUID eller så kan den vara standard *Bootmgr*.
3. Kör följande kommandon för att aktivera en seriell konsol:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * I kommandot ersätter du `<VOLUME LETTER WHERE THE BCD FOLDER IS>` med bokstaven för BCD-mappen.
    * I kommandot ersätter `<BOOT LOADER IDENTIFIER>` du med den identifierare som du hittade i föregående steg.
4. Kontrol lera att det lediga utrymmet på OS-disken är större än minnes storleken (RAM) på den virtuella datorn. 
    1. Om det inte finns tillräckligt med utrymme på OS-disken, bör du ändra den plats där minnesdumpen kommer att skapas. I stället för att skapa filen på OS-disken kan du referera till en annan datadisk som är ansluten till den virtuella datorn som har tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter du **% systemroot%** med enhets beteckningen (t **. ex. F:**) för data disken i de kommandon som anges nedan. 
    2. Ange följande kommandon (föreslagen dump-konfiguration):

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
### <a name="rebuild-the-virtual-machine"></a>Återskapa den virtuella datorn

* Använd [steg 5 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att återskapa den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Felsöka start fel för virtuella Azure-datorer](./boot-error-troubleshoot.md)