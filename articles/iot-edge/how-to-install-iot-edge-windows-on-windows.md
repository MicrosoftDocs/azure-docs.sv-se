---
title: Installera Azure IoT Edge för Windows | Microsoft Docs
description: Installera Azure IoT Edge för Windows-behållare på Windows-enheter
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: bb87d09b67658f9a3d7c68f635bfcd9a29de675c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201635"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Installera och hantera Azure IoT Edge med Windows-behållare

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge runtime är vad som förvandlar en enhet till en IoT Edge enhet. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogiken till den från molnet. Mer information finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Det finns två steg för att konfigurera en IoT Edge enhet. Det första steget är att installera körningen och dess beroenden. Det andra steget är att ansluta enheten till sin identitet i molnet och konfigurera autentisering med IoT Hub.

I den här artikeln beskrivs stegen för att installera Azure IoT Edge runtime med Windows-behållare. Om du vill använda Linux-behållare på en Windows-enhet kan du läsa artikeln [Azure IoT Edge för Linux på Windows](how-to-install-iot-edge-on-windows.md) .

>[!NOTE]
>Azure IoT Edge med Windows-behållare stöds inte från och med version 1,2 av Azure IoT Edge.
>
>Överväg att använda den nya metoden för att köra IoT Edge på Windows-enheter [Azure IoT Edge för Linux i Windows](iot-edge-for-linux-on-windows.md).

## <a name="prerequisites"></a>Förutsättningar

