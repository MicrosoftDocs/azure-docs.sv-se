---
title: Inbyggda & anpassade aviserings listan
description: Lär dig mer om säkerhets aviseringar och rekommenderade åtgärder med hjälp av Defender för IoT Hub funktioner och tjänster.
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
ms.openlocfilehash: ef33851600c576494e4e0903c6ab8ffefc9a1a59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636500"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Defender för IoT Hub säkerhets aviseringar

Defender för IoT analyserar kontinuerligt din IoT-lösning med avancerad analys och hot information för att varna dig om skadlig aktivitet.
Dessutom kan du skapa anpassade aviseringar baserat på dina kunskaper om förväntat enhets beteende.
En avisering fungerar som en indikator för potentiella kompromisser och bör undersökas och åtgärdas.

I den här artikeln hittar du en lista över inbyggda aviseringar som kan utlösas på IoT Hub.
Förutom inbyggda aviseringar kan du med hjälp av Defender för IoT definiera anpassade aviseringar baserat på förväntade IoT Hub och/eller enhets beteende.
Mer information finns i [anpassningsbara aviseringar](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Inbyggda aviseringar för IoT Hub

| Allvarlighetsgrad | Name | Beskrivning | Rekommenderad reparation |
|--|--|--|--|
| **Medelhög** allvarlighets grad |  |  |  |
| Nytt certifikat har lagts till i en IoT Hub | Medel | Ett certifikat med namnet \' % {DescCertificateName} har \' lagts till i IoT Hub \' % {DescIoTHubName} \' . Om den här åtgärden har gjorts av en obehörig part kan det indikera skadlig aktivitet. | 1. kontrol lera att certifikatet har lagts till av en auktoriserad part. <br> 2. om den inte har lagts till av en auktoriserad part tar du bort certifikatet och eskalerar aviseringen till organisationens säkerhets team. |
| Certifikatet har tagits bort från en IoT Hub | Medel | Ett certifikat med namnet \' % {DescCertificateName} togs \' bort från IoT Hub \' % {DescIoTHubName} \' . Om den här åtgärden har gjorts av en obehörig part kan det tyda på en skadlig aktivitet. | 1. kontrol lera att certifikatet har tagits bort av en auktoriserad part. <br> 2. om certifikatet inte har tagits bort av en auktoriserad part lägger du till certifikatet igen och eskalerar aviseringen till organisationens säkerhets team. |
| Ett misslyckat försök har upptäckts för att lägga till ett certifikat i en IoT Hub | Medel | Ett misslyckat försök att lägga till certifikatet \' % {DescCertificateName} har gjorts \' till IoT Hub \' % {DescIoTHubName} \' . Om den här åtgärden har gjorts av en obehörig part kan det indikera skadlig aktivitet. | Se till att behörigheter för att ändra certifikat endast beviljas till behöriga parter. |
| Ett misslyckat försök har upptäckts för att ta bort ett certifikat från en IoT Hub | Medel | Det gick inte att ta bort certifikatet \' % {DescCertificateName} \' från IoT Hub \' % {DescIoTHubName} \' . Om den här åtgärden har gjorts av en obehörig part kan det indikera skadlig aktivitet. | Se till att behörigheter för att ändra certifikat endast beviljas till en auktoriserad part. |
| 509 för enhets certifikatets tumavtryck matchar inte | Medel | x. 509 enhets certifikatets tumavtryck matchade inte konfigurationen. | Granska aviseringar på enheterna. Ingen ytterligare åtgärd krävs. |
| x. 509-certifikatet har gått ut | Medel | X. 509-enhetens certifikat har upphört att gälla. | Detta kan vara en legitim enhet med ett utgånget certifikat eller ett försök att personifiera en legitim enhet. Om den licensierade enheten för närvarande kommunicerar korrekt är detta förmodligen ett försök till personifiering. |
| **Låg** allvarlighets grad |  |  |  |
| Försök att lägga till eller redigera en diagnostisk inställning för en IoT Hub identifieras | Låg | Försök att lägga till eller redigera diagnostikinställningar för en IoT Hub har identifierats. Med diagnostikinställningar kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. Om den här åtgärden inte har gjorts av en auktoriserad part kan det indikera skadlig aktivitet. | 1. kontrol lera att certifikatet har tagits bort av en auktoriserad part.<br> 2. om certifikatet inte har tagits bort av en auktoriserad part lägger du till certifikatet igen och eskalerar aviseringen till ditt informations säkerhets team. |
| Försök att ta bort en diagnostisk inställning från en IoT Hub upptäcktes | Låg | % {DescAttemptStatusMessage} \' försökte lägga till eller redigera diagnostikinställningar \' % {DescDiagnosticSettingName} \' i IoT Hub \' % {DescIoTHubName} \' . Med diagnostisk inställning kan du återskapa aktivitets spårningar för utrednings ändamål när en säkerhets incident inträffar eller nätverket komprometteras. Om den här åtgärden inte har gjorts av en auktoriserad part kan det tyda på en skadlig aktivitet. | Se till att behörigheter för att ändra diagnostikinställningar endast beviljas till en auktoriserad part. |
| Utgånget SAS-token | Låg | Inaktuell SAS-token som används av en enhet | Kan vara en legitim enhet med en utgången token, eller ett försök att personifiera en legitim enhet. Om den licensierade enheten för närvarande kommunicerar korrekt, är det troligt vis ett personifierings försök. |
| Ogiltig signatur för SAS-token | Låg | En SAS-token som används av en enhet har en ogiltig signatur. Signaturen matchar inte antingen den primära eller sekundära nyckeln. | Granska aviseringarna på enheterna. Ingen ytterligare åtgärd krävs. |

## <a name="next-steps"></a>Nästa steg

- [Översikt över](overview.md) Defender for IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
