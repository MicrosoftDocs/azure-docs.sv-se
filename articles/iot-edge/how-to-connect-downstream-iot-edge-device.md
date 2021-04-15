---
title: Ansluta nedströmsenheter IoT Edge enheter – Azure IoT Edge | Microsoft Docs
description: Så här konfigurerar du en IoT Edge att ansluta till Azure IoT Edge gatewayenheter.
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
ms.openlocfilehash: 500833d1bb4fc492942c08239bd488c2d2c16d30
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484327"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway"></a>Ansluta en underordnad IoT Edge enhet till en Azure IoT Edge gateway

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Den här artikeln innehåller instruktioner för att upprätta en betrodd anslutning mellan en IoT Edge-gateway och en underordnad IoT Edge enhet.

I ett gatewayscenario kan en IoT Edge enhet vara både en gateway och en underordnad enhet. Flera IoT Edge gateways kan skiktas för att skapa en hierarki med enheter. Underordnade enheter kan autentisera och skicka eller ta emot meddelanden via sin gatewayenhet (eller överordnade).

Det finns två olika konfigurationer för IoT Edge enheter i en gatewayhierarki, och den här artikeln behandlar båda. Den första är det **översta lagret IoT Edge** enhet. När flera IoT Edge enheter ansluter via varandra anses alla enheter som inte har en överordnad enhet men som ansluter direkt till IoT Hub vara i det översta lagret. Den här enheten ansvarar för att hantera begäranden från alla enheter under den. Den andra konfigurationen gäller för alla IoT Edge enheter i **ett lägre lager** i hierarkin. Dessa enheter kan vara en gateway för andra underordnade IoT- och IoT Edge-enheter, men måste också dirigera all kommunikation via sina egna överordnade enheter.

Vissa nätverksarkitekturer kräver att endast den översta IoT Edge i en hierarki kan ansluta till molnet. I den här konfigurationen kan IoT Edge enheter i lägre lager i en hierarki endast kommunicera med sin gatewayenhet (eller överordnade) enhet och eventuella underordnade (eller underordnade) enheter.

Alla steg i den här artikeln bygger på stegen i Konfigurera en [IoT Edge-enhet](how-to-create-transparent-gateway.md)för att fungera som en transparent gateway , som konfigurerar en IoT Edge-enhet som en gateway för underordnade IoT-enheter. Samma grundläggande steg gäller för alla gateway-scenarier:

* **Autentisering:** Skapa IoT Hub identiteter för alla enheter i gatewayhierarkin.
* **Auktorisering:** Konfigurera den överordnade/underordnade relationen i IoT Hub att auktorisera underordnade enheter att ansluta till sin överordnade enhet på samma sätt som de ansluter till IoT Hub.
* **Gatewayidentifiering:** Kontrollera att den underordnade enheten kan hitta sin överordnade enhet i det lokala nätverket.
* **Säker anslutning:** Upprätta en säker anslutning med betrodda certifikat som ingår i samma kedja.

## <a name="prerequisites"></a>Förutsättningar

* En kostnadsfri IoT-hubb eller standardhubb.
* Minst två enheter **IoT Edge ,** en som ska vara enheten på översta lagret och en eller flera enheter med lägre lager. Om du inte har några tillgängliga IoT Edge kan du köra Azure IoT Edge [på virtuella Ubuntu-datorer.](how-to-install-iot-edge-ubuntuvm.md)
* Om du använder Azure CLI för att skapa och hantera enheter ska du ha Azure CLI v2.3.1 med Azure IoT-tillägget v0.10.6 eller senare installerat.

Den här artikeln innehåller detaljerade steg och alternativ som hjälper dig att skapa rätt gatewayhierarki för ditt scenario. En guidad självstudie finns i Skapa [en hierarki med IoT Edge enheter med hjälp av gatewayer](tutorial-nested-iot-edge.md).

## <a name="create-a-gateway-hierarchy"></a>Skapa en gatewayhierarki

