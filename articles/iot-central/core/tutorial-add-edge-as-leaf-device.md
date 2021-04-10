---
title: Självstudie – Lägg till en Azure IoT Edge enhet i Azure IoT Central | Microsoft Docs
description: Självstudie – som en operatör lägger du till en Azure IoT Edge enhet till ditt Azure IoT Central-program
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 373d144b4df818a075f0088e9cbf31cb5027e747
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724888"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Självstudie: Lägg till en Azure IoT Edge enhet till ditt Azure IoT Central-program

*Den här artikeln gäller operatörer, Solution Builders och enhets utvecklare.*

Den här självstudien visar hur du konfigurerar och lägger till en Azure IoT Edge-enhet i Azure IoT Central-programmet. Självstudien använder en IoT Edge-aktiverad virtuell Linux-dator (VM) för att simulera en IoT Edge enhet. Den IoT Edge enheten använder en modul som genererar simulerad miljö telemetri. Du visar Telemetrin på en instrument panel i ditt IoT Central-program.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en enhets mall för en IoT Edge enhet
> * Skapa en IoT Edge enhet i IoT Central
> * Distribuera en simulerad IoT Edge enhet till en virtuell Linux-dator

## <a name="prerequisites"></a>Förutsättningar

Slutför snabb starten [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) för att skapa ett IoT Central program med hjälp av den **anpassade appen > anpassade program** .

