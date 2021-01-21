---
title: Viktiga fördelar
description: Lär dig mer om Basic Defender for IoT-koncept.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 419e32a127947cde1d3761507f0df7a889528ff2
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621936"
---
# <a name="basic-concepts"></a>Grundläggande begrepp 

I den här artikeln beskrivs viktiga fördelar med Azure Defender för IoT.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Snabb icke-invasiv distribution och passiv övervakning

Defender för IoT-sensorer ansluter till en intervall port eller ett nätverk, och börjar omedelbart samla in ICS-nätverkstrafik via passiv (agent lös) övervakning. Djup paket inspektion (DPI) används för att dissect trafik från både seriell och Ethernet-kontroll av nätverks utrustning. Defender for IoT har ingen påverkan på nätverksbaserade nätverk eftersom det inte placeras i data Sök vägen och inte aktivt söker på enheter. 

För att tillhandahålla ögonblicks bilder av detaljerad enhets information, kompletterar Defender för IoT-sensorn passiv övervakning med en valfri aktiv komponent. Den här komponenten använder säkra, leverantörs godkända kommandon för att fråga både Windows-och kontrollant-enheter om enhets information, så ofta eller som du vill.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Inbäddad kunskap om ICS-protokoll, enheter och program

Enbart DPI är inte tillräckligt för att identifiera protokoll avvikelser och identifiera enheten på en detaljerad nivå. Defender for IoT-sensorn behandlar några av de största och mest komplexa miljöerna. Fler än 1 300-nätverk har analyser ATS hittills, i alla industri grenar.

## <a name="analytics-and-self-learning-engines"></a>Analys verktyg och självstudier

Motorer identifierar säkerhets problem via kontinuerlig övervakning och fem analys motorer som innehåller självstudier för att eliminera behovet av att uppdatera signaturer eller definiera regler. Motorerna använder ICS-baserad beteende analys och data vetenskap för att kontinuerligt analysera nätverks trafiken för avvikelser. De fem motorerna är:

- **Identifiering av protokoll fel**: identifierar användningen av paket strukturer och fält värden som strider mot ICS-protokollets specifikationer.

- **Identifiering av princip** överträdelser: identifierar princip överträdelser som obehörig användning av funktions koder, åtkomst till vissa objekt eller ändringar i enhets konfigurationen.

- **Identifiering av industriell skadlig kod**: identifierar beteenden som visar förekomsten av känt skadlig kod som Conficker, Black Energy, Havex, WannaCry och NotPetya.

- **Avvikelse identifiering**: identifierar ovanlig kommunikation mellan maskin-till-dator (M2M) och beteenden. Genom att utforma ICS-nätverk som deterministiska sekvenser av tillstånd och över gångar, använder motorn en patenterad teknik som kallas för IFSM (industriellt begränsad tillstånds modellering). Lösningen kräver en kortare inlärnings period än generiska matematiska metoder eller analyser, som ursprungligen utvecklades för IT i stället för. Den identifierar även avvikelser snabbare, med minimala falska positiva identifieringar.

- **Identifiering av drift incidenter**: identifierar drift problem, till exempel tillfällig anslutning som kan indikera tidig tecken på utrustnings fel.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Analys av nätverks trafik för risk-och sårbarhets bedömning

Som är unik i branschen använder Defender for IoT tillverkarspecifika NTA-algoritmer (Network Traffic Analysis) för att passivt identifiera alla nätverks-och slut punkts sårbarheter, till exempel:

- Obehöriga fjärråtkomstanslutningar
- Icke-registrerade enheter
- Svag autentisering
- Sårbara enheter (baserade på felkorrigerade CVEs)
- Otillåtna bryggor mellan undernät
- Svaga brand Väggs regler

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Data utvinning för undersökningar, data utredning och hot jakt

Plattformen ger ett intuitivt data utvinnings gränssnitt för detaljerad sökning efter historisk trafik i alla relevanta dimensioner. Exempel omfattar tids period, IP-adress, MAC-adress och portar. Du kan också göra protokoll-/regionsspecifika frågor baserade på funktions koder, protokoll tjänster och moduler. PCAPs med full kvalitet är tillgängliga för ytterligare analys av detalj nivå.

## <a name="sensor-cloud-management-mode"></a>Hanterings läge för sensor moln

Sensor moln hanterings läget avgör var enhet, avisering och annan information som sensorn identifierar visas.

För **moln anslutna sensorer** visas information som sensorn känner av i sensor konsolen. Aviserings information levereras via en IoT-hubb och kan delas med andra Azure-tjänster, till exempel Azure Sentinel.

För **lokalt anslutna sensorer** visas information som sensorn identifierar i sensor konsolen. Identifierings information delas också med den lokala hanterings konsolen om sensorn är ansluten till den.

## <a name="air-gapped-networks"></a>Gapped nätverk

Om du arbetar i en Air-gapped-miljö ger den lokala hanterings konsolen i Defender för IoT en real tids visning av viktiga IoT-och varnings indikatorer och aviseringar i alla dina lokaler. Den är nära integrerad med dina SOC-arbetsflöden och Runbooks, vilket möjliggör enkel prioritering av minsknings aktiviteter och korrelationer mellan olika webbplatser av hot.  

Defender för IoT är en konsol IDE rad vy över alla dina enheter. Den innehåller också viktig information om enheterna, till exempel typ (PLC, RTU, domänkontrollanter och mer), tillverkare, modell och revisions nivå för inbyggd program vara samt aviserings information.  

Defender för IoT möjliggör effektiv hantering av flera distributioner och en omfattande enhetlig vy av nätverket. Defender för IoT optimerar aviserings hantering och kontroll av drift nätverks säkerhet.

Den lokala hanterings konsolen är en webbaserad administrations plattform som gör att du kan övervaka och kontrol lera aktiviteter för globala sensor installationer. Förutom att hantera data som tas emot från distribuerade sensorer, integrerar den lokala hanterings konsolen data från en mängd olika företags resurser: CMDBs, DNS, brand väggar, webb-API: er med mera.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Den lokala hanterings konsolen visas.":::

Vi rekommenderar att du bekantar dig med de begrepp, funktioner och funktioner som finns tillgängliga för sensorer innan du arbetar med den lokala hanterings konsolen.

## <a name="integrations"></a>Integreringar

Du kan utöka funktionerna i Defender för IoT genom att dela både enhets-och aviserings information med partner system. Integreringar hjälper företag att överbrygga tidigare silode säkerhetslösningar för att avsevärt förbättra enhets synlighet och hot information. Integreringar hjälper företag att påskynda de systemomfattande svaren och minimera riskerna snabbare. 

Integreringarna minskar komplexiteten och eliminerar IT-och mellanliggande silor genom att integrera dem i dina befintliga SOC-arbetsflöden och säkerhets stackar. Till exempel:

- Siem, till exempel IBM QRadar, Splunk, ArcSight, LogRhythm och RSA netvittne

- Säkerhets dirigering och biljett system som ServiceNow och IBM elastisk

- Säkra lösningar för fjärråtkomst, till exempel CyberArk Privileged session Manager (PSM) och BeyondTrust

- Skydda NAC-system (Network Access Control) som Aruba ClearPass och Forescout motverkande

- Brand väggar som Fortinet och Check Point

## <a name="complete-protocol-support"></a>Fullständigt protokoll stöd

Förutom stöd för inbäddade protokoll kan du skydda IoT-och ICS-enheter som kör patentskyddade och anpassade protokoll, eller protokoll som avviker från valfri standard. Genom att använda ODE-SDK: n för Horisont kan utvecklare skapa insticks program som avkodar nätverks trafik baserat på definierade protokoll. Tjänster analyserar trafik för att tillhandahålla fullständig övervakning, avisering och rapportering. Använd horisont för att:

- Expandera synlighet och kontroll utan att behöva uppgradera till nya versioner.

- Skydda tillverkarspecifik information genom att utveckla på plats som ett externt plugin-program.

- Lokalisera text för aviseringar, händelser och protokoll parametrar.

Dessutom kan du använda företagsspecifika protokoll varningar för att kommunicera information:

- Om trafik identifieringar baserat på protokoll och underliggande protokoll i ett eget Horisont-plugin-program.

- Om en kombination av protokoll fält från alla protokoll lager. I en miljö som kör MODBUS kan du till exempel vilja generera en avisering när sensorn identifierar ett Skriv kommando till ett minnes register för en speciell IP-adress och ett Ethernet-mål. Eller så kanske du vill generera en avisering när någon åtkomst utförs till en speciell IP-adress.

Aviseringar utlöses när villkor för varnings regler för Horisont är uppfyllda.

Dessutom kan du med anpassade aviseringar för Horisont skriva egna aviserings titlar och meddelanden. Matchade protokoll fält och värden kan bäddas in i aviserings meddelande texten.

Genom att använda anpassade, villkorliga aviserings utlösare och meddelanden kan du hitta en speciell nätverks aktivitet och effektivt uppdatera din säkerhet, IT och drift team.


## <a name="high-availability"></a>Hög tillgänglighet

Öka återhämtningen av din Defender för IoT-distribution genom att installera en hög tillgänglighets enhet i den lokala hanterings konsolen. Distributioner med hög tillgänglighet säkerställer att dina hanterade sensorer kontinuerligt rapporterar till en aktiv lokal hanterings konsol.

Den här distributionen implementeras med ett lokalt hanterings konsol par som innehåller en primär och sekundär apparat.

## <a name="localization"></a>Lokalisering

Många konsol funktioner har stöd för en mängd olika språk.

## <a name="next-step"></a>Nästa steg

[Komma igång med Defender för IoT](getting-started.md)
