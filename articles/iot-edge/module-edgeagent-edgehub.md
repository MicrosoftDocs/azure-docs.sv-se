---
title: Egenskaper för agenten och hubbmodultvillingarna – Azure IoT Edge
description: Granska de specifika egenskaperna och deras värden för edgeAgent- och edgeHub-modultvillingarna
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29ec958764f4a464d51f29f4b9c8223d5d7a1760
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576014"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Egenskaper för IoT Edge och IoT Edge hubbmodultvillingarna

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Den IoT Edge agenten IoT Edge hubben är två moduler som utgör IoT Edge-körningen. Mer information om ansvarsområdena för varje körningsmodul finns i Förstå Azure IoT Edge [och dess arkitektur.](iot-edge-runtime.md)

Den här artikeln innehåller önskade egenskaper och rapporterade egenskaper för körningsmodultvillingarna. Mer information om hur du distribuerar moduler på IoT Edge enheter finns i Lär dig hur du distribuerar moduler och [etablerar vägar i IoT Edge](module-composition.md).

En modultvilling innehåller:

* **Önskade egenskaper**. Lösningens backend kan ange önskade egenskaper och modulen kan läsa dem. Modulen kan också ta emot meddelanden om ändringar i önskade egenskaper. Önskade egenskaper används tillsammans med rapporterade egenskaper för att synkronisera modulkonfiguration eller villkor.

* **Rapporterade egenskaper**. Modulen kan ange rapporterade egenskaper, och lösningens backend kan läsa och köra frågor mot dem. Rapporterade egenskaper används tillsammans med önskade egenskaper för att synkronisera modulkonfiguration eller villkor.

## <a name="edgeagent-desired-properties"></a>Önskade egenskaper för EdgeAgent

