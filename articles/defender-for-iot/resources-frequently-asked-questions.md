---
title: Defender för IoT vanliga frågor och svar
description: Hitta svar på vanliga frågor om Azure Defender för IoT-funktioner och-tjänster.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0ce8ded3eea344d72679e0f8b805f45b00279b58
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778601"
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

Exempel:
- En enda apparat (virtuell fysisk) kan finnas i DMZ-lagret, där all fabriks våningsplan trafik dirigeras till detta skikt.
- Du kan också hitta små mini-sensorer i varje arbets plan för arbets ytan med moln-eller lokal hantering som kommer att finnas i arbets ytan DMZ lager. En annan installation (virtuell eller fysisk) kan övervaka trafiken i affär DMZ-lagret (för SCADA, historian eller marknads avdelningen).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Hur jämförs Defender för IoT med tävlingen?

Azure Defender för IoT ger omfattande säkerhet på alla dina IoT/enhet-enheter. För **slut användar organisationer** erbjuder Azure Defender för IoT utan agent, säkerhet på nätverks nivå som snabbt distribueras, fungerar med olika patentskyddade komponenter och äldre Windows-system och samarbetar med Azure Sentinel och andra SoC-verktyg. Den kan distribueras lokalt eller i Azure-anslutna miljöer. För **IoT-enhets byggare** erbjuder Azure Defender för IoT lätta agenter att bädda in säkerhet på enhets nivå i nya IoT/DataStore-initiativ.

## <a name="do-i-have-to-be-an-azure-customer"></a>Måste jag vara en Azure-kund?

Nej, för den agentbaserade versionen av Azure Defender för IoT, behöver du inte vara en Azure-kund. Men om du vill skicka aviseringar till Azure Sentinel; etablera nätverks sensorer och övervaka deras hälsa från molnet. och dra nytta av automatiska uppdateringar av program vara och hot information, måste du ansluta sensorn till Azure via Azure IoT Hub.

För den agentbaserade versionen av Azure Defender för IoT måste du vara en Azure-kund.

## <a name="can-i-create-my-own-alerts"></a>Kan jag skapa mina egna aviseringar?

Ja, du kan skapa anpassade aviseringar baserat på flera parametrar, inklusive IP/MAC-adress, protokoll typ, klass, tjänst, funktion, kommando osv. och värden för anpassade taggar i nytto lasterna.  Se [skapa anpassade aviseringar](quickstart-create-custom-alerts.md) för att lära dig mer om anpassade aviseringar och hur du skapar dem.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Vad händer när Internet anslutningen slutar fungera?

Sensorer och agenter fortsätter att köra och lagra data så länge enheten körs. Data lagras i cacheminnet för säkerhets meddelanden enligt storleks konfigurationen. När enheten återfår anslutningen återupptas säkerhets meddelanden som skickas.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång med Defender för IoT finns i följande artiklar:

- Läs [översikten över](overview.md) Defender för IoT
- Kontrol lera [system kraven](quickstart-system-prerequisites.md)
- Lär dig mer om att [komma igång med Defender för IoT](getting-started.md)
- Förstå [Defender för IoT-säkerhetsaviseringar](concept-security-alerts.md)