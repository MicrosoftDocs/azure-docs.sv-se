---
title: Övervaka modul dubblare – Azure IoT Edge
description: Så här tolkar du enheternas nätverks-och modulerna för att fastställa anslutningar och hälsa.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0b7013979199eefa873a651d99e87dc8b2c47856
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201603"
---
# <a name="monitor-module-twins"></a>Övervaka modultvillingar

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Modul dubbla i Azure IoT Hub aktivera övervakning av anslutningen och hälsan för dina IoT Edge-distributioner. Modulerna innehåller värdefull information i din IoT-hubb om prestanda för dina moduler som körs. [IoT Edge-agenten](iot-edge-runtime.md#iot-edge-agent) och modulerna för [IoT Edge Hub](iot-edge-runtime.md#iot-edge-hub) -körning upprätthåller sin modul, `$edgeAgent` och `$edgeHub` respektive:

* `$edgeAgent` innehåller hälso tillstånds-och anslutnings data om både IoT Edge-agenten och IoT Edge Hub runtime-moduler och dina anpassade moduler. IoT Edge agenten ansvarar för att distribuera modulerna, övervaka dem och rapportera anslutnings status till Azure IoT Hub.
* `$edgeHub` innehåller data om kommunikation mellan IoT Edge hubben som körs på en enhet och Azure IoT Hub. Detta omfattar att bearbeta inkommande meddelanden från underordnade enheter. IoT Edge Hub ansvarar för bearbetning av kommunikationen mellan Azure-IoT Hub och IoT Edge enheter och moduler.

Data ordnas i metadata, taggar, tillsammans med önskade och rapporterade egenskaps uppsättningar i modulen är dubbla JSON-strukturer. De önskade egenskaperna som du angav i deployment.jspå filen kopieras till modulen. IoT Edge agenten och IoT Edge Hub uppdaterar de rapporterade egenskaperna för sina moduler.

På samma sätt kopieras de önskade egenskaperna för dina anpassade moduler i deployment.jspå filen till dess modul, men din lösning ansvarar för att tillhandahålla dess rapporterade egenskaps värden.

Den här artikeln beskriver hur du granskar modulen i Azure Portal, Azure CLI och i Visual Studio Code. Information om hur du övervakar hur enheterna får distributioner finns i [övervaka IoT Edge distributioner](how-to-monitor-iot-edge-deployments.md). En översikt över hur modulen är uppflätad finns i [förstå och använda modulerna med dubbla i IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).

> [!TIP]
> De rapporterade egenskaperna för en körnings modul kan vara inaktuella om en IoT Edge-enhet kopplas bort från sin IoT Hub. Du kan [pinga](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` modulen för att avgöra om anslutningen bröts.

## <a name="monitor-runtime-module-twins"></a>Övervakare av körnings modul

Om du vill felsöka problem med distributions anslutningar granskar du IoT Edge agent och IoT Edge Hub-körnings modul, och sedan går du vidare till andra moduler.

### <a name="monitor-iot-edge-agent-module-twin"></a>Övervaka IoT Edge agent modul, dubbla

Följande JSON visar `$edgeAgent` modulen i Visual Studio Code med de flesta JSON-avsnitten komprimerade.

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

JSON kan beskrivas i följande avsnitt, med början från början:

* Metadata – innehåller anslutnings data. Ett intressant läge är att anslutnings läget för IoT Edge agent alltid är i frånkopplat tillstånd: `"connectionState": "Disconnected"` . Anledningen till att anslutnings statusen gäller för D2C-meddelanden (Device-to-Cloud) och IoT Edge-agenten skickar inte D2C-meddelanden.
* Egenskaper – innehåller `desired` `reported` underavsnitten och.
* Egenskaper. önskad (dolda) förväntade egenskaps värden som angetts av operatorn i deployment.jsi filen.
* Egenskaper. rapporterade-de senaste egenskaps värden som rapporter ATS av IoT Edge agent.

Både `properties.desired` avsnitten och `properties.reported` har en liknande struktur och innehåller ytterligare metadata för schema-, versions-och körnings information. Det ingår också i `modules` avsnittet för alla anpassade moduler (till exempel `SimulatedTemperatureSensor` ) och i `systemModules` avsnittet `$edgeAgent` och i runtime- `$edgeHub` modulerna.

Genom att jämföra de rapporterade egenskapsvärdena mot de önskade värdena kan du fastställa avvikelser och identifiera från kopplingar som kan hjälpa dig att felsöka problem. När du gör dessa jämförelser kontrollerar du det `$lastUpdated` rapporterade värdet i `metadata` avsnittet för den egenskap du undersöker.

Följande egenskaper är viktiga för att söka efter fel sökning:

* **ExitCode** – andra värden än noll anger att modulen stoppades med ett haveri. Dock används felkoderna 137 eller 143 om en modul har avsiktligt ställts in på en stoppad status.

* **lastStartTimeUtc** – visar det **datum/tid** -värde som behållaren senast startades. Värdet är 0,001-01-01T00:00:00Z om behållaren inte startades.

* **lastExitTimeUtc** – visar det **datum/tid** -värde som behållaren senast avslutades. Värdet är 0,001-01-01T00:00:00Z om behållaren körs och inte har stoppats.

* **runtimeStatus** -kan vara något av följande värden:

    | Värde | Beskrivning |
    | --- | --- |
    | okänd | Standard tillstånd tills distributionen skapas. |
    | backoff | Modulen är schemalagd att starta men körs inte för tillfället. Det här värdet är användbart för en modul som genomgår status ändringar vid omstarten. När en misslyckad modul väntar på att startas om under den avstängda perioden är modulen i ett backoff-tillstånd. |
    | kör | Anger att modulen körs för tillfället. |
    | fel tillstånd | Anger att en hälso avsöknings kontroll misslyckades eller nådde tids gränsen. |
    | stoppats | Anger att modulen har avslut ATS (med slut koden noll). |
    | misslyckades | Anger att modulen avslutades med en avslutnings kod (inte noll). Modulen kan övergå tillbaka till backoff från det här läget, beroende på vilken princip för omstart som används. Det här läget kan tyda på att modulen har drabbats av ett oåterkalleligt fel. Fel inträffar när Microsoft Monitoring Agent (MMA) inte längre kan Resuscitate modulen, vilket kräver en ny distribution. |

Mer information finns i [EdgeAgent rapporterade egenskaper](module-edgeagent-edgehub.md#edgeagent-reported-properties) .

### <a name="monitor-iot-edge-hub-module-twin"></a>Övervaka IoT Edge Hub-modul, dubbla

Följande JSON visar `$edgeHub` modulen i Visual Studio Code med de flesta JSON-avsnitten komprimerade.

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

JSON kan beskrivas i följande avsnitt, med början från början:

* Metadata – innehåller anslutnings data.

* Egenskaper – innehåller `desired` `reported` underavsnitten och.
* Egenskaper. önskad (dolda) förväntade egenskaps värden som angetts av operatorn i deployment.jsi filen.
* Egenskaper. rapporterade-de senaste egenskaps värden som rapporter ATS av IoT Edge Hub.

Om du har problem med dina underordnade enheter kan det vara bra att undersöka dessa data.

## <a name="monitor-custom-module-twins"></a>Övervaka dubbla anpassade moduler

Informationen om anslutningarna för dina anpassade moduler behålls i IoT Edge agent-modul, med dubbla. Modulen för din anpassade modul används främst för att underhålla data för din lösning. De önskade egenskaperna som du definierade i deployment.jspå filen visas i modulen, och modulen kan uppdatera rapporterade egenskaps värden efter behov.

Du kan använda önskat programmeringsspråk med [Azure IoT Hub enhets-SDK: erna](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) för att uppdatera rapporterade egenskaps värden i modulen, baserat på modulens program kod. I följande procedur används Azure SDK för .NET för att göra detta med hjälp av kod från [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) -modulen:

1. Skapa en instans av [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) med [CreateFromEnvironmentAysnc](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) -metoden.

1. Hämta en samling av modulens egenskaper med [GetTwinAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync) -metoden.

1. Skapa en lyssnare (som skickar ett återanrop) för att fånga ändringar av önskade egenskaper med [SetDesiredPropertyUpdateCallbackAsync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) -metoden.

1. I återanrops metoden uppdaterar du de rapporterade egenskaperna i modulen dubbla med metoden [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) och skickar en [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) av de egenskaps värden som du vill ange.

## <a name="access-the-module-twins"></a>Få åtkomst till modulen dubbla

Du kan granska JSON för modulen i Azure IoT Hub, i Visual Studio Code och med Azure CLI.

### <a name="monitor-in-azure-iot-hub"></a>Övervaka i Azure IoT Hub

Så här visar du JSON för modul dubbla:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.
1. Välj **IoT Edge** på menyn i den vänstra rutan.
1. På fliken **IoT Edge enheter** väljer du **enhets-ID** för enheten med de moduler som du vill övervaka.
1. Välj modulnamnet på fliken **moduler** och välj sedan **modulens identitet** i det övre meny fältet.

  ![Välj en modul som är dubbel att visa i Azure Portal](./media/how-to-monitor-module-twins/select-module-twin.png)

Om du ser meddelandet "det finns ingen modul identitet för den här modulen", anger det här felet att Server dels lösningen inte längre är tillgänglig som ursprungligen skapade identiteten.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Övervaka modul dubbla i Visual Studio Code

Så här granskar och redigerar du en modul dubbla:

1. Om du inte redan har installerat installerar du [tillägget Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för Visual Studio Code.
1. I **Utforskaren** expanderar du **Azure-IoT Hub** och expanderar sedan enheten med den modul som du vill övervaka.
1. Högerklicka på modulen och välj **Redigera modul dubbla**. En temporär fil för modulen, som är dubbelt, hämtas till din dator och visas i Visual Studio Code.

  ![Hämta en modul med dubbla för att redigera i Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Om du gör ändringar väljer du **Uppdatera modul dubbla** ovanför koden i redigeraren för att spara ändringarna i IoT-hubben.

  ![Uppdatera en modul med dubbla i Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Övervaka modul dubbla i Azure CLI

Om du vill se om IoT Edge körs använder du [AZ IoT Hub Invoke-module-metoden](how-to-edgeagent-direct-method.md#ping) för att pinga IoT Edge-agenten.

[AZ IoT Hub-modulen – den dubbla](/cli/azure/ext/azure-iot/iot/hub/module-twin) strukturen innehåller följande kommandon:

* **AZ IoT Hub-modul – två show** – visar en modul med dubbla definitioner.
* **AZ IoT Hub-modul-dubbel uppdatering** – uppdatera en modul med dubbla definitioner.
* **AZ IoT Hub-modul – dubbel ersättning** – Ersätt en modul dubbla definitioner med en mål-JSON.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [kommunicerar med EdgeAgent med hjälp av inbyggda direkta metoder](how-to-edgeagent-direct-method.md).
