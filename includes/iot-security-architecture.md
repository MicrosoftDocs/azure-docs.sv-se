---
title: ta med fil
description: ta med fil
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 28609ad27330ae4ea5ea7c0d02d5a61181fbe0df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95559199"
---
När du designar ett system är det viktigt att förstå de potentiella hoten mot systemet och lägga till lämpliga försvar i enlighet med detta, eftersom systemet är utformat och konstruerat. Det är viktigt att utforma produkten från början med säkerhet i åtanke eftersom du förstår hur en angripare kan kompromettera ett system och se till att lämpliga åtgärder är på plats från början.

## <a name="security-starts-with-a-threat-model"></a>Säkerhet börjar med en hot modell

Microsoft har länge använda hot modeller för sina produkter och har gjort företagets hot modellerings process offentligt tillgänglig. Företags erfarenheten visar att modellen har oväntade förmåner utöver den omedelbara förståelsen av vilka hot som de flesta rör. Till exempel skapar det också en minimering för en öppen diskussion med andra utanför utvecklings teamet, som kan leda till nya idéer och förbättringar i produkten.

Målet med hot modellering är att förstå hur en angripare kan kompromettera ett system och sedan se till att lämpliga åtgärder är på plats. Hot modellering tvingar design teamet att överväga åtgärder som systemet är utformat i stället för när ett system har distribuerats. Detta faktum är kritiskt viktigt, eftersom onlineåterställningspunkter säkerhets försvar till en myriaden av enheter i fältet är ogenomförbar, fel som kan skadas och låter kunderna vara utsatt för risk.

Många utvecklings team gör ett utmärkt jobb för att fånga de funktionella kraven för systemet som fördelaktigar kunderna. Men att identifiera icke-uppenbara sätt som någon kan missbruka systemet är mer utmanande. Hot modellering kan hjälpa utvecklings team att förstå vad en angripare kan göra och varför. Threat Modeling är en strukturerad process som skapar en diskussion om säkerhets design beslut i systemet, samt ändringar i designen som görs på det sätt som påverkar säkerheten. Även om en hot modell bara är ett dokument, representerar den här dokumentationen även ett idealiskt sätt att säkerställa kontinuiteten för kunskap, kvarhållning av lärdomar och hjälpa till att snabbt registrera nya team. Slutligen är resultatet av hot modellering att du kan överväga andra säkerhets aspekter, till exempel vilka säkerhets åtaganden som du vill ge kunderna. Dessa åtaganden tillsammans med hot modellering informerar och testar din Sakernas Internets lösning (IoT).

### <a name="when-to-do-threat-modeling"></a>När du ska utföra hot modellering

[Threat Modeling](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) erbjuder det största värdet när du införlivar det i Design fasen. När du utformar har du störst flexibilitet att göra ändringar för att eliminera hot. Att eliminera hot efter design är det önskade resultatet. Det är mycket enklare än att lägga till åtgärder, testa dem och se till att de förblir aktuella och dessutom är sådan Eli minering inte alltid möjlig. Det blir svårare att eliminera hot när en produkt blir mer vuxen, och i tur och miljö behöver du mer arbete och mycket svårare kompromisser än hot modellering som är tidiga vid utvecklingen.

### <a name="what-to-consider-for-threat-modeling"></a>Vad du bör tänka på vid Threat Modeling

Du bör titta på lösningen som helhet och även fokusera på följande områden:

* Funktioner för säkerhet och sekretess
* De funktioner vars problem är säkerhets relevanta
* De funktioner som vidrör en förtroende gräns

### <a name="who-performs-threat-modeling"></a>Som utför hot modellering

Hot modellering är en process som någon annan. Det är en bra idé att behandla dokument för hotet Model som vilken annan komponent som helst i lösningen och verifiera det. Många utvecklings team gör ett utmärkt jobb för att fånga de funktionella kraven för systemet som fördelaktigar kunderna. Men att identifiera icke-uppenbara sätt som någon kan missbruka systemet är mer utmanande. Hot modellering kan hjälpa utvecklings team att förstå vad en angripare kan göra och varför.

### <a name="how-to-perform-threat-modeling"></a>Så här utför du hot modellering

Threat Modeling-processen består av fyra steg. stegen är:

