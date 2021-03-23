---
title: Anpassade aviseringar för agentbaserade säkerhet
description: Lär dig mer om anpassningsbara säkerhets varningar och rekommenderade åtgärder med hjälp av Defender för IoT-enhetens funktioner och tjänster.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 2fb1385c12cbd9d0479d8528f54aad8816393ad1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784925"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Defender för IoT-enheter anpassade säkerhets aviseringar

Defender för IoT analyserar kontinuerligt din IoT-lösning med avancerad analys och hot information för att varna dig om skadlig aktivitet.

Vi rekommenderar att du skapar anpassade aviseringar baserat på dina kunskaper om förväntad enhets beteende för att säkerställa att aviseringar fungerar som de mest effektiva indikatorerna för potentiell kompromiss i din unika organisations distribution och liggande.

Följande listor över Defender för IoT-aviseringar är definierbara av dig baserat på din förväntade funktion för IoT-enheter. Mer information om hur du anpassar varje avisering finns i [skapa anpassade aviseringar](quickstart-create-custom-alerts.md).

## <a name="agent-based-security-custom-alerts"></a>Anpassade aviseringar om agentbaserade säkerhetsfunktioner

| Allvarlighetsgrad | Aviseringsnamn | Datakälla | Beskrivning | Rekommenderad reparation |
|--|--|--|--|--|
| Låg | Anpassad avisering – antalet aktiva anslutningar ligger utanför det tillåtna intervallet | Klassisk Defender-IoT-Micro-agent, Azure återställnings tider | Antalet aktiva anslutningar inom ett särskilt tidsintervall är utanför det aktuella konfigurerade och tillåtna intervallet. | Undersök enhets loggarna. Lär dig var anslutningen kommer och ta reda på om den är ofarlig eller skadlig. Ta bort eventuell skadlig kod och förstå källan om det är skadligt. Om det är ofarligt kan du lägga till källan i listan över tillåtna anslutningar. |
| Låg | Anpassad avisering – utgående anslutning som skapats till en IP-adress som inte är tillåten | Klassisk Defender-IoT-Micro-agent, Azure återställnings tider | En utgående anslutning skapades till en IP-adress som ligger utanför din tillåtna IP-lista. | Undersök enhets loggarna. Lär dig var anslutningen kommer och ta reda på om den är ofarlig eller skadlig. Ta bort eventuell skadlig kod och förstå källan om det är skadligt. Om det är ofarligt kan du lägga till källan i listan över tillåtna IP-adresser. |
| Låg | Anpassad avisering – antalet misslyckade lokala inloggningar ligger utanför det tillåtna intervallet | Klassisk Defender-IoT-Micro-agent, Azure återställnings tider | Antalet misslyckade lokala inloggningar inom ett särskilt tidsintervall är utanför det aktuella konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – inloggningen för en användare som inte finns med i listan över tillåtna användare | Klassisk Defender-IoT-Micro-agent, Azure återställnings tider | En lokal användare utanför listan över tillåtna användare som är inloggad på enheten. | Om du sparar rå data navigerar du till ditt Log Analytics-konto och använder data för att undersöka enheten, identifiera källan och sedan reparera listan över tillåtna/blockerade för dessa inställningar. Om du inte sparar rå data för tillfället går du till enheten och korrigerar listan över tillåtna/blockerade för dessa inställningar. |
| Låg | Anpassad avisering – en process kördes som inte är tillåten | Klassisk Defender-IoT-Micro-agent, Azure återställnings tider | En process som inte är tillåten kördes på enheten. | Om du sparar rå data navigerar du till ditt Log Analytics-konto och använder data för att undersöka enheten, identifiera källan och sedan reparera listan över tillåtna/blockerade för dessa inställningar. Om du inte sparar rå data för tillfället går du till enheten och korrigerar listan över tillåtna/blockerade för dessa inställningar. |
|

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [anpassar en avisering](quickstart-create-custom-alerts.md)
- [Översikt över](overview.md) Defender for IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
