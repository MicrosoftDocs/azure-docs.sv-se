---
title: Så här certifierar du IoT Plug and Play-enheter | Microsoft Docs
description: Som enhets byggare lär du dig att köra tester och skicka en enhet för certifiering
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 43a568149ccf85579b4f8190974fff872d6e09ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199653"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>Certifiera IoT Plug and Play-enheter

Certifieringsprogrammet för IoT Plug and Play-enheter har verktyg som kontrollerar att en enhet uppfyller IoT Plug and Play-certifieringskraven. Verktygen hjälper också organisationer att öka medvetenheten om tillgängligheten för sina IoT Plug and Play-enheter. Dessa certifierade enheter är skräddarsydda för IoT-lösningar och hjälper till att minska tiden till marknaden.

Den här artikeln visar hur du:

- Installera tillägg för kommando rads verktyget för Azure IoT för Azure CLI
- Kör IoT Plug and Play-testerna för att validera enhets programmet under utvecklings fasen  
- Använd Azure-certifierad enhets Portal för att validera enhets programmet

## <a name="prepare-your-device"></a>Förbereda enheten

Program koden som körs på din IoT-Plug and Play måste:

- Anslut till Azure IoT Hub med [enhets etablerings tjänsten (DPS)](../iot-dps/about-iot-dps.md).
- Följ [IoT-plugin-programmet](concepts-convention.md) för att genomföra telemetri, egenskaper och kommandon.

Programmet är program vara som installeras separat från operativ systemet eller paketeras med operativ systemet i en avbildning av inbyggd program vara som är flash till enheten.

