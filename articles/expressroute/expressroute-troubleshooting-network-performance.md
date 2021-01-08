---
title: 'Felsöka prestanda för nätverks länkar: Azure'
description: Den här sidan innehåller en standardiserad metod för att testa prestanda för Azures nätverks länkar.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 35e080e0fe45c18ad6a6d5392e0c78b116853c3e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027476"
---
# <a name="troubleshooting-network-performance"></a>Felsöka nätverks prestanda
## <a name="overview"></a>Översikt
Azure tillhandahåller ett stabilt och snabbt sätt att ansluta från ditt lokala nätverk till Azure. Små och stora kunder använder metoder som plats-till-plats-VPN och ExpressRoute för att driva sin verksamhet i Azure. Men vad händer om prestandan inte passar din förväntade eller tidigare upplevelse? Den här artikeln kan hjälpa dig att standardisera hur du testar och bas linjerna i din speciella miljö.

Du får lära dig att snabbt och konsekvent testa nätverks fördröjningen och bandbredden mellan två värdar. Du får också en del råd om hur du kan titta på Azure-nätverket för att isolera problem punkter. PowerShell-skriptet och de verktyg som beskrivs kräver två värdar i nätverket (antingen i slutet av länken som testas). En värd måste vara en Windows-Server eller ett skriv bord, men den andra kan vara antingen Windows eller Linux. 

>[!NOTE]
>Metoden för fel sökning, verktyg och metoder som används är personliga inställningar. Det här dokumentet beskriver den metod och de verktyg som jag ofta tar. Din metod skiljer sig förmodligen, det är något fel med olika metoder för att lösa problemet. Men om du inte har ett etablerat tillvägagångs sätt kan du komma igång med det här dokumentet för att skapa egna metoder, verktyg och inställningar för fel sökning av nätverks problem.
>
>

## <a name="network-components"></a>Nätverkskomponenter
Innan du utforska till fel sökning ska vi diskutera några vanliga villkor och komponenter. Den här diskussionen ser till att vi tänker på varje komponent i slut punkt till slut punkt som möjliggör anslutning i Azure.
![1][1]

På den högsta nivån beskriver jag tre huvudsakliga nätverks Dirigerings domäner.

- Azure-nätverket (blått moln till höger)
- Internet eller WAN (grönt moln i mitten)
- Företags nätverket (persiko molnet till vänster)

Vi tittar på diagrammet från höger till vänster och diskuterar varje komponent:
 - **Virtuell dator** – servern kan ha flera nätverkskort. Se till att alla statiska vägar, standard vägar och operativ system inställningar skickar och tar emot trafik på det sätt som du tycker att det är. Dessutom har varje VM-SKU en bandbredds begränsning. Om du använder en mindre VM-SKU begränsas trafiken av den bandbredd som är tillgänglig för NÄTVERKSKORTet. Jag använder vanligt vis en DS5v2 för testning (och tar sedan bort när du har utfört testet för att spara pengar) för att säkerställa tillräcklig bandbredd på den virtuella datorn.
 - **NIC** – se till att du känner till den privata IP-adress som är tilldelad nätverkskortet i fråga.
 - **NIC-NSG** – det kan finnas en speciell NSG: er som tillämpas på nätverkskorts nivån, se till att NSG regel uppsättning är lämplig för den trafik som du försöker skicka. Se till exempel att portarna 5201 för iPerf, 3389 för RDP eller 22 för SSH är öppna för att tillåta att test trafik skickas.
 - **VNet-undernät** – nätverkskortet är tilldelat ett särskilt undernät, se till att du vet vilket och vilka regler som är kopplade till det under nätet.
 - **Under nätet NSG** – precis som nätverkskortet kan NSG: er även tillämpas på under nätet. Se till att regel uppsättningen NSG är lämplig för den trafik som du försöker skicka. (För inkommande trafik till NÄTVERKSKORTet gäller under nätet NSG först, sedan NIC-NSG. När trafik tas utgående från den virtuella datorn, gäller NIC-NSG först och sedan används under nätet NSG).
 - **Under NÄTS UDR** – User-Defined vägar kan dirigera trafik till ett mellanliggande hopp (t. ex. en brand vägg eller belastningsutjämnare). Se till att du vet om det finns en UDR för din trafik. I så fall, förstå var den går och vad som händer i nästa hopp till din trafik. En brand vägg kan till exempel överföra viss trafik och neka annan trafik mellan samma två värdar.
 - **Gateway-undernät/NSG/UDR** – precis som det virtuella dator under nätet kan gateway-undernätet ha NSG: er och UDR. Se till att du vet om de är där och vilka effekter de har på din trafik.
 - **VNet Gateway (ExpressRoute)** – när peering (ExpressRoute) eller VPN har Aktiver ATS finns det inte många inställningar som kan påverka hur eller om trafik vägar. Om du har en VNet-Gateway ansluten till flera ExpressRoute-kretsar eller VPN-tunnlar bör du vara medveten om inställningarna för anslutningens vikt. Anslutnings vikten påverkar anslutnings inställningarna och avgör vilken väg trafiken tar.
 - **Flödes filter** (visas inte) – ett flödes filter krävs vid användning av Microsoft-peering via ExpressRoute. Om du inte får några vägar kontrollerar du om Route-filtret är konfigurerat och appliceras korrekt på kretsen.

