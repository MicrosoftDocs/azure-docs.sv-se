---
title: Etablera Windows-enheter automatiskt med DPS-Azure IoT Edge | Microsoft Docs
description: Använd en simulerad enhet på Windows-datorn för att testa automatisk enhets etablering för Azure IoT Edge med enhets etablerings tjänsten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 11279c552cb599c24b72473d6574175450ca7ab0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200857"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Skapa och etablera en simulerad IoT Edge enhet med en virtuell TPM i Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge enheter kan konfigureras automatiskt med [enhets etablerings tjänsten](../iot-dps/index.yml) , precis som enheter som inte är Edge-aktiverade. Om du inte är bekant med processen för automatisk etablering, granskar du [etablerings](../iot-dps/about-iot-dps.md#provisioning-process) översikten innan du fortsätter.

DPS stöder symmetrisk nyckel attestering för IoT Edge enheter i både enskilda registreringar och grupp registrering. Om du markerar alternativet "är IoT Edge enhet" för att få en grupp registrering markeras alla enheter som är registrerade under den registrerings gruppen som IoT Edge enheter.

Den här artikeln visar hur du testar automatisk etablering på en simulerad IoT Edge enhet med följande steg:

* Skapa en instans av IoT Hub Device Provisioning Service (DPS).
* Skapa en simulerad enhet på din Windows-dator med en simulerad Trusted Platform Module (TPM) för maskin varu säkerhet.
* Skapa en enskild registrering för enheten.
* Installera IoT Edge Runtime och Anslut enheten till IoT Hub.

> [!TIP]
> I den här artikeln beskrivs hur du testar automatisk etablering genom att använda TPM-attestering på virtuella enheter, men det är mycket som gäller när du använder den fysiska TPM-maskinvaran.

## <a name="prerequisites"></a>Förutsättningar

* En Windows-utvecklings dator. Den här artikeln använder Windows 10.
* En aktiv IoT Hub.

> [!NOTE]
> TPM 2,0 krävs när du använder TPM-attestering med DPS och kan endast användas för att skapa enskilda, inte grupper, registreringar.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning Service

Skapa en ny instans av IoT Hub Device Provisioning Service i Azure och länka den till din IoT-hubb. Du kan följa anvisningarna i [konfigurera IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

När du har installerat enhets etablerings tjänsten kopierar du värdet för **ID-omfång** från översikts sidan. Du använder det här värdet när du konfigurerar IoT Edge Runtime.

> [!TIP]
> Om du använder en fysisk TPM-enhet måste du bestämma **bekräftelse nyckeln**, som är unik för varje TPM-chip och hämtas från TPM-kretsen som är kopplad till den. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att till exempel skapa en SHA-256-hash för bekräftelse nyckeln.
>
> Följ anvisningarna i artikeln [Hantera enhets registreringar med Azure Portal](../iot-dps/how-to-manage-enrollments.md) för att skapa din registrering i DPS och fortsätt sedan med avsnittet [Installera IoT Edge runtime](#install-the-iot-edge-runtime) i den här artikeln för att fortsätta.

## <a name="simulate-a-tpm-device"></a>Simulera en TPM-enhet

Skapa en simulerad TPM-enhet på Windows Development-datorn. Hämta **registrerings-ID** och **bekräftelse nyckel** för enheten och Använd dem för att skapa en enskild registrerings post i DPS.

När du skapar en registrering i DPS har du möjlighet att deklarera en **första enhets dubbla tillstånd**. I enheten är det enkelt att ange taggar för att gruppera enheter efter eventuella mått som du behöver i din lösning, t. ex. region, miljö, plats eller enhets typ. De här taggarna används för att skapa [automatiska distributioner](how-to-deploy-at-scale.md).

Välj det SDK-språk som du vill använda för att skapa den simulerade enheten och följ stegen tills du har skapat den enskilda registreringen.

När du skapar enskilda registreringar väljer du **Sant** för att deklarera att den SIMULERAde TPM-enheten på Windows Development-datorn är en **IoT Edge enhet**.

> [!TIP]
> I Azure CLI kan du skapa en [registrering](/cli/azure/ext/azure-iot/iot/dps/enrollment) eller en [registrerings grupp](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) och använda den **Edge-aktiverade** flaggan för att ange att en enhet, eller en grupp av enheter, är en IoT Edge enhet.

Simulerad enhet och enskilda registrerings guider:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

När du har skapat den enskilda registreringen sparar du värdet för **registrerings-ID: t**. Du använder det här värdet när du konfigurerar IoT Edge Runtime.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Komponenterna körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod i kanten. Installera IoT Edge runtime på enheten som kör den simulerade TPM: en.

Följ stegen i [installera Azure IoT Edge runtime](how-to-install-iot-edge.md)och gå sedan tillbaka till den här artikeln för att etablera enheten.

> [!TIP]
> Behåll fönstret som kör TPM-simulatorn öppen under installationen och testningen.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurera enheten med etablerings information

När körningen har installerats på enheten konfigurerar du enheten med den information som används för att ansluta till enhets etablerings tjänsten och IoT Hub.

1. Känna till ditt DPS **-ID-omfång** och enhets **registrerings-ID** som samlades in i föregående avsnitt.

1. Öppna ett PowerShell-fönster i administratörs läge. Se till att använda en AMD64-session av PowerShell när du installerar IoT Edge, inte PowerShell (x86).

1. Kommandot **Deploy-IoTEdge** kontrollerar att Windows-datorn finns på en version som stöds, aktiverar funktionen containers och laddar sedan ned Moby runtime och IoT Edge Runtime. Kommandot använder som standard Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. I det här läget kan IoT core-enheter startas om automatiskt. Windows 10-eller Windows Server-enheter kan bli ombedd att starta om. Om så är fallet startar du om enheten nu. När enheten är klar kör du PowerShell som administratör igen.

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge runtime på din dator. Kommandot är standardvärdet för manuell etablering med Windows-behållare. Använd `-Dps` flaggan för att använda enhets etablerings tjänsten i stället för manuell etablering.

   Ersätt plats hållarnas värden för `{scope_id}` och `{registration_id}` med de data som du samlade in tidigare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om körningen har startats kan du gå till IoT Hub och börja distribuera IoT Edge moduler till enheten. Använd följande kommandon på enheten för att kontrol lera att körningen har installerats och startats.  

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Undersök tjänst loggar från de senaste 5 minuterna.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista med moduler som körs.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Med registrerings processen för enhets etablerings tjänsten kan du ange enhets-ID och enhets dubbla taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in enskilda enheter eller grupper av enheter med automatisk enhets hantering. Lär dig hur du [distribuerar och övervakar IoT Edge moduler i skala med hjälp av Azure Portal](how-to-deploy-at-scale.md) eller [med hjälp av Azure CLI](how-to-deploy-cli-at-scale.md)
