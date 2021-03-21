---
title: Skapa test certifikat – Azure IoT Edge | Microsoft Docs
description: Skapa test certifikat och lär dig hur du installerar dem på en Azure IoT Edge enhet för att förbereda för produktions distribution.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d8cf3dbe9d1dc2ad329a0b5ab8fa9554c85ae55c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199068"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Skapa democertifikat för att testa funktioner på IoT Edge-enheter

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge enheter kräver certifikat för säker kommunikation mellan körningen, modulerna och eventuella efterföljande enheter.
Om du inte har någon certifikat utfärdare för att skapa de certifikat som krävs kan du använda demonstrations certifikat för att testa IoT Edge funktioner i test miljön.
I den här artikeln beskrivs funktionerna i skript för generering av certifikat som IoT Edge tillhandahåller för testning.

Dessa certifikat upphör att gälla om 30 dagar och ska inte användas i något produktions scenario.

Du kan skapa certifikat på vilken dator som helst och sedan kopiera dem till din IoT Edge-enhet.
Det är enklare att använda den primära datorn för att skapa certifikaten istället för att skapa dem på själva IoT Edge enheten.
Genom att använda den primära datorn kan du konfigurera skripten en gång och sedan använda dem för att skapa certifikat för flera enheter.

Följ de här stegen för att skapa demonstrations certifikat för att testa IoT Edge scenariot:

