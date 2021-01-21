---
title: IoT Edge versions navigering och historik – Azure IoT Edge
description: Upptäck vad som är nytt i IoT Edge med information om nya funktioner och funktioner i de senaste versionerna.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e7b85f3e01a1c9a9c354599cfed8359b4a56d549
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631462"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge versioner och viktig information

Azure IoT Edge är en produkt som är byggd från projektet med öppen källkod IoT Edge som finns på GitHub. Alla nya versioner görs tillgängliga i Azure IoT Edge- [projektet](https://github.com/Azure/azure-iotedge). Bidrag och fel rapporter kan göras på projektet med [öppen källkod IoT Edge](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Dokumenterade versioner

IoT Edge-dokumentationen på den här webbplatsen är tillgänglig för två olika versioner av produkten så att du kan välja det innehåll som gäller för din IoT Edge-miljö. För närvarande är de två versionerna som stöds:

* **IoT Edge 1.0.10** omfattar alla funktioner och funktioner genom den senaste tillgängliga versionen: [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10).
* **IoT Edge 1,2 (för hands version)** innehåller ytterligare innehåll för funktioner och funktioner i den senaste för hands versionen: [1,2 – rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * Medan IoT Edge 1,2 är i för hands version måste du installera versions kandidat versionerna. Mer information finns i [offline-eller en speciell versions installation](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation-optional).

## <a name="version-history"></a>Versionshistorik

Den här tabellen innehåller den senaste versions historiken för IoT Edge paket versioner och visar dokumentations uppdateringar som har gjorts för respektive version.

| Viktig information och till gångar | Typ | Date | Höjdpunkter |
| ------------------------ | ---- | ---- | ---------- |
| [1,2 – rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Förhandsgranskning | November 2020 | [IoT Edge enheter bakom gatewayer](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT-Broker](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Oktober 2020 | [UploadSupportBundle Direct-metod](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Överför körnings mått](how-to-access-built-in-metrics.md)<br>[Väg prioritet och Time-to-Live](module-composition.md#priority-and-time-to-live)<br>[Start ordning för modul](module-composition.md#configure-modules)<br>[509 manuell etablering för X.](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | Mars 2020 | [X. 509 automatisk etablering med DPS](how-to-auto-provision-x509-certs.md)<br>[RestartModule Direct-metod](how-to-edgeagent-direct-method.md#restart-module)<br>[Support – kommandot bunt](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Nästa steg

* [Visa alla Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases)
* [Göra eller granska funktions förfrågningar i feedback-forumet](https://feedback.azure.com/forums/907045-azure-iot-edge)