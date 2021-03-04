---
title: Autentisera underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Så här autentiserar du underordnade enheter eller löv enheter till IoT Hub och dirigerar deras anslutning via Azure IoT Edge gateway-enheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9e3e0f96d235829928c1f7c79864b1dc732f9e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046354"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autentisera en underordnad enhet på Azure IoT Hub

I ett scenario med transparent Gateway behöver underordnade enheter (ibland kallade löv enheter eller underordnade enheter) identiteter i IoT Hub som andra enheter. Den här artikeln vägleder dig genom alternativen för att autentisera en underordnad enhet till IoT Hub och visar hur du deklarerar Gateway-anslutningen.

Det finns tre allmänna steg för att konfigurera en lyckad transparent Gateway-anslutning. Den här artikeln beskriver det andra steget:

1. Konfigurera gateway-enheten som en server så att underordnade enheter kan ansluta till den på ett säkert sätt. Konfigurera gatewayen för att ta emot meddelanden från underordnade enheter och dirigera dem till rätt plats. De här stegen finns i [Konfigurera en IoT Edge enhet att fungera som en transparent Gateway](how-to-create-transparent-gateway.md).
2. **Skapa en enhets identitet för den underordnade enheten så att den kan autentiseras med IoT Hub. Konfigurera den underordnade enheten för att skicka meddelanden via gateway-enheten.**
3. Anslut den underordnade enheten till gateway-enheten och börja skicka meddelanden. De här stegen finns i [ansluta en underordnad enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-device.md).

Underordnade enheter kan autentiseras med IoT Hub med hjälp av någon av tre metoder: symmetriska nycklar (kallas ibland för delade åtkomst nycklar), X. 509 självsignerade certifikat eller certifikat från X. 509 certifikat utfärdare (CA) signerade certifikat. Stegen för autentisering liknar de steg som används för att konfigurera en icke-IoT-Edge-enhet med IoT Hub, med små skillnader för att deklarera Gateway-relationen.

Automatisk etablering av underordnade enheter med Azure-IoT Hub Device Provisioning Service (DPS) stöds inte.

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i [Konfigurera en IoT Edge-enhet så att den fungerar som en transparent Gateway](how-to-create-transparent-gateway.md).

Om du använder X. 509-autentisering kommer du att generera certifikat för din underordnade enhet. Ha samma certifikat från rot certifikat utfärdaren och skriptet som du använde för att kunna använda den transparenta Gateway-artikeln.

Den här artikeln hänvisar till *Gateway-värdnamnet* på flera punkter. Gateway-värdnamnet anges i parametern **hostname** för konfigurations filen på IoT Edge gateway-enheten. Det kallas i anslutnings strängen för den underordnade enheten. Gateway-värdnamnet måste matchas till en IP-adress, antingen med DNS eller en värd fil post på den underordnade enheten.

## <a name="register-device-with-iot-hub"></a>Registrera enheten med IoT Hub

Välj hur du vill att den underordnade enheten ska autentiseras med IoT Hub:

* [Symmetrisk nyckel autentisering](#symmetric-key-authentication): IoT Hub skapar en nyckel som du lägger på den underordnade enheten. När enheten autentiseras kontrollerar IoT Hub att de två nycklarna matchar. Du behöver inte skapa ytterligare certifikat för att använda symmetrisk nyckel autentisering.

  Den här metoden går snabbare att komma igång om du testar gatewayer i ett utvecklings-eller test scenario.

* [X. 509-självsignerad autentisering](#x509-self-signed-authentication): ibland kallas tumavtryck-autentisering, eftersom du delar tumavtrycket från enhetens X. 509-certifikat med IoT Hub.

  Certifikatautentisering rekommenderas för enheter i produktions scenarier.

* [X. 509 ca-signerad autentisering](#x509-ca-signed-authentication): Ladda upp rot certifikat utfärdarens certifikat till IoT Hub. När enheter presenterar sitt X. 509-certifikat för autentisering, kontrollerar IoT Hub att det tillhör en förtroende kedja signerade av samma rot certifikat för certifikat utfärdare.

  Certifikatautentisering rekommenderas för enheter i produktions scenarier.

### <a name="symmetric-key-authentication"></a>Autentisering med symmetrisk nyckel

Autentisering med symmetrisk nyckel eller autentisering med delad åtkomst nyckel är det enklaste sättet att autentisera med IoT Hub. Med symmetrisk nyckel autentisering är en Base64-nyckel kopplad till ditt IoT-enhets-ID i IoT Hub. Du inkluderar den nyckeln i dina IoT-program så att enheten kan presentera den när den ansluter till IoT Hub.

Lägg till en ny IoT-enhet i IoT Hub med hjälp av antingen Azure Portal, Azure CLI eller IoT-tillägget för Visual Studio Code. Kom ihåg att underordnade enheter måste identifieras i IoT Hub som vanliga IoT-enheter, inte IoT Edge enheter.

Ange följande information när du skapar den nya enhets identiteten:

* Skapa ett ID för enheten.

* Välj **symmetrisk nyckel** som autentiseringstyp.

* Välj **Ange en överordnad enhet** och välj den IoT Edge gateway-enhet som den här underordnade enheten ska ansluta till. Du kan alltid ändra överordnad senare.

   ![Skapa enhets-ID med symmetrisk nyckel-auth i portalen](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >Att ange den överordnade enheten som ska vara ett valfritt steg för underordnade enheter som använder symmetrisk nyckel autentisering. Men från och med IoT Edge version 1.1.0 varje underordnad enhet måste tilldelas en överordnad enhet.
   >
   >Du kan konfigurera IoT Edge Hub så att den går tillbaka till föregående beteende genom att ställa in miljövariabeln **AuthenticationMode** på värdet **CloudAndScope**.

Du kan också använda [IoT-tillägget för Azure CLI för](https://github.com/Azure/azure-iot-cli-extension) att slutföra samma åtgärd. I följande exempel används [AZ IoT Hub Device-Identity-](/cli/azure/ext/azure-iot/iot/hub/device-identity) kommandot för att skapa en ny IoT-enhet med symmetrisk nyckel autentisering och tilldela en överordnad enhet:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

[Hämta och ändra sedan anslutnings strängen](#retrieve-and-modify-connection-string) så att enheten kan ansluta via sin Gateway.

### <a name="x509-self-signed-authentication"></a>Självsignerad X. 509-autentisering

För X. 509-självsignerad autentisering, som ibland kallas tumavtryck, måste du skapa certifikat som ska placeras på din underordnade enhet. Dessa certifikat har ett tumavtryck i dem som du delar med IoT Hub för autentisering.

1. Skapa två enhets certifikat (primära och sekundära) för den underordnade enheten med ditt CA-certifikat.

   Om du inte har någon certifikat utfärdare för att skapa X. 509-certifikat kan du använda certifikat skripten IoT Edge demo för att [skapa underordnade enhets certifikat](how-to-create-test-certificates.md#create-downstream-device-certificates). Följ stegen för att skapa självsignerade certifikat. Använd samma rot certifikat för certifikat utfärdare som genererade certifikat för din gateway-enhet.

   Om du skapar egna certifikat måste du kontrol lera att enhetens certifikat mottagar namn har angetts till det enhets-ID som du använder när du registrerar IoT-enheten i Azure-IoT Hub. Den här inställningen krävs för autentisering.

2. Hämta SHA1-finger avtryck (kallas ett tumavtryck i IoT Hub-gränssnittet) från varje certifikat, som är en hexadecimal tecken sträng i 40. Använd följande openssl-kommando för att visa certifikatet och hitta finger avtrycket:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Kör det här kommandot två gånger, en gång för det primära certifikatet och en gång för det sekundära certifikatet. Du ger finger avtryck för båda certifikaten när du registrerar en ny IoT-enhet med självsignerade X. 509-certifikat.

3. Navigera till din IoT-hubb i Azure Portal och skapa en ny IoT-enhets identitet med följande värden:

   * Ange **enhets-ID: t** som matchar ämnes namnet för enhets certifikaten.
   * Välj **X. 509 själv signerad** som autentiseringstyp.
   * Klistra in de hexadecimala strängarna som du kopierade från enhetens primära och sekundära certifikat.
   * Välj **Ange en överordnad enhet** och välj den IoT Edge gateway-enhet som den här underordnade enheten ska ansluta till. Du kan alltid ändra överordnad senare.

   ![Skapa enhets-ID med X. 509 självsignerad autentisering i portalen](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Kopiera både de primära och sekundära enhets certifikaten och deras nycklar till valfri plats på den underordnade enheten. Flytta också en kopia av det delade rot certifikat utfärdarens certifikat som skapade både gateway-enhetens certifikat och de efterföljande enhets certifikaten.

   Du refererar till dessa certifikatfiler i alla program på den underordnade enheten som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](../key-vault/index.yml) eller en funktion som [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfiler.

5. Beroende på vilket språk du föredrar kan du läsa exempel på hur X. 509-certifikat kan refereras i IoT-program:

   * C#: [Konfigurera X. 509-säkerhet i din Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Du kan också använda [IoT-tillägget för Azure CLI för](https://github.com/Azure/azure-iot-cli-extension) att slutföra samma enhets skapande åtgärd. I följande exempel används [AZ IoT Hub Device-Identity-](/cli/azure/ext/azure-iot/iot/hub/device-identity) kommandot för att skapa en ny IoT-enhet med en självsignerad X. 509-autentisering och tilldelar en överordnad enhet:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

[Hämta och ändra sedan anslutnings strängen](#retrieve-and-modify-connection-string) så att enheten kan ansluta via sin Gateway.

### <a name="x509-ca-signed-authentication"></a>X. 509-signerad autentisering

För X. 509-signerad autentisering (CA) behöver du ett rot certifikat för certifikat utfärdare som registrerats i IoT Hub som du använder för att signera certifikat för din underordnade enhet. Alla enheter som använder ett certifikat som har problem med rot certifikat utfärdaren eller något av dess mellanliggande certifikat kommer att tillåtas att autentisera.

Det här avsnittet baseras på instruktionerna som beskrivs i IoT Hub artikel konfigurera [säkerhet för X. 509 i Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md).

1. Skapa två enhets certifikat (primära och sekundära) för den underordnade enheten med ditt CA-certifikat.

   Om du inte har någon certifikat utfärdare för att skapa X. 509-certifikat kan du använda certifikat skripten IoT Edge demo för att [skapa underordnade enhets certifikat](how-to-create-test-certificates.md#create-downstream-device-certificates). Följ stegen för att skapa CA-signerade certifikat. Använd samma rot certifikat för certifikat utfärdare som genererade certifikat för din gateway-enhet.

2. Följ anvisningarna i avsnittet [Registrera x. 509 CA-certifikat i IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) i *Konfigurera X. 509-säkerhet i Azure IoT Hub*. I det avsnittet utför du följande steg:

   1. Ladda upp ett rot certifikat för certifikat utfärdare. Om du använder demonstrations certifikaten är rot certifikat utfärdaren **\<path> /certs/Azure-IoT-test-Only.root.ca.cert.pem**.

   2. Kontrol lera att du äger det rot certifikat utfärdarens certifikat.

3. Följ anvisningarna i avsnittet [skapa en X. 509-enhet för din IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) i *Konfigurera X. 509-säkerhet i Azure IoT Hub*. I det avsnittet utför du följande steg:

   1. Lägg till en ny enhet. Ange ett gement namn för **enhets-ID** och välj autentiseringstypen **X. 509 ca signerad**.

   2. Ange en överordnad enhet. Välj **Ange en överordnad enhet** och välj den IoT Edge gateway-enhet som ska ansluta till IoT Hub.

4. Skapa en certifikat kedja för din underordnade enhet. Använd samma rot certifikat för certifikat utfärdare som du laddade upp till IoT Hub för att skapa den här kedjan. Använd samma små enhets-ID som du gav din enhets identitet i portalen.

5. Kopiera enhetens certifikat och nycklar till valfri plats på den underordnade enheten. Flytta också en kopia av det delade rot certifikat utfärdarens certifikat som skapade både gateway-enhetens certifikat och de efterföljande enhets certifikaten.

   Du refererar till de här filerna i alla program på den underordnade enheten som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](../key-vault/index.yml) eller en funktion som [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfiler.

6. Beroende på vilket språk du föredrar kan du läsa exempel på hur X. 509-certifikat kan refereras i IoT-program:

   * C#: [Konfigurera X. 509-säkerhet i din Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Du kan också använda [IoT-tillägget för Azure CLI för](https://github.com/Azure/azure-iot-cli-extension) att slutföra samma enhets skapande åtgärd. I följande exempel används [AZ IoT Hub Device-Identity-](/cli/azure/ext/azure-iot/iot/hub/device-identity) kommandot för att skapa en ny IoT-enhet med X. 509 ca-signerad autentisering och tilldelar en överordnad enhet:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

[Hämta och ändra sedan anslutnings strängen](#retrieve-and-modify-connection-string) så att enheten kan ansluta via sin Gateway.

## <a name="retrieve-and-modify-connection-string"></a>Hämta och ändra anslutnings sträng

När du har skapat en IoT-enhets identitet i portalen kan du hämta dess primära eller sekundära nycklar. En av dessa nycklar måste inkluderas i anslutnings strängen som program använder för att kommunicera med IoT Hub. För symmetrisk nyckel autentisering ger IoT Hub den fullständigt utformade anslutnings strängen i enhets informationen för din bekvämlighet. Du måste lägga till extra information om gateway-enheten i anslutnings strängen.

Anslutnings strängar för underordnade enheter behöver följande komponenter:

* IoT-hubben som enheten ansluter till: `Hostname={iothub name}.azure-devices.net`
* Enhets-ID som är registrerat i hubben: `DeviceID={device ID}`
* Autentiseringsmetoden, om symmetrisk nyckel eller X. 509-certifikat
  * Om du använder autentisering med symmetrisk nyckel ger du antingen den primära eller sekundära nyckeln: `SharedAccessKey={key}`
  * Om du använder autentisering med X. 509-certifikat anger du en flagga: `x509=true`
* Den gateway-enhet som enheten ansluter via. Ange **hostname** -värdet från IoT Edge gateway-enhetens konfigurations fil: `GatewayHostName={gateway hostname}`

Tillsammans ser en fullständig anslutnings sträng ut så här:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Eller

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Tack vare den överordnade/underordnade relationen kan du förenkla anslutnings strängen genom att anropa gatewayen direkt som anslutnings värd. Exempel:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Du använder den här ändrade anslutnings strängen i nästa artikel i den transparenta Gateway-serien.

## <a name="next-steps"></a>Nästa steg

Nu har du en IoT Edge enhet som registrerats med IoT Hub och som kon figurer ATS som en transparent Gateway. Du har också en underordnad enhet registrerad i IoT Hub och pekar på dess gateway-enhet.

Därefter måste du konfigurera en underordnad enhet för att lita på gateway-enheten och ansluta till den på ett säkert sätt. Fortsätt till nästa artikel i den transparenta Gateway-serien, [Anslut en underordnad enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-device.md).