1. [Konfigurera skript](#set-up-scripts) för att skapa certifikat på enheten.
2. [Skapa rot certifikat utfärdarens certifikat](#create-root-ca-certificate) som du använder för att signera alla andra certifikat för ditt scenario.
3. Generera de certifikat du behöver för scenariot du vill testa:
   * [Skapa IoT Edge enhets identitets certifikat](#create-iot-edge-device-identity-certificates) för etablering av enheter med X. 509-certifikatautentisering, antingen manuellt eller med IoT Hub Device Provisioning service.
   * [Skapa IoT Edge enhets certifikat utfärdare](#create-iot-edge-device-ca-certificates) för IoT Edge enheter i Gateway-scenarier.
   * [Skapa certifikat för underordnad enhet](#create-downstream-device-certificates) för autentisering av underordnade enheter i ett Gateway-scenario.

## <a name="prerequisites"></a>Förutsättningar

En utvecklings dator med git installerat.

## <a name="set-up-scripts"></a>Konfigurera skript

IoT Edge-lagringsplatsen på GitHub innehåller skript för generering av certifikat som du kan använda för att skapa demo certifikat.
Det här avsnittet innehåller anvisningar för hur du förbereder skript som ska köras på datorn, antingen på Windows eller Linux.
Om du använder en Linux-dator kan du gå vidare till [Konfigurera i Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Konfigurera i Windows

Om du vill skapa demo certifikat på en Windows-enhet måste du installera OpenSSL och sedan klona generationens skript och konfigurera dem för att köras lokalt i PowerShell.

#### <a name="install-openssl"></a>Installera OpenSSL

Installera OpenSSL för Windows på den dator som du använder för att generera certifikaten.
Om du redan har OpenSSL installerat på din Windows-enhet ser du till att openssl.exe är tillgänglig i miljövariabeln PATH.

Det finns flera sätt att installera OpenSSL, inklusive följande alternativ:

* **Enklare:** Hämta och installera eventuella [openssl-binärfiler från tredje part](https://wiki.openssl.org/index.php/Binaries), till exempel från [openssl på SourceForge](https://sourceforge.net/projects/openssl/). Lägg till den fullständiga sökvägen till openssl.exe i miljövariabeln PATH.

* **Rekommenderas:** Ladda ned OpenSSL-källkoden och bygg binärfilerna på din dator av dig själv eller via [vcpkg](https://github.com/Microsoft/vcpkg). Anvisningarna nedan använder vcpkg för att ladda ned källkod, kompilera och installera OpenSSL på din Windows-dator med enkla steg.

   1. Navigera till en katalog där du vill installera vcpkg. Följ anvisningarna för att ladda ned och installera [vcpkg](https://github.com/Microsoft/vcpkg).

   2. När vcpkg har installerats kör du följande kommando från en PowerShell-kommandotolk för att installera OpenSSL-paketet för Windows x64. Installationen tar vanligt vis ungefär 5 minuter att slutföra.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Lägg till `<vcpkg path>\installed\x64-windows\tools\openssl` i miljövariabeln PATH så att openssl.exe-filen är tillgänglig för anrop.

#### <a name="prepare-scripts-in-powershell"></a>Förbereda skript i PowerShell

Azure IoT Edge git-lagringsplatsen innehåller skript som du kan använda för att generera test certifikat.
I det här avsnittet ska du klona IoT Edge lagrings platsen och köra skripten.

1. Öppna ett PowerShell-fönster i administratörs läge.

2. Klona IoT Edge git-lagrings platsen, som innehåller skript för att generera demo certifikat. Använd `git clone` kommandot eller [Hämta zip](https://github.com/Azure/iotedge/archive/master.zip)-filen.

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigera till den katalog som du vill arbeta med. I den här artikeln kommer vi att anropa den här katalogen *\<WRKDIR>* . Alla certifikat och nycklar kommer att skapas i den här arbets katalogen.

4. Kopiera konfigurations-och skriptfiler från den klonade lagrings platsen till din arbets katalog.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Om du har hämtat lagrings platsen som ett ZIP-namn är mappnamnet `iotedge-master` och resten av sökvägen är samma.

5. Aktivera PowerShell för att köra skripten.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Ta de funktioner som används av skripten till PowerShell: s globala namn område.

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell-fönstret visar en varning om att de certifikat som genereras av det här skriptet endast är i test syfte och inte ska användas i produktions scenarier.

7. Kontrol lera att OpenSSL har installerats korrekt och att det inte finns namn konflikter med befintliga certifikat. Om det uppstår problem bör utdata i skriptet beskriva hur du kan åtgärda dem i systemet.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Konfigurera på Linux

Om du vill skapa demo certifikat på en Windows-enhet måste du klona generationens skript och konfigurera dem för att köras lokalt i bash.

1. Klona IoT Edge git-lagrings platsen, som innehåller skript för att generera demo certifikat.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigera till den katalog som du vill arbeta med. Vi kommer att referera till den här katalogen i hela artikeln som *\<WRKDIR>* . Alla certifikat och viktiga filer kommer att skapas i den här katalogen.
  
3. Kopiera config-och skriptfiler från den klonade IoT Edge lagrings platsen till din arbets katalog.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Skapa rot certifikat för certifikat utfärdare

Rot certifikat UTFÄRDARens certifikat används för att göra alla andra demonstrations certifikat för att testa ett IoT Edge scenario.
Du kan fortsätta att använda samma certifikat från rot certifikat utfärdaren för att fatta demonstrations certifikat för flera IoT Edge eller underordnade enheter.

Om du redan har ett certifikat från en rot certifikat utfärdare i arbetsmappen ska du inte skapa en ny.
Det nya rot certifikat utfärdarens certifikat kommer att skriva över det gamla, och eventuella underordnade certifikat som har skapats från den gamla fungerar inte längre.
Om du vill ha flera certifikat från rot certifikat utfärdare, se till att hantera dem i separata mappar.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnittet [Konfigurera skript](#set-up-scripts) för att förbereda en arbets katalog med skript för generering av demo certifikat.

### <a name="windows"></a>Windows

1. Navigera till arbets katalogen där du placerade skripten för att skapa certifikat.

1. Skapa rot certifikat utfärdarens certifikat och be det att signera ett mellanliggande certifikat. Alla certifikat placeras i din arbets katalog.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer, men när artiklar frågar efter **rot-CA-certifikatet** använder du följande fil:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navigera till arbets katalogen där du placerade skripten för att skapa certifikat.

1. Skapa rot certifikat utfärdarens certifikat och ett mellanliggande certifikat.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer, men när artiklar frågar efter **rot-CA-certifikatet** använder du följande fil:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>Skapa IoT Edge enhets identitets certifikat

Enhets identitets certifikat används för att etablera IoT Edge enheter om du väljer att använda X. 509-certifikatautentisering. Dessa certifikat fungerar oavsett om du använder manuell etablering eller automatisk etablering via Azure-IoT Hub Device Provisioning Service (DPS).

Enhets identitets certifikat finns i **etablerings** avsnittet i konfigurations filen på den IoT Edge enheten.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnitten [Konfigurera skript](#set-up-scripts) och [Skapa certifikat för rot certifikat utfärdare](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

Skapa IoT Edge enhetens identitets certifikat och privata nyckel med följande kommando:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Det namn som du skickar till det här kommandot är enhets-ID: t för den IoT Edge enheten i IoT Hub.

Kommandot ny enhets identitet skapar flera certifikat och viktiga filer, inklusive tre som du ska använda när du skapar en enskild registrering i DPS och installerar IoT Edge Runtime:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Skapa IoT Edge enhetens identitets certifikat och privata nyckel med följande kommando:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Det namn som du skickar till det här kommandot är enhets-ID: t för den IoT Edge enheten i IoT Hub.

Skriptet skapar flera certifikat och viktiga filer, inklusive tre som du ska använda när du skapar en enskild registrering i DPS och installerar IoT Edge Runtime:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>Skapa IoT Edge enhets certifikat för certifikat utfärdare

Varje IoT Edge enhet som kommer till produktion behöver ett CA-certifikatutfärdarcertifikat som refereras från konfigurations filen.
Enhetens CA-certifikat ansvarar för att skapa certifikat för moduler som körs på enheten.
Det är också nödvändigt för Gateway-scenarier, eftersom enhetens CA-certifikat är så att den IoT Edge enheten verifierar identiteten för underordnade enheter.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Enhetens CA-certifikat finns i avsnittet **certifikat** i filen config. yaml på den IoT Edge enheten.
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Enhetens CA-certifikat finns i avsnittet **Edge ca** i filen config. toml på den IoT Edge enheten.
:::moniker-end

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnitten [Konfigurera skript](#set-up-scripts) och [Skapa certifikat för rot certifikat utfärdare](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

1. Navigera till arbets katalogen som har skript för att skapa certifikat och rot certifikat utfärdare.

2. Skapa IoT Edge enhetens CA-certifikat och privata nyckel med följande kommando. Ange ett namn för CA-certifikatet.

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   Det här kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till en IoT Edge-enhet och refereras till i konfigurations filen:

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

Namnet som skickas till kommandot **New-CACertsEdgeDevice** får inte vara detsamma som parametern hostname i konfigurations filen, eller enhetens ID i IoT Hub.

### <a name="linux"></a>Linux

1. Navigera till arbets katalogen som har skript för att skapa certifikat och rot certifikat utfärdare.

2. Skapa IoT Edge enhetens CA-certifikat och privata nyckel med följande kommando. Ange ett namn för CA-certifikatet.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till en IoT Edge-enhet och refereras till i konfigurations filen:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Namnet som skickas till **create_edge_device_ca_certificate** -kommandot ska inte vara detsamma som parametern hostname i konfigurations filen, eller enhetens ID i IoT Hub.

## <a name="create-downstream-device-certificates"></a>Skapa certifikat för underordnad enhet

Om du konfigurerar en underordnad IoT-enhet för ett Gateway-scenario och vill använda X. 509-autentisering, kan du generera demo certifikat för den underordnade enheten.
Om du vill använda symmetrisk nyckel autentisering behöver du inte skapa ytterligare certifikat för den underordnade enheten.
Det finns två sätt att autentisera en IoT-enhet med X. 509-certifikat: använda självsignerade certifikat eller använda signerade certifikat från certifikat utfärdare (CA).
För X. 509-självsignerad autentisering, som ibland kallas tumavtryck-autentisering, måste du skapa nya certifikat som ska placeras på din IoT-enhet.
Dessa certifikat har ett tumavtryck i dem som du delar med IoT Hub för autentisering.
För X. 509-signerad autentisering (CA) behöver du ett rot certifikat för certifikat utfärdare som registrerats i IoT Hub som du använder för att signera certifikat för din IoT-enhet.
Alla enheter som använder ett certifikat som har utfärdats av rot certifikat utfärdarens certifikat eller något av dess mellanliggande certifikat kommer att tillåtas att autentisera.

Skripten för att skapa certifikat kan hjälpa dig att fatta demonstrations certifikat för att testa något av dessa scenarier för autentisering.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnitten [Konfigurera skript](#set-up-scripts) och [Skapa certifikat för rot certifikat utfärdare](#create-root-ca-certificate) .

### <a name="self-signed-certificates"></a>Självsignerade certifikat

När du autentiserar en IoT-enhet med självsignerade certifikat måste du skapa enhets certifikat baserat på rot certifikat utfärdarens certifikat för din lösning.
Sedan hämtar du ett hexadecimalt "finger avtryck" från certifikaten för att ange IoT Hub.
Din IoT-enhet behöver också en kopia av sina enhets certifikat så att den kan autentiseras med IoT Hub.

#### <a name="windows"></a>Windows

1. Navigera till arbets katalogen som har skript för att skapa certifikat och rot certifikat utfärdare.

2. Skapa två certifikat (primära och sekundära) för den underordnade enheten. En enkel namngivnings konvention att använda är att skapa certifikaten med namnet på IoT-enheten och sedan den primära eller sekundära etiketten. Exempel:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till den efterföljande IoT-enheten och refereras till i de program som ansluter till IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Hämta SHA1-finger avtryck (kallas ett tumavtryck i IoT Hub-kontexter) från varje certifikat. Finger avtrycket är en hexadecimal tecken sträng på 40. Använd följande openssl-kommando för att visa certifikatet och hitta finger avtrycket:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   Kör det här kommandot två gånger, en gång för det primära certifikatet och en gång för det sekundära certifikatet. Du ger finger avtryck för båda certifikaten när du registrerar en ny IoT-enhet med självsignerade X. 509-certifikat.

#### <a name="linux"></a>Linux

1. Navigera till arbets katalogen som har skript för att skapa certifikat och rot certifikat utfärdare.

2. Skapa två certifikat (primära och sekundära) för den underordnade enheten. En enkel namngivnings konvention att använda är att skapa certifikaten med namnet på IoT-enheten och sedan den primära eller sekundära etiketten. Exempel:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till den efterföljande IoT-enheten och refereras till i de program som ansluter till IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Hämta SHA1-finger avtryck (kallas ett tumavtryck i IoT Hub-kontexter) från varje certifikat. Finger avtrycket är en hexadecimal tecken sträng på 40. Använd följande openssl-kommando för att visa certifikatet och hitta finger avtrycket:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Du anger både primärt och sekundärt finger avtryck när du registrerar en ny IoT-enhet med självsignerade X. 509-certifikat.

### <a name="ca-signed-certificates"></a>CA-signerade certifikat

När du autentiserar en IoT-enhet med självsignerade certifikat måste du ladda upp rot certifikat utfärdarens certifikat för din lösning till IoT Hub.
Sedan utför du en verifiering för att bevisa att IoT Hub att du äger rot certifikat utfärdarens certifikat.
Slutligen använder du samma certifikat från rot certifikat utfärdaren för att skapa enhets certifikat som ska användas på din IoT-enhet så att det kan autentiseras med IoT Hub.

Certifikaten i det här avsnittet är för stegen i [Konfigurera X. 509-säkerhet i Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Ladda upp rot certifikat utfärdarens certifikat fil från arbets katalogen, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` till din IoT-hubb.

2. Använd koden som anges i Azure Portal för att kontrol lera att du äger det rot certifikat utfärdarens certifikat.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Skapa en certifikat kedja för din underordnade enhet. Använd samma enhets-ID som enheten har registrerats med i IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till den efterföljande IoT-enheten och refereras till i de program som ansluter till IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Ladda upp rot certifikat utfärdarens certifikat fil från arbets katalogen, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` till din IoT-hubb.

2. Använd koden som anges i Azure Portal för att kontrol lera att du äger det rot certifikat utfärdarens certifikat.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Skapa en certifikat kedja för din underordnade enhet. Använd samma enhets-ID som enheten har registrerats med i IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till den efterföljande IoT-enheten och refereras till i de program som ansluter till IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