* Modellera programmet
* Räkna upp hot
* Minimera hot
* Verifiera åtgärder

#### <a name="the-process-steps"></a>Process stegen

Tre tumregels regler som du bör tänka på när du skapar en hot modell:

1. Skapa ett diagram av referens arkitekturen.

2. Start bredd – först. Få en översikt och förstå hela systemet, innan djup simhopp. Den här metoden hjälper till att se till att du får djupet på rätt plats.

3. Kör processen, låt inte process enheten användas. Om du hittar ett problem i modell fasen och vill utforska det går du till! Du behöver inte följa de här stegen slavishly.

#### <a name="threats"></a>Hot

De fyra kärn elementen i en hot modell är:

* Processer som webb tjänster, Win32-tjänster och * nix-daemon. Vissa komplexa entiteter (t. ex. fält-gatewayer och sensorer) kan sammanställas som en process när det inte går att göra en teknisk granskning i dessa områden.

* Data lager (varifrån som helst lagras, till exempel en konfigurations fil eller databas)

* Data flöde (där data flyttas mellan andra element i programmet)

* Externa entiteter (allt som interagerar med systemet, men som inte är under kontroll av programmet, innehåller exempel användare och satellit flöden)

Alla element i arkitektur diagrammet omfattas av olika hot. den här artikeln är KLIVe. Läs [Threat Modeling igen och](/archive/blogs/larryosterman/threat-modeling-again-stride) Läs mer om kliv-elementen.

Olika element i program diagrammet omfattas av vissa kliv-hot:

* Processer omfattas av kliv
* Data flöden är beroende av TID
* Data lager omfattas av TID och ibland R när data lager är loggfiler.
* Externa entiteter omfattas av SRD

## <a name="security-in-iot"></a>Säkerhet i IoT

Anslutna särskilda syftes enheter har ett stort antal möjliga interaktions områden och interaktions mönster, som alla måste anses vara ett ramverk för att skydda digital åtkomst till dessa enheter. Termen "digital åtkomst" används här för att skilja från åtgärder som utförs via direkta interaktioner mellan enheter där åtkomst säkerhet tillhandahålls via fysisk åtkomst kontroll. Du kan till exempel placera enheten i ett rum med lås på dörren. Även om fysisk åtkomst inte kan nekas med hjälp av program vara och maskin vara kan du vidta åtgärder för att förhindra fysisk åtkomst från att leda till system störningar.

När du utforskar interaktions mönstren tittar du på "enhets kontroll" och "enhets data" med samma åtgärds nivå. "Enhets kontroll" kan klassificeras som all information som ges till en enhet av en part med målet att ändra eller påverka dess beteende gentemot dess tillstånd eller tillstånd för dess miljö. "Enhets data" kan klassificeras som all information som en enhet avger till någon annan part om dess tillstånd och miljöns observerade tillstånd.

För att optimera säkerhets praxis, rekommenderar vi att en typisk IoT-arkitektur är indelad i flera komponenter/zoner som en del av Threat Model-övningen. Dessa zoner beskrivs fullständigt i det här avsnittet och inkluderar:

* Anordningar
* Fält-Gateway,
* Moln-gatewayer och
* Terminal.

Zoner är breda sätt att segmentera en lösning. varje zon har ofta egna krav på data och autentisering och auktorisering. Zoner kan också användas för att isolera skador och begränsa effekten av låg förtroende zoner på högre förtroende zoner.

Varje zon skiljs åt av en förtroende gräns, som anges som en streckad röd linje i följande diagram. Den representerar en över gång av data/information från en källa till en annan. Under den här över gången kan data/information omfattas av förfalskning, manipulering, oavvislig het, informations avslöjande, Denial of service och höjning av privilegier (kliv).

![IoT-säkerhetszoner](media/iot-security-architecture/iot-security-architecture-fig1.png) 

De komponenter som illustreras inom varje gränser omfattas också av kliv, vilket möjliggör en fullständig 360 Threat Modeling-vy av lösningen. Följande avsnitt innehåller information om var och en av komponenterna och de säkerhets problem och lösningar som bör placeras på plats.

I följande avsnitt beskrivs standard komponenter som vanligt vis finns i dessa zoner.

### <a name="the-device-zone"></a>Enhets zonen

