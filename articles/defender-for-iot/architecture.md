---
title: Azure Defender för IoT-arkitektur
description: Lär dig mer om Azure Defender för IoT-arkitektur och informations flöde.
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
ms.date: 1/13/2021
ms.author: shhazam
ms.openlocfilehash: f3239434a7ba737bc9323bc4f383afd794800db1
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201491"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender för IoT-arkitektur

I den här artikeln beskrivs den funktionella system arkitekturen i Defender for IoT-lösningen. Azure Defender for IoT erbjuder två uppsättningar funktioner som passar din miljös behov, agent lös lösning för organisationer och en agent-baserad lösning för enhets byggare.

## <a name="agentless-solution-for-organizations"></a>Lösning utan agent för organisationer
### <a name="defender-for-iot-components"></a>Defender för IoT-komponenter

Defender för IoT ansluter både till Azure-molnet och lokala komponenter. Lösningen är utformad för skalbarhet i stora och geografiskt distribuerade miljöer med flera fjärranslutna platser. Den här lösningen möjliggör en distribuerad arkitektur med flera lager efter land, region, affär senhet eller zon. 

Azure Defender för IoT innehåller följande komponenter: 

**Moln anslutna distributioner**

- Azure Defender för IoT sensor VM eller-installation
- Azure Portal för moln hantering och integrering till Azure Sentinel
- Lokal hanterings konsol för lokal plats hantering
- En inbäddad säkerhets agent (valfritt)

**Air – gapped (offline) distributioner**

- Azure Defender för IoT sensor VM eller-installation
- Lokal hanterings konsol för lokal plats hantering

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="Arkitekturen för Defender för IoT.":::

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender för IoT-sensorer

Defender för IoT-sensorer identifierar och övervakar nätverks enheter kontinuerligt. Sensorer samlar in ICS-nätverkstrafik med passiv (agent utan agent) övervakning på IoT-och-enheter-enheter. 
 
Syfte – byggd för IoT och andra nätverk, men utan agent ger en djup insyn i IoT och synlig risk inom några minuter efter anslutning till nätverket. Prestanda påverkas av nätverks-och nätverks enheter på grund av den icke-invasiva NTA-metoden (Network Traffic Analysis). 
 
Med hjälp av patenterad, IoT och med funktions medveten analys och lager 7 djup paket inspektion (DPI) kan du analysera mer än traditionella signaturer för att omedelbart identifiera avancerade IoT-och känsligt hot (t. ex. filskadlig kod) baserat på avvikande eller otillåten aktivitet. 
  
Defender för IoT-sensorer ansluter till en intervall port eller ett nätverk, och börjar direkt utföra DPI på IoT-och nätverks trafik. 
 
Data insamling, bearbetning, analys och avisering äger rum direkt på sensorn. Detta gör det lämpligt för platser med låg bandbredd eller hög latens anslutning, eftersom endast metadata överförs till hanterings konsolen.

Sensorn inkluderar fem analys motorer. Motorer utlöser aviseringar baserat på analys av både real tids och förregistrerade trafik. Följande motorer är tillgängliga: 

#### <a name="protocol-violation-detection-engine"></a>Motor för identifiering av protokoll fel
Identifierings motorn för protokoll igenkänning identifierar användningen av paket strukturer och fält värden som strider mot ICS-protokollets specifikationer, till exempel: Modbus undantag och inledande av en inaktuell funktions kod aviseringar.

#### <a name="policy-violation-detection-engine"></a>Motor för identifiering av princip överträdelser
Med hjälp av Machine Learning, varnar identifierings motorn användare om eventuella avvikelser från bas linje beteendet, till exempel otillåten användning av vissa funktions koder, åtkomst till vissa objekt eller ändringar i enhets konfigurationen. Till exempel: DeltaV program version har ändrats och otillåtna PLC programmerings aviseringar. Mer specifikt modellererar princip överträdelse-motorn ICS-nätverken som deterministiska sekvenser av tillstånd och över gångar – med hjälp av en patenterad teknik som kallas för IFSM (industriellt begränsad tillstånds modellering). Identifierings motorn för princip avkänning upprättar en bas linje för ICS-nätverken, så att plattformen kräver en kortare inlärnings period för att bygga en bas linje för nätverket än allmänna matematiska metoder eller analyser, som ursprungligen utvecklades för IT i stället för nätverk.