Du skapar en IoT Edge gateway-hierarki genom att definiera överordnade/underordnade relationer för IoT Edge enheter i scenariot. Du kan ange en överordnad enhet när du skapar en ny enhetsidentitet, eller så kan du hantera över- och underordnade till en befintlig enhetsidentitet.

Steget för att konfigurera överordnade/underordnade relationer gör att underordnade enheter kan ansluta till sin överordnade enhet på samma sätt som de ansluter till IoT Hub.

Endast IoT Edge enheter kan vara överordnade enheter, men både IoT Edge och IoT-enheter kan vara underordnade. En överordnad kan ha många underordnade objekt, men ett underordnat kan bara ha en överordnad. En gatewayhierarki skapas genom länkning av överordnade/underordnade uppsättningar så att den underordnade enheten är överordnad till en annan.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Portal kan du hantera den överordnade/underordnade relationen när du skapar nya enhetsidentiteter eller genom att redigera befintliga enheter.

När du skapar en IoT Edge enhet kan du välja överordnade och underordnade enheter från listan över befintliga enheter IoT Edge i hubben.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din IoT-hubb.
1. Välj **IoT Edge** på navigeringsmenyn.
1. Välj **Lägg till en IoT Edge enhet**.
1. Förutom att ange inställningar för enhets-ID och autentisering kan du **ange en överordnad enhet eller** Välj underordnade **enheter.**
1. Välj den eller de enheter som du vill använda som överordnad eller underordnad enhet.

Du kan också skapa eller hantera överordnade/underordnade relationer för befintliga enheter.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din IoT-hubb.
1. Välj **IoT Edge** på navigeringsmenyn.
1. Välj den enhet som du vill hantera i listan över **IoT Edge enheter.**
1. Välj **Ange en överordnad enhet eller** Hantera underordnade **enheter.**
1. Lägg till eller ta bort överordnade eller underordnade enheter.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure-iot-tillägget](/cli/azure/iot) för Azure CLI innehåller kommandon för att hantera dina IoT-resurser. Du kan hantera den överordnade/underordnade relationen mellan IoT och IoT Edge när du skapar nya enhetsidentiteter eller genom att redigera befintliga enheter.

Med [az iot hub device-identity-uppsättningen](/cli/azure/iot/hub/device-identity) kommandon kan du hantera överordnade/underordnade relationer för en viss enhet.

Kommandot `create` innehåller parametrar för att lägga till underordnade enheter och ange en överordnad enhet när enheten skapas.

Ytterligare enhetsidentitetskommandon, inklusive , och eller och , gör att du `add-children` `list-children` kan hantera `remove-children` `get-parent` `set-parent` överordnade/underordnade relationer för befintliga enheter.

---

## <a name="prepare-certificates"></a>Förbereda certifikat

En konsekvent certifikatkedja måste installeras mellan enheter i samma gatewayhierarki för att upprätta en säker kommunikation mellan dem. Varje enhet i hierarkin, oavsett om IoT Edge en IoT-lövenhet eller en IoT-lövenhet, behöver en kopia av samma rotcertifikatutfärdarcertifikat. Varje IoT Edge i hierarkin använder sedan det rotcertifikatutfärdarcertifikatet som rot för enhetens CA-certifikat.

Med den här konfigurationen kan varje underordnad IoT Edge-enhet eller IoT-lövenhet verifiera sin överordnade enhets identitet genom att verifiera att edgeHub som de ansluter till har ett servercertifikat som signerats av det delade rotcertifikatutfärdarcertifikatet.

<!-- TODO: certificate graphic -->

Skapa följande certifikat:

* Ett **rotcertifikatutfärdarcertifikat**, som är det översta delade certifikatet för alla enheter i en viss gatewayhierarki. Det här certifikatet installeras på alla enheter.
* Alla **mellanliggande** certifikat som du vill inkludera i rotcertifikatkedjan.
* Ett **ca-certifikat för** enheten och dess **privata nyckel**, som genereras av rotcertifikaten och mellanliggande certifikat. Du behöver ett unikt ca-certifikat för varje IoT Edge i gatewayhierarkin.

