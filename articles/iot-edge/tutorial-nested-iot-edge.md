---
title: Självstudie – Skapa en hierarki med IoT Edge enheter – Azure IoT Edge
description: Den här självstudien visar hur du skapar en hierarkisk struktur med IoT Edge enheter med hjälp av gatewayer.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: bfecc88dc0c504cee615f1a3d35f9208aeb724f8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309244"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Självstudie: skapa en hierarki med IoT Edge enheter

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Distribuera Azure IoT Edge noder mellan nätverk som är ordnade i hierarkiska lager. Varje lager i en hierarki är en gateway-enhet som hanterar meddelanden och begär Anden från enheter i lagret under den.

Du kan strukturera en hierarki med enheter så att endast det översta lagret har anslutning till molnet och de nedre lagren kan bara kommunicera med intilliggande Nord-och syd-lager. Den här nätverks skikten är grunden i de flesta industriella nätverk, som följer [ISA-95-standarden](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Målet med den här självstudien är att skapa en hierarki med IoT Edge enheter som simulerar en förenklad produktions miljö. I slutet kommer du att distribuera den [simulerade temperatur sensor modulen](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) till en enhet med lägre lager utan Internet åtkomst genom att hämta behållar avbildningar via hierarkin.

För att uppnå det här målet vägleder dig genom den här självstudien genom att skapa en hierarki med IoT Edge enheter, distribuera IoT Edge runtime-behållare till dina enheter och konfigurera enheterna lokalt. I den här självstudien använder du ett automatiserat konfigurations verktyg för att:

> [!div class="checklist"]
>
> * Skapa och definiera relationer i en hierarki med IoT Edge enheter.
> * Konfigurera IoT Edge runtime på enheterna i hierarkin.
> * Installera konsekventa certifikat i din enhets hierarki.
> * Lägg till arbets belastningar till enheterna i hierarkin.
> * Använd [modulen IoT Edge-API-proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) för att på ett säkert sätt dirigera http-trafik över en enskild port från dina enheter med lägre lager.

>[!TIP]
>Den här självstudien innehåller en blandning av manuella och automatiserade steg för att tillhandahålla en demonstration av kapslade IoT Edge-funktioner.
>
>Om du vill ha en helt automatiserad titt på hur du konfigurerar en hierarki med IoT Edge enheter kan du följa exemplet med skript [Azure IoT Edge för industriella IoT](https://aka.ms/iotedge-nested-sample). Det här skript scenariot distribuerar virtuella Azure-datorer som förkonfigurerade enheter för att simulera en fabriks miljö.
>
>Om du vill ha en djupgående titt på de manuella stegen för att skapa och hantera en hierarki med IoT Edge enheter kan du läsa [instruktionen så här hittar du på IoT Edge enhets-Gateway-hierarkier](how-to-connect-downstream-iot-edge-device.md).

I den här självstudien definieras följande nätverks skikt:

* **Översta lagret**: IoT Edge enheter i det här skiktet kan ansluta direkt till molnet.

* **Lägre lager**: IoT Edge enheter på lager under det översta lagret kan inte ansluta direkt till molnet. De måste gå igenom en eller flera mellanliggande IoT Edge enheter för att kunna skicka och ta emot data.

I den här självstudien används två hierarkier för enkelhetens skull, som visas nedan. En enhet, den **översta lager enheten**, representerar en enhet i hierarkins övre skikt, som kan ansluta direkt till molnet. Enheten kommer även att kallas för den **överordnade enheten**. Den andra enheten, den **lägre lager enheten**, representerar en enhet i den nedre nivån i hierarkin, som inte kan ansluta direkt till molnet. Du kan lägga till fler lägre lager enheter för att representera produktions miljön efter behov. Enheter på lägre lager kallas även **underordnade enheter**.

![Strukturen för kursens hierarki, som innehåller två enheter: det översta lager enheten och den lägre lager enheten](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en hierarki med IoT Edge enheter behöver du:

* En dator (Windows eller Linux) med Internet anslutning.
* Ett Azure-konto med en giltig prenumeration. Om du inte har en [Azure-prenumeration](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En kostnads fri eller standard nivå [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) i Azure.
* Ett bash-gränssnitt i Azure Cloud Shell med Azure CLI v 2.3.1 med Azure IoT-tillägget v 0.10.6 eller senare installerat. I den här självstudien används [Azure Cloud Shell](../cloud-shell/overview.md). Om du inte känner till Azure Cloud Shell kan du [titta på en snabb start för mer information](./quickstart-linux.md#prerequisites).
  * Om du vill se dina aktuella versioner av Azure CLI-moduler och-tillägg kör du [AZ-versionen](/cli/azure/reference-index?#az_version).
* En Linux-enhet som ska konfigureras som en IoT Edge enhet för varje enhet i hierarkin. I den här självstudien används två enheter. Om du inte har tillgängliga enheter kan du skapa virtuella Azure-datorer för varje enhet i hierarkin med hjälp av kommandot nedan.

   Ersätt platshållartexten i följande kommando och kör den två gånger, en gång för varje virtuell dator. Varje virtuell dator behöver ett unikt DNS-prefix, som också fungerar som sitt namn. DNS-prefixet måste följa följande reguljära uttryck: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

   ```bash
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   Den virtuella datorn använder SSH-nycklar för att autentisera användare. Om du inte är bekant med att skapa och använda SSH-nycklar kan du följa [anvisningarna för offentliga SSH-nyckel par för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

   IoT Edge version 1,2 är förinstallerad med den här ARM-mallen och sparar behovet av att manuellt installera till gångarna på de virtuella datorerna. Om du installerar IoT Edge på dina egna enheter, se [installera Azure IoT Edge för Linux (version 1,2)](how-to-install-iot-edge.md) eller [Uppdatera IoT Edge till version 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   Om du har skapat en virtuell dator med den här ARM-mallen kommer den virtuella datorns `SSH` referens och fullständigt kvalificerat domän namn () att matas ut `FQDN` . Du kommer att använda SSH-handtaget och antingen FQDN eller IP-adressen för varje virtuell dator för konfiguration i senare steg, så håll koll på den här informationen. Ett exempel på utdata visas i bilden nedan.

   >[!TIP]
   >Du kan också hitta IP-adressen och det fullständiga domän namnet på Azure Portal. För IP-adressen navigerar du till listan över virtuella datorer och noterar **fältet offentlig IP-adress**. För det fullständiga domän namnet går du till sidan Översikt för varje virtuell dator och letar efter fältet **DNS-namn** .

   ![Den virtuella datorn kommer att mata ut en JSON när den skapas, som innehåller dess SSH-handtag](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Kontrol lera att följande portar är öppna inkommande för alla enheter förutom den lägsta lager enheten: 8000, 443, 5671, 8883:
  * 8000: används för att hämta Docker-behållar avbildningar via API-proxyn.
  * 443: används mellan överordnade och underordnade Edge-nav för REST API-anrop.
  * 5671, 8883: används för AMQP och MQTT.

  Mer information finns i [så här öppnar du portar till en virtuell dator med Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>Konfigurera din IoT Edge enhets hierarki

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Skapa en hierarki med IoT Edge enheter

IoT Edge enheter utgör lagren i hierarkin. I den här självstudien får du skapa en hierarki med två IoT Edge enheter: det **översta lager enheten** och dess underordnade, den **lägre lager enheten**. Du kan skapa ytterligare underordnade enheter efter behov.

Om du vill skapa och konfigurera hierarkin för IoT Edge enheter använder du `iotedge-config` verktyget. Det här verktyget fören klar konfigurationen av hierarkin genom att automatisera och kondensera flera steg i två:

1. Konfigurera moln konfigurationen och förbereda varje enhets konfiguration, bland annat:

   * Skapa enheter i din IoT Hub
   * Ange överordnad-underordnad-relationer för att auktorisera kommunikation mellan enheter
   * Skapa en kedja av certifikat för varje enhet för att upprätta säker kommunikation mellan dem
   * Genererar konfigurationsfiler för varje enhet

1. Installera varje enhets konfiguration, som innehåller:

   * Installera certifikat på varje enhet
   * Tillämpa konfigurationsfilerna för varje enhet

`iotedge-config`Verktyget kommer också att göra modulen distributioner till din IoT Edge enhet automatiskt.

Om du vill använda `iotedge-config` verktyget för att skapa och konfigurera hierarkin följer du stegen nedan i Azure CLI:

1. I [Azure Cloud Shell](https://shell.azure.com/)skapar du en katalog för självstudiernas resurser:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Ladda ned versionen av konfigurations verktyget och konfigurations mallarna:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Då skapas `iotedge_config_cli_release` mappen i din själv studie katalog.

   Mallfilen som används för att skapa din kategorihierarki är filen som `iotedge_config.yaml` finns i `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . I samma katalog `deploymentLowerLayer.json` är en JSON-distributions fil som innehåller instruktioner för vilka moduler som ska distribueras till den **lägre lager enheten**. `deploymentTopLayer.json`Filen är densamma men för enheten på den **översta nivån** är de moduler som distribueras till varje enhet inte desamma. `device_config.toml`Filen är en mall för IoT Edge enhets konfiguration och kommer att användas för att automatiskt generera konfigurations paket för enheterna i hierarkin.

   Om du vill ta en titt på käll koden och skripten för `iotedge-config` verktyget kan du kolla [in Azure-Samples-lagringsplatsen på GitHub](https://github.com/Azure-Samples/iotedge_config_cli).

1. Öppna konfigurations mal len för självstudie och redigera den med din information:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   I avsnittet **iothub** fyller du i `iothub_hostname` fälten och `iothub_name` med din information. Den här informationen hittar du på översikts sidan för din IoT Hub på Azure Portal.

   I avsnittet valfria **certifikat** kan du fylla i fälten med absoluta sökvägar till ditt certifikat och nyckel. Om du lämnar dessa fält tomma genererar skriptet automatiskt självsignerade test certifikat för din användning. Om du inte är bekant med hur certifikat används i ett Gateway-scenario, kan du läsa [avsnittet om hur du går till i guiden certifikat](how-to-connect-downstream-iot-edge-device.md#prepare-certificates).

   I **konfigurations** avsnittet `template_config_path` är sökvägen till den `device_config.toml` mall som användes för att skapa dina enhets konfigurationer. `default_edge_agent`Fältet avgör vilken Edge agent-avbildning som lägsta lager enheter kommer att hämta och från var.

   I avsnittet **edgedevices** för ett produktions scenario kan du redigera hierarkiträdet för att återspegla din önskade struktur. I den här självstudien accepterar du standard trädet. Det finns ett `device_id` fält där du kan namnge dina enheter för varje enhet. Det finns också `deployment` fältet som anger sökvägen till distributions-JSON för enheten.

   Du kan också registrera IoT Edge enheter manuellt i IoT Hub via Azure Portal eller Azure Cloud Shell. Läs mer i [guiden om hur du registrerar en IoT Edge-enhet](how-to-register-device.md).

   Du kan även definiera relationer över överordnade och underordnade manuellt. Mer information finns i avsnittet [skapa en gateway-hierarki](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) i instruktionen instruktion.

   ![I avsnittet edgedevices i konfigurations filen kan du definiera hierarkin](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Spara och Stäng filen:

   `CTRL + S`, `CTRL + Q`

1. Skapa en katalog för utdata för konfigurations paketen i din själv studie resurs katalog:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Navigera till din `iotedge_config_cli_release` katalog och kör verktyget för att skapa en hierarki med IoT Edge enheter:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Med `--output` -flaggan skapar verktyget enhets certifikat, certifikat paket och en loggfil i valfri katalog. När `-f` flaggan har angetts letar verktyget automatiskt efter befintliga IoT Edge enheter i IoT Hub och tar bort dem, för att undvika fel och hålla ditt nav rent säkert.

   Konfigurations verktyget skapar IoT Edge enheter och konfigurerar de överordnade och underordnade relationerna mellan dem. Du kan också skapa certifikat för dina enheter som ska användas. Om sökvägar till distributions-JSON anges skapar verktyget automatiskt dessa distributioner till dina enheter, men det är inget krav. Slutligen genererar verktyget konfigurations paketen för dina enheter och placerar dem i katalogen utdata. En grundlig genom gång av de steg som vidtas av konfigurations verktyget finns i logg filen i katalogen utdata.

   ![Skriptet visar en topologi för hierarkin vid körning](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Kontrol lera att topologins utdata från skriptet ser korrekt ut. När du är nöjd med att hierarkin är korrekt strukturerad är du redo att gå vidare.

### <a name="configure-the-iot-edge-runtime"></a>Konfigurera IoT Edge-körningen

Förutom att etablera dina enheter, etablerar konfigurations stegen betrodd kommunikation mellan enheterna i hierarkin med hjälp av de certifikat som du skapade tidigare. Stegen börjar också upprätta en nätverks struktur för hierarkin. Enheten på den översta nivån kommer att ha Internet anslutning, så att den kan hämta avbildningar för körning från molnet, medan lägre lager enheter dirigerar genom enheten på den översta nivån för att få åtkomst till de här avbildningarna.

Om du vill konfigurera IoT Edge runtime måste du tillämpa konfigurations paketen som skapats av installations skriptet på dina enheter. Konfigurationerna skiljer sig något från den **översta lager enheten** och en **enhet med lägre lager**, så det är mindful som du använder för varje enhet.

1. Varje enhet behöver sitt motsvarande konfigurations paket. Du kan använda en USB-enhet eller en [säker fil kopia](https://www.ssh.com/ssh/scp/) för att flytta konfigurations paketen till varje enhet.

   Se till att skicka rätt konfigurations paket till varje enhet.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   `:~`Innebär att konfigurationsfilerna placeras i hem katalogen på den virtuella datorn.

1. Logga in på den virtuella datorn för att tillämpa konfigurations paketet på enheten:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Zippa upp konfigurations paketet på varje enhet. Du måste först installera zip:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. På varje enhet använder du konfigurations paketet på enheten:

   ```bash
   sudo ./install.sh
   ```

   På enheten på den översta nivån får du en **uppräknings** meddelande om att ange värd namnet. På den **lägre lager enheten** frågar den om värd namnet och det överordnade objektets värdnamn. Ange lämplig IP eller FQDN för varje prompt. Du kan använda antingen, men vara konsekvent i ditt val mellan enheter. Utdata från installations skriptet visas i bilden nedan.

   Om du vill veta mer om vilka ändringar som görs i enhetens konfigurations fil kan du läsa [avsnittet konfigurera IoT Edge på enheter i instruktionen instruktion](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

  ![Genom att installera konfigurations paketen uppdateras config. toml-filerna på enheten och startar om alla IoT Edge-tjänster automatiskt](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Om du har slutfört ovanstående steg korrekt kan du kontrol lera att enheterna är korrekt konfigurerade.

Kör konfigurations-och anslutnings kontrollerna på enheterna. För enheten på den **översta nivån**:

   ```bash
   sudo iotedge check
   ```

För den **lägre lager enheten** måste diagnostik-avbildningen skickas manuellt i kommandot:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

På din **enhet på den översta nivån** förväntar du dig att se utdata med flera utvärderings resultat. Du kan se vissa varningar om loggar principer och, beroende på ditt nätverk, DNS-principer.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

När du är nöjd med att dina konfigurationer är korrekta på varje enhet är du redo att gå vidare.

## <a name="deploy-modules-to-your-devices"></a>Distribuera moduler till dina enheter

Modul distributionen till dina enheter genererades automatiskt när enheterna skapades. `iotedge-config-cli`Verktyget matas in distributions-JSON för de **övre och nedre skikt enheterna** efter att de har skapats. Modul distributionen väntade medan du konfigurerade IoT Edge runtime på varje enhet. När du har konfigurerat körningen påbörjades distributionen av **enheten på den översta nivån** . När dessa distributioner har slutförts kan den **lägre lager enheten** använda modulen **IoT Edge-API-proxy** för att hämta de bilder som behövs.

I [Azure Cloud Shell](https://shell.azure.com/)kan du ta en titt på den **översta lager enhetens** distributions-JSON för att förstå vilka moduler som distribuerats till din enhet:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Förutom körnings modulerna **IoT Edge agent** och **IoT Edge Hub** tar enheten på den **översta nivån** emot **docker-Registry** -modulen och **IoT Edge API-proxy** -modulen.

**Docker-register** -modulen pekar på en befintlig Azure Container Registry. I det här fallet `REGISTRY_PROXY_REMOTEURL` pekar du på Microsoft container Registry. I kan `createOptions` du se att den kommunicerar på port 5000.

Modulen **IoT Edge API-proxy** dirigerar HTTP-förfrågningar till andra moduler, vilket gör det möjligt för lägre lager enheter att hämta behållar avbildningar eller push-blobbar till lagring. I den här självstudien kommunicerar den på port 8000 och har kon figurer ATS för att skicka en pull-begäran till **Docker-register** -modulen på port 5000. Dessutom skickas alla Blob Storage-uppladdnings begär anden till modul AzureBlobStorageonIoTEdge på port 11002. Mer information om modulen **IoT Edge-API-proxy** och hur du konfigurerar den finns i modulens [instruktions guide](how-to-configure-api-proxy-module.md).

Om du vill ha en titt på hur du skapar en distribution som detta via Azure Portal eller Azure Cloud Shell, se [enhets avsnittet överst i instruktions guiden](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices).

I [Azure Cloud Shell](https://shell.azure.com/)kan du ta en titt på den **lägre Layer-enhetens** distributions-JSON för att förstå vilka moduler som distribuerats till din enhet:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

Du kan se `systemModules` att den **lägre lager enhetens** körnings moduler är inställda på Hämta från `$upstream:8000` , i stället för `mcr.microsoft.com` , som den **översta lager enheten** gjorde. Den **lägre lager enheten** skickar Docker-avbildningen begär **IoT Edge API-proxy** -modulen på port 8000, eftersom den inte kan hämta avbildningarna från molnet direkt. Den andra modulen som distribueras till den **lägre lager enheten**, den **simulerade temperaturen** i modulen hets sensor, gör också dess avbildnings förfrågan till `$upstream:8000` .

Om du vill ha en titt på hur du skapar en distribution som detta via Azure Portal eller Azure Cloud Shell, se [avsnittet om lägre lager enheter i instruktionen instruktions guide](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices).

Du kan visa status för dina moduler med hjälp av kommandot:

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Det här kommandot kommer att mata ut alla edgeAgent-rapporterade egenskaper. Här är några användbara för att övervaka status för enheten: *körnings status*, *körnings start tid*, *senaste avslutnings tid* för körning, *antal startomstart*.

Du kan också se status för dina moduler på [Azure Portal](https://ms.portal.azure.com/). Navigera till avsnittet **IoT Edge** i IoT Hub för att se dina enheter och moduler.

När du är nöjd med modul distributionerna är du redo att gå vidare.

## <a name="view-generated-data"></a>Visa genererade data

Modulen **simulerad temperatur sensor** som du har överfört genererar exempel miljö data. Den skickar meddelanden som innehåller omgivande temperatur och fukt, maskin temperatur och tryck och en tidsstämpel.

Du kan också visa dessa meddelanden via [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Felsökning

Kör `iotedge check` kommandot för att verifiera konfigurationen och felsöka fel.

Du kan köra `iotedge check` i en kapslad hierarki, även om de underordnade datorerna inte har direkt åtkomst till Internet.

När du kör `iotedge check` från det nedre lagret försöker programmet Hämta avbildningen från den överordnade via port 443.

I den här självstudien använder vi port 8000, så vi måste ange den:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2.0-rc4
```

`azureiotedge-diagnostics`Värdet hämtas från behållar registret som är länkat till register-modulen. Den här självstudien har den angetts som standard till https://mcr.microsoft.com:

| Name | Värde |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Om du använder ett privat behållar register, se till att alla avbildningar (IoTEdgeAPIProxy, edgeAgent, edgeHub, simulerade temperatur sensorer och diagnostik) finns i behållar registret.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de lokala konfigurationerna och de Azure-resurser som du skapade i den här artikeln för att undvika avgifter.

Ta bort resurser:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

2. Välj namnet på resursgruppen som innehåller dina IoT Edge-testresurser. 

3. Granska listan över resurser som ingår i din resursgrupp. Om du vill ta bort alla kan du välja **Ta bort resursgrupp**. Om du bara vill ta bort några av dem kan du klicka i varje resurs och ta bort dem individuellt. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du två IoT Edge enheter som gatewayer och anger en som överordnad enhet för den andra. Sedan visade du att du hämtar en behållar avbildning till den underordnade enheten via en gateway med hjälp av modulen IoT Edge-API-proxy. Se [instruktions guiden för proxy-modulens användning](how-to-configure-api-proxy-module.md) om du vill veta mer.

Om du vill veta mer om hur du använder gatewayer för att skapa hierarkiska lager med IoT Edge enheter kan du läsa [instruktionen för att ansluta underordnade IoT Edge enheter](how-to-connect-downstream-iot-edge-device.md).

Om du vill se hur Azure IoT Edge kan skapa flera lösningar för ditt företag fortsätter du med de andra självstudiekurserna.

> [!div class="nextstepaction"]
> [Distribuera en Azure Machine Learning-modell som en modul](tutorial-deploy-machine-learning.md)
