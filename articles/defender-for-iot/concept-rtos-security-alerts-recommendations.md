---
title: Defender-IoT-Micro-agent för Azure återställnings tider inbyggd & anpassningsbara aviseringar och rekommendationer
description: Lär dig mer om säkerhets aviseringar och rekommenderade åtgärder med hjälp av Azure IoT Defender-IoT-Micro-agent-återställnings tider.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: cfbd411617a0b80f4857e08f9803b34b80b873d4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784687"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Defender-IoT-Micro-agent för Azure återställnings tider säkerhets aviseringar och rekommendationer (för hands version)

Defender-IoT-Micro-agent för Azure återställnings tider analyserar kontinuerligt din IoT-lösning med avancerad analys och hot information för att varna dig om potentiella skadliga aktiviteter och misstänkta system ändringar. Du kan också skapa anpassade aviseringar baserat på dina kunskaper om förväntat enhets beteende och bas linjer.

En Defender-IoT-Micro-agent för Azure återställnings tider-avisering fungerar som en indikator för potentiella kompromisser och bör undersökas och åtgärdas. En Defender-IoT-Micro-agent för Azure återställnings tider-rekommendation identifierar svaga säkerhets position som ska åtgärdas och uppdateras. 

I den här artikeln hittar du en lista över inbyggda aviseringar och rekommendationer som utlöses baserat på standard intervall och anpassningsbara med dina egna värden, baserat på förväntat eller bas linje beteende. 

Mer information om hur du anpassar aviseringen i Defender for IoT-tjänsten finns i [anpassningsbara aviseringar](concept-customizable-security-alerts.md). De särskilda aviseringar och rekommendationer som är tillgängliga för anpassning när du använder Defender-IoT-Micro-agent för Azure återställnings tider beskrivs i följande tabeller. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Defender-IoT-Micro-agent för Azure återställnings tider-stöd för säkerhets aviseringar

### <a name="device-related-security-alerts"></a>Enhets-relaterade säkerhets aviseringar

|Enhets relaterad säkerhets aviserings aktivitet  |Aviseringsnamn  |
|---------|---------|
|IP-adress| Kommunikation med en misstänkt IP-adress upptäcktes|
|Tumavtryck för X. 509 enhets certifikat|509 för enhets certifikatets tumavtryck matchar inte|
|X. 509-certifikat| X. 509-certifikatet har gått ut|
|SAS-token| Utgånget SAS-token|
|SAS-token| Ogiltig signatur för SAS-token|

### <a name="iot-hub-related-security-alerts"></a>IoT Hub-relaterade säkerhets aviseringar

|IoT Hub säkerhets aviserings aktivitet  |Aviseringsnamn  |
|---------|---------|
|Lägg till ett certifikat    |  Ett misslyckat försök att lägga till ett certifikat har upptäckts i en IoT Hub       |
|Tillägg eller redigering av en diagnostisk inställning    | Ett försök gjordes att lägga till eller redigera en diagnostisk inställning för en IoT Hub      |
|Ta bort ett certifikat    |  Ett misslyckat försök att ta bort ett certifikat från en IoT Hub påträffades       |
|Ta bort en diagnostisk inställning    |  Ett försök gjordes att ta bort en diagnostisk inställning från en IoT Hub      |
|Borttaget certifikat    | Borttagning av ett certifikat har upptäckts från en IoT Hub        |
|Nytt certifikat     |  Tillägg av nytt certifikat har identifierats för en IoT Hub       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Defender-IoT-Micro-agent för Azure återställnings tider-stöd för anpassningsbara aviseringar

### <a name="device-related-customizable-alerts"></a>Enhet relaterade anpassningsbara aviseringar

|Enhets relaterad aktivitet |Aviseringsnamn  |
|---------|---------|
|Aktiva anslutningar|Antalet aktiva anslutningar är inte inom det tillåtna intervallet|
|Moln till enhets meddelanden i **MQTT** -protokoll|Antal moln till enhets meddelanden i **MQTT** -protokollet är inte inom det tillåtna intervallet|
|Utgående anslutning| Utgående anslutning till en IP-adress som inte är tillåten|

### <a name="hub-related-customizable-alerts"></a>NAV relaterade anpassningsbara aviseringar 

|Hubb relaterad aktivitet  |Aviseringsnamn  |
|---------|---------|
|Rensningar av kommando kön     |  Antalet rensningar av kommando kön utanför det tillåtna intervallet       |
|Moln till enhets meddelanden i **MQTT** -protokoll    |  Antal moln till enhets meddelanden i **MQTT** -protokollet utanför det tillåtna intervallet       |
|Enhet till moln meddelanden i **MQTT** -protokoll    | Antal enheter till moln meddelanden i **MQTT** -protokollet utanför det tillåtna intervallet        |
|Direkta metod anrop     |  Antal direkta metod anrop utanför det tillåtna intervallet       |
|Avvisade moln till enhets meddelanden i **MQTT** -protokoll     |   Antal avvisade moln till enhets meddelanden i **MQTT** -protokollet utanför det tillåtna intervallet      |
|Uppdateringar till dubbla moduler     |  Antal uppdateringar till dubbla moduler utanför det tillåtna intervallet       |
|Obehöriga åtgärder    |  Antal otillåtna åtgärder utanför det tillåtna intervallet       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Defender-IoT-Micro-agent för Azure återställnings tider-support rekommendationer

### <a name="device-related-recommendations"></a>Enhets relaterade rekommendationer

|Enhets relaterad aktivitet  |Rekommendations namn |
|---------|---------|
|Autentiseringsuppgifter för autentisering    |  Identiska autentiseringsuppgifter för autentisering används av flera enheter       |

### <a name="hub-related-recommendations"></a>NAV-relaterade rekommendationer

|IoT Hub-relaterad aktivitet  |Rekommendations namn |
|---------|---------|
|Princip för IP-filter   |  Standard principen för IP-filter ska vara inställd på **neka**  |
|Regel för IP-filter| IP filter regeln innehåller ett stort IP-intervall|
|Diagnostikloggar|Förslag för att aktivera diagnostikloggar i IoT Hub|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>Alla Defender för IoT-aviseringar och rekommendationer

En fullständig lista över alla Defender-relaterade aviseringar och rekommendationer finns i IoT- [säkerhetsaviseringar](concept-security-alerts.md), IoT- [säkerhetsrekommendationer](concept-recommendations.md).

## <a name="next-steps"></a>Nästa steg

- [Snabb start: Defender-IoT-Micro-agent för Azure återställnings tider](quickstart-azure-rtos-security-module.md)
- [Konfigurera och anpassa Defender-IoT-Micro-agent för Azure återställnings tider](how-to-azure-rtos-security-module.md)
- Se [API för Defender-IoT-Micro-agent för Azure återställnings tider-API](azure-rtos-security-module-api.md)