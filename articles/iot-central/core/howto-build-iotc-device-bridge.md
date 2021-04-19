---
title: Distribuera Azure IoT Central enhetsbryggan | Microsoft Docs
description: Distribuera IoT Central för att ansluta andra IoT-moln till din IoT Central app. Andra IoT-moln är Sigfox, Particle Device Cloud och The Things Network.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 6b535ecb80fae9f55eb6ab11751c26e0c6d0e9e5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713748"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Använd IoT Central för att ansluta andra IoT-moln till IoT Central

*Den här artikeln gäller för administratörer.*

## <a name="azure-iot-central-device-bridge"></a>Azure IoT Central enhetsbrygga

Enhetsbryggan IoT Central är en lösning med öppen källkod som ansluter andra IoT-moln till ditt IoT Central program. Exempel på andra IoT-moln är [Sigfox,](https://www.sigfox.com/) [Particle Device Cloud](https://www.particle.io/)och The Things [Network](https://www.thethingsnetwork.org/). Enhetsbryggan fungerar genom att vidarebefordra data från enheter som är anslutna till andra IoT-moln till ditt IoT Central program. Enhetsbryggan vidarebefordrar endast data till IoT Central, den skickar inte kommandon eller egenskapsuppdateringar från IoT Central tillbaka till enheterna.

Med enhetsbryggan kan du kombinera kraften i IoT Central med enheter som tillgångsspårningsenheter som är anslutna till Sigfox nät med låg ström, luftkvalitetsövervakningsenheter i molnet för partiklar eller jordfuktövervakningsenheter i Things Network. Du kan IoT Central programfunktioner som regler och analyser på data, skapa arbetsflöden i Power Automate och Azure Logic Apps eller exportera data.

Enhetsbryggalösningen tillhandahåller flera Azure-resurser i din Azure-prenumeration som tillsammans omvandlar och vidarebefordrar enhetsmeddelanden till IoT Central.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktionerna behöver du en aktiv Azure-prenumeration.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Slutför [snabbstarten Skapa Azure IoT Central program](./quick-deploy-iot-central.md) för att skapa ett IoT Central-program med hjälp av mallen **Anpassad app > anpassat** program.

## <a name="overview"></a>Översikt

Den IoT Central enhetsbryggan är en lösning med öppen källkod i GitHub. Den använder en anpassad Azure Resource Manager distribuerar flera resurser till din Azure-prenumeration, inklusive en Azure-funktionsapp.

Funktionsappen är kärnan i enhetsbryggan. Den tar emot HTTP POST-begäranden från andra IoT-plattformar via en enkel webhook. [Lagringsplatsen Azure IoT Central Device Bridge](https://github.com/Azure/iotc-device-bridge) innehåller exempel som visar hur man ansluter Sigfox-,Partikel- och The Things Network-moln. Du kan utöka den här lösningen för att ansluta till ditt anpassade IoT-moln om din plattform kan skicka HTTP POST-begäranden till din funktionsapp.

Funktionsappen transformerar data till ett format som IoT Central och vidarebefordrar dem med hjälp av enhetsetableringstjänsten och enhetsklient-API:er:

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Skärmbild av Azure-funktioner.":::

Om IoT Central identifierar enhets-ID:t i det vidarebefordrade meddelandet visas telemetrin från enheten i IoT Central. Om enhets-ID:t inte känns igen av ditt IoT Central program försöker funktionsappen registrera en ny enhet med enhets-ID:t. Den nya enheten visas som **en enhet som inte är** kopplad på **sidan** Enheter i IoT Central program. Från sidan **Enheter** kan du associera den nya enheten med en enhetsmall och sedan visa telemetrin.

## <a name="deploy-the-device-bridge"></a>Distribuera enhetsbryggan

Så här distribuerar du enhetsbryggan till din prenumeration:

1. I ditt IoT Central program går du till sidan **Administration > enhetsanslutning.**

    1. Anteckna **ID-omfånget**. Du använder det här värdet när du distribuerar enhetsbryggan.

    1. På samma sida öppnar du **registreringsgruppen SAS-IoT-Devices.** På **gruppsidan SAS-IoT-Devices** kopierar du **primärnyckeln**. Du använder det här värdet när du distribuerar enhetsbryggan.

1. Använd knappen **Distribuera till Azure** nedan för att öppna den anpassade Resource Manager som distribuerar funktionsappen till din prenumeration. Använd **ID-omfång** **och primärnyckel** från föregående steg:

    [![Distribuera till Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

När distributionen är klar måste du installera de NPM-paket som funktionen kräver:

1. I Azure Portal du funktionsappen som distribuerades till din prenumeration. Gå sedan till **Utvecklingsverktyg > Konsol.** Installera paketen genom att köra följande kommandon i -konsolen:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Det kan ta flera minuter att köra de här kommandona. Du kan ignorera eventuella varningsmeddelanden.

1. När paketinstallationen är klar väljer du **Starta** om på **översiktssidan** för funktionsappen:

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="Skärmbild av Starta om.":::

1. Funktionen är nu redo att användas. Externa system kan använda HTTP POST-begäranden för att skicka enhetsdata via enhetsbryggan till IoT Central program. Du hämtar funktions-URL:en genom att **gå till Functions > IoTCIntegration > Code + Test > Get function URL**:

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="Skärmbild av Hämta funktions-URL.":::

Meddelandekroppar som skickas till enhetsbryggan måste ha följande format:

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

Varje nyckel i `measurements` objektet måste matcha namnet på en telemetrityp i enhetsmallen i IoT Central program. Den här lösningen stöder inte att gränssnitts-ID:t anges i meddelandetexten. Så om två olika gränssnitt har en telemetrityp med samma namn visas måttet i båda telemetriströmmarna i ditt IoT Central program.

Du kan inkludera ett fält i brödtexten för att ange datum och tid för meddelandet i `timestamp` UTC. Det här fältet måste vara i ISO 8601-format. Till exempel `2020-06-08T20:16:54.602Z`. Om du inte inkluderar en tidsstämpel används aktuellt datum och tid.

Du kan inkludera ett `modelId` fält i brödtexten. Använd det här fältet för att associera enheten med en enhetsmall under etableringen. Den här funktionen stöds endast av [V3-program](howto-get-app-info.md).

måste `deviceId` vara alfanumeriskt, gemener och får innehålla bindestreck.

Om du inte inkluderar fältet, eller om IoT Central inte känner igen modell-ID:t, skapar ett meddelande med ett okänt en ny, oassocierad enhet `modelId` `deviceId` i IoT Central.  En operatör kan migrera enheten manuellt till rätt enhetsmall. Mer information finns i [Hantera enheter i Azure IoT Central program > Migrera enheter till en mall.](howto-manage-devices.md)

I [V2-program](howto-get-app-info.md)visas den nya enheten på **sidan Device Explorer > Enheter som inte är associerade.** Välj **Associera** och välj en enhetsmall för att börja ta emot inkommande telemetri från enheten.

> [!NOTE]
> Tills enheten har associerats med en mall returnerar alla HTTP-anrop till funktionen felstatusen 403.

Om du vill aktivera loggning för funktionsappen med Application Insights går du **till Monitoring > Logs** in your function app (Övervakningsloggar i funktionsappen) i Azure Portal. Välj **Aktivera Application Insights**.

## <a name="provisioned-resources"></a>Etablerade resurser

Mallen Resource Manager tillhandahåller följande resurser i din Azure-prenumeration:

* Funktionsapp
* App Service-plan
* Lagringskonto
* Nyckelvalv

Nyckelvalvet lagrar SAS-gruppnyckeln för ditt IoT Central program.

Funktionsappen körs på en [förbrukningsplan](https://azure.microsoft.com/pricing/details/functions/). Även om det här alternativet inte erbjuder dedikerade beräkningsresurser kan enhetsbryggan hantera hundratals enhetsmeddelanden per minut, vilket är lämpligt för mindre vagnparker med enheter eller enheter som skickar meddelanden mindre ofta. Om ditt program är beroende av att strömma ett stort antal enhetsmeddelanden ersätter du förbrukningsplanen med en dedikerad [App Service-plan.](https://azure.microsoft.com/pricing/details/app-service/windows/) Den här planen erbjuder dedikerade beräkningsresurser som ger snabbare serversvarstider. Med hjälp av App Service standardplan var den högsta observerade prestandan för Azure-funktionen på den här lagringsplatsen cirka 1 500 enhetsmeddelanden per minut. Mer information finns i [Värdalternativ för Azure-funktioner.](../../azure-functions/functions-scale.md)

Om du vill använda en App Service-plan i stället för en förbrukningsplan redigerar du den anpassade mallen innan du distribuerar. Välj **Redigera mall.**

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="Skärmbild av Redigera mall.":::

Ersätt följande segment:

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

med

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

Redigera sedan mallen som ska ingå `"alwaysOn": true` i konfigurationen för resursen under Konfigurationen `functionapp` `"properties": {"SiteConfig": {...}}` [alwaysOn säkerställer](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan) att funktionsappen alltid körs.

## <a name="examples"></a>Exempel

I följande exempel beskrivs hur du konfigurerar enhetsbryggan för olika IoT-moln:

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>Exempel 1: Ansluta partiklar via enhetsbryggan

Om du vill ansluta en partikelenhet via enhetsbryggan till IoT Central går du till partikelkonsolen och skapar en ny webhook-integrering. Ange JSON **som Format** **för begäran.**  Under **Avancerade inställningar** använder du följande anpassade brödtextformat:

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": {{{PARTICLE_EVENT_VALUE}}}
  }
}
```

Klistra in **funktions-URL:en** från din Azure-funktionsapp så ser du attPartikelenheter visas som oassocierade enheter i IoT Central. Mer information finns i blogginlägget Så här integrerar du [dinapartikeldrivna projekt Azure IoT Central](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/) här.

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>Exempel 2: Ansluta Sigfox-enheter via enhetsbryggan

Vissa plattformar tillåter kanske inte att du anger formatet för enhetsmeddelanden som skickas via en webhook. För sådana system måste du konvertera meddelandenyttolasten till det förväntade brödtextformatet innan enhetsbryggan bearbetar den. Du kan göra konverteringen i samma Azure-funktion som kör enhetsbryggan.

Det här avsnittet visar hur du konverterar nyttolasten för en Sigfox-webhook-integrering till det brödtextformat som förväntas av enhetsbryggan. Sigfox-molnet överför enhetsdata i ett hexadecimalt strängformat. För enkelhetens skull innehåller enhetsbryggan en konverteringsfunktion för det här formatet, som accepterar en delmängd av de möjliga fälttyperna i en Sigfox-enhetsnyttolast: och `int` `uint` 8, 16, 32 eller 64 bitar, av 32 bitar eller `float` 64 bitar; little-endian och big-endian. Om du vill bearbeta meddelanden från en Sigfox-webhook-integrering gör du följande ändringar _i IoTCIntegration/index.js-filen_ i funktionsappen.

Om du vill konvertera meddelandenyttolasten lägger du till följande kod före anropet till på rad 21 och ersätter med `handleMessage` `payloadDefinition` din Sigfox-nyttolastdefinition:

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Sigfox-enheter förväntar sig `204` en svarskod. Lägg till följande kod efter anropet `handleMessage` till på rad 21:

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>Exempel 3: Ansluta enheter från Things Network via enhetsbryggan

Så här ansluter du Things Network-enheter till IoT Central:

* Lägg till en ny HTTP-integrering i ditt program i The Things Network: **Application > Integrations > till integration > HTTP Integration**.
* Kontrollera att programmet innehåller en avkodarfunktion som automatiskt konverterar nyttolasten för dina enhetsmeddelanden till JSON innan den skickas till Azure Function: **Application > Payload Functions > decoder**.

I följande exempel visas en JavaScript-avkodningsfunktion som du kan använda för att avkoda vanliga numeriska typer från binära data:

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

När du har definierat integrationen lägger du till följande kod före anropet till på rad `handleMessage` 21 *i IoTCIntegration/index.js-filen* för Azure-funktionsappen. Den här koden översätter http-integreringens brödtext till det förväntade formatet.

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>Begränsningar

Enhetsbryggan vidarebefordrar endast meddelanden till IoT Central och skickar inte tillbaka meddelanden till enheter. Det är därför egenskaper och kommandon inte fungerar för enheter som ansluter till IoT Central via den här enhetsbryggan. Eftersom åtgärder för enhetstvilling inte stöds går det inte att uppdatera enhetsegenskaper via enhetsbryggan. Om du vill använda dessa funktioner måste en enhet ansluta direkt till IoT Central med någon av [Azure IoT-enhets-SDK:erna](../../iot-hub/iot-hub-devguide-sdks.md).

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du IoT Central enhetsbryggan föreslår vi följande steg:

> [!div class="nextstepaction"]
> [Hantera dina enheter](howto-manage-devices.md)
