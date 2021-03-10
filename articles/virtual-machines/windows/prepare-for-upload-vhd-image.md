---
title: Förbered en virtuell Windows-hårddisk att ladda upp till Azure
description: Lär dig hur du förbereder en virtuell Windows-disk eller VHDX för att ladda upp det till Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: 12ef839cbbbc69230b314bf7c56a63f57a0d6b20
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556272"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Förbereda en VHD eller VHDX i Windows för överföring till Azure

Innan du laddar upp en virtuell Windows-dator (VM) från en lokal plats till Azure måste du förbereda den virtuella hård disken (VHD eller VHDX). Azure stöder virtuella datorer i generation 1 och generation 2 som är i VHD-filformat och har en disk med fast storlek. Den maximala storlek som tillåts för OS-VHD: n på en virtuell dator i generation 1 är 2 TB.

Du kan konvertera en VHDX-fil till en virtuell hård disk, konvertera en dynamiskt expanderande disk till en disk med fast storlek, men du kan inte ändra den virtuella datorns generation. Mer information finns i [ska jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) och [stöd för virtuella datorer i generation 2 på Azure](../generation-2.md).

Information om support policyn för virtuella Azure-datorer finns i [Microsoft Server Software support för virtuella Azure-datorer](https://support.microsoft.com/help/2721672/).

> [!NOTE]
> Anvisningarna i den här artikeln gäller:
>
> - 64-bitars versionen av Windows Server 2008 R2 och senare Windows Server-operativsystem. Information om hur du kör ett 32-bitars operativ system i Azure finns i [stöd för 32-bitars operativ system i virtuella Azure-datorer](https://support.microsoft.com/help/4021388/).
> - Om något katastrof återställnings verktyg används för att migrera arbets belastningen, t. ex. Azure Site Recovery eller Azure Migrate, krävs fortfarande den här processen på gäst operativ systemet för att förbereda avbildningen före migreringen.

## <a name="system-file-checker"></a> systemfilskontroll

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>Kör Windows system fils Checker-verktyget innan du generaliserar OS-avbildningen

System fils Checker (SFC) används för att verifiera och ersätta Windows-systemfiler.

> [!IMPORTANT]
> Använd en upphöjd PowerShell-session för att köra exemplen i den här artikeln.

Kör SFC-kommandot:

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

När SFC-genomsökningen är klar installerar du Windows-uppdateringar och startar om datorn.

## <a name="set-windows-configurations-for-azure"></a>Ange Windows-konfigurationer för Azure

> [!NOTE]
> Azure-plattformen monterar en ISO-fil på DVD-ROM när en virtuell Windows-dator skapas från en generaliserad avbildning. Av den anledningen måste DVD-ROM-skivan vara aktive rad i operativ systemet i den generaliserade avbildningen. Om den är inaktive rad kommer den virtuella Windows-datorn att fastna i OOBE (out-of-Box Experience).

1. Ta bort alla statiska permanenta vägar i routningstabellen:

   - Kör om du vill visa routningstabellen `route.exe print` .
   - Kontrol lera avsnittet **beständiga vägar** . Om det finns en beständig väg använder du `route.exe delete` kommandot för att ta bort det.

1. Ta bort WinHTTP-proxyn:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    Om den virtuella datorn behöver arbeta med en speciell proxy lägger du till ett proxy-undantag för Azure IP-adressen ([168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)) så att den virtuella datorn kan ansluta till Azure:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. Öppna DiskPart:

   ```powershell
   diskpart.exe
   ```

   Ange diskens SAN-princip till [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11)) :

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Ange UTC-tid (Coordinated Universal Time) för Windows. Ange också start typen för Windows tids tjänst **W32Time** till **Automatisk**:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Ange energi profilen till hög prestanda:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. Kontrol lera att miljövariablerna **Temp** och **tmp** har angetts till standardvärdena:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```

## <a name="check-the-windows-services"></a>Kontrollera Windows-tjänsterna

Kontrol lera att följande Windows-tjänster är inställda på Windows-standardvärdet. De här tjänsterna är de lägsta som måste konfigureras för att säkerställa VM-anslutningen. Ange start inställningarna genom att köra följande exempel:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>Uppdatera inställningar för fjärr skrivbords register

Kontrol lera att följande inställningar är korrekt konfigurerade för fjärråtkomst:

> [!NOTE]
> Om du får ett fel meddelande när du kör `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` kan du ignorera det på ett säkert sätt. Det innebär att domänen inte anger den konfigurationen genom ett grupprincip-objekt.

1. Remote Desktop Protocol (RDP) är aktiverat:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. RDP-porten ställs in korrekt med standard porten 3389:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   När du distribuerar en virtuell dator skapas standard reglerna för port 3389. Om du vill ändra port numret gör du det efter att den virtuella datorn har distribuerats i Azure.

1. Lyssnaren lyssnar på varje nätverks gränssnitt:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. Konfigurera NLA-läge (Network-Level Authentication) för RDP-anslutningarna:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. Ange värdet för Keep-Alive:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Ange alternativ för åter anslutning:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Begränsa antalet samtidiga anslutningar:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. Ta bort alla självsignerade certifikat som är kopplade till RDP-lyssnaren:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Den här koden säkerställer att du kan ansluta när du distribuerar den virtuella datorn. Du kan också granska inställningarna när den virtuella datorn har distribuerats i Azure.

1. Om den virtuella datorn tillhör en domän kontrollerar du följande principer för att se till att de tidigare inställningarna inte har återställts.

    |                 Mål                  |                                                                            Policy                                                                            |                           Värde                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RDP är aktiverat                        | Dator konfiguration \ konfiguration Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Connections         | Tillåt användare att fjärrans luta via fjärr skrivbord    |
    | Princip för NLA-grup princip                      | Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security                                                    | Kräv användarautentisering för fjärråtkomst med hjälp av NLA |
    | Keep-Alive-inställningar                   | Dator konfiguration \ konfiguration \ Settings\Administrative \ \ Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Konfigurera intervall för Keep-Alive-anslutning                   |
    | Återanslut inställningar                    | Dator konfiguration \ konfiguration \ Settings\Administrative \ \ Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Återanslut automatiskt                                    |
    | Begränsat antal anslutnings inställningar | Dator konfiguration \ konfiguration \ Settings\Administrative \ \ Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Begränsa antalet anslutningar                                |

## <a name="configure-windows-firewall-rules"></a>Konfigurera regler för Windows-brandväggen

1. Aktivera Windows-brandväggen på de tre profilerna (domän, standard och offentligt):

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. Kör följande exempel för att tillåta WinRM genom de tre brand Väggs profilerna (domän, privat och offentlig) och aktivera PowerShell-fjärrtjänsten:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. Aktivera följande brand Väggs regler för att tillåta RDP-trafik:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. Aktivera regeln för fil-och skrivar delning så att den virtuella datorn kan svara på ping-begäranden i det virtuella nätverket:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Skapa en regel för Azure Platform Network:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. Om den virtuella datorn tillhör en domän kontrollerar du följande Azure AD-principer för att kontrol lera att de tidigare inställningarna inte har återställts.

    |                 Mål                 |                                                                         Policy                                                                          |                  Värde                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Aktivera profiler för Windows-brandväggen | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Skydda alla nätverks anslutningar         |
    | Aktivera RDP                           | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Tillåt inkommande fjärr skrivbords undantag |
    |                                      | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | Tillåt inkommande fjärr skrivbords undantag |
    | Aktivera ICMP-v4                       | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Tillåt ICMP-undantag                   |
    |                                      | Datorn konfiguration \ konfiguration Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | Tillåt ICMP-undantag                   |

## <a name="verify-the-vm"></a>Verifiera den virtuella datorn

Kontrol lera att den virtuella datorn är felfri, säker och RDP-tillgänglig:

1. Kontrol lera att disken är felfri och konsekvent genom att kontrol lera disken vid nästa omstart av datorn:

   ```powershell
   chkdsk.exe /f
   ```

   Se till att rapporten innehåller en ren och felfri disk.

1. Ange inställningarna för Boot Configuration Data (BCD).

   ```powershell
   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Dump-loggen kan vara till hjälp vid fel sökning av problem med Windows-krasch. Aktivera dump-logg samling:

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Kontrol lera att lagrings platsen för Windows Management Instrumentation (WMI) är konsekvent:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Om lagrings platsen är skadad, se [WMI: skadad databas eller inte](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484).

1. Se till att inga andra program använder port 3389. Den här porten används för RDP-tjänsten i Azure. Om du vill se vilka portar som används på den virtuella datorn kör du `netstat.exe -anob` :

   ```powershell
   netstat.exe -anob
   ```

1. Ladda upp en virtuell Windows-hårddisk som är en domänkontrollant:

   - Följ [dessa extra steg](https://support.microsoft.com/kb/2904015) för att förbereda disken.

   - Se till att du känner till DSRM-lösenordet (Directory Services Restore Mode) om du skulle behöva starta den virtuella datorn i DSRM. Mer information finns i [Ange ett DSRM-lösenord](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11)).

1. Se till att du känner till det inbyggda administratörs kontot och lösen ordet. Du kanske vill återställa det aktuella lokala administratörs lösen ordet och se till att du kan använda det här kontot för att logga in i Windows via RDP-anslutningen. Åtkomst behörigheten styrs av grupprincip-objektet "Tillåt inloggning via Fjärrskrivbordstjänster". Visa det här objektet i redigerare för lokalt grupprincipobjekt:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. Kontrol lera följande Azure AD-principer för att se till att de inte blockerar RDP-åtkomst:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Kontrol lera följande Azure AD-princip för att se till att de inte tar bort några av de nödvändiga åtkomst kontona:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   Principen ska lista följande grupper:

   - Administratörer

   - Ansvariga för säkerhetskopiering

   - Alla

   - Användare

1. Starta om den virtuella datorn för att kontrol lera att Windows fortfarande är felfritt och kan nås via RDP-anslutningen. I det här läget kan du överväga att skapa en virtuell dator på din lokala Hyper-V-server för att se till att den virtuella datorn startar helt. Testa sedan för att kontrol lera att du kan komma åt den virtuella datorn via RDP.

1. Ta bort alla filter för filter för extra transport driv Rutins gränssnitt (TDI). Ta till exempel bort program vara som analyserar TCP-paket eller extra brand väggar.

1. Avinstallera eventuella program från tredje part eller driv rutin som är relaterade till fysiska komponenter eller någon annan virtualiseringsteknik.

### <a name="install-windows-updates"></a>Installera Windows-uppdateringar

Vi rekommenderar att du behåller datorn uppdaterad till *korrigerings nivån*, om detta inte är möjligt, se till att följande uppdateringar är installerade. För att få de senaste uppdateringarna går du till Windows updates historik sidor: [Windows 10 och Windows server 2019](https://support.microsoft.com/help/4000825), [Windows 8,1 och Windows Server 2012 R2](https://support.microsoft.com/help/4009470) och [Windows 7 SP1 och Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

<br />

|        Komponent        |     Binär     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8,1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10-v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 – KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| Nätverk                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Kärna                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Fjärrskrivbordstjänster | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 – KB3000850          | 10.0.14393.0 - KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| Säkerhet                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE – 2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> För att undvika en oavsiktlig omstart under VM-etablering rekommenderar vi att du ser till att alla Windows Update installationer är klara och att inga uppdateringar väntar. Ett sätt att göra detta är att installera alla möjliga Windows-uppdateringar och starta om en gång innan du kör `sysprep.exe` kommandot.

## <a name="determine-when-to-use-sysprep"></a>Avgöra när Sysprep ska användas

Verktyget System preparation ( `sysprep.exe` ) är en process som du kan köra för att återställa en Windows-installation.
Sysprep tillhandahåller en "direkt användning"-upplevelse genom att ta bort all personlig information och återställa flera komponenter.

Du kör vanligt vis `sysprep.exe` för att skapa en mall som du kan använda för att distribuera flera virtuella datorer som har en speciell konfiguration. Mallen kallas en *generaliserad avbildning*.

Om du bara vill skapa en virtuell dator från en disk behöver du inte använda Sysprep. I stället kan du skapa den virtuella datorn från en *specialiserad avbildning*. Information om hur du skapar en virtuell dator från en specialiserad disk finns i:

- [Skapa en virtuell dator från en särskild disk](create-vm-specialized.md)
- [Skapa en virtuell dator från en specialiserad virtuell hård disk](./create-vm-specialized-portal.md)

Om du vill skapa en generaliserad avbildning måste du köra Sysprep. Mer information finns i [så här använder du Sysprep: en introduktion](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Alla roller och program som är installerade på en Windows-baserad dator stöder inte generaliserade avbildningar. Innan du använder den här proceduren kontrollerar du att Sysprep stöder datorns roll. Mer information finns i [Sysprep-stöd för Server roller](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

I synnerhet kräver Sysprep att enheterna måste dekrypteras fullständigt innan de körs. Om du har aktiverat kryptering på den virtuella datorn inaktiverar du det innan du kör Sysprep.


### <a name="generalize-a-vhd"></a>Generalisera en virtuell hård disk

>[!NOTE]
> När du `sysprep.exe` har kört följande steg stänger du av den virtuella datorn. Aktivera inte det igen förrän du har skapat en avbildning från den i Azure.

1. Logga in på den virtuella Windows-datorn.
1. Kör en PowerShell-session som administratör.
1. Ta bort Panther-katalogen (C:\Windows\Panther).
1. Ändra katalogen till `%windir%\system32\sysprep` . Kör sedan `sysprep.exe`.
1. I dialog rutan **system förberedelse verktyg** väljer du **Använd OOBE (system out-of-Box Experience)** och kontrollerar att kryss rutan **generalize** är markerad.

    ![System förberedelse verktyg](media/prepare-for-upload-vhd-image/syspre.png)
1. I **avslutnings alternativ** väljer du **Stäng** av.
1. Välj **OK**.
1. När Sysprep är klar stänger du den virtuella datorn. Använd inte **omstart** för att stänga av den virtuella datorn.

Nu kan den virtuella hård disken laddas upp. Mer information om hur du skapar en virtuell dator från en generaliserad disk finns i [överföra en generaliserad virtuell hård disk och använda den för att skapa en ny virtuell dator i Azure](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized).

>[!NOTE]
> Det finns inte stöd för en anpassad *unattend.xml* -fil. Även om vi har stöd för egenskapen **additionalUnattendContent** , som endast ger begränsat stöd för att lägga till [Microsoft-Windows-Shell-Setup-](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) alternativ i *unattend.xml* -filen som Azure Provisioning-agenten använder. Du kan till exempel använda [additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent) för att lägga till FirstLogonCommands och LogonCommands. Mer information finns i [AdditionalUnattendContent FirstLogonCommands-exempel](https://github.com/Azure/azure-quickstart-templates/issues/1407).

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>Konvertera den virtuella disken till en virtuell hård disk med fast storlek

Använd någon av metoderna i det här avsnittet för att konvertera och ändra storlek på den virtuella disken till det format som krävs för Azure:

1. Säkerhetskopiera den virtuella datorn innan du kör konverteringen av den virtuella disken eller storleks ändrings processen.

1. Kontrol lera att Windows-VHD fungerar korrekt på den lokala servern. Lös eventuella fel i den virtuella datorn innan du försöker konvertera eller ladda upp den till Azure.

1. Konvertera den virtuella disken till typen Fixed.

1. Ändra storlek på den virtuella disken så att den uppfyller Azure-kraven:

   1. Diskar i Azure måste ha en virtuell storlek som är justerad till 1 MiB. Om den virtuella hård disken är en bråkdel av 1 MiB måste du ändra storlek på disken till en multipel av 1 MiB. Diskar som är bråktal i en MiB orsakar fel vid skapande av avbildningar från den uppladdade virtuella hård disken. För att verifiera storleken kan du använda PowerShell-cmdleten [Get-VHD](/powershell/module/hyper-v/get-vhd) för att Visa "size", som måste vara en multipel av 1 MIB i Azure och "FILESIZE", vilket är lika med "size" plus 512 byte för VHD-foten.
   
   1. Den maximala storlek som tillåts för OS-VHD: n med en virtuell dator i generation 1 är 2 048 GiB (2 TiB). 
   1. Den maximala storleken för en datadisk är 32 767 GiB (32 TiB).

> [!NOTE]
> - Om du förbereder en Windows OS-disk efter att du har konverterat till en fast disk och ändrat storlek vid behov, skapar du en virtuell dator som använder disken. Starta och logga in på den virtuella datorn och fortsätt med avsnitten i den här artikeln för att slutföra förberedelserna för att ladda upp.  
> - Om du förbereder en datadisk kan du sluta med det här avsnittet och fortsätta att ladda upp disken.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Använd Hyper-V Manager för att konvertera disken

1. Öppna Hyper-V Manager och välj den lokala datorn till vänster. I menyn ovanför dator listan väljer du **åtgärd**  >  **Redigera disk**.
1. På sidan **hitta virtuell hård disk** väljer du den virtuella disken.
1. På sidan **Välj åtgärd** väljer du **konvertera**  >  **Nästa**.
1. Om du vill konvertera från VHDX väljer du **VHD**  >  **Nästa**.
1. Om du vill konvertera från en dynamiskt expanderande disk väljer du **fast storlek**  >  **Nästa**.
1. Leta upp och välj en sökväg för att spara den nya VHD-filen.
1. Välj **Slutför**.

### <a name="use-powershell-to-convert-the-disk"></a>Använd PowerShell för att konvertera disken

Du kan konvertera en virtuell disk med cmdleten [Convert-VHD](/powershell/module/hyper-v/convert-vhd) i PowerShell. Om du behöver information om hur du installerar denna cmdlet, se [Installera Hyper-V-rollen](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

I följande exempel konverteras disken från VHDX till VHD. Den konverterar också disken från en dynamiskt expanderande disk till en disk med fast storlek.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

I det här exemplet ersätter du värdet för **sökväg** med sökvägen till den virtuella hård disk som du vill konvertera. Ersätt värdet för **DestinationPath** med den nya sökvägen till och namnet på den konverterade disken.

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>Använd Hyper-V Manager för att ändra storlek på disken

1. Öppna Hyper-V Manager och välj den lokala datorn till vänster. I menyn ovanför dator listan väljer du **åtgärd**  >  **Redigera disk**.
1. På sidan **hitta virtuell hård disk** väljer du den virtuella disken.
1. På sidan **Välj åtgärd** väljer du **expandera**  >  **Nästa**.
1. På sidan **hitta virtuell hård disk** anger du den nya storleken i GIB > **Nästa**.
1. Välj **Slutför**.

### <a name="use-powershell-to-resize-the-disk"></a>Använd PowerShell för att ändra storlek på disken

Du kan ändra storlek på en virtuell disk med hjälp av [ändra storlek-VHD-](/powershell/module/hyper-v/resize-vhd) cmdlet i PowerShell. Om du behöver information om hur du installerar denna cmdlet, se [Installera Hyper-V-rollen](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

I följande exempel ändras storleken på disken från 100,5 MiB till 101 MiB för att uppfylla kraven för Azures justering.

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

I det här exemplet ersätter du värdet för **sökväg** med sökvägen till den virtuella hård disk som du vill ändra storlek på. Ersätt värdet för **SizeBytes** med den nya storleken i byte för disken.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertera från VMware VMDK disk format

Om du har en avbildning av en virtuell Windows-dator i [formatet VMDK](https://en.wikipedia.org/wiki/VMDK)kan du använda [Azure Migrate](../../migrate/server-migrate-overview.md) för att konvertera VMDK och ladda upp den till Azure.

## <a name="complete-the-recommended-configurations"></a>Slutför de rekommenderade konfigurationerna

Följande inställningar påverkar inte VHD-uppladdning. Vi rekommenderar dock starkt att du har konfigurerat dem.

- Installera [Azure Virtual Machine-agenten](https://go.microsoft.com/fwlink/?LinkID=394789). Sedan kan du aktivera VM-tillägg. VM-tilläggen implementerar de flesta av de kritiska funktioner som du kanske vill använda med dina virtuella datorer. Du behöver tillägg, till exempel för att återställa lösen ord eller konfigurera RDP. Mer information finns i [Översikt över Azure Virtual Machine agent](../extensions/agent-windows.md).
- När du har skapat den virtuella datorn i Azure rekommenderar vi att du sätter växlings filen på den *temporala volym volymen* för att förbättra prestandan. Du kan ställa in fil placeringen på följande sätt:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  Om en datadisk är ansluten till den virtuella datorn är den temporala enhets volymens beteckning normalt *D*. Den här beteckningen kan vara olika beroende på dina inställningar och antalet tillgängliga enheter.

  - Vi rekommenderar att du inaktiverar skript block som kan tillhandahållas av antivirus program. De kan störa och blockera Windows Provisioning agent-skript som körs när du distribuerar en ny virtuell dator från avbildningen.

## <a name="next-steps"></a>Nästa steg

- [Ladda upp en Windows VM-avbildning till Azure för Resource Manager-distributioner](upload-generalized-managed.md)
- [Felsök problem med aktivering av virtuella Azure Windows-datorer](../troubleshooting/troubleshoot-activation-problems.md)
