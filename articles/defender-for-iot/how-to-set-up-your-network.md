---
title: Konfigurera ditt nätverk
description: Lär dig om lösnings arkitektur, nätverks förberedelser, krav och annan information som behövs för att se till att du har konfigurerat nätverket så att det fungerar med Azure Defender för IoT-enheter.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/03/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a71ea75eb603b141c4b28cff5f2b4aa957583bcd
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621320"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>Om installation av Azure Defender för IoT-nätverk

Azure Defender för IoT ger kontinuerlig övervakning av ICS-hot och enhets identifiering. Plattformen innehåller följande komponenter:

**Defender för IoT-sensorer:** Sensorer samlar in ICS-nätverkstrafik genom att använda passiv (utan agent) övervakning. Som passiv och inkräktande, har sensorerna ingen prestanda påverkan på lokala och IoT-nätverk och-enheter. Sensorn ansluter till en intervall port eller nätverket trycker och börjar direkt övervaka nätverket. Identifieringar visas i sensor konsolen. Där kan du Visa, undersöka och analysera dem i en nätverks karta, en enhets inventering och en omfattande uppsättning rapporter. Exempel är rapporter om riskbedömning, data utvinnings frågor och angrepps vektorer. 

**Defender för lokal IoT-hanterings konsol**: den lokala hanterings konsolen innehåller en sammanslagen vy över alla nätverks enheter. Den ger en real tids visning av viktiga indikatorer och IoT-riskfaktorer och aviseringar i alla dina anläggningar. Den är nära integrerad med dina SOC-arbetsflöden och spel böcker, vilket möjliggör enkel prioritering av minsknings aktiviteter och korrelationer mellan olika webbplatser av hot. 

**Defender för IoT för IoT-portalen:** Med hjälp av Defender för IoT-programmet kan du köpa lösnings utrustning, installera och uppdatera program vara och uppdatera VÄRDEJÄMFÖRING-paket. 

Den här artikeln innehåller information om lösnings arkitektur, förberedelser för nätverk, krav och mer som hjälper dig att konfigurera nätverket så att det fungerar med Defender för IoT-enheter. Läsarna som arbetar med informationen i den här artikeln bör ha erfarenhet av drift och hantering av nätverks-och IoT-nätverk. Exempel är Automation-tekniker, växt hanterare, nätverks infrastruktur tjänster, cybersäkerhet team, ciso eller CIO: er.

Kontakta [Microsoft Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)om du behöver hjälp eller support.

## <a name="on-site-deployment-tasks"></a>Distributions uppgifter på plats

Plats distributions uppgifter är:

