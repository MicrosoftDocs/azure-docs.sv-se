---
title: Microsoft Connected cache i ett Azure IoT Edge för industriell IoT-konfiguration | Microsoft Docs
description: Microsoft Connected cache i en Azure IoT Edge för självstudier för industriell IoT-konfiguration
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d70ed8b906c171c001c5bda81a79ca9b65febac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101665034"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Exempel på distributions scenario för för hands version av Microsoft Connected cache: Microsoft anslöt cache i en Azure IoT Edge för konfiguration av industriell IoT

Tillverknings nätverk organiseras ofta i hierarkiska lager efter [Purdue-nätverks modellen](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (ingår i [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) och [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) -standarder). I dessa nätverk är det bara det översta lagret som har anslutning till molnet och de nedre lagren i hierarkin kan bara kommunicera med intilliggande Nord-och syd-lager.

Det här GitHub-exemplet [Azure IoT Edge för industriell IoT](https://github.com/Azure-Samples/iot-edge-for-iiot)och distribuerar följande:

* Simulerat Purdue-nätverk i Azure
* Industritillgångar 
* Hierarki med Azure IoT Edge-gatewayer
  
Dessa komponenter kommer att användas för att förvärva industriella data och överföra dem på ett säkert sätt till molnet utan att kompromissa med säkerheten i nätverket. Microsoft Connected cache kan distribueras för att stödja nedladdning av innehåll på alla nivåer i det ISA 95-kompatibla nätverket.

Den nyckel som krävs för att konfigurera Microsoft-anslutna cache-distributioner i ett ISA 95-kompatibelt nätverk konfigurerar både den andra nyckeln *och* den överordnade värden på L3 IoT Edge gatewayen.

1. Konfigurera Microsoft Connected cache-distributioner på L5-och L4-nivåerna enligt beskrivningen i Two-Level kapslade IoT Edge Gateway-exempel 
2. Distributionen vid L3 IoT Edge gatewayen måste ange:
   
   * UPSTREAM_HOST-IP/FQDN för den L4 IoT Edge-gatewayen, vilken L3 Microsoft Connected cache kommer att begära innehåll.
   * UPSTREAM_PROXY-IP/FQDN: porten för den indataport proxyservern.

3. Den måste lägga till den L4 MCC FQDN/IP-adressen i listan över tillåtna.

Du kan kontrol lera att Microsoft Connected cache fungerar korrekt genom att köra följande kommando i terminalen av den IoT Edge enheten, vara värd för modulen eller någon annan enhet i nätverket.

```bash
    wget "http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```