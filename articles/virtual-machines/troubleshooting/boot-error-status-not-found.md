---
title: Felsöka Windows Boot Manager-fel-0xC0000225 "status hittades inte"
description: Steg för att lösa problem där fel kod 0xC0000225 inträffar i en virtuell Azure-dator.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: a91add684d7b7aaee67e7dd4f4f2d0c6df078132
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632232"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Felsöka Windows Boot Manager-fel-0xC0000225 "status hittades inte"
 
Den här artikeln innehåller steg för att lösa problem där fel kod 0xC0000225 inträffar i en virtuell Azure-dator. Det här felet anger att det inte går att hitta statusen eller objektet.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar att det inte gick att starta ett Windows-fel med status koden *0xc0000225*.

Filen som är associerad med den här felkoden meddelar dig vilka steg du ska vidta för att lösa problemet. Leta upp **filen:** avsnittets text för att fastställa rätt åtgärds riktning.

### <a name="drivers-os-related-or-third-party"></a>Driv rutiner, OS-relaterad eller tredje part

Om filen finns men hänvisar till en driv rutin (som visas) eller är operativ system eller tredje part, följer du stegen i avsnittet [Reparera system filen](#repair-the-system-file).
 
Det gick inte att starta Windows Boot Manager-tillstånd i följande bild. En ny maskin-eller program varu ändring kan vara orsaken. " Bilden visar också status som "0xc0000225", **fil:** as `\windows\System32\drivers\atapi.sys` , och **Info:** as "Det gick inte att läsa in operativ systemet eftersom en kritisk system driv rutin saknas eller innehåller fel."

![Det gick inte att starta Windows Boot Manager-tillstånd. En ny maskin-eller program varu ändring kan vara orsaken till detta. Den visar också status som "0xc0000225", filen som "\windows\System32\drivers\atapi.sys" och i info-avsnittet tillstånd: "Det gick inte att läsa in operativ systemet eftersom en kritisk system driv rutin saknas eller innehåller fel."](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>Ingen fil

Om status koden visas, men ingen fil visas, följer du stegen i avsnittet [Lägg till variabeln OSDEVICE](#add-the-osdevice-variable).

Det gick inte att starta Windows Boot Manager-tillstånd i följande bild. En ny maskin-eller program varu ändring kan vara orsaken. " Bilden visar även status som "0xc0000225" och **Info:** as "Det gick inte att starta valet eftersom en nödvändig enhet inte är tillgänglig."

![Det gick inte att starta Windows Boot Manager-tillstånd. En ny maskin-eller program varu ändring kan vara orsaken till detta. Den visar också status som "0xc0000225" och i avsnittet information: "Start valet misslyckades eftersom en nödvändig enhet inte kan nås."](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>Register fil

Om det refererar till någon av registerfilerna, till exempel \Windows\System32\Config\System, följer du stegen i avsnittet [skapa ett support ärende](#contact-support).
 
Det gick inte att starta Windows Boot Manager-tillstånd i följande bild. En ny maskin-eller program varu ändring kan vara orsaken. " Bilden visar också status som "0xc0000225", filen som `\windows\System32\config\system` och **Info:** as "Det gick inte att läsa in operativ systemet eftersom System register filen saknas eller innehåller fel."

![Det gick inte att starta Windows Boot Manager-tillstånd. En ny maskin-eller program varu ändring kan vara orsaken till detta. Den visar också status som "0xc0000225", filen som "\windows\System32\config\system" och i info-avsnittets tillstånd: "Det gick inte att läsa in operativ systemet eftersom System register filen saknas eller innehåller fel."](./media/troubleshoot-boot-error-status-not-found/3.png)

I följande bild anger återställnings skärmen "din dator/enhet måste repare ras. Det gick inte att läsa in operativ systemet eftersom System register filen saknas eller innehåller fel. " I bilden visas även felkoden som "0xc0000225" och filen som `\windows\System32\config\system` .

![Återställnings skärmen anger att din dator/enhet måste repare ras. Det gick inte att läsa in operativ systemet eftersom System register filen saknas eller innehåller fel. Felkoden visas också som "0xc0000225" och filen som "\windows\System32\config\system".](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>Orsaker

### <a name="missing-binary"></a>Binär saknas

Du kanske stöter på att en binärfil saknas eller är skadad på din system fil **(. sys)** .

### <a name="bcd-corruption-or-improper-vhd-migration"></a>Skadad BCD eller felaktig VHD-migrering

I det här fallet är antingen **Boot Configuration data (BCD)** skadad, eller så har den **virtuella hård disken (VHD)** migrerats från lokalt, men var inte korrekt för beredd. Resultatet är att variabeln **OSDEVICE** saknas och måste läggas till.

### <a name="registry-hive-corruption"></a>Skadad registrerings data fil

En skadad registrerings data fil kan bero på att:

- Hive Miss lyckas
- Registrerings data filen monteras, men är tom
- Hive stängdes inte korrekt
## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

1. Skapa och få åtkomst till en reparations-VM.
1. Välj en lösning:
   - [Reparera system filen](#repair-the-system-file)
   - [Lägg till variabeln OSDevice](#add-the-osdevice-variable)
   - [Skapa ett support ärende](#contact-support)
1. Aktivera serie konsol och samling av minnes dum par.
1. Återskapa den virtuella datorn.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
1. Anslut till den virtuella reparations datorn med hjälp av Anslutning till fjärrskrivbord.

### <a name="select-a-solution"></a>Välj en lösning

1. Öppna en kommando tolk med förhöjd behörighet.
1. Följ stegen i motsvarande lösning baserat på det symptom som du identifierade tidigare. Du kan hoppa över stegen i de andra lösningarna eftersom de inte gäller ditt problem:

- [Reparera system filen](#repair-the-system-file)
- [Lägg till variabeln OSDevice](#add-the-osdevice-variable)
- [Skapa ett support ärende](#contact-support)

### <a name="repair-the-system-file"></a>Reparera system filen

1. Med hjälp av den anslutna virtuella hård disken navigerar du till fil platsen för den binärfil som visas på skärm bilden för den virtuella datorn (VM).
1. Högerklicka på filen, Välj **Egenskaper** och välj sedan fliken **information** för att se information om filen.
   1. Observera versionen av filen, som visas på bilden nedan:

      ![Fönstret Egenskaper för filen cng.sys, där fil versionen är markerad.](./media/troubleshoot-boot-error-status-not-found/5.png)

1. Byt namn på filen till **< BINARY.SYS >. Old** och ersätt **< BINARY.SYS >** med namnet på filen.

   För avbildningen i steget ovan kommer filen **cng.sys** att byta namn till **cng.sys. Old**

   > [!NOTE]
   > Om du försöker byta namn på filen och ta emot meddelandet "filen är skadad och oläslig", så [kontakta supporten](https://azure.microsoft.com/support/create-ticket/)om du vill ha hjälp, eftersom den här lösningen inte fungerar.

1. Nu när den skadade filen har bytt namn, korrigerar du filen genom att återställa den från den interna lagrings platsen.
   1. Starta en **cmd** -session.
   1. Navigera till **\windows\winsxs**.

   1. Sök efter den binärfil som finns i början av det här avsnittet med hjälp av följande kommando:

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      Med det här kommandot visas en lista över alla versioner av filen som datorn har, så att du får Sök vägs historiken för den komponenten.

      Till exempel skulle **dir cng.sys** byta namn till **dir cng.sys/s**

   1. Välj den senaste versionen av filen i listan (eller någon som du föredrar) och kopiera filen till mappen **Windows\System32** med hjälp av föregående sökväg och följande kommando:

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > Om den senaste binärfilen inte fungerade, kan du prova med en version före den en, eller någon av vilka du vet att det finns en stabil fil, till exempel en version före en korrigering.

      Om den binärfil som du söker efter **cmimcext.sys**, är den felaktiga enheten till exempel enhet **F:**, och du har kört en sökning efter den senaste versionen, ser du följande bild, där en fråga i kommando tolken `dir cmim* /s` söker efter den senaste versionen av cmimcext.syss filen.

      ![En fråga i kommando tolken "dir cmim */s" för att hitta den senaste versionen av cmimcext.sys-filen.](./media/troubleshoot-boot-error-status-not-found/6.png)

      I exempel bilden ovan utfördes frågan på **C:**, medan enhets beteckningen ska vara den felaktiga enheten, **F:**, som är den OS-disk som är ansluten som en datadisk på den virtuella reparations datorn.

      Det resulterande kommandot för att kopiera filen skulle vara: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers` .

När den här uppgiften är slutförd fortsätter du att [Aktivera samlings konsolen och minnes dumpnings samlingen](#enable-the-serial-console-and-memory-dump-collection).

### <a name="add-the-osdevice-variable"></a>Lägg till variabeln OSDEVICE

Samla in information om den aktuella start konfigurationen och anteckna identifieraren på den aktiva partitionen. Sedan använder du den här informationen för att lägga till **OSDEVICE** -variabeln enligt anvisningarna för att skapa den virtuella datorn.

Om den här informations insamlingen ger ett fel där det inte finns någon **\boot\bcd** -fil kan du använda instruktionerna i [Reparera system filen](#repair-the-system-file) i stället. 

1. För virtuella datorer i generation 1 öppnar du en upphöjd kommando tolk som administratör och anger följande kommando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   Den här bilden visar Windows Boot loader i en virtuell dator i generation 1 med attributet Identifier markerat. Attributet Identifier är markerat och visar en unik alfanumerisk sträng.

   ![Windows Boot Loader visas i en virtuell dator i generation 1 med attributet Identifier markerat. Attributet Identifier är markerat och visar en unik alfanumerisk sträng.](./media/troubleshoot-boot-error-status-not-found/7.png)

   Observera identifieraren för Windows Start inläsaren, vars sökväg är **\windows\system32\winload.exe**.

1. För virtuella datorer i generation 2 kontrollerar du att operativ system disken är online och att dess enhets beteckningar har tilldelats. När detta har verifierats ska du samla in information om start konfigurationen.
   1. I **Windows search** skriver du **disk hantering** och öppnar disk hanterings konsolen. Använd den här konsolen för att identifiera disk numret som är anslutet till den reparerade virtuella datorn och den Extensible Firmware Interface (EFI) partition som innehåller BCD-arkivet.

   I följande bild är disk 2 det disk nummer som är kopplat till den virtuella reparations datorn. Avbildningen visar också EFI-systempartitionen på disk 2, som är 100 MB i storlek och inte har en tilldelad bokstav.

   ![Disk 2 visas som det disk nummer som är kopplat till den virtuella reparations datorn. Den visar också EFI-systempartitionen på disk 2, som är 100 MB och inte har tilldelats någon bokstav.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. Öppna en upphöjd kommando tolk som administratör och ange följande kommandon:
      1. Öppna **verktyget DiskPart** med hjälp av kommandot `diskpart` .
      1. Lista alla diskar och välj sedan den anslutna disken som identifierades i föregående steg:
      
         ```
         list disk
         sel disk <DISK #>
         ```

         Följande bild visar resultatet av att visa och välja en disk. Disk 0 (127 GB/online), disk 1 (32 GB/online) och disk 2 (127 GB/online) visas, med disk 2 som väljs med hjälp av kommandot `sel disk 2` .

         ![Resultatet av registreringen och sedan att välja en disk. Disk 0 (127 GB | Online), disk 1 (32 GB | Online) och disk 2 (127 GB | Online) visas, med disk 2 som väljs.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. Visa en lista över partitionerna och välj den EFI-systempartition som identifierades i föregående steg:
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         Följande bild visar resultatet av att visa och välja en partition. Partition 1 (reserverad/16 MB), partition 2 (system/100 MB) och partition 3 (primär/126 GB) visas, med partition 2 som väljs med hjälp av kommandot `sel part 2` .

         ![Resultatet av registreringen och sedan välja en partition. Partition 1 (reserverad | 16 MB), partition 2 (system | 100 MB) och partition 3 (primär | 126 GB) visas, med partition 2 som väljs.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. Tilldela en bokstav till EFI-partitionen med hjälp av kommandot `assign` .

         I följande bild visas `assign` både kommandot och det nya enhets **systemet (F:)** i Utforskaren.

         ![Kommandot tilldela och det nya enhets systemet (F:) visas i Utforskaren.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. Visa en lista med data från BCD-arkivet med följande kommando:
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         I följande bild finns Windows Boot loader i en virtuell dator i generation 2 med attributet Identifier markerat. Det markerade Identifier-attributet har värdet **{default}**.

         ![Windows Boot Loader visas på en virtuell dator i generation 2 med attributet Identifier markerat. Det markerade attributet Identifier visar standard som värde.](./media/troubleshoot-boot-error-status-not-found/12.png)

         Observera identifieraren för Windows Start inläsaren, vars sökväg är **\windows\system32\winload.EFI**.

1. Observera att variabeln OSDEVICE på den aktiva partitionen saknas:

   ![Attributen för Windows Boot Manager och Windows Boot Loader visas i kommando tolken, med attributet OS-enhet saknas.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   I den här bilden visas attributen för Windows Boot Manager och Windows Boot loader i kommando tolken, men attributet OSDEVICE saknas.

1. Lägg till variabeln OSDEVICE baserat på följande information:

   Lägg till på operativ system diskar med en partition `BOOT` .

   > [!NOTE]
   > Startmappen är på samma partition som mappen Windows-mappen **\Windows**.
   > - Den startbara mappen för virtuella datorer i generation 1 är **(\boot\bcd-mapp)**.
   > - Den startbara mappen för virtuella datorer i generation 2 är **EFI\Microsoft\boot\bcd**.

   För virtuella datorer i generation 1 anger du följande kommando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   För virtuella datorer i generation 2 anger du följande kommando:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   För flera partitions-OS-diskar lägger du till `PARTITION=<LETTER OF WINDOWS FOLDER>:` .

   > [!NOTE]
   > Startmappen kommer förmodligen att finnas på en annan partition än mappen Windows-mappen **\Windows**.
   > - Den startbara mappen för virtuella datorer i generation 1 är **(\boot\bcd-mapp)**.
   > - Den startbara mappen för virtuella datorer i generation 2 är **EFI\Microsoft\boot\bcd**.

   För virtuella datorer i generation 1 anger du följande kommando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   För virtuella datorer i generation 2 anger du följande kommando:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

När den här uppgiften är slutförd fortsätter du att [Aktivera samlings konsolen och minnes dumpnings samlingen](#enable-the-serial-console-and-memory-dump-collection).

### <a name="contact-support"></a>Kontakta supporten

**Register fil** felet har en lösning, men du måste [skapa ett support ärende](https://azure.microsoft.com/support/create-ticket/) för att få hjälp.

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
