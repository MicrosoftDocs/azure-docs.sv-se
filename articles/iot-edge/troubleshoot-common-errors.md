---
title: Vanliga fel – Azure IoT Edge | Microsoft Docs
description: Använd den här artikeln för att lösa vanliga problem som uppstår när du distribuerar en IoT Edge-lösning
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
ms.openlocfilehash: cc6d7491d9c38f1ddf4aba2adecad4aaee3c344b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489588"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Vanliga problem och lösningar för Azure IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Använd den här artikeln för att hitta steg för att lösa vanliga problem som kan uppstå när du distribuerar IoT Edge-lösningar. Om du behöver lära dig hur du hittar loggar och fel från din IoT Edge-enhet läser du [felsöka IoT Edge-enheten](troubleshoot.md).

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agenten stoppas efter en minut

**Observerat beteende:**

EdgeAgent-modulen startar och körs för ungefär en minut och stoppas sedan. Loggarna indikerar att IoT Edge-agenten försöker ansluta till IoT Hub över AMQP och försöker sedan ansluta med AMQP över WebSocket. När det Miss lyckas avslutas IoT Edge agenten.

Exempel på edgeAgent-loggar:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Rotor saken:**

En nätverks konfiguration på värd nätverket hindrar IoT Edge-agenten från att nå nätverket. Agenten försöker ansluta via AMQP (port 5671) först. Om anslutningen Miss lyckas försöker den WebSockets (port 443).

IoT Edge-körningen ställer in ett nätverk för varje modul att kommunicera på. På Linux är nätverket en nätverksbrygga. I Windows använder den NAT. Det här problemet är vanligare på Windows-enheter som använder Windows-containrar som använder NAT-nätverket.

**Lösning:**

Kontrollera att det finns en väg till internet för IP-adresserna som är tilldelade till den här nätverksbryggan/NAT-nätverket. Ibland åsidosätter en VPN-konfiguration på värden IoT Edge-nätverket.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agenten har inte åtkomst till en moduls avbildning (403)

**Observerat beteende:**

Det går inte att köra en behållare och edgeAgent-loggarna visar ett 403-fel.

**Rotor saken:**

IoT Edge agent har inte behörighet att komma åt en moduls avbildning.

**Lösning:**

Kontrol lera att autentiseringsuppgifterna för registret är korrekt angivna i distributions manifestet.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge agent module rapporterar tom konfigurations fil och inga moduler startar på enheten

**Observerat beteende:**

Enheten har problem med att starta moduler som definierats i distributionen. Endast edgeAgent körs men löpande rapportering av Tom config-fil...

**Rotor saken:**

Som standard startar IoT Edge moduler i egna isolerade behållar nätverk. Enheten kan ha problem med DNS-namnmatchning i det här privata nätverket.

**Lösning:**

**Alternativ 1: Ange DNS-server i behållar motor inställningarna**

Ange DNS-servern för din miljö i inställningar för container motor som ska gälla för alla behållar moduler som startas av motorn. Skapa en fil med namnet `daemon.json` Ange den DNS-server som ska användas. Exempel:

```json
{
    "dns": ["1.1.1.1"]
}
```

Exemplet ovan anger DNS-servern till en offentligt tillgänglig DNS-tjänst. Om gräns enheten inte kan komma åt den här IP-adressen från dess miljö ersätter du den med den DNS-serveradress som är tillgänglig.

Placera `daemon.json` på rätt plats för din plattform:

| Plattform | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-värd med Windows-behållare | `C:\ProgramData\iotedge-moby\config` |

Om platsen redan innehåller `daemon.json` fil, lägger du till **DNS-** nyckeln till den och sparar filen.

Starta om behållar motorn för att uppdateringarna ska börja gälla.

| Plattform | Kommando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (admin PowerShell) | `Restart-Service iotedge-moby -Force` |

**Alternativ 2: Ange DNS-server i IoT Edge distribution per modul**

Du kan ställa in DNS-servern för varje moduls *createOptions* i IoT Edge distributionen. Exempel:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Se till att ange den här konfigurationen för *edgeAgent* -och *edgeHub* -modulerna också.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hubben kan inte startas

**Observerat beteende:**

EdgeHub-modulen kan inte startas. Du kan se ett meddelande som ett av följande fel i loggarna:

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

Eller

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**Rotor saken:**

En annan process på värddatorn har bundit en port som edgeHub-modulen försöker binda. IoT Edge Hub mappar portarna 443, 5671 och 8883 för användning i Gateway-scenarier. Modulen kan inte startas om en annan process redan har bundit en av dessa portar.

**Lösning:**

