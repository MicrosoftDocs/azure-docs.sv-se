---
title: Den virtuella Azure-datorn svarar inte när principen tillämpas
description: Den här artikeln innehåller steg för att lösa problem där inläsnings skärmen inte svarar vid tillämpning av en princip vid start i en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 7160ec9564ede21eab0a205b2d66a7d566639506
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632664"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>Den virtuella datorn svarar inte när principen för lokala användare och grupper tillämpas grupprincip

Den här artikeln innehåller steg för att lösa problem där inläsnings skärmen inte svarar när en virtuell Azure-dator (VM) använder en princip vid start.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att visa en skärm bild av den virtuella datorn, fastnar skärmen i inläsningen med meddelandet: "tillämpar Grupprincip lokala användare och grupper princip".

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Skärm bild av hur du tillämpar grupprincip lokala användare och grupper princip inläsningar (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Skärm bild av hur du tillämpar grupprincip lokala användare och grupper princip inläsningar (Windows Server 2012).":::

## <a name="cause"></a>Orsak

Det finns motstridiga Lås när principen försöker rensa gamla användar profiler.

> [!NOTE]
> Detta gäller endast för Windows Server 2012 och Windows Server 2012 R2.

Här är en problematisk princip:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Lösning

### <a name="process-overview"></a>Process översikt

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. [Skapa och få åtkomst till en virtuell reparations dator](#step-1-create-and-access-a-repair-vm)
1. [Inaktivera principen](#step-2-disable-the-policy)
1. [Aktivera serie konsol och samling av minnes dum par](#step-3-enable-serial-console-and-memory-dump-collection)
1. [Återskapa den virtuella datorn](#step-4-rebuild-the-vm)

> [!NOTE]
> Om du stöter på det här start felet fungerar inte gäst operativ systemet. Du måste felsöka i offline-läge.

### <a name="step-1-create-and-access-a-repair-vm"></a>Steg 1: skapa och få åtkomst till en reparations-VM

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att förbereda en reparations-VM.
2. Använd Anslutning till fjärrskrivbord för att ansluta till den virtuella reparations datorn.

### <a name="step-2-disable-the-policy"></a>Steg 2: inaktivera principen

1. Öppna Registereditorn på den virtuella datorn för reparation.
1. Leta upp nyckeln **HKEY_LOCAL_MACHINE** och välj **fil**  >  **läsnings registrerings data** fil på menyn.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Skärm bild som visar markerade HKEY_LOCAL_MACHINE och den meny som innehåller load Hive.":::

    - Du kan använda läsa in Hive för att läsa in register nycklar från ett offline-system. I det här fallet är systemet den trasiga disken som är kopplad till den virtuella reparations datorn.
    - Systemomfattande inställningar lagras på `HKEY_LOCAL_MACHINE` och kan förkortas som "HKLM".
1. I den anslutna disken går du till `\windows\system32\config\SOFTWARE` filen och öppnar den.

    1. När du uppmanas att ange ett namn, anger du BROKENSOFTWARE.
    1. Kontrol lera att BROKENSOFTWARE har lästs in genom att expandera **HKEY_LOCAL_MACHINE** och leta efter den tillagda BROKENSOFTWARE-nyckeln.
1. Gå till BROKENSOFTWARE och kontrol lera om CleanupProfile-nyckeln finns i den inlästa Hive.

    1. Om nyckeln finns anges CleanupProfile-principen. Värdet representerar bevarande principen mätt i dagar. Fortsätt att ta bort nyckeln.
    1. Om nyckeln inte finns anges inte CleanupProfile-principen. [Skicka ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), inklusive filen Memory. dmp som finns i Windows-katalogen på den anslutna OS-disken.

1. Ta bort CleanupProfiles-nyckeln med hjälp av det här kommandot:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
1.  Ta bort BROKENSOFTWARE Hive med hjälp av det här kommandot:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Steg 3: Aktivera insamling av serie konsol och minnes dum par

Om du vill aktivera samling av minnes dum par och serie konsolen kör du det här skriptet:

1. Öppna en upphöjd kommando tolk session. (Kör som administratör.)
1. Kör dessa kommandon för att aktivera serie konsolen:
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. Kontrol lera om det lediga utrymmet på OS-disken är minst lika med den virtuella datorns minnes storlek (RAM).

    Om det inte finns tillräckligt med utrymme på OS-disken ändrar du platsen för minnes dumpning och hänvisar till en ansluten datadisk med tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter du% systemroot% med enhets beteckningen (till exempel "F:") för data disken i följande kommandon.

    **Föreslagen konfiguration för att aktivera OS-dump**

    Läs in trasig OS-disk:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Aktivera på ControlSet001:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    Aktivera på ControlSet002:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    Ta bort skadad OS-disk:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>Steg 4: återskapa den virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att bygga upp den virtuella datorn igen.

Om problemet är löst är principen nu inaktive rad lokalt. Använd inte CleanupProfiles-principen på virtuella datorer för en permanent lösning. Använd en annan metod för att utföra profil rensningar.

Använd inte den här principen:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Nästa steg

Om du har problem när du använder Windows Update kan du läsa [den virtuella datorn svarar inte med "C01A001D"-fel när du använder Windows Update](./unresponsive-vm-apply-windows-update.md).
