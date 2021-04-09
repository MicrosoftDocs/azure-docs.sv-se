---
title: Anslut underordnade IoT Edge enheter – Azure IoT Edge | Microsoft Docs
description: Så här konfigurerar du en IoT Edge-enhet för att ansluta till Azure IoT Edge gateway-enheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 382cdf87016044748685e5e64ff04ebac53f018d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199145"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>Ansluta en underordnad IoT Edge enhet till en Azure IoT Edge Gateway (förhands granskning)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Den här artikeln innehåller instruktioner för att upprätta en betrodd anslutning mellan en IoT Edge gateway och en underordnad IoT Edge enhet.

>[!NOTE]
>Den här funktionen kräver IoT Edge version 1,2, som finns i offentlig för hands version, som kör Linux-behållare.
>
>Den här artikeln visar den senaste för hands versionen av IoT Edge version 1,2. Kontrol lera att enheten kör version [1.2.0-RC4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc4) eller senare. Anvisningar för hur du hämtar den senaste för hands versionen på enheten finns i [installera Azure IoT Edge för Linux (version 1,2)](how-to-install-iot-edge.md) eller [Uppdatera IoT Edge till version 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

I ett Gateway-scenario kan en IoT Edge enhet vara både en gateway och en underordnad enhet. Flera IoT Edge gatewayer kan skiktas för att skapa en hierarki med enheter. Underordnade (eller underordnade) enheter kan autentisera och skicka eller ta emot meddelanden via deras Gateway (eller överordnade) enhet.

Det finns två olika konfigurationer för IoT Edge enheter i en gateway-hierarki, och den här artikeln innehåller båda. Det första är det **översta lagret** IoT Edge-enheten. När flera IoT Edge enheter ansluter via varandra anses alla enheter som inte har en överordnad enhet, men som ansluter direkt till IoT Hub, vara i det översta lagret. Den här enheten ansvarar för att hantera begär Anden från alla enheter under den. Den andra konfigurationen gäller för alla IoT Edge enheter i ett **lägre lager** i hierarkin. Dessa enheter kan vara en gateway för andra underordnad IoT och IoT Edge enheter, men du måste också dirigera all kommunikation via sina egna överordnade enheter.

Vissa nätverks arkitekturer kräver att endast den översta IoT Edges enheten i en hierarki kan ansluta till molnet. I den här konfigurationen kan alla IoT Edge enheter i lägre lager i en hierarki endast kommunicera med deras gateway-eller överordnad enhet (eller underordnade) enheter.

Alla steg i den här artikeln bygger på dem i [Konfigurera en IoT Edge-enhet som fungerar som en transparent Gateway](how-to-create-transparent-gateway.md), som konfigurerar en IoT Edge-enhet som en gateway för underordnade IoT-enheter. Samma grundläggande steg gäller för alla Gateway-scenarier:

* **Autentisering**: skapa IoT Hub identiteter för alla enheter i Gateway-hierarkin.
* **Auktorisering**: Konfigurera den överordnade/underordnade relationen i IoT Hub att auktorisera underordnade enheter att ansluta till den överordnade enheten, till exempel att de ansluter till IoT Hub.
* **Gateway-identifiering**: kontrol lera att den underordnade enheten kan hitta sin överordnade enhet i det lokala nätverket.
* **Säker anslutning**: upprätta en säker anslutning med betrodda certifikat som ingår i samma kedja.

## <a name="prerequisites"></a>Förutsättningar

* En kostnads fri eller standard IoT-hubb.
* Minst två **IoT Edge enheter**, en som ska vara det översta lager enheten och en eller flera enheter med lägre lager. Om du inte har IoT Edge tillgängliga enheter kan du [köra Azure IoT Edge på virtuella Ubuntu-datorer](how-to-install-iot-edge-ubuntuvm.md).
* Om du använder Azure CLI för att skapa och hantera enheter måste du ha Azure CLI v 2.3.1 med Azure IoT Extension v-0.10.6 eller senare installerat.

Den här artikeln innehåller detaljerade steg och alternativ som hjälper dig att skapa rätt Gateway-hierarki för ditt scenario. En guidad självstudie finns i [skapa en hierarki med IoT Edge enheter med hjälp av gatewayer](tutorial-nested-iot-edge.md).

## <a name="create-a-gateway-hierarchy"></a>Skapa en gateway-hierarki

Du skapar en IoT Edge Gateway-hierarki genom att definiera överordnade/underordnade relationer för de IoT Edge enheterna i scenariot. Du kan ange en överordnad enhet när du skapar en ny enhets identitet, eller så kan du hantera de överordnade och underordnade objekten för en befintlig enhets identitet.

Steget för att ställa in överordnade/underordnade-relationer tillåter att underordnade enheter ansluter till den överordnade enheten, till exempel att de ansluter till IoT Hub.

Endast IoT Edge enheter kan vara överordnade enheter, men både IoT Edge enheter och IoT-enheter kan vara underordnade. En överordnad kan ha många underordnade objekt, men ett underordnat objekt kan bara ha en överordnad. En gateway-hierarki skapas genom länkning av överordnade/underordnade uppsättningar, så att den underordnade enheten är överordnad en annan.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Portal kan du hantera den överordnade/underordnade relationen när du skapar nya enhets identiteter, eller genom att redigera befintliga enheter.

När du skapar en ny IoT Edge enhet har du möjlighet att välja överordnade och underordnade enheter i listan över befintliga IoT Edge enheter i hubben.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din IoT Hub.
1. Välj **IoT Edge** på navigerings menyn.
1. Välj **Lägg till en IoT Edge enhet**.
1. Förutom att ange enhets-ID och autentiseringsinställningar kan du **Ange en överordnad enhet** eller **välja underordnade enheter**.
1. Välj den enhet eller de enheter som du vill använda som överordnad eller underordnad.

Du kan också skapa eller hantera överordnade/underordnade relationer för befintliga enheter.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din IoT Hub.
1. Välj **IoT Edge** på navigerings menyn.
1. Välj den enhet som du vill hantera i listan med **IoT Edge enheter**.
1. Välj **Ange en överordnad enhet** eller **Hantera underordnade enheter**.
1. Lägg till eller ta bort alla överordnade eller underordnade enheter.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure-IoT-](/cli/azure/ext/azure-iot) tillägget för Azure CLI innehåller kommandon för att hantera dina IoT-resurser. Du kan hantera den överordnade/underordnade relationen mellan IoT och IoT Edge enheter när du skapar nya enhets identiteter eller redigerar befintliga enheter.