#### <a name="industrial-malware-detection-engine"></a>Identifierings motor för industriella skadlig kod
Den industriella motorn för identifiering av skadlig kod identifierar beteenden som indikerar förekomst av känt skadlig kod, till exempel Conficker, svart energi, Havex, WannaCry, NotPetya och Triton. 

#### <a name="anomaly-detection-engine"></a>Avvikelse identifierings motor
Motorn för avvikelse identifiering identifierar onormal kommunikation mellan maskin-till-dator (M2M) och beteenden. Genom att modellera ICS-nätverk som deterministiska sekvenser av tillstånd och över gångar, kräver plattformen en kortare inlärnings period än allmänna matematiska metoder eller analyser som ursprungligen utvecklades för IT snarare än. Den identifierar även avvikelser snabbare, med minimala falska positiva identifieringar. Aviseringar för avvikelse identifierings motorn innehåller omfattande SMB-inloggnings försök och PLC-genomsökning upptäckte varningar.

#### <a name="operational-incident-detection"></a>Identifiering av drift incident
Identifieringen av drifts incidenter identifierar drift problem, till exempel tillfällig anslutning som kan indikera tidig tecken på utrustnings fel. Till exempel anses enheten vara frånkopplad (svarar inte) och Siemens S7 Stop PLC skickade aviseringar.

### <a name="management-consoles"></a>Hanterings konsoler
Hantering av Azure Defender för IoT i hybrid miljöer sker via två hanterings portaler: 
- Sensor konsol
- Den lokala hanterings konsolen
- Azure Portal

### <a name="sensor-console"></a>Sensor konsol
Sensor identifieringar visas i sensor konsolen där de kan visas, undersökas och analyseras i en nätverks karta, till gångs inventering och i många olika rapporter, till exempel riskhanterings rapporter, frågor om Data utvinning och angrepps vektorer. Du kan också använda-konsolen för att visa och hantera hot som upptäckts av sensor motorer, vidarebefordra information till partner system, hantera användare med mera.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Defender för IoT sensor konsol":::

### <a name="on-premises-management-console"></a>Lokal hanterings konsol
Den lokala hanterings konsolen gör det möjligt för SOC-operatörer att hantera och analysera aviseringar som sammanställs från flera sensorer till en enda instrument panel och ger en övergripande vy över hälsan hos de olika nätverken.

Den här arkitekturen ger en heltäckande vy över nätverket på en SOC nivå, optimerad aviserings hantering och kontroll av drift nätverks säkerhet, vilket säkerställer att besluts fattande och riskhantering är felfritt.

Förutom hantering av flera innehavare, övervakning, data analys och centraliserad sensor, innehåller hanterings konsolen ytterligare system underhålls verktyg (t. ex. varnings undantag) och helt anpassade rapportfunktioner för varje fjärran sluten utrustning. Den här skalbara arkitekturen stöder både lokal hantering på webbplats nivå, zon nivå och global hantering inom SOC.

Hanterings konsolen kan distribueras för konfiguration med hög tillgänglighet, vilket ger en säkerhets kopierings konsol som regelbundet tar emot säkerhets kopior av alla konfigurationsfiler som krävs för återställning. Om den primära konsolen Miss lyckas växlar de lokala plats hanterings utrustningarna automatiskt över till att synkronisera med säkerhets kopierings konsolen för att upprätthålla tillgänglighet utan avbrott.

Den är nära integrerad med dina SOC-arbetsflöden och kör böcker, vilket möjliggör enkel prioritering av minsknings aktiviteter och korrelationer mellan olika webbplatser av hot.

