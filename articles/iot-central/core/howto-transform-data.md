---
title: Transformera data för Azure IoT Central | Microsoft Docs
description: IoT-enheter skickar data i olika format som du kan behöva transformera. Den här artikeln beskriver hur du transformerar data både på vägen IoT Central och på väg ut. De scenarier som beskrivs använder IoT Edge och Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7d9575bedbdce96ef59e9b1d77b9034162bc16bf
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730487"
---
# <a name="transform-data-for-iot-central"></a>Transformera data för IoT Central

*Det här avsnittet gäller för lösningsbyggare.*

IoT-enheter skickar data i olika format. Om du vill använda enhetsdata med IoT Central program kan du behöva använda en transformering för att:

- Gör dataformatet kompatibelt med ditt IoT Central program.
- Konvertera enheter.
- Beräkna nya mått.
- Utöka data från andra källor.

Den här artikeln visar hur du transformerar enhetsdata utanför IoT Central antingen vid ingress eller utgående.

Följande diagram visar tre vägar för data som innehåller transformningar:

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Sammanfattning av datatransformeringsvägar för både in- och utgående trafik" border="false":::

I följande tabell visas tre exempel på transformeringstyper:

| Transformering | Beskrivning | Exempel  | Anteckningar |
|------------------------|-------------|----------|-------|
| Meddelandeformat         | Konvertera till eller ändra JSON-meddelanden. | CSV till JSON  | Vid ingress. IoT Central accepterar endast JSON-värdemeddelanden. Mer information finns i [Telemetri, egenskaps- och kommandonyttolaster.](concepts-telemetry-properties-commands.md) |
| Beräkningar           | Matematikfunktioner som [Azure Functions](../../azure-functions/index.yml) kan köra. | Enhetskonvertering från Fahrenheit till Celsius.  | Transformera med hjälp av utgående mönster för att dra nytta av skalbar enhetsingress via direktanslutning till IoT Central. Genom att transformera data kan du IoT Central funktioner som visualiseringar och jobb. |
| Meddelandeberikning     | Berikningar från externa datakällor hittades inte i enhetsegenskaper eller telemetri. Mer information om interna berikanden finns i Exportera [IoT-data till molnmål med hjälp av dataexport](howto-export-data.md) | Lägg till väderinformation i meddelanden med platsdata från enheter. | Transformera med hjälp av utgående mönster för att dra nytta av skalbar enhetsingress via direktanslutning till IoT Central. |

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln behöver du en aktiv Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

För att konfigurera lösningen behöver du ett IoT Central program. Information om hur du skapar ett IoT Central-program finns i [Skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).

## <a name="data-transformation-at-ingress"></a>Datatransformering vid ingress

Det finns två alternativ för att transformera enhetsdata vid ingress:

- **IoT Edge:** Använd en IoT Edge-modul för att transformera data från nedströmsenheter innan du skickar data till IoT Central program.