Du kan använda antingen en självsignerad certifikatutfärdare eller köpa en från en betrodd kommersiell certifikatutfärdare som Baltimore, Verisign, Digicert eller GlobalSign.

Om du inte har egna certifikat att använda kan du skapa democertifikat för att testa [IoT Edge enhetsfunktioner.](how-to-create-test-certificates.md) Följ stegen i den här artikeln för att skapa en uppsättning rotcertifikat och mellanliggande certifikat och skapa sedan IoT Edge ca-enhetscertifikat för var och en av dina enheter.

## <a name="configure-iot-edge-on-devices"></a>Konfigurera IoT Edge på enheter

Stegen för att konfigurera IoT Edge som en gateway liknar stegen för att konfigurera IoT Edge som en underordnad enhet.

För att aktivera gatewayidentifiering måste varje IoT Edge konfigureras  med ett värdnamn som dess underordnade enheter använder för att hitta den i det lokala nätverket. Varje nedströmsenhet IoT Edge måste konfigureras med en parent_hostname **att** ansluta till. Om en enskild IoT Edge enhet är både en överordnad och en underordnad enhet behöver den båda parametrarna.

Om du vill aktivera säkra anslutningar måste varje IoT Edge-enhet i ett gatewayscenario konfigureras med ett unikt ca-certifikat för enheten och en kopia av rotcertifikatutfärdarcertifikatet som delas av alla enheter i gatewayhierarkin.

Du bör redan ha IoT Edge installerat på enheten. Om inte följer du stegen för att [registrera en IoT Edge i IoT Hub](how-to-register-device.md) och sedan installera Azure IoT Edge [runtime](how-to-install-iot-edge.md).

