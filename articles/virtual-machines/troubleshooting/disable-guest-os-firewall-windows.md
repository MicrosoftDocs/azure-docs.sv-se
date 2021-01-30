---
title: Inaktivera gäst operativ systemets brand vägg i Azure VM | Microsoft Docs
description: Lär dig en lösnings metod för fel söknings situationer där en brand vägg för gäst operativ system filtrerar delvis eller fullständig trafik till en virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 74d06d3d4aaa0d76b80257d2148fb62f71c3fdb0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093203"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Inaktivera brandvägg för gästoperativsystem i en virtuell Azure-dator

Den här artikeln innehåller en referens för situationer där du misstänker att gäst operativ systemets brand vägg filtrerar delvis eller fullständig trafik till en virtuell dator (VM). Detta kan inträffa om ändringar avsiktligt gjordes i brand väggen som orsakade att RDP-anslutningar misslyckats.

## <a name="solution"></a>Lösning

Processen som beskrivs i den här artikeln är avsedd att användas som en lösning så att du kan fokusera på att åtgärda det riktiga problemet, vilket innebär att brand Väggs reglerna konfigureras på rätt sätt. Det är Microsofts bästa praxis att låta komponenten Windows-brandväggen vara aktive rad. Hur du konfigurerar brand Väggs reglerna beror på vilken åtkomst nivå som krävs för den virtuella datorn.

### <a name="online-solutions"></a>Online-lösningar 

Om den virtuella datorn är online och kan nås på en annan virtuell dator i samma virtuella nätverk kan du utföra dessa åtgärder med hjälp av den andra virtuella datorn.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Minskning 1: anpassat skript tillägg eller Kör kommando funktion

Om du har en fungerande Azure-agent kan du använda [anpassat skript tillägg](../extensions/custom-script-windows.md) eller funktionen [Kör kommandon](../windows/run-command.md) (endast virtuella Resource Manager-datorer) för att fjärrköra följande skript.

> [!Note]
> * Kör följande skript om brand väggen har angetts lokalt:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Om brand väggen är inställd via en Active Directory princip kan du använda Kör följande skript för tillfällig åtkomst. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' -name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Men så snart principen tillämpas igen, kommer du att lanseras från fjärrsessionen. Den permanenta korrigeringen för det här problemet är att ändra principen som tillämpas på den här datorn.

#### <a name="mitigation-2-remote-powershell"></a>Minskning 2: fjärr-PowerShell

1.  Anslut till en virtuell dator som finns i samma virtuella nätverk som den virtuella datorn som du inte kan komma åt med hjälp av RDP-anslutning.

2.  Öppna ett fönster i PowerShell-konsolen.

3.  Kör följande kommandon:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Om brand väggen ställs in via ett grupprincip-objekt kanske den här metoden inte fungerar eftersom det här kommandot endast ändrar de lokala register posterna. Om en princip är på plats kommer den att åsidosätta den här ändringen. 

#### <a name="mitigation-3-pstools-commands"></a>Minskning 3: PSTools-kommandon

1.  Hämta [PSTools](/sysinternals/downloads/pstools)på den virtuella datorn för fel sökning.

2.  Öppna en CMD-instans och få åtkomst till den virtuella datorn via dess DIP.

3.  Kör följande kommandon:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Minskning 4: fjär register 

Följ dessa steg om du vill använda [fjär registret](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/314837).

1.  Starta Registereditorn på den virtuella datorn för fel sökning och gå sedan till **fil**  >  **anslutning nätverks register**.

2.  Öppna *mål datorn*\System-grenen och ange följande värden:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Starta om tjänsten. Eftersom du inte kan göra det med hjälp av fjär registret måste du använda konsolen för fjärrtjänst.

4.  Öppna en instans av **Services. msc**.

5.  Klicka på **tjänster (lokalt)**.

6.  Välj **Anslut till en annan dator**.

7.  Ange den **privata IP-adressen (DIP)** för den virtuella datorns problem.

8.  Starta om den lokala brand Väggs principen.

9.  Försök att ansluta till den virtuella datorn via RDP igen från den lokala datorn.

### <a name="offline-solutions"></a>Offline-lösningar 

Om du har en situation där du inte kan komma åt den virtuella datorn med någon annan metod kommer det inte att gå att använda det anpassade skript tillägget och du måste arbeta i OFFLINELÄGE genom att gå direkt genom system disken. Det gör du genom att följa dessa steg:

1.  [Anslut system disken till en virtuell återställnings dator](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en fjärr skrivbords anslutning till den virtuella återställnings datorn.

3.  Kontrol lera att disken är flaggad som online i disk hanterings konsolen. Anteckna enhets beteckningen som är kopplad till den anslutna system disken.

4.  Innan du gör några ändringar skapar du en kopia av mappen \Windows\System32\Config om du behöver återställa ändringarna.

5.  Starta Registereditorn (regedit.exe) på den virtuella datorn för fel sökning. 

6.  I den här fel söknings proceduren monterar vi registreringsdatafiler som BROKENSYSTEM och BROKENSOFTWARE.

7.  Markera HKEY_LOCAL_MACHINE nyckel och välj sedan Arkiv > läsa in Hive på menyn.

8.  Leta upp \windows\system32\config\SYSTEM-filen på den anslutna system disken.

9.  Öppna en upphöjd PowerShell-instans och kör sedan följande kommandon:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Koppla från system disken och skapa den virtuella datorn på nytt](troubleshoot-recovery-disks-portal-windows.md).

11. Kontrol lera om problemet är löst.
