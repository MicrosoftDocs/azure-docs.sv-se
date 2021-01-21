---
title: Windows Stop-fel-0x00000074 felaktig system konfigurations information
description: Den här artikeln innehåller steg för att lösa problem där Windows inte kan starta och måste startas om på grund av felaktig system konfigurations information på en virtuell Azure-dator (VM).
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: cbfdb9a73f53e194b43010c0b2d84357aa3e2e5b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631993"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Windows Stop-fel-0x00000074 felaktig system konfigurations information

Den här artikeln innehåller steg för att lösa problem där Windows inte kan starta och måste startas om på grund av felaktig system konfigurations information på en virtuell Azure-dator (VM).

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar Windows Stop-koden **#0x00000074** eller **BAD_SYSTEM_CONFIG_INFO**.

*Datorn stötte på ett problem och måste startas om. Du kan starta om.* 
 *Mer information om det här problemet och eventuella korrigeringar finns http://windows.com/stopcode i* 
 *Om du kallar en support person ger du dem denna information:* 
 *Stoppkod: BAD_SYSTEM_CONFIG_INFO*

  ![Windows Stop Code 0x00000074, som också visas som "BAD_SYSTEM_CONFIG_INFO". Windows informerar användaren om att datorn har stött på ett problem och måste startas om.](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>Orsak

**BAD_SYSTEM_CONFIG_INFO** stoppkod uppstår om registrerings data filen för **system** registret verkar vara skadad. Felet kan bero på någon av följande orsaker:

- Registrerings data filen stängdes inte korrekt.
- Registrerings data filen är skadad.
- Det saknas register nycklar eller värden.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt:

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. Skapa och få åtkomst till en reparations-VM.
1. Kontrol lera om Hive är skadat.
1. Aktivera serie konsol och samling av minnes dum par.
1. Återskapa den virtuella datorn.

> [!NOTE]
> När det här felet påträffas fungerar inte gäst operativ systemet (OS). Du felsöker i offline-läge för att lösa det här problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
1. Kontrol lera om Hive är skadat.
1. Använd Anslutning till fjärrskrivbord för att ansluta till den virtuella reparations datorn.
1. Kopiera `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` mappen och spara den antingen på din felfria diskpartition eller på en annan säker plats. Säkerhetskopiera den här mappen som en försiktighets åtgärd eftersom du kommer att redigera viktiga registerfiler. 

> [!NOTE]
> Gör en kopia av `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` mappen som en säkerhets kopia om du måste återställa de ändringar du gör i registret.

### <a name="check-for-hive-corruption"></a>Kontrol lera om Hive är skadad

Anvisningarna nedan hjälper dig att avgöra om orsaken berodde på en skadad Hive eller om Hive-filen inte stängdes på rätt sätt. Om Hive inte stängdes på rätt sätt kan du låsa upp filen och korrigera den virtuella datorn.

1. Öppna programmet **Registry Editor** på den virtuella datorn för reparation. Skriv "REGEDIT" i Sök fältet i Windows för att hitta det.
1. I Registereditorn väljer du **HKEY_LOCAL_MACHINE** för att markera den och väljer sedan **Arkiv > Läs in registrerings data fil...** från menyn.
1. Bläddra till `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM` och välj **Öppna**.
1. När du uppmanas att ange ett namn, anger du **BROKENSYSTEM**.

   1. Om det inte går att öppna Hive, eller om den är tom, är Hive skadad. Om Hive har skadats öppnar du [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

     ![Ett fel uppstår som anger att Registereditorn inte kan läsa in Hive.](./media/windows-stop-error-bad-system-config-info/2.png)

   1. Om Hive visas som vanligt stängdes Hive-filen på ett felaktigt sätt. Fortsätt till steg 5.

1. Om du vill åtgärda en Hive som inte stängdes korrekt markerar du **BROKENSYSTEM** och väljer sedan **fil > ta bort Hive...** för att låsa upp filen.

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