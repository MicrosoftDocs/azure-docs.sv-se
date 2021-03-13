---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 881d22592e57ef14885eedf2330c71ffa154a136
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603337"
---
Beroende på klientens operativ system är procedurerna för fjärr anslutning till enheten olika.

### <a name="remotely-connect-from-a-windows-client"></a>Fjärrans luta från en Windows-klient

Innan du börjar kontrollerar du att Windows-klienten kör Windows PowerShell 5,0 eller senare.

Följ de här stegen för att fjärrans luta från en Windows-klient.

1. Kör en Windows PowerShell-session som administratör.
2. Kontrol lera att tjänsten Windows Remote Management körs på klienten. Skriv följande i kommandotolken:

    `winrm quickconfig`

    Mer information finns i [installation och konfiguration för Windows Remote Management](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Tilldela en variabel till enhetens IP-adress.

    $ip = "<device_ip>"

    Ersätt `<device_ip>` med enhetens IP-adress.

4. Om du vill lägga till IP-adressen för enheten i klientens lista över betrodda värdar, skriver du följande kommando:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Starta en Windows PowerShell-session på enheten:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Ange lösen ordet när du uppmanas till det. Använd samma lösen ord som används för att logga in på det lokala webb gränssnittet. Standard lösen ordet för lokalt webb gränssnitt är *Password1*. När du ansluter till enheten via fjärr-PowerShell visas följande exempel på utdata:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Fjärrans luta från en Linux-klient

På den Linux-klient som du ska använda för att ansluta:

- [Installera den senaste PowerShell Core för Linux](/powershell/scripting/install/installing-powershell-core-on-linux) från GitHub för att hämta SSH-funktionen för fjärr kommunikation. 
- [Installera endast `gss-ntlmssp` paketet från NTLM-modulen](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). För Ubuntu-klienter använder du följande kommando:
    - `sudo apt-get install gss-ntlmssp`

Mer information finns i PowerShell- [fjärrkommunikation via SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Följ de här stegen för att fjärrans luta från en NFS-klient.

1. Öppna PowerShell-sessionen genom att skriva:

    `pwsh`
 
2. För att ansluta med fjärran sluten klient skriver du:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Ange det lösen ord som används för att logga in på enheten när du uppmanas till det.
 
> [!NOTE]
> Den här proceduren fungerar inte på Mac OS.