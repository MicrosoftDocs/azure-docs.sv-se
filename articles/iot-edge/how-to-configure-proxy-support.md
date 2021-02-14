---
title: Konfigurera enheter för nätverks-proxy – Azure IoT Edge | Microsoft Docs
description: Så här konfigurerar du Azure IoT Edge Runtime och alla Internet-riktade IoT Edge-moduler för att kommunicera via en proxyserver.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperf-fy21q1
ms.openlocfilehash: 7fc57b46055281c64b39767047f6b7cb5b748ad2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373835"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver

IoT Edge enheter skickar HTTPS-begäranden till att kommunicera med IoT Hub. Om enheten är ansluten till ett nätverk som använder en proxyserver måste du konfigurera IoT Edge runtime för att kommunicera via servern. Proxyservrar kan också påverka enskilda IoT Edge moduler om de gör HTTP-eller HTTPS-begäranden som inte dirigeras via IoT Edge Hub.

Den här artikeln vägleder dig genom följande fyra steg för att konfigurera och sedan hantera en IoT Edge enhet bakom en proxyserver:

1. [**Installera IoT Edge runtime på enheten**](#install-the-runtime-through-a-proxy)

   Installations skripten för IoT Edge hämtar paket och filer från Internet, så din enhet måste kommunicera via proxyservern för att utföra dessa förfrågningar. För Windows-enheter tillhandahåller installations skriptet även ett alternativ för offline-installation.

   Det här steget är en engångs process för att konfigurera den IoT Edge enheten när du först konfigurerar den. Samma anslutningar krävs också när du uppdaterar IoT Edge Runtime.

2. [**Konfigurera Docker daemon och IoT Edge daemon på enheten**](#configure-the-daemons)

   IoT Edge använder två bakgrunds program på enheten, som båda måste göra webb förfrågningar via proxyservern. IoT Edge daemon ansvarar för kommunikation med IoT Hub. Moby daemon ansvarar för behållar hantering, och kommunicerar med behållar register.

   Det här steget är en engångs process för att konfigurera den IoT Edge enheten när du först konfigurerar den.

3. [**Konfigurera egenskaperna för IoT Edge agent i filen config. yaml på enheten**](#configure-the-iot-edge-agent)

   IoT Edge daemon startar edgeAgent-modulen från början. Sedan hämtar edgeAgent-modulen distributions manifestet från IoT Hub och startar alla andra moduler. Konfigurera edgeAgent-modulens miljövariabler manuellt på själva enheten för att den IoT Edge agenten ska göra den första anslutningen till IoT Hub. Efter den första anslutningen kan du konfigurera edgeAgent-modulen från en annan plats.

   Det här steget är en engångs process för att konfigurera den IoT Edge enheten när du först konfigurerar den.

4. [**För alla framtida modul distributioner ställer du in miljövariabler för alla moduler som kommunicerar via proxyn**](#configure-deployment-manifests)

   När din IoT Edge-enhet har kon figurer ATS och anslutits till IoT Hub via proxyservern måste du upprätthålla anslutningen i alla framtida distributioner av modulen.

   Det här steget är en pågående process som har utförts på distans så att varje ny modul eller distributions uppdatering upprätthåller enhetens möjlighet att kommunicera via proxyservern.

## <a name="know-your-proxy-url"></a>Känner till din proxyservers URL

Innan du påbörjar något av stegen i den här artikeln måste du känna till din proxy-URL.

Proxy-URL: er ska ha följande format: **protokoll**://**proxy_host**:**proxy_port**.

* **Protokollet** är antingen http eller https. Docker daemon kan använda antingen protokollet, beroende på dina behållar register inställningar, men IoT Edge daemon-och runtime-behållare bör alltid använda HTTP för att ansluta till proxyservern.

* **Proxy_host** är en adress för proxyservern. Om proxyservern kräver autentisering kan du ange dina autentiseringsuppgifter som en del av proxy-värden med följande format: **användare**:**lösen ord** \@ **proxy_host**.

* **Proxy_port** är nätverks porten där proxyservern svarar på nätverks trafik.

## <a name="install-the-runtime-through-a-proxy"></a>Installera körningen via en proxy

Oavsett om din IoT Edge-enhet körs på Windows eller Linux måste du komma åt installations paketen via proxyservern. Följ stegen för att installera IoT Edge runtime via en proxyserver, beroende på ditt operativ system.

### <a name="linux-devices"></a>Linux-enheter

Om du installerar IoT Edge runtime på en Linux-enhet konfigurerar du paket hanteraren att gå via proxyservern för att få åtkomst till installations paketet. Konfigurera till exempel [apt-get för att använda en http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). När paket hanteraren har kon figurer ATS följer du anvisningarna i [installera Azure IoT Edge runtime](how-to-install-iot-edge.md) som vanligt.

### <a name="windows-devices"></a>Windows-enheter

Om du installerar IoT Edge runtime på en Windows-enhet måste du gå igenom proxyservern två gånger. Den första anslutningen laddar ned installations skript filen och den andra anslutningen är under installationen för att ladda ned nödvändiga komponenter. Du kan konfigurera proxyinformation i Windows-inställningar eller inkludera proxyinformation direkt i PowerShell-kommandona.

Följande steg visar ett exempel på en Windows-installation med hjälp av `-proxy` argumentet:

1. Det Invoke-WebRequest kommandot behöver information om proxy för att komma åt installations skriptet. Sedan behöver Deploy-IoTEdge kommandot information om proxyn för att ladda ned installationsfilerna.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Initialize-IoTEdge kommandot behöver inte gå via proxyservern, så det andra steget kräver bara proxyinformation för Invoke-webbegäran.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Om du har komplicerade autentiseringsuppgifter för proxyservern som inte kan ingå i URL: en, använder du- `-ProxyCredential` parametern i `-InvokeWebRequestParameters` . Exempel:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Mer information om parametrar finns i [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest). Mer information om installations parametrarna för Windows finns i [PowerShell-skript för IoT Edge i Windows](reference-windows-scripts.md).

## <a name="configure-the-daemons"></a>Konfigurera daemonar

IoT Edge förlitar sig på två daemonar som körs på IoT Edge enheten. Moby daemon gör webb förfrågningar till att hämta behållar avbildningar från behållar register. IoT Edge daemon gör webb förfrågningar till att kommunicera med IoT Hub.

Både Moby och IoT Edge daemon måste konfigureras för att använda proxyservern för kontinuerlig enhets funktion. Det här steget sker på IoT Edge enhet under installationen av den första enheten.

### <a name="moby-daemon"></a>Moby daemon

Eftersom Moby är byggd på Docker, se Docker-dokumentationen för att konfigurera Moby-daemon med miljövariabler. De flesta behållar register (inklusive DockerHub och Azure Container register) stöder HTTPS-begäranden, så den parameter som du bör ange är **HTTPS_PROXY**. Om du hämtar bilder från ett register som inte stöder TLS (Transport Layer Security) bör du ange **HTTP_PROXY** parameter.

Välj den artikel som gäller för IoT Edge enhetens operativ system:

* [Konfigurera Docker daemon på Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Moby daemon på Linux-enheter behåller namn Docker.
* [Konfigurera Docker daemon i Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Daemon för Moby på Windows-enheter kallas iotedge-Moby. Namnen skiljer sig åt eftersom det är möjligt att köra både Docker Desktop och Moby parallellt på en Windows-enhet.

### <a name="iot-edge-daemon"></a>IoT Edge daemon

IoT Edge daemon har kon figurer ATS på liknande sätt som Moby daemon. Använd följande steg för att ange en miljö variabel för tjänsten, baserat på ditt operativ system.

IoT Edge daemon använder alltid HTTPS för att skicka begär anden till IoT Hub.

#### <a name="linux"></a>Linux

Konfigurera IoT Edge daemon genom att öppna en redigerare i terminalen.

```bash
sudo systemctl edit iotedge
```

Ange följande text och Ersätt **\<proxy URL>** med proxyserverns adress och port. Spara och avsluta sedan.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Uppdatera Service Manager för att hämta den nya konfigurationen för IoT Edge.

```bash
sudo systemctl daemon-reload
```

Starta om IoT Edge för att ändringarna ska börja gälla.

```bash
sudo systemctl restart iotedge
```

Kontrol lera att din miljö variabel har skapats och att den nya konfigurationen har lästs in.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Öppna ett PowerShell-fönster som administratör och kör följande kommando för att redigera registret med den nya miljövariabeln. Ersätt **\<proxy url>** med proxyserverns adress och port.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Starta om IoT Edge för att ändringarna ska börja gälla.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurera IoT Edge agent

Den IoT Edge agenten är den första modulen som startar på valfri IoT Edge enhet. Den startas för första gången baserat på informationen i filen IoT Edge config. yaml. IoT Edge agenten ansluter sedan till IoT Hub för att hämta distributions manifest, som deklarerar vilka andra moduler som ska distribueras på enheten.

Det här steget sker en gång på IoT Edge enheten under den första installationen av enheten.

1. Öppna filen config. yaml på din IoT Edge-enhet. På Linux-system finns den här filen på **/etc/iotedge/config.yaml**. På Windows-system finns den här filen på **C:\ProgramData\iotedge\config.yaml**. Konfigurations filen är skyddad, så du behöver administratörs behörighet för att komma åt den. På Linux-system använder du `sudo` kommandot innan du öppnar filen i önskad text redigerare. Öppna en text redigerare som till exempel anteckningar som administratör i Windows och öppna sedan filen.

2. I filen config. yaml, letar du reda på avsnittet om **Edge agent module** . Den IoT Edge agent definitionen innehåller en **miljö** -parameter där du kan lägga till miljövariabler.

3. Ta bort klammerparenteser som är plats hållare för parametern "kuvert" och Lägg till den nya variabeln på en ny rad. Kom ihåg att indrag i YAML är två blank steg.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IoT Edge runtime använder AMQP som standard för att prata med IoT Hub. Vissa proxyservrar blockerar AMQP-portar. I så fall måste du också konfigurera edgeAgent för att använda AMQP över WebSocket. Lägg till en andra miljö variabel.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent-definition med miljövariabler](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Spara ändringarna i config. yaml och Stäng Redigeraren. Starta om IoT Edge för att ändringarna ska börja gälla.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Konfigurera distributions manifest  

När din IoT Edge enhet har kon figurer ATS för att fungera med proxyservern måste du fortsätta att deklarera HTTPS_PROXY-miljövariabeln i framtida distributions manifest. Du kan redigera distributions manifest antingen med hjälp av guiden Azure Portal eller genom att redigera en JSON-fil för distributions manifestet.

Konfigurera alltid de två modulerna för körning, edgeAgent och edgeHub, för att kommunicera via proxyservern så att de kan underhålla en anslutning med IoT Hub. Om du tar bort proxyinformation från edgeAgent-modulen är det enda sättet att återupprätta anslutningen genom att redigera filen config. yaml på enheten, enligt beskrivningen i föregående avsnitt.

Förutom modulerna edgeAgent och edgeHub kan andra moduler behöva proxy-konfigurationen. Moduler som behöver åtkomst till Azure-resurser förutom IoT Hub, till exempel Blob Storage, måste ha den HTTPS_PROXY variabel som anges i distributions manifest filen.

Följande procedur gäller under den IoT Edge enhetens livs längd.

### <a name="azure-portal"></a>Azure Portal

När du använder guiden **Ange moduler** för att skapa distributioner för IoT Edge enheter, finns det ett **miljövariabler** i varje modul där du kan konfigurera proxy server-anslutningar.

Konfigurera IoT Edge agent och IoT Edge Hub-moduler genom att välja **körnings inställningar** i det första steget i guiden.

![Konfigurera avancerade inställningar för Edge-körning](./media/how-to-configure-proxy-support/configure-runtime.png)

Lägg till miljövariabeln **https_proxy** i både modulen IoT Edge agent och IoT Edge hubb. Om du har inkluderat miljövariabeln **UpstreamProtocol** i filen config. yaml på din IoT Edge-enhet, lägger du till den i modulen för att skapa IoT Edge-agenten.

![Ange https_proxy miljö variabel](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Alla andra moduler som du lägger till i ett distributions manifest följer samma mönster.

### <a name="json-deployment-manifest-files"></a>Manifest fil för JSON-distribution

Om du skapar distributioner för IoT Edge enheter med mallarna i Visual Studio Code eller genom att skapa JSON-filer manuellt, kan du lägga till miljövariablerna direkt i varje modul definition.

Använd följande JSON-format:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

När miljövariablerna ingår bör din modultyp se ut som följande edgeHub-exempel:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Om du har inkluderat miljövariabeln **UpstreamProtocol** i filen config. yaml på din IoT Edge-enhet, lägger du till den IoT Edge i definitionen för modulen för.

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="working-with-traffic-inspecting-proxies"></a>Arbeta med trafik-inspekterande proxyservrar

Om proxyn som du försöker använda utför trafik kontroll på TLS-skyddade anslutningar är det viktigt att Observera att autentisering med X. 509-certifikat inte fungerar. IoT Edge upprättar en TLS-kanal som är krypterad och slutar med det tillhandahållna certifikatet och nyckeln. Om kanalen är bruten för trafik kontroll kan proxyn inte återupprätta kanalen med rätt autentiseringsuppgifter och IoT Hub och IoT Hub enhets etablerings tjänsten returnerar ett `Unauthorized` fel.

Om du vill använda en proxy som utför trafik inspektionen måste du använda antingen autentisering med delad åtkomst eller IoT Hub och IoT Hub enhets etablerings tjänsten som lagts till i en lista över tillåtna för att undvika inspektion.

## <a name="next-steps"></a>Nästa steg

Läs mer om rollerna för [IoT Edge runtime](iot-edge-runtime.md).

Felsök installations-och konfigurations fel med [vanliga problem och lösningar för Azure IoT Edge](troubleshoot.md)