- Holistisk – minska komplexiteten med en enda enhetlig plattform för till gångs hantering, risk-och sårbarhets hantering, samt hot övervakning med incident svar.

- Agg regering och korrelation – Visa, aggregera och analysera data och aviseringar som samlats in från alla platser.

- Kontrol lera alla sensorer – konfigurera och övervaka alla sensorer från en enda plats.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Hantera alla aviseringar och all information.":::

### <a name="azure-portal"></a>Azure Portal

Defender för IoT-portalen i Azure används för att hjälpa dig att:

- Köp lösnings utrustning

- Installera och uppdatera program vara
- Onboard-sensorer till Azure
- Uppdatera hot informations paket

## <a name="agent-based-solution-for-device-builders"></a>Agentbaserad lösning för enhetsskapare

### <a name="embedded-security-agent-built-in-mode"></a>Inbäddad säkerhets agent: inbyggt läge

I det **inbyggda** läget är Defender för IoT aktiverat när du väljer att aktivera **säkerhets** alternativet i din IoT-hubb. Vi erbjuder övervakning i real tid, rekommendationer och aviseringar, det inbyggda läget erbjuder en synlighet för enskilda steg och en oöverträffad säkerhet. I det här läget krävs ingen agent installation på några enheter och avancerad analys används för loggade aktiviteter för att analysera och skydda fält enheten och IoT Hub.

### <a name="embedded-security-agent-enhanced-mode"></a>Inbäddad säkerhets agent: Avancerat läge

I **Avancerat** läge, efter att du har aktiverat **säkerhets** alternativet i din IoT-hubb och installerat Defender för IoT-enhets agenter på dina enheter, samlar agenterna samman, sammanställa och analyserar rå säkerhets händelser från dina enheter. Rå säkerhets händelser kan omfatta IP-anslutningar, process skapande, användar inloggningar och annan säkerhetsrelaterad information. Defender för IoT-enhets agenter hanterar också händelse agg regering för att undvika hög nätverks data flöde. Agenterna är mycket anpassningsbara, så att du kan använda dem för olika uppgifter, t. ex. genom att bara skicka viktig information till det snabbaste service avtalet, eller för att samla in omfattande säkerhets information och kontext i större segment, vilket kan undvika högre tjänste kostnader.

Enhets agenter och andra program använder Azure-funktionen för att **Skicka Security meddelande-SDK** för att skicka säkerhets information till Azure IoT Hub. IoT Hub hämtar den här informationen och vidarebefordrar den till tjänsten Defender för IoT.

När tjänsten Defender for IoT är aktive rad skickar IoT Hub förutom vidarebefordrade data även alla interna data för analys av Defender för IoT. Dessa data omfattar enhets moln åtgärds loggar, enhets identiteter och nav konfiguration. All den här informationen hjälper dig att skapa en pipeline för Defender för IoT Analytics.

Defender för IoT Analytics-pipeline tar också emot ytterligare hot informations strömmar från olika källor i Microsoft-och Microsoft-partner. Pipelinen Defender for IoT hela Analytics fungerar med varje kundkonfiguration som gjorts på tjänsten (till exempel anpassade aviseringar och användning av SDK: n för att skicka Security-meddelanden).

Med Analytics-pipeline kombinerar Defender för IoT alla data strömmar för att generera åtgärds bara rekommendationer och aviseringar. Pipelinen innehåller både anpassade regler som skapats av säkerhets forskare och experter, samt Machine Learning-modeller som söker efter avvikelser från standardenhets beteende och risk analys.

Defender för IoT-rekommendationer och-varningar (analys av pipeline-utdata) skrivs till Log Analytics arbets ytan för varje kund. Att inkludera rå händelser i arbets ytan samt aviseringar och rekommendationer möjliggör djupgående undersökning och frågor med hjälp av exakt information om de misstänkta aktiviteter som upptäckts.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="Mikroagentens arkitektur.":::

## <a name="see-also"></a>Se även

[Vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)

[Systemkrav](quickstart-system-prerequisites.md)
