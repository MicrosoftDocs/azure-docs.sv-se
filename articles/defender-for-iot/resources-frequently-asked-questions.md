---
title: Defender för IoT vanliga frågor och svar
description: Hitta svar på vanliga frågor om Azure Defender för IoT-funktioner och-tjänster.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 678b50ffb2df2cd16312548109b93f6f1a7f08ff
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247473"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Defender för IoT

Den här artikeln innehåller en lista över vanliga frågor och svar om Defender för IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Vad är det unika värdet i Azure för IoT-säkerhet?

Defender för IoT gör det möjligt för företag att utöka sin befintliga cyberhot-säkerhetsvy till hela IoT-lösningen. Azure ger en heltäckande vy över din affärs lösning, så att du kan vidta affärsrelaterade åtgärder och beslut baserat på företagets säkerhets position och insamlade data. Med kombinerad säkerhet med Azure IoT, Azure IoT Edge och Azure Security Center kan du skapa den lösning som du vill ha med den säkerhet du behöver.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>Vår organisation använder företagsägda industriella protokoll som inte är standard. Stöds de? 

Azure Defender för IoT tillhandahåller omfattande protokoll stöd. Förutom stöd för inbäddade protokoll kan du skydda IoT-och enhets enheter som kör patentskyddade och anpassade protokoll, eller protokoll som avviker från valfri standard. Med hjälp av ODE-SDK: n (Open Development Environment) kan utvecklare skapa insektoriella plugin-program som avkodar nätverks trafik baserat på definierade protokoll. Trafiken analyseras av tjänster för att tillhandahålla fullständig övervakning, avisering och rapportering. Använd horisont för att:
- Expandera synlighet och kontroll utan att behöva uppgradera till nya versioner.
- Skydda tillverkarspecifik information genom att utveckla på plats som ett externt plugin-program. 
- Lokalisera text för aviseringar, händelser och protokoll parametrar.

Den här unika lösningen för att utveckla protokoll som plugin-program kräver inte särskilda utvecklar team eller versioner för att kunna stödja ett nytt protokoll. Utvecklare, partner och kunder kan på ett säkert sätt utveckla protokoll och dela insikter och kunskap med hjälp av Horisont. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Måste jag köpa maskin varu enheter från Microsoft-partner?
Azure Defender for IoT-sensorn körs på specifika specifikationer för maskin vara som beskrivs i [hand boken för maskin varu specifikationer](./how-to-identify-required-appliances.md), kunder kan köpa certifierad maskin vara från Microsoft-partner eller använda den tillhandahållna räkningen av material (BOM) och köpa den på egen hand. 

Certifierad maskin vara har testats i våra labb för driv rutins stabilitet, paket som sjunker och nätverks storlek.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>Med förordningen kan vi inte ansluta vårt system till Internet. Kan vi fortfarande använda Defender för IoT?

Ja, du kan! Azure Defender för IoT Platforms lokala lösning distribueras som en fysisk eller virtuell sensor utrustning som passivt matar in nätverks trafik (via SPAN, RSPAN eller KNACKNING) för att analysera, upptäcka och kontinuerligt övervaka IT-, nätverks-och IoT-nätverk. För större företag kan flera sensorer aggregera sina data till en lokal hanterings konsol.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>Var i nätverket ska jag ansluta övervaknings portarna?

Azure Defender för IoT-sensorn ansluter till en intervall port eller ett nätverk, och börjar omedelbart samla in ICS-nätverks trafik via passiv (utan agent) övervakning. Den har ingen påverkan på nätverks vägs nätverk eftersom den inte placerats i data Sök vägen och söker inte aktivt på enhets nivå.

Här är några exempel:
- En enda apparat (virtuell fysisk) kan finnas i DMZ-lagret, där all fabriks våningsplan trafik dirigeras till detta skikt.
- Du kan också hitta små mini-sensorer i varje arbets plan för arbets ytan med moln-eller lokal hantering som kommer att finnas i arbets ytan DMZ lager. En annan installation (virtuell eller fysisk) kan övervaka trafiken i affär DMZ-lagret (för SCADA, historian eller marknads avdelningen).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Hur jämförs Defender för IoT med tävlingen?

Även om andra lösningar tillhandahåller en uppsättning funktioner som gör att kunderna kan skapa sina egna lösningar, ger Defender för IoT en unik lösning från slut punkt till slut punkt som ger en bred vy över säkerheten för alla dina relaterade Azure-resurser. Azure möjliggör snabb distribution och fullständig integrering med IoT Hub modul för enkel integrering med befintliga enhets hanterings verktyg.


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Måste jag vara en Azure IoT-kund?

Ja. För moln anslutna distributioner är Azure Defender för IoT beroende av Azure IoT-anslutning och-infrastruktur.
## <a name="can-i-create-my-own-alerts"></a>Kan jag skapa mina egna aviseringar?

Ja. Du kan ange en anpassad avisering på en fördefinierad uppsättning beteenden, till exempel IP-adress och öppna portar. Se [skapa anpassade aviseringar](quickstart-create-custom-alerts.md) för att lära dig mer om anpassade aviseringar och hur du gör dem.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Var kan jag se loggar? Kan jag anpassa loggar?

- Visa aviseringar och rekommendationer med hjälp av den anslutna Log Analytics-arbetsytan. Konfigurera lagrings storlek och varaktighet i arbets ytan.

- Rå data från din säkerhets agent kan också lagras i ditt Log Analytics-konto. Överväg storlek, varaktighet, lagrings krav och tillhör ande kostnader innan du ändrar konfigurationen för det här alternativet.



## <a name="what-happens-when-the-internet-connection-stops-working"></a>Vad händer när Internet anslutningen slutar fungera?

Sensorer och agenter fortsätter att köra och lagra data så länge enheten körs. Data lagras i cacheminnet för säkerhets meddelanden enligt storleks konfigurationen. När enheten återfår anslutningen återupptas säkerhets meddelanden som skickas.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång med Defender för IoT finns i följande artiklar:

- Läs [översikten över](overview.md) Defender för IoT
- Kontrol lera [system kraven](quickstart-system-prerequisites.md)
- Lär dig mer om att [komma igång med Defender för IoT](getting-started.md)
- Förstå [Defender för IoT-säkerhetsaviseringar](concept-security-alerts.md)