Modultvillingen för IoT Edge-agenten anropas och samordnar kommunikationen mellan den IoT Edge agent som körs på en `$edgeAgent` enhet och IoT Hub. Önskade egenskaper anges när du tillämpar ett distributionsmanifest på en specifik enhet som en del av en distribution på en enskild enhet eller i stor skala.

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| schemaVersion | Antingen "1.0" eller "1.1". Version 1.1 introducerades med IoT Edge version 1.0.10 och rekommenderas. | Yes |
| runtime.type | Måste vara "docker" | Yes |
| runtime.settings.minDockerVersion | Ange till den lägsta Docker-version som krävs av det här distributionsmanifestet | Yes |
| runtime.settings.loggingOptions | En strängifierad JSON som innehåller loggningsalternativen för IoT Edge agentcontainern. [Alternativ för Docker-loggning](https://docs.docker.com/engine/admin/logging/overview/) | No |
| runtime.settings.registryCredentials<br>. {registryId}.username | Användarnamnet för containerregistret. För Azure Container Registry är användarnamnet vanligtvis registernamnet.<br><br> Registerautentiseringsuppgifter krävs för alla privata modulavbildningar. | No |
| runtime.settings.registryCredentials<br>. {registryId}.password | Lösenordet för containerregistret. | No |
| runtime.settings.registryCredentials<br>. {registryId}.address | Adressen till containerregistret. För Azure Container Registry är adressen vanligtvis *{registernamn}.azurecr.io*. | No |  
| systemModules.edgeAgent.type | Måste vara "docker" | Yes |
| systemModules.edgeAgent.settings.image | URI:en för bilden av IoT Edge agenten. För närvarande kan IoT Edge-agenten inte uppdatera sig själv. | Yes |
| systemModules.edgeAgent.settings<br>.createOptions | En strängifierad JSON som innehåller alternativen för att skapa IoT Edge agentcontainern. [Alternativ för att skapa Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | ID:t för distributionen som distribuerade den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Inte en del av ett distributionsmanifest. |
| systemModules.edgeHub.type | Måste vara "docker" | Yes |
| systemModules.edgeHub.status | Måste vara "körs" | Yes |
| systemModules.edgeHub.restartPolicy | Måste vara "alltid" | Yes |
| systemModules.edgeHub.startupOrder | Ett heltalsvärde för vilket en modul kan upptäckas i startordningen. 0 är första och max heltal (4294967295) är sist. Om inget värde anges är standardvärdet max heltal.  | No |
| systemModules.edgeHub.settings.image | URI:en för avbildningen av IoT Edge hubb. | Yes |
| systemModules.edgeHub.settings<br>.createOptions | En strängifierad JSON som innehåller alternativen för att skapa IoT Edge hubbcontainer. [Alternativ för att skapa Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | ID:t för den distribution som distribuerade den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Inte en del av ett distributionsmanifest. |
| Modules. {moduleId}.version | En användardefinierad sträng som representerar versionen av den här modulen. | Yes |
| Modules. {moduleId}.type | Måste vara "docker" | Yes |
| Modules. {moduleId}.status | {"running" \| "stoppad"} | Yes |
| Modules. {moduleId}.restartPolicy | {"aldrig" \| "on-failure" \| "on-unhealthy" \| "always"} | Yes |
| Modules. {moduleId}.startupOrder | Ett heltalsvärde som en modul för oskadd kan ha i startordningen. 0 är första och max heltal (4294967295) är sist. Om inget värde anges är standardvärdet max heltal.  | No |
| Modules. {moduleId}.imagePullPolicy | {"on-create" \| "aldrig"} | No |
| Modules. {moduleId}.env | En lista över miljövariabler som ska överföras till modulen. Har formatet `"<name>": {"value": "<value>"}` | No |
| Modules. {moduleId}.settings.image | URI:en till modulavbildningen. | Yes |
| Modules. {moduleId}.settings.createOptions | En strängifierad JSON som innehåller alternativen för att skapa modulcontainern. [Alternativ för att skapa Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| Modules. {moduleId}.configuration.id | ID för den distribution som distribuerade den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Inte en del av ett distributionsmanifest. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent-rapporterade egenskaper

Egenskaperna IoT Edge agenten innehåller tre huvudsakliga uppgifter:

1. Status för tillämpningen av de senast sedd önskade egenskaperna.
2. Status för de moduler som för närvarande körs på enheten, enligt IoT Edge agenten; Och
3. En kopia av önskade egenskaper som för närvarande körs på enheten.

Kopian av de aktuella önskade egenskaperna är användbar för att se om enheten har tillämpat den senaste distributionen eller fortfarande kör ett tidigare distributionsmanifest.

> [!NOTE]
> De rapporterade egenskaperna för IoT Edge-agenten är användbara eftersom de kan efterfrågas [med IoT Hub-frågespråket](../iot-hub/iot-hub-devguide-query-language.md) för att undersöka statusen för distributioner i stor skala. Mer information om hur du använder IoT Edge agentegenskaper för status finns i [Förstå IoT Edge-distributioner](module-deployment-monitoring.md)för enskilda enheter eller i stor skala.

Följande tabell innehåller inte den information som kopieras från önskade egenskaper.

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Det här heltalet refererar till den senaste versionen av önskade egenskaper som bearbetas av IoT Edge agenten. |
| lastDesiredStatus.code | Den här statuskoden refererar till de senast önskade egenskaperna som IoT Edge agenten. Tillåtna värden: `200` Lyckades, `400` Ogiltig konfiguration, `412` Ogiltig schemaversion, `417` önskade egenskaper är tomma, `500` Misslyckades |
| lastDesiredStatus.description | Textbeskrivning av statusen |
| configurationHealth. {deploymentId}.health | `healthy` om körningsstatusen för alla moduler som angetts av distributionen {deploymentId} är antingen `running` eller `stopped` , `unhealthy` annars |
| runtime.platform.OS | Rapportera operativsystemet som körs på enheten |
| runtime.platform.architecture | Rapportera cpu-arkitekturen på enheten |
| systemModules.edgeAgent.runtimeStatus | Rapporterad status för IoT Edge agent: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Textbeskrivning av rapporterad status för IoT Edge agenten. |
| systemModules.edgeHub.runtimeStatus | Status för IoT Edge hub: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Textbeskrivning av status för IoT Edge om den är skadad. |
| systemModules.edgeHub.exitCode | Slutkoden som rapporteras av IoT Edge hub-containern om containern avslutas |
| systemModules.edgeHub.startTimeUtc | Tid när IoT Edge senast startades |
| systemModules.edgeHub.lastExitTimeUtc | Tid när IoT Edge senast avslutats |
| systemModules.edgeHub.lastRestartTimeUtc | Tid när IoT Edge senast startades om |
| systemModules.edgeHub.restartCount | Antal gånger som den här modulen startades om som en del av omstartsprincipen. |
| Modules. {moduleId}.runtimeStatus | Status för modulen: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| Modules. {moduleId}.statusDescription | Textbeskrivning av status för modulen om den är skadad. |
| Modules. {moduleId}.exitCode | Slutkoden som rapporteras av modulcontainern om containern avslutas |
| Modules. {moduleId}.startTimeUtc | Tid när modulen senast startades |
| Modules. {moduleId}.lastExitTimeUtc | Tid när modulen senast avslutats |
| Modules. {moduleId}.lastRestartTimeUtc | Tid då modulen senast startades om |
| Modules. {moduleId}.restartCount | Antal gånger som den här modulen startades om som en del av omstartsprincipen. |

## <a name="edgehub-desired-properties"></a>Önskade egenskaper för EdgeHub

Modultvillingen för IoT Edge anropas och samordnar kommunikationen mellan IoT Edge som körs på en enhet `$edgeHub` och IoT Hub. De önskade egenskaperna anges när du tillämpar ett distributionsmanifest på en specifik enhet som en del av en distribution på en enskild enhet eller i stor skala.

| Egenskap | Beskrivning | Krävs i distributionsmanifestet |
| -------- | ----------- | -------- |
| schemaVersion | Antingen "1.0" eller "1.1". Version 1.1 introducerades med IoT Edge version 1.0.10 och rekommenderas. | Yes |
| Vägar. {routeName} | En sträng som representerar IoT Edge en hubbväg. Mer information finns i [Deklarera vägar.](module-composition.md#declare-routes) | Elementet `routes` kan finnas men vara tomt. |
| storeAndForwardConfiguration.timeToLiveSecs | Den tid i sekunder som IoT Edge hubben behåller meddelanden om de är frånkopplade från routningsslutpunkter, IoT Hub eller en lokal modul. Värdet kan vara vilket positivt heltal som helst. | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub-rapporterade egenskaper

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Det här heltalsnumret avser den senaste versionen av de önskade egenskaper som bearbetas IoT Edge hubben. |
| lastDesiredStatus.code | Statuskoden som refererar till de senast önskade egenskaperna som IoT Edge hubben. Tillåtna värden: `200` Lyckades, `400` Ogiltig konfiguration, `500` Misslyckades |
| lastDesiredStatus.description | Textbeskrivning av statusen. |
| Klienter. {device or moduleId}.status | Anslutningsstatus för den här enheten eller modulen. Möjliga värden {"connected" \| "disconnected"}. Endast modulidentiteter kan vara i frånkopplat tillstånd. Nedströmsenheter som ansluter IoT Edge hubben visas bara när de är anslutna. |
| Klienter. {device or moduleId}.lastConnectTime | Senaste anslutning av enheten eller modulen. |
| Klienter. {device or moduleId}.lastDisconnectTime | Senaste frånkopplade enheten eller modulen. |

## <a name="next-steps"></a>Nästa steg

Information om hur du använder dessa egenskaper för att bygga ut distributionsmanifest finns i Förstå [hur IoT Edge-moduler kan användas, konfigureras och återanvändas.](module-composition.md)