Enhets miljön är det omedelbara fysiska utrymmet runt enheten där fysisk åtkomst och/eller "lokalt nätverk" peer-to-peer digital åtkomst till enheten är möjligt. Ett "lokalt nätverk" antas vara ett nätverk som är tydligt och isolerat från – men som eventuellt är bryggad till – det offentliga Internet, och som innehåller en trådlös radio teknik med korta intervall som tillåter peer-to-peer-kommunikation av enheter. Den innehåller ingen teknik för nätverksvirtualisering som skapar en illusion av ett sådant lokalt nätverk och *det inkluderar inte* heller offentliga operatörs nätverk som kräver två enheter för att kommunicera över det offentliga nätverks utrymmet om de vore att ange en peer-to-peer-kommunikations relation.

### <a name="the-field-gateway-zone"></a>Fält-Gateway-zon

Fält-Gateway är en enhet/apparat eller en program vara för generell användning av Server datorer som fungerar som kommunikations tjänst och som kan användas som enhets kontroll system och enhets data bearbetnings hubb. Field Gateway-zonen inkluderar själva fält-gatewayen och alla enheter som är kopplade till den. Eftersom namnet antyder fungerar fält-gatewayer utanför dedikerade data bearbetnings anläggningar, är vanligt vis plats gränser, de kan vara beroende av fysiskt intrång och har begränsad funktions redundans. Allt för att anta att en fält-Gateway ofta är en som kan vidrör och sabotage, samtidigt som man vet vad dess funktion är.

En fält-Gateway skiljer sig från en router med enbart trafik eftersom den har haft en aktiv roll i hantering av åtkomst-och informations flöden, vilket innebär att det är en entitet för programentiteter och nätverks anslutning eller sessions-Terminal. En NAT-enhet eller brand vägg är däremot inte kvalificerad som fält-gatewayer eftersom de inte är explicita anslutningar eller sessioner, utan i stället en väg (eller blockera) anslutningar eller sessioner som görs via dem. Fält-gatewayen har två distinkta ytor. En ansikten är de enheter som är kopplade till den och representerar insidan av zonen, och andra ansikten alla externa parter och är gränsen för zonen.

### <a name="the-cloud-gateway-zone"></a>Cloud Gateway-zonen

En moln-Gateway är ett system som möjliggör fjärr kommunikation från och till enheter eller fält-gatewayer från flera olika platser över ett offentligt nätverks utrymme, vanligt vis mot ett molnbaserad kontroll-och data analys system, en federation av sådana system. I vissa fall kan en moln-Gateway omedelbart under lätta åtkomsten till särskilda enheter från terminaler som surfplattor eller telefoner. I det sammanhang som beskrivs här är "moln" avsett att referera till ett dedikerat data behandlings system som inte är kopplat till samma plats som de anslutna enheterna eller fält-gatewayerna. I en moln zon förhindrar drift åtgärder för att nå fysisk åtkomst och kan inte nödvändigt vis exponeras för en "offentlig moln infrastruktur".  

En moln-Gateway kan eventuellt mappas till ett överlägg för nätverksvirtualisering för att isolera moln-gatewayen och alla dess anslutna enheter eller fält-gatewayer från valfri annan nätverks trafik. Själva moln-gatewayen är inte ett enhets kontroll system eller en bearbetnings-eller lagrings funktion för enhets data. dessa anläggningar-gränssnitt med moln-gatewayen. Cloud Gateway-zonen inkluderar själva moln-gatewayen tillsammans med alla fält-gatewayer och enheter som är direkt eller indirekt kopplade till den. Gränsen för zonen är en distinkt yta där alla externa parter kommunicerar genom.

### <a name="the-services-zone"></a>Zonen tjänster

En "tjänst" definieras för den här kontexten som en program varu komponent eller modul som är kopplad till enheter via en fält-eller molnbaserad Gateway för data insamling och analys, samt för kommando och kontroll. Tjänsterna är Mediators. De agerar under sin identitet mot gatewayer och andra under system, lagrar och analyserar data, självständigt utfärdar kommandon till enheter som baseras på data insikter eller scheman och ger till gång till information och kontroll funktioner till behöriga slutanvändare.

### <a name="information-devices-versus-special-purpose-devices"></a>Informations enheter jämfört med särskilda användnings enheter

