---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 12e67103fb1d16276cc3d8fd6171eee30dd58851
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517588"
---
Beroende på klientens operativ system är procedurerna för fjärr anslutning till enheten olika.

### <a name="remotely-connect-from-a-windows-client"></a>Fjärrans luta från en Windows-klient


#### <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

- Windows-klienten kör Windows PowerShell 5,0 eller senare.
- Din Windows-klient har den signerings kedja (rot certifikat) som motsvarar det nod-certifikat som är installerat på enheten. Detaljerade anvisningar finns i [Installera certifikat på Windows-klienten](../articles/databox-online/azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
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

    > [!NOTE] 
    > När du använder `-UseSSL` alternativet är du fjärr kommunikation via PowerShell över *https*. Vi rekommenderar att du alltid använder *https* för fjärr anslutning via PowerShell. Även om en *http-* session inte är den säkraste anslutnings metoden, är den acceptabel för betrodda nätverk.

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

### <a name="remotely-connect-from-a-linux-client"></a>Fjärrans luta från en Linux-klient

På den Linux-klient som du ska använda för att ansluta:

- [Installera den senaste PowerShell Core för Linux](/powershell/scripting/install/installing-powershell-core-on-linux) från GitHub för att hämta SSH-funktionen för fjärr kommunikation. 
- [Installera endast `gss-ntlmssp` paketet från NTLM-modulen](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). För Ubuntu-klienter använder du följande kommando:
    - `sudo apt-get install gss-ntlmssp`

Mer information finns i PowerShell- [fjärrkommunikation via SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Följ de här stegen för att fjärrans luta från en NFS-klient.

1. Öppna PowerShell-sessionen genom att skriva:

    `sudo pwsh`
 
2. För att ansluta med fjärran sluten klient skriver du:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser -UseSSL`

    Ange det lösen ord som används för att logga in på enheten när du uppmanas till det.
 
> [!NOTE]
> Den här proceduren fungerar inte på Mac OS.