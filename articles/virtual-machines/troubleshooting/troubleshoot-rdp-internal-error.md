---
title: Ett internt fel uppstår när du gör en RDP-anslutning till Azure Virtual Machines | Microsoft Docs
description: Lär dig hur du felsöker RDP Internal errors i Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 5c8bd335832a950385f88f13dc31eb7f6159f831
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548139"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Ett internt fel inträffar när du försöker ansluta till en virtuell Azure-dator via Fjärrskrivbord

I den här artikeln beskrivs ett fel som kan uppstå när du försöker ansluta till en virtuell dator (VM) i Microsoft Azure.


## <a name="symptoms"></a>Symtom

Du kan inte ansluta till en virtuell Azure-dator med hjälp av Remote Desktop Protocol (RDP). Anslutningen har fastnat i avsnittet **Konfigurera fjärr** anslutning, eller så visas följande fel meddelande:

- Internt RDP-fel
- Ett internt fel har inträffat
- Datorn kan inte anslutas till fjärrdatorn. Försök ansluta igen. Kontakta ägaren till fjärrdatorn eller nätverks administratören om problemet kvarstår.


## <a name="cause"></a>Orsak

Det här problemet kan inträffa av följande orsaker:

- Den virtuella datorn kan ha attackeras.
- Det går inte att komma åt lokala RSA-krypteringsnyckeln.
- TLS-protokollet är inaktiverat.
- Certifikatet är skadat eller har upphört att gälla.

## <a name="solution"></a>Lösning

Du kan felsöka det här problemet genom att följa anvisningarna i följande avsnitt. Innan du börjar ska du ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

### <a name="check-rdp-security"></a>Kontrol lera RDP-säkerhet

Kontrol lera först om nätverks säkerhets gruppen för RDP-port 3389 är oskyddad (öppen). Om den är oskyddad och visas \* som Källans IP-adress för inkommande, begränsar du RDP-porten till en specifc-användares IP-adress och testar sedan RDP-åtkomsten. Om detta Miss lyckas, slutför du stegen i nästa avsnitt.

### <a name="use-serial-control"></a>Använd seriell kontroll

