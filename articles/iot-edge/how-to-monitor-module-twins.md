---
title: Övervaka modultvillingarna – Azure IoT Edge
description: Så här tolkar du enhetstvillingarna och modultvillingarna för att fastställa anslutningen och hälsotillståndet.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5a31e15c88cef588c93f44c8fe5303d930b5b2c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479380"
---
# <a name="monitor-module-twins"></a>Övervaka modultvillingar

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Modultvillingarna i Azure IoT Hub att övervaka anslutningen och hälsotillståndet för IoT Edge distributioner. Modultvillingarna lagrar användbar information i din IoT-hubb om prestanda för moduler som körs. De [IoT Edge agenten](iot-edge-runtime.md#iot-edge-agent) och IoT Edge-hubbkörningsmodulerna underhåller sina modultvillingarna [](iot-edge-runtime.md#iot-edge-hub) `$edgeAgent` `$edgeHub` respektive:

* `$edgeAgent` innehåller hälso- och anslutningsdata om både IoT Edge-agenten och IoT Edge-hubbkörningsmoduler och dina anpassade moduler. Agenten IoT Edge ansvarar för att distribuera modulerna, övervaka dem och rapportera anslutningsstatus till din Azure IoT Hub.
* `$edgeHub` innehåller data om kommunikationen mellan IoT Edge som körs på en enhet och din Azure IoT Hub. Detta inkluderar bearbetning av inkommande meddelanden från underordnade enheter. IoT Edge ansvarar för att bearbeta kommunikationen mellan Azure IoT Hub och IoT Edge enheter och moduler.

Data är ordnade i metadata, taggar samt önskade och rapporterade egenskapsuppsättningar i modultvillingarnas JSON-strukturer. De önskade egenskaper som du angav i deployment.jspå filen kopieras till modultvillingarna. Varje IoT Edge agent och IoT Edge uppdaterar de rapporterade egenskaperna för sina moduler.

På samma sätt kopieras önskade egenskaper som angetts för dina anpassade moduler i deployment.jspå filen till modultvillingen, men lösningen ansvarar för att ange dess rapporterade egenskapsvärden.

Den här artikeln beskriver hur du granskar modultvillingarna i Azure Portal, Azure CLI och i Visual Studio Code. Information om hur du övervakar hur dina enheter tar emot distributionerna finns i [Övervaka IoT Edge distributioner.](how-to-monitor-iot-edge-deployments.md) En översikt över begreppet modultvilling finns i [Förstå och använda modultvillingarna i IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).

> [!TIP]
> De rapporterade egenskaperna för en körningsmodul kan vara inaktuella om en IoT Edge frånkopplas från sin IoT-hubb. Du kan [pinga](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` modulen för att avgöra om anslutningen har gått förlorad.

## <a name="monitor-runtime-module-twins"></a>Övervaka körningsmodultvilling

Om du vill felsöka anslutningsproblem för distribution kan du granska IoT Edge-agenten och IoT Edge hub runtime-modultvillingarna och sedan öka detaljgranska i andra moduler.

### <a name="monitor-iot-edge-agent-module-twin"></a>Övervaka IoT Edge agentmodultvilling

Följande JSON visar `$edgeAgent` modultvillingen i Visual Studio Code med de flesta JSON-avsnitt komprimerade.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

JSON kan beskrivas i följande avsnitt, med början uppifrån:

* Metadata – Innehåller anslutningsdata. Intressant är att anslutningstillståndet för den IoT Edge agenten alltid är i ett frånkopplat tillstånd: `"connectionState": "Disconnected"` . Orsaken är anslutningstillståndet gäller meddelanden från enheten till molnet (D2C), och IoT Edge agenten skickar inte D2C-meddelanden.
* Egenskaper – Innehåller `desired` `reported` underavsnitten och .
* Properties.desired – (visas komprimerade) Förväntade egenskapsvärden som angetts av operatorn i deployment.jspå filen.
* Properties.reported – Senaste egenskapsvärden som rapporterats IoT Edge agenten.

Både `properties.desired` avsnitten `properties.reported` och har en liknande struktur och innehåller ytterligare metadata för schema-, versions- och körningsinformation. Avsnittet ingår även `modules` för alla anpassade moduler (till exempel ), och avsnittet för och `SimulatedTemperatureSensor` `systemModules` `$edgeAgent` `$edgeHub` körningsmodulerna.

Genom att jämföra de rapporterade egenskapsvärdena med önskade värden kan du fastställa avvikelser och identifiera frånkopplingar som kan hjälpa dig att felsöka problem. När du gör dessa jämförelser kontrollerar du `$lastUpdated` det rapporterade värdet i avsnittet för den egenskap som du `metadata` undersöker.

Följande egenskaper är viktiga att undersöka för felsökning:

* **exitcode** – Ett annat värde än noll anger att modulen stoppades med ett fel. Felkoderna 137 eller 143 används dock om en modul avsiktligt har ställts in på en stoppad status.

* **lastStartTimeUtc** – Visar **den DateTime** som containern senast startades. Det här värdet är 0001-01-01T00:00:00Z om containern inte startades.

* **lastExitTimeUtc** – Visar **den DateTime som** containern senast slutförde. Det här värdet är 0001-01-01T00:00:00Z om containern körs och aldrig stoppades.

* **runtimeStatus** – Kan vara något av följande värden:

    | Värde | Beskrivning |
    | --- | --- |
    | okänd | Standardtillstånd tills distributionen har skapats. |
    | backoff | Modulen är schemalagd att starta men körs inte för närvarande. Det här värdet är användbart för en modul som genomgår tillståndsändringar vid omstart. När en modul som misslyckas väntar på omstart under avstängningsperioden är modulen i ett backoff-tillstånd. |
    | Kör | Anger att modulen körs för närvarande. |
    | Ohälsosamma | Anger att hälsoavsökningskontrollen misslyckades eller att en time out har gått ut. |
    | Stoppat | Anger att modulen har avslutats (med slutkoden noll). |
    | misslyckades | Anger att modulen avslutas med en slutkod för fel (inte noll). Modulen kan gå tillbaka till backoff från det här tillståndet beroende på vilken omstartsprincip som gäller. Det här tillståndet kan indikera att modulen har drabbats av ett oåterkalleligt fel. Felet inträffar när Microsoft Monitoring Agent (MMA) inte längre kan återanvända modulen, vilket kräver en ny distribution. |

Mer information finns i EdgeAgent reported properties (Rapporterade egenskaper för [EdgeAgent).](module-edgeagent-edgehub.md#edgeagent-reported-properties)

### <a name="monitor-iot-edge-hub-module-twin"></a>Övervaka IoT Edge hub-modultvilling

Följande JSON visar `$edgeHub` modultvillingen i Visual Studio Code med de flesta JSON-avsnitt komprimerade.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

JSON kan beskrivas i följande avsnitt, med början uppifrån:

* Metadata – Innehåller anslutningsdata.

* Egenskaper – Innehåller `desired` `reported` underavsnitten och .
* Properties.desired – (visas komprimerade) Förväntade egenskapsvärden som angetts av operatorn i deployment.jspå filen.
* Properties.reported – De senaste egenskapsvärdena som rapporterats IoT Edge hubben.

Om du har problem med dina nedströmsenheter kan det vara bra att börja med att undersöka dessa data.

## <a name="monitor-custom-module-twins"></a>Övervaka anpassade modultvillingar

Informationen om anslutningen för dina anpassade moduler finns i IoT Edge agentmodultvilling. Modultvillingen för din anpassade modul används främst för att underhålla data för din lösning. De önskade egenskaper som du definierade deployment.jspå filen återspeglas i modultvillingen och modulen kan uppdatera rapporterade egenskapsvärden efter behov.

Du kan använda det programmeringsspråk du föredrar [med Azure IoT Hub Device-SDK:er](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) för att uppdatera rapporterade egenskapsvärden i modultvillingen baserat på modulens programkod. Följande procedur använder Azure SDK för .NET för att göra detta med hjälp av kod från [modulen SimulatedTemperatureSensor:](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs)

1. Skapa en instans av [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) med metoden [CreateFromEnvironmentAysnc.](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync)

1. Hämta en samling av modultvillingens egenskaper med [metoden GetTwinAsync.](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync)

1. Skapa en lyssnare (skicka ett återanrop) för att fånga upp ändringar i önskade egenskaper med metoden [SetDesiredPropertyUpdateCallbackAsync.](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync)

1. I motringningsmetoden uppdaterar du de rapporterade egenskaperna i modultvillingen med metoden [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) och anger en [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) för de egenskapsvärden som du vill ange.

## <a name="access-the-module-twins"></a>Få åtkomst till modultvillingarna

Du kan granska JSON för modultvillingarna i Azure IoT Hub, i Visual Studio Code och med Azure CLI.

### <a name="monitor-in-azure-iot-hub"></a>Övervaka i Azure IoT Hub

Så här visar du JSON för modultvillingen:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din IoT-hubb.
1. Välj **IoT Edge** på menyn till vänster.
1. På fliken **IoT Edge enheter** väljer du **Enhets-ID** för enheten med de moduler som du vill övervaka.
1. Välj modulnamnet på fliken **Moduler och** välj sedan **Modulidentitetstvilling** på den övre menyraden.

  ![Välj en modultvilling att visa i Azure Portal](./media/how-to-monitor-module-twins/select-module-twin.png)

Om du ser meddelandet "Det finns ingen modulidentitet för den här modulen" anger det här felet att backend-lösningen inte längre är tillgänglig som ursprungligen skapade identiteten.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Övervaka modultvillingarna i Visual Studio Code

Så här granskar och redigerar du en modultvilling:

1. Om det inte redan är installerat installerar [du Azure IoT Tools-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för Visual Studio Code.
1. I **Utforskaren** expanderar **du Azure IoT Hub** och expanderar sedan enheten med den modul som du vill övervaka.
1. Högerklicka på modulen och välj Redigera **modultvilling.** En tillfällig fil med modultvillingen laddas ned till datorn och visas i Visual Studio Code.

  ![Hämta en modultvilling att redigera i Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Om du gör ändringar väljer du **Uppdatera modultvilling** ovanför koden i redigeraren för att spara ändringarna i din IoT-hubb.

  ![Uppdatera en modultvilling i Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Övervaka modultvillingarna i Azure CLI

Om du vill IoT Edge körs använder du [az iot hub invoke-module-method](how-to-edgeagent-direct-method.md#ping) för att pinga IoT Edge agenten.

Strukturen [az iot hub module-twin](/cli/azure/iot/hub/module-twin) innehåller följande kommandon:

* **az iot hub module-twin show** – Visa en modultvillingdefinition.
* **az iot hub module-twin update** – Uppdatera en modultvillingdefinition.
* **az iot hub module-twin replace** – Ersätt en modultvillingdefinition med en mål-JSON.

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du kommunicerar med EdgeAgent med hjälp av inbyggda direktmetoder](how-to-edgeagent-direct-method.md).
