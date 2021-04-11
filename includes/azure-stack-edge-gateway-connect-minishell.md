---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081028"
---
Beroende på klientens operativ system är procedurerna för fjärr anslutning till enheten olika.

### <a name="remotely-connect-from-a-windows-client"></a>Fjärrans luta från en Windows-klient


#### <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

- Windows-klienten kör Windows PowerShell 5,0 eller senare.
- Din Windows-klient har den signerings kedja (rot certifikat) som motsvarar det nod-certifikat som är installerat på enheten. Detaljerade anvisningar finns i [Installera certifikat på Windows-klienten](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- `hosts`Filen som finns på `C:\Windows\System32\drivers\etc` för din Windows-klient har en post som motsvarar nodens certifikat i följande format:

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Här är ett exempel på en post för `hosts` filen:
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Detaljerade steg

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

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    Om du ser ett fel som är relaterat till förtroende relationen kontrollerar du om signerings kedjan för det Node-certifikat som har överförts till enheten också installeras på klienten som har åtkomst till enheten.

    Om du inte använder certifikaten (vi rekommenderar att du använder certifikaten!) kan du hoppa över den här kontrollen genom att använda session alternativen: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` .

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > När du använder `-UseSSL` alternativet är du fjärr kommunikation via PowerShell över *https*. Vi rekommenderar att du alltid använder *https* för fjärr anslutning via PowerShell. 

6. Ange lösen ordet när du uppmanas till det. Använd samma lösen ord som används för att logga in på det lokala webb gränssnittet. Standard lösen ordet för lokalt webb gränssnitt är *Password1*. När du ansluter till enheten via fjärr-PowerShell visas följande exempel på utdata:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

> [!IMPORTANT]
> I den aktuella versionen kan du bara ansluta till PowerShell-gränssnittet på enheten via en Windows-klient. `-UseSSL`Alternativet fungerar inte med Linux-klienterna.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->