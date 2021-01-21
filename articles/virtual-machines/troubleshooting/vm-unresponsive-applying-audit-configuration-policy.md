---
title: Den virtuella datorn svarar inte när konfigurations principen för gransknings principen tillämpas
description: Den här artikeln innehåller steg för att lösa problem där den virtuella datorn (VM) slutar svara när du använder konfigurations principen för gransknings principer, vilket förhindrar att en virtuell Azure-dator startas.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: f286542c91ba473d13595d8e8299b1bbd8c93856
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632613"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>Den virtuella datorn svarar inte när konfigurations principen för gransknings principen tillämpas

Den här artikeln innehåller steg för att lösa problem där den virtuella datorn (VM) slutar svara när du använder konfigurations principen för gransknings principer, vilket förhindrar att en virtuell Azure-dator startas.

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar att operativ systemet (OS) inte svarade under en start med meddelandet att **använda konfigurations principen för gransknings principer**.

  ![Operativ systemet startar med meddelandet: "tillämpar konfigurations principen för gransknings principer"](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![Start programmet för operativ systemet i Windows Server 2012 med meddelandet: "tillämpar konfigurations principen för gransknings principer"](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>Orsak

Det finns motstridiga Lås när principen försöker rensa gamla användar profiler.

> [!NOTE]
> Detta gäller endast för Windows Server 2012 och Windows Server 2012 R2.

Här är en problematisk princip: *Computer Datorkonfiguration\principer\administrativa templates \ system/User Profiles\Delete User profiler som är äldre än ett visst antal dagar vid omstart av systemet.*

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. Skapa och få åtkomst till en reparations-VM.
1. Inaktivera principen.
1. Aktivera serie konsol och samling av minnes dum par.
1. Återskapa den virtuella datorn.
1. Samla in minnesdumpen och skicka in ett support ärende.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
1. Anslut till den virtuella reparations datorn med hjälp av Anslutning till fjärrskrivbord.

### <a name="disable-the-policy"></a>Inaktivera principen

1. Öppna **Registereditorn** på den virtuella datorn för reparation.
1. Leta upp nyckeln **HKEY_LOCAL_MACHINE** och välj **fil > läsa in Hive** från menyn.

   ![Navigering i Registereditorn för att läsa in en Hive.](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - Du kan använda läsa in Hive för att läsa in register nycklar från ett offline-system. I det här fallet är systemet den trasiga disken som är kopplad till den virtuella reparations datorn.
   - Systemomfattande inställningar lagras på **HKEY_LOCAL_MACHINE** och kan förkortas som **HKLM**.

1. Öppna filen på den anslutna disken `\windows\system32\config\SOFTWARE` .

   - När du uppmanas att ange ett namn, anger du **BROKENSOFTWARE**.
   - Kontrol lera att **BROKENSOFTWARE** har lästs in genom att expandera **HKEY_LOCAL_MACHINE** och leta efter den tillagda **BROKENSOFTWARE** -nyckeln.

1. Gå till **BROKENSOFTWARE** och kontrol lera om **CleanupProfiles** -nyckeln finns i den inlästa Hive.

   - Om nyckeln finns anges **CleanupProfiles** -principen. Värdet representerar bevarande principen mätt i dagar.
   - Om nyckeln inte finns anges inte **CleanupProfiles** -principen. I så fall kan du gå vidare till [Skicka ett support ärende med en](#collect-the-memory-dump-file-and-submit-a-support-ticket)minnesdumpfil.

1. Ta bort **CleanupProfiles** -nyckeln med hjälp av det här kommandot:

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. Ta bort **BROKENSOFTWARE** Hive med hjälp av det här kommandot:

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Aktivera samlings konsolen och minnes dumpnings samlingen

**Rekommenderas**: innan du återskapar den virtuella datorn aktiverar du samlings konsolen och samlings dumpningen genom att köra följande skript:

1. Öppna en upphöjd kommando tolks session som administratör.
1. Visa en lista med data från BCD-arkivet och identifiera start programmets identifierare, som du kommer att använda i nästa steg.

   1. För en virtuell dator i generation 1, anger du följande kommando och noterar identifieraren:

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - I kommandot ersätter du `<BOOT PARTITON>` med bokstaven för partitionen på den anslutna disken som innehåller startmappen.

        ![Bild 4 visar resultatet av registreringen av BCD-arkivet i en virtuell dator i generation 1, som visas under Windows Boot Loader (identifierings numret).](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. För en virtuell dator i generation 2 anger du följande kommando och noterar identifieraren:

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - I kommandot ersätter du `<LETTER OF THE EFI SYSTEM PARTITION>` med bokstaven för EFI-systempartitionen.
      - Det kan vara bra att starta disk hanterings konsolen för att identifiera lämplig systempartition som är märkt som **EFI-systempartition**.
      - Identifieraren kan vara ett unikt GUID eller så kan den vara standard **Bootmgr**.

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
   
### <a name="rebuild-the-virtual-machine"></a>Återskapa den virtuella datorn

1. Använd [steg 5 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att återskapa den virtuella datorn.

1. Testa om din virtuella dator startar normalt för att se om problemet har löst problemet.

   - Om problemet inte har åtgärd ATS kan du fortsätta att [samla in en dumpfil och skicka in ett support ärende](#collect-the-memory-dump-file-and-submit-a-support-ticket).
   - Om problemet har åtgärd ATS krävs inga ytterligare åtgärder.

Om problemet har åtgärd ATS är principen nu inaktive rad lokalt. För en permanent lösning ska du inte använda CleanupProfiles-principen på virtuella datorer eftersom den automatiskt tar bort användar profiler. Använd en annan metod för att utföra profil rensningar, till exempel en schemalagd aktivitet eller ett skript.

**Använd inte den här principen:** 
 *Machine\Admin Datorkonfiguration\principer\administrativa Profiles\Delete användar profiler som är äldre än ett visst antal dagar efter omstart av systemet.*

### <a name="the-issue-should-now-be-fixed"></a>Problemet bör nu åtgärdas

Testa din virtuella dator för att kontrol lera att den fungerar som vanligt. Om du fortfarande har problem kan du fortsätta till nästa avsnitt för att få hjälp.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>Samla in minnesdumpen och skicka in ett support ärende

För att lösa det här problemet måste du först samla in minnesdumpen för kraschen och sedan kontakta supporten med minnesdumpen. Samla in dumpfilen genom att följa dessa steg:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en ny reparations-VM.
1. Använda Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka in ett support ärende

1. På den reparera virtuella datorn går du till Windows-mappen på den anslutna OS-disken. Om den driv rutins beteckning som tilldelas till den anslutna OS-disken är märkt som *F* måste du gå till `F:\Windows` .
1. Leta upp `memory.dmp` filen och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpen.
1. Om du har problem med att hitta `memory.dmp` filen kan du i stället använda [icke-maskbart avbrott (NMI) i serie konsolen](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Följ guiden för att [Generera en kraschdump-fil med NMI-anrop här](/windows/client-management/generate-kernel-or-complete-crash-dump).