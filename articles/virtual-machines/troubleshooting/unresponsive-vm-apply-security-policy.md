---
title: Den virtuella Azure-datorn svarar inte när säkerhets principen tillämpades på systemet
description: Den här artikeln innehåller steg för att lösa problem där inläsnings skärmen fastnar när den virtuella datorn inte svarar när säkerhets principen tillämpas på systemet på en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 047c8afbfe7b489e5c3ac0ccb677f6fc021443a8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632647"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>Den virtuella Azure-datorn svarar inte när säkerhets principen tillämpades på systemet

Den här artikeln innehåller steg för att lösa problem där operativ systemet låser sig och slutar svara när en säkerhets princip tillämpas på en virtuell Azure-dator.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar det operativ system som fastnat när du startar med meddelandet:

> ' Tillämpar säkerhets princip på systemet.

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Skärm bild av Start skärmen för Windows Server 2012 R2 har fastnat.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Skärm bild av Start skärmen för OS har fastnat.":::

## <a name="cause"></a>Orsak

Det finns en mängd olika av möjliga orsaker till det här problemet. Du kommer inte att kunna känna till källan förrän en minnes dumpnings analys har utförts.

## <a name="resolution"></a>Lösning

### <a name="process-overview"></a>Process översikt

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. [Skapa och få åtkomst till en virtuell reparations dator](#create-and-access-a-repair-vm)
2. [Aktivera serie konsol och samling av minnes dum par](#enable-serial-console-and-memory-dump-collection)
3. [Återskapa den virtuella datorn](#rebuild-the-vm)
4. [Samla in minnesdumpen](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att förbereda en reparations-VM.
2. Använd Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Aktivera serie konsol och samling av minnes dum par

Kör det här skriptet om du vill aktivera samling av minnes dum par och seriella konsoler:

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör).
2. Visa en lista med data från BCD-arkivet och identifiera start programmets identifierare, som du kommer att använda i nästa steg.

     1. För en virtuell dator i generation 1, anger du följande kommando och noterar identifieraren:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        I kommandot ersätter du \<BOOT PARTITON> med bokstaven för partitionen på den anslutna disken som innehåller startmappen.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="Diagrammet visar resultatet av registreringen av BCD-arkivet i en virtuell dator i generation 1, som visas under Windows Boot Loader (identifierings numret).":::

     2. För en virtuell dator i generation 2 anger du följande kommando och noterar identifieraren:

        ```console
        bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
        ```

        - I kommandot ersätter du \<LETTER OF THE EFI SYSTEM PARTITION> med bokstaven för EFI-systempartitionen.
        - Det kan vara bra att starta disk hanterings konsolen för att identifiera rätt systempartition som är märkt med EFI-systempartitionen.
        - Identifieraren kan vara ett unikt GUID eller så kan den vara standard "Bootmgr".
3. Kör följande kommandon för att aktivera en seriell konsol:

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

    - I kommandot ersätter du \<VOLUME LETTER WHERE THE BCD FOLDER IS> med bokstaven för BCD-mappen.
    - I kommandot ersätter \<BOOT LOADER IDENTIFIER> du med den identifierare som du hittade i föregående steg.
4. Kontrol lera att det lediga utrymmet på OS-disken är större än minnes storleken (RAM) på den virtuella datorn.

    1. Om det inte finns tillräckligt med utrymme på OS-disken, bör du ändra den plats där minnesdumpen kommer att skapas. I stället för att skapa filen på OS-disken kan du referera till en annan datadisk som är ansluten till den virtuella datorn som har tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter du% systemroot% med enhets beteckningen (till exempel "F:") för data disken i de kommandon som visas nedan.
    2. Ange följande kommandon (föreslagen dump-konfiguration):

        Läs in trasig OS-disk:

        ```console
        REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
        ```

        Aktivera på ControlSet001:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Aktivera på ControlSet002:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Ta bort skadad OS-disk:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>Återskapa den virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att bygga upp den virtuella datorn igen.

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpen

För att lösa det här problemet behöver du först samla in minnesdumpen för kraschen och kontakta supporten med minnesdumpen. Samla in dumpfilen genom att följa dessa steg:

1. Koppla OS-disken till en ny virtuell reparations dator:

    - Använd [steg 1-3 i reparations kommandona för virtuella datorer](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att förbereda en ny reparations-VM.
    - Använd Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

2. Leta rätt på dumpfilen och skapa en supportbegäran:

    - På den reparera virtuella datorn går du till Windows-mappen på den anslutna OS-disken. Om operativsystemdisken har enhetsbeteckningen `F` går du till `F:\Windows`.
    - Leta upp filen Memory. dmp och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpen.
    - Om du har problem med att hitta Memory. dmp-filen kanske du vill använda [icke-maskbart avbrott (NMI) i serie konsolen i](serial-console-windows.md#use-the-serial-console-for-nmi-calls) stället. Du kan följa guiden för att [skapa en kraschdump-fil med NMI-anrop](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Nästa steg

Om du har problem när du använder principer för lokala användare och grupper ser du [att den virtuella datorn inte svarar när du tillämpar Grupprincip principer för lokala användare och grupper](unresponsive-vm-apply-group-policy.md)