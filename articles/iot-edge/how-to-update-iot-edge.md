---
title: Uppdatera IoT Edge version på enheter – Azure IoT Edge | Microsoft Docs
description: Så här uppdaterar du IoT Edge enheter för att köra de senaste versionerna av Security daemon och IoT Edge runtime
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e5034c228a354c98b5792492d484da9eb10b8cf2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310860"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Uppdatera IoT Edge-säkerhetsdaemon och runtime

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

När IoT Edge service släpper nya versioner vill du uppdatera dina IoT Edge enheter för de senaste funktionerna och säkerhets förbättringarna. Den här artikeln innehåller information om hur du uppdaterar IoT Edge enheter när en ny version är tillgänglig.

Två komponenter i en IoT Edge-enhet måste uppdateras om du vill flytta till en nyare version. Det första är Security daemon, som körs på enheten och startar körnings moduler när enheten startar. Security daemon kan för närvarande bara uppdateras från själva enheten. Den andra komponenten är körning, som består av IoT Edge hubb och IoT Edge agent-moduler. Beroende på hur du strukturerar distributionen kan körningen uppdateras från enheten eller via fjärr anslutning.

För att hitta den senaste versionen av Azure IoT Edge, se [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Uppdatera Security daemon

IoT Edge Security daemon är en inbyggd komponent som behöver uppdateras med hjälp av paket hanteraren på den IoT Edge enheten.

Kontrol lera vilken version av säkerhets-daemon som körs på enheten med hjälp av kommandot `iotedge version` .

>[!IMPORTANT]
>Om du uppdaterar en enhet från version 1,0 eller 1,1 till version 1,2, finns det skillnader i installations-och konfigurations processerna som kräver extra steg. Mer information finns i stegen längre fram i den här artikeln: [specialfall: uppdatera från 1,0 eller 1,1 till 1,2](#special-case-update-from-10-or-11-to-12).

# <a name="linux"></a>[Linux](#tab/linux)

På linux x64-enheter använder du apt-get eller lämplig Package Manager för att uppdatera säkerhets daemonen till den senaste versionen.

Hämta den senaste databas konfigurationen från Microsoft:

* **Ubuntu Server 18,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi OS-storlek**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Kopiera den genererade listan.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installera Microsoft GPG offentlig nyckel.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Uppdatera apt.

   ```bash
   sudo apt-get update
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Kontrol lera vilka versioner av IoT Edge som är tillgängliga.

   ```bash
   apt list -a iotedge
   ```

Om du vill uppdatera till den senaste versionen av Security daemon använder du följande kommando som också uppdaterar **libiothsm-STD** till den senaste versionen:

   ```bash
   sudo apt-get install iotedge
   ```

Om du vill uppdatera till en speciell version av Security daemon, anger du versionen från apt lista utdata. När **iotedge** uppdateras försöker den automatiskt uppdatera **libiothsm-STD-** paketet till den senaste versionen, vilket kan orsaka en beroende konflikt. Om du inte kommer till den senaste versionen, måste du ange båda paketen för samma version. Följande kommando installerar till exempel en version av 1,1-versionen:

   ```bash
   sudo apt-get install iotedge=1.1.1 libiothsm-std=1.1.1
   ```

Om den version som du vill installera inte är tillgänglig via apt-get kan du använda spiral för att rikta in dig på alla versioner från lagrings platsen för [IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) . För den version som du vill installera letar du reda på lämpliga **libiothsm-STD-** och **iotedge** -filer för enheten. För varje fil högerklickar du på fil länken och kopierar länk adressen. Använd länk adressen för att installera de olika versionerna av dessa komponenter:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
```
<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Kontrol lera vilka versioner av IoT Edge som är tillgängliga.

   ```bash
   apt list -a aziot-edge
   ```

Om du vill uppdatera till den senaste versionen av IoT Edge använder du följande kommando som också uppdaterar identitets tjänsten till den senaste versionen:

   ```bash
   sudo apt-get install aziot-edge
   ```
<!-- end 1.2 -->
:::moniker-end

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Med IoT Edge för Linux i Windows IoT Edge körs på en virtuell Linux-dator som finns på en Windows-enhet. Den här virtuella datorn är förinstallerad med IoT Edge och hanteras med Microsoft Update för att hålla komponenterna aktuella. Om du har aktiverat automatiska uppdateringar kommer nya uppdateringar att laddas ned och installeras när de är tillgängliga.

IoT Edge körs direkt på Windows-enheten med IoT Edge för Windows. Instruktioner för uppdatering med hjälp av PowerShell-skript finns i [Installera och hantera Azure IoT Edge för Windows](how-to-install-iot-edge-windows-on-windows.md).
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

För närvarande finns inte stöd för IoT Edge version 1,2 som körs på Windows-enheter.

:::moniker-end

---

## <a name="update-the-runtime-containers"></a>Uppdatera runtime-behållare

Hur du uppdaterar IoT Edge agenten och IoT Edge Hub-behållare beror på om du använder rullande Taggar (t. ex. 1,1) eller vissa taggar (som 1.1.1) i distributionen.

Kontrol lera versionen av IoT Edge agent och IoT Edge Hub-moduler som finns på enheten med hjälp av kommandona `iotedge logs edgeAgent` eller `iotedge logs edgeHub` .

  ![Hitta behållar version i loggar](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Förstå IoT Edge Taggar

IoT Edge-agenten och IoT Edge Hub-avbildningarna är taggade med den IoT Edge-version som de är associerade med. Det finns två olika sätt att använda taggar med körnings avbildningarna:

* **Rullande Taggar** – Använd bara de första två värdena i versions numret för att hämta den senaste bilden som matchar de siffrorna. 1,1 uppdateras till exempel när det finns en ny version som pekar på den senaste 1.1. x-versionen. Om behållar körningen på din IoT Edge enhet hämtar avbildningen igen, uppdateras runtime-modulerna till den senaste versionen. Distributioner från Azure Portal standardvärde till rullande taggar. *Den här metoden föreslås i utvecklings syfte.*

* **Vissa Taggar** -Använd alla tre värdena i versions numret för att uttryckligen ange avbildnings versionen. 1.1.0 ändras till exempel inte efter den första versionen. Du kan deklarera ett nytt versions nummer i distributions manifestet när du är redo att uppdatera. *Den här metoden rekommenderas för produktions syfte.*

### <a name="update-a-rolling-tag-image"></a>Uppdatera en rullande tag-bild

Om du använder rullande Taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1,1**) måste du tvinga behållar körning på enheten att hämta den senaste versionen av avbildningen.

Ta bort den lokala versionen av avbildningen från din IoT Edge-enhet. När du avinstallerar Security daemon på Windows-datorer tar du även bort körnings avbildningarna, så du behöver inte göra det här steget igen.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.1
docker rmi mcr.microsoft.com/azureiotedge-agent:1.1
```

Du kan behöva använda Force- `-f` flaggan för att ta bort avbildningarna.

IoT Edge tjänsten hämtar de senaste versionerna av körnings avbildningarna och startar dem automatiskt på enheten igen.

### <a name="update-a-specific-tag-image"></a>Uppdatera en speciell tag-bild

Om du använder vissa taggar i din distribution (t. ex. mcr.microsoft.com/azureiotedge-hub:**1.1.1**) måste du uppdatera taggen i distributions manifestet och tillämpa ändringarna på enheten.

1. I IoT Hub i Azure Portal väljer du din IoT Edge enhet och väljer **Ange moduler**.

1. I avsnittet **IoT Edge moduler** väljer du **körnings inställningar**.

   ![Konfigurera körnings inställningar](./media/how-to-update-iot-edge/configure-runtime.png)

1. Uppdatera **avbildning** svärdet för **Edge Hub** med önskad version i **körnings inställningarna**. Välj inte **Spara** ännu.

   ![Uppdatera bild version för Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Minimera inställningarna för **Edge Hub** eller rulla nedåt och uppdatera **avbildning** svärdet för **Edge-agenten** med samma önskade version.

   ![Uppdatera agent version för Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Välj **Spara**.

1. Välj **Granska + skapa**, granska distributionen och välj **skapa**.

## <a name="special-case-update-from-10-or-11-to-12"></a>Specialfall: uppdatera från 1,0 eller 1,1 till 1,2

Från och med version 1,2 använder tjänsten IoT Edge ett nytt paket namn och har vissa skillnader i installations-och konfigurations processerna. Om du har en IoT Edge-enhet som kör version 1,0 eller 1,1 kan du använda dessa instruktioner för att lära dig hur du uppdaterar till 1,2.

>[!NOTE]
>För närvarande finns det inget stöd för IoT Edge version 1,2 som körs på Windows-enheter.

Några av de viktigaste skillnaderna mellan 1,2 och tidigare versioner är:

* Paket namnet ändrades från **iotedge** till **aziot**.
* **Libiothsm-STD-** paketet används inte längre. Om du använde standard paketet som ingår i IoT Edges versionen kan dina konfigurationer överföras till den nya versionen. Om du använde en annan implementering av libiothsm-STD, måste alla certifikat från användare som till exempel enhets identitets certifikat, enhets certifikat utfärdare och förtroende paket konfigureras om.
* En ny identitets tjänst, **aziot-Identity-service** , introducerades som en del av 1,2-versionen. Den här tjänsten hanterar identitets etableringen och-hanteringen för IoT Edge och för andra enhets komponenter som måste kommunicera med IoT Hub, till exempel [enhets uppdatering för IoT Hub](../iot-hub-device-update/understand-device-update.md).
* Standard konfigurations filen har ett nytt namn och en ny plats. Tidigare `/etc/iotedge/config.yaml` förväntas din enhets konfigurations information vara i `/etc/aziot/config.toml` som standard. `iotedge config import`Kommandot kan användas för att migrera konfigurations information från den gamla platsen och syntaxen till den nya.
  * Import kommandot kan inte identifiera eller ändra åtkomst regler för en enhets TPM (Trusted Platform Module). Om enheten använder TPM-attestering måste du manuellt uppdatera/etc/udev/rules.d/tpmaccess.rules-filen för att ge åtkomst till aziottpm-tjänsten. Mer information finns i [ge IoT Edge åtkomst till TPM:](how-to-auto-provision-simulated-device-linux.md?view=iotedge-2020-11&preserve-view=true#give-iot-edge-access-to-the-tpm)en.
* Arbets belastnings-API: n i version 1,2 sparar krypterade hemligheter i ett nytt format. Om du uppgraderar från en äldre version till version 1,2 importeras den befintliga huvud krypterings nyckeln. Arbets belastnings-API: et kan läsa hemligheter som sparats i det tidigare formatet med den importerade krypterings nyckeln. Men arbets belastnings-API: et kan inte skriva krypterade hemligheter i det gamla formatet. När en hemlighet krypteras igen av en modul, sparas den i det nya formatet. Hemligheter som krypteras i version 1,2 kan inte läsas av samma modul i version 1,1. Om du behåller krypterade data till en värdbaserad mapp eller volym ska du alltid skapa en säkerhets kopia av data *innan* du uppgraderar för att ge möjlighet att nedgradera vid behov.

Innan du automatiserar uppdaterings processerna kontrollerar du att den fungerar på test datorer.

När du är klar kan du uppdatera IoT Edge på dina enheter genom att följa de här stegen:

1. Hämta den senaste databas konfigurationen från Microsoft:

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS-storlek**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Kopiera den genererade listan.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Installera Microsoft GPG offentlig nyckel.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

4. Uppdatera apt.

   ```bash
   sudo apt-get update
   ```

5. Avinstallera den tidigare versionen av IoT Edge och lämna konfigurationsfilerna på plats.

   ```bash
   sudo apt-get remove iotedge
   ```

6. Installera den senaste versionen av IoT Edge, tillsammans med IoT Identity service.

   ```bash
   sudo apt-get install aziot-edge
   ```

7. Importera din gamla config. yaml-fil till det nya formatet och Använd konfigurations informationen.

   ```bash
   sudo iotedge config import
   ```

Nu när IoT Edge tjänsten som körs på dina enheter har uppdaterats följer du stegen i den här artikeln för att [Uppdatera runtime-behållare](#update-the-runtime-containers).

## <a name="special-case-update-to-a-release-candidate-version"></a>Specialfall: uppdatera till en version av Release Candidate

Azure IoT Edge regelbundet frigör nya versioner av tjänsten IoT Edge. Innan varje stabil utgåva finns det en eller flera versioner av Release Candidate (RC). RC-versioner innehåller alla planerade funktioner för versionen, men fortsätter att testa och verifiera. Om du vill testa en ny funktion tidigt kan du installera en RC-version och ge feedback via GitHub.

Release Candidate-versioner följer samma siffer konvention, men har **-RC** plus ett stegvist nummer som läggs till i slutet. Du kan se versions kandidaterna i samma lista med [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases) som stabila versioner. Du kan till exempel hitta **1.2.0-RC4**, en av de release-kandidater som publicerats före **1.2.0**. Du kan också se att RC-versionerna är markerade med **för hands versions** etiketter.

IoT Edge agent-och hubb-moduler har RC-versioner som är taggade med samma konvention. Till exempel **MCR.Microsoft.com/azureiotedge-Hub:1.2.0-RC4**.

Som för hands versioner ingår inte versions kandidat versioner som den senaste versionen som används som mål för vanliga installationer. I stället måste du manuellt rikta in till gångarna för RC-versionen som du vill testa. För det mesta är att installera eller uppdatera till en RC-version som riktar sig mot en annan speciell version av IoT Edge.

Använd avsnitten i den här artikeln för att lära dig hur du uppdaterar en IoT Edge-enhet till en viss version av daemon-eller körnings moduler.

Om du installerar IoT Edge, i stället för att uppgradera en befintlig installation, använder du stegen i [offline eller en speciell versions installation](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

## <a name="next-steps"></a>Nästa steg

Visa de senaste [Azure IoT Edge versionerna](https://github.com/Azure/azure-iotedge/releases).

Håll dig uppdaterad med de senaste uppdateringarna och meddelandena i [Sakernas Internet blogg](https://azure.microsoft.com/blog/topics/internet-of-things/)
