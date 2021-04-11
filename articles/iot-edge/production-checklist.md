---
title: Förbered för att distribuera din lösning i produktion – Azure IoT Edge
description: Lär dig hur du tar din Azure IoT Edge-lösning från utveckling till produktion, inklusive hur du konfigurerar dina enheter med lämpliga certifikat och gör en distributions plan för framtida kod uppdateringar.
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
ms.openlocfilehash: fda69d582f26b0c9189898bb5c8b0004a1e47360
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722777"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Förbered för att distribuera din IoT Edge-lösning i produktion

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

När du är redo att ta din IoT Edge lösning från utveckling till produktion kontrollerar du att den har kon figurer ATS för kontinuerliga prestanda.

Informationen i den här artikeln är inte lika stor. För att hjälpa dig att prioritera, börjar varje avsnitt med listor som delar in arbetet i två delar: **viktigt** att slutföra innan du går till produktion, eller **hjälper** dig att lära dig.

## <a name="device-configuration"></a>Enhetskonfiguration

IoT Edge enheter kan vara allt från en Raspberry Pi till en bärbar dator till en virtuell dator som körs på en server. Du kan ha åtkomst till enheten antingen fysiskt eller via en virtuell anslutning, eller så kan den vara isolerad under längre tids perioder. Oavsett hur du vill kontrol lera att den är konfigurerad för att fungera korrekt.

* **Viktigt**
  * Installera produktionscertifikat
  * Ha en plan för enhets hantering
  * Använd Moby som behållar motor

* **Användbart**
  * Välj överordnat protokoll

### <a name="install-production-certificates"></a>Installera produktionscertifikat

Varje IoT Edge enhet i produktionen måste ha ett certifikat för enhetens certifikat utfärdare installerat på den. CA-certifikatet deklareras sedan till IoT Edge runtime i konfigurations filen. För utvecklings-och testnings scenarier skapar IoT Edge runtime tillfälliga certifikat om inga certifikat har deklarerats i konfigurations filen. Dessa tillfälliga certifikat upphör dock att gälla efter tre månader och är inte säkra för produktions scenarier. För produktions scenarier bör du ange ditt eget certifikat för enhets certifikat utfärdare, antingen från en självsignerad certifikat utfärdare eller köpas från en kommersiell certifikat utfärdare.

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> För närvarande förhindrar en begränsning i libiothsm användningen av certifikat som upphör att gälla den 1 januari 2038.

:::moniker-end

