---
title: ta med fil
description: ta med fil
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 234407e6973657ba3ad0d78189e7cb1d363c15e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95555242"
---
Sakernas Internet (IoT) utgör unika utmaningar för säkerhet, sekretess och efterlevnad för företag över hela världen. Till skillnad från traditionell cyberhot-teknik där problemen kretsar kring program vara och hur de implementeras, berör IoT vad som händer när cyberhot och det fysiska världar konvergerar. Att skydda IoT-lösningar kräver säker etablering av enheter, säker anslutning mellan enheterna och molnet och skyddar data skyddet i molnet under bearbetning och lagring. Att arbeta med sådana funktioner är dock resurs begränsade enheter, geografisk distribution av distributioner och ett stort antal enheter inom en lösning.

Den här artikeln visar hur IoT Solution Accelerators tillhandahåller en säker och privat Sakernas Internet moln lösning. Lösnings acceleratorerna levererar en komplett lösning från slut punkt till slut punkt, med säkerhet som är inbyggd i varje fas från grunden. På Microsoft är att utveckla säker program vara en del av den tekniska övningen, som är rotad i Microsofts årtionde för att utveckla säkra program. För att säkerställa detta, är säkerhets utvecklings livs cykeln (SDL) den grundläggande utvecklings metoden, tillsammans med en värd för säkerhets tjänster på infrastruktur nivå, till exempel drift säkerhets säkring (OSA) och Microsoft Digital brottslighet-enhet, Microsoft Security Response Center och Microsoft Malware Protection Center.

Lösnings acceleratorerna erbjuder unika funktioner som gör det enkelt och transparent att skapa, ansluta till och lagra data från IoT-enheter, och de flesta är säkra. Den här artikeln undersöker säkerhetsfunktionerna i Azure IoT Solution acceleratorer och distributions strategier för att se till att säkerhets-, sekretess-och efterlevnads utmaningarna åtgärdas.

## <a name="introduction"></a>Introduktion

Den Sakernas Internet (IoT) är den som är en framtid av framtiden, som erbjuder företag omedelbara och verkliga möjligheter att minska kostnaderna, öka intäkterna och omvandla sin verksamhet. Många företag är dock tveka att distribuera IoT i sina organisationer på grund av problem med säkerhet, sekretess och efterlevnad. En viktig angelägenhet kommer från den unika IoT-infrastrukturen, som sammanfogar cyberhot och fysiska världar, som sammanställer enskilda risker i dessa två världar. Säkerhet för IoT är ett sätt att säkerställa integriteten för kod som körs på enheter, vilket ger enhets-och användarautentisering, definierar rensad ägande rätt för enheter (samt data som genereras av enheterna) och som är elastiska till cyberhot och fysiska attacker.

Sedan är det problem med sekretessen. Företag vill ha genomskinlighet för data insamling, som i vad som samlas in och varför, vem som kan se den, vem som kontrollerar åtkomsten och så vidare. Slutligen finns det generella säkerhets problem hos utrustningen tillsammans med de personer som arbetar med dem och problem med att upprätthålla bransch standarder för efterlevnad.

Med hänsyn till säkerhet, sekretess, transparens och efterlevnad kan du välja rätt IoT Solution Provider som är en utmaning. Att häfta samman enskilda typer av IoT-program och-tjänster som tillhandahålls av en rad olika leverantörer, ger upphov till luckor i säkerhet, sekretess, transparens och efterlevnad, vilket kan vara svårt att upptäcka, vilket ger en ensam rätt lösning. Valet av rätt IoT-programvara och tjänst leverantör baseras på att hitta leverantörer som har omfattande erfarenhet av att köra tjänster, som sträcker sig över lodräta och geografiska områden, men kan också skala på ett säkert och transparent sätt. På samma sätt bidrar det till att den valda providern har en lång erfarenhet av att utveckla säker program vara som körs på miljard tals datorer i hela världen och kan uppskatta det hot som ligger i den nya världen av Sakernas Internet.

## <a name="secure-infrastructure-from-the-ground-up"></a>Skydda infrastrukturen från grunden

[Microsoft Cloud](https://azure.microsoft.com) -infrastrukturen har stöd för över 1 000 000 000 kunder i 127 länder/regioner. Genom att rita på Microsofts årtionde-långa erfarenhet av att bygga företags program vara och köra några av de största onlinetjänstererna i världen, ger Microsoft Cloud högre nivåer av förbättrad säkerhet, sekretess, efterlevnad och hot minsknings metoder än de flesta kunder kan åstadkomma sig själva.

[Livs cykeln för säkerhets utveckling (SDL)](https://www.microsoft.com/sdl/) tillhandahåller en obligatorisk utvecklings process för hela företaget som bäddar in säkerhets krav i hela program livs cykeln. För att säkerställa att operativa aktiviteter följer samma säkerhets praxis, använder SDL rigorösa säkerhets rikt linjer som finns i Microsofts process för Operational Security Assurance (OSA). Microsoft arbetar också med gransknings företag från tredje part för att kunna verifiera att de uppfyller sina krav på efterlevnad, och Microsoft samarbetar i stora säkerhets ansträngningar genom att skapa Expert Center, inklusive Microsoft Digital brottslighet-enhet, Microsoft Security Response Center och Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure säker IoT-infrastruktur för ditt företag

Microsoft Azure erbjuder en komplett moln lösning, en som kombinerar en ständigt växande samling integrerade moln tjänster – analys, maskin inlärning, lagring, säkerhet, nätverk och webb – med ett branschledande engagemang för skyddet och sekretessen för dina data. Microsofts strategi för att komma till [överträdelse](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) använder ett dedikerat *rött team* av program varu säkerhets experter som simulerar attacker, testar förmågan hos Azure att upptäcka, skydda mot nya hot och återställa mot överträdelser. Microsofts [globala incident](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) hanterings team arbetar dygnet runt för att minimera effekterna av attacker och skadlig aktivitet. Teamet följer etablerade procedurer för incident hantering, kommunikation och återställning, och använder sig av identifierbara och förutsägbara gränssnitt med interna och externa partner.

Microsofts system ger kontinuerlig intrångs identifiering och förebyggande, skydd av tjänst attacker, testning av vanliga inläsningar och kriminal tekniska verktyg som hjälper dig att identifiera och åtgärda hot. [Multi-Factor Authentication](../articles/active-directory/authentication/concept-mfa-howitworks.md) ger ett extra säkerhets lager som slutanvändarna kan använda för att få åtkomst till nätverket. Och för program-och värd leverantören erbjuder Microsoft åtkomst kontroll, övervakning, skydd mot skadlig kod, sårbarhets sökning, korrigeringar och konfigurations hantering.

Lösnings acceleratorerna drar nytta av den säkerhet och sekretess som är inbyggd i Azure-plattformen tillsammans med SDL-och OSA-processerna för säker utveckling och drift av all Microsoft-programvara. Dessa procedurer ger infrastruktur skydd, nätverks skydd och identitets-och hanterings funktioner som är grundläggande för säkerheten för alla lösningar.

[Azure-IoT Hub](../articles/iot-hub/about-iot-hub.md) i [IoT Solution Accelerators](../articles/iot-fundamentals/iot-introduction.md) erbjuder en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan IoT-enheter och Azure-tjänster, till exempel [Azure Machine Learning](../articles/machine-learning/classic/index.yml) och [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) med hjälp av säkerhets referenser för varje enhet och åtkomst kontroll.

Om du vill kommunicera med de säkerhets-och sekretessfunktioner som är inbyggda i Azure IoT Solution-acceleratorerna, delar vi upp sviten i de tre primära säkerhets områdena.

![Azure IoT-lösningsacceleratorer](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Skydda enhets etablering och autentisering

Solution Accelerators säkrar enheter när de är i fältet genom att tillhandahålla en unik identitets nyckel för varje enhet som kan användas av IoT-infrastrukturen för att kommunicera med enheten medan den är i drift. Processen är snabb och enkel att konfigurera. Den genererade nyckeln med ett användardefinierat enhets-ID utgör grunden för en token som används i all kommunikation mellan enheten och Azure-IoT Hub.

Enhets-ID: n kan associeras med en enhet under tillverkningen (det är, blinkar i en modul för maskin varu förtroende) eller kan använda en befintlig fast identitet som proxy (till exempel processor serie nummer). Eftersom det inte är enkelt att ändra den här identifierings informationen i enheten, är det viktigt att introducera logiska enhets-ID: n om den underliggande enhetens maskin varu ändringar, men den logiska enheten är oförändrad. I vissa fall kan associationen av en enhets identitet ske vid distributions tiden för enheten (till exempel att en autentiserad fält tekniker konfigurerar en ny enhet samtidigt som den kommunicerar med lösningens Server del). [Azure IoT Hub Identity-registret](../articles/iot-hub/iot-hub-devguide.md) ger säker lagring av enhets identiteter och säkerhets nycklar för en lösning. Enskilda eller grupper av enhets identiteter kan läggas till i en lista över tillåtna eller blockerade enheter, vilket möjliggör fullständig kontroll över enhets åtkomsten.

Azure IoT Hub åtkomst kontroll principer i molnet aktivera aktivering och inaktive ring av alla enhets identiteter, vilket ger ett sätt att avassociera en enhet från en IoT-distribution vid behov. Den här associationen och disassociationen av enheterna baseras på varje enhets identitet.

Ytterligare funktioner för enhets säkerhet är:

* Enheter accepterar inte oönskade nätverks anslutningar. De upprättar alla anslutningar och vägar i en utgående miljö. För att en enhet ska kunna ta emot ett kommando från Server delen måste enheten initiera en anslutning för att söka efter väntande kommandon som ska bearbetas. När en anslutning mellan enheten och IoT Hub har etablerats säkert, kan meddelanden från molnet till enheten och enheten till molnet skickas transparent.

* Enheter ansluter bara till eller upprättar vägar till välkända tjänster som de är peer-kopplade till, till exempel ett Azure-IoT Hub.

* Auktorisering och autentisering på system nivå använder identiteter per enhet, vilket ger åtkomst till autentiseringsuppgifter och behörigheter i nära revocable.

### <a name="secure-connectivity"></a>Säker anslutning

Hållbarheten hos meddelanden är en viktig funktion i alla IoT-lösningar. Behovet av att varaktigt leverera kommandon och/eller ta emot data från enheter är understrukna i det faktum att IoT-enheter är anslutna via Internet eller andra liknande nätverk som kan vara otillförlitliga. Azure IoT Hub erbjuder tålighet för meddelanden mellan molnet och enheterna genom ett system med bekräftelser som svar på meddelanden. Ytterligare hållbarhet för meddelanden uppnås genom att cachelagra meddelanden i IoT Hub i upp till sju dagar för telemetri och två dagar för kommandon.

Effektiviteten är viktigt för att säkerställa bevarande av resurser och åtgärder i en resurs begränsad miljö. HTTPS (HTTP Secure), den säkraste versionen av det populära http-protokollet som stöds av Azure IoT Hub, vilket möjliggör effektiv kommunikation. Advanced Message Queueing Protocol (AMQP) och Message Queuing telemetri transport (MQTT), som stöds av Azure IoT Hub, är utformade för att inte bara vara effektiva när det gäller resursanvändning men även tillförlitlig meddelande leverans. 

Skalbarheten kräver möjlighet att samar beta med en mängd olika enheter. Azure IoT Hub ger säker anslutning till både IP-aktiverade och icke-IP-aktiverade enheter. IP-aktiverade enheter kan ansluta direkt och kommunicera med IoT Hub via en säker anslutning. Enheter som inte är IP-aktiverade är resurs-begränsade och ansluter bara över kort distans kommunikations protokoll, till exempel zwave, ZigBee och Bluetooth. En fält-Gateway används för att aggregera enheterna och utför protokoll översättning för att möjliggöra säker dubbelriktad kommunikation med molnet.

Ytterligare funktioner för anslutnings säkerhet är:

* Kommunikations vägen mellan enheter och Azure-IoT Hub, eller mellan gatewayer och Azure IoT Hub, skyddas med hjälp av branschstandardiserade Transport Layer Security (TLS) med Azure IoT Hub autentiserad med X. 509-protokollet.

* För att skydda enheter från oönskade inkommande anslutningar öppnar inte Azure IoT Hub någon anslutning till enheten. Enheten initierar alla anslutningar.

* Azure IoT Hub varaktigt lagrar meddelanden för enheter och väntar på att enheten ska ansluta. Dessa kommandon lagras i två dagar, vilket gör det möjligt för enheter att ansluta sporadiskt på grund av strömförsörjnings-eller anslutnings problem, för att ta emot dessa kommandon. Azure IoT Hub underhåller en kö per enhet för varje enhet.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Säker bearbetning och lagring i molnet

Med hjälp av krypterad kommunikation för att bearbeta data i molnet hjälper Solution Accelerators att skydda data. Det ger flexibilitet att implementera ytterligare kryptering och hantering av säkerhets nycklar.

Med hjälp av Azure Active Directory (AAD) för användarautentisering och auktorisering kan Azure IoT Solution Accelerators tillhandahålla en principbaserad auktoriseringskod för data i molnet, vilket möjliggör enkel åtkomst hantering som kan granskas och granskas. Den här modellen möjliggör också omedelbar åter kallelse av åtkomst till data i molnet, och enheter som är anslutna till Azure IoT Solution-acceleratorer.

När data finns i molnet kan de bearbetas och lagras i ett användardefinierat arbets flöde. Åtkomst till varje del av data kontrol leras med Azure Active Directory, beroende på vilken lagrings tjänst som används.

Alla nycklar som används av IoT-infrastrukturen lagras i molnet i säkert lagrings utrymme, med möjlighet att gå över i fall då nycklar måste reserveras. Data kan lagras i [Azure Cosmos DB](../articles/cosmos-db/introduction.md) eller i [SQL Database](../articles/azure-sql/database/sql-database-paas-overview.md), vilket gör det möjligt att definiera vilken säkerhets nivå som önskas. Dessutom ger Azure ett sätt att övervaka och granska all åtkomst till dina data för att varna dig om intrång eller obehörig åtkomst.

## <a name="conclusion"></a>Slutsats

Sakernas Internet börjar med dina saker – de saker som är viktiga för företag. IoT kan leverera fantastisk värde till ett företag genom att minska kostnaderna, öka intäkterna och omvandla verksamheten. Den här transformeringen är stor beroende av att du väljer rätt IoT-programvara och tjänst leverantör. Det innebär att du hittar en provider som inte bara catalyzes den här omvandlingen genom att förstå behoven och kraven i företaget, utan även tillhandahålla tjänster och program vara som skapats med säkerhet, sekretess, transparens och efterlevnad som viktiga design överväganden. Microsoft har omfattande erfarenhet av att utveckla och distribuera säkra program och tjänster och fortsätter att bli ledande i denna nya ålder av Sakernas Internet.

Lösnings acceleratorerna bygger på säkerhets åtgärder enligt design, vilket möjliggör säker övervakning av till gångar för att förbättra effektiviteten, driva drifts prestanda för att möjliggöra innovation och använda avancerad data analys för att omvandla företag. Med det skiktade tillvägagångs sättet för säkerhet, flera säkerhetsfunktioner och design mönster kan du använda lösnings acceleratorer för att distribuera en infrastruktur som kan användas för att transformera alla företag.

## <a name="additional-information"></a>Ytterligare information

Varje lösnings Accelerator skapar instanser av Azure-tjänster, till exempel:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): din gateway som ansluter molnet till enheter. Du kan skala till miljon tals anslutningar per hubb och bearbeta enorma data volymer med stöd för autentisering per enhet, vilket hjälper dig att skydda din lösning.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): en skalbar, fullständigt indexerad databas tjänst för halv strukturerade data som hanterar metadata för de enheter som du etablerar, till exempel attribut, konfiguration och säkerhets egenskaper. Azure Cosmos DB erbjuder data behandling med höga prestanda och hög genom strömning, schema-oberoende indexering av data och ett omfattande SQL-frågeuttryck.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): strömnings bearbetning i real tid i molnet som hjälper dig att snabbt utveckla och distribuera en analys lösning med låg kostnad för att få insikter i real tid från enheter, sensorer, infrastruktur och program. Data från den här fullständigt hanterade tjänsten kan skalas till vilken volym som helst samtidigt som stora data flöden, låga svars tider och återhämtning uppnås.

* [**Azure App tjänster**](https://azure.microsoft.com/services/app-service/): en moln plattform för att bygga kraftfulla webb-och mobilappar som ansluter till data överallt. i molnet eller lokalt. Utveckla intressanta mobilappar för iOS, Android och Windows. Integrera med din program vara som en tjänst (SaaS) och företags program med direkt anslutning till dussin tals molnbaserade tjänster och företags program. Koda på ditt favorit språk och IDE – .NET, Node.js, PHP, python eller Java – för att bygga webbappar och API: er snabbare än någonsin.

* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): funktionen Logic Apps i Azure App Service hjälper dig att integrera IoT-lösningen till dina befintliga affärs system och automatisera arbets flödes processer. Logic Apps gör det möjligt för utvecklare att utforma arbets flöden som startar från en utlösare och sedan köra en serie steg – regler och åtgärder som använder kraftfulla kopplingar för att integrera med dina affärs processer. Logic Apps erbjuder direkt anslutning till ett omfattande eko system med SaaS, molnbaserade och lokala program.

* [**Azure Blob Storage**](https://azure.microsoft.com/services/storage/): tillförlitlig, ekonomisk moln lagring för de data som dina enheter skickar till molnet.