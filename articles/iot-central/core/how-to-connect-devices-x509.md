---
title: Ansluta enheter med X. 509-certifikat i ett Azure IoT Central-program
description: Så här ansluter du enheter med X. 509-certifikat med hjälp av Node.js Device SDK för IoT Central program
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: cf0db71600c9350b4d70e6375f509a6e88709f70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100378340"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Så här ansluter du enheter med X. 509-certifikat med hjälp av Node.js Device SDK för IoT Central program

IoT Central stöder de båda certifikaten för signaturer för delad åtkomst (SAS) och X. 509 för att skydda kommunikationen mellan en enhet och ditt program. Självstudien [skapa och Anslut ett klient program till Azure IoT Central-programmet](./tutorial-connect-device.md) använder SAS. I den här artikeln får du lära dig hur du ändrar kod exemplet för att använda X. 509.  X. 509-certifikat rekommenderas i produktions miljöer. Mer information finns i [Anslut till Azure IoT Central](./concepts-get-connected.md).

Den här artikeln visar två sätt att använda X. 509 – [grupp registreringar](how-to-connect-devices-x509.md#use-a-group-enrollment) som vanligt vis används i en produktions miljö och [enskilda registreringar](how-to-connect-devices-x509.md#use-an-individual-enrollment) är användbara för testning.

Kodfragmenten i den här artikeln använder Java Script. Kod exempel på andra språk finns i:

- [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_ll_client_x509_sample)
- [C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/X509DeviceCertWithChainSample)
- [Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
- [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)

## <a name="prerequisites"></a>Förutsättningar

- Slut för ande av guiden [skapa och Anslut ett klient program till din Azure IoT Central program (Java Script)](./tutorial-connect-device.md) .
- [Git](https://git-scm.com/download/).
- Hämta och installera [openssl](https://www.openssl.org/). Om du använder Windows kan du använda binärfilerna från [openssl-sidan på SourceForge](https://sourceforge.net/projects/openssl/).

## <a name="use-a-group-enrollment"></a>Använd en grupp registrering

Använd X. 509-certifikat med en grupp registrering i en produktions miljö. I en grupp registrering lägger du till ett rot-eller mellanliggande X. 509-certifikat till ditt IoT Central-program. Enheter med löv certifikat härledda från rot-eller mellanliggande certifikat kan ansluta till ditt program.

## <a name="generate-root-and-device-cert"></a>Generera rot-och enhets certifikat

I det här avsnittet använder du ett X. 509-certifikat för att ansluta en enhet med ett certifikat som härletts från registrerings gruppens certifikat, som kan ansluta till ditt IoT Central-program.

> [!WARNING]
> Det här sättet att generera X. 509-certifikat är endast för testning. För en produktions miljö bör du använda den officiella, säkra mekanismen för att skapa certifikat.

1. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för skript för generering av certifikat:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Navigera till skriptet för certifikat generatorn och installera de nödvändiga paketen:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Skapa ett rot certifikat och Härled sedan ett enhets certifikat genom att köra skriptet:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Ett enhets-ID kan innehålla bokstäver, siffror och `-` tecken.

Dessa kommandon producerar tre filer för roten och enhets certifikatet

filename | innehåller
-------- | --------
\<name\>_cert. pem | Den offentliga delen av X509-certifikatet
\<name\>_key. pem | Den privata nyckeln för X509-certifikatet
\<name\>_fullchain. pem | Hela nyckel ringen för X509-certifikatet.

## <a name="create-a-group-enrollment"></a>Skapa en grupp registrering

1. Öppna ditt IoT Central program och gå till **Administration**  i det vänstra fönstret och välj **enhets anslutning**.

1. Välj **+ skapa registrerings grupp** och skapa en ny registrerings grupp med namnet _MyX509Group_ med en attesterings typ för **certifikat (X. 509)**.

1. Öppna den registrerings grupp som du har skapat och välj **Hantera primär**.

1. Välj fil alternativ och ladda upp rot certifikat filen som heter _mytestrootcert_cert. pem_ som du skapade tidigare:

    ![Certifikat uppladdning](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Slutför verifieringen genom att generera verifierings koden, kopiera den och sedan använda den för att skapa ett X. 509-verifierings certifikat i kommando tolken:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Ladda upp det signerade verifierings certifikatet _verification_cert. pem_ för att slutföra verifieringen:

    ![Verifierat certifikat](./media/how-to-connect-devices-x509/verified.png)

Nu kan du ansluta enheter som har ett X. 509-certifikat som härletts från det här primära rot certifikatet.

När du har sparat registrerings gruppen ska du anteckna ID-omfånget.

## <a name="run-sample-device-code"></a>Kör exempel på enhets kod

1. Kopiera filen **sampleDevice01_key. pem** och **sampleDevice01_cert. pem** till mappen _Azure-IoT-SDK-Node/Device/samples/PnP_ som innehåller **simple_thermostat.js** programmet. Du använde det här programmet när du har slutfört [guiden Anslut en enhet (Java Script)](./tutorial-connect-device.md).

1. Gå till mappen _Azure-IoT-SDK-Node/Device/samples/PnP_ som innehåller **simple_thermostat.js** programmet och kör följande kommando för att installera X. 509-paketet:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Öppna **simple_thermostat.js** -filen i en text redigerare.

1. Redigera `require` instruktionerna för att inkludera följande:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Lägg till följande fyra rader i avsnittet "information om DPS-anslutning" för att initiera `deviceCert` variabeln:

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Redigera `provisionDevice` funktionen som skapar klienten genom att ersätta den första raden med följande:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. I samma funktion ändrar du raden som anger `deviceConnectionString` variabeln enligt följande:

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. I `main` funktionen lägger du till följande rad efter raden som anropar `Client.fromConnectionString` :

    ```javascript
    client.setOptions(deviceCert);
    ```

1. I din gränssnitts miljö anger du följande två miljövariabler:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > Du ställer in de andra nödvändiga miljövariablerna när du har slutfört guiden [skapa och ansluta ett klient program till Azure IoT Central Application](./tutorial-connect-device.md) .

1. Kör skriptet och kontrol lera att enheten har allokerats:

    ```cmd/sh
    node simple_thermostat.js
    ```

    Du kan också kontrol lera att telemetri visas på instrument panelen.

    ![Verifiera telemetri för enhet](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Använd en enskild registrering

Använd X. 509-certifikat med en enskild registrering för att testa enheten och lösningen. I en enskild registrering finns inget rot-eller mellanliggande X. 509-certifikat i ditt IoT Central-program. Enheter använder ett självsignerat X. 509-certifikat för att ansluta till ditt program.

## <a name="generate-self-signed-device-cert"></a>Generera ett självsignerat enhets certifikat

I det här avsnittet använder du ett självsignerat X. 509-certifikat för att ansluta enheter för enskild registrering, som används för att registrera en enskild enhet. Självsignerade certifikat är endast för testning.

Skapa ett självsignerat X. 509-enhets certifikat genom att köra skriptet. Se till att endast använda gemener alfanumeriska tecken och bindestreck för certifikat namn:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Skapa enskild registrering

1. I Azure IoT Central-programmet väljer du **enheter** och skapar en ny enhet med **enhets-ID** som _mytestselfcertprimary_ från enhets mal len för termostat. Anteckna **ID-omfånget**. du använder det senare.

1. Öppna enheten som du har skapat och välj **Anslut**.

1. Välj **enskilda registreringar** som Connect- **metod** och **certifikat (X. 509)** som mekanism:

    ![Individuell registrering](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Välj alternativet Arkiv under primär och överför certifikat filen med namnet _mytestselfcertprimary_cert. pem_ som du skapade tidigare.

1. Välj fil alternativet för det sekundära certifikatet och ladda upp certifikat filen med namnet _mytestselfcertsecondary_cert. pem._ Välj sedan **Spara**:

    ![Överföring av enskilda registrerings certifikat](./media/how-to-connect-devices-x509/individual-enrollment.png)

Enheten har nu tillhandahållits med X. 509-certifikat.

## <a name="run-a-sample-individual-enrollment-device"></a>Kör ett exempel på en enskild registrerings enhet

1. Kopiera filen _mytestselfcertprimary_key. pem_ och _mytestselfcertprimary_cert. pem_ till mappen _Azure-IoT-SDK-Node/Device/samples/PnP_ som innehåller **simple_thermostat.js** programmet. Du använde det här programmet när du har slutfört [guiden Anslut en enhet (Java Script)](./tutorial-connect-device.md).

1. Ändra de miljövariabler som du använde i exemplet ovan enligt följande:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Kör skriptet och kontrol lera att enheten har allokerats:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Du kan också kontrol lera att telemetri visas på instrument panelen.

    ![Telemetri för enskilda registreringar](./media/how-to-connect-devices-x509/telemetry-primary.png)

Du kan även upprepa stegen ovan för _mytestselfcertsecondary_ -certifikat.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter enheter med X. 509-certifikat, är det föreslagna nästa steg att lära dig hur du [övervakar enhets anslutning med hjälp av Azure CLI](howto-monitor-devices-azure-cli.md)
