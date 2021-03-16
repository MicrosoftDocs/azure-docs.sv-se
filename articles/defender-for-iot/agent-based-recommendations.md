---
title: Agentbaserade rekommendationer
titleSuffix: Azure Defender for IoT
description: Lär dig mer om begreppet säkerhets rekommendationer och hur de används för Defender för IoT-enheter.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: e746f37fdf3b67467c1844ebea9191679d52d6d1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491273"
---
# <a name="security-recommendations-for-iot-devices"></a>Säkerhets rekommendationer för IoT-enheter

Defender för IoT genomsöker dina Azure-resurser och IoT-enheter och ger säkerhets rekommendationer för att minska risken för attacker.
Säkerhets rekommendationer är åtgärds bara och syftar till att hjälpa kunder att följa rekommenderade säkerhets metoder.

I den här artikeln hittar du en lista över rekommendationer som kan utlösas på dina IoT-enheter.

## <a name="agent-based-recommendations"></a>Agentbaserade rekommendationer

Enhets rekommendationer ger insikter och förslag för att förbättra position för enhets säkerhet.

| Allvarlighetsgrad | Namn | Datakälla | Beskrivning |
|--|--|--|--|
| Medel | Öppna portar på enheten | Klassisk Defender-IoT-Micro-agent| En lyssnande slut punkt hittades på enheten. |
| Medel | Princip för att tillåta brand vägg hittas i en av kedjorna. | Klassisk Defender-IoT-Micro-agent| Tillåten brand Väggs princip hittades (indata/utdata). Brand Väggs principen bör neka all trafik som standard och definiera regler för att tillåta nödvändig kommunikation till/från enheten. |
| Medel | En tillåtande brand Väggs regel i indatamängden hittades | Klassisk Defender-IoT-Micro-agent| En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar. |
| Medel | En tillåtande brand Väggs regel i utgående kedja hittades | Klassisk Defender-IoT-Micro-agent| En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar. |
| Medel | Det gick inte att verifiera operativ systemets bas linje | Klassisk Defender-IoT-Micro-agent| Enheten är inte kompatibel med [CIS Linux-benchmarks](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Agentbaserade operativa rekommendationer

Drift rekommendationer ger insikter och förslag för att förbättra konfigurationen av säkerhets agenten.

| Allvarlighetsgrad | Namn | Datakälla | Beskrivning |
|--|--|--|--|
| Låg | Agent skickar meddelanden som inte används | Klassisk Defender-IoT-Micro-agent| 10% eller flera säkerhets meddelanden var mindre än 4 KB under de senaste 24 timmarna. |
| Låg | Säkerhet, dubbel konfiguration inte optimal | Klassisk Defender-IoT-Micro-agent| En dubbel säkerhets konfiguration är inte optimalt. |
| Låg | Konflikt vid dubbel konfiguration av säkerhet | Klassisk Defender-IoT-Micro-agent| Konflikter identifierades i den dubbla säkerheten. |  |

## <a name="next-steps"></a>Nästa steg

- [Översikt över](overview.md) Defender for IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
