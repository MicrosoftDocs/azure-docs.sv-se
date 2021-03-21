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
ms.openlocfilehash: c1b30a1eafe9af92c1ef3f81773d213ccf96555c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462039"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Självstudie: skapa en hierarki med IoT Edge enheter (förhands granskning)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Distribuera Azure IoT Edge noder mellan nätverk som är ordnade i hierarkiska lager. Varje lager i en hierarki är en gateway-enhet som hanterar meddelanden och begär Anden från enheter i lagret under den.

>[!NOTE]
>Den här funktionen kräver IoT Edge version 1,2, som finns i offentlig för hands version, som kör Linux-behållare.

Du kan strukturera en hierarki med enheter så att endast det översta lagret har anslutning till molnet och de nedre lagren kan bara kommunicera med intilliggande Nord-och syd-lager. Den här nätverks skikten är grunden i de flesta industriella nätverk, som följer [ISA-95-standarden](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Målet med den här självstudien är att skapa en hierarki med IoT Edge enheter som simulerar en produktions miljö. I slutet kommer du att distribuera den [simulerade temperatur sensor modulen](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) till en enhet med lägre lager utan Internet åtkomst genom att hämta behållar avbildningar via hierarkin.

För att uppnå det här målet vägleder dig genom den här självstudien genom att skapa en hierarki med IoT Edge enheter, distribuera IoT Edge runtime-behållare till dina enheter och konfigurera enheterna lokalt. I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa och definiera relationer i en hierarki med IoT Edge enheter.
> * Konfigurera IoT Edge runtime på enheterna i hierarkin.
> * Installera konsekventa certifikat i din enhets hierarki.
> * Lägg till arbets belastningar till enheterna i hierarkin.
> * Använd [modulen IoT Edge-API-proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) för att på ett säkert sätt dirigera http-trafik över en enskild port från dina enheter med lägre lager.

I den här självstudien definieras följande nätverks skikt:

* **Översta lagret**: IoT Edge enheter i det här skiktet kan ansluta direkt till molnet.

* **Lägre lager**: IoT Edge enheter på lager under det översta lagret kan inte ansluta direkt till molnet. De måste gå igenom en eller flera mellanliggande IoT Edge enheter för att kunna skicka och ta emot data.

I den här självstudien används två hierarkier för enkelhetens skull, som visas nedan. En enhet, den **översta lager enheten**, representerar en enhet i hierarkins övre skikt, som kan ansluta direkt till molnet. Enheten kommer även att kallas för den **överordnade enheten**. Den andra enheten, den **lägre lager enheten**, representerar en enhet i den nedre nivån i hierarkin, som inte kan ansluta direkt till molnet. Du kan lägga till ytterligare lägre lager enheter för att representera produktions miljön efter behov. Enheter på lägre lager kallas även **underordnade enheter**. Konfigurationen av ytterligare lägre lager enheter kommer att följa konfigurationen för den **lägre lager enheten**.

![Strukturen för kursens hierarki, som innehåller två enheter: det översta lager enheten och den lägre lager enheten](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en hierarki med IoT Edge enheter behöver du:

* En dator (Windows eller Linux) med Internet anslutning.
* Ett Azure-konto med en giltig prenumeration. Om du inte har en [Azure-prenumeration](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En kostnads fri eller standard nivå [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) i Azure.
* Azure CLI v 2.3.1 med Azure IoT-tillägget v 0.10.6 eller senare installerat. I den här självstudien används [Azure Cloud Shell](../cloud-shell/overview.md). Om du inte känner till Azure Cloud Shell kan du [titta på en snabb start för mer information](./quickstart-linux.md#prerequisites).
  * Om du vill se dina aktuella versioner av Azure CLI-moduler och-tillägg kör du [AZ-versionen](/cli/azure/reference-index?#az_version).
* En Linux-enhet som ska konfigureras som en IoT Edge enhet för varje enhet i hierarkin. I den här självstudien används två enheter. Om du inte har tillgängliga enheter kan du skapa virtuella Azure-datorer för varje enhet i hierarkin genom att ersätta platshållartexten i följande kommando och köra den:

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* Kontrol lera att följande portar är öppna inkommande: 8000, 443, 5671, 8883:
  * 8000: används för att hämta Docker-behållar avbildningar via API-proxyn.
  * 443: används mellan överordnade och underordnade Edge-nav för REST API-anrop.
  * 5671, 8883: används för AMQP och MQTT.

  Ner information finns i artikeln om [hur du öppnar portar för en virtuell dator med Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

>[!TIP]
>Om du vill ha en automatisk titt på hur du konfigurerar en hierarki med IoT Edge enheter kan du följa exemplet med skript [Azure IoT Edge för industriella IoT-exempel](https://aka.ms/iotedge-nested-sample). Det här skript scenariot distribuerar virtuella Azure-datorer som förkonfigurerade enheter för att simulera en fabriks miljö.
>
>Om du vill fortsätta trots att du skapade övnings exemplet steg för steg, fortsätter du med själv studie stegen nedan.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Konfigurera din IoT Edge enhets hierarki

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Skapa en hierarki med IoT Edge enheter

IoT Edge enheter utgör lagren i hierarkin. I den här självstudien får du skapa en hierarki med två IoT Edge enheter: det **översta lager enheten** och dess underordnade, den **lägre lager enheten**. Du kan skapa ytterligare underordnade enheter efter behov.

Du kan använda Azure Portal eller Azure CLI för att skapa dina IoT Edge enheter.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://ms.portal.azure.com/)navigerar du till IoT Hub.

1. I menyn i den vänstra rutan, under **Automatisk enhets hantering**, väljer du **IoT Edge**.

1. Välj **+ Lägg till en IoT Edge enhet**. Enheten kommer att bli den översta lager enheten, så ange ett lämpligt unikt enhets-ID. Välj **Spara**.

1. Välj **+ Lägg till en IoT Edge enhet** igen. Enheten kommer att vara en enhet med lägre lager, så ange ett lämpligt unikt enhets-ID.

1. Välj **Ange en överordnad enhet**, Välj ditt översta skikts enhet i listan över enheter och välj **OK**. Välj **Spara**.

   ![Anger överordnad för enhet med lägre lager](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. I [Azure Cloud Shell](https://shell.azure.com/)anger du följande kommando för att skapa en IoT Edge-enhet i hubben. Enheten kommer att bli den översta lager enheten, så ange ett lämpligt unikt enhets-ID:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   En lyckad enhets skapande kommer att mata ut enhetens JSON-konfiguration.

   ![JSON-utdata för en lyckad enhets skapande](./media/tutorial-nested-iot-edge/json-success-output.png)

1. Ange följande kommando för att skapa en lägre Layer IoT Edge-enhet. Du kan skapa mer än en enhet med lägre lager om du vill ha mer lager i hierarkin. Se till att tillhandahålla unika enhets identiteter.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Ange följande kommando för att definiera varje överordnad-underordnad-relation mellan den **översta lager enheten** och varje **enhet med lägre lager**. Se till att köra det här kommandot för varje enhet med lägre lager i hierarkin.

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   Det här kommandot har inga explicita utdata.

---

Anteckna sedan varje IoT Edge enhets anslutnings sträng. De kommer att användas senare.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://ms.portal.azure.com/)navigerar du till avsnittet **IoT Edge** i IoT Hub.

1. Klicka på enhets-ID: t för en av enheterna i listan över enheter.

1. Välj **Kopiera** i fältet **primär anslutnings sträng** och registrera den på valfri plats.

1. Upprepa för alla andra enheter.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Ange följande kommando för varje enhet i [Azure Cloud Shell](https://shell.azure.com/)för att hämta anslutnings strängen för enheten och registrera den på valfri plats:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

Om du har slutfört ovanstående steg korrekt kan du använda följande steg för att kontrol lera att de överordnade-underordnade relationerna är korrekta i Azure Portal eller Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://ms.portal.azure.com/)navigerar du till avsnittet **IoT Edge** i IoT Hub.

1. Klicka på enhets-ID: t för en enhet med ett **lägre lager** i listan över enheter.

1. På enhetens informations sida bör du se ditt **högsta skikts enhets** identitet som anges bredvid fältet **överordnad enhet** .

   [Överordnad enhet har godkänts av en underordnad enhet](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

Du kan också ha en underordnad hierarkins relation till din **översta lager enhet**.

1. Klicka på enhets-ID: t för en **enhet på översta nivån** i listan över enheter.

1. Välj fliken **Hantera underordnade enheter** överst.

1. Under listan över enheter bör du se din **lägre lager enhet**.

   [Underordnad enhet har godkänts av överordnad enhet](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. I [Azure Cloud Shell](https://shell.azure.com/) kan du kontrol lera att någon av dina underordnade enheter har upprättat relationen med den överordnade enheten genom att hämta identiteten för den underordnade enhetens bekräftade överordnade enhet. Detta kommer att generera den överordnade enhetens JSON-konfiguration, bildad ovan:

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

Du kan också underordna hierarkins Relations hip genom att fråga din **enhet på högsta nivån**.

1. Lista de underordnade enheterna som den överordnade enheten känner till:

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

När du är nöjd med att hierarkin är korrekt strukturerad är du redo att gå vidare.

### <a name="create-certificates"></a>Skapa certifikat

Alla enheter i ett [Gateway-scenario](iot-edge-as-gateway.md) måste ha ett delat certifikat för att skapa säkra anslutningar mellan dem. Använd följande steg för att skapa demo certifikat för båda enheterna i det här scenariot.

Om du vill skapa demo certifikat på en Linux-enhet måste du klona generationens skript och konfigurera dem för att köras lokalt i bash.

1. Klona IoT Edge git-lagrings platsen, som innehåller skript för att generera demo certifikat.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Navigera till den katalog som du vill arbeta med. Alla certifikat och viktiga filer kommer att skapas i den här katalogen.

1. Kopiera config-och skriptfiler från den klonade IoT Edge lagrings platsen till din arbets katalog.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Skapa rot certifikat utfärdarens certifikat och ett mellanliggande certifikat.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer, men vi använder följande fil som **rot certifikat utfärdarens certifikat** för gateway-hierarkin:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Skapa två uppsättningar med IoT Edge enhets certifikat och privata nycklar med följande kommando: en uppsättning för det översta skiktets enhet och en uppsättning för den lägre lager enheten. Ange minnes namn för certifikat utfärdarna för att skilja dem från varandra.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer, men vi använder följande certifikat och nyckel par på varje IoT Edge enhet och refereras till i konfigurations filen:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Varje enhet behöver en kopia av rot certifikat utfärdarens certifikat och en kopia av dess egna enhets certifikat och privata nyckel. Du kan använda en USB-enhet eller en [säker fil kopia](https://www.ssh.com/ssh/scp/) för att flytta certifikaten till varje enhet.

1. När certifikaten har överförts installerar du rot certifikat utfärdaren för varje enhet.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Det här kommandot ska utdata som visar att ett certifikat har lagts till i `/etc/ssl/certs` .

   [Installations meddelande för lyckad certifikat](./media/tutorial-nested-iot-edge/certificates-success-output.png)

Om du har slutfört ovanstående steg korrekt kan du kontrol lera att dina certifikat är installerade i `/etc/ssl/certs` genom att gå till den katalogen och söka efter de installerade certifikaten.

1. Gå till `/etc/ssl/certs` :

   ```bash
   cd /etc/ssl/certs
   ```

1. Lista de installerade certifikaten och `grep` för `azure` :

   ```bash
   ll | grep azure
   ```

   Du bör se en certifikat-hash som är länkad till ditt rot certifikat för certifikat utfärdare och ditt rotcertifikatutfärdarcertifikat som är länkat till kopian i din `usr/local/share` katalog.

   [Sök Resultat för Azure-certifikat](./media/tutorial-nested-iot-edge/certificate-list-results.png)

När du är nöjd med att dina certifikat är installerade på varje enhet är du redo att gå vidare.

### <a name="install-iot-edge-on-the-devices"></a>Installera IoT Edge på enheterna

Genom att installera IoT Edge version 1,2 Runtime-avbildningarna får dina enheter åtkomst till de funktioner som krävs för att kunna användas som en hierarki med enheter.

Om du vill installera IoT Edge måste du installera lämplig lagrings plats konfiguration, installera nödvändiga komponenter och installera de nödvändiga versions resurserna.

1. Installera den lagrings plats konfiguration som matchar enhetens operativ system.

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS-storlek**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. Kopiera den genererade listan till katalogen sources. list. d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Installera den offentliga nyckeln för Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

1. Uppdatera paket listor på enheten.

   ```bash
   sudo apt-get update
   ```

1. Installera Moby-motorn.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Installera IoT Edge och IoT Identity service.

   ```bash
   sudo apt-get install aziot-edge
   ```

Om du har slutfört ovanstående steg korrekt såg du det Azure IoT Edge banner-meddelande som begär att du uppdaterar Azure IoT Edge konfigurations filen, `/etc/aziot/config.toml` på varje enhet i hierarkin. I så fall är du redo att fortsätta.

### <a name="configure-the-iot-edge-runtime"></a>Konfigurera IoT Edge-körningen

Förutom att etablera dina enheter, etablerar konfigurations stegen betrodd kommunikation mellan enheterna i hierarkin med hjälp av de certifikat som du skapade tidigare. Stegen börjar också upprätta en nätverks struktur för hierarkin. Enheten på den översta nivån kommer att ha Internet anslutning, så att den kan hämta avbildningar för körning från molnet, medan lägre lager enheter dirigerar genom enheten på den översta nivån för att få åtkomst till de här avbildningarna.

Om du vill konfigurera IoT Edge runtime måste du ändra flera komponenter i konfigurations filen. Konfigurationen skiljer sig något från den **översta lager enheten** och en **enhet med lägre lager**, så det är mindful som du redigerar för varje steg. Att konfigurera IoT Edge runtime för dina enheter består av fyra steg, som du gör genom att redigera IoT Edge-konfigurations filen:

* Etablera varje enhet manuellt genom att lägga till enhetens anslutnings sträng i konfigurations filen.

* Slutför konfigurationen av enhetens certifikat genom att peka konfigurations filen på enhetens CA-certifikat, privat nyckel för enhetens certifikat utfärdare och certifikat för rot certifikat utfärdare.

* Starta systemet med IoT Edge-agenten.

* Uppdatera **hostname** -parametern för enheten på den **högsta nivån** och uppdatera både parametern **hostname** och **parent_hostname** parametern för de **lägre lager** enheterna.

Slutför de här stegen och starta om tjänsten IoT Edge för att konfigurera dina enheter.

>[!TIP]
>När du navigerar i konfigurations filen i nano kan du söka efter nyckelord i filen med **CTRL + W** .

1. Skapa en konfigurations fil baserat på den inkluderade mallen på varje enhet.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Hitta avsnittet **hostname** för din enhet på **högsta nivån** . Ta bort kommentaren till raden med `hostname` parametern och uppdatera värdet så att det är det fullständigt kvalificerade domän namnet (FQDN) eller IP-adressen för den IoT Edge enheten. Använd det värde du väljer konsekvent över enheterna i hierarkin.

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >Klistra in innehållet i Urklipp i nano `Shift+Right Click` eller tryck på `Shift+Insert` .

1. För alla IoT Edge enheter i **lägre lager** hittar du avsnittet **överordnat värdnamn** . Ta bort kommentaren till raden med `parent_hostname` parametern och uppdatera värdet så att det pekar på det fullständiga domän namnet eller IP-adressen för den överordnade enheten. Använd det exakta värdet som du angav i den överordnade enhetens **värdnamn** -fält. Lämna den här parametern kommenterad för IoT Edge-enheten i det **översta lagret**.

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > För hierarkier med fler än ett lägre lager uppdaterar du *parent_hostname* fältet med FQDN för enheten i lagret direkt ovan.

1. Hitta avsnittet **TRUSTe Bundle-certifikat** i filen. Ta bort kommentaren till raden med `trust_bundle_cert` parametern och uppdatera värdet med fil-URI-sökvägen till rot certifikat utfärdarens certifikat som delas av alla enheter i Gateway-hierarkin.

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. Hitta **etablerings** avsnittet i filen. Ta bort kommentarer till raderna för **manuell etablering med anslutnings strängen**. Uppdatera värdet för **connection_string** med anslutnings strängen från IoT Edge-enheten för varje enhet i hierarkin.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. Hitta standard sektionen för **Edge-agenten** .

   * Uppdatera värdet för edgeAgent-avbildningen till den offentliga för hands versionen av modulen i Azure Container Registry för din enhet på **högsta nivån** .
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * Uppdatera edgeAgent-avbildningen för varje IoT Edge enhet i **lägre lager** så att den pekar på den överordnade enheten följt av porten som API-proxyn lyssnar på. Du kommer att distribuera API-proxy-modulen till den överordnade enheten i nästa avsnitt.
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. Hitta avsnittet **Edge CA-certifikat** . Ta bort kommentarer till de tre första raderna i det här avsnittet. Sedan uppdaterar du de två parametrarna så att de pekar på enhetens CA-certifikat och privata nyckelfiler för enhets certifikat som du skapade i föregående avsnitt och flyttade till den IoT Edge enheten. Ange fil-URI-sökvägar, som tar formatet `file:///<path>/<filename>` , till exempel `file:///certs/iot-edge-device-ca-top-layer-device.key.pem` .

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >Se till att du använder den **fullständiga kedjans** certifikat och fil namn för att fylla i `device_ca_cert` fältet.

1. Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

1. När du har angett etablerings informationen i konfigurations filen ska du använda ändringarna:

   ```bash
   sudo iotedge config apply
   ```

Innan du fortsätter kontrollerar du att du har uppdaterat konfigurations filen för varje enhet i hierarkin. Beroende på hierarkins struktur konfigurerade du en enhet på **översta nivån** och en eller flera **enheter med lägre lager**.

Om du har slutfört ovanstående steg korrekt kan du kontrol lera att enheterna är korrekt konfigurerade.

1. Kör konfigurations-och anslutnings kontrollerna på dina enheter:

   ```bash
   sudo iotedge check
   ```

På din **enhet på den översta nivån** förväntar du dig att se utdata med flera utvärderings resultat och minst en varning. Kontrollen för kommer att `latest security daemon` varna dig om att en annan IoT Edge version är den senaste stabila versionen eftersom IoT Edge version 1,2 finns i offentlig för hands version. Du kan se ytterligare varningar om loggar principer och, beroende på ditt nätverk, DNS-principer.

På en **enhet med ett lägre lager** förväntar du dig att se utdata som liknar det översta skiktet, men med ytterligare en varning som anger att EdgeAgent-modulen inte kan hämtas från överordnad enhet. Detta är acceptabelt, eftersom modulen IoT Edge-API-proxy och Docker Container Registry modul, som gör att lägre lager enheter hämtar bilder genom, inte har distribuerats till enheten på den **översta nivån**.

Ett exempel på utdata `iotedge check` visas nedan:

[Exempel på konfiguration och anslutnings resultat](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

När du är nöjd med att dina konfigurationer är korrekta på varje enhet är du redo att gå vidare.

## <a name="deploy-modules-to-the-top-layer-device"></a>Distribuera moduler till enheten på den översta nivån

Moduler för att slutföra distributionen och IoT Edge runtime till dina enheter och definiera en ytterligare struktur för hierarkin. Modulen IoT Edge-API-proxy säker routs HTTP-trafik över en enskild port från dina enheter med lägre lager. I Docker Registry-modulen får du en lagrings plats med Docker-avbildningar som de lägre lager enheterna kan komma åt genom att dirigera avbildningar till enheten på den översta nivån.

Om du vill distribuera moduler till enheten på den översta nivån kan du använda Azure Portal eller Azure CLI.

>[!NOTE]
>De återstående stegen för att slutföra konfigurationen av IoT Edge Runtime och distribuera arbets belastningar görs inte på dina IoT Edge enheter.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I [Azure Portal](https://ms.portal.azure.com/):

1. Navigera till din IoT Hub.

1. I menyn i den vänstra rutan, under **Automatisk enhets hantering**, väljer du **IoT Edge**.

1. Klicka på enhets-ID: t för den **översta lager** gränsen i listan över enheter.

1. I den övre stapeln väljer du **Ange moduler**.

1. Välj **körnings inställningar**, bredvid kugg hjuls ikonen.

1. Skriv i fältet bild under **Edge Hub** `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` .

   ![Redigera Edge Hub-bilden](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Lägg till följande miljövariabler i din Edge Hub-modul:

    | Name | Värde |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Redigera kant hubbens miljövariabler](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Skriv i fältet bild under **Edge-agent** `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4` . Välj **Spara**.

1. Lägg till Docker-Registry-modulen i enheten på den översta nivån. Välj **+ Lägg till** och välj **IoT Edge modul** i list rutan. Ange namnet `registry` på din Docker-register-modul och ange `registry:latest` för avbildnings-URI: n. Lägg sedan till miljövariabler och skapa alternativ för att peka din lokala register-modul i Microsoft container Registry för att hämta behållar avbildningar från och för att betjäna avbildningarna i registret: 5000.

1. Under fliken miljövariabler anger du följande Miljövariabelns namn-värde-par:

    | Name | Värde |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. Under fliken behållare skapa alternativ anger du följande JSON:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Lägg sedan till API-proxy-modulen till enheten på den översta nivån. Välj **+ Lägg till** och välj **Marketplace-modul** i list rutan. Sök efter `IoT Edge API Proxy` och välj modulen. IoT Edge API-proxyn använder port 8000 och har kon figurer ATS för att använda din register-modul med namnet `registry` port 5000 som standard.

1. Välj **Granska + skapa** och sedan **skapa** för att slutföra distributionen. Ditt översta lager enhets IoT Edge runtime, som har åtkomst till Internet, kommer att hämta och köra de **offentliga förhands gransknings** konfigurationerna för IoT Edge hub och IoT Edge agent.

   ![Slutför distribution med Edge Hub, Edge agent, Registry module och API proxy-modulen](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. I [Azure Cloud Shell](https://shell.azure.com/)anger du följande kommando för att skapa en deployment.jspå filen:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Kopiera innehållet i JSON nedan till din deployment.jspå, spara det och Stäng det.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "registry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                               } 
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Ange följande kommando för att skapa en distribution till din högsta lager gräns enhet:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

Om du har slutfört ovanstående steg korrekt bör **enheten i det högsta skiktet** rapportera de fyra modulerna: modulen IoT Edge-API-proxy, docker container Registry-modulen och systemmodulerna, som **anges i distributionen**. Det kan ta några minuter innan enheten tar emot sin nya distribution och startar modulerna. Uppdatera sidan tills du ser modulen temperatur sensor listad som **rapporteras av enheten**. När modulerna rapporter ATS av enheten är du redo att fortsätta.

## <a name="deploy-modules-to-the-lower-layer-device"></a>Distribuera moduler till den lägre lager enheten

Moduler fungerar också som arbets belastningar för de lägre lager enheterna. Modulen simulerad temperatur sensor skapar exempel på telemetridata för att tillhandahålla ett funktionellt data flöde genom din hierarki av enheter.

Om du vill distribuera moduler till de lägre lager enheterna kan du använda Azure Portal eller Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I [Azure Portal](https://ms.portal.azure.com/):

1. Navigera till din IoT Hub.

1. I menyn i den vänstra rutan, under **Automatisk enhets hantering**, väljer du **IoT Edge**.

1. Klicka på enhets-ID: t för den lägre lager enheten i listan över IoT Edge enheter.

1. I den övre stapeln väljer du **Ange moduler**.

1. Välj **körnings inställningar**, bredvid kugg hjuls ikonen.

1. Skriv i fältet bild under **Edge Hub** `$upstream:8000/azureiotedge-hub:1.2.0-rc4` .

1. Lägg till följande miljövariabler i din Edge Hub-modul:

    | Name | Värde |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Skriv i fältet bild under **Edge-agent** `$upstream:8000/azureiotedge-agent:1.2.0-rc4` . Välj **Spara**.

1. Lägg till modulen temperatur sensor. Välj **+ Lägg till** och välj **Marketplace-modul** i list rutan. Sök efter `Simulated Temperature Sensor` och välj modulen.

1. Under **IoT Edge moduler** väljer du den `Simulated Temperature Sensor` modul som du nyss lade till och uppdaterar dess bild-URI så att den pekar på `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0` .

1. Välj **Spara**, **Granska + skapa** och **skapa** för att slutföra distributionen.

   ![Slutför distribution med Edge Hub, Edge agent och simulerad temperatur sensor](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. I [Azure Cloud Shell](https://shell.azure.com/)anger du följande kommando för att skapa en deployment.jspå filen:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Kopiera innehållet i JSON nedan till din deployment.jspå, spara det och Stäng det.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Ange följande kommando för att skapa en uppsättning moduler distribution till den lägre lager gränsen:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

`azureiotedge-diagnostics`Värdet hämtas från behållar registret som är länkat till register-modulen. Den här självstudien har den angetts som standard till https://mcr.microsoft.com:

| Name | Värde |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Om du använder ett privat behållar register, se till att alla avbildningar (till exempel IoTEdgeAPIProxy, edgeAgent, edgeHub och diagnostik) finns i behållar registret.

## <a name="view-generated-data"></a>Visa genererade data

Modulen **simulerad temperatur sensor** som du har överfört genererar exempel miljö data. Den skickar meddelanden som innehåller omgivande temperatur och fukt, maskin temperatur och tryck och en tidsstämpel.

Du kan titta på meddelandena i IoT Hub med hjälp av [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Du kan också visa dessa meddelanden via [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de lokala konfigurationerna och de Azure-resurser som du skapade i den här artikeln för att undvika avgifter.

Ta bort resurser:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

2. Välj namnet på resursgruppen som innehåller dina IoT Edge-testresurser. 

3. Granska listan över resurser som ingår i din resursgrupp. Om du vill ta bort alla kan du välja **Ta bort resursgrupp**. Om du bara vill ta bort några av dem kan du klicka i varje resurs och ta bort dem individuellt. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du två IoT Edge enheter som gatewayer och anger en som överordnad enhet för den andra. Sedan har du påvisat hämtningen av en behållar avbildning till den underordnade enheten via en gateway.

Om du vill se hur Azure IoT Edge kan skapa flera lösningar för ditt företag fortsätter du med de andra självstudiekurserna.

> [!div class="nextstepaction"]
> [Distribuera en Azure Machine Learning-modell som en modul](tutorial-deploy-machine-learning.md)
