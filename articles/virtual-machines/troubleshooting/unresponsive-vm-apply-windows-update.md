---
title: Den virtuella Azure-datorn svarar inte med C01A001D-fel vid tillämpning av Windows Update
description: Den här artikeln innehåller steg för att lösa problem där Windows Update genererar ett fel och slutar svara i en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 55dcd65e901a5ce8665ef8d3a2a29d7943581df9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200727"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Den virtuella datorn svarar inte på "C01A001D"-fel vid tillämpning av Windows Update

Den här artikeln innehåller steg för att lösa problem där Windows Update (KB) genererar ett fel och slutar svara i en virtuell Azure-dator.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn visas den Windows Update (KB) som pågår, men den Miss lyckas med felkoden: ' C01A001D '.

![svarar inte Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Orsak

Det går inte att skapa en kärn fil i fil systemet. Operativ systemet kan inte skriva filer till disken.

## <a name="resolution"></a>Lösning

### <a name="process-overview"></a>Process översikt

1. [Skapa och få åtkomst till en reparations-VM](#create-and-access-a-repair-vm).
2. [Frigör utrymme på hård disken](#free-up-space-on-the-hard-disk).
3. [Rekommenderas: innan du återskapar den virtuella datorn aktiverar du serie konsolen och samling av minnes dum par](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Återskapa den virtuella datorn](#rebuild-the-vm).

> [!NOTE]
> När det här felet uppstår fungerar inte gäst operativ systemet. Du måste felsöka i offline-läge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Förbered en virtuell reparations dator genom att följa [steg 1-3 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) .
2. Anslut till den reparerade virtuella datorn med Anslutning till fjärrskrivbord.

### <a name="free-up-space-on-the-hard-disk"></a>Frigör utrymme på hård disken

Om disken inte är redan 1 TB måste du ändra storlek på den. När disken är 1 TB utför du en disk rensning och en defragmentering av enheten.

1. Kontrol lera att disken är full. Om disken är under 1 TB [expanderar du den till högst 1 TB med hjälp av PowerShell](../windows/expand-os-disk.md).
2. När disken är 1 TB utför du en disk rensning.
    - [Koppla bort data disken från den brutna virtuella datorn](../windows/detach-disk.md).
    - [Koppla data disken till en fungerande virtuell dator](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
    - Använd [disk rensnings verktyget](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) för att frigöra utrymme.
3. När du har ändrat storlek och rensat, defragmentera enheten:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    Detta kan ta flera timmar beroende på Fragmenteringens nivå.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Rekommenderas: innan du återskapar den virtuella datorn aktiverar du serie konsolen och samling av minnes dum par

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör).
2. Kör följande kommandon:

    Aktivera serie konsol:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Kontrol lera att det lediga utrymmet på OS-disken är minst lika med storleken på det virtuella dator minnet (RAM-minne).

    Om det inte finns tillräckligt med utrymme på OS-disken ändrar du den plats där minnesdumpen ska skapas och refererar den till en datadisk som är ansluten till den virtuella datorn och med tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter du `%SystemRoot%` med enhets beteckningen (till exempel "F:") för data disken i följande kommandon:

    **Aktivera föreslagen konfiguration för OS-dump:**

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

### <a name="rebuild-the-vm"></a>Återskapa den virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att bygga upp den virtuella datorn igen.
