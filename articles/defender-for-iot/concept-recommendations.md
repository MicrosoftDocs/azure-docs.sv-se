---
title: Säkerhetsrekommendationer
description: Lär dig mer om begreppet säkerhets rekommendationer och hur de används i Defender för IoT.
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
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: 10246277715d23f42eb0bd17d19794b8d4503f3f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521874"
---
# <a name="security-recommendations"></a>Säkerhetsrekommendationer

Defender för IoT genomsöker dina Azure-resurser och IoT-enheter och ger säkerhets rekommendationer för att minska risken för attacker.
Säkerhets rekommendationer är åtgärds bara och syftar till att hjälpa kunder att följa rekommenderade säkerhets metoder.

I den här artikeln hittar du en lista över rekommendationer som kan utlösas på dina IoT Hub-och/eller IoT-enheter.

## <a name="agent-based-recommendations"></a>Agentbaserade rekommendationer

Enhets rekommendationer ger insikter och förslag för att förbättra position för enhets säkerhet.

| Allvarlighetsgrad | Namn | Datakälla | Beskrivning |
|--|--|--|--|
| Medel | Öppna portar på enheten | Klassisk säkerhetsmodul | En lyssnande slut punkt hittades på enheten. |
| Medel | Princip för att tillåta brand vägg hittas i en av kedjorna. | Klassisk säkerhetsmodul | Tillåten brand Väggs princip hittades (indata/utdata). Brand Väggs principen bör neka all trafik som standard och definiera regler för att tillåta nödvändig kommunikation till/från enheten. |
| Medel | En tillåtande brand Väggs regel i indatamängden hittades | Klassisk säkerhetsmodul | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar. |
| Medel | En tillåtande brand Väggs regel i utgående kedja hittades | Klassisk säkerhetsmodul | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för en mängd olika IP-adresser eller portar. |
| Medel | Det gick inte att verifiera operativ systemets bas linje | Klassisk säkerhetsmodul | Enheten är inte kompatibel med [CIS Linux-benchmarks](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Agentbaserade drift rekommendationer

Drift rekommendationer ger insikter och förslag för att förbättra konfigurationen av säkerhets agenten.

| Allvarlighetsgrad | Namn | Datakälla | Beskrivning |
|--|--|--|--|
| Låg | Agent skickar meddelanden som inte används | Klassisk säkerhetsmodul | 10% eller flera säkerhets meddelanden var mindre än 4 KB under de senaste 24 timmarna. |
| Låg | Säkerhet, dubbel konfiguration inte optimal | Klassisk säkerhetsmodul | En dubbel säkerhets konfiguration är inte optimalt. |
| Låg | Konflikt vid dubbel konfiguration av säkerhet | Klassisk säkerhetsmodul | Konflikter identifierades i den dubbla säkerheten. |  |


## <a name="built-in-recommendations-in-iot-hub"></a>Inbyggda rekommendationer i IoT Hub

Rekommendations aviseringar ger insikter och förslag på åtgärder för att förbättra position säkerhets miljö.

| Allvarlighetsgrad | Namn | Datakälla | Beskrivning |
|--|--|--|--|
| Högt | Identiska autentiseringsuppgifter för autentisering används av flera enheter | IoT Hub | IoT Hub autentiseringsuppgifter för autentisering används av flera enheter. Den här processen kan tyda på en Illegitimate-enhet som personifierar en legitim enhet. Dubblett av autentiseringsuppgifter ökar risken för personifiering av enheter av en skadlig aktör. |
| Medel | Standard princip för IP-filter ska nekas | IoT Hub | IP-filterlistan måste ha definierade regler för tillåten trafik och ska som standard neka all annan trafik som standard. |
| Medel | IP-filterlistan innehåller stora IP-intervall | IoT Hub | En Tillåt källa för IP-filterlistan är för stor. Överskrivna regler kan exponera IoT-hubben för skadliga aktörer. |
| Låg | Aktivera diagnostikloggar i IoT Hub | IoT Hub | Aktivera loggar och behåll dem i upp till ett år. Om du behåller loggarna kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |

## <a name="next-steps"></a>Nästa steg

- [Översikt över](overview.md) Defender for IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
