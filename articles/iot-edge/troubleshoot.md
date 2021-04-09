---
title: Felsök – Azure IoT Edge | Microsoft Docs
description: Använd den här artikeln för att lära dig om diagnostiska färdigheter för Azure IoT Edge, till exempel hämtning av komponent status och loggar
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7b3b8078a03ef0e891306f056c604545cde71459
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489465"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Felsöka IoT Edge-enheten

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Om du får problem med att köra Azure IoT Edge i din miljö kan du använda den här artikeln som en guide för fel sökning och diagnostik.

## <a name="run-the-check-command"></a>Kör kommandot check

Ditt första steg när du felsöker IoT Edge bör vara att använda `check` kommandot, som kör en samling konfigurations-och anslutnings test för vanliga problem. `check`Kommandot är tillgängligt i [version 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) och senare.

>[!NOTE]
>Fel söknings verktyget kan inte köra anslutnings kontroller om den IoT Edge enheten ligger bakom en proxyserver.

Du kan köra `check` kommandot på följande sätt, eller inkludera `--help` flaggan om du vill se en fullständig lista med alternativ:

I Linux:

```bash
sudo iotedge check
```

I Windows:

```powershell
iotedge check
```

Fel söknings verktyget kör många kontroller som är sorterade i följande tre kategorier:

* *Konfigurations kontroller* undersöker information som kan förhindra IoT Edge enheter från att ansluta till molnet, inklusive problem med konfigurations filen och behållar motorn.
* *Anslutnings kontroller* verifierar att IoT Edge runtime kan komma åt portar på värd enheten och att alla IoT Edge-komponenter kan ansluta till IoT Hub. Den här uppsättningen kontroller returnerar fel om IoT Edge enheten ligger bakom en proxyserver.
* I *produktions beredskaps kontrollerna* kan du söka efter rekommenderade produktions metoder, t. ex. tillstånd för certifikat från enhetens certifikat utfärdare och logg fils konfigurationen för modulen.

