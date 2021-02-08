---
title: Installera C#-agenten på Windows-enhet
description: Lär dig hur du installerar Defender för IoT-agent på 32-bitars eller 64-bitars Windows-enheter.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 6c441c9f81477a586c3e153e23b1fc63f2f76344
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819935"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Distribuera en Defender for IoT C#-baserad säkerhets agent för Windows

Den här guiden beskriver hur du installerar säkerhets agenten Defender för IoT C# i Windows.

I den här guiden får du lära du dig att:

> [!div class="checklist"]
> * Installera
> * Verifiera distributionen
> * Avinstallera agenten
> * Felsöka

## <a name="prerequisites"></a>Förutsättningar

För andra plattformar och agent-varianter, se [Välj rätt säkerhets agent](how-to-deploy-agent.md).

1. Lokal administratörs behörighet på den dator som du vill installera på.

1. [Skapa en säkerhetsmodul](quickstart-create-security-twin.md) för enheten.

## <a name="installation"></a>Installation

Använd följande arbets flöde om du vill installera säkerhets agenten:

1. Installera Defender för IoT Windows C#-agent på enheten. Ladda ned den senaste versionen till datorn från Defender för IoT GitHub- [lagringsplatsen](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Extrahera innehållet i paketet och navigera till mappen/install.

1. Öppna Windows PowerShell som administratör.
1. Lägg till behörigheter som körs i InstallSecurityAgent-skriptet genom att köra:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    kör sedan:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Exempel:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Mer information om autentiseringsmetoder finns i [så här konfigurerar du autentisering](concept-security-agent-authentication-methods.md).

Det här skriptet utför följande åtgärder:

* Installerar nödvändiga komponenter.
* Lägger till en tjänst användare (med interaktiv inloggning inaktive rad).
* Installerar agenten som en **system tjänst**.
* Konfigurerar agenten med de angivna autentiseringsinställningarna.

Om du behöver ytterligare hjälp använder du kommandot Get-Help i PowerShell.

Get-Help exempel:    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verifiera distributions status

Kontrol lera agent distributionens status genom att köra:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Så här avinstallerar du agenten:

1. Kör följande PowerShell-skript med parametern **-mode** inställd på **Uninstall**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Felsökning

Om agenten inte kan starta aktiverar du loggning (loggning är *inaktiverat* som standard) för att få mer information.

Så här aktiverar du loggning:

1. Öppna konfigurations filen (General.config) för redigering med en standard fil redigerare.

1. Redigera följande värden:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Vi rekommenderar att du **inaktiverar utloggning när** fel sökningen har slutförts. **Om** du lämnar loggning ökar logg filens storlek och data användningen.

1. Starta om agenten genom att köra följande PowerShell-eller kommando rad:

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   eller

    **KOMMANDOT**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Granska logg filen om du vill ha mer information om felen. Logg filen finns i arbets katalogen där vi kör skriptet. 

   Plats för loggfil: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Nästa steg

* Läs [översikten över](overview.md) Defender for IoT-tjänsten
* Läs mer om Defender för IoT- [arkitektur](architecture.md)
* Aktivera [tjänsten](quickstart-onboard-iot-hub.md)
* Läs [vanliga frågor och svar](resources-frequently-asked-questions.md)
* Förstå [aviseringar](concept-security-alerts.md)