Med [AZ IoT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) set kan du hantera överordnade/underordnade relationer för en angiven enhet.

`create`Kommandot innehåller parametrar för att lägga till underordnade enheter och ange en överordnad enhet när enheten skapas.

Ytterligare kommandon för enhets identitet, inklusive `add-children` , `list-children` och `remove-children` eller `get-parent` och `set-parent` , låter dig hantera överordnade/underordnade relationer för befintliga enheter.

---

## <a name="prepare-certificates"></a>Förbereda certifikat

En konsekvent kedja av certifikat måste installeras mellan enheter i samma Gateway-hierarki för att upprätta en säker kommunikation sinsemellan. Varje enhet i hierarkin, om en IoT Edge enhet eller en IoT-lövnod, behöver en kopia av samma rot certifikat för certifikat utfärdaren. Varje IoT Edge enhet i hierarkin använder sedan det rot certifikat utfärdarens certifikat som rot för enhetens CA-certifikat.

Med den här installationen kan varje underordnad IoT Edge enhet eller IoT-lövnod verifiera identiteten för deras överordnade genom att kontrol lera att de edgeHub som de ansluter till har ett Server certifikat som är signerat av det delade rot certifikat utfärdaren.

<!-- TODO: certificate graphic -->

Skapa följande certifikat:

* Ett **certifikat från en rot certifikat utfärdare**, vilket är det översta delade certifikatet för alla enheter i en specifik Gateway-hierarki. Det här certifikatet installeras på alla enheter.
* Alla **mellanliggande certifikat** som du vill inkludera i rot certifikat kedjan.
* Ett certifikat för **enhets certifikat** och dess **privata nyckel** som genereras av rot-och mellanliggande certifikat. Du behöver ett unikt enhets certifikat för varje IoT Edge enhet i Gateway-hierarkin.