Verktyget IoT Edge check använder en behållare för att köra dess diagnostik. Behållar avbildningen, `mcr.microsoft.com/azureiotedge-diagnostics:latest` , är tillgänglig via [Microsoft container Registry](https://github.com/microsoft/containerregistry). Om du behöver köra en kontroll på en enhet utan direkt åtkomst till Internet, behöver enheterna åtkomst till behållar avbildningen.

Information om var och en av de diagnostiska kontroller som utförs av verktyget, inklusive vad du ska göra om du får ett fel eller en varning, finns i [IoT Edge Felsök checkar](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Samla in felsöknings information med kommandot "support-Bundle"

När du behöver samla in loggar från en IoT Edge enhet är det enklaste sättet att använda `support-bundle` kommandot. Som standard samlar detta kommando in modul, IoT Edge Security Manager-och behållar motor loggar, `iotedge check` JSON-utdata och annan användbar felsöknings information. Den komprimerar dem till en enda fil för enkel delning. `support-bundle`Kommandot är tillgängligt i [version 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) och senare.

Kör `support-bundle` kommandot med `--since` flaggan för att ange hur lång tid från det förflutna du vill hämta loggar. Till exempel `6h` får loggar sedan de senaste sex timmarna, `6d` sedan de senaste sex dagarna, sedan de senaste sex `6m` minuterna och så vidare. Ta med `--help` flaggan om du vill se en fullständig lista med alternativ.

I Linux:

```bash
sudo iotedge support-bundle --since 6h
```

I Windows:

```powershell
iotedge support-bundle --since 6h
```

Du kan också använda ett [direkt metod](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) anrop till enheten för att överföra utdata från kommandot support-Bundle till Azure Blob Storage.

> [!WARNING]
> Utdata från `support-bundle` kommandot kan innehålla värden, enhets-och Modulnamn, information som loggas av dina moduler osv. Tänk på detta om du delar utdata i ett offentligt forum.

## <a name="check-your-iot-edge-version"></a>Kontrol lera din IoT Edge version

Om du kör en äldre version av IoT Edge kan du eventuellt lösa problemet genom att uppgradera. `iotedge check`Verktyget kontrollerar att IoT Edge Security daemon är den senaste versionen, men kontrollerar inte versionerna av IoT Edge Hub och agent-moduler. Om du vill kontrol lera versionen av Runtime-modulerna på enheten använder du kommandona `iotedge logs edgeAgent` och `iotedge logs edgeHub` . Versionsnumret skrivs ut i loggarna när modulen startas.

Anvisningar om hur du uppdaterar din enhet finns i [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>Verifiera installationen av IoT Edge på dina enheter

Du kan kontrol lera installationen av IoT Edge på dina enheter genom [att övervaka edgeAgent-modulen med dubbla](./how-to-monitor-module-twins.md).

För att få den senaste edgeAgent-modulen dubbla, kör du följande kommando från [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

Det här kommandot kommer att mata ut alla edgeAgent- [rapporterade egenskaper](./module-edgeagent-edgehub.md). Här följer några användbara som övervakar enhetens status:

* körnings status
* Start tid för körning
* senaste slut tid för körning
* antal omstart för körning

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrol lera status för IoT Edge Security Manager och dess loggar

[IoT Edge Security Manager](iot-edge-security-manager.md) ansvarar för åtgärder som att initiera IoT Edge systemet vid start och etablering av enheter. Om IoT Edge inte startar kan säkerhets hanterarens loggar ge värdefull information.

I Linux:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

* Visa status för IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Visa loggarna för IoT Edge Security Manager:

   ```bash
   sudo journalctl -u iotedge -f
   ```

* Visa mer detaljerade loggar av IoT Edge Security Manager:

  1. Redigera inställningarna för IoT Edge daemon:

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. Uppdatera följande rader:

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=edgelet=debug
     ```

  3. Starta om IoT Edge Security daemon:

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```
<!--end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* Visa status för IoT Edge system tjänster:

   ```bash
   sudo iotedge system status
   ```

* Visa loggarna för IoT Edge system tjänster:

   ```bash
   sudo iotedge system logs -- -f
   ```

* Aktivera fel söknings nivå loggar om du vill visa mer detaljerade loggar av IoT Edge system tjänster:

  1. Aktivera fel söknings nivå loggar.

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. Växla tillbaka till standard informations nivå loggarna efter fel sökning.

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

<!-- end 1.2 -->
:::moniker-end

I Windows:

* Visa status för IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

* Visa loggarna för IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Visa endast de senaste 5 minuterna i IoT Edge Security Manager-loggarna:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Visa mer detaljerade loggar av IoT Edge Security Manager:

  1. Lägg till en miljö variabel på system nivå:

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. Starta om IoT Edge Security daemon:

     ```powershell
     Restart-Service iotedge
     ```

## <a name="check-container-logs-for-issues"></a>Kontrol lera behållar loggar för problem

När IoT Edge Security Daemon körs tittar du på loggarna för behållarna för att identifiera problem. Börja med dina distribuerade behållare och titta sedan på de behållare som utgör den IoT Edge körningen: edgeAgent och edgeHub. IoT Edge agent loggar ger vanligt vis information om livs cykeln för varje behållare. IoT Edge Hubbs loggar innehåller information om meddelanden och routning.

```cmd
iotedge logs <container name>
```

Du kan också använda ett [direkt metod](how-to-retrieve-iot-edge-logs.md#upload-module-logs) anrop till en modul på enheten för att ladda upp loggarna för den modulen till Azure Blob Storage.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Visa meddelanden som går via IoT Edge Hub

<!--1.1 -->
:::moniker range="iotedge-2018-06"

Du kan visa meddelanden som går via IoT Edge hubben och samla in insikter från utförliga loggar från runtime-behållare. Om du vill aktivera utförliga loggar på de här behållarna anger `RuntimeLogLevel` du konfigurations filen för yaml. Så här öppnar du filen:

I Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

I Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Som standard `agent` ser elementet ut som i följande exempel:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

Ersätt `env: {}` med:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-filer får inte innehålla tabbar som indrag. Använd 2 Blank steg i stället. Toppnivå objekt får inte ha inledande blank steg.

Spara filen och starta om IoT Edge Security Manager.

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Du kan visa meddelanden som går via IoT Edge Hub och samla in insikter från utförliga loggar från runtime-behållare. Om du vill aktivera utförliga loggar i dessa behållare anger du `RuntimeLogLevel` miljövariabeln i distributions manifestet.

Om du vill visa meddelanden som går via IoT Edge Hub ställer du in `RuntimeLogLevel` miljövariabeln på `debug` för edgeHub-modulen.

Både edgeHub-och edgeAgent-modulerna har den här miljövariabeln för körnings loggen, med standardvärdet inställt på `info` . Den här miljövariabeln kan ha följande värden:

* döds
* fel
* varning
* information
* felsökning
* utförlig

<!-- end 1.2 -->
:::moniker-end

Du kan också kontrol lera meddelanden som skickas mellan IoT Hub-och IoT-enheter. Visa dessa meddelanden med hjälp av [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Mer information finns i [användbart verktyg när du utvecklar med Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

## <a name="restart-containers"></a>Starta om behållare

När du har undersökt loggarna och meddelandena för information kan du prova att starta om behållare:

```cmd
iotedge restart <container name>
```

Starta om IoT Edge runtime-behållare:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Kontrol lera brand Väggs-och port konfigurations reglerna

Azure IoT Edge tillåter kommunikation från en lokal server till Azure-molnet med IoT Hub protokoll som stöds, se [välja ett kommunikations protokoll](../iot-hub/iot-hub-devguide-protocols.md). För förbättrad säkerhet är kommunikations kanaler mellan Azure IoT Edge och Azure-IoT Hub alltid konfigurerade att vara utgående. Den här konfigurationen baseras på [kommunikations mönstret tjänster](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices), som minimerar angrepps ytan för en skadlig entitet att utforska. Inkommande kommunikation krävs endast för vissa scenarier där Azure IoT Hub behöver skicka meddelanden till Azure IoT Edge-enheten. Meddelanden från moln till enhet skyddas med hjälp av säkra TLS-kanaler och kan skyddas ytterligare med X. 509-certifikat och TPM-enhets moduler. Azure IoT Edge Security Manager styr hur kommunikationen kan upprättas, se [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Även om IoT Edge ger förbättrad konfiguration för att skydda Azure IoT Edge Runtime och distribuerade moduler, är det fortfarande beroende av den underliggande datorn och nätverks konfigurationen. Därför är det absolut nödvändigt att se till att rätt nätverks-och brand Väggs regler har kon figurer ATS för säker Edge till moln kommunikation. Följande tabell kan användas som en rikt linje när du konfigurerar brand Väggs regler för de underliggande servrarna där Azure IoT Edge runtime finns:

|Protokoll|Port|Inkommande|Autentiseringsnivå|Vägledning|
|--|--|--|--|--|
|MQTT|8883|BLOCKERAd (standard)|BLOCKERAd (standard)|<ul> <li>Konfigurera utgående (utgående) som ska öppnas när MQTT används som kommunikations protokoll.<li>1883 för MQTT stöds inte av IoT Edge. <li>Inkommande (inkommande) anslutningar ska blockeras.</ul>|
|AMQP|5671|BLOCKERAd (standard)|ÖPPNA (standard)|<ul> <li>Standard kommunikations protokoll för IoT Edge. <li> Måste konfigureras för att vara öppen om Azure IoT Edge inte har kon figurer ATS för andra protokoll som stöds eller om AMQP är det önskade kommunikations protokollet.<li>5672 för AMQP stöds inte av IoT Edge.<li>Blockera den här porten när Azure IoT Edge använder ett annat IoT Hub protokoll som stöds.<li>Inkommande (inkommande) anslutningar ska blockeras.</ul></ul>|
|HTTPS|443|BLOCKERAd (standard)|ÖPPNA (standard)|<ul> <li>Konfigurera utgående (utgående) som ska öppnas på 443 för IoT Edge etablering. Den här konfigurationen krävs när du använder manuella skript eller DPS (Azure IoT Device Provisioning service). <li>Inkommande (inkommande) anslutning ska endast vara öppen för vissa scenarier: <ul> <li>  Om du har en transparent Gateway med löv enheter som kan skicka metod begär Anden. I det här fallet behöver port 443 inte vara öppen för externa nätverk för att ansluta till IoTHub eller tillhandahålla IoTHub-tjänster via Azure IoT Edge. Det innebär att den inkommande regeln kan begränsas till endast öppna inkommande (inkommande) från det interna nätverket. <li> För C2D-scenarier (Client to Device).</ul><li>80 för HTTP stöds inte av IoT Edge.<li>Om icke-HTTP-protokoll (till exempel AMQP eller MQTT) inte kan konfigureras i företaget. meddelandena kan skickas via WebSockets. Port 443 kommer att användas för WebSocket-kommunikation i så fall.</ul>|

## <a name="next-steps"></a>Nästa steg

Tror du att du har hittat ett fel i IoT Edge-plattformen? [Skicka in ett ärende](https://github.com/Azure/iotedge/issues) så att vi kan fortsätta att förbättra.

Om du har fler frågor kan du skapa en [supportbegäran](https://portal.azure.com/#create/Microsoft.Support) för hjälp.