- **IoT Central enhetsbrygga:** IoT Central-enhetsbryggan ansluter andra IoT-enhetsmoln, till exempel Sigfox, Particle och The Things Network, till IoT Central. [](https://github.com/Azure/iotc-device-bridge) Enhetsbryggan använder en Azure-funktion för att vidarebefordra data och du kan anpassa funktionen för att transformera enhetsdata.

### <a name="use-iot-edge-to-transform-device-data"></a>Använda IoT Edge för att transformera enhetsdata

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="Datatransformering vid ingress med IoT Edge" border="false":::

I det här scenariot IoT Edge en modul data från nedströmsenheter innan de vidarebefordras till IoT Central program. På en hög nivå är stegen för att konfigurera det här scenariot:

1. **Konfigurera en IoT Edge enhet:** Installera och etablera en IoT Edge som en gateway och anslut gatewayen till ditt IoT Central program.

1. **Anslut nedströmsenhet till IoT Edge enhet:** Anslut nedströmsenheter till IoT Edge och etablera dem i ditt IoT Central program.

1. **Transformera enhetsdata i IoT Edge:** Skapa en IoT Edge modul för att transformera data. Distribuera modulen till den IoT Edge gateway-enhet som vidarebefordrar transformerade enhetsdata till ditt IoT Central program.

1. **Verifiera**: Skicka data från en underordnad enhet till gatewayen och kontrollera att transformerade enhetsdata når IoT Central program.

I exemplet som beskrivs i följande avsnitt skickar den underordnade enheten CSV-data i följande format till IoT Edge gatewayenheten:

```csv
"<temperature >, <pressure>, <humidity>"
```

Du vill använda en IoT Edge-modul för att transformera data till följande JSON-format innan de skickas till IoT Central:

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

Följande steg visar hur du konfigurerar det här scenariot:

### <a name="build-the-custom-module"></a>Skapa den anpassade modulen

I det här scenariot IoT Edge enheten en anpassad modul som transformerar data från den underordnade enheten. Innan du distribuerar och konfigurerar IoT Edge-enheten måste du:

- Skapa den anpassade modulen.
- Lägg till den anpassade modulen i ett containerregister.

Körningskörningen IoT Edge anpassade moduler från ett containerregister, till exempel ett Azure-containerregister eller Docker Hub. I [Azure Cloud Shell](../../cloud-shell/overview.md) finns alla verktyg du behöver för att skapa ett containerregister, skapa modulen och ladda upp modulen till registret:

Så här skapar du ett containerregister:

1. Öppna Azure Cloud Shell [och](https://shell.azure.com/) logga in på din Azure-prenumeration.

1. Kör följande kommandon för att skapa ett Azure-containerregister:

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Anteckna värdena och `username` `password` . Du använder dem senare.

Så här skapar du den anpassade modulen [i Azure Cloud Shell](https://shell.azure.com/):

1. I [Azure Cloud Shell](https://shell.azure.com/)navigerar du till en lämplig mapp.
1. Kör följande kommando för att klona GitHub-lagringsplatsen som innehåller modulens källkod:

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Skapa den anpassade modulen genom att köra följande kommandon i Azure Cloud Shell:

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    Det kan ta flera minuter att köra de tidigare kommandona.

### <a name="set-up-an-iot-edge-device"></a>Konfigurera en IoT Edge enhet

I det här scenariot används IoT Edge gatewayenhet för att transformera data från alla nedströmsenheter. I det här avsnittet beskrivs hur IoT Central skapa enhetsmallar för gatewayen och nedströmsenheterna i IoT Central program. IoT Edge enheter använder ett distributionsmanifest för att konfigurera sina moduler.

För att skapa en enhetsmall för nedströmsenheten använder det här scenariot en enkel termostatenhetsmodell:

1. Ladda ned [enhetsmodellen för termostatenheten](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) till den lokala datorn.

1. Logga in på IoT Central program och gå till sidan **Enhetsmallar.**

1. Välj **+ Ny,** välj **IoT-enhet** och välj **Nästa: Anpassa**.

1. Ange *Termostat* som mallnamn och välj **Nästa: Granska**. Välj sedan **Skapa**.

1. Välj **Importera en modell** och importerathermostat-2.js *fil* som du laddade ned tidigare.

1. Välj **Publicera** för att publicera den nya enhetsmallen.

Så här skapar du en enhetsmall för IoT Edge gatewayenhet:

1. Spara en kopia av distributionsmanifestet till din lokala utvecklingsdator: [moduledeployment.jspå](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json).

1. Öppna din lokala kopia av *moduledeployment.jspå* manifestfilen i en textredigerare.

1. Leta upp `registryCredentials` avsnittet och ersätt platshållarna med de värden som du antecknade när du skapade Azure-containerregistret. Värdet `address` ser ut så `<username>.azurecr.io` här: .

1. Leta reda `settings` på avsnittet för `transformmodule` . Ersätt `<acr or docker repo>` med samma värde som du använde i föregående `address` steg. Spara ändringarna.

1. I ditt IoT Central program går du till **sidan Enhetsmallar.**

1. Välj **+ Ny,** **välj Azure IoT Edge** och välj sedan **Nästa: Anpassa**.

1. Ange *IoT Edge gatewayenhet* som enhetsmallens namn. Välj **This is a gateway device (Det här är en gatewayenhet).** Välj **Bläddra** för att ladda uppmoduledeployment.jspå distributionsmanifestfilen som du redigerade tidigare. 

1. När distributionsmanifestet har verifierats **väljer du Nästa: Granska** och sedan **Skapa.**

1. Under **Modell** väljer du **Relationer**. Välj **+ Lägg till relation**. Ange *Nedströmsenhet* som visningsnamn och **välj Termostat** som mål. Välj **Spara**.

1. Välj **Publicera** för att publicera enhetsmallen.

Nu har du två enhetsmallar i ditt IoT Central program. Mallen **IoT Edge gatewayenhet** och mallen **Termostat** som nedströmsenhet.

Registrera en gatewayenhet i IoT Central:

1. I ditt IoT Central program går du till **sidan** Enheter.

1. Välj **IoT Edge gatewayenhet** och välj **Skapa en enhet.** Ange *IoT Edge gateway-enhet* som enhetsnamn, ange *gateway-01* som enhets-ID, kontrollera att IoT Edge **gateway-enhet** har valts som enhetsmall. Välj **Skapa**.

1. I listan över enheter klickar du på IoT Edge **gatewayenhet** och väljer sedan **Anslut.**

1. Anteckna värdena för **ID-omfång,** **Enhets-ID** och **Primärnyckel** för den IoT Edge **gatewayenheten**. Du använder dem senare.

Registrera en underordnad enhet i IoT Central:

1. I ditt IoT Central program går du till **sidan** Enheter.

1. Välj **Termostat** och välj **Skapa en enhet.** Ange *Termostat* som enhetsnamn, ange *downstream-01* som enhets-ID, kontrollera att **Termostat** har valts som enhetsmall. Välj **Skapa**.

1. I listan över enheter väljer du **Termostat och** sedan Anslut **till gateway.** Välj **enhetsmallen IoT Edge gateway** och IoT Edge **gatewayenhetsinstansen.** Välj **Bifoga.**

1. I listan över enheter klickar du på **termostaten** och väljer sedan **Anslut.**

1. Anteckna värdena för **ID-omfång,** **Enhets-ID** och **Primärnyckel** för **termostatenheten.** Du använder dem senare.

### <a name="deploy-the-gateway-and-downstream-devices"></a>Distribuera gatewayen och nedströmsenheterna

För enkelhetens skull använder den här artikeln virtuella Azure-datorer för att köra gatewayen och nedströmsenheterna. Om du vill skapa de två virtuella Azure-datorerna väljer du knappen Distribuera till **Azure** nedan och använder informationen i följande tabell för att fylla i **formuläret Anpassad** distribution:

| Fält | Värde |
| ----- | ----- |
| Resursgrupp | `ingress-scenario` |
| Gateway för DNS-etikettprefix | Ett unikt DNS-namn för den här datorn, till exempel `<your name>edgegateway` |
| Dns-etikettprefix nedströms | Ett unikt DNS-namn för den här datorn, till exempel `<your name>downstream` |
| Omfångs-ID | ID-omfånget som du antecknade tidigare |
| Enhets-ID IoT Edge Gateway | `gateway-01` |
| Enhetsnyckel IoT Edge gateway | Det primära nyckelvärde som du antecknade tidigare |
| Autentiseringstyp | Lösenord |
| Administratörslösenord eller nyckel | Ditt val av lösenord för **AzureUser-kontot** på båda virtuella datorerna. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Välj **Granska + skapa** och sedan **Skapa.** Det tar några minuter att skapa de virtuella datorerna i **resursgruppen för ingressscenariot.**

Så här kontrollerar du IoT Edge enheten körs korrekt:

1. Öppna ditt IoT Central program. Gå sedan till **IoT Edge Gateway-enheten** i listan över enheter på **sidan** Enheter.

1. Välj fliken **Moduler** och kontrollera statusen för de tre modulerna. Det tar några minuter för IoT Edge att starta i den virtuella datorn. När den startas är statusen för de tre modulerna **Körs.** Om IoT Edge startar kan du gå till [Felsöka IoT Edge enhet.](../../iot-edge/troubleshoot.md)

För att IoT Edge enhet ska fungera som en gateway behöver den vissa certifikat för att bevisa sin identitet för alla nedströmsenheter. I den här artikeln används democertifikat. Använd certifikat från certifikatutfärdaren i en produktionsmiljö.

Generera democertifikaten och installera dem på din gatewayenhet:

1. Använd SSH för att ansluta till och logga in på din virtuella gatewayenhetsdator. Du hittar DNS-namnet för den här virtuella datorn i Azure Portal. Gå till den **virtuella edgegateway-datorn** i **resursgruppen ingress-scenario.**

    > [!TIP]
    > Du kan behöva öppna port 22 för SSH-åtkomst på båda dina virtuella datorer innan du kan använda SSH för att ansluta från den lokala datorn eller Azure Cloud Shell.

1. Kör följande kommandon för att klona IoT Edge lagringsplatsen och generera dina democertifikat:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    När du har kört föregående kommandon är följande filer redo att användas i nästa steg:

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* – Rotcertifikatutfärdarens certifikat som används för att göra alla andra democertifikat för att testa ett IoT Edge-scenario.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* – Ett CA-enhetscertifikat som refereras från *filen config.yaml.* I ett gatewayscenario är det här CA-certifikatet hur IoT Edge verifierar sin identitet till underordnade enheter.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* – Den privata nyckeln som är associerad med enhetens CA-certifikat.

    Mer information om dessa democertifikat finns i Skapa [democertifikat för att testa IoT Edge-enhetsfunktioner.](../../iot-edge/how-to-create-test-certificates.md)

1. Öppna filen *config.yaml* i en textredigerare. Exempel:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Leta upp `Certificate settings` inställningarna. Avkommentering och ändring av certifikatinställningarna enligt följande:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    Exemplet ovan förutsätter att du är inloggad som **AzureUser** och har skapat en certifikatutfärdare för enheten med namnet "mycacert".

1. Spara ändringarna och starta om IoT Edge körtid:

    ```bash
    sudo systemctl restart iotedge
    ```

Om IoT Edge startar efter ändringarna ändras statusen för $edgeAgent **och** **$edgeHub** till **Körs**. Du kan se dessa statusvärden på **sidan Moduler** för din gatewayenhet i IoT Central.

Om körningen inte startar kontrollerar du de ändringar du har gjort i *config.yaml* och ser [Felsöka din IoT Edge enhet.](../../iot-edge/troubleshoot.md)

### <a name="connect-downstream-device-to-iot-edge-device"></a>Ansluta nedströmsenhet till IoT Edge enhet

Så här ansluter du en underordnad enhet IoT Edge en gatewayenhet:

1. Använd SSH för att ansluta till och logga in på din underordnade enhets virtuella dator. Du hittar DNS-namnet för den här virtuella datorn i Azure Portal. Gå till **den virtuella datorn lövenhet** i **resursgruppen ingress-scenario.**

    > [!TIP]
    > Du kan behöva öppna port 22 för SSH-åtkomst på båda dina virtuella datorer innan du kan använda SSH för att ansluta från den lokala datorn eller Azure Cloud Shell.

1. Kör följande kommando för att klona GitHub-lagringsplatsen med källkoden för den underordnade exempelenheten:

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Kör följande kommandon för att kopiera certifikatet som krävs från `scp` gatewayenheten. Det `scp` här kommandot använder värdnamnet för att identifiera den virtuella `edgegateway` gatewaydatorn. Du uppmanas att ange ditt lösenord:

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. Navigera till *mappen leafdevice* och installera de paket som krävs. Kör sedan `build` skripten `start` och för att etablera och ansluta enheten till gatewayen:

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Ange enhets-ID, omfångs-ID och SAS-nyckel för den nedströmsenhet som du skapade tidigare. För värdnamnet anger du `edgegateway` . Utdata från kommandot ser ut så här:

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Verifiera

Kontrollera att scenariot körs genom att gå till **IoT Edge gatewayenhet** i IoT Central:

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Skärmbild som visar transformerade data på enhetssidan.":::

- Välj **Modules (Moduler).** Kontrollera att de tre IoT Edge **modulerna $edgeAgent**, **$edgeHub** **och transformmodule** körs.
- Välj **Nedströmsenheter** och kontrollera att den underordnade enheten har etablerats.
- Välj **Rådata.** Telemetridata i kolumnen **Omodeled data** ser ut så här:

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

Eftersom IoT Edge enheten transformerar data från nedströmsenheten associeras telemetrin med gatewayenheten i IoT Central. Om du vill visualisera telemetrin skapar du en ny version **IoT Edge en gatewayenhetsmall** med definitioner för telemetrityperna.

## <a name="data-transformation-at-egress"></a>Datatransformering vid utgående

Du kan ansluta dina enheter till IoT Central, exportera enhetsdata till en beräkningsmotor för att transformera dem och sedan skicka transformerade data tillbaka till IoT Central för enhetshantering och analys. Exempel:

- Dina enheter skickar platsdata till IoT Central.
- IoT Central exporterar data till en beräkningsmotor som förbättrar platsdata med väderinformation.
- Beräkningsmotorn skickar tillbaka förbättrade data till IoT Central.

Du kan använda den [IoT Central enhetsbryggan som](https://github.com/Azure/iotc-device-bridge) beräkningsmotor för att transformera data som exporteras från IoT Central.

En fördel med att transformera data vid utgående data är att dina enheter ansluter direkt till IoT Central, vilket gör det enkelt att skicka kommandon till enheter eller uppdatera enhetsegenskaper. Med den här metoden kan du dock använda fler meddelanden än din månatliga tilldelning och öka kostnaden för att använda Azure IoT Central.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>Använda IoT Central för att transformera enhetsdata

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="Datatransformering vid utgående IoT Edge" border="false":::

I det här scenariot omvandlar en beräkningsmotor enhetsdata som exporterats från IoT Central innan de skickas tillbaka till IoT Central program. På en hög nivå är stegen för att konfigurera det här scenariot:

1. **Konfigurera beräkningsmotorn:** Skapa en IoT Central som fungerar som beräkningsmotor för datatransformering.

1. **Transformera enhetsdata i enhetsbryggan:** Transformera data i enhetsbryggan genom att ändra funktionskoden för enhetsbryggan för ditt användningsfall för datatransformering.

1. **Aktivera dataflöde från IoT Central till enhetsbryggan:** Exportera data från IoT Central till enhetsbryggan för transformering. Vidarebefordra sedan transformerade data tillbaka till IoT Central. När du skapar dataexporten använder du filter för meddelandeegenskap för att endast exportera otransformerade data.

1. **Kontrollera:** Anslut enheten till IoT Central appen och sök efter både rådata och transformerade data i IoT Central.

<!-- To Do - doesn't the device send JSON data? -->
I exemplet som beskrivs i följande avsnitt skickar enheten CSV-data i följande format till IoT Edge gatewayenheten:

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

Du kan använda enhetsbryggan för att transformera enhetsdata genom att:

- Ändra temperaturenheten från centigrade till fahrenheit.
- Berika enhetsdata med väderdata som hämtas från [Open Weather-tjänsten](https://openweathermap.org/) för latitud- och longitudvärdena.

Enhetsbryggan skickar sedan transformerade data till IoT Central i följande format:

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

Följande steg visar hur du konfigurerar det här scenariot:

### <a name="retrieve-your-iot-central-connection-settings"></a>Hämta IoT Central anslutningsinställningar

Innan du ställer in det här scenariot måste du hämta några anslutningsinställningar från ditt IoT Central program:

1. Logga in på ditt IoT Central program.

1. Gå till **Administration > Enhetsanslutning**.

1. Anteckna **ID-omfånget**. Du använder det här värdet senare.

1. Välj **registreringsgruppen SaS-IoT-Devices.** Anteckna primärnyckeln för signatur för delad åtkomst. Du använder det här värdet senare.

### <a name="set-up-a-compute-engine"></a>Konfigurera en beräkningsmotor

I det här scenariot används Azure Functions distribution som IoT Central enhetsbryggan. Om du vill distribuera enhetsbryggan väljer **du knappen** Distribuera till Azure nedan och använder informationen i följande tabell för att fylla i **formuläret Anpassad** distribution:

| Fält | Värde |
| ----- | ----- |
| Resursgrupp | Skapa en ny resursgrupp med namnet `egress-scenario` |
| Region | Välj den region som är närmast dig. |
| Omfångs-ID | Använd **ID-omfånget** som du antecknade tidigare. |
| IoT Central SAS-nyckel | Använd primärnyckeln för signatur för delad åtkomst för **registreringsgruppen SaS-IoT-Devices.** Du antecknade det här värdet tidigare. |

[ ![ Distribuera till Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json).

Välj **Granska + skapa** och sedan **Skapa.** Det tar några minuter att skapa Azure-funktionen och relaterade resurser i **resursgruppen egress-scenario.**

### <a name="transform-device-data-in-the-device-bridge"></a>Transformera enhetsdata i enhetsbryggan

Så här konfigurerar du enhetsbryggan för att transformera exporterade enhetsdata:

1. Hämta en program-API-nyckel från Open tjänsten Väder. Ett konto är kostnadsfritt med begränsad användning av tjänsten. Skapa en API-nyckel för programmet genom att skapa ett konto [i Öppna tjänsten Väder-portalen](https://openweathermap.org/) och följa instruktionerna. Du använder nyckeln för Open Weather-API:et senare.

1. I Azure Portal du till Funktionsapp i **resursgruppen egress-scenario.**

1. I det vänstra navigeringsfönstret i **Utvecklingsverktyg** väljer **du App Service Editor (förhandsversion)**.

1. Välj **&rarr; Gå** för att öppna **App Service Editor** sidan. Gör följande ändringar:

    1. Öppna *filen wwwroot/IoTCIntegration/index.js.* Ersätt all kod i den här filen med koden i [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js).

    1. I den nya *index.js* variabelfilen `openWeatherAppId` med den Open Weather API-nyckel som du fick tidigare.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. Lägg till en meddelandeegenskap i de data som skickas av funktionen till IoT Central. IoT Central använder den här egenskapen för att förhindra export av transformerade data. Om du vill göra den här ändringen öppnar *du filen wwwroot/IoTCIntegration/lib/engine.js.* Leta upp följande kod:

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Lägg till följande kod precis efter koden i föregående kodavsnitt:

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Som referens kan du visa ett [](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js) slutfört exempel påengine.jsfil.

1. I den **App Service Editor** väljer du **Konsol i** det vänstra navigeringsfönstret. Installera de nödvändiga paketen genom att köra följande kommandon:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Det kan ett par minuter att köra kommandot.

1. Gå tillbaka till **översiktssidan för Azure-funktionen** och starta om funktionen:

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="Starta om funktionen":::

1. Välj **Funktioner** i det vänstra navigeringsfältet. Välj sedan **IoTCIntegration**. Välj **Kod + testa**.

1. Anteckna funktions-URL:en. Du behöver det här värdet senare:

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="Hämta funktions-URL:en":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>Aktivera dataflöde från IoT Central till enhetsbryggan

I det här avsnittet beskrivs hur du ställer in Azure IoT Central program.

Spara först [enhetsmodellfilen](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) på den lokala datorn.

Om du vill lägga till en enhetsmall IoT Central ditt program navigerar du till IoT Central program och gör sedan följande:

1. Logga in på IoT Central program och gå till **sidan Enhetsmallar.**

1. Välj **+ Ny,** välj **IoT-enhet,** **välj Nästa: Anpassa,** ange *Beräkningsmodell* som mallnamn. Välj **Nästa: Granskning**. Välj sedan **Skapa**.

1. Välj **Importera en modell** och bläddra till denmodel.js *fil* som du laddade ned tidigare.

1. När modellen har importerats väljer du **Publicera för** att publicera **enhetsmallen för** beräkningsmodellen.

Så här ställer du in dataexporten för att skicka data till enhetsbryggan:

1. I ditt IoT Central väljer du **Dataexport**.

1. Välj **+ Nytt mål för** att skapa ett mål som ska användas med enhetsbryggan. Anropa *compute-målfunktionen* för **Måltyp och** välj **Webhook**. Som motringning-URL väljer du klistra in funktions-URL:en som du antecknade tidigare. Lämna **Auktorisering** **som Ingen autentisering.**

1. Spara ändringarna.

1. Välj **+ Ny export och** skapa en dataexport med namnet *Compute export*.

1. Lägg till ett filter för att endast exportera enhetsdata för den enhetsmall som du använder. Välj **+ Filtrera,** välj objektet **Enhetsmall,** välj operatorn **Är lika** med och välj sedan **enhetsmallen Beräkningsmodell** som du nyss skapade.

1. Lägg till ett meddelandefilter för att skilja mellan transformerade och otransformerade data. Det här filtret förhindrar att omvandlade värden skickas tillbaka till enhetsbryggan. Välj **+ Meddelandeegenskapsfilter** och ange det *namnvärde* som beräknas och välj sedan **operatorn Finns inte.** Strängen `computed` används som ett nyckelord i exempelkoden för enhetsbryggan.

1. Som mål väljer du **beräkningsfunktionens mål** som du skapade tidigare.

1. Spara ändringarna. Efter någon minut visas **exportstatusen** som **Felfri.**

### <a name="verify"></a>Verifiera

Exempelenheten som du använder för att testa scenariot skrivs i Node.js. Kontrollera att du har Node.js och NPM installerat på den lokala datorn. Om du inte vill installera de här förutsättningarna använder du Azure Cloud Shell[som](https://shell.azure.com/) har dem förinstallerade.

Så här kör du en exempelenhet som testar scenariot:

1. Klona GitHub-lagringsplatsen som innehåller exempelkoden och kör följande kommando:

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Om du vill ansluta exempelenheten till IoT Central-programmet redigerar du anslutningsinställningarna *i filen iot-central-compute/device/device.js.* Ersätt omfångs-ID:t och SAS-nyckeln med de värden som du antecknade tidigare:

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Spara ändringarna.

1. Använd följande kommandon för att installera de nödvändiga paketen och köra enheten:

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. Resultatet av det här kommandot ser ut som följande utdata:

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. I ditt IoT Central program navigerar du till enheten som heter **computeDevice**. I vyn **Rådata** finns det två olika telemetriströmmar som visas ungefär var femte sekund. Dataströmmen med omodellerade data är den ursprungliga telemetrin, dataströmmen med modellerade data är de data som funktionen omvandlade:

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Skärmbild som visar ursprungliga och omvandlade rådata.":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver de Azure-resurser som du skapade när du följde stegen i den här guiden tar du bort resursgrupperna [i Azure Portal](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups).

De två resursgrupper som du använde i den här guiden är **ingress-scenario** **och egress-scenario**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de olika alternativen för att transformera enhetsdata för IoT Central, både vid ingress och egress. Artikeln innehåller genomgångar för två specifika scenarier:

- Använd en IoT Edge modul för att transformera data från nedströmsenheter innan data skickas till ditt IoT Central program.
- Använd Azure Functions att transformera data utanför IoT Central. I det här scenariot IoT Central en dataexport för att skicka inkommande data till en Azure-funktion som ska transformeras. Funktionen skickar transformerade data tillbaka till ditt IoT Central program.

Nu när du har lärt dig hur du transformerar enhetsdata utanför ditt Azure IoT Central-program kan du lära dig hur du använder analyser för att analysera [enhetsdata i IoT Central](howto-create-analytics.md).
