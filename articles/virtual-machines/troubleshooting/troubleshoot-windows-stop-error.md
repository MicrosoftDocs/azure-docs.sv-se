---
title: Windows-stoppfel – status slut på minne
description: Den här artikeln innehåller steg för att lösa problem där Windows inte kan starta och visar status "status inget minne".
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d6e738152e542617046834980d3e7c58e497093
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664689"
---
# <a name="windows-stop-error---status-no-memory"></a>Windows-stoppfel – status slut på minne

Den här artikeln innehåller steg för att lösa problem där Windows inte kan starta och visar status-eller felkod #0xC0000017, även kallat "status inget minne".

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn (VM) ser du att skärm bilden visar felkoden: `0xC0000017` . Beroende på vilken version av Windows du kör kan du se den här koden som visas i antingen **Windows Boot Manager** eller på **återställnings skärmen**.

   **Windows Boot Manager**

   ![Windows Boot Manager säger att "Windows misslyckades att starta. En ny maskin-eller program varu ändring kan vara orsaken ". Rulla nedåt visas status koden "0xC0000017" samt information om att det inte gick att läsa in programmet eller operativ systemet, eftersom en fil som krävs saknas eller innehåller fel ".](./media/troubleshoot-windows-stop-error/1.png)

   **Återställnings skärm**
 
   ![Återställnings skärmen anger att "din dator/enhet måste repare ras. Det finns inte tillräckligt med minne för att skapa en Ramdisk-enhet. Du bör också se felkoden "0xC0000017".](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>Orsak

Operativ systemets disk är antingen full, för fragmenterad eller operativ systemet (OS) kan inte komma åt minnet eller växlings filen eller både och.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt:

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. Skapa och få åtkomst till en virtuell reparations dator
1. Frigör utrymme på disken
1. Rensa dåligt minne från BCD-arkivet
1. Återställa växlings filen till standard platsen
1. Aktivera serie konsol och samling av minnes dum par
1. Återskapa den virtuella datorn

> [!NOTE]
> Gäst operativ systemet fungerar inte när det här felet påträffas. Du ska felsöka i offline-läge för att lösa problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
1. Använda Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>Tilldela en bokstav till Extensible Firmware Interface-partitionen (EFI) för virtuella datorer i generation 2:

Om du använder en virtuell dator i generation 2 kanske inte den anslutna diskens EFI-partition har tilldelats någon bokstav. Du måste följa stegen nedan för att tilldela en bokstav till partitionen innan du fortsätter med den här fel söknings guiden.

1. I Windows search anger `diskmgmt` och öppnar du **konsolen disk hantering**.
1. Identifiera den skadade disk som är ansluten till den virtuella reparations datorn. Normalt visas den här disken sist i-konsolen och har det högsta numeriska värdet.
1. Observera att på disken finns det en partition som innehåller EFI- **systempartitionen**, som inte heller har tilldelats ett brev värde (till exempel enhet *F:*). Om alla partitioner tilldelas kan du gå vidare till frigör utrymme på disken. Annars fortsätter du att tilldela en bokstav till den här disken.

   ![Disk hanterings konsolen med den anslutna disken "disk 2", samt den otilldelade partition som är 100 MB och är "EFI-systempartitionen".](./media/troubleshoot-windows-stop-error/3.png)

1. Öppna en kommando tolk med förhöjd behörighet som administratör och ange `diskpart` för att starta **DiskPart** -verktyget.
1. Ange följande kommandon:

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - I kommandot ersätter `<NUMBER OF THE ATTACHED DISK>` du med disk numret som du identifierade i steg 2.
   - I kommandot ersätter du `<NUMBER OF THE SYSTEM PARTION>` med partitions numret för EFI-systempartitionen. Den här partitionen har inte tilldelats någon bokstav ännu, men den bör vara av typen **system** och vara cirka 100 MB.

   > [!NOTE]
   > Att jämföra partitionerna i disk hanterings konsolen med de som anges i DISKPART-verktyget, kan vara till hjälp när du ska bestämma vilket partitionsnummer som motsvarar EFI-systempartitionen på den anslutna disken.

1. Stäng kommandotolksfönstret.

### <a name="free-up-space-on-the-disk"></a>Frigör utrymme på disken

Nu när den skadade disken är kopplad till den virtuella reparations datorn bör du kontrol lera att operativ systemet på disken har tillräckligt med utrymme för att fungera korrekt. 

1. Kontrol lera att disken är full genom att högerklicka på enheten på den anslutna disken och välja **Egenskaper**.
1. Om disken har **mindre än 300 MB ledigt utrymme** [expanderar du den till maximalt 1 TB med hjälp av PowerShell](../windows/expand-os-disk.md).
1. När disk storleken är **1 TB** måste du utföra en disk rensning. Du kan frigöra utrymme med [disk rensnings verktyget](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) .
1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör) och utför en defragmentering på enheten:

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - Det kan ta flera timmar beroende på Fragmenteringens nivå.
   - I kommandot ersätter `<LETTER ASSIGNED TO THE OS DISK>` du med OS-diskens bokstav (till exempel enhet *F:*).

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>Rensa dåligt minne från Boot Configuration Data (BCD) Store

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör).
1. Fråga start konfigurations filen efter skadade minnes flaggor med följande kommando:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - I kommandot ersätter `<LETTER ASSIGNED TO THE OS DISK>` du med OS-diskens bokstav (till exempel enhet *F:*).