För att slutföra stegen i den här självstudien behöver du en aktiv Azure-prenumeration.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Hämta IoT Edge manifest filen från GitHub. Högerklicka på följande länk och välj sedan **Spara länk som**: [EnvironmentalSensorManifest.jspå](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Skapa enhets mall

I det här avsnittet skapar du en mall för IoT Central enhet för en IoT Edge enhet. Du importerar ett IoT Edge manifest för att komma igång och sedan ändra mallen för att lägga till mallar för telemetri och vyer:

### <a name="import-manifest-to-create-template"></a>Importera manifestet för att skapa mallen

Så här skapar du en enhets mal len från ett IoT Edge manifest:

1. I ditt IoT Central program, navigerar du till **enhets mallar** och väljer **+ ny**.

1. På sidan **Välj Malltyp** väljer du panelen **Azure IoT Edge** . Välj sedan **Nästa: anpassa**.

1. På sidan **överför ett distributions manifest för ett Azure IoT Edge** *anger du enhets* mal len namn. Välj sedan **Bläddra** för att ladda upp **EnvironmentalSensorManifest.jspå** som du laddade ned tidigare. Välj sedan **Nästa: granska**.

1. På sidan **Granska** väljer du **skapa**.

1. Välj **Hantera** -gränssnittet i **SimulatedTemperatureSensor** -modulen för att visa de två egenskaperna som definierats i manifestet:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="Enhets mal len skapades från IoT Edge manifest":::

> [!TIP]
> Det här distributions manifestet hämtar modulblad från en Azure Container Registry-lagringsplats som inte kräver några autentiseringsuppgifter för att ansluta. Om du vill använda modulblad från ett privat lager, anger du autentiseringsuppgifterna för behållar registret i manifestet.

### <a name="add-telemetry-to-manifest"></a>Lägg till telemetri i manifestet

Ett IoT Edge manifest definierar inte Telemetrin som en modul skickar. Du lägger till telemetri-definitionerna i enhets mal len i IoT Central. **SimulatedTemperatureSensor** -modulen skickar telemetridata som ser ut som följande JSON:

```json
{
  "machine": {
    "temperature": 75.0,
    "pressure": 40.2
  },
  "ambient": {
    "temperature": 23.0,
    "humidity": 30.0
  },
  "timeCreated": ""
}
```

Lägga till telemetri-definitionerna i enhets mal len:

1. Välj **Hantera** gränssnitt i enhets mal len för **miljö sensor gränsen** .

1. Välj **+ Lägg till funktion**. Ange *datorn* som **visnings namn** och se till att **funktions typen** är **telemetri**.

1. Välj **objekt** som schema typ och välj sedan **definiera**. På sidan objekt definition lägger du till *temperatur* och *tryck* som attribut av typen **Double** och väljer sedan **Använd**.

1. Välj **+ Lägg till funktion**. Ange *omgivande* som **visnings namn** och se till att **funktions typen** är **telemetri**.

1. Välj **objekt** som schema typ och välj sedan **definiera**. På sidan objekt definition lägger du till *temperatur* och *fuktighet* som attribut av typen **Double** och väljer sedan **Använd**.

1. Välj **+ Lägg till funktion**. Ange *timeCreated* som **visnings namn** och se till att **funktions typen** är **telemetri**.

1. Välj **datetime** som schema typ.

1. Välj **Spara** för att uppdatera mallen.

**Hanterings** gränssnittet innehåller nu typerna **Machine**, **omgivande** och **timeCreated** telemetri:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="Gränssnitt med typer av dator och omgivande telemetri":::

### <a name="add-views-to-template"></a>Lägg till vyer i mallen

Enhets mal len har ännu ingen vy som låter en operatör se Telemetrin från den IoT Edge enheten. Så här lägger du till en vy i enhets mal len:

1. Välj **vyer** i enhets mal len för **miljö sensor gränsen** .

1. På sidan **Välj att lägga till en ny vy** väljer du den **visualisera enhets** panelen.

1. Ändra vyns namn om du vill *visa IoT Edge enhetens telemetri*.

1. Välj typer av telemetri för **omgivning** och **datorer** . Välj sedan **Lägg till panel**.

1. Klicka på **Spara** för att spara vyn **IoT Edge enhets telemetri** .

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="Enhets mal len med vyn telemetri":::

### <a name="publish-the-template"></a>Publicera mallen

Innan du kan lägga till en enhet som använder **enhets mal len miljö sensor gräns** måste du publicera mallen.

Navigera till enhets mal len för **miljö sensor** och välj **publicera**. I **mallen publicera den här enheten i program** panelen väljer du **publicera** för att publicera mallen:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="Publicera enhetsmallen":::

## <a name="add-iot-edge-device"></a>Lägga till IoT Edge-enhet

Nu har du publicerat mallen för **miljö sensor gräns** , du kan lägga till en enhet till ditt IoT Central-program:

1. I ditt IoT Central-program går du till sidan **enheter** och väljer **miljö sensor gräns enhet** i listan över tillgängliga mallar.

1. Välj **+ ny** för att lägga till en ny enhet från mallen. På sidan **Skapa ny enhet** väljer du **skapa**.

Nu har du en ny enhet med statusen **registrerad**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="Ny, registrerad enhet":::

### <a name="get-the-device-credentials"></a>Hämta autentiseringsuppgifter för enheten

När du distribuerar den IoT Edge enheten senare i den här självstudien behöver du de autentiseringsuppgifter som tillåter att enheten ansluter till ditt IoT Central-program. Hämta autentiseringsuppgifterna för enheten:

1. På sidan **enhet** väljer du den enhet som du skapade.

1. Välj **Anslut**.

1. På sidan **enhets anslutning** noterar du **ID-omfånget**, **enhets-ID: t** och den **primära nyckeln**. Du använder dessa värden senare.

1. Välj **Stäng**.

Nu har du slutfört konfigurationen av IoT Central-programmet så att en IoT Edge-enhet kan ansluta.

## <a name="deploy-an-iot-edge-device"></a>Distribuera en IoT Edge-enhet

I den här självstudien använder du en Azure IoT Edge-aktiverad Linux-baserad virtuell dator som skapats på Azure för att simulera en IoT Edge-enhet. Skapa den IoT Edge-aktiverade virtuella datorn i din Azure-prenumeration genom att klicka på:

[![Knappen Distribuera till Azure för iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

På sidan **Anpassad distribution** :

1. Välj din Azure-prenumeration.

1. Välj **Skapa ny** för att skapa en ny resurs grupp med namnet *Central-Edge-RG*.

1. Välj en region nära dig.

1. Lägg till ett unikt **prefix för DNS-etikett** , till exempel *contoso-Central-Edge*.

1. Välj ett administratörs användar namn för den virtuella datorn.

1. Ange *Temp* som anslutnings sträng. Senare konfigurerar du enheten för att ansluta med DPS.

1. Acceptera standardvärdena för VM-storlek, Ubuntu-version och plats.

1. Välj **lösen ord** som autentiseringstyp.

1. Ange ett lösen ord för den virtuella datorn.

1. Välj sedan **Granska + skapa**.

1. Granska dina val och välj sedan **skapa**:

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="Skapa en IoT Edge virtuell dator":::

Det tar några minuter att slutföra distributionen. När distributionen är klar navigerar du till resurs gruppen **Central-RG** i Azure Portal.

### <a name="configure-the-iot-edge-vm"></a>Konfigurera den virtuella datorn IoT Edge

Så här konfigurerar du IoT Edge i den virtuella datorn att använda DPS för att registrera och ansluta till ditt IoT Central-program:

1. I resurs gruppen **contoso-Edge-RG** väljer du den virtuella dator instansen.

1. I avsnittet **support och fel sökning** väljer du **seriell konsol**. Om du uppmanas att konfigurera startdiagnostik följer du anvisningarna i portalen.

1. Tryck på **RETUR** för att se `login:` prompten. Logga in genom att ange ditt användar namn och lösen ord.

1. Kör följande kommando för att kontrol lera IoT Edge runtime-versionen. Vid tidpunkten för skrivning är versionen 1.0.9.1:

    ```bash
    sudo iotedge --version
    ```

1. Använd `nano` Redigeraren för att öppna filen IoT Edge config. yaml:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Rulla nedåt tills du ser `# Manual provisioning configuration` . Kommentera ut följande tre rader som du ser i följande kodfragment:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Rulla nedåt tills du ser `# DPS symmetric key provisioning configuration` . Ta bort kommentaren till de efterföljande åtta raderna som visas i följande kodfragment:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

    > [!TIP]
    > Se till att det inte finns något utrymme kvar framför `provisioning:`

1. Ersätt `{scope_id}` med **ID-omfånget** som du antecknade tidigare.

1. Ersätt `{registration_id}` med **enhets-ID: t** som du antecknade tidigare.

1. Ersätt `{symmetric_key}` med den **primära nyckeln** som du antecknade tidigare.

1. Spara ändringarna (**CTRL-O**) och avsluta (**CTRL-X**) `nano` redigeraren.

1. Kör följande kommando för att starta om IoT Edge daemon:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Kör följande kommando för att kontrol lera status för IoT Edge moduler:

    ```bash
    iotedge list
    ```

    Följande exempel på utdata visar de moduler som körs:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > Du kan behöva vänta tills alla moduler börjar köras.

## <a name="view-the-telemetry"></a>Visa telemetrin

Den simulerade IoT Edge enheten körs nu på den virtuella datorn. I ditt IoT Central-program är enhets statusen nu **etablerad** på sidan **enheter** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="Etablerade IoT Edge enhet":::

Du kan se Telemetrin från enheten på sidan **visa IoT Edge enhets telemetri** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="Telemetri för enhet":::

Sidan **moduler** visar status för IoT Edge modulerna på enheten:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="Status för enhets modul":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med den virtuella datorn IoT Edge kan du behålla och återanvända de resurser som du har använt i den här självstudien. Annars kan du ta bort de resurser du skapade i den här självstudien för att undvika ytterligare kostnader:

* Om du vill ta bort IoT Edge VM och dess tillhör ande resurser tar du bort resurs gruppen **contoso-Edge-RG** i Azure Portal.
* Om du vill ta bort IoT Central programmet går du till sidan **program** i avsnittet **Administration** i programmet och väljer **ta bort**.

Som en lösnings utvecklare eller operatör, nu när du har lärt dig hur du arbetar med och hanterar IoT Edge enheter i IoT Central, är ett föreslaget nästa steg att:

> [!div class="nextstepaction"]
> [Använda enhets grupper för att analysera enhets telemetri](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>Nästa steg

Som enhets utvecklare är det nu viktigt att du har lärt dig hur du arbetar med och hanterar IoT Edge enheter i IoT Central. ett förslag till nästa steg är att läsa:

> [!div class="nextstepaction"]
> [Utveckla IoT Edge moduler](../../iot-edge/tutorial-develop-for-linux.md)