- [Samla in plats information](#collect-site-information)

- [Förbered en konfigurations arbets Station](#prepare-a-configuration-workstation)

- [Planera rack installation](#planning-rack-installation)

### <a name="collect-site-information"></a>Samla in plats information

Registrera plats information som:

- Nätverks information för sensor hantering.

- Nätverks arkitektur för plats.

- Fysisk miljö.

- System integreringar.

- Planerade användarautentiseringsuppgifter.

- Konfigurations arbets Station.

- SSL-certifikat (valfritt men rekommenderas).

- SMTP-autentisering (valfritt). Förbered de autentiseringsuppgifter som krävs för servern om du vill använda SMTP-servern med autentisering.

- DNS-servrar (valfritt). Förbered DNS-serverns IP-och värd namn.

En detaljerad lista och beskrivning av viktig plats information finns i [exempel på plats bok](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Lyckade övervaknings rikt linjer

Vi rekommenderar att du följer den här proceduren för att hitta den optimala platsen för att ansluta enheten i varje produktions nätverk:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Diagram exempel på installation av produktions nätverk.":::

### <a name="prepare-a-configuration-workstation"></a>Förbered en konfigurations arbets Station

Förbered en Windows-arbetsstation, inklusive följande:

- Anslutning till sensor hanterings gränssnittet.

- En webbläsare som stöds

- Terminalprogram vara, till exempel SparaTillFil.

Kontrol lera att de nödvändiga brand Väggs reglerna är öppna på arbets stationen. Se [krav för nätverks åtkomst](#network-access-requirements) för mer information.

#### <a name="supported-browsers"></a>Webbläsare som stöds

Följande webbläsare stöds för sensorer och lokala hanterings konsol webb program:

- Chrome 32 +

- Microsoft Edge 86 +

- Internet Explorer 10 +

### <a name="network-access-requirements"></a>Krav för nätverks åtkomst

Kontrol lera att din organisations säkerhets princip tillåter åtkomst till följande:

| Protokoll | Transport | In/ut | Port | Använt | Syfte | Källa | Mål |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | IN/UT | 443 | Webb konsolen för sensor och lokal hanterings konsol | Åtkomst till webb konsolen | Klient | Sensor och lokal hanterings konsol |
| SSH | TCP | IN/UT | 22 | CLI | Åtkomst till CLI | Klient | Sensor och lokal hanterings konsol |
| SSL | TCP | IN/UT | 443 | Sensor och lokal hanterings konsol | Anslutning mellan CyberX-plattformen och den centrala hanterings plattformen | sensor | Lokal hanterings konsol |
| NTP | UDP | IN | 123 | Tidssynkronisering | Lokal hanterings konsol använder som NTP för sensor | sensor | lokal hanterings konsol |
| NTP | UDP | IN/UT | 123 | Tidssynkronisering | Sensor ansluten till extern NTP-server när det inte finns någon lokal hanterings konsol installerad | sensor | NTP |
| SMTP | TCP | GENOMFÖR | 25 | E-post | Anslutningen mellan CyberX-plattformen och hanterings plattformen och e-postservern | Sensor och lokal hanterings konsol | E-postserver |
| Syslog | UDP | GENOMFÖR | 514 | LEEF | Loggar som skickar från den lokala hanterings konsolen till syslog-servern | Lokal hanterings konsol och sensor | Syslog-server |
| DNS |  | IN/UT | 53 | DNS | DNS-serverport | Lokal hanterings konsol och sensor | DNS-server |
| LDAP | TCP | IN/UT | 389 | Active Directory | Anslutningen mellan CyberX-plattformen och hanterings plattformen till Active Directory | Lokal hanterings konsol och sensor | LDAP-server |
| LDAPS | TCP | IN/UT | 636 | Active Directory | Anslutningen mellan CyberX-plattformen och hanterings plattformen till Active Directory | Lokal hanterings konsol och sensor | LDAP-server |
| SNMP | UDP | GENOMFÖR | 161 | Övervakning | Fjärr-SNMP-insamlare. | Lokal hanterings konsol och sensor | SNMP-Server |
| WMI | UDP | GENOMFÖR | 135 | övervakning | Övervakning av Windows-slutpunkt | Mäta | Relevant nätverks element |
| Tunneltrafik | TCP | IN | 9000 <br /><br />– ovanpå port 443 <br /><br />Från slutanvändaren till den lokala hanterings konsolen. <br /><br />– Port 22 från sensor till den lokala hanterings konsolen  | övervakning | Tunneltrafik | Mäta | Lokal hanterings konsol |

### <a name="planning-rack-installation"></a>Planera rack installation

Planera din rack installation:

1. Förbered en övervakare och ett tangent bord för dina nätverks inställningar för enheten.

1. Allokera rack utrymme för enheten.

1. Ha växel ström tillgänglig för installationen av enheten.
1. Förbered LAN-kabeln för anslutning av hanteringen till nätverks växeln.
1. Förbered LAN-kablar för att ansluta portar för växel intervall (spegling) och nätverks knackningar till Defender för IoT-enheten. 
1. Konfigurera, Anslut och verifiera intervall portar i de speglade växlarna enligt beskrivningen i arkitektur gransknings sessionen.
1. Anslut den konfigurerade intervall porten till en dator som kör wireshark och kontrol lera att porten är korrekt konfigurerad.
1. Öppna alla relevanta brand Väggs portar.

## <a name="about-passive-network-monitoring"></a>Om passiv nätverks övervakning

Enheten tar emot trafik från flera källor, antingen genom att växla speglings portar (intervall portar) eller genom att trycka på nätverket. Hanterings porten är ansluten till företags-, företags-eller sensor hanterings nätverket med anslutning till en lokal hanterings konsol eller Defender för IoT-portalen.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Diagram över en hanterad växel med port spegling.":::

### <a name="purdue-model"></a>Purdue-modell

I följande avsnitt beskrivs Purdue-nivåer.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Diagram över Purdue-modellen.":::

####  <a name="level-0-cell-and-area"></a>Nivå 0: cell och yta  

Nivå 0 består av en mängd olika sensorer, motstånd och enheter som ingår i den grundläggande tillverknings processen. Dessa enheter utför de grundläggande funktionerna i industriella automatiserings-och kontroll system, till exempel:

- Köra en motor.

- Mäter variabler.
- Anger utdata.
- Utföra viktiga funktioner, till exempel målning, svetsning och böjning.

#### <a name="level-1-process-control"></a>Nivå 1: process kontroll

Nivå 1 består av inbäddade styrenheter som styr och manipulerar tillverknings processen vars nyckel funktion är att kommunicera med nivå 0-enheter. I diskret tillverkning är dessa enheter programmerbara logiska styrenheter (PLCs) eller RTUs (Remote telemetri Unit). Vid process tillverkning kallas Basic Controller ett distribuerat kontroll system (DCS).

#### <a name="level-2-supervisory"></a>Nivå 2: övervakare

Nivå 2 representerar de system och funktioner som är kopplade till övervaknings miljön och driften av ett produktionsområde i en produktions anläggning. Dessa omfattar vanligt vis följande: 

- Operatörs gränssnitt eller HMIs

- Larm eller varnings system

- Bearbeta historian och batch Management Systems

- Kontroll av rums arbets stationer

Dessa system kommunicerar med PLCs och RTUs på nivå 1. I vissa fall kommunicerar eller delar data med plats-eller företags-(nivå 4-och nivå 5) system och program. Dessa system är främst baserade på standard utrustning för datorer och operativ system (UNIX eller Microsoft Windows).

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Nivå 3 och 3,5: nätverk på webbplats nivå och industriellt perimeternätverk

Webbplats nivån representerar den högsta nivån av industriella automatiserings-och kontroll system. De system och program som finns på den här nivån hanterar webbplatsomfattande funktioner för industriell automatisering och styrning. Nivåerna 0 till 3 anses vara kritiska för plats åtgärder. De system och funktioner som finns på den här nivån kan omfatta följande:

- Produktions rapportering (till exempel cykel tider, kvalitets index, förutsägande underhåll)

- Växt historian

- Detaljerad produktions schemaläggning

- Åtgärds hantering på plats nivå

- Enhets-och material hantering

- Server för uppdaterings lansering

- Filserver

- Industriell domän, Active Directory, Terminal Server

Dessa system kommunicerar med produktions zonen och delar data med Enterprise-system och-program (nivå 4 och nivå 5).  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Nivå 4 och 5: företags-och företags nätverk

Nivå 4 och nivå 5 representerar platsen eller företags nätverket där de centraliserade IT-systemen och funktionerna finns. IT-organisationen hanterar direkt tjänster, system och program på dessa nivåer.

## <a name="planning-for-network-monitoring"></a>Planera för nätverks övervakning

I följande exempel finns olika typer av topologier för industriella kontroll nätverk, tillsammans med överväganden för optimal övervakning och placering av sensorer.

### <a name="what-should-be-monitored"></a>Vad ska övervakas?

Trafik som går igenom skikt 1 och 2 ska övervakas.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>Vad ska Defender för IoT-enheten ansluta till?

Defender för IoT-enheten bör ansluta till de hanterade växlar som ser den industriella kommunikationen mellan skikt 1 och 2 (i vissa fall även skikt 3).

Följande diagram är en allmän abstraktion av ett Multilayer, ett nätverk med flera innehavare, med ett mycket cybersäkerhet-eko system som vanligt vis drivs av en SOC och MSSP.

Normalt distribueras NTA-sensorer i skikten 0 till 3 i OSI-modellen.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Diagram över OSI-modellen.":::

#### <a name="example-ring-topology"></a>Exempel: Ring topologi

Ring signalen är en topologi där varje växel eller nod ansluter till exakt två andra växlar, vilket utgör en enda kontinuerlig väg för trafiken.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Diagram över ring signal sto pol Ogin.":::

#### <a name="example-linear-bus-and-star-topology"></a>Exempel: linjär buss och Star-topologi

I ett stjärn nätverk är alla värdar anslutna till en central hubb. I sin enklaste form fungerar ett centralt nav som en överförings kanal för att skicka meddelanden. I följande exempel övervakas inte lägre växlar och trafik som förblir lokal för dessa växlar visas inte. Enheter kan identifieras baserat på ARP-meddelanden, men anslutnings informationen kommer att saknas.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Diagram över den linjära bussen och Star-topologin.":::

#### <a name="multisensor-deployment"></a>Distribution av Multisensor

Här följer några rekommendationer för att distribuera flera sensorer:

| **Nummer** | **Mäta** | **Beroende** | **Antal sensorer** |
|--|--|--|--|
| Det maximala avståndet mellan växlar | 80 meter | För beredd Ethernet-kabel | Mer än 1 |
| Antal nätverks nätverk | Mer än 1 | Ingen fysisk anslutning | Mer än 1 |
| Antal växlar | Kan använda RSPAN-konfiguration | Upp till åtta växlar med lokalt intervall nära sensorn efter kabel avstånd | Mer än 1 |

#### <a name="traffic-mirroring"></a>Trafik spegling  

Om du bara vill se relevant information för trafik analys måste du ansluta Defender för IoT-plattformen till en speglings port på en växel eller en anslutning som bara omfattar industriella ICS-och SCADA-trafik. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Använd den här växeln för din installation.":::

Du kan övervaka växel trafik genom att använda följande metoder:

- [Växla SPAN-port](#switch-span-port)

- [Fjärrintervall (RSPAN)](#remote-span-rspan)

- [Aktiv och passiv agg regerings KNACKNING](#active-and-passive-aggregation-tap)

SPAN-och RSPAN är Cisco-terminologi. Andra märken av växlar har liknande funktioner, men kan använda olika termer.

#### <a name="switch-span-port"></a>Växla SPAN-port

En växel port analys speglar lokal trafik från gränssnitt på växel till gränssnitt på samma växel. Här följer några saker att tänka på:

- Kontrol lera att den relevanta växeln stöder port speglings funktionen.  

- Speglings alternativet är inaktiverat som standard.

- Vi rekommenderar att du konfigurerar alla växelns portar, även om inga data är anslutna till dem. Annars kan en falsk enhet anslutas till en ej övervakad port och den kan inte aviseras om sensorn.

- Vid nätverks växlings nätverk som använder broadcast-eller multicast-meddelanden konfigurerar du växeln så att den endast speglar överföring av RX (Receive). Annars kommer multicast-meddelanden att upprepas för så många aktiva portar och bandbredden multipliceras.

Följande konfigurations exempel är endast för referens och baseras på en Cisco 2960-växel (24 portar) som kör IOS. De är typiska exempel, så Använd dem inte som instruktioner. Speglade portar på andra Cisco-operativsystem och andra typer av växlar konfigureras på olika sätt.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Diagram över en port för att konfigurera en SPAN-port.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="Diagram över konfigurations läget för SPAN-porten.":::

##### <a name="monitoring-multiple-vlans"></a>Övervaka flera VLAN

Med Defender för IoT kan du övervaka virtuella lokala nätverk som kon figurer ATS i nätverket. Det krävs ingen konfiguration av Defender för IoT-systemet. Användaren måste se till att växeln i nätverket är konfigurerad att skicka VLAN-taggar till Defender för IoT.

I följande exempel visas de nödvändiga kommandon som måste konfigureras på Cisco-växeln för att övervaka VLAN i Defender för IoT:

**Övervaka session**: det här kommandot ansvarar för att skicka VLAN till intervall porten.

- övervaka session 1 source Interface Gi1/2

- övervaka session 1 filter paket typ godkänd RX

- övervaka session 1 mål gränssnitt fastEthernet1/1 Encapsulation dot1q

**Övervaka segment porten F.E. Gi1/1**: VLAN har kon figurer ATS på segment porten.

- gränssnitt GigabitEthernet1/1

- switchport trunk-inkapsling dot1q

- trunk för switchport-läge

#### <a name="remote-span-rspan"></a>Fjärrintervall (RSPAN)

Fjärrspan-sessionen speglar trafik från flera distribuerade käll portar till ett dedikerat fjärrnätverk.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Diagram över fjärrintervall.":::

Data i VLAN levereras sedan via segment portar över flera växlar till en speciell växel som innehåller den fysiska mål porten. Den här porten ansluter till Defender för IoT-plattformen.  

##### <a name="more-about-rspan"></a>Mer om RSPAN

- RSPAN är en avancerad funktion som kräver att ett särskilt VLAN-nätverk bär trafiken som sträcker sig mellan växlar. RSPAN stöds inte för alla växlar. Kontrol lera att växeln stöder funktionen RSPAN.

- Speglings alternativet är inaktiverat som standard.

- Fjärrnätverket måste vara tillåtet på den trunkerade porten mellan källans och målets växlar.

- Alla växlar som ansluter samma RSPAN-session måste vara från samma leverantör.

> [!NOTE]
> Kontrol lera att segment porten som delar fjärrnätverket för fjärrnätverket mellan växlarna inte har definierats som en speglad sessions-källport.
>
> Fjärrnätverket för fjärrnätverk ökar bandbredden på den trunkerade porten med storleken på den speglade sessionens bandbredd. Kontrol lera att växelns segment port stöder det.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Diagram över fjärrnätverk.":::

#### <a name="rspan-configuration-examples"></a>RSPAN konfigurations exempel

RSPAN: baserat på Cisco Catalyst 2960 (24 portar).

**Exempel på konfiguration av käll växel:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Skärm bild av RSPAN-konfiguration.":::

1. Ange globalt konfigurations läge.

1. Skapa ett dedikerat VLAN.

1. Identifiera VLAN som RSPAN-VLAN.

1. Gå tillbaka till läget "Konfigurera Terminal".

1. Konfigurera alla 24 portar som Sessions källor.

1. Konfigurera RSPAN VLAN som sessionens mål.

1. Återgå till Privileged EXEC-läge.

1. Verifiera konfiguration av port spegling.

**Exempel på konfiguration av mål växel:**

1. Ange globalt konfigurations läge.

1. Konfigurera RSPAN VLAN som sessionens källa.

1. Konfigurera den fysiska porten 24 som sessionens mål.

1. Återgå till Privileged EXEC-läge.

1. Verifiera konfiguration av port spegling.

1. Spara konfigurationen.

#### <a name="active-and-passive-aggregation-tap"></a>Aktiv och passiv agg regerings KNACKNING

En aktiv eller passiv agg regerings anslutning är direkt installerad på nätverks kabeln. Den duplicerar både RX och TX till den övervakande sensorn.

Terminal Access Point (KNACKNING) är en maskin varu enhet som gör det möjligt för nätverks trafik att flöda från Port A till port B och från Port B till port A, utan avbrott. Den skapar en exakt kopia av båda sidor av trafikflödet, kontinuerligt, utan att kompromissa med nätverks integriteten. Vissa tryckningar sammanställer överföring och tar emot trafik genom att använda växel inställningar om så önskas. Om agg regering inte stöds använder varje tryckning två sensor portar för att övervaka sändnings-och mottagnings trafik.

Knackningar är fördelaktiga av olika anledningar. De är maskin varubaserade och kan inte komprometteras. De skickar all trafik, även skadade meddelanden, vilka växlar ofta släpps. De är inte processor känsliga, så paket tiden är exakt där växlar hanterar funktionen spegel som en låg prioritets aktivitet som kan påverka tiden för de speglade paketen. I kriminal tekniska-syfte är en KNACKNING den bästa enheten.

Tryck på agg regeringar kan också användas för port övervakning. Dessa enheter är processorbaserade och är inte lika säkra som maskin varu tryckningar. De kanske inte återspeglar den exakta paket tiden.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Diagram över aktiva och passiva kranar.":::

##### <a name="common-tap-models"></a>Vanliga tryck modeller

Dessa modeller har testats för kompatibilitet. Andra leverantörer och modeller kan också vara kompatibla.

| Bild | Modell |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Skärm bild av Garland-P1GCCAS."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Skärm bild av IXIA TPA2-CU3."::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Skärm bild av US Robotics USR 4503."::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>Särskild tryck på konfiguration

| Garland tryck | US Robotics-KNACKNING |
| ----------- | --------------- |
| Se till att byglarna är inställda på följande sätt:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Skärm bild av US Robotics-växel.":::| Kontrol lera att agg regerings läge är aktivt. |

## <a name="deployment-validation"></a>Distributions validering

### <a name="engineering-self-review"></a>Själv granskning av teknik  

Att granska nätverks diagrammet för nätverks-och nätverks anslutningar är det mest effektiva sättet att definiera den bästa platsen för att ansluta till, där du kan få den mest relevanta trafiken för övervakning.

Plats teknikerna vet vad deras nätverk ser ut. Att ha en gransknings session med det lokala nätverket och de operativa teamen brukar klargör förväntningar och definiera den bästa platsen för att ansluta enheten.

Relevant information:

- Lista över kända enheter (kalkyl blad)  

- Uppskattat antal enheter  

- Leverantörer och industriella protokoll

- Modell av växlar för att kontrol lera att alternativet för port spegling är tillgängligt

- Information om vem som hanterar växlar (till exempel) och om de är externa resurser

- Lista över nätverks nätverks platser på platsen

#### <a name="common-questions"></a>Vanliga frågor

- Vilka är de övergripande målen för implementeringen? Är det viktigt med en fullständig inventering och korrekt nätverks karta?

- Finns det flera eller redundanta nätverk i ICS? Övervakas alla nätverk?

- Finns det någon kommunikation mellan ICS-nätverket och företags nätverket (företags nätverket)? Övervakas dessa kommunikationer?

- Är VLAN konfigurerat i nätverks designen?

- Hur utförs underhåll av ICS-funktionen med fasta eller tillfälliga enheter?

- Var är brand väggar installerade i de övervakade nätverken?

- Finns det någon routning i de övervakade nätverken?

- Vilka protokoll är aktiva i de övervakade nätverken?

- Om vi ansluter till den här växeln kommer vi att se kommunikationen mellan HMI och PLCs?

- Vad är det fysiska avståndet mellan ICS-växlarna och företags brand väggen? 

- Kan ohanterade växlar ersättas med hanterade växlar eller använder nätverket ett alternativ?

- Finns det någon seriell kommunikation i nätverket? Om ja, visar du det i nätverks diagrammet.

- Om Defender för IoT-enheten ska anslutas till den växeln, finns det fysiskt tillgängligt rack utrymme i skåpet?

#### <a name="other-considerations"></a>Ytterligare överväganden

Syftet med Defender för IoT-enheten är att övervaka trafik från skikt 1 och 2.

För vissa arkitekturer övervakar Defender för IoT-enheten även Layer 3, om den finns på den här nivån. Tänk på följande variabler när du granskar plats arkitekturen och bestämmer om du vill övervaka en växel:

- Vad kostar/fördelarna med att övervaka den här växeln?

- Är det möjligt att övervaka trafiken från en växel på högre nivå om en växel är ohanterad?

  Om ICS-arkitekturen är en ring signal måste bara en växel i den här ringen övervakas.

- Vad är säkerhets-eller drifts risken i det här nätverket?

- Är det möjligt att övervaka växelns VLAN? Är det VLAN synligt i en annan växel som vi kan övervaka?

#### <a name="technical-validation"></a>Teknisk validering

Att ta emot ett exempel på en inspelad trafik (PCAP-fil) från Switch SPAN-porten (eller spegling) kan hjälpa dig att:

- Verifiera om växeln är korrekt konfigurerad.

- Bekräfta om trafiken som går genom växeln är relevant för övervakning (trafik).

- Identifiera bandbredd och uppskattat antal enheter i den här växeln.

Du kan registrera en exempel PCAP-fil (några minuter) genom att ansluta en bärbar dator till en redan konfigurerad SPAN-port via wireshark-programmet.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Skärm bild av en bärbar dator som är ansluten till en intervall port.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Skärm bild av inspelningen av en exempel PCAP-fil.":::

#### <a name="wireshark-validation"></a>Wireshark-validering

- Kontrol lera att *unicast-paket* finns i inspelnings trafiken. Unicast är från en adress till en annan. Om det mesta av trafiken är ARP-meddelanden är växel installationen felaktig.

- Gå till   >  **hierarkin** för statistik protokoll. Kontrol lera att industriella protokoll är tillgängliga.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Skärm bild av wireshark-validering.":::

## <a name="troubleshooting"></a>Felsökning

Använd följande avsnitt för fel söknings problem:

- [Det går inte att ansluta med ett webb gränssnitt](#cant-connect-by-using-a-web-interface)

- [Enheten svarar inte](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Det går inte att ansluta med ett webb gränssnitt

1. Kontrol lera att datorn du försöker ansluta är i samma nätverk som installationen.

2. Kontrol lera att GUI-nätverket är anslutet till hanterings porten på sensorn.

3. Pinga IP-adressen för enheten. Om det inte finns något svar på ping:

    1. Anslut en övervakare och ett tangent bord till-enheten.

    1. Använd **support** användaren och lösen ordet för att logga in.

    1. Använd listan kommando **nätverk** för att se den aktuella IP-adressen.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Skärm bild av kommandot Network List.":::

4. Om nätverks parametrarna är felkonfigurerade kan du använda följande procedur för att ändra den:

    1. Använd kommandot **Network Edit-Settings**.

    1. Om du vill ändra IP-adressen för hanterings nätverket väljer du **Y**.

    1. Om du vill ändra nät masken väljer du **Y**.

    1. Om du vill ändra DNS väljer du **Y**.

    1. Om du vill ändra IP-adress för standard-gateway väljer du **Y**.

    1. Välj **Y** för ändringen av indataports gränssnitt (endast för sensor).

    1. För bryggans gränssnitt väljer du **N**.

    1. Om du vill tillämpa inställningarna väljer du **Y**.

5. När du har startat om ansluter du med användar supporten och använder kommandot **nätverks lista** för att kontrol lera att parametrarna har ändrats.

6. Försök att pinga och ansluta från det grafiska användar gränssnittet igen.

### <a name="appliance-is-not-responding"></a>Enheten svarar inte

1. Anslut med en bildskärm och ett tangent bord till enheten eller Använd SparaTillFil för att fjärrans luta till CLI.

2. Använd de autentiseringsuppgifter som krävs för att logga in.

3. Använd kommandot **system Sanity** och kontrol lera att alla processer körs.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Skärm bild av kommandot system Sanity.":::

Kontakta [Microsoft Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)om du har problem.

## <a name="example-site-book"></a>Exempel på webbplats bok

Använd exempel plats boken för att hämta och granska viktig information som du behöver för nätverks konfiguration.

### <a name="site-checklist"></a>Check lista för webbplats

Granska den här listan innan du distribuerar webbplatsen:

| **#** | **Aktivitet eller aktivitet** | **Status** | **Kommentarer** |
|--|--|--|--|
| 1 | Ange global. | ☐ |  |
| 3 | Beställ apparater. | ☐ |  |
| 4 | Förbered en lista över undernät i nätverket. | ☐ |  |
| 5 | Ange en VLAN-lista över produktions nätverken. | ☐ |  |
| 6 | Ange en lista över växel modeller i nätverket. | ☐ |  |
| 7 | Ange en lista över leverantörer och protokoll för den industriella utrustningen. | ☐ |  |
| 8 | Ange nätverks information för sensorer (IP-adress, undernät, D-GW, DNS). | ☐ |  |
| 9 | Skapa nödvändiga brand Väggs regler och åtkomst listan. | ☐ |  |
| 10 | Skapa utsträckta portar på växlar för port övervakning eller konfigurera nätverks tryckningar efter behov. | ☐ |  |
| 11 | Förbered rack utrymme för sensor utrustning. | ☐ |  |
| 12 | Förbered en arbets station för personal. | ☐ |  |
| 13 | Ange ett tangent bord, en bildskärm och en mus för-rack-enheter för Defender för IoT. | ☐ |  |
| 14 | Rack och kabel av enheterna. | ☐ |  |
| 15 | Allokera plats resurser för att stödja distribution. | ☐ |  |
| 16 | Skapa Active Directory grupper eller lokala användare. | ☐ |  |
| 17 | Konfigurera utbildning (själv inlärning). | ☐ |  |
| 18 | Go eller no-go. | ☐ |  |
| 19 | Schemalägg distributions datumet. | ☐ |  |


| **Datum** | **Obs!** | **Distributions datum** | **Obs!** |
|--|--|--|--|
| Defender för IoT |  | Plats namn * |  |
| Namn |  | Namn |  |
| Position |  | Position |  |

#### <a name="architecture-review"></a>Arkitektur granskning

En översikt över det industriella nätverks diagrammet gör att du kan definiera rätt plats för Defender för IoT-utrustning.

1.  Visa ett globalt nätverks diagram för den industriella miljö miljön. Till exempel:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Diagram över industri miljö för det globala nätverket.":::

    > [!NOTE]
    > Defender för IoT-enheten bör vara ansluten till en lågnivå-växel som ser trafiken mellan portarna på växeln.  

2. Ange det ungefärliga antalet nätverks enheter som ska övervakas. Du behöver den här informationen när du registrerar din prenumeration på Azure Defender för IoT-portalen. Under onboarding-processen uppmanas du att ange antalet enheter i steg om 1000.

3. Ange en under näts lista för produktions nätverken och en beskrivning (valfritt). 

    |  **#**  | **Namn på undernät** | **Beskrivning** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Ange en VLAN-lista över produktions nätverken.

    | **#** | **VLAN-namn** | **Beskrivning** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Du kan kontrol lera att växlarna har port speglings kapacitet genom att ange de växel modell nummer som Defender for IoT-plattformen ska ansluta till:

    | **#** | **Switch** | **Modell** | **Stöd för trafik spegling (SPAN, RSPAN eller none)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Hanterar en tredje part växlar? Y eller N 

    Om ja, vem? __________________________________ 

    Vad är principen? __________________________________ 

    Till exempel:

    - Siemens

    - Rockwell Automation – Ethernet eller IP

    - Emerson – DeltaV, ovation
    
6. Finns det enheter som kommunicerar via en seriell anslutning i nätverket? Ja eller nej 

    Om ja, ange vilket seriellt kommunikations protokoll: ________________ 

    Om ja, markera i nätverks diagrammet vilka enheter som kommunicerar med serie protokoll och var de finns: 
 
    <Add your network diagram with marked serial connection> 

7. För QoS är standard inställningen för sensorn 1,5 Mbit/s. Ange om du vill ändra den: ________________ 

   Affär senhet (BU): ________________ 

### <a name="specifications-for-site-equipment"></a>Specifikationer för plats utrustning

#### <a name="network"></a>Nätverk  

Sensor installationen är ansluten till Switch SPAN-port via ett nätverkskort. Den är ansluten till kundens företags nätverk för hantering via ett annat dedikerat nätverkskort.

Ange adress information för det sensor nätverkskort som ska anslutas i företags nätverket: 

|  Objekt               | Apparat 1 | Apparat 2 | Utrustning 3 |
| --------------- | ------------- | ------------- | ------------- |
| IP-adress för utrustning    |               |               |               |
| Undernät          |               |               |               |
| Standard gateway |               |               |               |
| DNS             |               |               |               |
| Värdnamn       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/iLO/Server Management

|       Objekt          | Apparat 1 | Apparat 2 | Utrustning 3 |
| --------------- | ------------- | ------------- | ------------- |
| IP-adress för utrustning     |               |               |               |
| Undernät          |               |               |               |
| Standard gateway |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Lokal hanterings konsol  

|       Objekt          | Aktiv | Passiv (vid användning av HA) |
| --------------- | ------ | ----------------------- |
| IP-adress             |        |                         |
| Undernät          |        |                         |
| Standard gateway |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Objekt              | Information |
| --------------- | ------ |
| IP-adress              |        |
| IP-adress | |
| Användarnamn | |
| Lösenord | |
| Autentiseringstyp | MD5 eller SHA |
| Kryptering | DES eller AES |
| Hemlig nyckel | |
| SNMP v2-grupp sträng |

### <a name="on-premises-management-console-ssl-certificate"></a>SSL-certifikat för lokal hanterings konsol

Planerar du att använda ett SSL-certifikat? Ja eller nej

Om ja, vilken tjänst kommer du att använda för att skapa den? Vilka attribut ska inkluderas i certifikatet (till exempel domän eller IP-adress)?

### <a name="smtp-authentication"></a>SMTP-autentisering

Planerar du att använda SMTP för att vidarebefordra aviseringar till en e-postserver? Ja eller nej

Om ja, vilken autentiseringsmetod kommer du att använda?  

### <a name="active-directory-or-local-users"></a>Active Directory eller lokala användare

Kontakta en Active Directory administratör om du vill skapa en Active Directory webbplats användar grupp eller skapa lokala användare. Se till att användarna är klara för distributions dagen. 

### <a name="iot-device-types-in-the-network"></a>IoT-enhets typer i nätverket

| Enhetstyp | Antal enheter i nätverket | Genomsnittlig bandbredd |
| --------------- | ------ | ----------------------- |
| Kamera | |
| X-ray-dator | |

## <a name="see-also"></a>Se även

[Om Defender för IoT-installation](how-to-install-software.md)
