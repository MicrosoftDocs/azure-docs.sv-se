---
title: Autentisera nedströmsenheter – Azure IoT Edge | Microsoft Docs
description: Hur du autentiserar nedströmsenheter eller lövenheter till IoT Hub och dirigerar anslutningen via Azure IoT Edge gatewayenheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4702682dcd6af68242fd5a34d1fb2e0a9273da36
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482032"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autentisera en underordnad enhet på Azure IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

I ett scenario med transparent gateway behöver nedströmsenheter (ibland kallade lövenheter eller underordnade enheter) identiteter i IoT Hub som vilken annan enhet som helst. Den här artikeln går igenom alternativen för att autentisera en nedströmsenhet till IoT Hub och visar sedan hur du deklarerar gatewayanslutningen.

Det finns tre allmänna steg för att konfigurera en lyckad transparent gatewayanslutning. Den här artikeln beskriver det andra steget:

1. Konfigurera gatewayenheten som en server så att nedströmsenheter kan ansluta till den på ett säkert sätt. Konfigurera gatewayen för att ta emot meddelanden från nedströmsenheter och dirigera dem till rätt mål. Dessa steg finns i Konfigurera [en IoT Edge att fungera som en transparent gateway.](how-to-create-transparent-gateway.md)
2. **Skapa en enhetsidentitet för nedströmsenheten så att den kan autentisera med IoT Hub. Konfigurera nedströmsenheten så att den skickar meddelanden via gatewayenheten.**
3. Anslut nedströmsenheten till gatewayenheten och börja skicka meddelanden. Dessa steg finns i Ansluta [en nedströmsenhet till en Azure IoT Edge gateway.](how-to-connect-downstream-device.md)

Nedströmsenheter kan autentisera med IoT Hub med någon av tre metoder: symmetriska nycklar (kallas ibland för delade åtkomstnycklar), själv signerade X.509-certifikat eller X.509-certifikatutfärdare (CA). Autentiseringsstegen liknar de steg som används för att konfigurera en icke-IoT Edge-enhet med IoT Hub, med små skillnader för att deklarera gateway-relationen.

Automatisk etablering av underordnade enheter med Azure IoT Hub Device Provisioning Service (DPS) stöds inte.

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i Konfigurera [en IoT Edge som ska fungera som en transparent gateway.](how-to-create-transparent-gateway.md)

Om du använder X.509-autentisering genererar du certifikat för den underordnade enheten. Ha samma rotcertifikatutfärdarcertifikat och det skript för certifikatgenerering som du använde för den transparenta gatewayartikeln som är tillgänglig för användning igen.

Den här artikeln refererar till *gatewayens värdnamn* på flera punkter. Gatewayens värdnamn deklareras i **parametern hostname** i konfigurationsfilen på den IoT Edge gatewayenheten. Det refereras till i anslutningssträngen för den underordnade enheten. Gatewayens värdnamn måste kunna matchas till en IP-adress, antingen med hjälp av DNS eller en värdfilpost på den underordnade enheten.

## <a name="register-device-with-iot-hub"></a>Registrera en enhet med IoT Hub

Välj hur du vill att nedströmsenheten ska autentisera med IoT Hub:

* [Autentisering med symmetrisk nyckel:](#symmetric-key-authentication)IoT Hub skapar en nyckel som du lägger på den underordnade enheten. När enheten autentiseras kontrollerar IoT Hub att de två nycklarna matchar. Du behöver inte skapa ytterligare certifikat för att använda autentisering med symmetrisk nyckel.

  Den här metoden är snabbare att komma igång om du testar gatewayer i ett utvecklings- eller testscenario.

* [X.509-själv](#x509-self-signed-authentication)signerad autentisering: Kallas ibland för tumavtrycksautentisering eftersom du delar tumavtrycket från enhetens X.509-certifikat med IoT Hub.

  Certifikatautentisering rekommenderas för enheter i produktionsscenarier.

* [X.509 CA-signerad autentisering:](#x509-ca-signed-authentication)Ladda upp rotcertifikatutfärdaren till IoT Hub. När enheter presenterar sina X.509-certifikat för autentisering IoT Hub att de tillhör en förtroendekedja som signerats av samma rotcertifikatutfärdarcertifikat.

  Certifikatautentisering rekommenderas för enheter i produktionsscenarier.

### <a name="symmetric-key-authentication"></a>Autentisering med symmetrisk nyckel

Symmetrisk nyckelautentisering, eller autentisering med delad åtkomstnyckel, är det enklaste sättet att autentisera med IoT Hub. Med autentisering med symmetrisk nyckel associeras en base64-nyckel med ditt IoT-enhets-ID i IoT Hub. Du inkluderar nyckeln i dina IoT-program så att enheten kan presentera den när den ansluter till IoT Hub.

Lägg till en ny IoT-enhet i din IoT-hubb med antingen Azure Portal, Azure CLI eller IoT-tillägget för Visual Studio Code. Kom ihåg att nedströmsenheter måste identifieras i IoT Hub vanliga IoT-enheter, inte IoT Edge enheter.

När du skapar den nya enhetsidentiteten anger du följande information:

* Skapa ett ID för din enhet.

* Välj **Symmetrisk nyckel** som autentiseringstyp.

* Välj **Ange en överordnad enhet och** välj den IoT Edge gatewayenhet som den här underordnade enheten ska ansluta via. Du kan alltid ändra den överordnade senare.

   ![Skapa enhets-ID med symmetrisk nyckelauth i portalen](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >Att ange den överordnade enheten brukade vara ett valfritt steg för nedströmsenheter som använder autentisering med symmetrisk nyckel. Men från och med IoT Edge version 1.1.0 måste varje underordnad enhet tilldelas till en överordnad enhet.
   >
   >Du kan konfigurera IoT Edge för att gå tillbaka till det tidigare beteendet genom att ange miljövariabeln **AuthenticationMode** till värdet **CloudAndScope**.

Du kan också använda [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) för att slutföra samma åtgärd. I följande exempel används kommandot [az iot hub device-identity](/cli/azure/iot/hub/device-identity) för att skapa en ny IoT-enhet med autentisering med symmetrisk nyckel och tilldela en överordnad enhet:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Hämta och [ändra sedan anslutningssträngen så](#retrieve-and-modify-connection-string) att enheten vet att den ska ansluta via sin gateway.

### <a name="x509-self-signed-authentication"></a>Själv signerad X.509-autentisering

För X.509-själv signerad autentisering, som ibland kallas tumavtrycksautentisering, måste du skapa certifikat som ska placera på din underordnade enhet. De här certifikaten har ett tumavtryck som du delar med IoT Hub för autentisering.

1. Skapa två enhetscertifikat (primär och sekundär) för den underordnade enheten med hjälp av certifikatutfärdaren.

   Om du inte har en certifikatutfärdare för att skapa X.509-certifikat kan du använda IoT Edge för att skapa underordnade [enhetscertifikat.](how-to-create-test-certificates.md#create-downstream-device-certificates) Följ stegen för att skapa själv signerade certifikat. Använd samma rotcertifikatutfärdarcertifikat som genererade certifikaten för din gatewayenhet.

   Om du skapar egna certifikat kontrollerar du att enhetscertifikatet ämnesnamn är inställt på det enhets-ID som du använder när du registrerar IoT-enheten i Azure IoT Hub. Den här inställningen krävs för autentisering.

2. Hämta SHA1-fingeravtrycket (kallas tumavtryck i IoT Hub gränssnittet) från varje certifikat, som är en sträng med 40 hexadecimala tecken. Använd följande openssl-kommando för att visa certifikatet och hitta fingeravtrycket:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Kör det här kommandot två gånger, en gång för det primära certifikatet och en gång för det sekundära certifikatet. Du anger fingeravtryck för båda certifikaten när du registrerar en ny IoT-enhet med själv signerade X.509-certifikat.

3. Gå till din IoT-hubb i Azure Portal och skapa en ny IoT-enhetsidentitet med följande värden:

   * Ange det **enhets-ID** som matchar ämnesnamnet för dina enhetscertifikat.
   * Välj **X.509 Self-Signed** som autentiseringstyp.
   * Klistra in de hexadecimala strängar som du kopierade från enhetens primära och sekundära certifikat.
   * Välj **Ange en överordnad enhet och** välj den IoT Edge gatewayenhet som den här underordnade enheten ska ansluta via. Du kan alltid ändra den överordnade senare.

   ![Skapa enhets-ID med själv signerad X.509-autentisering i portalen](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Kopiera både de primära och sekundära enhetscertifikaten och deras nycklar till valfri plats på den underordnade enheten. Flytta också en kopia av det delade rotcertifikatutfärdarcertifikatet som genererade både gatewayenhetscertifikatet och underordnade enhetscertifikat.

   Du refererar till dessa certifikatfiler i alla program på den nedströmsenhet som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](../key-vault/index.yml) eller en funktion som [Secure Copy Protocol för](https://www.ssh.com/ssh/scp/) att flytta certifikatfilerna.

5. Beroende på vilket språk du föredrar kan du granska exempel på hur X.509-certifikat kan refereras till i IoT-program:

   * C#: [Konfigurera X.509-säkerhet i din Azure IoT-hubb](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Du kan också använda [IoT-tillägget för Azure CLI för](https://github.com/Azure/azure-iot-cli-extension) att slutföra samma enhetsgenereringsåtgärd. I följande exempel används kommandot [az iot hub device-identity](/cli/azure/iot/hub/device-identity) för att skapa en ny IoT-enhet med själv signerad X.509-autentisering och tilldelar en överordnad enhet:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Hämta och [ändra sedan anslutningssträngen så](#retrieve-and-modify-connection-string) att enheten vet att den ska ansluta via sin gateway.

### <a name="x509-ca-signed-authentication"></a>X.509 CA-signerad autentisering

För X.509-certifikatutfärdare (CA) signerad autentisering behöver du ett rotcertifikatutfärdarcertifikat registrerat i IoT Hub som du använder för att signera certifikat för din underordnade enhet. Alla enheter som använder ett certifikat som har problem med rotcertifikatutfärdarcertifikatet eller något av dess mellanliggande certifikat kommer att tillåtas att autentisera.

Det här avsnittet baseras på anvisningarna i artikeln IoT Hub [Konfigurera X.509-säkerhet i Azure IoT Hub.](../iot-hub/iot-hub-security-x509-get-started.md)

1. Skapa två enhetscertifikat (primär och sekundär) för den underordnade enheten med hjälp av certifikatutfärdaren.

   Om du inte har en certifikatutfärdare för att skapa X.509-certifikat kan du använda IoT Edge för att skapa underordnade [enhetscertifikat.](how-to-create-test-certificates.md#create-downstream-device-certificates) Följ stegen för att skapa CA-signerade certifikat. Använd samma rotcertifikatutfärdarcertifikat som genererade certifikaten för din gatewayenhet.

2. Följ anvisningarna i avsnittet [Registrera X.509 CA-certifikat till din IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) i Konfigurera *X.509-säkerhet i Azure IoT Hub.* I det avsnittet utför du följande steg:

   1. Ladda upp ett rotcertifikatutfärdarcertifikat. Om du använder democertifikaten är rotcertifikatutfärdaren **\<path> /certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Kontrollera att du äger rotcertifikatutfärdaren.

3. Följ anvisningarna i avsnittet Skapa en [X.509-enhet för din IoT-hubb](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) i Konfigurera *X.509-säkerhet i Azure IoT Hub.* I det avsnittet utför du följande steg:

   1. Lägg till en ny enhet. Ange ett gemener för **enhets-ID** och välj autentiseringstypen **X.509 CA-signerad**.

   2. Ange en överordnad enhet. Välj **Ange en överordnad enhet** och välj den IoT Edge gatewayenhet som ska tillhandahålla anslutningen till IoT Hub.

4. Skapa en certifikatkedja för den underordnade enheten. Använd samma rotcertifikatutfärdarcertifikat som du laddade upp till IoT Hub för att skapa den här kedjan. Använd samma enhets-ID i gemener som du gav till enhetsidentiteten i portalen.

5. Kopiera enhetscertifikatet och nycklarna till valfri plats på den underordnade enheten. Flytta också en kopia av det delade rotcertifikatutfärdarcertifikatet som genererade både gatewayenhetscertifikatet och underordnade enhetscertifikat.

   Du refererar till dessa filer i alla program på nedströmsenheten som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](../key-vault/index.yml) eller en funktion som [Secure Copy Protocol för](https://www.ssh.com/ssh/scp/) att flytta certifikatfilerna.

6. Beroende på vilket språk du föredrar kan du granska exempel på hur X.509-certifikat kan refereras till i IoT-program:

   * C#: [Konfigurera X.509-säkerhet i din Azure IoT-hubb](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Du kan också använda [IoT-tillägget för Azure CLI för](https://github.com/Azure/azure-iot-cli-extension) att slutföra samma enhetsskapandeåtgärd. I följande exempel används kommandot [az iot hub device-identity](/cli/azure/iot/hub/device-identity) för att skapa en ny IoT-enhet med X.509 CA-signerad autentisering och tilldelar en överordnad enhet:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Hämta och [ändra sedan anslutningssträngen så](#retrieve-and-modify-connection-string) att enheten vet att den ska ansluta via sin gateway.

## <a name="retrieve-and-modify-connection-string"></a>Hämta och ändra anslutningssträng

När du har skapat en IoT-enhetsidentitet i portalen kan du hämta dess primära eller sekundära nycklar. En av dessa nycklar måste ingå i anslutningssträngen som program använder för att kommunicera med IoT Hub. För autentisering med symmetrisk nyckel IoT Hub den fullständigt formatade anslutningssträngen i enhetsinformation för att underlätta för dig. Du måste lägga till extra information om gatewayenheten i anslutningssträngen.

Anslutningssträngar för nedströmsenheter behöver följande komponenter:

* IoT-hubben som enheten ansluter till: `Hostname={iothub name}.azure-devices.net`
* Enhets-ID:t som registrerats med hubben: `DeviceID={device ID}`
* Autentiseringsmetoden, oavsett om det är symmetrisk nyckel eller X.509-certifikat
  * Om du använder autentisering med symmetrisk nyckel anger du antingen den primära eller sekundära nyckeln: `SharedAccessKey={key}`
  * Om du använder X.509-certifikatautentisering anger du en flagga: `x509=true`
* Gatewayenheten som enheten ansluter via. Ange värdet **för värddatornamn** från IoT Edge-gatewayenhetens konfigurationsfil: `GatewayHostName={gateway hostname}`

Tillsammans ser en fullständig anslutningssträng ut så här:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Eller:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Tack vare den överordnade/underordnade relationen kan du förenkla anslutningssträngen genom att anropa gatewayen direkt som anslutningsvärd. Exempel:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Du använder den här ändrade anslutningssträngen i nästa artikel i serien med transparent gateway.

## <a name="next-steps"></a>Nästa steg

Nu har du en enhet IoT Edge din IoT-hubb och konfigurerad som en transparent gateway. Du har också en underordnad enhet registrerad hos din IoT-hubb och pekar på dess gatewayenhet.

Därefter måste du konfigurera nedströmsenheten så att den litar på gatewayenheten och ansluta till den på ett säkert sätt. Fortsätt till nästa artikel i serien med transparent gateway, Anslut en [underordnad enhet till en Azure IoT Edge gateway](how-to-connect-downstream-device.md).