Du kan lösa det här problemet på två sätt:

Om IoT Edge enheten fungerar som en gateway-enhet måste du söka efter och stoppa den process som använder port 443, 5671 eller 8883. Ett fel för port 443 innebär vanligt vis att den andra processen är en webb server.

Om du inte behöver använda den IoT Edge enheten som en gateway, kan du ta bort port bindningarna från edgeHub för modulens skapande alternativ. Du kan ändra alternativen för att skapa i Azure Portal eller direkt i deployment.jspå filen.

I Azure-portalen:

1. Navigera till din IoT-hubb och välj **IoT Edge**.

2. Välj den IoT Edge enhet som du vill uppdatera.

3. Välj **Ange moduler**.

4. Välj **körnings inställningar**.

5. Ta bort allt från text rutan **skapa alternativ** i inställningarna för **Edge Hub** -modulen.

6. Spara ändringarna och skapa distributionen.

I deployment.jspå filen:

1. Öppna deployment.jspå filen som du använde på IoT Edge enheten.

2. Hitta `edgeHub` inställningarna i avsnittet önskade egenskaper för edgeAgent:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Ta bort `createOptions` raden och avslutande kommatecken i slutet av `image` raden innan den:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Spara filen och tillämpa den på din IoT Edge enhet igen.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge Security daemon Miss lyckas med ett ogiltigt värdnamn

**Observerat beteende:**

Försök att [kontrol lera IoT Edge Security Manager-loggarna](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) Miss lyckas och skriver ut följande meddelande:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Rotor saken:**

IoT Edge runtime kan bara stödja värdnamn som är kortare än 64 tecken. Fysiska datorer har vanligt vis inte långa värdnamn, men problemet är vanligare på en virtuell dator. Automatiskt genererade värdnamn för virtuella Windows-datorer som är värdbaserade i Azure, i synnerhet tenderar att vara långa.

**Lösning:**

När du ser det här felet kan du lösa det genom att konfigurera den virtuella datorns DNS-namn och sedan ange DNS-namnet som värdnamn i installations kommandot.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. I Azure Portal går du till översikts sidan för den virtuella datorn.
2. Välj **Konfigurera** under DNS-namn. Om du redan har konfigurerat ett DNS-namn på den virtuella datorn behöver du inte konfigurera någon ny.

   ![Konfigurera DNS-namnet för den virtuella datorn](./media/troubleshoot/configure-dns.png)

3. Ange ett värde för **DNS-namn etikett** och välj **Spara**.
4. Kopiera det nya DNS-namnet som ska vara i formatet **\<DNSnamelabel\> . \<vmlocation\> . cloudapp.azure.com**.
5. I den virtuella datorn använder du följande kommando för att konfigurera IoT Edge runtime med ditt DNS-namn:

   * I Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * I Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. I Azure Portal går du till översikts sidan för den virtuella datorn.

2. Välj **Konfigurera** under DNS-namn. Om du redan har konfigurerat ett DNS-namn på den virtuella datorn behöver du inte konfigurera någon ny.

   ![Konfigurera DNS-namnet för den virtuella datorn](./media/troubleshoot/configure-dns.png)

3. Ange ett värde för **DNS-namn etikett** och välj **Spara**.

4. Kopiera det nya DNS-namnet som ska vara i formatet **\<DNSnamelabel\> . \<vmlocation\> . cloudapp.azure.com**.

5. Öppna konfigurations filen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

6. Ersätt värdet för `hostname` med ditt DNS-namn.

7. Spara och Stäng filen och tillämpa sedan ändringarna på IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Det går inte att hämta IoT Edge daemon-loggar i Windows

**Observerat beteende:**

Du får en EventLogException när du använder `Get-WinEvent` i Windows.

**Rotor saken:**

`Get-WinEvent`PowerShell-kommandot är beroende av att en register post är tillgänglig för att hitta loggar med en speciell `ProviderName` .

**Lösning:**

Ange en register post för IoT Edge daemon. Skapa en **iotedge. reg** -fil med följande innehåll och importera den till Windows-registret genom att dubbelklicka på den eller använda `reg import iotedge.reg` kommandot:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>Stabilitets problem på mindre enheter

**Observerat beteende:**

Det kan uppstå stabilitets problem på resurs begränsade enheter som Raspberry Pi, särskilt när de används som en gateway. Symptom innehåller minnes undantag i IoT Edge Hub-modulen, underordnade enheter som inte kan anslutas eller enheten kan inte skicka telemetridata efter några timmar.

**Rotor saken:**

