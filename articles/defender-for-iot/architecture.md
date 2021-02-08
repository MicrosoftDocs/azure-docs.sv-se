---
title: Arkitektur för agent lösa lösningar
description: Lär dig mer om Azure Defender för IoT agent utan arkitektur och informations flöde.
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
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 1eb939a63edc4abc638dc128af0d9ebbd7241bef
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809311"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender för IoT-arkitektur

I den här artikeln beskrivs den funktionella system arkitekturen i lösningen Defender for IoT utan lösning. Azure Defender for IoT erbjuder två uppsättningar funktioner som passar din miljös behov, agent lös lösning för organisationer och en agent-baserad lösning för enhets byggare.

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

Defender för IoT-sensorer identifierar och övervakar kontinuerligt nätverks enheter. Sensorer samlar in ICS-nätverkstrafik med passiv (agent utan agent) övervakning på IoT-och-enheter-enheter. 
 
Syfte – byggd för IoT och andra nätverk, men utan agent ger en djup insyn i IoT och synlig risk inom några minuter efter anslutning till nätverket. Prestanda påverkas av nätverks-och nätverks enheter på grund av den icke-invasiva NTA-metoden (Network Traffic Analysis). 
 
Genom att använda patenterad, IoT och med funktions medveten analys och nivå 7 djup paket inspektion (DPI) kan du analysera mer än traditionella signaturer för att omedelbart identifiera avancerade IoT-och känsligt hot (t. ex. filskadlig kod) baserat på avvikande eller otillåten aktivitet. 
  
Defender för IoT-sensorer ansluter till en intervall port eller ett nätverk, och börjar direkt utföra DPI på IoT-och nätverks trafik. 
 
Data insamling, bearbetning, analys och avisering äger rum direkt på sensorn. Den här processen gör det lämpligt för platser med låg bandbredd eller hög latens anslutning, eftersom endast metadata överförs till hanterings konsolen.

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
Sensor identifieringar visas i sensor konsolen där de kan visas, undersökas och analyseras i en nätverks karta, enhets inventering och i många olika rapporter, till exempel riskhanterings rapporter, frågor om Data utvinning och angrepps vektorer. Du kan också använda-konsolen för att visa och hantera hot som upptäckts av sensor motorer, vidarebefordra information till partner system, hantera användare med mera.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Defender för IoT sensor konsol":::

### <a name="on-premises-management-console"></a>Lokal hanterings konsol
Den lokala hanterings konsolen gör det möjligt för SOC-operatörer att hantera och analysera aviseringar som sammanställs från flera sensorer till en enda instrument panel och ger en övergripande vy över hälsan hos de olika nätverken.

Den här arkitekturen ger en heltäckande vy över nätverket på en SOC nivå, optimerad aviserings hantering och kontroll av drift nätverks säkerhet, vilket säkerställer att besluts fattande och riskhantering är felfritt.

Förutom hantering av flera innehavare, övervakning, data analys och centraliserad sensor, innehåller hanterings konsolen ytterligare verktyg för system underhåll (till exempel varnings undantag) och helt anpassade rapporterings funktioner för varje fjärran sluten utrustning. Den här arkitekturen stöder både lokal hantering på webbplats nivå, zon nivå och global hantering inom SOC.

Hanterings konsolen kan distribueras för konfiguration med hög tillgänglighet, vilket ger en säkerhets kopierings konsol som regelbundet tar emot säkerhets kopior av alla konfigurationsfiler som krävs för återställning. Om den primära konsolen Miss lyckas växlar de lokala plats hanterings utrustningarna automatiskt över till att synkronisera med säkerhets kopierings konsolen för att upprätthålla tillgänglighet utan avbrott.

Den är nära integrerad med dina SOC-arbetsflöden och kör böcker, vilket möjliggör enkel prioritering av minsknings aktiviteter och korrelationer mellan olika webbplatser av hot.

- Holistisk – minska komplexiteten med en enda enhetlig plattform för enhets hantering, risk-och sårbarhets hantering och hot övervakning med incident svar.

- Agg regering och korrelation – Visa, aggregera och analysera data och aviseringar som samlats in från alla platser.

- Kontrol lera alla sensorer – konfigurera och övervaka alla sensorer från en enda plats.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Hantera alla aviseringar och all information.":::

### <a name="azure-portal"></a>Azure Portal

Defender för IoT-portalen i Azure används för att hjälpa dig att:

- Köp lösnings utrustning

- Installera och uppdatera program vara

- Onboard-sensorer till Azure

- Uppdatera hot informations paket

## <a name="see-also"></a>Se även

[Vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)

[Systemkrav](quickstart-system-prerequisites.md)
