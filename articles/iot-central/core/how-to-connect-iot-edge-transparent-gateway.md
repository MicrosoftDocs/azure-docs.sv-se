---
title: Ansluta en IoT Edge transparent Gateway till ett Azure IoT Central-program
description: Ansluta enheter via en IoT Edge transparent Gateway till ett IoT Central-program
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 4e88ad58c7baba1c66c30df3f4effdbf11371c18
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045339"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Ansluta enheter via en IoT Edge transparent Gateway

En IoT Edge enhet kan fungera som en gateway som ger en anslutning mellan andra enheter i ett lokalt nätverk och ditt IoT Central-program. Du använder en gateway när enheten inte kan komma åt ditt IoT Central program direkt.

IoT Edge stöder de [ *genomskinliga* och *översättnings* -gatewayens mönster](../../iot-edge/iot-edge-as-gateway.md). I den här artikeln sammanfattas hur du implementerar mönstret för transparent Gateway. I det här mönstret skickar gatewayen meddelanden från den underordnade enheten till IoT Hub slut punkten i IoT Central programmet.

Den här artikeln använder virtuella datorer som värd för den underordnade enheten och gatewayen. I ett verkligt scenario körs den underordnade enheten och gatewayen på fysiska enheter i det lokala nätverket.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här självstudien behöver du en aktiv Azure-prenumeration.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Slutför snabb starten [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) för att skapa ett IoT Central program med hjälp av den **anpassade appen > anpassade program** .

Följ anvisningarna i den här artikeln genom att ladda ned följande filer till datorn:

- [Enhets modell för termostat](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Manifest för transparent Gateway](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Lägga till enhetsspecifika mallar

Både de efterföljande enheterna och gateway-enheten kräver enhets mallar i IoT Central. Med IoT Central kan du utforma relationen mellan dina underordnade enheter och din gateway så att du kan visa och hantera dem när de är anslutna.

Skapa en enhets mall för en underordnad enhet genom att skapa en standardmall för enheter som modellerar enhetens funktioner. Exemplet som visas i den här artikeln använder enhets modellen termostat.

Så här skapar du en enhets mal len för en underordnad enhet:

1. Skapa en enhets mall och välj **IoT-enhet** som Malltyp.

1. På sidan **Anpassa** i guiden anger du ett namn, till exempel *termostat* för enhets mal len.

1. När du har skapat enhets mal len väljer du **Importera en modell**. Välj en modell som *thermostat-1.jspå* filen som du laddade ned tidigare.

1. Om du vill generera några standardvyer för termostat väljer du vyer och sedan **generera standardvyer**.

1. Publicera enhets mal len.

Så här skapar du en enhets mall för en transparent IoT Edge Gateway:

1. Skapa en enhets mall och välj **Azure IoT Edge** som Malltyp.

1. På sidan **Anpassa** i guiden anger du ett namn som till exempel *Edge Gateway* för enhets mal len.

1. På sidan **Anpassa** i guiden kontrollerar du Gateway- **enheten med underordnade enheter**.

1. På sidan **Anpassa** i guiden väljer du **Bläddra**. Ladda upp *EdgeTransparentGatewayManifest.js* filen som du laddade ned tidigare.

1. Lägg till en post i **relationer** till den underordnade enhets mal len.

Följande skärm bild visar sidan **relationer** för en IoT Edge gateway-enhet som har underordnade enheter som använder **termostat** enhets mal len:

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Skärm bild som visar IoT Edge gateway-enhet mal len relation med en termostat underordnad enhets mall.":::

Den föregående skärm bilden visar en IoT Edge Gateway-enhets mal len utan definierade moduler. En transparent Gateway kräver inte några moduler eftersom IoT Edge runtime vidarebefordrar meddelanden från efterföljande enheter till IoT Central. Om själva gatewayen behöver skicka telemetri, synkronisera egenskaper eller hantera kommandon kan du definiera dessa funktioner i standard komponenten eller i en modul.

Lägg till eventuella obligatoriska moln egenskaper och vyer innan du publicerar gateway och underordnad enhets mallar.

## <a name="add-the-devices"></a>Lägg till enheterna

När du lägger till enheterna i ditt IoT Central-program kan du definiera förhållandet mellan de efterföljande enheterna och den transparenta gatewayen.

Så här lägger du till enheterna:

1. Gå till sidan enheter i IoT Central programmet.

1. Lägg till en instans av den transparenta Gateway-IoT Edge enheten. I den här artikeln är gatewayens enhets-ID `edgegateway` .

1. Lägg till en eller flera instanser av den underordnade enheten. I den här artikeln är de underordnade enheterna termostater med ID: n `thermostat1` och `thermostat2` .

1. I enhets listan väljer du varje underordnad enhet och väljer **Anslut till gateway**.

Följande skärm bild visar en lista över enheter som är anslutna till en gateway på sidan **efterföljande enheter** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Skärm bild som visar listan över underordnade enheter som är anslutna till en transparent Gateway.":::

I en transparent Gateway ansluter de underordnade enheterna till själva gatewayen, inte till en anpassad modul som finns i gatewayen.

Innan du distribuerar enheterna behöver du:

- IoT Central programmets **ID-omfång** .
- **Enhets-ID-** värden för gatewayen och underordnade enheter.
- **Primär nyckel** värden för gateway och underordnade enheter.

Du hittar de här värdena genom att gå till varje enhet i enhets listan och välja **Anslut**. Anteckna dessa värden innan du fortsätter.

## <a name="deploy-the-gateway-and-devices"></a>Distribuera gatewayen och enheterna

För att du ska kunna testa det här scenariot visar följande steg hur du distribuerar gatewayen och underordnade enheter till virtuella Azure-datorer. I ett verkligt scenario körs den underordnade enheten och gatewayen på fysiska enheter i det lokala nätverket.

Om du vill testa scenariot med transparent Gateway väljer du följande knapp för att distribuera två virtuella Linux-datorer. En virtuell dator är en transparent IoT Edge Gateway, den andra är en underordnad enhet som simulerar en termostat:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

När de två virtuella datorerna distribueras och körs kontrollerar du att den IoT Edge gateway-enheten körs på den `edgegateway` virtuella datorn:

1. Gå till sidan **enheter** i IoT Central programmet. Om IoT Edge gateway-enheten är ansluten till IoT Central, är dess status **etablerad**.

1. Öppna IoT Edge gateway-enheten och kontrol lera status för modulerna på sidan **moduler** . Om IoT Edge Runtime har startats, **körs** **$edgeAgent** -och **$edgeHub** -modulerna:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Skärm bild som visar $edgeAgent och $edgeHub moduler som körs på IoT Edge Gateway.":::

> [!TIP]
> Du kan behöva vänta några minuter medan den virtuella datorn startar och enheten tillhandahålls i IoT Central-programmet.

## <a name="configure-the-gateway"></a>Konfigurera gatewayen

För att din IoT Edge-enhet ska fungera som en transparent Gateway behöver den vissa certifikat för att bevisa sin identitet för underordnade enheter. I den här artikeln används demo certifikat. Använd certifikat från certifikat utfärdaren i en produktions miljö.

Så här genererar du demonstrations certifikaten och installerar dem på din gateway-enhet:

1. Använd SSH för att ansluta till och logga in på din virtuella dator för gateway-enheten.

1. Kör följande kommandon för att klona IoT Edge-lagringsplatsen och generera dina demonstrations certifikat:

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

    När du har kört föregående kommandon är följande filer klara att användas i nästa steg:

    - *~/certs/certs/Azure-IoT-test-Only.root.ca.cert.pem* – rot certifikat utfärdarens certifikat som används för att göra alla andra demonstrations certifikat för att testa ett IoT Edge scenario.
    - *~/certs/certs/IoT-Edge-Device-mycacert-full-Chain.cert.pem* – ett enhets certifikat som refereras till i filen *config. yaml* . I ett Gateway-scenario är det här CA-certifikatet hur IoT Edge-enheten verifierar identiteten för underordnade enheter.
    - *~/certs/Private/IoT-Edge-Device-mycacert.Key.pem* – den privata nyckeln som är kopplad till ENHETens CA-certifikat.

    Mer information om dessa demo certifikat finns i [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](../../iot-edge/how-to-create-test-certificates.md).

1. Öppna filen *config. yaml* i en text redigerare. Exempel:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Leta upp `Certificate settings` inställningarna. Ta bort kommentaren och ändra certifikat inställningarna enligt följande:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    Exemplet som visas ovan förutsätter att du är inloggad som **AzureUser** och skapat en enhets certifikat utfärdare med namnet "mycacert".

1. Spara ändringarna och starta om IoT Edge Runtime:

    ```bash
    sudo systemctl restart iotedge
    ```

Om IoT Edge runtime startar efter dina ändringar ändras status för **$edgeAgent** -och **$edgeHub** -modulerna till som **körs** på sidan **moduler** för din gateway-enhet i IoT Central.

Om körningen inte startar, kontrol lera de ändringar du har gjort i *config. yaml* och se [Felsöka din IoT Edge-enhet](../../iot-edge/troubleshoot.md).

Din transparenta Gateway har nu kon figurer ATS och är redo att börja vidarebefordra telemetri från efterföljande enheter.

## <a name="provision-a-downstream-device"></a>Etablera en underordnad enhet

IoT Edge kan för närvarande inte automatiskt etablera en underordnad enhet till ditt IoT Central-program. Följande steg visar hur du etablerar `thermostat1` enheten. För att utföra de här stegen behöver du en miljö med python 3,6 (eller högre) installerat och Internet anslutning. [Azure Cloud Shell](https://shell.azure.com/) har python 3,7 förinstallerat:

1. Kör följande kommando för att installera `azure.iot.device` modulen:

    ```bash
    pip install azure.iot.device
    ```

1. Kör följande kommando för att ladda ned python-skriptet som utför etableringen:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Om du vill etablera den `thermostat1` underordnade enheten i ditt IoT Central program kör du följande kommandon och ersätter `{your application id scope}` och `{your device primary key}`  :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

I ditt IoT Central-program kontrollerar du att **enhets statusen** för thermostat1-enheten nu har **tillhandahållits**. 

## <a name="configure-a-downstream-device"></a>Konfigurera en underordnad enhet

I föregående avsnitt konfigurerade du den `edgegateway` virtuella datorn med demonstrations certifikaten så att den kan köras som gateway. Den `leafdevice` virtuella datorn är redo att installera en termostat-simulator som använder gatewayen för att ansluta till IoT Central.

Den `leafdevice` virtuella datorn måste ha en kopia av rot certifikat utfärdarens certifikat som du skapade på den `edgegateway` virtuella datorn. Kopiera */Home/AzureUser/certs/certs/Azure-IoT-test-Only.root.ca.cert.pem* -filen från den `edgegateway` virtuella datorn till din hem katalog på den `leafdevice` virtuella datorn. Du kan använda **SCP** -kommandot för att kopiera filer till och från en virtuell Linux-dator.

Information om hur du kontrollerar anslutningen från den underordnade enheten till gatewayen finns i [testa Gateway-anslutningen](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection).

Så här kör du termostat-simulatorn på den `leafdevice` virtuella datorn:

1. Hämta python-exemplet till din hem katalog:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Installera python-modulen för Azure IoT Device:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Ställ in miljövariabler för att konfigurera exemplet. Ersätt `{your device shared key}` med den primära nyckeln i `thermostat1` som du antecknade tidigare. De här variablerna förutsätter namnet på den virtuella Gateway `edgegateway` -datorn och ID: t för termostat-enheten `thermostat1` :

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Observera hur anslutnings strängen använder namnet på gateway-enheten och inte namnet på en IoT-hubb.

1. Om du vill köra koden använder du följande kommando:

    ```bash
    python3 simple_thermostat.py
    ```

    Utdata från det här kommandot ser ut så här:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. Om du vill se telemetri i IoT Central går du till **översikts** sidan för **thermostat1** -enheten:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Skärm bild som visar telemetri från den underordnade enheten.":::

    På sidan **om** kan du Visa egenskaps värden som skickas från den underordnade enheten och på **kommando** sidan kan du anropa kommandon på den underordnade enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar en transparent Gateway med IoT Central, är det föreslagna nästa steg att lära dig mer om [IoT Edge](../../iot-edge/about-iot-edge.md).