* En Windows-enhet

  IoT Edge med Windows-behållare kräver Windows version 1809/build 17763, vilket är den senaste versionen av [Windows långtids support](/windows/release-information/). Se till att gå igenom [listan över system](support.md#operating-systems) som stöds för att se en lista över SKU: er som stöds.

* Ett [registrerat enhets-ID](how-to-register-device.md)

  Om du har registrerat enheten med symmetrisk nyckel autentisering, så är enhets anslutnings strängen klar.

  Om du har registrerat din enhet med en självsignerad 509-autentisering i X måste du ha minst ett av de identitets certifikat som du använde för att registrera enheten och dess matchande privata privata nyckel som är tillgänglig på din enhet.

## <a name="install-a-container-engine"></a>Installera en behållar motor

Azure IoT Edge förlitar sig på en OCI-kompatibel container runtime som [Moby](https://github.com/moby/moby). En Moby-baserad motor som ingår i installations skriptet. Det finns inga ytterligare steg för att installera motorn.

## <a name="install-the-iot-edge-security-daemon"></a>Installera IoT Edge Security daemon

1. Kör PowerShell som administratör.

   Använd en AMD64-session av PowerShell, inte PowerShell (x86). Om du är osäker på vilken typ av session du använder kör du följande kommando:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Kör kommandot [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , som utför följande uppgifter:

   * Kontrollerar att Windows-datorn finns på en version som stöds.
   * Aktiverar funktionen containers.
   * Laddar ned Moby-motorn och IoT Edge Runtime.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Starta om enheten om du uppmanas att göra det.

När du installerar IoT Edge på en enhet kan du använda ytterligare parametrar för att ändra processen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Peka installations programmet till en lokal katalog för offline-installation.

Mer information om dessa ytterligare parametrar finns i [PowerShell-skript för IoT Edge med Windows-behållare](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Etablera enheten med dess moln identitet

Nu när behållar motorn och IoT Edge runtime är installerade på enheten är du redo för nästa steg, vilket är att konfigurera enheten med dess moln identitet och autentiseringsinformation.

Välj nästa avsnitt baserat på vilken autentiseringstyp du vill använda:

* [Alternativ 1: autentisera med symmetriska nycklar](#option-1-authenticate-with-symmetric-keys)
* [Alternativ 2: autentisera med X. 509-certifikat](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Alternativ 1: autentisera med symmetriska nycklar

I det här läget installeras IoT Edge runtime på din Windows-enhet och du måste etablera enheten med dess moln identitet och autentiseringsinformation.

Det här avsnittet går igenom stegen för att etablera en enhet med symmetrisk nyckel autentisering. Du bör ha registrerat enheten i IoT Hub och hämtat anslutnings strängen från enhets informationen. Annars följer du stegen i [Registrera en IoT Edge enhet i IoT Hub](how-to-register-device.md).

1. Kör PowerShell som administratör på IoT Edge-enheten.

2. Använd kommandot [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) för att konfigurera IoT Edge runtime på din dator. Kommandot är standardvärdet för manuell etablering med Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Om du har laddat ned IoTEdgeSecurityDaemon.ps1-skriptet till enheten för offline eller en speciell versions installation, måste du se till att referera till den lokala kopian av skriptet.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. När du uppmanas till det anger du enhets anslutnings strängen som du hämtade i föregående avsnitt. Enhets anslutnings strängen kopplar den fysiska enheten med ett enhets-ID i IoT Hub och ger autentiseringsinformation.

   Enhets anslutnings strängen har följande format och ska inte innehålla citat tecken: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

När du etablerar en enhet manuellt kan du använda ytterligare parametrar för att ändra processen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Deklarera en speciell avbildning av edgeAgent-behållaren och ange autentiseringsuppgifter om den finns i ett privat register

Mer information om dessa ytterligare parametrar finns i [PowerShell-skript för IoT Edge med Windows-behållare](reference-windows-scripts.md).

### <a name="option-2-authenticate-with-x509-certificates"></a>Alternativ 2: autentisera med X. 509-certifikat

I det här läget installeras IoT Edge runtime på din Windows-enhet och du måste etablera enheten med dess moln identitet och autentiseringsinformation.

Det här avsnittet går igenom stegen för att etablera en enhet med X. 509 certifikatautentisering. Du bör ha registrerat din enhet i IoT Hub och tillhandahålla tumavtrycken som matchar certifikatet och den privata nyckeln som finns på din IoT Edge-enhet. Annars följer du stegen i [Registrera en IoT Edge enhet i IoT Hub](how-to-register-device.md).

1. Kör PowerShell som administratör på IoT Edge-enheten.

2. Använd kommandot [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) för att konfigurera IoT Edge runtime på din dator.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Om du har laddat ned IoTEdgeSecurityDaemon.ps1-skriptet till enheten för offline eller en speciell versions installation, måste du se till att referera till den lokala kopian av skriptet.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. När du uppmanas att göra det anger du följande information:

   * **IotHubHostName**: värd namnet för den IoT-hubb som enheten ska ansluta till. Till exempel `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: det ID som du angav när du registrerade enheten.
   * **X509IdentityCertificate**: absolut sökväg till ett identitets certifikat på enheten. Till exempel `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: absolut sökväg till den privata nyckel filen för det tillhandahållna identitets certifikatet. Till exempel `C:\path\identity_key.pem`.

När du etablerar en enhet manuellt kan du använda ytterligare parametrar för att ändra processen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Deklarera en speciell avbildning av edgeAgent-behållaren och ange autentiseringsuppgifter om den finns i ett privat register

Mer information om dessa ytterligare parametrar finns i [PowerShell-skript för IoT Edge med Windows-behållare](reference-windows-scripts.md).

## <a name="offline-or-specific-version-installation-optional"></a>Offline eller en speciell versions installation (valfritt)

Stegen i det här avsnittet gäller scenarier som inte omfattas av standard installations stegen. Detta kan vara:

* Installera IoT Edge offline
* Installera en version av Release Candidate
* Installera en annan version än den senaste

Under installationen hämtas tre filer:

* Ett PowerShell-skript som innehåller installations anvisningarna
* Microsoft Azure IoT Edge CAB, som innehåller IoT Edge Security daemon (iotedged), Moby container Engine och Moby CLI
* Installations program för Visual C++ Redistributable Package (VC Runtime)

Om enheten ska vara offline under installationen, eller om du vill installera en version av IoT Edge, kan du hämta filerna i förväg till enheten. När det är dags att installera, kan du peka installations skriptet i katalogen som innehåller de hämtade filerna. Installations programmet kontrollerar först katalogen och hämtar bara komponenter som inte hittas. Om alla filer är tillgängliga offline kan du installera utan Internet anslutning.

1. De senaste IoT Edge-installationsfilerna tillsammans med tidigare versioner finns i [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases).

2. Leta upp den version som du vill installera och ladda ned följande filer från avsnittet **till gångar** i viktig information på din IoT-enhet:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab från 1,1 versions kanal.

   Det är viktigt att använda PowerShell-skriptet från samma version som. cab-filen som du använder eftersom funktionen ändras till att stödja funktionerna i varje version.

3. Om CAB-filen som du laddade ned har ett arkitektur-suffix, byter du namn på filen till bara **Microsoft-Azure-IoTEdge.cab**.

4. Du kan också hämta ett installations program för Visual C++ Redistributable. PowerShell-skriptet använder till exempel den här versionen: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Spara installations programmet i samma mapp på din IoT-enhet som IoT Edge-filer.

5. Om du vill installera med offline-komponenter kan du [punkt källa](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) den lokala kopian av PowerShell-skriptet.

6. Kör kommandot [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) med `-OfflineInstallationPath` parametern. Ange den absoluta sökvägen till fil katalogen. Exempel:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Distributions kommandot använder de komponenter som finns i den lokala fil katalogen. Om CAB-filen eller Visual C++-installationsprogrammet saknas försöker den hämta dem.

## <a name="update-iot-edge"></a>Uppdatera IoT Edge

Använd `Update-IoTEdge` kommandot för att uppdatera Security daemon. Skriptet hämtar automatiskt den senaste versionen av Security daemon.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Att köra kommandot Update-IoTEdge tar bort och uppdaterar säkerhets daemonen från enheten, tillsammans med de två behållar avbildningarna. Config. yaml-filen sparas på enheten, samt data från Moby container Engine. Att behålla konfigurations informationen innebär att du inte behöver ange anslutnings strängen eller enhets etablerings tjänstens information för enheten igen under uppdaterings processen.

Om du vill uppdatera till en speciell version av Security daemon letar du reda på versionen från 1,1 versions kanal som du vill rikta mot [IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases). I den versionen hämtar du **Microsoft-Azure-IoTEdge.cab** -filen. Använd sedan `-OfflineInstallationPath` parametern för att peka på den lokala fil platsen. Exempel:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath`Parametern söker efter en fil med namnet **Microsoft-Azure-IoTEdge.cab** i den angivna katalogen. Byt namn på filen om du vill ta bort Architecture-suffixet.

Om du vill uppdatera en enhet offline söker du efter den version som du vill använda för [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases). I den versionen laddar du ned *IoTEdgeSecurityDaemon.ps1* och *Microsoft-Azure-IoTEdge.cab* filer. Det är viktigt att använda PowerShell-skriptet från samma version som. cab-filen som du använder eftersom funktionen ändras till att stödja funktionerna i varje version.

Om CAB-filen som du laddade ned har ett arkitektur-suffix, byter du namn på filen till bara **Microsoft-Azure-IoTEdge.cab**.

Om du vill uppdatera med offline-komponenter kan du [punkt källa](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) den lokala kopian av PowerShell-skriptet. Använd sedan `-OfflineInstallationPath` parametern som en del av `Update-IoTEdge` kommandot och ange den absoluta sökvägen till fil katalogen. Exempel:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Om du vill ha mer information om uppdaterings alternativ använder du kommandot `Get-Help Update-IoTEdge -full` eller läser [PowerShell-skript för IoT Edge med Windows-behållare](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge installationen från enheten använder du följande kommandon.

Om du vill ta bort IoT Edge installationen från Windows-enheten använder du kommandot [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) från ett administrations PowerShell-fönster. Det här kommandot tar bort IoT Edge runtime, tillsammans med den befintliga konfigurationen och Moby-motorns data.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Om du vill ha mer information om avinstallations alternativ använder du kommandot `Get-Help Uninstall-IoTEdge -full` .

## <a name="next-steps"></a>Nästa steg

Fortsätt att [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md) och lär dig hur du distribuerar moduler till din enhet.