Du kan antingen använda en självsignerad certifikat utfärdare eller köpa en från en betrodd kommersiell certifikat utfärdare som Baltimore, VeriSign, DigiCert eller GlobalSign.

Om du inte har dina egna certifikat att använda kan du [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md). Följ stegen i artikeln för att skapa en uppsättning rot-och mellanliggande certifikat och skapa sedan IoT Edge enhetens CA-certifikat för var och en av dina enheter.

## <a name="configure-iot-edge-on-devices"></a>Konfigurera IoT Edge på enheter

Stegen för att konfigurera IoT Edge som en gateway liknar stegen för att ställa in IoT Edge som en underordnad enhet.

Om du vill aktivera Gateway-identifiering måste varje IoT Edge gateway-enhet konfigureras med ett **värdnamn** som de underordnade enheterna ska använda för att hitta den i det lokala nätverket. Varje underordnad IoT Edge enhet måste konfigureras med en **parent_hostname** för att ansluta till. Om en enskild IoT Edge enhet är både en överordnad och en underordnad enhet behöver den båda parametrarna.

För att aktivera säkra anslutningar måste varje IoT Edge enhet i ett Gateway-scenario konfigureras med ett unikt enhets certifikat för certifikat utfärdare och en kopia av rot certifikat utfärdarens certifikat som delas av alla enheter i Gateway-hierarkin.

Du bör redan ha IoT Edge installerat på enheten. Annars följer du stegen för att [Registrera en IoT Edge-enhet i IoT Hub](how-to-register-device.md) och [installerar sedan Azure IoT Edge runtime](how-to-install-iot-edge.md).