IoT Edge Hub, som är en del av IoT Edge runtime, är optimerad för prestanda som standard och försöker allokera stora mängder minne. Den här optimeringen är inte idealisk för begränsade gräns enheter och kan orsaka stabilitets problem.

**Lösning:**

För IoT Edge Hub ställer du in en miljö variabel **OptimizeForPerformance** på **falskt**. Det finns två sätt att ange miljövariabler:

I Azure-portalen:

I IoT Hub väljer du IoT Edge enheten och på sidan enhets information och väljer **Ange**  >  **Inställningar** för moduler. Skapa en miljö variabel för IoT Edge Hub-modulen med namnet *OptimizeForPerformance* som har angetts till *false*.

![OptimizeForPerformance har angetts till false](./media/troubleshoot/optimizeforperformance-false.png)

I distributions manifestet:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge modul kan inte skicka ett meddelande till edgeHub med 404-fel

**Observerat beteende:**

En anpassad IoT Edge-modul kan inte skicka ett meddelande till IoT Edge Hub med ett 404- `Module not found` fel. IoT Edge daemon skriver ut följande meddelande till loggarna:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Rotor saken:**

IoT Edge daemon framtvingar process-ID för alla moduler som ansluter till edgeHub av säkerhets skäl. Den verifierar att alla meddelanden som skickas av en modul kommer från huvud process-ID: t för modulen. Om ett meddelande skickas av en modul från ett annat process-ID än vad som upprättats, kommer det att neka meddelandet med ett 404-fel meddelande.

**Lösning:**

Från och med version 1.0.7 har alla modulblad behörighet att ansluta. Mer information finns i [1.0.7 Release ändringsloggen](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Om det inte går att uppgradera till 1.0.7 utför du följande steg. Kontrol lera att samma process-ID alltid används av den anpassade IoT Edge-modulen för att skicka meddelanden till edgeHub. Till exempel, se till i `ENTRYPOINT` stället för `CMD` kommandot i Docker-filen. `CMD`Kommandot leder till ett process-ID för modulen och ett annat process-ID för bash-kommandot som kör huvud programmet, men `ENTRYPOINT` leder till ett enda process-ID.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge-modulen har distribuerats försvinner sedan från enheten

**Observerat beteende:**

När du har angett moduler för en IoT Edge enhet distribueras modulerna, men efter några minuter försvinner de från enheten och från enhets informationen i Azure Portal. Andra moduler än de som definierats kan också visas på enheten.

**Rotor saken:**

Om en automatisk distribution är riktad mot en enhet, prioriteras den manuellt för att ställa in moduler för en enskild enhet. Funktionen **Ange moduler** i Azure Portal eller **skapa distribution för en enskild enhets** funktion i Visual Studio Code börjar gälla för en stund. Du ser de moduler som du har definierat start på enheten. Sedan arbetar den automatiska distributions prioriteten i och skriver över enhetens önskade egenskaper.

**Lösning:**

Använd bara en typ av distributions mekanism per enhet, antingen en automatisk distribution eller en enskild enhets distribution. Om du har flera automatiska distributioner som riktar sig mot en enhet kan du ändra prioritet eller mål beskrivningar för att kontrol lera att rätt en gäller för en specifik enhet. Du kan också uppdatera enheten så att den inte längre matchar mål beskrivningen för den automatiska distributionen.

Mer information finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>IoT Edge bakom en gateway kan inte utföra HTTP-begäranden och starta edgeAgent-modulen

**Observerat beteende:**

IoT Edge daemon är aktiv med en giltig konfigurations fil, men kan inte starta edgeAgent-modulen. Kommandot `iotedge list` returnerar en tom lista. Rapporten IoT Edge daemon-loggar `Could not perform HTTP request` .

**Rotor saken:**

IoT Edge enheter bakom en gateway får sina modulblad från den överordnade IoT Edges enhet som anges i `parent_hostname` fältet i konfigurations filen. `Could not perform HTTP request`Felet innebär att den underordnade enheten inte kan komma åt den överordnade enheten via http.

**Lösning:**

Kontrol lera att den överordnade IoT Edges enheten kan ta emot inkommande begär Anden från den underordnade IoT Edge enheten. Öppna Nätverks trafik på portarna 443 och 6617 för begär Anden som kommer från den underordnade enheten.

:::moniker-end

## <a name="next-steps"></a>Nästa steg

Tror du att du har hittat ett fel i IoT Edge-plattformen? [Skicka in ett ärende](https://github.com/Azure/iotedge/issues) så att vi kan fortsätta att förbättra.

Om du har fler frågor kan du skapa en [supportbegäran](https://portal.azure.com/#create/Microsoft.Support) för hjälp.
