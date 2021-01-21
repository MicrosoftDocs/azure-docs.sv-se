---
title: Felsöka OS-start – Windows Update installations kapacitet
description: Steg för att lösa problem där Windows Update (KB) får ett fel och slutar svara i en virtuell Azure-dator.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 0c0ec45eee86031e1533b97ccf352de0ecf70e38
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633162"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Felsöka OS-start – Windows Update installations kapacitet

Den här artikeln innehåller steg för att lösa problem i en virtuell Azure-dator (VM) där Windows Update (KB) får ett fel och slutar svara.

## <a name="symptom"></a>Symptom

När du använder startdiagnostik för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar Windows Update (KB) pågår, men felkoden är: **C01A001D**. Följande bild visar Windows Update (KB) som fastnar i meddelandet "Error C01A001D Apply Update operation # # # # of # # # # (# # #)":

![Windows Update (KB) har fastnat i meddelandet "Error C01A001D to Update operation X of Y (Z)".](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Orsak

I den här situationen kan operativ systemet (OS) inte slutföra en Windows Update (KB)-installation eftersom det inte går att skapa en kärn fil i fil systemet. Den här felkoden tyder på att operativsystemet inte kan skriva filer till disken.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt:

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. Skapa och få åtkomst till en reparations-VM.
1. Ledigt utrymme på disken.
1. Aktivera serie konsol och samling av minnes dum par.
1. Återskapa den virtuella datorn.

> [!NOTE]
> Gäst operativ systemet fungerar inte när det här felet påträffas. Felsök det här problemet i offline-läge för att lösa det här problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
1. Anslut till den virtuella reparations datorn med hjälp av Anslutning till fjärrskrivbord.

### <a name="free-up-space-on-the-disk"></a>Frigör utrymme på disken

Så här löser du problemet:

- Ändra storlek på disken upp till 1 TB om den inte redan har en maximal storlek på 1 TB.
- Utför en disk rensning.
- Defragmentera enheten.

1. Kontrol lera att disken är full. Om disk storleken är under 1 TB expanderar du upp till maximalt 1 TB [med hjälp av PowerShell](../windows/expand-os-disk.md).
1. Om disken redan är 1 TB måste du utföra en disk rensning.
   1. Använd [disk rensnings verktyget](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) för att frigöra utrymme.
1. När storleks ändringen och rensningen är klar, defragmenteras enheten med följande kommando:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
Det kan ta flera timmar beroende på Fragmenteringens nivå.

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

    **Läs in den trasiga OS-disken:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **Aktivera på ControlSet001**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Aktivera på ControlSet002**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Ta bort skadad OS-disk**:

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Återskapa den virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att återskapa den virtuella datorn.