1. Om frågan inte visar några dåliga minnes block går du vidare till nästa uppgift. Annars fortsätter du till steg 4.
1. Om dåliga minnes block identifieras förhindrar dessa block att en RAMDISK skapas och ska tas bort med följande kommando:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - I kommandot ersätter `<LETTER ASSIGNED TO THE OS DISK>` du med OS-diskens bokstav (till exempel enhet *F:*).

### <a name="restore-the-page-file-to-its-default-location"></a>Återställa växlings filen till standard platsen

Växlings filen lagrar data som inte hanteras av datorns RAM-minne (Random Access Memory) som en form av data spill/säkerhets kopiering. Det är möjligt att filen finns på en virtuell hård disk i stället för den temporära enheten, som är standard platsen för Azure. Om det här värdet är sant kanske filen inte är tillgänglig och ska återställas till standard platsen.

Innan du vidtar några steg bör du skapa en kopia av mappen **\Windows\System32\Config** på en felfri disk. Det här steget garanterar att kan ångra eventuella oönskade ändringar. Du kommer att arbeta med viktiga systemfiler, så den här försiktighets åtgärden rekommenderas.

1. I Windows search anger du **regedit** och öppnar Registry Editor-programmet.
1. Markera nyckeln **HKEY_LOCAL_MACHINE** i Registereditorn och välj **fil > Läs in Hive...** från menyn.

   ![Menyn Läs in Hive i Registereditorn.](./media/troubleshoot-windows-stop-error/4.png)

1. I dialog rutan Läs in Hive väljer du **\windows\system32\config\SYSTEM** och klickar på öppna.
   1. Du uppmanas att ange ett namn som du anger **BROKENSYSTEM**. Det här namnet hjälper till att särskilja de påverkade registreringsdatafiler när du felsöker.
   1. Expandera **HKEY_LOCAL_MACHINE** för att se den nya BROKENSYSTEM-nyckeln som du har lagt till.
1. Använd Registereditorn för att avgöra vilken ControlSet datorn startar från.
   1. Gå till **HKEY_LOCAL_MACHINE >> BROKENSYSTEM >> Välj**.
   1. Anteckna datavärdet för Current i de nycklar som visas. Om det här värdet till exempel är **1** eller **0x00000001 (1)**, blir kontroll uppsättningen ControlSet001.
1. Kontrol lera den plats där växlings filen skapades.
   1. I HKEY_LOCAL_MACHINE\BROKENSYSTEM expanderar du katalogen som matchar det ControlSet-nummer som du identifierade i steg 4, till exempel **ControlSet001**.
   1. Gå till **kontroll >> sessionshanteraren >> minnes hantering** och notera platsen för **ExistingPageFiles** -nyckeln.
   1. Den här nyckeln bör finnas på Azure-standardplatsen för temp-enheten. Om den inte finns där och finns på en virtuell hård disk på en annan plats, t. ex. data disk enhet eller operativ system enhet, måste du ta bort den.
   1. Bläddra till den platsen i Utforskaren och ta sedan bort **pagefile.syss** filen.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Aktivera samlings konsolen och minnes dumpnings samlingen

**Rekommenderas**: innan du återskapar den virtuella datorn aktiverar du samlings konsolen och samlings dumpningen genom att köra följande skript:

Om du vill aktivera samlings-och serie konsolen för minnes dum par kör du följande skript:

1. Öppna en upphöjd kommando tolks session som administratör.
1. Visa en lista med data från BCD-arkivet och ta reda på Start programmets identifierare, som du kommer att använda i nästa steg.

   1. För en virtuell dator i generation 1, anger du följande kommando och noterar identifieraren:
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - I kommandot ersätter du `<BOOT PARTITON>` med bokstaven för partitionen på den anslutna disken som innehåller startmappen.

      ![Resultatet av registreringen av BCD-arkivet i en virtuell dator i generation 1, som visar listor under Windows Boot Loader (ID-numret).](./media/troubleshoot-windows-stop-error/5.png)

   1. För en virtuell dator i generation 2 anger du följande kommando och noterar identifieraren:
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - I kommandot ersätter du `<LETTER OF THE EFI SYSTEM PARTITION>` med bokstaven för EFI-systempartitionen.
   - Det kan vara bra att starta disk hanterings konsolen för att identifiera lämplig systempartition som är märkt som **EFI-systempartition**.
   - Identifieraren kan vara ett unikt GUID eller så kan den vara standard **Bootmgr**.

1. Kör följande kommandon för att aktivera en seriell konsol:

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - I kommandot ersätter du `<VOLUME LETTER WHERE THE BCD FOLDER IS>` med bokstaven för BCD-mappen.
   - I kommandot ersätter `<BOOT LOADER IDENTIFIER>` du med den identifierare som du hittade i föregående steg.

1. Kontrol lera att det lediga utrymmet på OS-disken är större än minnes storleken (RAM) på den virtuella datorn.

   Om det inte finns tillräckligt med utrymme på OS-disken, ändrar du den plats där minnesdumpen skapas och kontrollerar platsen till alla data diskar som är anslutna till den virtuella datorn med tillräckligt med ledigt utrymme. Om du vill ändra platsen ersätter du **% systemroot%** med enhets bokstaven för data disken, till exempel enhet **F:**, i följande kommandon.

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
