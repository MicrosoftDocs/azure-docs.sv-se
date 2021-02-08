---
title: Anpassade säkerhets aviseringar
description: Lär dig mer om anpassningsbara säkerhets varningar och rekommenderade åtgärder med hjälp av Defender för IoT-funktioner och-tjänster.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 021b05176da141fad2f4555b0617b5a4a51e453f
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809294"
---
# <a name="defender-for-iot-custom-security-alerts"></a>Defender för anpassade säkerhets aviseringar för IoT

Defender för IoT analyserar kontinuerligt din IoT-lösning med avancerad analys och hot information för att varna dig om skadlig aktivitet.

Vi rekommenderar att du skapar anpassade aviseringar baserat på dina kunskaper om förväntad enhets beteende för att säkerställa att aviseringar fungerar som de mest effektiva indikatorerna för potentiell kompromiss i din unika organisations distribution och liggande.

Följande listor över Defender för IoT-aviseringar är definierbara utifrån din förväntade IoT Hub och/eller enhets beteende. Mer information om hur du anpassar varje avisering finns i [skapa anpassade aviseringar](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Inbyggda anpassade aviseringar i IoT Hub

| Allvarlighetsgrad | Aviseringsnamn | Datakälla | Description | Rekommenderad reparation |
|--|--|--|--|--|
| Låg | Anpassad avisering – antalet moln till enhets meddelanden i AMQP-protokollet ligger utanför det tillåtna intervallet | IoT Hub | Antalet moln till enhets meddelanden (AMQP-protokoll) inom ett särskilt tidsintervall är utanför det konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – antalet nekade moln till enhets meddelanden i AMQP-protokollet ligger utanför det tillåtna intervallet | IoT Hub | Antalet meddelanden om moln till enhet (AMQP-protokoll) som avvisats av enheten inom ett särskilt tidsintervall är utanför det konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – antalet enheter till moln meddelanden i AMQP-protokollet ligger utanför det tillåtna intervallet | IoT Hub | Mängden enhet till moln meddelanden (AMQP-protokoll) inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – antalet direkta metod anrop är utanför det tillåtna intervallet | IoT Hub | Mängden direkta metod anrop inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – antalet fil överföringar ligger utanför det tillåtna intervallet | IoT Hub | Mängden fil överföringar inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – antalet moln till enhets meddelanden i HTTP-protokollet ligger utanför det tillåtna intervallet | IoT Hub | Mängden moln till enhets meddelanden (HTTP-protokoll) i ett tids fönster är inte inom det konfigurerade tillåtna intervallet |
| Låg | Anpassad avisering – antalet nekade moln till enhets meddelanden i HTTP-protokollet är inte inom det tillåtna intervallet | IoT Hub | Mängden moln till enhets meddelanden (HTTP-protokoll) inom en angiven tids period är utanför det konfigurerade och tillåtna intervallet. |
| Låg | Anpassad avisering – antalet enheter till moln meddelanden i HTTP-protokollet ligger utanför det tillåtna intervallet | IoT Hub | Mängden enhets-till-moln-meddelanden (HTTP-protokoll) inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – antalet moln till enhets meddelanden i MQTT-protokollet ligger utanför det tillåtna intervallet | IoT Hub | Mängden moln till enhets meddelanden (MQTT-protokoll) inom ett visst tidsintervall är utanför det aktuella konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – antalet nekade moln till enhets meddelanden i MQTT-protokollet ligger utanför det tillåtna intervallet | IoT Hub | Mängden moln till enhets meddelanden (MQTT-protokoll) som avvisats av enheten inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet. |
| Låg | Anpassad avisering – antalet enheter till moln meddelanden i MQTT-protokollet ligger utanför det tillåtna intervallet | IoT Hub | Mängden enhet till moln meddelanden (MQTT-protokoll) inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet. |
| Låg | Anpassad avisering – antalet rensningar av kommando köer som ligger utanför det tillåtna intervallet | IoT Hub | Mängden kommando köer som rensas inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – antalet dubbla uppdateringar i modulen ligger utanför det tillåtna intervallet | IoT Hub | Mängden dubbla uppdateringar inom en bestämd tids period ligger utanför det konfigurerade och tillåtna intervallet. |
| Låg | Anpassad avisering – antalet otillåtna åtgärder ligger utanför det tillåtna intervallet | IoT Hub | Mängden ej auktoriserade åtgärder inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet. |


## <a name="agent-based-security-custom-alerts"></a>Anpassade aviseringar om agentbaserade säkerhetsfunktioner

| Allvarlighetsgrad | Aviseringsnamn | Datakälla | Description | Rekommenderad reparation |
|--|--|--|--|--|
| Låg | Anpassad avisering – antalet aktiva anslutningar ligger utanför det tillåtna intervallet | Klassisk säkerhetsmodul, Azure-återställnings tider | Antalet aktiva anslutningar inom ett särskilt tidsintervall är utanför det aktuella konfigurerade och tillåtna intervallet. | Undersök enhets loggarna. Lär dig var anslutningen kommer och ta reda på om den är ofarlig eller skadlig. Ta bort eventuell skadlig kod och förstå källan om det är skadligt. Om det är ofarligt kan du lägga till källan i listan över tillåtna anslutningar. |
| Låg | Anpassad avisering – utgående anslutning som skapats till en IP-adress som inte är tillåten | Klassisk säkerhetsmodul, Azure-återställnings tider | En utgående anslutning skapades till en IP-adress som ligger utanför din tillåtna IP-lista. | Undersök enhets loggarna. Lär dig var anslutningen kommer och ta reda på om den är ofarlig eller skadlig. Ta bort eventuell skadlig kod och förstå källan om det är skadligt. Om det är ofarligt kan du lägga till källan i listan över tillåtna IP-adresser. |
| Låg | Anpassad avisering – antalet misslyckade lokala inloggningar ligger utanför det tillåtna intervallet | Klassisk säkerhetsmodul, Azure-återställnings tider | Antalet misslyckade lokala inloggningar inom ett särskilt tidsintervall är utanför det aktuella konfigurerade och tillåtna intervallet. |  |
| Låg | Anpassad avisering – inloggningen för en användare som inte finns med i listan över tillåtna användare | Klassisk säkerhetsmodul, Azure-återställnings tider | En lokal användare utanför listan över tillåtna användare som är inloggad på enheten. | Om du sparar rå data navigerar du till ditt Log Analytics-konto och använder data för att undersöka enheten, identifiera källan och sedan reparera listan över tillåtna/blockerade för dessa inställningar. Om du inte sparar rå data för tillfället går du till enheten och korrigerar listan över tillåtna/blockerade för dessa inställningar. |
| Låg | Anpassad avisering – en process kördes som inte är tillåten | Klassisk säkerhetsmodul, Azure-återställnings tider | En process som inte är tillåten kördes på enheten. | Om du sparar rå data navigerar du till ditt Log Analytics-konto och använder data för att undersöka enheten, identifiera källan och sedan reparera listan över tillåtna/blockerade för dessa inställningar. Om du inte sparar rå data för tillfället går du till enheten och korrigerar listan över tillåtna/blockerade för dessa inställningar. |
|

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [anpassar en avisering](quickstart-create-custom-alerts.md)
- [Översikt över](overview.md) Defender for IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
