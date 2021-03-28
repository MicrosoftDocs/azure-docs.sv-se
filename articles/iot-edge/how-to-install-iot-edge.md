---
title: Installera Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge Installationsinstruktioner på Windows-eller Linux-enheter
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: kgremban
ms.openlocfilehash: a98eed61904b580988fe34302999f3ec6a24ac9e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640915"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Installera eller avinstallera Azure IoT Edge för Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge runtime är vad som förvandlar en enhet till en IoT Edge enhet. Körningen kan distribueras på enheter så små som Raspberry Pi eller lika stora som en industriell Server. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogiken till den från molnet. Mer information finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Den här artikeln beskriver stegen för att installera Azure IoT Edge runtime på Linux-enheter.

## <a name="prerequisites"></a>Förutsättningar

* Ett [registrerat enhets-ID](how-to-register-device.md)

  Om du har registrerat enheten med symmetrisk nyckel autentisering, så är enhets anslutnings strängen klar.

  Om du har registrerat din enhet med 509-autentisering för självsignerade certifikat i X måste du ha minst ett av de identitets certifikat som du använde för att registrera enheten och dess matchande privata nyckel som är tillgänglig på enheten.

* En Linux-enhet

  Ha en x64-, ARM32-eller ARM64 Linux-enhet. Microsoft tillhandahåller installations paket för Ubuntu Server 18,04 och Raspberry Pi OS-omskalande operativ system.

  Den senaste informationen om vilka operativ system som för närvarande stöds för produktions scenarier finns i [Azure IoT Edge system som stöds](support.md#operating-systems)

  >[!NOTE]
  >Stöd för ARM64-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Förbered din enhet för att få åtkomst till Microsoft-installations paketen.

  Installera den lagrings plats konfiguration som matchar enhetens operativ system.

  * **Ubuntu Server 18,04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS-storlek**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Kopiera den genererade listan till katalogen sources. list. d.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Installera den offentliga nyckeln för Microsoft GPG.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge program varu paket omfattas av licens villkoren som finns i varje paket ( `usr/share/doc/{package-name}` eller i `LICENSE` katalogen). Läs licens villkoren innan du använder ett paket. Din installation och användning av ett paket utgör ditt godkännande av dessa villkor. Om du inte godkänner licens villkoren ska du inte använda det paketet.

## <a name="install-a-container-engine"></a>Installera en behållar motor

Azure IoT Edge förlitar sig på en OCI-kompatibel container Runtime. För produktions scenarier rekommenderar vi att du använder Moby-motorn. Moby-motorn är den enda behållar motorn som stöds officiellt med Azure IoT Edge. Docker CE/EE-behållar avbildningar är kompatibla med Moby-körningsmiljön.

Uppdatera paket listor på enheten.

   ```bash
   sudo apt-get update
   ```

Installera Moby-motorn.

   ```bash
   sudo apt-get install moby-engine
   ```

Om du får fel när du installerar Moby container Engine, verifiera din Linux-kernel för kompatibilitet med Moby. Vissa inbäddade enhets tillverkare levererar enhets avbildningar som innehåller anpassade Linux-Kernels utan de funktioner som krävs för container Engine-kompatibilitet. Kör följande kommando, som använder skriptet för att [kontrol](https://github.com/moby/moby/blob/master/contrib/check-config.sh) lera konfigurationen som tillhandahålls av Moby, för att kontrol lera kernel-konfigurationen:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

I utdata från skriptet kontrollerar du att alla objekt under `Generally Necessary` och `Network Drivers` är aktiverade. Om du saknar funktioner kan du aktivera dem genom att återskapa din kernel från källa och välja de associerade modulerna som ska ingå i den aktuella kernel. config. På samma sätt kan du, om du använder en kernel-konfigurations generator som `defconfig` eller `menuconfig` , hitta och aktivera respektive funktioner och återskapa din kernel enligt detta. När du har distribuerat den nyligen ändrade kärnan kör du check config-skriptet igen för att kontrol lera att alla nödvändiga funktioner har Aktiver ATS.

## <a name="install-iot-edge"></a>Installera IoT Edge

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge Security daemon tillhandahåller och upprätthåller säkerhets standarder på den IoT Edge enheten. Daemonen startar vid varje start och startar enheten genom att starta resten av IoT Edge Runtime.

Stegen i det här avsnittet representerar den typiska processen för att installera den senaste versionen på en enhet som har Internet anslutning. Om du behöver installera en speciell version, t. ex. en för hands version, eller om du behöver installera offline, följer du anvisningarna för att installera [offline eller vissa versioner](#offline-or-specific-version-installation-optional) senare i den här artikeln.

Uppdatera paket listor på enheten.

   ```bash
   sudo apt-get update
   ```

Kontrol lera vilka versioner av IoT Edge som är tillgängliga.

   ```bash
   apt list -a iotedge
   ```

Om du vill installera den senaste versionen av Security daemon använder du följande kommando som också installerar den senaste versionen av **libiothsm-STD-** paketet:

   ```bash
   sudo apt-get install iotedge
   ```

Eller, om du vill installera en speciell version av säkerhets-daemonen, anger du versionen från apt lista utdata. Ange också samma version för **libiothsm-STD-** paketet, vilket annars skulle installera den senaste versionen. Följande kommando installerar till exempel den senaste versionen av 1.0.10-versionen:

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

Om den version som du vill installera inte finns med i listan följer du stegen för [offline-eller detaljerad versions installation](#offline-or-specific-version-installation-optional) senare i den här artikeln. I avsnittet visas hur du riktar in dig på en tidigare version av IoT Edge Security daemon eller Release Candidate-versioner.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Tjänsten IoT Edge tillhandahåller och underhåller säkerhets standarder på den IoT Edge enheten. Tjänsten startar vid varje start och startar enheten genom att starta resten av IoT Edge Runtime.

IoT Identity service introducerades tillsammans med version 1,2 av IoT Edge. Den här tjänsten hanterar identitets etableringen och-hanteringen för IoT Edge och för andra enhets komponenter som måste kommunicera med IoT Hub.

Stegen i det här avsnittet representerar den typiska processen för att installera den senaste versionen på en enhet som har Internet anslutning. Om du behöver installera en speciell version, t. ex. en för hands version, eller om du behöver installera offline, följer du anvisningarna för att installera [offline eller vissa versioner](#offline-or-specific-version-installation-optional) senare i den här artikeln.

>[!NOTE]
>Stegen i det här avsnittet visar hur du installerar IoT Edge version 1,2, som för närvarande finns i en offentlig för hands version. Om du letar efter stegen för att installera den senaste allmänt tillgängliga versionen av IoT Edge kan du läsa 1,1-versionen [(LTS)](?view=iotedge-2018-06&preserve-view=true) av den här artikeln.
>
>Om du redan har en IoT Edge enhet som kör en äldre version och vill uppgradera till 1,2 kan du använda stegen i [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md). Version 1,2 är tillräckligt mycket annorlunda än tidigare versioner av IoT Edge att vissa steg är nödvändiga för att uppgradera.

Uppdatera paket listor på enheten.

   ```bash
   sudo apt-get update
   ```

Kontrol lera vilka versioner av IoT Edge som är tillgängliga.

   ```bash
   apt list -a aziot-edge
   ```

Om du vill installera den senaste versionen av IoT Edge använder du följande kommando som också installerar den senaste versionen av identitets tjänst paketet:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- commenting out for public preview. reintroduce at GA

Or, if you want to install a specific version of IoT Edge and the identity service, specify the versions from the apt list output. Specify the same versions for both services.. For example, the following command installs the most recent version of the 1.2 release:

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

-->

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Etablera enheten med dess moln identitet

Nu när behållar motorn och IoT Edge runtime är installerade på enheten är du redo för nästa steg, vilket är att konfigurera enheten med dess moln identitet och autentiseringsinformation.

Välj nästa avsnitt baserat på vilken autentiseringstyp du vill använda:

* [Alternativ 1: autentisera med symmetriska nycklar](#option-1-authenticate-with-symmetric-keys)
* [Alternativ 2: autentisera med X. 509-certifikat](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Alternativ 1: autentisera med symmetriska nycklar

I det här läget installeras IoT Edge runtime på din Linux-enhet och du måste etablera enheten med dess moln identitet och autentiseringsinformation.

Det här avsnittet går igenom stegen för att etablera en enhet med symmetrisk nyckel autentisering. Du bör ha registrerat enheten i IoT Hub och hämtat anslutnings strängen från enhets informationen. Annars följer du stegen i [Registrera en IoT Edge enhet i IoT Hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Öppna konfigurations filen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Hitta etablerings konfigurationerna för filen och ta bort kommentaren till den **manuella etablerings konfigurationen med hjälp av en anslutnings sträng** , om den inte redan är kommenterad.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Uppdatera värdet för **device_connection_string** med anslutnings strängen från IoT Edge enheten. Se till att alla andra etablerings avsnitt är kommenterade. Se till att **etableringen:** raden inte har några föregående blank steg och att kapslade objekt är indragna med två blank steg.

Klistra in innehållet i Urklipp i nano `Shift+Right Click` eller tryck på `Shift+Insert` .

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

När du har angett etablerings informationen i konfigurations filen startar du om daemonen:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Skapa konfigurations filen för enheten baserat på en mallfil som tillhandahålls som en del av IoT Edge installationen.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Öppna konfigurations filen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Hitta **etablerings** avsnittet i filen och ta bort kommentaren mellan manuell etablering med anslutnings sträng rader.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Uppdatera värdet för **connection_string** med anslutnings strängen från IoT Edge enheten.

Klistra in innehållet i Urklipp i nano `Shift+Right Click` eller tryck på `Shift+Insert` .

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

När du har angett etablerings informationen i konfigurations filen ska du tillämpa ändringarna:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>Alternativ 2: autentisera med X. 509-certifikat

I det här läget installeras IoT Edge runtime på din Linux-enhet och du måste etablera enheten med dess moln identitet och autentiseringsinformation.

Det här avsnittet går igenom stegen för att etablera en enhet med X. 509 certifikatautentisering. Du bör ha registrerat din enhet i IoT Hub och tillhandahålla tumavtrycken som matchar certifikatet och den privata nyckeln som finns på din IoT Edge-enhet. Annars följer du stegen i [Registrera en IoT Edge enhet i IoT Hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Öppna konfigurations filen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Hitta konfigurations avsnittet för etablering i filen och ta bort kommentarer till den **manuella etablerings konfigurationen med hjälp av ett X. 509-identitets certifikat** . Se till att alla andra etablerings avsnitt är kommenterade. Se till att **etableringen:** raden inte har några föregående blank steg och att kapslade objekt är indragna med två blank steg.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Uppdatera följande fält:

* **iothub_hostname**: värd namnet för den IoT-hubb som enheten ska ansluta till. Till exempel `{IoT hub name}.azure-devices.net`.
* **device_id**: det ID som du angav när du registrerade enheten.
* **identity_cert**: URI till ett identitets certifikat på enheten. Till exempel `file:///path/identity_certificate.pem`.
* **identity_pk**: URI till den privata nyckel filen för det tillhandahållna identitets certifikatet. Till exempel `file:///path/identity_key.pem`.

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

När du har angett etablerings informationen i konfigurations filen startar du om daemonen:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Skapa konfigurations filen för enheten baserat på en mallfil som tillhandahålls som en del av IoT Edge installationen.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Öppna konfigurations filen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Hitta **etablerings** avsnittet i filen och ta bort kommentarer till raderna för manuell etablering med X. 509-identitets certifikat. Se till att alla andra etablerings avsnitt är kommenterade.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Uppdatera följande fält:

* **iothub_hostname**: värd namnet för den IoT-hubb som enheten ska ansluta till. Till exempel `{IoT hub name}.azure-devices.net`.
* **device_id**: det ID som du angav när du registrerade enheten.
* **identity_cert**: URI till ett identitets certifikat på enheten, till exempel: `file:///path/identity_certificate.pem` . Eller, utfärda ett dynamiskt certifikat med hjälp av EST eller en lokal certifikat utfärdare.
* **identity_pk**: URI till den privata nyckel filen för det tillhandahållna identitets certifikatet, till exempel: `file:///path/identity_key.pem` . Eller ange en PKCS # 11-URI och ange sedan konfigurations informationen i avsnittet **PKCS # 11** senare i konfigurations filen.

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

När du har angett etablerings informationen i konfigurations filen ska du tillämpa ändringarna:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Verifiera lyckad konfiguration

Verifiera att körningen har installerats och konfigurerats korrekt på din IoT Edge-enhet.

>[!TIP]
>Förhöjd behörighet krävs för att köra `iotedge`-kommandon. När du loggar ut från datorn och sedan loggar in igen för första gången efter installationen av IoT Edge-körningen, så uppdateras dina behörigheter automatiskt. Tills dess kan du använda framför `sudo` kommandona.

Kontrol lera att den IoT Edge system tjänsten körs.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

Ett lyckat status svar är `Ok` .

::: moniker-end

Hämta tjänstloggar om du behöver felsöka tjänsten.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Använd `check` verktyget för att verifiera enhetens konfiguration och anslutnings status.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Använd alltid `sudo` för att köra kontroll verktyget, även efter att dina behörigheter har uppdaterats. Verktyget behöver utökade privilegier för att få åtkomst till konfigurations filen för att verifiera konfigurations status.

Visa alla moduler som körs på din IoT Edge-enhet. När tjänsten startar för första gången bör du bara se **edgeAgent** -modulen som körs. EdgeAgent-modulen körs som standard och hjälper till att installera och starta ytterligare moduler som du distribuerar till din enhet.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Offline eller en speciell versions installation (valfritt)

Stegen i det här avsnittet gäller scenarier som inte omfattas av standard installations stegen. Detta kan vara:

* Installera IoT Edge offline
* Installera en version av Release Candidate

Använd stegen i det här avsnittet om du vill installera en viss version av Azure IoT Edge runtime som inte är tillgänglig via `apt-get install` . Listan med Microsoft-paket innehåller bara en begränsad uppsättning av de senaste versionerna och deras under versioner, så dessa steg är för alla som vill installera en äldre version eller en version av en version av versionen.

Med hjälp av ett spiral kommando kan du rikta in komponentfilerna direkt från IoT Edge GitHub-lagringsplatsen.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Gå till [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases)och leta upp den version som du vill använda som mål.

2. Expandera avsnittet **till gångar** för den versionen.

3. Varje utgåva bör ha nya filer för IoT Edge Security daemon och hsmlib. Om du ska installera IoT Edge på en enhet som är offline kan du hämta filerna i förväg. Annars kan du använda följande kommandon för att uppdatera dessa komponenter.

   1. Hitta filen **libiothsm-STD** som matchar IoT Edge enhetens arkitektur. Högerklicka på fil länken och Kopiera länk adressen.

   2. Använd den kopierade länken i följande kommando för att installera den versionen av hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Hitta den **iotedge** -fil som matchar din IoT Edge enhets arkitektur. Högerklicka på fil länken och Kopiera länk adressen.

   4. Använd den kopierade länken i följande kommando för att installera den versionen av IoT Edge Security daemon.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Om enheten för närvarande kör IoT Edge version 1,1 eller äldre avinstallerar du **iotedge** **-och libiothsm-STD-** paketen innan du följer stegen i det här avsnittet. Mer information finns i [Uppdatera från 1,0 eller 1,1 till 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Gå till [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases)och leta upp den version som du vill använda som mål.

2. Expandera avsnittet **till gångar** för den versionen.

3. Varje utgåva bör ha nya filer för IoT Edge och identitets tjänsten. Om du ska installera IoT Edge på en enhet som är offline kan du hämta filerna i förväg. Annars kan du använda följande kommandon för att uppdatera dessa komponenter.

   1. Hitta den **aziot-Identity-service** -fil som matchar din IoT Edge enhets arkitektur. Högerklicka på fil länken och Kopiera länk adressen.

   2. Använd den kopierade länken i följande kommando för att installera den här versionen av identitets tjänsten:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. Hitta den **aziot-** fil som matchar din IoT Edge enhets arkitektur. Högerklicka på fil länken och Kopiera länk adressen.

   4. Använd den kopierade länken i följande kommando för att installera den versionen av IoT Edge.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Nu när behållar motorn och IoT Edge runtime är installerade på enheten är du redo för nästa steg, vilket är att [etablera enheten med dess moln identitet](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge installationen från enheten använder du följande kommandon.

Ta bort IoT Edge-körningen.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Använd `--purge` -flaggan om du vill ta bort alla filer som är associerade med IoT Edge, inklusive konfigurationsfilerna. Lämna ut den här flaggan om du vill installera om IoT Edge och använda samma konfigurations information i framtiden.

När IoT Edge runtime tas bort, stoppas alla behållare som den skapade, men de finns kvar på enheten. Visa alla behållare för att se vilka som är kvar.

```bash
sudo docker ps -a
```

Ta bort behållarna från enheten, inklusive de två körnings behållarna.

```bash
sudo docker rm -f <container name>
```

Slutligen tar du bort behållar körningen från enheten.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Nästa steg

Fortsätt att [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md) och lär dig hur du distribuerar moduler till din enhet.
