---
title: Etablera Windows-enheter automatiskt med DPS – Azure IoT Edge | Microsoft Docs
description: Använd en simulerad enhet på Windows-datorn för att testa automatisk enhetsetablering för Azure IoT Edge med Enhetsetableringstjänsten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bd027773a5759caee19228f56ba4b3dfe8c2cf
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482015"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Skapa och etablera en simulerad IoT Edge med en virtuell TPM i Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge enheter kan etableras automatiskt med [enhetsetableringstjänsten](../iot-dps/index.yml) precis som enheter som inte är gränsaktiverade. Om du inte är bekant med processen för automatisk etablering kan du granska [etableringsöversikten](../iot-dps/about-iot-dps.md#provisioning-process) innan du fortsätter.

DPS stöder symmetrisk nyckel attestation för IoT Edge enheter i både enskild registrering och gruppregistrering. Om du för gruppregistrering markerar att alternativet "är IoT Edge-enhet" är sant i symmetrisk nyckel atterstation markeras alla enheter som är registrerade under den registreringsgruppen som IoT Edge enheter.

Den här artikeln visar hur du testar automatisk etablering på en simulerad IoT Edge enhet med följande steg:

* Skapa en instans av IoT Hub Device Provisioning Service (DPS).
* Skapa en simulerad enhet på din Windows-dator med en simulerad Trusted Platform Module (TPM) för maskinvarusäkerhet.
* Skapa en enskild registrering för enheten.
* Installera IoT Edge och anslut enheten till IoT Hub.

> [!TIP]
> I den här artikeln beskrivs testning av automatisk etablering med hjälp av TPM-atttestering på virtuella enheter, men mycket av det gäller även när du använder fysisk TPM-maskinvara.

## <a name="prerequisites"></a>Förutsättningar

* En Windows-utvecklingsdator. Den här artikeln använder Windows 10.
* En aktiv IoT Hub.

> [!NOTE]
> TPM 2.0 krävs när du använder TPM-attestation med DPS och kan bara användas för att skapa enskilda registreringar, inte gruppregistreringar.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning Service

Skapa en ny instans av IoT Hub Device Provisioning Service Azure och länka den till din IoT-hubb. Du kan följa instruktionerna i [Konfigurera IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)

När enhetsetableringstjänsten körs kopierar du värdet för **ID-omfång** från översiktssidan. Du använder det här värdet när du konfigurerar IoT Edge körning.

> [!TIP]
> Om du använder en fysisk TPM-enhet måste du fastställa bekräftelsenyckeln **,** som är unik för varje TPM-krets och hämtas från den TPM-kretstillverkare som är associerad med den. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att till exempel skapa en SHA-256-hash för bekräftelsenyckeln.
>
> Följ anvisningarna i artikeln Så här hanterar du enhetsregistreringar med [Azure Portal](../iot-dps/how-to-manage-enrollments.md) för att skapa din registrering i DPS och fortsätt sedan med avsnittet [Installera IoT Edge-körningen](#install-the-iot-edge-runtime) i den här artikeln.

## <a name="simulate-a-tpm-device"></a>Simulera en TPM-enhet

Skapa en simulerad TPM-enhet på din Windows-utvecklingsdator. Hämta **registrerings-ID och** **bekräftelsenyckel** för din enhet och använd dem för att skapa en enskild registreringspost i DPS.

När du skapar en registrering i DPS har du möjlighet att deklarera en första **enhetstvillingtillstånd**. I enhetstvillingen kan du ange taggar för att gruppera enheter efter mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. De här taggarna används för att [skapa automatiska distributioner](how-to-deploy-at-scale.md).

Välj det SDK-språk som du vill använda för att skapa den simulerade enheten och följ stegen tills du skapar den enskilda registreringen.

När du skapar den enskilda registreringen väljer du **Sant** för att deklarera att den simulerade TPM-enheten på Windows-utvecklingsdatorn är **en IoT Edge enhet**.

> [!TIP]
> I Azure CLI kan du skapa [](/cli/azure/iot/dps/enrollment-group) en registrering eller  en registreringsgrupp och använda den kantaktiverade flaggan för att ange att en enhet, eller grupp med enheter, IoT Edge enhet. [](/cli/azure/iot/dps/enrollment)

Guider för simulerad enhet och enskild registrering:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

När du har skapat den enskilda registreringen sparar du värdet för **registrerings-ID:t**. Du använder det här värdet när du konfigurerar IoT Edge körning.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i containrar och gör att du kan distribuera ytterligare containrar till enheten så att du kan köra kod på gränsen. Installera IoT Edge på den enhet som kör den simulerade TPM:en.

Följ stegen i [Installera Azure IoT Edge runtime och](how-to-install-iot-edge.md)gå sedan tillbaka till den här artikeln för att etablera enheten.

> [!TIP]
> Låt fönstret som kör TPM-simulatorn vara öppet under installationen och testningen.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurera enheten med etableringsinformation

När körningen har installerats på enheten konfigurerar du den med den information den använder för att ansluta till enhetsetableringstjänsten och IoT Hub.

1. Ta del av ditt **DPS-ID-omfång** **och enhetsregistrerings-ID** som samlades in i föregående avsnitt.

1. Öppna ett PowerShell-fönster i administratörsläge. Se till att använda en AMD64-session med PowerShell när du IoT Edge, inte PowerShell (x86).

1. Kommandot **Deploy-IoTEdge** kontrollerar att din Windows-dator har en version som stöds, aktiverar containerfunktionen och laddar sedan ned moby-körningen och IoT Edge-körningen. Kommandot använder som standard Windows-containrar.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Nu kan IoT Core-enheter startas om automatiskt. Windows 10- eller Windows Server-enheter kan uppmana dig att starta om. I så fall startar du om enheten nu. När enheten är klar kör du PowerShell som administratör igen.

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge på datorn. Kommandot använder som standard manuell etablering med Windows-containrar. Använd flaggan `-Dps` för att använda Device Provisioning Service i stället för manuell etablering.

   Ersätt platshållarvärdena `{scope_id}` för och med de data som du samlade in `{registration_id}` tidigare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verifiera att installationen har lyckats

Om körningen har startats kan du gå in på IoT Hub och börja distribuera IoT Edge till enheten. Använd följande kommandon på enheten för att kontrollera att körningen har installerats och startats.  

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Granska tjänstloggar från de senaste 5 minuterna.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista moduler som körs.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Registreringsprocessen för enhetsetableringstjänsten låter dig ange enhets-ID och taggar för enhetstvilling samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in dig på enskilda enheter eller grupper av enheter med hjälp av automatisk enhetshantering. Lär dig hur [du distribuerar IoT Edge övervaka moduler i stor skala med hjälp Azure Portal](how-to-deploy-at-scale.md) eller med Azure [CLI](how-to-deploy-cli-at-scale.md)