Använd serie konsolen eller [reparera den virtuella datorn offline](#repair-the-vm-offline) genom att koppla den virtuella DATORns OS-disk till en virtuell dator för återställning.

Börja genom att ansluta till [serie konsolen och öppna en PowerShell-instans](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om serie konsolen inte är aktive rad på den virtuella datorn går du till avsnittet [reparera den virtuella datorn offline](#repair-the-vm-offline) .

#### <a name="step-1-check-the-rdp-port"></a>Steg: 1 kontrol lera RDP-porten

1. I en PowerShell-instans använder du [netstat](/windows-server/administration/windows-commands/netstat) för att kontrol lera om port 3389 används av andra program:

    ```powershell
    Netstat -anob |more
    ```

2. Om Termservice.exe använder 3389-porten går du till steg 2. Om en annan tjänst eller ett annat program än Termservice.exe använder 3389-porten följer du dessa steg:

    1. Stoppa tjänsten för programmet som använder tjänsten 3389:

        ```powershell
        Stop-Service -Name <ServiceName> -Force
        ```

    2. Starta Terminal Service:

        ```powershell
        Start-Service -Name Termservice
        ```

2. Om programmet inte kan stoppas eller om den här metoden inte gäller för dig ändrar du port för RDP:

    1. Ändra porten:

        ```powershell
        Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

        Stop-Service -Name Termservice -Force

        Start-Service -Name Termservice
        ```

    2. Ange brand väggen för den nya porten:

        ```powershell
        Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>
        ```

    3. [Uppdatera nätverks säkerhets gruppen för den nya porten](../../virtual-network/network-security-groups-overview.md) i Azure Portal RDP-porten.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Steg 2: ange rätt behörigheter för det RDP-självsignerade certifikatet

1. I en PowerShell-instans kör du följande kommandon en i taget för att förnya det självsignerade RDP-certifikatet:

    ```powershell
    Import-Module PKI

    Set-Location Cert:\LocalMachine 

    $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 

    Remove-Item -Path $RdpCertThumbprint

    Stop-Service -Name "SessionEnv"

    Start-Service -Name "SessionEnv"
    ```

2. Om du inte kan förnya certifikatet med den här metoden kan du försöka förnya RDP-självsignerat certifikat via fjärr anslutning:

    1. Från en fungerande virtuell dator som har anslutning till den virtuella datorn som har problem, skriver du **MMC** i rutan **Kör** för att öppna Microsoft Management Console.
    2. I menyn **Arkiv** väljer du **Lägg till/ta bort snapin-modul**, väljer **certifikat** och väljer sedan **Lägg till**.
    3. Välj **dator konton**, Välj **en annan dator** och Lägg sedan till IP-adressen för den virtuella problemet.
    4. Gå till mappen **fjärr Desktop\Certificates** , högerklicka på certifikatet och välj sedan **ta bort**.
    5. I en PowerShell-instans från serie konsolen startar du om tjänsten fjärr skrivbords konfiguration:

        ```powershell
        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"
        ```

3. Återställ behörigheten för MachineKeys-mappen.

    ```powershell
    remove-module psreadline 

    md c:\temp

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 

    takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 

    Restart-Service TermService -Force
    ```

4. Starta om den virtuella datorn och försök sedan starta en fjärr skrivbords anslutning till den virtuella datorn. Om felet fortfarande inträffar går du till nästa steg.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Steg 3: Aktivera alla TLS-versioner som stöds

RDP-klienten använder TLS 1,0 som standard protokoll. Detta kan dock ändras till TLS 1,1, som har blivit den nya standarden. Om TLS 1,1 är inaktiverat på den virtuella datorn kommer anslutningen att Miss förväntas.

1. I en CMD-instans aktiverar du TLS-protokollet:

    ```console
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

2. Stoppa grup princip uppdateringen tillfälligt för att förhindra att AD-principen skriver över ändringarna:

    ```console
    REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
    ```

3. Starta om den virtuella datorn så att ändringarna börjar gälla. Om problemet är löst kör du följande kommando för att återaktivera grup principen:

    ```console
    sc config gpsvc start= auto sc start gpsvc

    gpupdate /force
    ```

    Om ändringen återställs innebär det att det finns en Active Directory princip i företags domänen. Du måste ändra principen för att undvika att det här problemet inträffar igen.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Koppla OS-disken till en virtuell dator för återställning

1. [Koppla OS-disken till en virtuell dator för återställning](./troubleshoot-recovery-disks-portal-windows.md).
2. När OS-disken är ansluten till den virtuella återställnings datorn kontrollerar du att disken är flaggad som **online** i disk hanterings konsolen. Anteckna enhets beteckningen som tilldelas till den anslutna OS-disken.
3. Starta en fjärr skrivbords anslutning till den virtuella återställnings datorn.

#### <a name="enable-dump-log-and-serial-console"></a>Aktivera dumpa logg och seriell konsol

Kör följande skript för att aktivera dumpa logg och seriell konsol.

1. Öppna en kommando tolk med förhöjd behörighet (**Kör som administratör**).
2. Kör följande skript:

    I det här skriptet antar vi att den enhets beteckning som är kopplad till den anslutna OS-disken är F. Ersätt enhets beteckningen med lämpligt värde för den virtuella datorn.

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Återställa behörigheten för mappen MachineKeys

1. Öppna en kommando tolk med förhöjd behörighet (**Kör som administratör**).
2. Kör följande skript. I det här skriptet antar vi att den enhets beteckning som är kopplad till den anslutna OS-disken är F. Ersätt enhets beteckningen med lämpligt värde för den virtuella datorn.

    ```console
    Md F:\temp

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt

    takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
    ```

#### <a name="enable-all-supported-tls-versions"></a>Aktivera alla TLS-versioner som stöds

1. Öppna en kommando tolk med förhöjd behörighet (**Kör som administratör**) och kör följande kommandon. Följande skript förutsätter att driv rutins beteckningen är kopplad till den anslutna OS-disken är F. Ersätt enhets beteckningen med lämpligt värde för den virtuella datorn.
2. Kontrol lera vilken TLS som är aktiverat:

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO
    ```

3. Om nyckeln inte finns, eller om dess värde är **0**, aktiverar du protokollet genom att köra följande skript:

    ```console
    REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

4. Aktivera NLA:

    ```console
    REM Enable NLA

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
    ```

5. Ta [bort OS-disken och återskapa den virtuella datorn](./troubleshoot-recovery-disks-portal-windows.md)och kontrol lera om problemet är löst.
