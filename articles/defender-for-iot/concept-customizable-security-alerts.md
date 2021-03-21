---
title: Anpassade säkerhets aviseringar för IoT Hub
description: Lär dig mer om anpassningsbara säkerhets varningar och rekommenderade åtgärder med hjälp av Defender för IoT Hubens funktioner och tjänster.
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
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: 04198432f2b600a3c703d5e4f253656f116000db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636534"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Defender för IoT Hub anpassade säkerhets aviseringar

Defender för IoT analyserar kontinuerligt din IoT-lösning med avancerad analys och hot information för att varna dig om skadlig aktivitet.

Vi rekommenderar att du skapar anpassade aviseringar baserat på dina kunskaper om förväntad enhets beteende för att säkerställa att aviseringar fungerar som de mest effektiva indikatorerna för potentiell kompromiss i din unika organisations distribution och liggande.

Följande listor över Defender för IoT-aviseringar är definierbara av dig baserat på det förväntade IoT Hub beteendet. Mer information om hur du anpassar varje avisering finns i [skapa anpassade aviseringar](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Inbyggda anpassade aviseringar i IoT Hub

| Allvarlighetsgrad | Aviseringsnamn | Datakälla | Beskrivning | Rekommenderad reparation |
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

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [anpassar en avisering](quickstart-create-custom-alerts.md)
- [Översikt över](overview.md) Defender for IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
