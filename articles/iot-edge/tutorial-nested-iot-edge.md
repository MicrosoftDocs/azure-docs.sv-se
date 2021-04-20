---
title: Självstudie – Skapa en hierarki med IoT Edge enheter – Azure IoT Edge
description: Den här självstudien visar hur du skapar en hierarkisk struktur för IoT Edge enheter med hjälp av gatewayer.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 44fe6bb3787e1fe0df7ccf83200497b46c473568
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728508"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Självstudie: Skapa en hierarki med IoT Edge enheter

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Distribuera Azure IoT Edge noder i nätverk som är ordnade i hierarkiska lager. Varje lager i en hierarki är en gateway-enhet som hanterar meddelanden och begäranden från enheter i lagret under den.

Du kan strukturera en hierarki med enheter så att endast det översta lagret har anslutning till molnet, och de lägre lagren bara kan kommunicera med intilliggande, norra och södra skikt. Den här nätverksskiktningen är grunden för de flesta industriella nätverk, som följer [ISA-95-standarden](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Målet med den här självstudien är att skapa en hierarki med IoT Edge enheter som simulerar en förenklad produktionsmiljö. I slutet distribuerar du modulen [Simulerad](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) temperatursensor till en enhet på lägre nivå utan Internetåtkomst genom att ladda ned containeravbildningar via hierarkin.

För att uppnå det här målet går den här självstudien igenom hur du skapar en hierarki med IoT Edge enheter, distribuerar IoT Edge-körningscontainrar till dina enheter och konfigurerar dina enheter lokalt. I den här självstudien använder du ett automatiserat konfigurationsverktyg för att:

> [!div class="checklist"]
>
> * Skapa och definiera relationerna i en hierarki med IoT Edge enheter.
> * Konfigurera IoT Edge på enheterna i hierarkin.
> * Installera konsekventa certifikat i din enhetshierarki.
> * Lägg till arbetsbelastningar till enheterna i hierarkin.
> * Använd [modulen IoT Edge API Proxy för att](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) på ett säkert sätt dirigera HTTP-trafik via en enskild port från dina enheter på lägre nivå.

>[!TIP]
>Den här självstudien innehåller en blandning av manuella och automatiserade steg för att tillhandahålla en presentation av kapslade IoT Edge funktioner.
>
>Om du vill ha en helt automatiserad titt på att konfigurera en hierarki med IoT Edge enheter kan du följa skripten [Azure IoT Edge för industriella IoT-exempel](https://aka.ms/iotedge-nested-sample). Det här skriptscenariot distribuerar virtuella Azure-datorer som förkonfigurerade enheter för att simulera en fabriksmiljö.
>
>Om du vill ha en detaljerad titt på de manuella stegen för att skapa och hantera en hierarki med IoT Edge-enheter kan du läsa instruktionerna i IoT Edge om [enhetsgatewayhierarkier](how-to-connect-downstream-iot-edge-device.md).

I den här självstudien definieras följande nätverkslager:

* **Översta lagret:** IoT Edge enheter i det här skiktet kan ansluta direkt till molnet.

* **Lägre lager:** IoT Edge enheter i lager under det översta lagret kan inte ansluta direkt till molnet. De måste gå igenom en eller flera mellanliggande enheter IoT Edge att skicka och ta emot data.

I den här självstudien används en två-enhetshierarki för enkelhetens skull, på bilden nedan. En enhet, det **översta lagret,** representerar en enhet i det översta lagret i hierarkin, som kan ansluta direkt till molnet. Den här enheten kallas även för den **överordnade enheten**. Den andra enheten, enheten **på lägre nivå,** representerar en enhet i det nedre lagret i hierarkin, som inte kan ansluta direkt till molnet. Du kan lägga till enheter med lägre lager för att representera produktionsmiljön efter behov. Enheter med lägre lager kallas även underordnade **enheter**.

![Strukturen i självstudiehierarkin, som innehåller två enheter: enheten på det översta lagret och enheten i det lägre lagret](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en IoT Edge med enheter behöver du:

* En dator (Windows eller Linux) med Internetanslutning.
* Ett Azure-konto med en giltig prenumeration. Om du inte har en [Azure-prenumeration kan](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En kostnadsfri nivå eller [standardnivå IoT Hub](../iot-hub/iot-hub-create-through-portal.md) i Azure.
* Ett Bash-gränssnitt Azure Cloud Shell azure CLI v2.3.1 med Azure IoT-tillägget v0.10.6 eller senare installerat. I den här självstudien [används Azure Cloud Shell](../cloud-shell/overview.md). Om du inte är bekant med Azure Cloud Shell du [en snabbstart för mer information.](./quickstart-linux.md#prerequisites)
  * Om du vill se dina aktuella versioner av Azure CLI-modulerna och tilläggen kör [du az version](/cli/azure/reference-index?#az_version).
* En Linux-enhet som ska konfigureras IoT Edge en enhet för varje enhet i hierarkin. I den här självstudien används två enheter. Om du inte har tillgängliga enheter kan du skapa virtuella Azure-datorer för varje enhet i hierarkin med hjälp av kommandot nedan.

   Ersätt platshållartexten i följande kommando och kör den två gånger, en gång för varje virtuell dator. Varje virtuell dator behöver ett unikt DNS-prefix, som också fungerar som dess namn. DNS-prefixet måste överensstämma med följande reguljära uttryck: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

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

   Den virtuella datorn använder SSH-nycklar för autentisering av användare. Om du inte är bekant med att skapa och använda SSH-nycklar kan du följa anvisningarna för [offentliga/privata SSH-nyckelpar](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)för virtuella Linux-datorer i Azure .

   IoT Edge version 1.2 förinstalleras med den här ARM-mallen, vilket gör att du inte behöver installera tillgångarna manuellt på de virtuella datorerna. Om du installerar IoT Edge på dina egna enheter kan du se [Installera Azure IoT Edge för Linux (version 1.2)](how-to-install-iot-edge.md) eller Uppdatera IoT Edge till version [1.2.](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)

   Om en virtuell dator skapas med hjälp av den här ARM-mallen matas den virtuella datorns referens `SSH` och fullständigt kvalificerade domännamn ut ( `FQDN` ). Du kommer att använda SSH-referensen och Antingen FQDN- eller IP-adressen för varje virtuell dator för konfiguration i senare steg, så håll reda på den här informationen. Ett exempel på utdata visas nedan.

   >[!TIP]
   >Du kan också hitta IP-adressen och FQDN på Azure Portal. För IP-adressen navigerar du till listan över virtuella datorer och noterar fältet **Offentlig IP-adress.** För FQDN går du till översiktssidan för varje virtuell dator och letar efter **fältet DNS-namn.**

   ![Den virtuella datorn matar ut en JSON när den skapas, som innehåller dess SSH-referens](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Kontrollera att följande portar är öppna för alla enheter utom enheten på det lägsta lagret: 8000, 443, 5671, 8883:
  * 8000: Används för att hämta Docker-containeravbildningar via API-proxyn.
  * 443: Används mellan överordnade och underordnade kanthubbbar för REST API anrop.
  * 5671, 8883: Används för AMQP och MQTT.

  Mer information finns i [hur du öppnar portar till en virtuell dator med hjälp av Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>Konfigurera din IoT Edge enhetshierarki

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Skapa en hierarki med IoT Edge enheter

IoT Edge enheter utgör lagren i hierarkin. Den här självstudien skapar en hierarki med  IoT Edge enheter: enheten på det översta lagret och dess underordnade enhet, **enheten i det lägre lagret**. Du kan skapa ytterligare underordnade enheter efter behov.

Om du vill skapa och konfigurera hierarkin IoT Edge enheter använder du `iotedge-config` verktyget . Det här verktyget förenklar konfigurationen av hierarkin genom att automatisera och komprimera flera steg till två:

1. Konfigurera molnkonfigurationen och förbereda varje enhetskonfiguration, vilket omfattar:

   * Skapa enheter i IoT Hub
   * Ange överordnade-underordnade relationer för att auktorisera kommunikation mellan enheter
   * Generera en certifikatkedja för varje enhet för att upprätta säker kommunikation mellan dem
   * Generera konfigurationsfiler för varje enhet

1. Installera varje enhetskonfiguration, vilket innefattar:

   * Installera certifikat på varje enhet
   * Tillämpa konfigurationsfilerna för varje enhet

Verktyget `iotedge-config` gör även moduldistributionerna till din IoT Edge enhet automatiskt.

Om du vill `iotedge-config` använda verktyget för att skapa och konfigurera hierarkin följer du stegen nedan i Azure CLI:

1. I [Azure Cloud Shell](https://shell.azure.com/)du en katalog för självstudiens resurser:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Ladda ned versionen av konfigurationsverktyget och konfigurationsmallarna:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Då skapas mappen `iotedge_config_cli_release` i självstudiekatalogen.

   Mallfilen som används för att skapa din enhetshierarki är `iotedge_config.yaml` den fil som finns i `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . I samma katalog finns en `deploymentLowerLayer.json` JSON-distributionsfil som innehåller instruktioner för vilka moduler som ska distribueras till **enheten på den lägre nivån.** Filen är densamma, men för enheten på den översta nivån `deploymentTopLayer.json` är modulerna som distribueras till varje enhet inte desamma.  Filen är en mall för IoT Edge enhetskonfigurationer och används för att automatiskt generera `device_config.toml` konfigurationspaketen för enheterna i hierarkin.

   Om du vill ta en titt på källkoden och skripten för verktyget kan du ta en titt på `iotedge-config` [Azure-Samples på GitHub.](https://github.com/Azure-Samples/iotedge_config_cli)

1. Öppna konfigurationsmallen för självstudien och redigera den med din information:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   I **avsnittet iothub** fyller du i `iothub_hostname` fälten och med din `iothub_name` information. Den här informationen finns på översiktssidan för ditt IoT Hub på Azure Portal.

   I avsnittet **valfria** certifikat kan du fylla i fälten med de absoluta sökvägarna till ditt certifikat och din nyckel. Om du lämnar fälten tomma genererar skriptet automatiskt själv signerade testcertifikat för din användning. Om du inte är bekant med hur certifikat används i ett gatewayscenario kan du läsa avsnittet om certifikat i [guiden.](how-to-connect-downstream-iot-edge-device.md#prepare-certificates)

   I **konfigurationsavsnittet** är `template_config_path` sökvägen till den mall `device_config.toml` som används för att skapa enhetskonfigurationerna. Fältet `default_edge_agent` avgör vilken Edge Agent-avbildning som enheter med lägre lager hämtar och varifrån.

   I avsnittet **gränsenheter kan** du för ett produktionsscenario redigera hierarkiträdet så att det återspeglar din önskade struktur. I den här självstudien accepterar du standardträdet. För varje enhet finns det ett `device_id` fält där du kan namnge dina enheter. Det finns också `deployment` fältet , som anger sökvägen till JSON-distributionssökvägen för den enheten.

   Du kan också manuellt registrera IoT Edge enheter i din IoT Hub via Azure Portal eller Azure Cloud Shell. Information om hur du gör [finns i guiden om hur du registrerar en IoT Edge enhet](how-to-register-device.md).

   Du kan även definiera de överordnade-underordnade relationerna manuellt. Mer information [finns i avsnittet skapa](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) en gatewayhierarki i guiden.

   ![I avsnittet edgedevices i konfigurationsfilen kan du definiera hierarkin](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Spara och stäng filen:

   `CTRL + S`, `CTRL + Q`

1. Skapa en utdatakatalog för konfigurationspaketen i katalogen med självstudieresurser:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Gå till katalogen `iotedge_config_cli_release` och kör verktyget för att skapa hierarkin med IoT Edge enheter:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Med `--output` flaggan skapar verktyget enhetscertifikat, certifikatpaket och en loggfil i valfri katalog. När flaggan har angetts letar verktyget automatiskt efter befintliga IoT Edge i din IoT Hub och tar bort dem, för att undvika fel och hålla `-f` hubben ren.

   Konfigurationsverktyget skapar dina IoT Edge och uppsättningar de överordnade-underordnade relationerna mellan dem. Du kan också skapa certifikat som dina enheter kan använda. Om sökvägar till JSON för distribution tillhandahålls skapar verktyget automatiskt dessa distributioner till dina enheter, men detta krävs inte. Slutligen genererar verktyget konfigurationspaketen för dina enheter och placerar dem i utdatakatalogen. En grundlig genomgång av de steg som krävs av konfigurationsverktyget finns i loggfilen i utdatakatalogen.

   ![Skriptet visar en topologi i hierarkin vid körning](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Kontrollera att topologiutdata från skriptet ser korrekta ut. När du är nöjd med hierarkin är korrekt strukturerad är du redo att fortsätta.

### <a name="configure-the-iot-edge-runtime"></a>Konfigurera IoT Edge-körningen

Utöver etableringen av dina enheter upprättar konfigurationsstegen betrodd kommunikation mellan enheterna i hierarkin med hjälp av de certifikat som du skapade tidigare. Stegen börjar också upprätta nätverksstrukturen i hierarkin. Enheten på översta lagret upprätthåller Internetanslutningen, vilket gör att den kan hämta avbildningar för sin körning från molnet, medan enheter med lägre lager dirigeras genom den översta lagerenheten för att få åtkomst till dessa avbildningar.

För att konfigurera IoT Edge-körningen måste du tillämpa konfigurationspaketen som skapats av installationsskriptet på dina enheter. Konfigurationerna skiljer sig  något mellan enheten på den översta nivån och en enhet på lägre **nivå,** så var ihåg att tänka på vilken enhets konfigurationsfil du tillämpar på varje enhet.

1. Varje enhet behöver sitt motsvarande konfigurationspaket. Du kan använda en USB-enhet [eller en säker filkopia](https://www.ssh.com/ssh/scp/) för att flytta konfigurationspaketen till varje enhet.

   Se till att skicka rätt konfigurationspaket till varje enhet.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Innebär `:~` att konfigurationsmappen placeras i arbetskatalogen på den virtuella datorn.

1. Logga in på den virtuella datorn för att tillämpa konfigurationspaketet på enheten:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Packa upp konfigurationspaketet på varje enhet. Du måste installera zip först:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Tillämpa konfigurationspaketet på enheten på varje enhet:

   ```bash
   sudo ./install.sh
   ```

   På den **översta lagerenheten** får du en uppmaning om att ange värdnamnet. På enheten **på den lägre** nivån frågar den efter värdnamnet och det överordnade värdnamnet. Ange lämplig IP-adress eller FQDN för varje fråga. Du kan använda båda, men var konsekvent när du väljer mellan enheter. Utdata från installationsskriptet visas nedan.

   Om du vill ha en närmare titt på vilka ändringar som görs i enhetens konfigurationsfil kan du gå till avsnittet konfigurera IoT Edge på enheter i [i guiden](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

  ![När du installerar konfigurationspaketen uppdateras config.toml-filerna på enheten och alla tjänster IoT Edge startas om automatiskt](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Om du har slutfört ovanstående steg korrekt kan du kontrollera att enheterna är korrekt konfigurerade.

Kör konfigurations- och anslutningskontrollerna på dina enheter. För enheten **på översta lagret:**

   ```bash
   sudo iotedge check
   ```

För enheten **med lägre lager** måste diagnostikavbildningen skickas manuellt i kommandot :

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

På enheten **på det översta lagret** kan du förvänta dig att se utdata med flera utvärderingsresultat. Du kan se några varningar om loggar principer och, beroende på nätverket DNS-principer.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

När du är nöjd med dina konfigurationer på varje enhet är du redo att fortsätta.

## <a name="deploy-modules-to-your-devices"></a>Distribuera moduler till dina enheter

Moduldistributionerna till dina enheter genererades automatiskt när enheterna skapades. Verktyget `iotedge-config-cli` skickade JSON-distribution för enheter **på den översta och den lägre nivån** efter att de skapades. Moduldistributionen väntade medan du konfigurerade IoT Edge på varje enhet. När du har konfigurerat körningen började distributionerna till **enheten på översta** lagret. När distributionerna är klara kan enheten **på lägre nivå använda** IoT Edge API **Proxy-modulen** för att hämta nödvändiga avbildningar.

I [Azure Cloud Shell](https://shell.azure.com/)kan du ta en titt  på JSON-distributions-JSON på det översta lagret för att förstå vilka moduler som har distribuerats till enheten:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Dessutom tar runtime-modulerna **IoT Edge Agent** **och IoT Edge** Hub  , den översta lagerenheten emot **Docker-registermodulen** och IoT Edge API **Proxy-modulen.**

**Docker-registermodulen** pekar på en befintlig Azure Container Registry. I det här fallet `REGISTRY_PROXY_REMOTEURL` pekar på Microsoft Container Registry. I `createOptions` kan du se att den kommunicerar på port 5000.

Modulen **IoT Edge API Proxy** dirigerar HTTP-begäranden till andra moduler, vilket gör att enheter på lägre nivå kan hämta containeravbildningar eller push-blobar till lagringen. I den här självstudien kommunicerar den på port 8000 och är konfigurerad att skicka pull-begäranden för Docker-containeravbildningar till **din Docker-registermodul** på port 5000. Dessutom dirigeras alla begäranden om uppladdning av bloblagring till modulen AzureBlobStorageonIoTEdge på port 11002. Mer information om modulen **IoT Edge API Proxy** och hur du konfigurerar den finns i [modulens guide](how-to-configure-api-proxy-module.md).

Om du vill ta en titt på hur du skapar en distribution som den här via Azure Portal eller Azure Cloud Shell kan du gå till avsnittet enhet på översta lagret i guiden [.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices)

I [Azure Cloud Shell](https://shell.azure.com/)kan du ta en titt  på enhetens JSON-distribution på den lägre nivån för att förstå vilka moduler som har distribuerats till din enhet:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

Du kan se under att enhetens körningsmoduler på lägre nivå är inställda på att hämta från , i stället för , som `systemModules` enheten på översta  `$upstream:8000` `mcr.microsoft.com` **lagret** gjorde. Enheten **på den lägre nivån** skickar Docker-avbildningar IoT Edge API **Proxy-modulen** på port 8000 eftersom den inte kan hämta avbildningarna direkt från molnet. Den andra modulen som distribueras till **enheten på den lägre nivån**, modulen **Simulerad temperatursensor,** skickar även sin bildbegäran till `$upstream:8000` .

Om du vill ta en titt på hur du skapar en distribution som den här via Azure Portal eller Azure Cloud Shell kan du gå till avsnittet enhet på lägre nivå i guiden [.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices)

Du kan visa statusen för dina moduler med hjälp av kommandot :

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Det här kommandot matar ut alla edgeAgent-rapporterade egenskaper. Här följer några användbara för att övervaka status för enheten: *körningsstatus,* starttid för *körning,* körningstid vid senaste avslutstid, *antal omstarter av körning.* 

Du kan också se status för dina moduler på [Azure Portal](https://ms.portal.azure.com/). Gå till **avsnittet IoT Edge** i din IoT Hub för att se dina enheter och moduler.

När du är nöjd med dina moduldistributioner är du redo att fortsätta.

## <a name="view-generated-data"></a>Visa genererade data

Modulen **Simulerad temperatursensor** som du push-pushar genererar exempelmiljödata. Den skickar meddelanden som innehåller omgivande temperatur och luftfuktighet, maskintemperatur och tryck samt en tidsstämpel.

Du kan också visa dessa meddelanden via [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Felsökning

Kör kommandot `iotedge check` för att verifiera konfigurationen och felsöka fel.

Du kan köra `iotedge check` i en kapslad hierarki, även om de underordnade datorerna inte har direkt Internetåtkomst.

När du kör `iotedge check` från det nedre lagret försöker programmet hämta avbildningen från den överordnade via port 443.

I den här självstudien använder vi port 8000, så vi måste ange den:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2
```

Värdet `azureiotedge-diagnostics` hämtas från containerregistret som är länkat till registermodulen. I den här självstudien anges den som standard till https://mcr.microsoft.com:

| Name | Värde |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Om du använder ett privat containerregister kontrollerar du att alla avbildningar (IoTEdgeAPIProxy, edgeAgent, edgeHub, Simulated Temperature Sensor och diagnostics) finns i containerregistret.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de lokala konfigurationerna och De Azure-resurser som du skapade i den här artikeln för att undvika kostnader.

Ta bort resurser:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

2. Välj namnet på resursgruppen som innehåller dina IoT Edge-testresurser. 

3. Granska listan över resurser som ingår i din resursgrupp. Om du vill ta bort alla kan du välja **Ta bort resursgrupp**. Om du bara vill ta bort några av dem kan du klicka i varje resurs och ta bort dem individuellt. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du två IoT Edge som gatewayer och konfigurerade den ena som den överordnade enheten till den andra. Sedan visade du hur du kan hämta en containeravbildning till den underordnade enheten via en gateway med hjälp IoT Edge API Proxy-modulen. Mer [information finns i i guiden om hur proxymodulen](how-to-configure-api-proxy-module.md) används.

Mer information om hur du använder gatewayer för att skapa hierarkiska lager på [IoT Edge-enheter](how-to-connect-downstream-iot-edge-device.md)finns i i guiden om hur du ansluter nedströmsenheter IoT Edge enheter.

Om du vill se hur Azure IoT Edge kan skapa flera lösningar för ditt företag fortsätter du med de andra självstudiekurserna.

> [!div class="nextstepaction"]
> [Distribuera en Azure Machine Learning-modell som en modul](tutorial-deploy-machine-learning.md)