Information om hur du förstår rollen för enhetens CA-certifikat finns i [hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

Mer information om hur du installerar certifikat på en IoT Edge-enhet och refererar till dem från konfigurations filen finns i [Hantera certifikat på en IoT Edge enhet](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Ha en plan för enhets hantering

Innan du sätter en enhet i produktion bör du veta hur du ska hantera framtida uppdateringar. För en IoT Edge enhet kan listan över komponenter som ska uppdateras innehålla:

* Enhetens inbyggda programvara
* Operativ system bibliotek
* Container motor, t. ex. Moby
* IoT Edge
* CA-Certifikat

Mer information finns i [uppdatera IoT Edge runtime](how-to-update-iot-edge.md). Aktuella metoder för att uppdatera IoT Edge kräver fysisk eller SSH-åtkomst till IoT Edge-enheten. Om du har många enheter att uppdatera kan du överväga att lägga till uppdaterings stegen i ett skript eller använda ett Automation-verktyg som Ansible.

### <a name="use-moby-as-the-container-engine"></a>Använd Moby som behållar motor

En behållar motor är en förutsättning för alla IoT Edge enheter. Endast Moby-motor stöds i produktion. Andra behållar motorer, som Docker, fungerar med IoT Edge och det är OK att använda dessa motorer för utveckling. Moby-motorn kan distribueras om när den används med Azure IoT Edge och Microsoft tillhandahåller underhåll för denna motor.

### <a name="choose-upstream-protocol"></a>Välj överordnat protokoll

Du kan konfigurera protokollet (som avgör vilken port som används) för överordnad kommunikation till IoT Hub för både IoT Edge-agenten och IoT Edge Hub. Standard protokollet är AMQP, men du kanske vill ändra beroende på din nätverks konfiguration.

De två modulerna för körning har båda en **UpstreamProtocol** -miljö variabel. Giltiga värden för variabeln är:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Konfigurera variabeln UpstreamProtocol för IoT Edge agenten i konfigurations filen på själva enheten. Om din IoT Edge enhet till exempel ligger bakom en proxyserver som blockerar AMQP-portar kan du behöva konfigurera IoT Edge-agenten att använda AMQP över WebSocket (AMQPWS) för att upprätta den första anslutningen till IoT Hub.

När din IoT Edge enhet ansluter måste du fortsätta konfigurera variabeln UpstreamProtocol för båda körnings modulerna i framtida distributioner. Ett exempel på den här processen finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Distribution

* **Användbart**
  * Var konsekvent med det överordnade protokollet
  * Konfigurera värd lagring för systemmoduler
  * Minska minnes utrymmet som används av IoT Edge Hub
  * Använd inte fel söknings versioner av module-avbildningar

### <a name="be-consistent-with-upstream-protocol"></a>Var konsekvent med det överordnade protokollet

Om du har konfigurerat IoT Edge-agenten på IoT Edge-enheten för att använda ett annat protokoll än standard-AMQP, bör du deklarera samma protokoll i alla framtida distributioner. Om din IoT Edge enhet till exempel ligger bakom en proxyserver som blockerar AMQP-portar, konfigurerade du förmodligen enheten för att ansluta över AMQP över WebSocket (AMQPWS). När du distribuerar moduler till enheten konfigurerar du samma AMQPWS-protokoll för IoT Edge-agenten och IoT Edge Hub, eller så åsidosätter standard AMQP inställningarna och hindrar dig från att ansluta igen.

Du behöver bara konfigurera miljövariabeln UpstreamProtocol för IoT Edge agenten och IoT Edge Hub-moduler. Alla ytterligare moduler antar det protokoll som anges i runtime-modulerna.

Ett exempel på den här processen finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Konfigurera värd lagring för systemmoduler

IoT Edge hubb och agent-moduler använder lokal lagring för att underhålla tillstånd och aktivera meddelanden mellan moduler, enheter och molnet. För bättre tillförlitlighet och prestanda konfigurerar du systemmodulerna för att använda lagring på värd fil systemet.

Mer information finns i [värd lagring för systemmoduler](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Minska minnes utrymmet som används av IoT Edge hubb

Om du distribuerar begränsade enheter med begränsat minne tillgängligt kan du konfigurera IoT Edge hubben så att den körs i en mer strömlinjeformad kapacitet och använder mindre disk utrymme. De här konfigurationerna begränsar IoT Edge hubbens prestanda, så hitta rätt saldo som fungerar för din lösning.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Optimera inte för prestanda på begränsade enheter

IoT Edge hubben är optimerad för prestanda som standard och försöker allokera stora mängder minne. Den här konfigurationen kan orsaka stabilitets problem på mindre enheter, t. ex. Raspberry Pi. Om du distribuerar enheter med begränsade resurser kanske du vill ange **OptimizeForPerformance** -miljövariabeln till **false** på IoT Edge Hub.

När **OptimizeForPerformance** är inställt på **True** använder MQTT-protokollets huvud PooledByteBufferAllocator, vilket ger bättre prestanda men allokerar mer minne. Allokeraren fungerar inte bra på 32-bitars operativ system eller på enheter med ont om minne. När RocksDb allokeras för prestanda, allokerar dessutom mer minne för rollen som den lokala lagrings leverantören.

Mer information finns i [stabilitets problem på mindre enheter](troubleshoot-common-errors.md#stability-issues-on-smaller-devices).

#### <a name="disable-unused-protocols"></a>Inaktivera oanvända protokoll

Ett annat sätt att optimera prestanda för IoT Edge hubben och minska dess minnes användning är att inaktivera protokoll huvudena för alla protokoll som du inte använder i lösningen.

Protokoll huvuden konfigureras genom att ställa in booleska miljövariabler för modulen IoT Edge Hub i distributions manifesten. De tre variablerna är:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Alla tre variablerna har *två under streck* och kan anges till antingen sant eller falskt.

#### <a name="reduce-storage-time-for-messages"></a>Minska lagrings tiden för meddelanden

Modulen IoT Edge Hub lagrar meddelanden tillfälligt om de inte kan levereras till IoT Hub av någon anledning. Du kan konfigurera hur länge IoT Edge hubben ska lagra i meddelanden som inte levererats innan de upphör att gälla. Om du har minnes problem på enheten kan du sänka **timeToLiveSecs** -värdet i IoT Edge Hub-modulen.

Standardvärdet för parametern timeToLiveSecs är 7200 sekunder, vilket är två timmar.

### <a name="do-not-use-debug-versions-of-module-images"></a>Använd inte fel söknings versioner av module-avbildningar

Kom ihåg att ta bort fel söknings konfigurationerna från distributions manifest när du flyttar från test scenarier till produktions scenarier. Kontrol lera att ingen av modulens bilder i distributions manifesten har **\. fel söknings** -suffixet. Om du har lagt till skapa alternativ för att exponera portar i modulerna för fel sökning tar du även bort de här alternativen för att skapa.

## <a name="container-management"></a>Hantering av containrar

* **Viktigt**
  * Hantera åtkomst till behållar registret
  * Använda taggar för att hantera versioner
* **Användbart**
  * Lagra runtime-behållare i ditt privata register

### <a name="manage-access-to-your-container-registry"></a>Hantera åtkomst till behållar registret

Innan du distribuerar moduler till produktions IoT Edge enheter bör du kontrol lera att du styr åtkomsten till behållar registret så att de inte kan komma åt eller göra ändringar i behållar avbildningarna. Använd ett privat, inte offentligt, behållar register för att hantera behållar avbildningar.

I självstudierna och annan dokumentation instruerar vi dig att använda samma autentiseringsuppgifter för behållar registret på din IoT Edge enhet när du använder på din utvecklings dator. Dessa instruktioner är endast avsedda att hjälpa dig att konfigurera testnings-och utvecklings miljöer enklare och bör inte följas i ett produktions scenario.

För en säkrare åtkomst till registret kan du välja [autentiseringsalternativ](../container-registry/container-registry-authentication.md). En populär och Rekommenderad autentisering är att använda en Active Directory tjänstens huvud namn som lämpar sig väl för program eller tjänster för att hämta behållar avbildningar på ett automatiserat eller annars obevakat sätt, som IoT Edge enheter gör.

Om du vill skapa ett huvud namn för tjänsten kör du de två skripten enligt beskrivningen i [skapa ett huvud namn för tjänsten](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal). Dessa skript utför följande uppgifter:

* Det första skriptet skapar tjänstens huvud namn. Tjänstens huvud namn och lösen ord för tjänstens huvud namn matas ut. Lagra värdena säkert i dina poster.

* Det andra skriptet skapar roll tilldelningar för att ge tjänstens huvud namn, vilket kan köras senare om det behövs. Vi rekommenderar att du använder användar rollen **acrPull** för `role` parametern. En lista över roller finns i [Azure Container Registry roller och behörigheter](../container-registry/container-registry-roles.md).

Om du vill autentisera med ett huvud namn för tjänsten anger du det ID för tjänstens huvud namn och lösen ord som du fick från det första skriptet. Ange dessa autentiseringsuppgifter i distributions manifestet.

* För användar namn eller klient-ID anger du tjänstens huvud namn-ID.

* Ange lösen ordet för tjänstens huvud namn för lösen ordet eller klient hemligheten.

> [!NOTE]
> När du har implementerat en utökad säkerhetsautentisering inaktiverar du **användar inställningen administratör** så att standard åtkomsten för användar namn/lösen ord inte längre är tillgänglig. I behållar registret i Azure Portal väljer du **åtkomst nycklar** från menyn i den vänstra rutan under **Inställningar**.

### <a name="use-tags-to-manage-versions"></a>Använda taggar för att hantera versioner

En-tagg är ett Docker-koncept som du kan använda för att skilja mellan versioner av Docker-behållare. Taggar är suffix som **1,0** som finns i slutet av en behållar lagrings plats. Till exempel **MCR.Microsoft.com/azureiotedge-agent:1.0**. Taggarna är föränderligt och kan ändras så att de pekar till en annan behållare när som helst, så att ditt team ska komma överens om en konvention att följa när du uppdaterar dina avbildningar flyttas framåt.

Taggar hjälper dig också att genomdriva uppdateringar på dina IoT Edge enheter. När du push-överför en uppdaterad version av en modul till behållar registret ökar du taggen. Sedan kan du skicka en ny distribution till dina enheter med taggen som ökar. Behållar motorn känner igen den stegvisa taggen som en ny version och hämtar den senaste modulens version till din enhet.

#### <a name="tags-for-the-iot-edge-runtime"></a>Taggar för IoT Edge runtime

IoT Edge-agenten och IoT Edge Hub-avbildningarna är taggade med den IoT Edge-version som de är associerade med. Det finns två olika sätt att använda taggar med körnings avbildningarna:

* **Rullande Taggar** – Använd bara de första två värdena i versions numret för att hämta den senaste bilden som matchar de siffrorna. 1,1 uppdateras till exempel när det finns en ny version som pekar på den senaste 1.1. x-versionen. Om behållar körningen på din IoT Edge enhet hämtar avbildningen igen, uppdateras runtime-modulerna till den senaste versionen. Distributioner från Azure Portal standardvärde till rullande taggar. *Den här metoden föreslås i utvecklings syfte.*

* **Vissa Taggar** -Använd alla tre värdena i versions numret för att uttryckligen ange avbildnings versionen. 1.1.0 ändras till exempel inte efter den första versionen. Du kan deklarera ett nytt versions nummer i distributions manifestet när du är redo att uppdatera. *Den här metoden rekommenderas för produktions syfte.*

### <a name="store-runtime-containers-in-your-private-registry"></a>Lagra runtime-behållare i ditt privata register

Du vet om att lagra behållar avbildningar för anpassade kodmoduler i ditt privata Azure-register, men du kan också använda den för att lagra offentliga behållar avbildningar som för edgeAgent-och edgHub-modulerna. Det kan krävas om du har mycket tätt brand Väggs begränsningar eftersom dessa runtime-behållare lagras i Microsoft Container Registry (MCR).

Hämta avbildningarna med kommandot Docker pull för att placera i det privata registret. Tänk på att du måste uppdatera avbildningarna med varje ny version av IoT Edge Runtime.

| IoT Edge runtime-behållare | Docker pull-kommando |
| --- | --- |
| [Azure IoT Edge agent](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Azure IoT Edge hubb](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

Se sedan till att uppdatera avbildnings referenserna i deployment.template.jsfilen för systemmodulerna edgeAgent och edgeHub. Ersätt `mcr.microsoft.com` med ditt register namn och server för båda modulerna.

* edgeAgent:

    `"image": "<registry name and server>/azureiotedge-agent:1.1",`

* edgeHub:

    `"image": "<registry name and server>/azureiotedge-hub:1.1",`

## <a name="networking"></a>Nätverk

* **Användbart**
  * Granska utgående/inkommande konfiguration
  * Tillåt anslutningar från IoT Edge enheter
  * Konfigurera kommunikation via en proxy

### <a name="review-outboundinbound-configuration"></a>Granska utgående/inkommande konfiguration

Kommunikations kanaler mellan Azure IoT Hub och IoT Edge är alltid konfigurerade att vara utgående. För de flesta IoT Edge-scenarier behövs bara tre anslutningar. Behållar motorn måste ansluta till behållar registret (eller registren) som innehåller modul avbildningarna. IoT Edge runtime måste ansluta till IoT Hub för att hämta information om enhets konfigurationen och för att skicka meddelanden och telemetri. Om du använder automatisk etablering måste IoT Edge ansluta till enhets etablerings tjänsten. Mer information finns i [brand Väggs-och port konfigurations regler](troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="allow-connections-from-iot-edge-devices"></a>Tillåt anslutningar från IoT Edge enheter

Om nätverks konfigurationen kräver att du uttryckligen tillåter anslutningar som görs från IoT Edge-enheter läser du följande lista över IoT Edge komponenter:

* **IoT Edge agent** öppnar en beständig AMQP/MQTT-anslutning till IoT Hub, möjligen över WebSockets.
* **IoT Edge hubb** öppnar en enda beständig AMQP-anslutning eller flera MQTT-anslutningar för att IoT Hub, möjligen över WebSockets.
* **IoT Edge tjänsten** gör tillfälliga https-anrop till IoT Hub.

I samtliga tre fall matchar DNS-namnet mönstret \* . Azure-Devices.net.

Dessutom gör **behållar motorn** anrop till behållar register via https. Om du vill hämta IoT Edge runtime container-avbildningarna är DNS-namnet mcr.microsoft.com. Behållar motorn ansluter till andra register som kon figurer ATS i distributionen.

Den här check listan är en start punkt för brand Väggs regler:

   | URL ( \* = jokertecken) | Utgående TCP-portar | Användning |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft Container Registry |
   | global.azure-devices-provisioning.net  | 443 | DPS-åtkomst (valfritt) |
   | \*. azurecr.io | 443 | Personliga och tredje parts behållar register |
   | \*.blob.core.windows.net | 443 | Ladda ned Azure Container Registry avbildnings delta från Blob Storage |
   | \*. azure-devices.net | 5671, 8883, 443 | IoT Hub åtkomst |
   | \*. docker.io  | 443 | Docker Hub-åtkomst (valfritt) |

Vissa av brand Väggs reglerna ärvs från Azure Container Registry. Mer information finns i [Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg](../container-registry/container-registry-firewall-access-rules.md).

> [!NOTE]
> För att tillhandahålla ett konsekvent FQDN mellan REST-och data slut punkter, från och med den **15 juni 2020** , kommer Microsoft container Registry data slut punkten att ändras från `*.cdn.mscr.io` till `*.data.mcr.microsoft.com`  
> Mer information finns i [konfiguration av brand Väggs regler för Microsoft container Registry-klient](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)

Om du inte vill konfigurera brand väggen för att tillåta åtkomst till offentliga behållar register, kan du lagra avbildningar i din privata behållar register, enligt beskrivningen i [lagra runtime-behållare i ditt privata register](#store-runtime-containers-in-your-private-registry).

### <a name="configure-communication-through-a-proxy"></a>Konfigurera kommunikation via en proxy

Om dina enheter ska distribueras i ett nätverk som använder en proxyserver måste de kunna kommunicera via proxyservern för att uppnå IoT Hub-och behållar register. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Lösnings hantering

* **Användbart**
  * Konfigurera loggar och diagnostik
  * Överväg tester och CI/CD-pipeliner

### <a name="set-up-logs-and-diagnostics"></a>Konfigurera loggar och diagnostik

I Linux använder IoT Edge daemon journaler som standard driv rutin för loggning. Du kan använda kommando rads verktyget `journalctl` för att skicka frågor till daemon-loggarna.

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

Från och med version 1,2 förlitar sig IoT Edge på flera bakgrunds program. Även om varje daemons loggar kan frågas individuellt med `journalctl` , är `iotedge system` kommandona ett bekvämt sätt att fråga samman de kombinerade loggarna.

* Konsoliderat `iotedge` kommando:

  ```bash
  sudo iotedge system logs
  ```

* Motsvarande `journalctl` kommando:

  ```bash
  journalctl -u aziot-edge -u aziot-identityd -u aziot-keyd -u aziot-certd -u aziot-tpmd
  ```

:::moniker-end

I Windows använder IoT Edge daemon PowerShell-diagnostik. Använd `Get-IoTEdgeLog` för att fråga efter loggar från daemonen. IoT Edge moduler använder JSON-drivrutinen för loggning, vilket är standardvärdet.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

När du testar en IoT Edge-distribution kan du vanligt vis få åtkomst till dina enheter för att hämta loggar och felsöka. Du kanske inte har det alternativet i ett distributions scenario. Överväg hur du ska samla in information om dina enheter i produktionen. Ett alternativ är att använda en loggningsmodul som samlar in information från de andra modulerna och skickar den till molnet. Ett exempel på en loggningsmodul är [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), eller så kan du skapa en egen.

### <a name="place-limits-on-log-size"></a>Placera gränser för logg storlek

Som standard anger Moby container Engine inte storleks gränser för behållar loggen. Med tiden kan detta leda till att enheten fyller i loggar och håller på att få slut på disk utrymme. Överväg följande alternativ för att förhindra detta:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Alternativ: ange globala gränser som gäller för alla behållar moduler

Du kan begränsa storleken på alla behållar logg fils loggar i behållar Motorns logg alternativ. I följande exempel anges logg driv rutinen till `json-file` (rekommenderas) med gränser för storlek och antal filer:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Lägg till (eller Lägg till) den här informationen i en fil med namnet `daemon.json` och placera den rätt plats för din enhets plattform.

| Plattform | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Behållar motorn måste startas om för att ändringarna ska börja gälla.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Alternativ: justera logg inställningar för varje container module

Du kan göra det i **createOptions** för varje modul. Exempel:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

#### <a name="additional-options-on-linux-systems"></a>Ytterligare alternativ för Linux-system

* Konfigurera behållar motorn att skicka loggar till `systemd` [journalen](https://docs.docker.com/config/containers/logging/journald/) genom `journald` att ange som standard driv rutin för loggning.

* Ta regelbundet bort gamla loggar från enheten genom att installera ett logrotate-verktyg. Använd följande fil specifikation:

   ```txt
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Överväg tester och CI/CD-pipeliner

För det effektivaste IoT Edge distributions scenariot, kan du överväga att integrera produktions distributionen i dina test-och CI/CD-pipeliner. Azure IoT Edge stöder flera CI/CD-plattformar, inklusive Azure DevOps. Mer information finns i [kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [IoT Edge automatisk distribution](module-deployment-monitoring.md).
* Se hur IoT Edge stöder [kontinuerlig integrering och kontinuerlig distribution](how-to-continuous-integration-continuous-deployment.md).