Nu är du på WAN-delen av länken. Den här routningsdomänen kan vara din tjänst leverantör, ditt företags WAN eller Internet. Det finns många hopp, enheter och företag som ingår i dessa länkar, vilket kan göra det svårt att felsöka. Du måste först utesluta både Azure och företagets nätverk innan du kan undersöka hoppen mellan.

I det föregående diagrammet är företagets nätverk längst till vänster. Beroende på företagets storlek kan den här routningsdomänen vara ett fåtal nätverks enheter mellan dig och WAN eller flera lager enheter i ett Campus/Enterprise-nätverk.

Med tanke på komplexiteten hos de tre olika nätverks miljöerna på hög nivå. Det är ofta optimalt att börja på kanterna och försöka visa var prestanda är bra och var de degraderas. Den här metoden kan hjälpa dig att identifiera problemet som vidarebefordrar domänen för de tre. Sedan kan du fokusera på din fel sökning i den aktuella miljön.

## <a name="tools"></a>Verktyg
De flesta nätverks problem kan analyseras och isoleras med hjälp av grundläggande verktyg som ping och traceroute. Det är sällsynt att du behöver gå lika djupt som en paket analys med hjälp av verktyg som Wireshark. 

För att under lätta fel sökningen utvecklades Azure Connectivity Toolkit (AzureCT) för att publicera några av dessa verktyg i ett enkelt paket. För prestanda testning kan verktyg som iPerf och PSPing ge dig information om ditt nätverk. iPerf är ett vanligt verktyg för grundläggande prestanda test och är ganska enkelt att använda. PSPing är ett ping-verktyg som utvecklats av SysInternals. PSPing kan utföra både ICMP-och TCP-pingar för att komma åt en fjärrvärd. Båda dessa verktyg är lätta att "installeras" genom att bara kopiera filerna till en katalog på värden.

Dessa verktyg och metoder är inkapslade i en PowerShell-modul (AzureCT) som du kan installera och använda.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – Azure Connectivity Toolkit
AzureCT PowerShell-modulen har två komponenter [tillgänglighets testning][Availability Doc] och [prestandatest][Performance Doc]. Det här dokumentet är bara bekymrat med prestanda testning, så att du kan fokusera på de två länk prestanda kommandona i den här PowerShell-modulen.

Det finns tre grundläggande steg för att använda den här verktygs uppsättningen för prestanda testning. 1) installera PowerShell-modulen, 2) installera de stödda programmen iPerf och PSPing 3) kör prestanda testet.

1. Installera PowerShell-modulen

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Det här kommandot laddar ned PowerShell-modulen och installerar den lokalt.

2. Installera de stödda programmen
    ```powershell
    Install-LinkPerformance
    ```
    Detta AzureCT-kommando installerar iPerf och PSPing i en ny katalog "C:\ACTTools", och öppnar även portarna för Windows-brandväggen för att tillåta ICMP-och port 5201-trafik (iPerf).

3. Kör prestanda testet

    Först måste du installera och köra iPerf i server läge på den fjärranslutna värden. Se också till att fjärrvärden lyssnar på antingen 3389 (RDP för Windows) eller 22 (SSH för Linux) och tillåter trafik på port 5201 för iPerf. Om den fjärranslutna värden är Windows installerar du AzureCT och kör kommandot Install-LinkPerformance. Kommandot konfigurerar iPerf och de brand Väggs regler som krävs för att starta iPerf i server läge. 
    
    När fjärrdatorn är klar öppnar du PowerShell på den lokala datorn och startar testet:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Det här kommandot kör en serie av samtidiga belastnings-och svars tider för att beräkna bandbredds kapaciteten och fördröjningen hos din nätverks länk.

