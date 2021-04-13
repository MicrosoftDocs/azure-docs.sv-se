---
title: IoT Edge versions navigering och historik – Azure IoT Edge
description: Upptäck vad som är nytt i IoT Edge med information om nya funktioner och funktioner i de senaste versionerna.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d3473e975e7c69a83c359b040a2de0defaac69b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310486"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge versioner och viktig information

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge är en produkt som är byggd från projektet med öppen källkod IoT Edge som finns på GitHub. Alla nya versioner görs tillgängliga i Azure IoT Edge- [projektet](https://github.com/Azure/azure-iotedge). Bidrag och fel rapporter kan göras på projektet med [öppen källkod IoT Edge](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Dokumenterade versioner

IoT Edge-dokumentationen på den här webbplatsen är tillgänglig för två olika versioner av produkten så att du kan välja det innehåll som gäller för din IoT Edge-miljö. För närvarande är de två versionerna som stöds:

* **IoT Edge 1,2** innehåller ytterligare innehåll för nya funktioner och funktioner i den senaste stabila versionen.
* **IoT Edge 1,1 (LTS)** är den första långsiktiga support versionen (LTS) av IoT Edge. Dokumentationen för den här versionen omfattar alla funktioner och funktioner från alla tidigare versioner till 1,1. Den här dokumentations versionen blir stabil genom den livs längd som stöds för version 1,1 och kommer inte att återspegla nya funktioner som lanseras i senare versioner.

Mer information om IoT Edge-versioner finns i [Azure IoT Edge system som stöds](support.md).

## <a name="version-history"></a>Versionshistorik

Den här tabellen innehåller den senaste versions historiken för IoT Edge paket versioner och visar dokumentations uppdateringar som har gjorts för respektive version.

| Viktig information och till gångar | Typ | Datum | Höjdpunkter |
| ------------------------ | ---- | ---- | ---------- |
| [1.2](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0) | Stable | April 2021 | [IoT Edge enheter bakom gatewayer](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT-Broker (för hands version)](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true)<br>Nya IoT Edges paket som introducerades, med nya installations-och konfigurations steg. Mer information finns i [Uppdatera från 1,0 eller 1,1 till 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | Långsiktigt stöd (LTS) | Februari 2021 | [Långsiktig Support plan och system uppdateringar som stöds](support.md) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Oktober 2020 | [UploadSupportBundle Direct-metod](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Överför körnings mått](how-to-access-built-in-metrics.md)<br>[Väg prioritet och Time-to-Live](module-composition.md#priority-and-time-to-live)<br>[Start ordning för modul](module-composition.md#configure-modules)<br>[509 manuell etablering för X.](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | Mars 2020 | [X. 509 automatisk etablering med DPS](how-to-auto-provision-x509-certs.md)<br>[RestartModule Direct-metod](how-to-edgeagent-direct-method.md#restart-module)<br>[Support – kommandot bunt](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Nästa steg

* [Visa alla Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases)
* [Göra eller granska funktions förfrågningar i feedback-forumet](https://feedback.azure.com/forums/907045-azure-iot-edge)