Certifierings processen verifierar att enheten är IoT Plug and Play-kompatibel genom att verifiera att enhets implementeringen matchar telemetri, egenskaper och kommandon som definierats i DTDL-enhets modellen (Digital enmated [Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) och att modellen är tillgänglig i databasen för den [offentliga Azure IoT-modellen](concepts-model-repository.md).

För att uppfylla certifierings kraven måste enheten:

- Ansluter till Azure IoT Hub med hjälp av [DPS](../iot-dps/about-iot-dps.md).
- Implementera telemetri, egenskaper eller kommandon som följer IoT Plug and Play-konventionen.
- Beskriv interaktionen mellan enheter med en [DTDL v2](https://aka.ms/dtdl) -modell.
- Publicera modellen och alla nödvändiga gränssnitt, i lagrings platsen för den offentliga Azure IoT-modellen
- Skicka modell-ID under [DPS-registreringen](./concepts-developer-guide-device.md#dps-payload) i nytto lasten för DPS-etablering.
- Meddela modell-ID: t under [MQTT-anslutningen](./concepts-developer-guide-device.md#model-id-announcement).
- Alla enhets modeller måste vara kompatibla med [Azure IoT Central](../iot-central/core/overview-iot-central-developer.md).

> [!NOTE]
> Azure IoT Central har för närvarande inte fullt stöd för **matrisen** och **geospatiala** data typerna DTDL.

## <a name="test-with-the-azure-iot-extension-cli"></a>Testa med Azure IoT Extension CLI

Med [Azure IoT CLI-tillägget](/cli/azure/ext/azure-iot/iot/product) kan du verifiera att enhets implementeringen matchar modellen innan du skickar in enheten för certifiering via Azure-certifierad enhets Portal.

Följande steg visar hur du förbereder och kör certifierings test med hjälp av CLI:

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>Installera Azure IoT-tillägget för Azure CLI

Se installations anvisningarna för att konfigurera [Azure CLI](/cli/azure) i din miljö.

Kör följande kommando för att installera Azure IoT-tillägget:

```azurecli
az extension add --name azure-iot
```

Mer information finns i [Azure CLI för Azure IoT](/cli/azure/azure-cli-reference-for-iot).

### <a name="create-a-new-product-test"></a>Skapa ett nytt produkt test

Följande kommando skapar ett test med hjälp av DPS med en metod för symmetrisk nyckel attestering:

- Skapar en ny produkt som ska testas och genererar en test konfiguration. I utdata visas DPS-information som enheten måste använda för etablering: primär nyckel, enhets-ID och ID-omfång.
- Anger mappen med de DTDL-filer som beskriver din modell.

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> Du måste [Logga in i](/cli/azure/authenticate-azure-cli) din prenumeration när du använder cli.

JSON-utdata från kommandot innehåller `primaryKey` , `registrationId` och `scopeID` används när du ansluter enheten.

Förväntad utdata:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>Anslut enheten

Använd DPS-informations utdata med föregående kommando för att ansluta enheten till test IoT Hubs instansen.

### <a name="manage-and-configure-the-product-tests"></a>Hantera och konfigurera produkt testen

När enheten är ansluten och redo att samverka med IoT Hub genererar du en produkt test konfigurations fil. Så här skapar du filen:

- Använd testet `id` från utdata från föregående kommando.
- Använd `--wait` parametern för att hämta test väskan.

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Förväntad utdata:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

Du kan använda `az iot product test case update` kommandot för att ändra test konfigurations filen.

### <a name="run-the-tests"></a>Kör testerna

När du har skapat test konfigurationen är nästa steg att köra testerna. Använd samma `devicetestId` från föregående kommandon som-parameter för att köra testerna. Kontrol lera test resultaten för att se till att alla tester har statusen `Passed` .

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Exempel på testning av utdata

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Testa med portalen för Microsoft Azure Certified for IoT

Följande steg visar hur du använder [Azure-certifierad enhets Portal](https://aka.ms/acdp) för att publicera, registrera produkt information, skicka in en guide för att komma igång och köra certifierings test.

### <a name="onboarding"></a>Publicering

Om du vill använda [certifierings portalen](https://aka.ms/acdp)måste du använda en Azure Active Directory från din arbets-eller skol klient.

Ditt konto måste vara medlem i [Microsoft Partner Network](https://partner.microsoft.com)för att publicera modeller till den [offentliga Azure IoT-modellen](https://github.com/Azure/iot-plugandplay-models). Systemet kontrollerar att Microsoft Partner Network-ID finns och att kontot är fullständigt testats innan det publiceras till enhets katalogen.

### <a name="company-profile"></a>Företags profil

Du kan hantera din företags profil från den vänstra navigerings menyn. Företags profilen innehåller företagets URL, e-postadress och företags logo typ. Program avtalet måste godkännas på den här sidan innan du kör några certifierings åtgärder.

Företags profil informationen används i enhets beskrivningen som visas i enhets katalogen.

### <a name="create-new-project"></a>Skapa nytt projekt

Om du vill certifiera en enhet måste du först skapa ett nytt projekt.

Navigera till [certifierings portalen](https://aka.ms/acdp). På sidan **projekt** väljer du *+ Skapa nytt projekt*. Ange ett namn för projektet, enhetens namn och välj en enhets klass.

Den produkt information som du anger under certifierings processen hamnar i fyra kategorier:

- Enhets information. Samlar in information om enheten, till exempel namn, beskrivning, certifieringar och operativ system.
- Guiden **Kom igång** . Du måste skicka guiden som ett PDF-dokument som ska godkännas av system administratören innan du publicerar enheten.
- Marknadsförings information. Ange kund färdig marknadsförings information för din enhet. Marknadsförings informationen innehåller som beskrivning, ett foto och distributörer.
- Ytterligare bransch certifieringar. I det här valfria avsnittet kan du ange ytterligare information om andra certifieringar som enheten har.

### <a name="connect-and-test"></a>Anslut och testa

Steget Anslut och testa kontrollerar att enheten uppfyller kraven för IoT Plug and Play-certifiering.

Det finns tre steg som ska utföras:

1. Anslut och identifiera gränssnitt. Enheten måste ansluta till Azure IoT-certifierings tjänsten via DPS. Välj autentiseringsmetoden (X. 509-certifikat, symmetriska nycklar eller Trusted Platform Module) för att använda och uppdatera enhets programmet med DPS-informationen.
1. Granska gränssnitt. Granska gränssnittet och se till att var och en har nytto laster som passar för testning.
1. Koncentration. Systemet testar varje enhets modell för att kontrol lera att telemetri, egenskaper och kommandon som beskrivs i modellen följer IoT Plug and Play konventioner. När testet är klart väljer du länken **Visa loggar** för att se Telemetrin från enheten och rå data som skickas till IoT Hub enhetens dubbla egenskaper.

### <a name="submit-and-publish"></a>Skicka och publicera

Den slutliga nödvändiga fasen är att skicka projektet för granskning. Det här steget meddelar en Azure-certifierad enhets grupp medlem att granska ditt projekt, inklusive enhets-och marknadsförings information och guiden kom igång. En grupp medlem kan kontakta dig på företagets e-postadress som tidigare medföljde frågor eller redigera begär Anden innan godkännande.

Om enheten kräver ytterligare manuell verifiering som en del av certifieringen får du ett meddelande för tillfället.

När en enhet är certifierad kan du välja att publicera din produkt information till Azure-certifierad enhets katalog med hjälp av funktionen **Publicera till katalog** på sidan produkt Sammanfattning.

## <a name="next-steps"></a>Nästa steg

Nu när enheten har lagts till, kan du kontakta enhetens certifierings team på [iotcert@microsoft.com](mailto:iotcert@microsoft.com) för att fortsätta till nästa steg, som omfattar Microsoft Partner Network medlemskaps verifiering och en granskning av kom igång guider. När alla krav är uppfyllda kan du välja att enheten ska ingå i [katalogen certifierad för Azure IoT-enheter](https://aka.ms/devicecatalog).