4. Granska utdata från testerna

    Formatet för PowerShell-utdata ser ut ungefär så här:

    ![4][4]

    De detaljerade resultaten av alla iPerf-och PSPing-testerna finns i enskilda textfiler i katalogen AzureCT tools på "C:\ACTTools."

## <a name="troubleshooting"></a>Felsökning
Om prestanda testet inte ger dig förväntade resultat ska du räkna ut varför bör vara en stegvis steg-för-steg-process. Med hänsyn till antalet komponenter i sökvägen ger en systematisk metod en snabbare väg till lösning än att hoppa runt. Genom att felsöka systematiskt kan du förhindra onödig testning flera gånger.

>[!NOTE]
>Scenariot här är ett prestanda problem, inte ett anslutnings problem. Stegen skulle vara olika om trafiken inte överfördes alls.
>
>

Börja med att utmana dina antaganden. Är ditt förväntat omdöme? Om du till exempel har en 1 Gbit/s ExpressRoute-krets och 100 ms av svars tiden. Vi rekommenderar inte att du förväntar sig att den fullständiga 1 Gbit/s trafik har fått prestanda egenskaperna för TCP över högt latens-länkar. I [avsnittet referenser](#references) finns mer information om prestanda antaganden.

Sedan rekommenderar jag att du börjar på kanterna mellan routningsdomäner och försöker att isolera problemet till en enda större routningsdomän. Du kan starta i företags nätverket, WAN-nätverket eller Azure-nätverket. Personer klandra ofta "svart box" i sökvägen. Även om det är lätt att Blaming den svarta rutan är det möjligt att få en större fördröjning. Särskilt om problemet finns i ett utrymme som du kan göra ändringar för att åtgärda problemet. Kontrol lera att du har noggrannhet innan du lämnar till din tjänst leverantör eller Internet leverantör.

När du har identifierat den huvudsakliga routningsdomänen som verkar innehålla problemet bör du skapa ett diagram över det aktuella avsnittet. Genom att rita ett diagram på en whiteboard, anteckningar eller Visio kan du använda och isolera problemet på ett metodiskt och särskiljande problem. Du kan planera test punkter och uppdatera kartan när du rensar områden eller går djupare när Testningen fortskrider.

Nu när du har ett diagram börjar du med att dela upp nätverket i segment och minska problemet. Ta reda på var det fungerar och var det inte är det. Fortsätt att flytta dina test punkter för att isolera ned till den felande komponenten.

Glöm inte heller att titta på andra lager i OSI-modellen. Det är enkelt att fokusera på nätverket och skikt 1-3 (fysiska, data och nätverks lager), men problemen kan också vara upp på nivå 7 i program lagret. Behåll ett öppet sinne och verifiera antaganden.

## <a name="advanced-expressroute-troubleshooting"></a>Avancerad ExpressRoute-felsökning
Om du inte är säker på var molnet faktiskt är, kan det vara en utmaning att isolera Azure-komponenterna. När ExpressRoute används är gränsen en nätverks komponent som kallas Microsoft Enterprise Edge (MSEE: N). **När du använder ExpressRoute** är msee: n den första kontakt punkten i Microsofts nätverk och det senaste hoppet när du lämnar Microsoft-nätverket. När du skapar ett anslutnings objekt mellan din VNet-gateway och ExpressRoute-kretsen ansluter du faktiskt en anslutning till MSEE: N. Att identifiera MSEE: N som det första eller sista hoppet beroende på vilken riktning som trafiken är avgörande för att isolera ett problem med Azure-nätverket. Veta vilken riktning som ska bevisa om problemet är i Azure eller längre fram i WAN-eller företags nätverket. 

![2][2]

>[!NOTE]
> Observera att MSEE: N inte finns i Azure-molnet. ExpressRoute är faktiskt i kanten av Microsoft-nätverket som inte faktiskt finns i Azure. När du är ansluten till ExpressRoute till en MSEE: N är du ansluten till Microsofts nätverk, där du kan gå till någon av moln tjänsterna, t. ex. Microsoft 365 (med Microsoft-peering) eller Azure (med privat och/eller Microsoft-peering).
>
>

Om två virtuella nätverk är anslutna till **samma** ExpressRoute-krets kan du göra en serie tester för att isolera problemet i Azure.
 
### <a name="test-plan"></a>Test plan
1. Kör Get-LinkPerformance-testet mellan VM1 och VM2. Det här testet ger inblick i om problemet är lokalt eller inte. Om det här testet ger godtagbar svars tid och bandbredds resultat kan du markera det lokala VNet-nätverket som bra.
2. Under förutsättning att den lokala VNet-trafiken är felfri, kör Get-LinkPerformance-testet mellan VM1 och VM3. Det här testet utnyttjar anslutningen via Microsoft-nätverket till MSEE: N och tillbaka till Azure. Om det här testet ger godtagbar svars tid och bandbredds resultat kan du markera Azure-nätverket som bra.
3. Om Azure har reglerat ut kan du utföra en liknande sekvens med tester i företags nätverket. Om det också testar, är det dags att arbeta med din tjänst leverantör eller ISP för att diagnostisera WAN-anslutningen. Exempel: kör det här testet mellan två avdelnings kontor eller mellan ditt skriv bord och en data Center Server. Beroende på vad du testar kan du hitta slut punkter som servrar och klient datorer som kan utnyttja den sökvägen.

>[!IMPORTANT]
> Det är viktigt att för varje test markerar du den tid på dagen då du kör testet och registrerar resultatet på en gemensam plats (till exempel OneNote eller Excel). Varje testkörning bör ha identiska utdata så att du kan jämföra resultat data över test körningar och inte ha "hål" i data. Konsekvens över flera tester är den primära anledningen till att jag använder AzureCT för fel sökning. Magic är inte i de exakta belastnings scenarier som jag kör, men *i stället är det* faktum att jag får ett *konsekvent test och data utdata* från varje test. Att registrera tiden och få konsekventa data varje gång är det särskilt användbart om du senare upptäcker att problemet är sporadiskt. Var noggrann med data insamlingen och Undvik att du behöver testa om samma scenarier (jag har lärt dig hur många år sedan).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problemet är isolerat, nu vad?
Ju mer du isolerar problemet, desto snabbare kan lösningen hittas. Någon gång når du en punkt där du inte kan gå vidare med fel sökningen. Du kan till exempel se länken över din tjänst leverantör som tar hopp via Europa, men du förväntar dig att sökvägen förblir i Asien. I det här läget bör du delta någon för hjälp. Vem du ställer är beroende av routningsdomänen som du isolerar problemet till. Om du kan begränsa det till en specifik komponent som skulle vara ännu bättre.

För problem med företags nätverk kan din interna IT-avdelning eller tjänst leverantör hjälpa till med enhets konfiguration eller maskin varu reparation.

För WAN-nätverket kan du dela dina test resultat med din tjänst leverantör eller Internet leverantören för att komma igång. Detta innebär också att du inte behöver duplicera samma arbete som du redan har gjort. Är inte överträtta om de vill verifiera resultatet själva. "Förtroende men verifiera" är ett korrekt motto vid fel sökning baserat på andra personers rapporterade resultat.

När du har isolerat problemet i så mycket information som du kan med Azure, är det dags att granska [Azures nätverks dokumentation][Network Docs] och om du fortfarande behöver [öppna ett support ärende][Ticket Link].

## <a name="references"></a>Referenser
### <a name="latencybandwidth-expectations"></a>Förväntningar på fördröjning/bandbredd
>[!TIP]
> Geografisk latens (miles eller kilo meter) mellan de slut punkter som du testar är den största delen av svars tiden. Även om det finns utrustnings latens (fysiska och virtuella komponenter, antal hopp osv.), har geografin visat sig vara den största delen av den övergripande svars tiden vid hantering av WAN-anslutningar. Det är också viktigt att Observera att avståndet är avståndet för fiber körningen, inte det raka linje-eller väg kart avståndet. Det här avståndet är otroligt hårt för att få en noggrannhet. Därför använder jag vanligt vis en distans kalkylator på Internet och vet att den här metoden är ett felaktigt mått, men det räcker med att ange en allmän förväntad åtgärd.
>
>

Jag har en ExpressRoute-installation i Seattle, Washington i USA. I följande tabell visas den svars tid och bandbredd som jag såg för att testa på olika Azure-platser. Jag har beräknat det geografiska avståndet mellan varje tests slut.

Test konfiguration:
 - En fysisk server som kör Windows Server 2016 med 10 Gbit/s NIC ansluten till en ExpressRoute-krets.
 - En 10Gbps Premium ExpressRoute-krets på den plats som identifieras med privat peering aktive rad.
 - Ett Azure VNet med en UltraPerformance-gateway i den angivna regionen.
 - En virtuell DS5v2-dator som kör Windows Server 2016 på VNet. Den virtuella datorn var icke-domänansluten, byggd från standard Azure-avbildningen (ingen optimering eller anpassning) med AzureCT installerad.
 - Alla tester använder kommandot AzureCT Get-LinkPerformance med ett belastnings test på 5 minuter för var och en av de sex test körningarna. Exempel:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Data flödet för varje test hade belastningen flödar från den lokala fysiska servern (iPerf-klienten i Seattle) upp till den virtuella Azure-datorn (iPerf-server i den Azure-region som visas).
 - Kolumn data för "svars tid" är från ingen belastnings test (ett TCP-svars svar utan iPerf körs).
 - Kolumn data för "max bandbredd" hämtas från belastnings testet på 16 TCP-flöde med en storlek på 1 MB.

![3][3]

### <a name="latencybandwidth-results"></a>Svars tid/bandbredds resultat
>[!IMPORTANT]
> Dessa tal är endast för allmän referens. Många faktorer påverkar svars tiden och även om dessa värden vanligt vis är konsekventa över tid, kan villkor i Azure eller tjänst leverantörs nätverket skicka trafik via olika sökvägar när som helst, så att svars tiden och bandbredden kan påverkas. Effekterna av dessa ändringar resulterar i allmänhet inte i betydande skillnader.
>
>

| ExpressRoute<br/>Plats|Azure<br/>Region | Ungefärlig<br/>Avstånd (km) | Svarstid|1 session<br/>Bandbredd | Maximal<br/>Bandbredd |
| ------------------------------------------ | --------------------------- |  - | - | - | - |
| Seattle | USA, västra 2        |    191 km |   5 MS | 262,0 Mbit per sekund |  3,74 Gbits per sekund |
| Seattle | USA, västra          |  1 094 km |  18 MS |  82,3 Mbit per sekund |  3,70 Gbits per sekund |
| Seattle | Central US       |  2 357 km |  40 MS |  38,8 Mbit per sekund |  2,55 Gbits per sekund |
| Seattle | USA, södra centrala |  2 877 km |  51 MS |  30,6 Mbit per sekund |  2,49 Gbits per sekund |
| Seattle | USA, norra centrala |  2 792 km |  55 MS |  27,7 Mbit per sekund |  2,19 Gbits per sekund |
| Seattle | USA, östra 2        |  3 769 km |  73 MS |  21,3 Mbit per sekund |  1,79 Gbits per sekund |
| Seattle | East US          |  3 699 km |  74 MS |  21,1 Mbit per sekund |  1,78 Gbits per sekund |
| Seattle | Japan, östra       |  7 705 km | 106 MS |  14,6 Mbit per sekund |  1,22 Gbits per sekund |
| Seattle | Storbritannien, södra         |  7 708 km | 146 MS |  10,6 Mbit per sekund |   896 Mbit per sekund |
| Seattle | Europa, västra      |  7 834 km | 153 MS |  10,2 Mbit per sekund |   761 Mbit per sekund |
| Seattle | Australien, östra   | 12 484 km | 165 MS |   9,4 Mbit per sekund |   794 Mbit per sekund |
| Seattle | Sydostasien   | 12 989 km | 170 MS |   9,2 Mbit per sekund |   756 Mbit per sekund |
| Seattle | Brasilien, södra *   | 10 930 km | 189 MS |   8,2 Mbit per sekund |   699 Mbit per sekund |
| Seattle | Indien, södra      | 12 918 km | 202 MS |   7,7 Mbit per sekund |   634 Mbit per sekund |

\* Svars tiden för Brasilien är ett användbart exempel där det linjära avståndet skiljer sig avsevärt från det fiber kör avståndet. Den förväntade svars tiden skulle ligga i 160 MS, men är i själva verket 189 MS. Skillnaden i svars tiden skulle visa ett nätverks problem någonstans. Men verkligheten är den fiber linje som inte går till Brasilien i en rak linje. Därför bör du förvänta dig en extra 1 000 km eller så att du kan komma till Brasilien från Seattle.

## <a name="next-steps"></a>Nästa steg
1. Hämta Azure Connectivity Toolkit från GitHub på [https://aka.ms/AzCT][ACT]
2. Följ anvisningarna för [testning av länk prestanda][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure nätverks komponenter"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute-felsökning"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Prestanda test miljö"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell-utdata"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: ../index.yml
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT