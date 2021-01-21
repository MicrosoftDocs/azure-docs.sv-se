---
title: KRITISK tjänst misslyckades vid start av en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker felet "0x0000005A-kritisk tjänst misslyckades" som uppstår vid start av | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: a937528e3bfd8bea16912d614133988763748bab
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632967"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows visar "kritisk tjänst misslyckades" på blå skärm vid start av en virtuell Azure-dator
I den här artikeln beskrivs fel meddelandet "kritisk tjänst misslyckades" som kan uppstå när du startar en virtuell Windows-dator (VM) i Microsoft Azure. Den innehåller fel söknings steg som hjälper dig att lösa problemen. 


## <a name="symptom"></a>Symptom 

En virtuell Windows-dator startar inte. När du kontrollerar start skärmarna i [startdiagnostik](./boot-diagnostics.md), ser du något av följande fel meddelanden på en blå skärm:

- "Datorn stötte på ett problem och måste startas om. Du kan starta om. Mer information om det här problemet och eventuella korrigeringar finns på https://windows.com/stopcode . Om du kallar en support person ger du dem denna information: stoppkod: felkod: kritisk tjänst misslyckades " 
- "Datorn stötte på ett problem och måste startas om. Vi samlar bara in fel information och startar sedan om. Om du vill veta mer kan du söka online senare efter det här felet: CRITICAL_SERVICE_FAILED "

## <a name="cause"></a>Orsak

Det finns olika orsaker till stopp fel. De vanligaste orsakerna är:
- Problem med en driv rutin
- Skadad system fil eller minne
- Programmet får åtkomst till en förbjuden sektor i minnet

## <a name="solution"></a>Lösning 

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

För att lösa det här problemet kan du [kontakta supporten och skicka en dumpfil](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), vilket hjälper oss att diagnostisera problemet snabbare, eller prova följande själv hjälp lösning.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. Ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla OS-disken till en virtuell dator för återställning](./troubleshoot-recovery-disks-portal-windows.md). 
3. Upprätta en fjärr skrivbords anslutning till den virtuella återställnings datorn.

### <a name="enable-dump-logs-and-serial-console"></a>Aktivera dumpnings loggar och serie konsol

I dumpnings loggen och [serie konsolen](./serial-console-windows.md) kan vi göra ytterligare fel sökning.

Kör följande skript om du vill aktivera dumpnings loggar och en serie konsol.

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör).
2. Kör följande skript:

    I det här skriptet antar vi att enhets beteckningen som är kopplad till den anslutna OS-disken är F. Du bör ersätta den med rätt värde för den virtuella datorn.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Ersätt de osignerade driv rutinerna

1. Kör följande kommando från en upphöjd kommando tolk på den virtuella återställnings datorn. Detta kommando ställer in den påverkade OS-disken så att den startar i fel säkert läge vid nästa start:

    ```console
    bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal
    ```

    Om den OS-disk som du har anslutit till exempel är enhet F kör du följande kommando:

    ```console
    bcdedit /store F: boot\bcd /set {default} safeboot minimal
    ```

2. [Koppla från OS-disken och återanslut sedan OS-disken till den berörda virtuella datorn](troubleshoot-recovery-disks-portal-windows.md). Den virtuella datorn startas om i fel säkert läge. Om du fortfarande upplever felet går du till det valfria steget.
3. Öppna rutan **Kör** och kör **Verifier** för att starta verktyget driv rutins hanteraren.
4. Välj Välj **osignerade driv rutiner automatiskt** och klicka sedan på **Nästa**.
5. Du får en lista över de drivrutinsfiler som är osignerade. Kom ihåg fil namnen.
6. Kopiera samma versioner av de här filerna från en fungerande virtuell dator och ersätt sedan dessa osignerade filer. 

7. Ta bort inställningarna för säker start:

    ```console
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
    ```

8.  Starta om den virtuella datorn. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Valfritt: analysera dumpnings loggarna i dumpa krasch läge

Följ dessa steg om du vill analysera dumpnings loggarna själv:

1. Koppla OS-disken till en virtuell dator för återställning.
2. På den OS-disk som du har bifogat bläddrar du till **\Windows\System32\Config**. Kopiera alla filerna som en säkerhets kopia om en återställning krävs.
3. Starta **Registereditorn** (regedit.exe).
4. Välj den **HKEY_LOCAL_MACHINE** nyckeln. På menyn väljer du **fil**  >  **läsnings registrerings data** fil.
5. Bläddra till mappen **\windows\system32\config\SYSTEM** på den OS-disk som du har anslutit. Ange **BROKENSYSTEM** som namn på Hive. Den nya registrerings data filen visas under **HKEY_LOCAL_MACHINE** nyckeln.
6. Bläddra till **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** och gör följande ändringar:

    AutoReboot = 0

    CrashDumpEnabled = 2
7.  Välj **BROKENSYSTEM**. Från menyn väljer du **Arkiv**  >  **ta bort Hive**.
8.  Ändra BCD-installationen för att starta i fel söknings läge. Kör följande kommando från en kommandorad med förhöjd behörighet:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Koppla från OS-disken och återanslut sedan OS-disken till den berörda virtuella datorn](troubleshoot-recovery-disks-portal-windows.md).
10. Starta den virtuella datorn för att se om den visar dump-analys. Hitta filen som inte kan läsas in. Du måste ersätta den här filen med en fil från den aktiva virtuella datorn. 

    Följande är exempel på dump-analys. Du kan se att **felen** är på filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. När den virtuella datorn fungerar och startar normalt, tar du bort inställningarna för dump kraschar:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
