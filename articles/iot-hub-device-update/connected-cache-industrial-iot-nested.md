---
title: Microsoft Ansluten cache en Azure IoT Edge för industriella IoT-konfigurationsinställningar | Microsoft Docs
description: Microsoft Ansluten cache i en självstudie Azure IoT Edge för industriell IoT-konfiguration
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 083c7bf6edc7da1fd617487e91b0a3848fb401fe
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811828"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Distributionsscenarioexempel Ansluten cache Microsoft Ansluten cache förhandsversion: Microsoft Ansluten cache en Azure IoT Edge för industriella IoT-konfigurationer

Tillverkningsnätverk organiseras ofta i hierarkiska lager enligt [Purdue-nätverksmodellen](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (ingår i [ISA 95-](https://en.wikipedia.org/wiki/ANSI/ISA-95) och [ISA 99-standarderna).](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) I dessa nätverk är det bara det översta lagret som har anslutning till molnet och de lägre lagren i hierarkin kan bara kommunicera med intilliggande, norra och södra skikt.

Det här [GitHub-Azure IoT Edge för industriell IoT](https://github.com/Azure-Samples/iot-edge-for-iiot)distribuerar följande:

* Simulerat purdue-nätverk i Azure
* Industritillgångar 
* Hierarki med Azure IoT Edge gateways
  
Dessa komponenter används för att hämta industriella data och på ett säkert sätt överföra dem till molnet utan att äventyra nätverkets säkerhet. Microsoft Ansluten cache kan distribueras för att stödja nedladdning av innehåll på alla nivåer i det ISA 95-kompatibla nätverket.

Nyckeln för att konfigurera Microsoft Ansluten cache-distributioner i ett ISA 95-kompatibelt nätverk är att konfigurera både *OT-proxyn* och den överordnade värden på L3 IoT Edge-gatewayen.

1. Konfigurera Microsoft Ansluten cache distributioner på nivåerna L5 och L4 enligt beskrivningen i Two-Level kapslade IoT Edge gateway-exemplet 
2. Distributionen på L3 IoT Edge gatewayen måste ange:
   
   * UPSTREAM_HOST – IP-/FQDN för L4 IoT Edge-gatewayen som L3 Microsoft Ansluten cache begär innehåll.
   * UPSTREAM_PROXY – IP/FQDN:PORT för OT-proxyservern.

3. OT-proxyn måste lägga till L4 MCC FQDN/IP-adressen i listan över tillåtna.

Kontrollera att Microsoft Ansluten cache fungerar korrekt genom att köra följande kommando i terminalen för IoT Edge-enheten, som är värd för modulen eller någon annan enhet i nätverket. Ersätt \<Azure IoT Edge Gateway IP\> med IP-adressen eller värdnamnet för din IoT Edge gateway. (se information om miljövariabler för information om synligheten för den här rapporten).

```bash
    wget http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```