Stegen i det här avsnittet refererar till **ca-rotcertifikatet** och ca-enhetscertifikatet och den **privata nyckeln** som diskuterades tidigare i den här artikeln. Om du har skapat dessa certifikat på en annan enhet ska du ha dem tillgängliga på den här enheten. Du kan överföra filerna fysiskt, som med en USB-enhet, med en [tjänst som Azure Key Vault](../key-vault/general/overview.md), eller med en funktion som Säker [filkopiering.](https://www.ssh.com/ssh/scp/)

Använd följande steg för att konfigurera IoT Edge på enheten.

Kontrollera att användaren **iotedge har läsbehörighet** för katalogen som innehåller certifikaten och nycklarna.

1. Installera **rotcertifikatutfärdarcertifikatet** på den IoT Edge enheten.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. Uppdatera certifikatarkivet.

   ```bash
   sudo update-ca-certificates
   ```

   Det här kommandot bör mata ut att ett certifikat har lagts till i /etc/ssl/certs.

1. Öppna konfigurationsfilen för IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >Om konfigurationsfilen inte finns på enheten ännu använder du som `/etc/aziot/config.toml.edge.template` mall för att skapa en.

1. Leta upp **avsnittet Värdnamn** i konfigurationsfilen. Avkommenterade raden som innehåller parametern och uppdatera värdet till det fullständiga domännamnet `hostname` (FQDN) eller IP-adressen för den IoT Edge enheten.

   Värdet för den här parametern är vad nedströmsenheter kommer att använda för att ansluta till den här gatewayen. Värdnamnet tar datornamnet som standard, men FQDN eller IP-adressen krävs för att ansluta underordnade enheter.

   Använd ett värdnamn som är kortare än 64 tecken, vilket är teckengränsen för ett servercertifikats eget namn.

   Var konsekvent med mönstret värdnamn i en gatewayhierarki. Använd Antingen FQDN eller IP-adresser, men inte båda.

1. *Om den här enheten är en underordnad enhet* hittar du avsnittet Överordnad **värddatornamn.** Avkommentering och uppdatering av parametern till FQDN eller IP-adress för den överordnade enheten, som matchar det som angavs som värdnamn i den överordnade `parent_hostname` enhetens konfigurationsfil.

1. Leta reda **på avsnittet Förtroendepakets-certifikat.** Avkommentering och uppdatering `trust_bundle_cert` av parametern med fil-URI till rotcertifikatutfärdarens certifikat på enheten.

1. Kontrollera IoT Edge enheten kommer att använda rätt version IoT Edge agenten när den startas.

   Leta upp **avsnittet Standard edge-agent** och kontrollera att avbildningsvärdet är IoT Edge version 1.2. Om inte uppdaterar du den:

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2"
   ```

1. Leta reda på avsnittet Edge CA certificate **(Certifikat** för Edge CA) i konfigurationsfilen. Avkommenteringen av raderna i det här avsnittet och ange filens URI-sökvägar för certifikat- och nyckelfilerna på IoT Edge enheten.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Spara ( `Ctrl+O` ) och stäng ( ) `Ctrl+X` konfigurationsfilen.

1. Om du tidigare har använt andra certifikat för IoT Edge tar du bort filerna i följande två kataloger för att se till att dina nya certifikat tillämpas:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. Spara ändringarna.

   ```bash
   sudo iotedge config apply
   ```

1. Sök efter eventuella fel i konfigurationen.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >Verktyget IoT Edge check använder en container för att utföra en del av diagnostikkontrollen. Om du vill använda det här verktyget på underordnade IoT Edge enheter kontrollerar du att de har åtkomst till eller har `mcr.microsoft.com/azureiotedge-diagnostics:latest` containeravbildningen i ditt privata containerregister.

## <a name="network-isolate-downstream-devices"></a>Nätverksisoler nedströmsenheter

Stegen hittills i den här artikeln IoT Edge enheter som antingen en gateway eller en underordnad enhet och skapa en betrodd anslutning mellan dem. Gatewayenheten hanterar interaktioner mellan nedströmsenheten och IoT Hub, inklusive autentisering och meddelanderoutning. Moduler som distribueras till nedströms IoT Edge enheter kan fortfarande skapa egna anslutningar till molntjänster.

Vissa nätverksarkitekturer, till exempel de som följer ISA-95-standarden, försöker minimera antalet Internetanslutningar. I dessa scenarier kan du konfigurera nedströmsenheter IoT Edge utan direkt Internetanslutning. Utöver routning IoT Hub kommunikation via sin gatewayenhet kan nedströmsenheter IoT Edge förlita sig på gatewayenheten för alla molnanslutningar.

Den här nätverkskonfigurationen kräver att endast IoT Edge i det översta lagret i en gatewayhierarki har direkta anslutningar till molnet. IoT Edge enheter i de lägre lagren kan bara kommunicera med sin överordnade enhet eller underordnade enheter. Särskilda moduler på gatewayenheterna möjliggör det här scenariot, inklusive:

* **API-proxymodulen** krävs på en IoT Edge gateway som har en annan IoT Edge under sig. Det innebär att den måste finnas på *varje lager i* en gatewayhierarki förutom det nedre lagret. I den här modulen används [en omvänd nginx-proxy](https://nginx.org) för att dirigera HTTP-data via nätverkslager över en enda port. Den kan konfigureras via modultvillingen och miljövariablerna, så den kan justeras så att den passar kraven för ditt gatewayscenario.

* **Docker-registermodulen** kan distribueras på IoT Edge gateway högst upp *i en* gatewayhierarki. Den här modulen ansvarar för att hämta och cachelagra containeravbildningar för alla IoT Edge enheter i lägre lager. Alternativet till att distribuera den här modulen på det översta lagret är att använda ett lokalt register eller att manuellt läsa in containeravbildningar på enheter och ange modulens pull-princip till **aldrig**.

* Den **Azure Blob Storage på IoT Edge** kan distribueras på IoT Edge gateway högst upp i *en* gatewayhierarki. Den här modulen ansvarar för att ladda upp blobar åt alla IoT Edge enheter i lägre lager. Möjligheten att ladda upp blobar möjliggör också användbara felsökningsfunktioner för IoT Edge enheter i lägre lager, till exempel uppladdning av modulloggar och stöd för paketuppladdning.

### <a name="network-configuration"></a>Konfiguration av nätverk

För varje gateway-enhet i det översta lagret måste nätverksoperatörerna:

* Ange en statisk IP-adress eller fullständigt domännamn (FQDN).
* Auktorisera utgående kommunikation från den här IP-adressen till Azure IoT Hub värdnamn via portarna 443 (HTTPS) och 5671 (AMQP).
* Auktorisera utgående kommunikation från den här IP-adressen till Azure Container Registry värdnamn via port 443 (HTTPS).

  API-proxymodulen kan bara hantera anslutningar till ett containerregister i taget. Vi rekommenderar att alla containeravbildningar, inklusive offentliga avbildningar som tillhandahålls av Microsoft Container Registry (mcr.microsoft.com), lagras i ditt privata containerregister.

För varje gateway-enhet i ett lägre lager måste nätverksoperatörerna:

* Ange en statisk IP-adress.
* Auktorisera utgående kommunikation från den här IP-adressen till den överordnade gatewayens IP-adress via portarna 443 (HTTPS) och 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Distribuera moduler till enheter på översta lagret

Enheten IoT Edge det översta lagret i en gatewayhierarki har en uppsättning nödvändiga moduler som måste distribueras till den, förutom de arbetsbelastningsmoduler som du kan köra på enheten.

API-proxymodulen har utformats för att anpassas för att hantera de vanligaste gatewayscenarierna. Den här artikeln innehåller och exempel för att konfigurera modulerna i en grundläggande konfiguration. Mer detaljerad [information och exempel finns i Konfigurera API-proxymodulen](how-to-configure-api-proxy-module.md) för ditt gateway-hierarkiscenario.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)navigerar du till din IoT-hubb.
1. Välj **IoT Edge** på navigeringsmenyn.
1. Välj den enhet på översta lagret som du konfigurerar i listan med **IoT Edge enheter.**
1. Välj **Ange moduler**.
1. I avsnittet **IoT Edge moduler** väljer du Lägg **till och** sedan **Marketplace-modul**.
1. Sök efter och välj modulen **IoT Edge API Proxy.**
1. Välj namnet på API-proxymodulen i listan över distribuerade moduler och uppdatera följande modulinställningar:
   1. På fliken **Miljövariabler** uppdaterar du värdet för **NGINX_DEFAULT_PORT** till `443` .
   1. På fliken **Alternativ för att skapa containrar** uppdaterar du portbindningarna till referensport 443.

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

   De här ändringarna konfigurerar API-proxymodulen så att den lyssnar på port 443. För att förhindra kollisioner med portbindning måste du konfigurera edgeHub-modulen så att den inte lyssnar på port 443. I stället dirigerar API-proxymodulen all edgeHub-trafik på port 443.

1. Välj **Körningsinställningar och** leta reda på alternativ för att skapa edgeHub-modulen. Ta bort portbindningen för port 443 och lämna bindningarna för portarna 5671 och 8883.

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

1. Välj **Spara** för att spara ändringarna i körningsinställningarna.
1. Välj **Lägg** till igen och välj **sedan IoT Edge modul**.
1. Ange följande värden för att lägga till Docker-registermodulen i distributionen:
   1. **IoT Edge modulnamn**: `registry`
   1. På fliken **Modulinställningar,** **Bild-URI:**`registry:latest`
   1. Lägg till **följande miljövariabler** på fliken Miljövariabler:

      * **Namn:** `REGISTRY_PROXY_REMOTEURL` **Värde:** URL:en för det containerregister som du vill att den här registermodulen ska mappa till. Till exempel `https://myregistry.azurecr`.

        Registermodulen kan bara mappa till ett containerregister, så vi rekommenderar att du har alla containeravbildningar i ett enda privat containerregister.

      * **Namn:** `REGISTRY_PROXY_USERNAME` **Värde:** Användarnamn för autentisering till containerregistret.

      * **Namn:** `REGISTRY_PROXY_PASSWORD` **Värde:** Lösenord för autentisering till containerregistret.

   1. På fliken **Alternativ för att skapa container** klistrar du in:

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
1. Välj **Nästa: Vägar** för att gå till nästa steg.
1. Om du vill aktivera "enhet till molnet"-meddelanden från nedströmsenheter för att nå IoT Hub, inkluderar du en väg som skickar alla meddelanden till IoT Hub. Exempel:
    1. **Namn**: `Route`
    1. **Värde**: `FROM /messages/* INTO $upstream`
1. Välj **Granska + skapa för** att gå till det sista steget.
1. Välj **Skapa** för att distribuera till enheten.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. I [Azure Cloud Shell](https://shell.azure.com/)du en JSON-distributionsfil. Exempel:

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "The URL for the container registry you want this registry module to map to. For example, https://myregistry.azurecr"
                               },
                               "REGISTRY_PROXY_USERNAME": {
                                   "value": "Username to authenticate to the container registry."
                               },
                               "REGISTRY_PROXY_PASSWORD": {
                                   "value": "Password to authenticate to the container registry."
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy:1.0",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"443/tcp\": [{\"HostPort\":\"443\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "443"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
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
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {},
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

   Den här distributionsfilen konfigurerar API-proxymodulen så att den lyssnar på port 443. För att förhindra kollisioner med portbindning konfigurerar filen edgeHub-modulen så att den inte lyssnar på port 443. I stället dirigerar API-proxymodulen all edgeHub-trafik på port 443.

1. Ange följande kommando för att skapa en distribution till en IoT Edge enhet:

   ```bash
   az iot edge set-modules --device-id <device_id> --hub-name <iot_hub_name> --content ./<deployment_file_name>.json
   ```

---

### <a name="deploy-modules-to-lower-layer-devices"></a>Distribuera moduler till enheter med lägre nivå

IoT Edge enheter i lägre lager i en gatewayhierarki har en obligatorisk modul som måste distribueras till dem, förutom de arbetsbelastningsmoduler som du kan köra på enheten.

#### <a name="route-container-image-pulls"></a>Hämta routecontaineravbildningar

Innan vi diskuterar den obligatoriska proxymodulen för IoT Edge-enheter i gatewayhierarkier är det viktigt att förstå hur IoT Edge enheter i lägre lager får sina modulavbildningar.

Om dina enheter på lägre nivå inte kan ansluta till molnet, men du vill att de ska hämta modulavbildningar som vanligt, måste enheten på det översta lagret i gatewayhierarkin konfigureras för att hantera dessa begäranden. Enheten på översta lagret måste köra en **Docker-registermodul** som är mappad till containerregistret. Konfigurera sedan API-proxymodulen för att dirigera containerbegäranden till den. Dessa detaljer beskrivs i de tidigare avsnitten i den här artikeln. I den här konfigurationen ska enheter på lägre nivå inte peka på molncontainerregister, utan till registret som körs i det översta lagret.

I stället för att till exempel anropa `mcr.microsoft.com/azureiotedge-api-proxy:1.0` ska enheter med lägre lager anropa `$upstream:443/azureiotedge-api-proxy:1.0` .

Den **$upstream** parametern pekar på den överordnade enheten på en enhet på lägre nivå, så begäran dirigeras genom alla lager tills den når det översta lagret som har en proxymiljö som dirigerar containerbegäranden till registermodulen. Porten `:443` i det här exemplet ska ersättas med den port som API-proxymodulen på den överordnade enheten lyssnar på.

API-proxymodulen kan bara dirigeras till en registermodul och varje registermodul kan bara mappa till ett containerregister. Därför måste alla avbildningar som enheter med lägre nivå behöver hämta lagras i ett enda containerregister.

Om du inte vill att enheter på lägre nivå ska göra modul pull-begäranden via en gatewayhierarki är ett annat alternativ att hantera en lokal registerlösning. Eller så kan du skicka modulavbildningarna till enheterna innan du skapar distributioner och sedan ange **imagePullPolicy till** **aldrig**.

#### <a name="bootstrap-the-iot-edge-agent"></a>Bootstrap-IoT Edge agenten

Agenten IoT Edge är den första körningskomponenten som startar på valfri IoT Edge enhet. Du måste se till att alla underordnade IoT Edge-enheter kan komma åt edgeAgent-modulavbildningen när de startas, och att de sedan kan komma åt distributioner och starta resten av modulavbildningarna.

När du går till konfigurationsfilen på en IoT Edge enhet för att ange autentiseringsinformation, certifikat och överordnat värdnamn uppdaterar du även containeravbildningen edgeAgent.

Om gatewayenheten på den översta nivån är konfigurerad för att hantera containeravbildningsbegäranden ersätter du med det överordnade värdnamnet och `mcr.microsoft.com` lyssningsporten för API-proxy. I distributionsmanifestet kan du använda som en genväg, men det kräver att edgeHub-modulen hanterar routning och modulen `$upstream` har inte startat just nu. Exempel:

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2"
```

Om du använder ett lokalt containerregister eller tillhandahåller containeravbildningarna manuellt på enheten uppdaterar du konfigurationsfilen därefter.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Konfigurera körnings- och distributionsproxymodul

**API-proxymodulen** krävs för routning av all kommunikation mellan molnet och alla underordnade IoT Edge enheter. En IoT Edge enhet i det nedre lagret i hierarkin, utan underordnade IoT Edge enheter, behöver inte den här modulen.

API-proxymodulen har utformats för att anpassas för att hantera de vanligaste gatewayscenarierna. Den här artikeln beskriver kortfattat stegen för att konfigurera modulerna i en grundläggande konfiguration. Mer detaljerad information [och exempel finns i Konfigurera API-proxymodulen](how-to-configure-api-proxy-module.md) för ditt gateway-hierarkiscenario.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din IoT-hubb.
1. Välj **IoT Edge** på navigeringsmenyn.
1. Välj den enhet på lägre nivå som du konfigurerar i listan med **IoT Edge enheter.**
1. Välj **Ange moduler**.
1. I avsnittet **IoT Edge moduler** väljer du Lägg **till och** sedan **Marketplace-modul**.
1. Sök efter och välj modulen **IoT Edge API Proxy.**
1. Välj namnet på API-proxymodulen i listan över distribuerade moduler och uppdatera följande modulinställningar:
   1. På fliken **Modulinställningar** uppdaterar du värdet för **Bild-URI.** Ersätt `mcr.microsoft.com` med `$upstream:443`.
   1. På fliken **Miljövariabler** uppdaterar du värdet för **NGINX_DEFAULT_PORT** till `443` .
   1. På fliken **Alternativ för att skapa containrar** uppdaterar du portbindningarna till referensport 443.

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

   De här ändringarna konfigurerar API-proxymodulen så att den lyssnar på port 443. För att förhindra kollisioner med portbindning måste du konfigurera edgeHub-modulen så att den inte lyssnar på port 443. I stället dirigerar API-proxymodulen all edgeHub-trafik på port 443.

1. Välj **Körningsinställningar.**
1. Uppdatera edgeHub-modulinställningarna:

   1. I fältet **Bild** ersätter du `mcr.microsoft.com` med `$upstream:443` .
   1. I fältet **Skapa alternativ** tar du bort portbindningen för port 443 och lämnar bindningarna för portarna 5671 och 8883.

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

1. Uppdatera edgeAgent-modulinställningarna:
   1. I fältet **Bild** ersätter du `mcr.microsoft.com` med `$upstream:443` .

1. Välj **Spara** för att spara ändringarna i körningsinställningarna.
1. Välj **Nästa: Vägar** för att gå till nästa steg.
1. Om du vill aktivera "enhet till molnet"-meddelanden från nedströmsenheter så att de når IoT Hub, inkluderar du en väg som skickar alla meddelanden till `$upstream` . Den överordnade parametern pekar på den överordnade enheten när det gäller enheter med lägre lager. Exempel:
    1. **Namn**: `Route`
    1. **Värde**: `FROM /messages/* INTO $upstream`
1. Välj **Granska + skapa för** att gå till det sista steget.
1. Välj **Skapa** för att distribuera till enheten.

## <a name="next-steps"></a>Nästa steg

[Så kan en IoT Edge-enhet användas som gateway](iot-edge-as-gateway.md)

[Konfigurera API-proxymodulen för gatewayhierarkiscenariot](how-to-configure-api-proxy-module.md)