Datorer, telefoner och surfplattor är huvudsakligen interaktiva informations enheter. Telefoner och surfplattor optimeras explicit runt batteriets livs längd. De bör helst stänga av delvis när de inte direkt interagerar med en person, eller när de inte tillhandahåller tjänster som att spela musik eller GUID för deras ägare till en viss plats. Från ett system perspektiv fungerar dessa informations teknik enheter främst som proxyservrar för människor. De är "person motstånd" som föreslår åtgärder och "människor sensorer" insamlar inmatade uppgifter.

Särskilda enheter, från enkla temperatur sensorer till komplexa fabriks produktions linjer med tusentals komponenter inuti dem, skiljer sig åt. De här enheterna är mycket mer begränsade och även om de tillhandahåller ett visst användar gränssnitt, är de i stort sett begränsade till att samverka med eller vara integrerade i till gångar i den fysiska världen. De mäter och rapporterar miljö förhållanden, vänder ventiler, styr Servos, ljud larm, växlar lampor och utför många andra uppgifter. De hjälper dig att utföra arbete för vilka en informations enhet är antingen för allmän, för dyr, för stor eller för sårbar. Betong syftet styr omedelbart sin tekniska design och den tillgängliga ekonomiska budgeten för deras produktion och schemalagda livs längd. Kombinationen av de här två viktiga faktorerna begränsar den tillgängliga drift energi budgeten, den fysiska storleken och därmed tillgängliga lagrings-, beräknings-och säkerhets funktioner.

Om något "hamnar fel" med automatiserade eller fjärrstyrda enheter, till exempel fysiska defekter eller kontroll logiks fel för att willful obehörigt intrång och manipulering. Produktions partierna kan förstöras, byggnader kan vara looteda eller nedbrända och människor kan vara skadade eller till och med Die. Detta är en hel annan klass av skada än någon som maxing ut en stulen kredit korts gräns. Säkerhets fältet för enheter som gör att saker flyttas och även för sensor data som slutligen resulterar i kommandon som gör att det går att flytta, måste vara högre än i ett e-handels-eller bank scenario.

### <a name="device-control-and-device-data-interactions"></a>Interaktion av enhets kontroll och enhets data

Anslutna särskilda syftes enheter har ett stort antal möjliga interaktions områden och interaktions mönster, som alla måste anses vara ett ramverk för att skydda digital åtkomst till dessa enheter. Termen "digital åtkomst" används här för att skilja från åtgärder som utförs via direkta interaktioner mellan enheter där åtkomst säkerhet tillhandahålls via fysisk åtkomst kontroll. Du kan till exempel placera enheten i ett rum med lås på dörren. Även om fysisk åtkomst inte kan nekas med hjälp av program vara och maskin vara kan du vidta åtgärder för att förhindra fysisk åtkomst från att leda till system störningar.

När du utforskar interaktions mönstren kan du titta på "enhets kontroll" och "enhets data" med samma nivå av uppmärksamhet och hot modellering. "Enhets kontroll" kan klassificeras som all information som ges till en enhet av en part med målet att ändra eller påverka dess beteende gentemot dess tillstånd eller tillstånd för dess miljö. "Enhets data" kan klassificeras som all information som en enhet avger till någon annan part om dess tillstånd och miljöns observerade tillstånd.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Utföra hot modellering för Azure IoT-referens arkitekturen

Microsoft använder ramverket som beskrivs tidigare för att utföra hot modellering för Azure IoT. I följande avsnitt används konkret exempel på referens arkitektur för Azure IoT för att demonstrera hur du kan tänka på Threat Modeling för IoT och hur du åtgärdar de hot som identifieras. I det här exemplet identifieras fyra huvud fokus områden:

* Enheter och data källor,
* Data transport,
* Enhets-och händelse bearbetning och
* Presentation