Stegen i det här avsnittet hänvisar till **rot certifikat utfärdarens certifikat** och **enhetens CA-certifikat och den privata nyckeln** som diskuterades tidigare i den här artikeln. Om du har skapat dessa certifikat på en annan enhet, måste de vara tillgängliga på den här enheten. Du kan överföra filerna fysiskt, t. ex. med en USB-enhet, med en tjänst som [Azure Key Vault](../key-vault/general/overview.md)eller med en funktion som [säker fil kopiering](https://www.ssh.com/ssh/scp/).

Använd följande steg för att konfigurera IoT Edge på enheten.

Se till att användar **iotedge** har Läs behörighet för katalogen som innehåller certifikaten och nycklarna.

1. Installera **rot certifikat utfärdarens certifikat** på den här IoT Edges enheten.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Uppdatera certifikat arkivet.

   ```bash
   sudo update-ca-certificates
   ```

   Det här kommandot ska mata ut ett certifikat som har lagts till i/etc/ssl/certs.

1. Öppna konfigurationsfilen för IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >Om konfigurations filen inte finns på enheten ännu använder du `/etc/aziot/config.toml.edge.template` som mall för att skapa en.

1. Hitta **hostname** -avsnittet i konfigurations filen. Ta bort kommentaren till raden som innehåller `hostname` parametern och uppdatera värdet så att det är det fullständigt kvalificerade domän namnet (FQDN) eller IP-adressen för den IoT Edge enheten.

   Värdet för den här parametern är det som underordnade enheter kommer att använda för att ansluta till den här gatewayen. Värd namnet tar dator namnet som standard, men FQDN eller IP-adress krävs för att ansluta underordnade enheter.

   Använd ett värdnamn som är kortare än 64 tecken, vilket är tecken gränsen för ett nätverks namn för Server certifikat.

   Var konsekvent med värd namns mönstret i en gateway-hierarki. Använd antingen FQDN eller IP-adresser, men inte båda.

1. *Om enheten är en underordnad enhet* hittar du avsnittet **överordnat värdnamn** . Ta bort kommentaren och uppdatera `parent_hostname` parametern så att den är FQDN eller IP-adress för den överordnade enheten, vilket matchar det som angavs som värdnamn i den överordnade enhetens konfigurations fil.

1. Hitta avsnittet **TRUSTe Bundle cert** . Ta bort kommentaren och uppdatera `trust_bundle_cert` parametern med fil-URI: n till rot certifikat utfärdarens certifikat på enheten.

1. Även om den här funktionen finns i en offentlig för hands version måste du konfigurera din IoT Edge-enhet så att den använder den offentliga för hands versionen av IoT Edge agenten när den startas.

   Hitta standardvärdet för **Edge-agenten** och uppdatera avbildning svärdet till den offentliga förhands gransknings bilden:

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
   ```

1. Hitta avsnittet **Edge CA-certifikat** i konfigurations filen. Ta bort kommentarer till raderna i det här avsnittet och ange fil-URI-sökvägar för certifikatet och nyckelfilen på den IoT Edge enheten.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Spara ( `Ctrl+O` ) och Stäng ( `Ctrl+X` ) i konfigurations filen.

1. Om du har använt andra certifikat för IoT Edge tidigare, tar du bort filerna i följande två kataloger för att se till att dina nya certifikat tillämpas:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. Spara ändringarna.

   ```bash
   sudo iotedge config apply
   ```

1. Sök efter fel i konfigurationen.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >I IoT Edges kontroll verktyget används en behållare för att utföra en del av diagnostisk kontroll. Om du vill använda det här verktyget på underordnade IoT Edge enheter kontrollerar du att de kan komma åt `mcr.microsoft.com/azureiotedge-diagnostics:latest` eller har behållar avbildningen i ditt privata behållar register.

## <a name="configure-runtime-modules-for-public-preview"></a>Konfigurera Runtime-moduler för offentlig för hands version

Även om den här funktionen finns i en offentlig för hands version måste du konfigurera IoT Edge-enheten så att den använder de offentliga för hands versionerna av IoT Edge runtime-modulerna. I föregående avsnitt finns steg för att konfigurera edgeAgent vid start. Du måste också konfigurera körnings modulerna i distributioner för enheten.

1. Konfigurera edgeHub-modulen så att den använder den offentliga förhands gransknings bilden: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` .

1. Konfigurera följande miljövariabler för edgeHub-modulen:

   | Name | Värde |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Konfigurera edgeAgent-modulen så att den använder den offentliga förhands gransknings bilden: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` .

## <a name="network-isolate-downstream-devices"></a>Isolerade enheter i nätverket

Anvisningarna i den här artikeln ställer in IoT Edge enheter som en gateway eller en underordnad enhet och skapar en betrodd anslutning mellan dem. Gateway-enheten hanterar interaktioner mellan den underordnade enheten och IoT Hub, inklusive autentisering och meddelanderoutning. Moduler som distribueras till underordnade IoT Edge enheter kan fortfarande skapa egna anslutningar till moln tjänster.

Vissa nätverks arkitekturer, t. ex. sådana som följer ISA-95-standarden, söker för att minimera antalet Internet anslutningar. I dessa scenarier kan du konfigurera underordnade IoT Edge enheter utan direkt Internet anslutning. Utöver routning IoT Hub kommunikation via sin gateway-enhet kan underordnade IoT Edge enheter förlita sig på gateway-enheten för alla moln anslutningar.

Den här nätverks konfigurationen kräver att endast IoT Edges enheten i det övre lagret i en gateway-hierarki har direkt anslutningar till molnet. IoT Edge enheter i de nedre lagren kan bara kommunicera med sina överordnade enheter eller underordnade enheter. Särskilda moduler på gateway-enheter möjliggör det här scenariot, inklusive:

* **Modulen API-proxy** krävs på alla IoT Edge-gatewayer som har en annan IoT Edge enhet under den. Det innebär att det måste finnas på *varje lager* i en gateway-hierarki förutom det understa lagret. I den här modulen används en [nginx](https://nginx.org) omvänd proxy för att dirigera http-data via nätverks lager över en enskild port. Det går starkt att konfigurera via dess modul med dubbla och miljövariabler, så kan justeras så att de passar dina krav för gateway-scenario.

* **Docker-Registry-modulen** kan distribueras på IoT Edge-gatewayen i det *övre lagret* i en gateway-hierarki. Den här modulen ansvarar för att hämta och cachelagra behållar avbildningar på uppdrag av alla IoT Edge enheter i lägre lager. Alternativet att distribuera den här modulen på det översta lagret är att använda ett lokalt register eller att manuellt läsa in behållar avbildningar till enheter och ställa in modulens pull-princip på **aldrig**.

* **Azure-Blob Storage på IoT Edge** kan distribueras på IoT Edge-gatewayen i det *övre skiktet* i en gateway-hierarki. Den här modulen ansvarar för överföring av blobbar för alla IoT Edge enheter i lägre lager. Möjligheten att ladda upp blobbar möjliggör också användbara fel söknings funktioner för IoT Edge enheter i lägre lager, t. ex. för modul logg uppladdning och uppladdning av support paket.

### <a name="network-configuration"></a>Konfiguration av nätverk

För varje gateway-enhet på det översta lagret måste nätverks operatörerna:

* Ange en statisk IP-adress eller ett fullständigt kvalificerat domän namn (FQDN).
* Auktorisera utgående kommunikation från den här IP-adressen till din Azure IoT Hub hostname över portarna 443 (HTTPS) och 5671 (AMQP).
* Auktorisera utgående kommunikation från den här IP-adressen till din Azure Container Registry-värdnamn via port 443 (HTTPS).

  API-proxy-modulen kan bara hantera anslutningar till ett behållar register i taget. Vi rekommenderar att du har alla behållar avbildningar, inklusive de offentliga avbildningar som tillhandahålls av Microsoft Container Registry (mcr.microsoft.com), som lagras i ditt privata behållar register.

För varje gateway-enhet i ett lägre lager måste nätverks operatörerna:

* Ange en statisk IP-adress.
* Auktorisera utgående kommunikation från den här IP-adressen till den överordnade gatewayens IP-adress över portarna 443 (HTTPS) och 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Distribuera moduler till översta lager enheter

Den IoT Edge enheten på det övre lagret i en gateway-hierarki har en uppsättning nödvändiga moduler som måste distribueras till den, förutom eventuella arbets belastnings moduler som du kan köra på enheten.

API-proxy modulen har utformats för att anpassas för att hantera de flesta vanliga Gateway-scenarier. Den här artikeln innehåller och exempel på hur du ställer in modulerna i en grundläggande konfiguration. Mer detaljerad information och exempel hittar du i [Konfigurera API-proxy-modulen för scenariot för gateway-hierarkin](how-to-configure-api-proxy-module.md) .

1. I [Azure Portal](https://portal.azure.com)navigerar du till din IoT Hub.
1. Välj **IoT Edge** på navigerings menyn.
1. Välj den översta lager enhet som du konfigurerar från listan över **IoT Edge enheter**.
1. Välj **Ange moduler**.
1. I avsnittet **IoT Edge moduler** väljer du **Lägg till** och sedan **Marketplace-modul**.
1. Sök efter och välj modulen **IoT Edge-API-proxy** .
1. Välj namnet på API-proxy-modulen i listan över distribuerade moduler och uppdatera följande inställningar för modul:
   1. På fliken **miljövariabler** uppdaterar du värdet för **NGINX_DEFAULT_PORT** till `443` .
   1. På fliken **behållare skapa alternativ** uppdaterar du Port bindningarna till referens port 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Dessa ändringar konfigurerar API-proxy-modulen att lyssna på port 443. För att förhindra port bindnings kollisioner måste du konfigurera edgeHub-modulen så att den inte lyssnar på port 443. I stället dirigerar API proxy-modulen all edgeHub-trafik på port 443.

1. Välj **körnings inställningar** och Sök efter edgeHub-modulen för att skapa alternativ. Ta bort port bindningen för port 443, och lämna bindningarna för portarna 5671 och 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Spara ändringarna i körnings inställningarna genom att välja **Spara** .
1. Välj **Lägg till** igen och välj sedan **IoT Edge modul**.
1. Ange följande värden för att lägga till Docker-Registry-modulen i distributionen:
   1. **Namn på IoT Edge modul**: `registry`
   1. På fliken **Modulnamn** , bild- **URI**: `registry:latest`
   1. På fliken **miljövariabler** lägger du till följande miljövariabler:

      * **Name**: `REGISTRY_PROXY_REMOTEURL` **värde**: URL: en för det behållar register som du vill att den här Registry-modulen ska mappa till. Till exempel `https://myregistry.azurecr`.

        Registret kan bara mappas till ett behållar register, så vi rekommenderar att du har alla behållar avbildningar i ett enda privat behållar register.

      * **Namn**: `REGISTRY_PROXY_USERNAME` **värde**: användar namn att autentisera till behållar registret.

      * **Namn**: `REGISTRY_PROXY_PASSWORD` **värde**: lösen ord för att autentisera till behållar registret.

   1. På fliken **behållare skapa alternativ** , klistra in:

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

1. Välj **Lägg till** för att lägga till modulen i distributionen.
1. Välj **Nästa: vägar** för att gå vidare till nästa steg.
1. Om du vill aktivera meddelanden från enheten till molnet från efterföljande enheter för att uppnå IoT Hub, lägger du till en väg som skickar alla meddelanden till IoT Hub. Exempel:
    1. **Namn**: `Route`
    1. **Värde**: `FROM /messages/* INTO $upstream`
1. Välj **Granska + skapa** för att gå till det sista steget.
1. Välj **skapa** för att distribuera till din enhet.

### <a name="deploy-modules-to-lower-layer-devices"></a>Distribuera moduler till lägre lager enheter

IoT Edge enheter på lägre lager i en gateway-hierarki har en nödvändig modul som måste distribueras till dem, förutom eventuella arbets belastnings moduler som du kan köra på enheten.

#### <a name="route-container-image-pulls"></a>Väg container image-hämtningar

Innan du diskuterar den nödvändiga proxy-modulen för IoT Edge enheter i Gateway-hierarkier, är det viktigt att förstå hur IoT Edge enheter i lägre lager hämtar sina modulblad.

Om de lägre lager enheterna inte kan ansluta till molnet, men du vill att de ska hämta bilder som vanligt, måste den översta lager enheten i Gateway-hierarkin konfigureras för att hantera dessa begär Anden. Enheten på den översta nivån måste köra en Docker **-kontrollmodul som** är mappad till behållar registret. Konfigurera sedan API-proxy-modulen för att dirigera container begär anden till den. Informationen beskrivs i föregående avsnitt i den här artikeln. I den här konfigurationen ska de lägre skikt enheterna inte peka på moln behållar register, utan till registret som körs i det översta lagret.

Till exempel, i stället för att anropa `mcr.microsoft.com/azureiotedge-api-proxy:latest` , ska lägre lager enheter anropa `$upstream:443/azureiotedge-api-proxy:latest` .

**$Upstream** -parametern pekar på en överordnad enhet som är en enhet med en lägre nivå, så att begäran dirigerar över alla lager tills den når det översta lagret som har en proxy-miljö som dirigerar behållare till registret. `:443`Porten i det här exemplet ska ersättas med den port som API-proxy-modulen på den överordnade enheten lyssnar på.

API-proxy-modulen kan bara dirigera till en register-modul och varje register-modul kan bara mappas till ett behållar register. Därför måste alla bilder som har lägre lager enheter vara lagrade i ett enda behållar register.

Om du inte vill att lägre lager enheter ska skapa pull-begäranden via en gateway-hierarki, är ett annat alternativ att hantera en lokal register lösning. Du kan också push-placera modulerna på enheterna innan du skapar distributioner och sedan ange **imagePullPolicy** till **aldrig**.

#### <a name="bootstrap-the-iot-edge-agent"></a>Starta IoT Edge agent

IoT Edge agent är den första körnings komponenten som startar på alla IoT Edge enheter. Du måste se till att alla underordnade IoT Edge enheter kan komma åt edgeAgent-modulens avbildning när de startar, och sedan kan de komma åt distributioner och starta resten av modulens avbildningar.

När du går till konfigurations filen på en IoT Edge-enhet för att tillhandahålla autentiseringsinformation, certifikat och överordnat värdnamn, uppdaterar också behållar avbildningen för edgeAgent.

Om gateway-enheten på den översta nivån har kon figurer ATS för att hantera behållar avbildnings begär Anden, ersätter `mcr.microsoft.com` du med den överordnade värdnamn-och API-proxy I distributions manifestet kan du använda `$upstream` som en genväg, men det kräver att edgeHub-modulen hanterar Routning och att modulen inte har startats i det här läget. Exempel:

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc4"
```

Om du använder ett lokalt behållar register eller om du tillhandahåller behållar avbildningarna manuellt på enheten uppdaterar du konfigurations filen på motsvarande sätt.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Konfigurera Runtime och distribuera Proxy-modulen

**API-proxy-modulen** krävs för att dirigera all kommunikation mellan molnet och eventuella underordnade IoT Edge enheter. En IoT Edge enhet i det nedre lagret i hierarkin, utan några underordnade IoT Edge enheter, behöver inte den här modulen.

API-proxy modulen har utformats för att anpassas för att hantera de flesta vanliga Gateway-scenarier. Den här artikeln beskriver kortfattat stegen för att ställa in modulerna i en grundläggande konfiguration. Mer detaljerad information och exempel hittar du i [Konfigurera API-proxy-modulen för scenariot för gateway-hierarkin](how-to-configure-api-proxy-module.md) .

1. I [Azure Portal](https://portal.azure.com)navigerar du till din IoT Hub.
1. Välj **IoT Edge** på navigerings menyn.
1. Välj den lägre lager enhet som du konfigurerar i listan över **IoT Edge enheter**.
1. Välj **Ange moduler**.
1. I avsnittet **IoT Edge moduler** väljer du **Lägg till** och sedan **Marketplace-modul**.
1. Sök efter och välj modulen **IoT Edge-API-proxy** .
1. Välj namnet på API-proxy-modulen i listan över distribuerade moduler och uppdatera följande inställningar för modul:
   1. På fliken **Modulnamn** uppdaterar du värdet för **bild-URI**. Ersätt `mcr.microsoft.com` med `$upstream:443`.
   1. På fliken **miljövariabler** uppdaterar du värdet för **NGINX_DEFAULT_PORT** till `443` .
   1. På fliken **behållare skapa alternativ** uppdaterar du Port bindningarna till referens port 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Dessa ändringar konfigurerar API-proxy-modulen att lyssna på port 443. För att förhindra port bindnings kollisioner måste du konfigurera edgeHub-modulen så att den inte lyssnar på port 443. I stället dirigerar API proxy-modulen all edgeHub-trafik på port 443.

1. Välj **körnings inställningar**.
1. Uppdatera inställningarna för edgeHub-modulen:

   1. Ersätt med i fältet **bild** `mcr.microsoft.com` `$upstream:443` .
   1. Ta bort port bindningen för port 443 i fältet **skapa alternativ** och lämna bindningarna för portarna 5671 och 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Uppdatera inställningarna för edgeAgent-modulen:
   1. Ersätt med i fältet **bild** `mcr.microsoft.com` `$upstream:443` .

1. Spara ändringarna i körnings inställningarna genom att välja **Spara** .
1. Välj **Nästa: vägar** för att gå vidare till nästa steg.
1. Om du vill aktivera meddelanden från enheten till molnet från efterföljande enheter för att uppnå IoT Hub, lägger du till en väg som skickar alla meddelanden till `$upstream` . Den överordnade parametern pekar på den överordnade enheten när det gäller lägre lager enheter. Exempel:
    1. **Namn**: `Route`
    1. **Värde**: `FROM /messages/* INTO $upstream`
1. Välj **Granska + skapa** för att gå till det sista steget.
1. Välj **skapa** för att distribuera till din enhet.

## <a name="next-steps"></a>Nästa steg

[Så kan en IoT Edge-enhet användas som gateway](iot-edge-as-gateway.md)

[Konfigurera API-proxy-modulen för scenariot för gateway-hierarkin](how-to-configure-api-proxy-module.md)