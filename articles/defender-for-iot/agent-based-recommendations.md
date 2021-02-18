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
ms.openlocfilehash: c7407db1460c2d0a83f2262a3348c81b4de300a7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642594"
---
# <a name="security-recommendations-for-iot-devices"></a>Säkerhets rekommendationer för IoT-enheter

Defender för IoT genomsöker dina Azure-resurser och IoT-enheter och ger säkerhets rekommendationer för att minska risken för attacker.
Säkerhets rekommendationer är åtgärds bara och syftar till att hjälpa kunder att följa rekommenderade säkerhets metoder.

I den här artikeln hittar du en lista över rekommendationer som kan utlösas på dina IoT-enheter.

## <a name="agent-based-recommendations"></a>Agentbaserade rekommendationer

Enhets rekommendationer ger insikter och förslag för att förbättra position för enhets säkerhet.

| Allvarlighetsgrad | Name | Datakälla | Description |
|--|--|--|--|
| Medel | Öppna portar på enheten | Klassisk säkerhetsmodul | En lyssnande slut punkt hittades på enheten. |
| Medel | Princip för att tillåta brand vägg hittas i en av kedjorna. | Klassisk säkerhetsmodul | Tillåten brand Väggs princip hittades (indata/utdata). Brand Väggs principen bör neka all trafik som standard och definiera regler för att tillåta nödvändig kommunikation till/från enheten. |
| Medel | En tillåtande brand Väggs regel i indatamängden hittades | Klassisk säkerhetsmodul | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar. |
| Medel | En tillåtande brand Väggs regel i utgående kedja hittades | Klassisk säkerhetsmodul | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar. |
| Medel | Det gick inte att verifiera operativ systemets bas linje | Klassisk säkerhetsmodul | Enheten är inte kompatibel med [CIS Linux-benchmarks](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Agentbaserade operativa rekommendationer

Drift rekommendationer ger insikter och förslag för att förbättra konfigurationen av säkerhets agenten.

| Allvarlighetsgrad | Name | Datakälla | Description |
|--|--|--|--|
| Låg | Agent skickar meddelanden som inte används | Klassisk säkerhetsmodul | 10% eller flera säkerhets meddelanden var mindre än 4 KB under de senaste 24 timmarna. |
| Låg | Säkerhet, dubbel konfiguration inte optimal | Klassisk säkerhetsmodul | En dubbel säkerhets konfiguration är inte optimalt. |
| Låg | Konflikt vid dubbel konfiguration av säkerhet | Klassisk säkerhetsmodul | Konflikter identifierades i den dubbla säkerheten. |  |

## <a name="next-steps"></a>Nästa steg

- [Översikt över](overview.md) Defender for IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
