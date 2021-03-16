---
title: Egenskaperna för agent-och Hub-modulen är dubbla Azure IoT Edge
description: Granska de olika egenskaperna och deras värden för edgeAgent-och edgeHub-modulen är dubbla
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/31/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 951111b217b7ace3f12676edf6febfa7266094df
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489956"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Egenskaperna för IoT Edge agenten och IoT Edge Hub-modulen är dubbla

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge agent och IoT Edge hubb är två moduler som utgör IoT Edge Runtime. Mer information om ansvars områden för varje körnings modul finns i [förstå Azure IoT Edge körning och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller de önskade egenskaperna och de rapporterade egenskaperna för körnings modulen är dubbla. Mer information om hur du distribuerar moduler på IoT Edge enheter finns i [Lär dig hur du distribuerar moduler och upprättar vägar i IoT Edge](module-composition.md).

En modul som är dubbel innehåller:

* **Önskade egenskaper**. Lösningens Server del kan ange önskade egenskaper och modulen kan läsa dem. Modulen kan också ta emot aviseringar om ändringar i önskade egenskaper. Önskade egenskaper används tillsammans med rapporterade egenskaper för att synkronisera konfiguration eller villkor för modulen.

* **Rapporterade egenskaper**. Modulen kan ange rapporterade egenskaper och Server delen kan läsa och fråga dem. Rapporterade egenskaper används tillsammans med önskade egenskaper för att synkronisera konfiguration eller villkor för modulen.

## <a name="edgeagent-desired-properties"></a>EdgeAgent önskade egenskaper

Modulerna för IoT Edge agenten anropas `$edgeAgent` och koordinerar kommunikationen mellan IoT Edge-agenten som körs på en enhet och IoT Hub. De önskade egenskaperna anges när du tillämpar ett distributions manifest på en viss enhet som en del av en distribution med en enda enhet eller i skala.

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| Schema | Antingen "1,0" eller "1,1". Version 1,1 introducerades med IoT Edge version 1.0.10 och rekommenderas. | Ja |
| Runtime. Type | Måste vara "Docker" | Ja |
| Runtime. Settings. minDockerVersion | Ange den lägsta Docker-version som krävs av distributions manifestet | Ja |
| Runtime. Settings. loggingOptions | En stringified-JSON som innehåller loggnings alternativen för IoT Edge agent-behållaren. [Docker-loggnings alternativ](https://docs.docker.com/engine/admin/logging/overview/) | Inga |
| Runtime. Settings. registryCredentials<br>. {registryId}. användar namn | Användar namnet för behållar registret. För Azure Container Registry är användar namnet vanligt vis register namnet.<br><br> Autentiseringsuppgifter för registret krävs för bilder i en privat modul. | Inga |
| Runtime. Settings. registryCredentials<br>. {registryId}. lösen ord | Lösen ordet för behållar registret. | Inga |
| Runtime. Settings. registryCredentials<br>. {registryId}. adress | Adress till behållar registret. För Azure Container Registry är adressen vanligt vis *{Registry Name}. azurecr. io*. | Inga |  
| systemModules. edgeAgent. Type | Måste vara "Docker" | Ja |
| systemModules. edgeAgent. Settings. image | URI för avbildningen av IoT Edge agenten. IoT Edge-agenten kan för närvarande inte uppdatera sig själv. | Ja |
| systemModules. edgeAgent. Settings<br>.createOptions | En stringified-JSON som innehåller alternativen för att skapa IoT Edge agent-behållaren. [Alternativ för Docker-skapande](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Inga |
| systemModules.edgeAgent.configuration.id | ID för distributionen som distribuerade den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Ingår inte i ett distributions manifest. |
| systemModules. edgeHub. Type | Måste vara "Docker" | Ja |
| status för systemModules. edgeHub. | Måste vara "igång" | Ja |
| systemModules. edgeHub. restartPolicy | Måste vara "Always" | Ja |
| systemModules.edgeHub.startupOrder | Ett heltals värde för vilket plats en modul har i Start ordningen. 0 är första och högsta heltal (4294967295) är sist. Om ett värde inte anges är standardvärdet Max heltal.  | Inga |
| systemModules. edgeHub. Settings. image | URI för avbildningen av IoT Edge Hub. | Ja |
| systemModules. edgeHub. Settings<br>.createOptions | En stringified-JSON som innehåller alternativen för att skapa IoT Edge Hub-behållaren. [Alternativ för Docker-skapande](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Inga |
| systemModules.edgeHub.configuration.id | ID för distributionen som distribuerade den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Ingår inte i ett distributions manifest. |
| moduler. {moduleId}. version | En användardefinierad sträng som representerar versionen för den här modulen. | Ja |
| moduler. {moduleId}. typ | Måste vara "Docker" | Ja |
| moduler. {moduleId}. status | {"körs" \| "stoppad"} | Ja |
| moduler. {moduleId}. restartPolicy | {"aldrig" \| "vid fel" \| "på-ohälsosam" " \| Always"} | Ja |
| moduler. {moduleId}. startupOrder | Ett heltals värde för vilket plats en modul har i Start ordningen. 0 är första och högsta heltal (4294967295) är sist. Om ett värde inte anges är standardvärdet Max heltal.  | Inga |
| moduler. {moduleId}. imagePullPolicy | {"på-skapa" \| "aldrig"} | Inga |
| moduler. {moduleId}. kuvert | En lista med miljövariabler som ska skickas till modulen. Använder formatet `"<name>": {"value": "<value>"}` | Inga |
| moduler. {moduleId}. Settings. image | URI: n till modulens bild. | Ja |
| moduler. {moduleId}. Settings. createOptions | En stringified-JSON som innehåller alternativen för att skapa en modul-behållare. [Alternativ för Docker-skapande](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Inga |
| moduler. {moduleId}. Configuration. ID | ID för distributionen som distribuerade den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Ingår inte i ett distributions manifest. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent rapporterade egenskaper

De egenskaper som rapporteras av IoT Edges Agent innehåller tre huvud delar av information:

1. Status för programmet för de senast visade önskade egenskaperna.
2. Status för de moduler som för närvarande körs på enheten, enligt rapporter från IoT Edge agenten. särskilt
3. En kopia av önskade egenskaper som körs på enheten.

Kopian av de aktuella önskade egenskaperna är användbar för att avgöra om enheten har tillämpat den senaste distributionen eller fortfarande kör ett tidigare distributions manifest.

> [!NOTE]
> De rapporterade egenskaperna för IoT Edge agenten är användbara eftersom de kan frågas med det [IoT Hub frågespråket](../iot-hub/iot-hub-devguide-query-language.md) för att undersöka statusen för distributioner i stor skala. Mer information om hur du använder egenskaperna för IoT Edge agent för status finns i [förstå IoT Edge distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

Följande tabell innehåller inte den information som kopieras från önskade egenskaper.

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Detta heltal refererar till den senaste versionen av önskade egenskaper som bearbetas av IoT Edge agenten. |
| lastDesiredStatus. Code | Den här status koden refererar till de senast önskade egenskaperna som visas av IoT Edge agenten. Tillåtna värden: `200` lyckad, `400` ogiltig konfiguration, `412` ogiltig schema version, `417` önskade egenskaper är tomma, `500` misslyckades |
| lastDesiredStatus. Description | Text Beskrivning av status |
| deviceHealth | `healthy` om körnings status för alla moduler är antingen `running` eller `stopped` , `unhealthy` annars |
| configurationHealth. {deploymentId}. hälsa | `healthy` om körnings status för alla moduler som anges av distributionen {deploymentId} är antingen `running` eller `stopped` , `unhealthy` annars |
| Runtime. Platform. OS | Rapportera operativ systemet som körs på enheten |
| Runtime. Platform. Architecture | Rapportera CPU-arkitekturen på enheten |
| systemModules.edgeAgent.runtimeStatus | Den rapporterade statusen för IoT Edge agent: {"kör" \| "ohälsosam"} |
| systemModules.edgeAgent.statusDescription | Text Beskrivning av den rapporterade statusen för IoT Edge agenten. |
| systemModules.edgeHub.runtimeStatus | Status för IoT Edge Hub: {"igång" "" stoppad "" misslyckades "" \| \| \| backoff "" är inte \| felfritt "} |
| systemModules.edgeHub.statusDescription | Text Beskrivning av status för IoT Edge hubb om den är felfri. |
| systemModules. edgeHub. exitCode | Slut koden som rapporteras av IoT Edge Hub-behållaren om behållaren avslutas |
| systemModules.edgeHub.startTimeUtc | Tid när IoT Edge hubben senast startades |
| systemModules.edgeHub.lastExitTimeUtc | Tid när IoT Edge Hub senast avslutades |
| systemModules.edgeHub.lastRestartTimeUtc | Tid när IoT Edge hubben senast startades om |
| systemModules.edgeHub.restartCount | Antal gånger som den här modulen startats om som en del av principen för omstart. |
| moduler. {moduleId}. runtimeStatus | Status för modulen: {"igång" "" stoppad "" misslyckades "" \| \| \| backoff " \| " är inte felfri "} |
| moduler. {moduleId}. statusDescription | Text Beskrivning av status för modulen om den inte är felfri. |
| moduler. {moduleId}. exitCode | Slut koden som rapporteras av modulen container om behållaren avslutas |
| moduler. {moduleId}. startTimeUtc | Tid när modulen senast startades |
| moduler. {moduleId}. lastExitTimeUtc | Tid när modulen senast avslutades |
| moduler. {moduleId}. lastRestartTimeUtc | Tid när modulen senast startades om |
| moduler. {moduleId}. restartCount | Antal gånger som den här modulen startats om som en del av principen för omstart. |

## <a name="edgehub-desired-properties"></a>EdgeHub önskade egenskaper

Modulen för IoT Edge Hub anropas `$edgeHub` och koordinerar kommunikationen mellan IoT Edge hubben som körs på en enhet och IoT Hub. De önskade egenskaperna anges när du tillämpar ett distributions manifest på en viss enhet som en del av en distribution med en enda enhet eller i skala.

| Egenskap | Beskrivning | Krävs i distributions manifestet |
| -------- | ----------- | -------- |
| Schema | Antingen "1,0" eller "1,1". Version 1,1 introducerades med IoT Edge version 1.0.10 och rekommenderas. | Ja |
| cirkulera. RouteName | En sträng som representerar en IoT Edge Hub-väg. Mer information finns i [deklarera vägar](module-composition.md#declare-routes). | `routes`Elementet kan vara tillgängligt men tomt. |
| storeAndForwardConfiguration.timeToLiveSecs | Tiden i sekunder som IoT Edge hubben bevarar meddelanden om de är frånkopplade från routning slut punkter, oavsett om de IoT Hub eller en lokal modul. Värdet kan vara valfritt positivt heltal. | Ja |

## <a name="edgehub-reported-properties"></a>EdgeHub rapporterade egenskaper

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Detta heltal refererar till den senaste versionen av önskade egenskaper som bearbetas av IoT Edge Hub. |
| lastDesiredStatus. Code | Status koden som refererar till de senaste önskade egenskaperna som visas av IoT Edge Hub. Tillåtna värden: `200` lyckad, `400` ogiltig konfiguration, `500` misslyckades |
| lastDesiredStatus. Description | Text Beskrivning av statusen. |
| klienter. {Device eller moduleId}. status | Enhetens eller modulens anslutnings status. Möjliga värden {"ansluten" \| "kopplade från"}. Endast modul identiteter kan vara i frånkopplat tillstånd. Underordnade enheter som ansluter till IoT Edge Hub visas bara när de är anslutna. |
| klienter. {Device eller moduleId}. lastConnectTime | Senaste gången då enheten eller modulen anslöts. |
| klienter. {Device eller moduleId}. lastDisconnectTime | Senaste gången enheten eller modulen kopplades från. |

## <a name="next-steps"></a>Nästa steg

Information om hur du använder dessa egenskaper för att bygga distributions manifest finns i [förstå hur IoT Edge moduler kan användas, konfigureras och återanvändas](module-composition.md).