![Hot modellering för Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Följande diagram ger en förenklad vy av Microsofts IoT-arkitektur med en data flödes diagram modell som används av Microsoft Threat Modeling Tool:

![Hot modellering för Azure IoT med MS Threat Modeling Tool](media/iot-security-architecture/iot-security-architecture-fig3.png)

Det är viktigt att Observera att arkitekturen skiljer enhets-och gateway-funktionerna. Den här metoden gör det möjligt för användaren att utnyttja gateway-enheter som är säkrare: de kan kommunicera med moln-gatewayen med hjälp av säkra protokoll, vilket vanligt vis kräver större bearbetnings belastning som en inbyggd enhet, till exempel en termostat, kan tillhandahålla sig själv. I Azure-tjänstens zon antar vi att Cloud Gateway representeras av Azure IoT Hub-tjänsten.

### <a name="device-and-data-sourcesdata-transport"></a>Enhets-och data källor/data transport

I det här avsnittet får du en översikt över den arkitektur som beskrivs tidigare via linsen av hot modellering och ger en översikt över hur du kan åtgärda några av de olika problemen. Det här exemplet fokuserar på kärn elementen i en hot modell:

* Processer (både under din kontroll och externa objekt)
* Kommunikation (kallas även data flöden)
* Lagring (kallas även data lager)

#### <a name="processes"></a>Processer

I var och en av de kategorier som beskrivs i Azure IoT-arkitekturen försöker det här exemplet minska ett antal olika hot i olika steg data/information finns i: process, kommunikation och lagring. Nedan följer en översikt över de vanligaste för kategorin "process", följt av en översikt över hur dessa hot kan minskas bäst:

**Förfalskning (er)**: en angripare kan extrahera kryptografiskt nyckel material från en enhet, antingen på program-eller maskin varu nivå, och sedan komma åt systemet med en annan fysisk eller virtuell enhet under identiteten för den enhet som nyckel materialet har hämtats från. En klar bild är fjärrstyrda som kan koppla från TV och som är populära Prankster-verktyg.

**Denial of service (D)**: en enhet kan återges som inte kan fungera eller kommunicera genom att störa radio frekvenser eller skära kablar. Till exempel kan en övervaknings kamera som hade sin ström-eller nätverks anslutning avsiktligt blockeras och inte rapportera data alls.

**Manipulering (T)**: en angripare kan delvis eller helt ersätta program varan som körs på enheten, vilket kan göra att den ersatta program varan kan utnyttja enhetens äkta identitet om nyckel materialet eller de kryptografiska anläggningar som innehåller viktiga material är tillgängliga för illegal program. En angripare kan till exempel utnyttja extraherat nyckel material för att fånga upp och utelämna data från enheten på kommunikations vägen och ersätta den med falska data som autentiseras med det stulna nyckel materialet.

**Utlämnande av information (I)**: om enheten kör manipulerad program vara kan sådan manipulerad program vara potentiellt läcka data till obehöriga parter. En angripare kan till exempel utnyttja det extraherade nyckel materialet för att mata in sig själv i kommunikations vägen mellan enheten och en kontrollant eller en fält-gateway eller en molnbaserad Siphon av information.

**Utökade privilegier (E)**: en enhet som har en speciell funktion kan tvingas att göra något annat. Till exempel kan en ventil som är programmerad med öppen hälft vara svår att öppna på hela vägen.

| **Komponent** | **Hot** | **Åtgärd** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enhet |S |Tilldela identitet till enheten och autentisera enheten |Ersätta enheten eller delar av enheten med en annan enhet. Hur vet du att du pratar med rätt enhet? |Autentisera enheten med hjälp av Transport Layer Security (TLS) eller IPSec. Infrastrukturen bör ha stöd för användning av i förväg delad nyckel (PSK) på de enheter som inte kan hantera fullständig asymmetrisk kryptering. Utnyttja Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Tillämpa tamperproof-mekanismer på enheten, till exempel genom att göra det svårt att extrahera nycklar och annat kryptografiskt material från enheten. |Risken är att någon manipulerar enheten (fysisk interferens). Hur vill du att enheten inte har manipulerats. |Den mest effektiva minskningen är en TPM-kapacitet (Trusted Platform Module) som gör det möjligt att lagra nycklar i särskilda chip som nycklarna inte kan läsas från, men som bara kan användas för kryptografiska åtgärder som använder nyckeln men som aldrig lämnar nyckeln. Minnes kryptering för enheten. Nyckel hantering för enheten. Signerar koden. |
|| E |Har åtkomst kontroll över enheten. Authorization-schema. |Om enheten gör det möjligt för enskilda åtgärder att utföras baserat på kommandon från en extern källa, eller till och med komprometterade sensorer, kan angreppet utföra åtgärder som inte är tillgängliga på annat sätt. |Har ett Authorization-schema för enheten |
| Fält-Gateway |S |Autentisering av fältets Gateway till Cloud Gateway (till exempel cert-baserat, PSK eller anspråk baserat.) |Om någon kan falska fält Gateway kan den presentera sig själv som vilken enhet som helst. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Alla samma nyckel lagrings-och attesterings aspekter av enheterna i allmänhet – det bästa fallet är att använda TPM. 6LowPAN-tillägg för IPSec som stöder trådlösa sensor nätverk (WSN). |
|| TRID |Skydda fält-gatewayen mot manipulering (TPM?) |Förfalsknings attacker som lurar den moln-gateway som tänker på att den pratar om fält-Gateway kan leda till att information avslöjas och att data manipuleras |Minnes kryptering, TPM, autentisering. |
|| E |Åtkomst kontroll mekanism för Field Gateway | | |

Här följer några exempel på hot i den här kategorin:

**Förfalskning**: en angripare kan extrahera kryptografiskt nyckel material från en enhet, antingen på program-eller maskin varu nivå, och sedan komma åt systemet med en annan fysisk eller virtuell enhet under identiteten för den enhet som nyckel materialet har hämtats från.

**Denial of service**: en enhet kan återges som inte kan fungera eller kommunicera genom att störa radio frekvenser eller skära kablar. Till exempel kan en övervaknings kamera som hade sin ström-eller nätverks anslutning avsiktligt blockeras och inte rapportera data alls.

**Manipulering**: en angripare kan delvis eller helt ersätta program varan som körs på enheten, vilket kan göra att den ersatta program varan kan utnyttja enhetens äkta identitet om nyckel materialet eller de kryptografiska anläggningar som innehåller viktiga material är tillgängliga för illegal program.

**Manipulering**: en övervaknings kamera som visar en bild av ett synligt spektrum av en tom Hall kan riktas mot ett fotografi av en sådan Hall. En rök-eller brand sensor kan rapportera att någon delar en ljusare under IT. I båda fallen kan enheten vara tekniskt tillförlitlig i systemet, men den rapporterar manipulerad information.

**Manipulering**: en angripare kan utnyttja extraherat nyckel material för att fånga upp och utelämna data från enheten på kommunikations vägen och ersätta den med falska data som autentiseras med det stulna nyckel materialet.

**Manipulering**: en angripare kan helt eller delvis ersätta program varan som körs på enheten, vilket kan göra att den ersatta program varan kan utnyttja enhetens äkta identitet om nyckel materialet eller de kryptografiska anläggningar som innehåller viktiga material är tillgängliga för illegal program.

**Utlämnande av information**: om enheten kör manipulerad program vara kan sådan manipulerad program vara potentiellt läcka data till obehöriga parter.

**Utlämnande av information**: en angripare kan utnyttja det extraherade nyckel materialet för att mata in sig själv i kommunikations vägen mellan enheten och en kontrollant eller en fält-gateway eller en molnbaserad Siphon av information.

**Denial of service**: enheten kan stängas av eller inaktive ras i ett läge där kommunikationen inte är möjlig (vilket är avsiktligt i många industriella datorer).

**Manipulering**: enheten kan konfigureras om för att fungera i ett tillstånd som är okänt för kontroll systemet (utanför kända kalibrerings parametrar) och därmed förses med data som kan feltolkas

**Höjning av behörighet**: en enhet som har en speciell funktion kan tvingas att göra något annat. Till exempel kan en ventil som är programmerad med öppen hälft vara svår att öppna på hela vägen.

**Denial of service**: enheten kan inaktive ras i ett tillstånd där kommunikationen inte är möjlig.

**Manipulering**: enheten kan konfigureras om för att fungera i ett tillstånd som är okänt för kontroll systemet (utanför kända kalibrerings parametrar) och därmed tillhandahålla data som kan feltolkas.

**Förfalskning/manipulering/oavvislig het**: om det inte är skyddat (vilket sällan är fallet med klientens fjärrstyrda kontroller) kan en angripare ändra status för en enhet anonymt. En klar bild är fjärrstyrda som kan koppla från TV och som är populära Prankster-verktyg.

#### <a name="communication"></a>Kommunikation

Hot kring kommunikations vägen mellan enheter, enheter och fält-gatewayer samt enhets-och moln-Gateway. Följande tabell innehåller en vägledning kring Open Sockets på enheten/VPN:

| **Komponent** | **Hot** | **Åtgärd** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enhets IoT Hub |BEKRÄFTA |Styr TLS (PSK/RSA) för att kryptera trafiken |Avlyssning eller störande kommunikation mellan enheten och gatewayen |Säkerhet på protokoll nivå. Med anpassade protokoll måste du ta reda på hur du skyddar dem. I de flesta fall sker kommunikationen från enheten till den IoT Hub (enheten initierar anslutningen). |
| Enhet till enhet |BEKRÄFTA |Styr TLS (PSK/RSA) för att kryptera trafiken. |Läser in data i överföring mellan enheter. Manipulering av data. Överbelasta enheten med nya anslutningar |Säkerhet på protokoll nivå (MQTT/AMQP/HTTP/CoAP. Med anpassade protokoll måste du ta reda på hur du skyddar dem. Minskningen av DoS-hotet är att peer-enheter via en moln-eller fält-gateway och att de endast fungerar som klienter i nätverket. Peer koppling kan resultera i en direkt anslutning mellan peer-datorerna när den har frisläppts av gatewayen |
| Extern enhets enhet |BEKRÄFTA |Stark länkning av den externa entiteten till enheten |Avlyssning av anslutningen till enheten. Störa kommunikationen med enheten |Koppla den externa entiteten på ett säkert sätt till enhet NFC/Bluetooth LE. Styra enhetens operativa panel (fysisk) |
| Cloud Gateway för fält Gateway |BEKRÄFTA |TLS (PSK/RSA) för att kryptera trafiken. |Avlyssning eller störande kommunikation mellan enheten och gatewayen |Säkerhet på protokoll nivå (MQTT/AMQP/HTTP/CoAP). Med anpassade protokoll måste du ta reda på hur du skyddar dem. |
| Cloud Gateway för enhet |BEKRÄFTA |TLS (PSK/RSA) för att kryptera trafiken. |Avlyssning eller störande kommunikation mellan enheten och gatewayen |Säkerhet på protokoll nivå (MQTT/AMQP/HTTP/CoAP). Med anpassade protokoll måste du ta reda på hur du skyddar dem. |

Här följer några exempel på hot i den här kategorin:

**Denial of service**: begränsade enheter är vanligt vis under dos-hot när de aktivt lyssnar efter inkommande anslutningar eller oönskade datagram i ett nätverk, eftersom en angripare kan öppna många anslutningar parallellt och inte betjäna dem eller betjäna dem långsamt, eller så kan enheten översvämmas med oönskad trafik. I båda fallen kan enheten effektivt renderas i nätverket.

**Förfalskning, avslöjande av information**: begränsade enheter och särskilda användnings enheter har ofta en-för-alla säkerhetsfunktioner, t. ex. lösen ord eller PIN-skydd, eller de är helt beroende av att lita på nätverket, vilket innebär att de beviljar åtkomst till information när en enhet är i samma nätverk och nätverket ofta skyddas av en delad nyckel. Det innebär att när den delade hemligheten till enheten eller nätverket har lämnats, är det möjligt att kontrol lera enheten eller Observera data som har avsänts från enheten.  

**Förfalskning**: en angripare kan fånga eller delvis åsidosätta sändningen och förfalskningarna från upphovs mannen (man i mitten)

**Manipulering**: en angripare kan fånga upp eller delvis åsidosätta sändningen och skicka falsk information 

**Utlämnande av information:** en angripare kan eavesdrop en sändning och få information utan auktorisering **av denial of service:** en angripare kan ta emot sändnings signalen och neka informations distribution

#### <a name="storage"></a>Storage

Varje enhet och fält-Gateway har någon form av lagring (temporär för köer data, operativ system avbildnings lagring).

| **Komponent** | **Hot** | **Åtgärd** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enhets lagring |TRID |Lagrings kryptering, signering av loggar |Läsning av data från lagrings platsen (PII-data), manipulering av telemetridata. Manipulering av data för kommando kontroll i kö eller cache. Manipulering av konfigurations paket eller uppdaterings paket för inbyggd program vara när cachelagrade eller köade lokalt kan leda till att OS och/eller system komponenter komprometteras |Kryptering, Message Authentication Code (MAC) eller digital signatur. Där det är möjligt, stark åtkomst kontroll via listor över åtkomst kontrol listor (ACL: er) eller behörigheter. |
| Enhets operativ system avbildning |TRID | |Manipulering av OS-/Replacing OS-komponenter |Skrivskyddad OS-partition, signerad OS-avbildning, kryptering |
| Fält-Gateway-lagring (köa data) |TRID |Lagrings kryptering, signering av loggar |Läsning av data från lagrings platsen (PII-data), manipulering av telemetridata, manipulering av köade eller cachelagrade kommando kontroll data. Manipulering av konfigurations paket eller uppdaterings paket för inbyggd program vara (avsedda för enheter eller fält-Gateway) när de cachelagras eller köas lokalt kan leda till att OS och/eller system komponenter komprometteras |BitLocker |
| Fält Gateway OS-avbildning |TRID | |Manipulering av OS-/Replacing OS-komponenter |Skrivskyddad OS-partition, signerad OS-avbildning, kryptering |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Enhets-och händelse bearbetning/Cloud Gateway-zon

En moln-Gateway är ett system som möjliggör fjärr kommunikation från och till enheter eller fält-gatewayer från flera olika platser över ett offentligt nätverks utrymme, vanligt vis mot ett molnbaserad kontroll-och data analys system, en federation av sådana system. I vissa fall kan en moln-Gateway omedelbart under lätta åtkomsten till särskilda enheter från terminaler som surfplattor eller telefoner. I det sammanhang som beskrivs här är "moln" avsett att referera till ett dedikerat data behandlings system som inte är kopplat till samma plats som de anslutna enheterna eller fält-gatewayerna, och där drift åtgärder förhindrar riktad fysisk åtkomst, men inte nödvändigt vis till en "offentlig moln infrastruktur". En moln-Gateway kan eventuellt mappas till ett överlägg för nätverksvirtualisering för att isolera moln-gatewayen och alla dess anslutna enheter eller fält-gatewayer från valfri annan nätverks trafik. Själva moln-gatewayen är inte ett enhets kontroll system eller en bearbetnings-eller lagrings funktion för enhets data. dessa anläggningar-gränssnitt med moln-gatewayen. Cloud Gateway-zonen inkluderar själva moln-gatewayen tillsammans med alla fält-gatewayer och enheter som är direkt eller indirekt kopplade till den.

Cloud Gateway är mest anpassad inbyggd program vara som körs som en tjänst med exponerade slut punkter som fält-gateway och enheter ansluter till. Det måste vara utformat med säkerhet i åtanke. Följ [sdl](https://www.microsoft.com/sdl) -processen för att designa och skapa den här tjänsten.

#### <a name="services-zone"></a>Tjänst zon

Ett kontroll system (eller kontrollant) är en program varu lösning som innehåller en enhet, en fält-gateway eller en molnbaserad Gateway för att styra en eller flera enheter och/eller för att samla in och/eller lagra och/eller analysera enhets data för presentation, eller efterföljande kontroll syften. Kontroll system är de enda enheterna inom den här diskussions omfånget som kan omedelbart under lätta interaktion med personer. Undantagen är mellanliggande fysiska kontroll ytor på enheter, till exempel en växel som gör att en person kan stänga av enheten eller ändra andra egenskaper, och för vilka det inte finns någon funktionell motsvarighet som kan nås digitalt.

Mellanliggande fysiska kontroll ytor är de där den styrande logiken begränsar funktionen hos den fysiska kontroll ytan, så att en motsvarande funktion kan initieras via fjärr anslutning eller indatamängds konflikter med fjärrinloggning kan undvikas – sådana mellanliggande kontroll ytor är konceptuellt kopplade till ett lokalt kontroll system som använder samma underliggande funktioner som andra fjärr styrnings system som enheten kan vara ansluten till parallellt. De främsta hoten till molnbaserad data behandling kan läsas på sidan [CSA (Cloud Security Alliance)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) .

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i följande artiklar:

* [SDL-Threat Modeling Tool](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Referens arkitektur för Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)