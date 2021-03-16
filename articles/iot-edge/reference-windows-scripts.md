---
title: Skript för Azure IoT Edge med Windows-behållare | Microsoft Docs
description: Referensinformation för att installera, avinstallera eller uppdatera Windows-enheter med hjälp av IoT Edge PowerShell-skript
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a919238e4a62ae8954e101cb21a2fd4943191f6a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489532"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>PowerShell-skript för IoT Edge med Windows-behållare

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Förstå de PowerShell-skript som installerar, uppdaterar eller avinstallerar IoT Edge på Windows-enheter.

Kommandona som beskrivs i den här artikeln är från den `IoTEdgeSecurityDaemon.ps1` fil som släpps med alla [IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases). Den senaste versionen av skriptet är alltid tillgänglig på aka.ms/iotedge-win.

Du kan köra något av kommandona med hjälp av- `Invoke-WebRequest` cmdlet: en för att få åtkomst till den senaste skript versionen. Exempel:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

Du kan också hämta skriptet eller en version av skriptet från en speciell version för att köra kommandona. Exempel:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

Det tillhandahållna skriptet är signerat för att öka säkerheten. Du kan kontrol lera signaturen genom att ladda ned skriptet till enheten och sedan köra följande PowerShell-kommando:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Status för utdata är **giltig** om signaturen verifieras.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Deploy-IoTEdge kommandot laddar ned och distribuerar IoT Edge Security daemon och dess beroenden. Distributions kommandot accepterar dessa vanliga parametrar, bland annat. För den fullständiga listan använder du kommandot `Get-Help Deploy-IoTEdge -full` .  

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Container** | **Windows** eller **Linux** | Om inget behållar operativ system anges är Windows standardvärdet.<br><br>För Windows-behållare använder IoT Edge Moby container Engine som ingår i installationen. För Linux-behållare måste du installera en behållar motor innan du startar installationen. |
| **Proxy** | Proxy-URL | Ta med den här parametern om enheten måste gå igenom en proxyserver för att komma åt Internet. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Sökväg till katalog | Om den här parametern tas med, kontrollerar installations programmet katalogen för de IoT Edge CAB-och VC runtime-MSI-filer som krävs för installationen. Filer som inte hittas i katalogen laddas ned. Om båda filerna finns i katalogen kan du installera IoT Edge utan Internet anslutning. Du kan också använda den här parametern för att använda en speciell version. |
| **InvokeWebRequestParameters** | Hash-värde för parametrar och värden | Under installationen görs flera webb förfrågningar. Använd det här fältet om du vill ange parametrar för dessa webb förfrågningar. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | inget | Med den här flaggan kan distributions skriptet starta om datorn utan att behöva ange om det behövs. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Kommandot Initialize-IoTEdge konfigurerar IoT Edge med enhetens anslutnings sträng och drift information. Mycket av den information som genereras av det här kommandot lagras sedan i iotedge\config.yaml-filen. Initierings kommandot accepterar dessa vanliga parametrar, bland annat. För den fullständiga listan använder du kommandot `Get-Help Initialize-IoTEdge -full` .

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **ManualConnectionString** | Inget | **Växlings parameter**. **Standardvärde**. Om ingen etablerings typ anges är manuell etablering med en anslutnings sträng standardvärdet.<br><br>Deklarerar att du kommer att ange en enhets anslutnings sträng för att etablera enheten manuellt. |
| **ManualX509** | Inget | **Växlings parameter**. Om ingen etablerings typ anges är manuell etablering med en anslutnings sträng standardvärdet.<br><br>Deklarerar att du får ett identitets certifikat och en privat nyckel för att etablera enheten manuellt.
| **DpsTpm** | Inget | **Växlings parameter**. Om ingen etablerings typ anges är manuell etablering med en anslutnings sträng standardvärdet.<br><br>Förklarar att du kommer att tillhandahålla ett omfångs-ID för enhets etablerings tjänsten (DPS) och din enhets registrerings-ID för att etablera genom DPS.  |
| **DpsSymmetricKey** | Inget | **Växlings parameter**. Om ingen etablerings typ anges är manuell etablering med en anslutnings sträng standardvärdet.<br><br>Förklarar att du kommer att tillhandahålla ett omfångs-ID för enhets etablerings tjänsten (DPS) och din enhets registrerings-ID för att etablera genom DPS, tillsammans med en symmetrisk nyckel för attestering. |
| **DpsX509** | Inget | **Växlings parameter**. Om ingen etablerings typ anges är manuell etablering med en anslutnings sträng standardvärdet.<br><br>Förklarar att du kommer att tillhandahålla ett omfångs-ID för enhets etablerings tjänsten (DPS) och din enhets registrerings-ID för att etablera via DPS, tillsammans med ett X. 509-identitets certifikat och en privat nyckel för attestering.  |
| **DeviceConnectionString** | En anslutnings sträng från en IoT Edge enhet som är registrerad i en IoT Hub, med enkla citat tecken | **Krävs** för manuell etablering med en anslutnings sträng. Om du inte anger någon anslutnings sträng i skript parametrarna uppmanas du att ange en. |
| **IotHubHostName** | Värdnamn för den IoT-hubb som en enhet ansluter till. | **Krävs** för manuell etablering med X. 509-certifikat. Använder formatet *{Hub Name}. Azure-Devices.net*. |
| **DeviceId** | Enhets-ID: t från en registrerad enhets identitet i IoT Hub. | **Krävs** för manuell etablering med X. 509-certifikat. |
| **ScopeId** | Ett scope-ID från en instans av enhets etablerings tjänsten som är kopplad till din IoT Hub. | **Krävs** för DPS-etablering. Om du inte anger ett omfångs-ID i skript parametrarna uppmanas du att ange ett. |
| **RegistrationId** | Ett registrerings-ID som genereras av din enhet | **Krävs** för DPS-etablering om du använder TPM eller symmetrisk nyckel attestering. **Valfritt** om du använder 509-certifikat för certifikats attestering i X. |
| **X509IdentityCertificate** | URI-sökvägen till X. 509 enhetens identitets certifikat på enheten. | **Krävs** för manuell eller DPS-etablering om du använder X. 509-certifikatets attestering. |
| **X509IdentityPrivateKey** | URI-sökvägen till den X. 509 enhetens identitets certifikat nyckel på enheten. | **Krävs** för manuell eller DPS-etablering om du använder X. 509-certifikatets attestering. |
| **SymmetricKey** | Den symmetriska nyckel som används för att etablera IoT Edge enhets identitet när DPS används | **Krävs** för DPS-etablering om du använder symmetrisk nyckel attestering. |
| **Container** | **Windows** eller **Linux** | Om inget behållar operativ system anges är Windows standardvärdet.<br><br>För Windows-behållare använder IoT Edge Moby container Engine som ingår i installationen. För Linux-behållare måste du installera en behållar motor innan du startar installationen. |
| **DeviceCACertificate** | URI-sökvägen till X. 509-enhetens CA-certifikat på enheten. | Kan också konfigureras i `C:\ProgramData\iotedge\config.yaml` filen. Mer information finns i [Hantera certifikat på en IoT Edge enhet](how-to-manage-device-certificates.md). |
| **DeviceCAPrivateKey** | URI-sökvägen till X. 509-enhetens privata nyckel för certifikat utfärdare på enheten. | Kan också konfigureras i `C:\ProgramData\iotedge\config.yaml` filen. Mer information finns i [Hantera certifikat på en IoT Edge enhet](how-to-manage-device-certificates.md). |
| **InvokeWebRequestParameters** | Hash-värde för parametrar och värden | Under installationen görs flera webb förfrågningar. Använd det här fältet om du vill ange parametrar för dessa webb förfrågningar. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **AgentImage** | IoT Edge agent avbildnings-URI | Som standard använder en ny IoT Edge-installation den senaste rullande taggen för IoT Edge agent-avbildningen. Använd den här parametern för att ange en speciell tagg för avbildnings versionen eller för att ange en egen agent avbildning. Mer information finns i [förstå IoT Edge Taggar](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Användarnamn** | Användar namn för container registret | Använd bara den här parametern om du anger parametern-AgentImage till en behållare i ett privat register. Ange ett användar namn med åtkomst till registret. |
| **Lösenord** | Säker lösen ords sträng | Använd bara den här parametern om du anger parametern-AgentImage till en behållare i ett privat register. Ange lösen ordet för att få åtkomst till registret. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Container** | **Windows** eller **Linux** | Om inget behållar-OS anges är Windows standardvärdet. För Windows-behållare kommer en behållar motor att inkluderas i installationen. För Linux-behållare måste du installera en behållar motor innan du startar installationen. |
| **Proxy** | Proxy-URL | Ta med den här parametern om enheten måste gå igenom en proxyserver för att komma åt Internet. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hash-värde för parametrar och värden | Under installationen görs flera webb förfrågningar. Använd det här fältet om du vill ange parametrar för dessa webb förfrågningar. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Sökväg till katalog | Om den här parametern tas med, kontrollerar installations programmet katalogen för de IoT Edge CAB-och VC runtime-MSI-filer som krävs för installationen. Filer som inte hittas i katalogen laddas ned. Om båda filerna finns i katalogen kan du installera IoT Edge utan Internet anslutning. Du kan också använda den här parametern för att använda en speciell version. |
| **RestartIfNeeded** | inget | Med den här flaggan kan distributions skriptet starta om datorn utan att behöva ange om det behövs. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Force** | inget | Den här flaggan tvingar avinstallationen om det tidigare försöket att avinstallera misslyckades.
| **RestartIfNeeded** | inget | Den här flaggan gör att avinstallations skriptet kan starta om datorn utan att begära det om det behövs. |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder dessa kommandon i följande artikel:

* [Installera eller avinstallera Azure IoT Edge för Windows](how-to-install-iot-edge-windows-on-windows.md)
