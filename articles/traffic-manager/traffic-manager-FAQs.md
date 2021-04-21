---
title: Azure Traffic Manager – Vanliga frågor och svar
description: Den här artikeln innehåller svar på vanliga frågor om Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: 708d63695cbba53578b13d1674b9aa99018bcae4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791129"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager vanliga frågor och svar

## <a name="traffic-manager-basics"></a>Traffic Manager grunderna

### <a name="what-ip-address-does-traffic-manager-use"></a>Vilken IP-adress Traffic Manager använder?

Som förklaras [i Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager fungerar på DNS-nivå. Den skickar DNS-svar för att dirigera klienter till lämplig tjänstslutpunkt. Klienter ansluter sedan direkt till tjänstslutpunkten, inte via Traffic Manager.

Därför ger Traffic Manager ingen slutpunkt eller IP-adress som klienter kan ansluta till. Om du vill ha en statisk IP-adress för din tjänst måste den konfigureras på tjänsten, inte i Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Vilka typer av trafik kan dirigeras med hjälp av Traffic Manager?
Som förklaras [i Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)kan en Traffic Manager slutpunkt vara valfri Internetuppkopplingstjänst som finns i eller utanför Azure. Därför kan Traffic Manager dirigera trafik som kommer från det offentliga Internet till en uppsättning slutpunkter som också är internetriktade. Om du har slutpunkter som finns i ett privat nätverk (till exempel en intern version av [Azure Load Balancer](../load-balancer/components.md#frontend-ip-configurations)) eller har användare som gör DNS-begäranden från sådana interna nätverk kan du inte använda Traffic Manager för att dirigera trafiken.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Stöder Traffic Manager "sticky"-sessioner?

Som förklaras [i Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager fungerar på DNS-nivå. Den använder DNS-svar för att dirigera klienter till lämplig tjänstslutpunkt. Klienter ansluter direkt till tjänstslutpunkten, inte via Traffic Manager. Därför Traffic Manager inte HTTP-trafiken mellan klienten och servern.

Ip-källadressen för DNS-frågan som tas emot av Traffic Manager tillhör den rekursiva DNS-tjänsten, inte klienten. Därför har Traffic Manager inget sätt att spåra enskilda klienter och kan inte implementera "sticky"-sessioner. Den här begränsningen är gemensam för alla DNS-baserade trafikhanteringssystem och är inte specifik för Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Varför visas ett HTTP-fel när jag använder Traffic Manager?

Som förklaras [i Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager fungerar på DNS-nivå. Den använder DNS-svar för att dirigera klienter till lämplig tjänstslutpunkt. Klienter ansluter sedan direkt till tjänstslutpunkten, inte via Traffic Manager. Traffic Manager ser inte HTTP-trafik mellan klient och server. Därför måste alla HTTP-fel som visas komma från ditt program. Alla DNS-lösningssteg är slutförda för att klienten ska kunna ansluta till programmet. Det omfattar alla interaktioner Traffic Manager har i programtrafikflödet.

Ytterligare undersökning bör därför fokusera på programmet.

HTTP-värdhuvudet som skickas från klientens webbläsare är den vanligaste orsaken till problem. Kontrollera att programmet är konfigurerat för att acceptera rätt värdrubrik för det domännamn som du använder. Information om slutpunkter som använder Azure App Service finns i Konfigurera ett anpassat domännamn för en [webbapp](../app-service/configure-domain-traffic-manager.md)i Azure App Service med Traffic Manager .

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Hur påverkas prestandan av att använda Traffic Manager?

Som förklaras [i Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager fungerar på DNS-nivå. Eftersom klienter ansluter till dina tjänstslutpunkter direkt påverkas inte prestanda när Traffic Manager när anslutningen har upprättats.

Eftersom Traffic Manager integreras med program på DNS-nivå kräver det att ytterligare en DNS-sökning infogas i DNS-lösningskedjan. Effekten av att Traffic Manager DNS-upplösningstid är minimal. Traffic Manager använder ett globalt nätverk med namnservrar och [anycast-nätverk](https://en.wikipedia.org/wiki/Anycast) för att säkerställa att DNS-frågor alltid dirigeras till den närmaste tillgängliga namnservern. Dessutom innebär cachelagring av DNS-svar att den ytterligare DNS-svarstiden som uppstår Traffic Manager endast gäller för en bråkdel av sessionerna.

Prestandametoden dirigerar trafik till den närmaste tillgängliga slutpunkten. Nettoresultatet är att den övergripande prestandapåverkan som är associerad med den här metoden bör vara minimal. En ökning av DNS-svarstiden bör förskjutas av lägre nätverksfördröjning till slutpunkten.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Vilka programprotokoll kan jag använda med Traffic Manager?

Som förklaras [i Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager fungerar på DNS-nivå. När DNS-sökning är klar ansluter klienterna direkt till programslutpunkten, inte via Traffic Manager. Anslutningen kan därför använda vilket programprotokoll som helst. Om du väljer TCP som övervakningsprotokoll kan Traffic Manager slutpunktshälsoövervakning göras utan att använda några programprotokoll. Om du väljer att hälsoverifiera med hjälp av ett programprotokoll måste slutpunkten kunna svara på HTTP- eller HTTPS GET-begäranden.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Kan jag använda Traffic Manager med ett "domändomännamn" som är "domänbyte"?

Ja. Information om hur du skapar en aliaspost för ditt domännamnsapex för att referera till en Azure Traffic Manager-profil finns i Konfigurera en [aliaspost](../dns/tutorial-alias-tm.md)för att stödja toppdomännamn med Traffic Manager .

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Tar Traffic Manager klientens undernätsadress vid hantering av DNS-frågor? 

Ja, förutom IP-källadressen för DEN DNS-fråga som den tar emot (vilket vanligtvis är IP-adressen för DNS-matcharen), tar Traffic Manager även hänsyn till klientens undernätsadress när den utför sökning efter routningsmetoder för geografiska områden, prestanda och undernät, om den ingår i frågan av matcharen som gör begäran åt slutanvändaren.  
Mer [specifikt, RFC 7871 –](https://tools.ietf.org/html/rfc7871) Klientundernät i DNS-frågor som tillhandahåller en tilläggsmekanism för [DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) som kan skicka vidare klientens undernätsadress från matchare som stöder det.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Vad är DNS TTL och hur påverkar det mina användare?

När en DNS-fråga hamnar Traffic Manager anger den ett värde i svaret time-to-live (TTL). Det här värdet, vars enhet är i sekunder, anger för DNS-matchare nedströms hur länge svaret ska cachelagras. Även om DNS-matchare inte är garanterade att cachelagra det här resultatet kan de cachelagras så att de kan svara på efterföljande frågor från cachen i stället för att gå till Traffic Manager DNS-servrar. Detta påverkar svaren på följande sätt:

- En högre TTL minskar antalet frågor som hamnar på Traffic Manager DNS-servrarna, vilket kan minska kostnaden för en kund eftersom antalet frågor som besvaras är en fakturerbar användning.
- en högre TTL kan potentiellt minska den tid det tar att göra en DNS-sökning.
- ett högre TTL-värde innebär också att dina data inte återspeglar den senaste hälsoinformationen som Traffic Manager har fått via sina avsökningsagenter.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hur hög eller låg kan jag ange TTL för Traffic Manager svar?

På profilnivå kan du ange att DNS-TTL-värdet ska vara så lågt som 0 sekunder och så högt som 2 147 483 647 sekunder (det maximala intervallet som är kompatibelt med [RFC-1035).](https://www.ietf.org/rfc/rfc1035.txt ) Ett TTL-värde på 0 innebär att underordnade DNS-matchare inte cachelagrar frågesvar och att alla frågor förväntas nå de underordnade DNS-servrarna Traffic Manager för lösning.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hur kan jag förstå mängden frågor som kommer till min profil? 

Ett av de mått som tillhandahålls av Traffic Manager är antalet frågor som besvaras av en profil. Du kan hämta den här informationen på en aggregering på profilnivå eller dela upp den ytterligare för att se mängden frågor där specifika slutpunkter returnerades. Dessutom kan du ställa in aviseringar som meddelar dig om frågesvarsvolymen går över de villkor som du har angett. Mer information finns [Traffic Manager mått och aviseringar](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager Geografisk trafikroutningsmetod

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Vad är några användningsfall där geografisk routning är användbar?

Geografisk routningstyp kan användas i alla scenarier där en Azure-kund behöver särskilja sina användare baserat på geografiska regioner. Om du till exempel använder trafikroutningsmetoden Geografisk kan du ge användare från specifika regioner en annan användarupplevelse än de som kommer från andra regioner. Ett annat exempel är att uppfylla lokala krav på datasuveränitet som kräver att användare från en viss region endast ska betjänas av slutpunkter i den regionen.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hur gör jag för att bestämma om jag ska använda routningsmetoden Prestanda eller Geografisk routningsmetod?

Den största skillnaden mellan dessa två populära routningsmetoder är att ditt primära mål i Prestandaroutningsmetod är att skicka trafik till slutpunkten som kan ge anroparen kortast svarstid, medan det primära målet i Geografisk routning är att framtvinga en geo-stängsel för dina anropare så att du avsiktligt kan dirigera dem till en specifik slutpunkt. Överlappningen sker eftersom det finns en korrelation mellan geografisk närhet och kortare svarstid, även om detta inte alltid är sant. Det kan finnas en slutpunkt i ett annat geografiskt område som kan ge en bättre svarstid för anroparen och i så fall skickar prestandadirigering användaren till den slutpunkten, men geografisk routning skickar dem alltid till den slutpunkt som du har mappat för deras geografiska region. För att göra det tydligare kan du titta på följande exempel – med geografisk routning kan du göra ovanliga mappningar som att skicka all trafik från Asien till slutpunkter i USA och all amerikansk trafik till slutpunkter i Asien. I så fall gör geografisk routning avsiktligt exakt det du har konfigurerat den för att göra och prestandaoptimering är inte något att överväga. 
>[!NOTE]
>Det kan finnas scenarier där du kan behöva funktioner för både prestanda och geografisk routning. För dessa scenarier kan kapslade profiler vara ett bra val. Du kan till exempel konfigurera en överordnad profil med geografisk routning där du skickar all trafik från Nordamerika till en kapslad profil som har slutpunkter i USA och använder prestandadirigering för att skicka trafiken till den bästa slutpunkten i den uppsättningen. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Vilka regioner stöds av en Traffic Manager geografisk routning?

Hierarkin för land/region som används av Traffic Manager finns [här.](traffic-manager-geographic-regions.md) Den här sidan hålls uppdaterad med ändringar, men du kan också programmässigt hämta samma information med hjälp av [Azure Traffic Manager REST API](/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hur avgör Traffic Manager var en användare frågar från?

Traffic Manager tittar på käll-IP-adressen för frågan (detta är troligen en lokal DNS-matchare som gör frågan för användarens räkning) och använder en intern IP-adress till regionskarta för att fastställa platsen. Den här kartan uppdateras kontinuerligt för att ta hänsyn till ändringar i Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Garanteras det att Traffic Manager kan fastställa den exakta geografiska platsen för användaren i varje fall?

Nej, Traffic Manager kan inte garantera att den geografiska region som vi här nedan kommer från IP-källadressen för en DNS-fråga alltid motsvarar användarens plats på grund av följande orsaker:

- Först, enligt beskrivningen i föregående vanliga frågor och svar, är källans IP-adress som vi ser en DNS-matchare som gör uppslag för användarens räkning. Även om DEN geografiska platsen för DNS-matcharen är en bra proxy för användarens geografiska plats kan den också vara olika beroende på DNS-matchartjänstens fotavtryck och den specifika DNS-matchartjänst som en kund har valt att använda. Till exempel kan en kund i Malaysia ange i sina enhetsinställningar använda en DNS-matchartjänst vars DNS-server i Singapore kan väljas för att hantera frågeupplösningarna för den användaren/enheten. I så fall Traffic Manager bara se matcharens IP-adress som motsvarar platsen i Singapore. Se även tidigare vanliga frågor och svar om stöd för klientens undernätsadresser på den här sidan.

- För det Traffic Manager en intern karta för att göra IP-adressen till översättning av geografiska regioner. Den här kartan verifieras och uppdateras kontinuerligt för att öka noggrannheten och ta hänsyn till internets växande natur, men det finns fortfarande en risk att vår information inte är en exakt representation av den geografiska platsen för alla IP-adresser.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Måste en slutpunkt finnas fysiskt i samma region som den som den är konfigurerad med för geografisk routning?

Nej, slutpunktens plats har inga begränsningar för vilka regioner som kan mappas till den. Till exempel kan en slutpunkt i US-Central Azure-regionen få alla användare från Indien dirigerade till den.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Kan jag tilldela geografiska regioner till slutpunkter i en profil som inte har konfigurerats för geografisk routning?

Ja, om routningsmetoden för en profil inte [](/rest/api/trafficmanager/) är geografisk kan du använda Azure Traffic Manager REST API för att tilldela geografiska regioner till slutpunkter i profilen. När det gäller profiler av icke-geografisk routningstyp ignoreras den här konfigurationen. Om du ändrar en sådan profil till geografisk routningstyp vid ett senare tillfälle kan Traffic Manager använda dessa mappningar.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Varför får jag ett felmeddelande när jag försöker ändra routningsmetoden för en befintlig profil till Geografisk?

Alla slutpunkter under en profil med geografisk routning måste ha minst en region mappad till den. Om du vill konvertera en befintlig profil till geografisk routningstyp måste du först associera geografiska regioner till alla dess slutpunkter med hjälp [av Azure Traffic Manager REST API](/rest/api/trafficmanager/) innan du ändrar routningstypen till geografisk. Om du använder portalen tar du först bort slutpunkterna, ändrar routningsmetoden för profilen till geografisk och lägger sedan till slutpunkterna tillsammans med deras mappning av geografiska områden.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Varför rekommenderas det starkt att kunder skapar kapslade profiler i stället för slutpunkter under en profil med geografisk routning aktiverat?

En region kan bara tilldelas till en slutpunkt inom en profil om den använder den geografiska routningsmetoden. Om den slutpunkten inte är en kapslad typ med en underordnad profil kopplad till den, fortsätter Traffic Manager att skicka trafik till den om slutpunkten blir skadad eftersom alternativet att inte skicka någon trafik inte är bättre. Traffic Manager redundans inte till en annan slutpunkt, även om den region som tilldelats är "överordnad" för den region som tilldelats till den slutpunkt som inte är felsäker (till exempel om en slutpunkt som har regionen Spanien inte är felsäker, redundans till en annan slutpunkt som har regionen Europa tilldelats till sig). Detta görs för att säkerställa Traffic Manager respekterar de geografiska gränser som en kund har ställt in i sin profil. För att få fördelen med att redundanta till en annan slutpunkt när en slutpunkt blir skadad rekommenderar vi att geografiska regioner tilldelas till kapslade profiler med flera slutpunkter i stället för enskilda slutpunkter. På så sätt, om en slutpunkt i den kapslade underordnade profilen misslyckas, kan trafiken växla över till en annan slutpunkt i samma kapslade underordnade profil.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Finns det några begränsningar i API-versionen som stöder den här routningstypen?

Ja, endast API-version 2017-03-01 och senare stöder routningstypen Geografisk. Äldre API-versioner kan inte användas för att skapa profiler av geografisk routningstyp eller tilldela geografiska regioner till slutpunkter. Om en äldre API-version används för att hämta profiler från en Azure-prenumeration returneras inte någon profil av geografisk routningstyp. När du använder äldre API-versioner visas inte heller någon tilldelning av geografisk region för alla profiler som returneras med slutpunkter med en geografisk regiontilldelning.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager trafikroutningsmetod för undernät

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Vad är några användningsfall där undernätsroutning är användbart?

Med undernätsroutning kan du särskilja upplevelsen du levererar för specifika uppsättningar av användare som identifieras av käll-IP-adressen för deras IP-adress för DNS-begäranden. Ett exempel är att visa olika innehåll om användarna ansluter till en webbplats från företagets huvudkontor. En annan skulle begränsa användare från vissa Internetleverantörer till att endast få åtkomst till slutpunkter som endast stöder IPv4-anslutningar om dessa Internetleverantörer har prestanda under par när IPv6 används.
Ett annat skäl till att använda routningsmetoden för undernät är tillsammans med andra profiler i en kapslad profiluppsättning. Om du till exempel vill använda geografisk routningsmetod för geoavstängning av dina användare, men för en specifik Internetleverantör som du vill använda en annan routningsmetod kan du ha en profil med routningsmetoden Undernät som överordnad profil och åsidosätta internetleverantören för att använda en specifik underordnad profil och ha en geografisk standardprofil för alla andra.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Hur vet Traffic Manager ip-adressen för slutanvändaren?

Slutanvändarenheterna använder vanligtvis en DNS-matchare för att göra DNS-sökning åt dem. Den utgående IP-adressen för sådana matchare Traffic Manager ser som källans IP-adress. Dessutom försöker undernätsroutningsmetoden också se om det finns information om EDNS0 Extended Client Subnet (ECS) som skickades med begäran. Om ECS-information finns är det den adress som används för att fastställa routningen. I avsaknad av ECS-information används käll-IP-adressen för frågan i routningssyfte.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hur kan jag ange IP-adresser när jag använder undernätsroutning?

IP-adresserna som ska associeras med en slutpunkt kan anges på två sätt. Först kan du använda den fyrdelade decimaltaltals notationen med start- och slutadresser för att ange intervallet (till exempel 1.2.3.4-5.6.7.8 eller 3.4.5.6-3.4.5.6). För det andra kan du använda CIDR-notationen för att ange intervallet (till exempel 1.2.3.0/24). Du kan ange flera intervall och använda båda notationstyperna i en intervalluppsättning. Några begränsningar gäller.

-    Du kan inte överlappa adressintervallen eftersom varje IP-adress måste mappas till endast en enda slutpunkt
-    Startadressen får inte vara mer än slutadressen
-    När det gäller CIDR-notationen ska IP-adressen före "/" vara startadressen för intervallet (till exempel är 1.2.3.0/24 giltig men 1.2.3.4.4/24 är INTE giltigt)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hur kan jag ange en återställningsslutpunkt när jag använder undernätsroutning?

Om du har en slutpunkt utan mappade undernät i en profil med undernätsroutning dirigeras alla förfrågningar som inte matchar med andra slutpunkter till här. Vi rekommenderar starkt att du har en sådan återställningsslutpunkt i din profil eftersom Traffic Manager returnerar ett NXDOMAIN-svar om en begäran kommer in och den inte mappas till några slutpunkter eller om den mappas till en slutpunkt men slutpunkten inte är fel.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Vad händer om en slutpunkt är inaktiverad i en profil av routningstyp för undernät?

Om du har en slutpunkt med som är inaktiverad i en profil med undernätsroutning beter Traffic Manager sig som om slutpunkten och undernätsmappningarna som den inte har finns. Om en fråga som skulle ha matchats med dess IP-adressmappning tas emot och slutpunkten är inaktiverad returnerar Traffic Manager en återställningsslutpunkt (en utan mappningar) eller om en sådan slutpunkt inte finns returnerar ett NXDOMAIN-svar.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager MultiValue-trafikroutningsmetod

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Vad är några användningsfall där MultiValue-routning är användbart?

MultiValue-routning returnerar flera felfria slutpunkter i ett enda frågesvar. Den största fördelen med detta är att om en slutpunkt är skadad har klienten fler alternativ för att försöka igen utan att göra ett annat DNS-anrop (som kan returnera samma värde från en överordnad cache). Detta gäller för tillgänglighetskänsliga program som vill minimera stilleståndstiden.
Ett annat användningssätt för MultiValue-routningsmetoden är om en slutpunkt är "dual-homed" till både IPv4- och IPv6-adresser och du vill ge anroparen båda alternativen att välja mellan när den initierar en anslutning till slutpunkten.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hur många slutpunkter returneras när MultiValue-routning används?

Du kan ange det maximala antalet slutpunkter som ska returneras och MultiValue returnerar inte fler än så många felfria slutpunkter när en fråga tas emot. Det högsta möjliga värdet för den här konfigurationen är 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Får jag samma uppsättning slutpunkter när MultiValue-routning används?

Vi kan inte garantera att samma uppsättning slutpunkter returneras i varje fråga. Detta påverkas också av det faktum att vissa slutpunkter kan bli felaktiga och då tas de inte med i svaret

## <a name="real-user-measurements"></a>Faktisk slutanvändarmätning

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Vilka är fördelarna med att använda Faktisk slutanvändarmätning?

När du använder prestandaroutningsmetoden väljer Traffic Manager den bästa Azure-regionen som slutanvändaren kan ansluta till genom att granska källans IP-adress och EDNS-klientundernät (om det skickas) och kontrollera det mot den information om nätverksfördröjning som tjänsten underhåller. Faktisk slutanvändarmätning förbättrar detta för slutanvändarbasen genom att deras erfarenhet bidrar till den här svarstidstabellen, förutom att se till att den här tabellen omfattar slutanvändarnätverken som slutanvändarna ansluter till Azure från. Detta leder till en ökad noggrannhet i routningen av slutanvändaren.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Kan jag använda Faktisk slutanvändarmätning med icke-Azure-regioner?

Faktisk slutanvändarmätning mått och rapporter om endast svarstiden för att nå Azure-regioner. Om du använder prestandabaserad routning med slutpunkter som finns i icke-Azure-regioner kan du fortfarande dra nytta av den här funktionen genom att ha mer information om svarstider om den representativa Azure-region som du har valt för att associeras med den här slutpunkten.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Vilken routningsmetod har nytta av Faktisk slutanvändarmätning?

Den ytterligare information som fås Faktisk slutanvändarmätning gäller endast för profiler som använder prestandaroutningsmetoden. Länken Faktisk slutanvändarmätning är tillgänglig från alla profiler när du visar den via Azure Portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Måste jag aktivera Faktisk slutanvändarmätning varje profil separat?

Nej, du behöver bara aktivera det en gång per prenumeration och all svarstidsinformation som mäts och rapporteras är tillgänglig för alla profiler.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hur gör jag för att inaktivera Faktisk slutanvändarmätning min prenumeration?

Du kan sluta betala avgifter som rör Faktisk slutanvändarmätning när du slutar samla in och skicka tillbaka svarstidsmått från klientprogrammet. När javaScript-måttet till exempel är inbäddat i webbsidor kan du sluta använda den här funktionen genom att ta bort JavaScript eller genom att inaktivera anropet när sidan återges.

Du kan också stänga av Faktisk slutanvändarmätning genom att ta bort nyckeln. När du tar bort nyckeln tas alla mått som skickas Traffic Manager med den nyckeln bort.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Kan jag använda Faktisk slutanvändarmätning med andra klientprogram än webbsidor?

Ja, Faktisk slutanvändarmätning har utformats för att mata in data som samlas in via olika typer av slutanvändares klienter. Dessa vanliga frågor och svar kommer att uppdateras när nya typer av klientprogram stöds.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hur många mått görs varje gång min Faktisk slutanvändarmätning aktiverade webbsidan återges?

När Faktisk slutanvändarmätning används med javaScript-måttet resulterar varje sidåtergivning i sex mätningar. Dessa rapporteras sedan tillbaka till Traffic Manager tjänsten. Du debiteras för den här funktionen baserat på antalet mätningar som rapporteras till Traffic Manager tjänst. Om användaren till exempel navigerar bort från webbplatsen när måtten tas men innan de rapporterades, beaktas inte dessa mått i faktureringssyfte.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Finns det en fördröjning innan Faktisk slutanvändarmätning skript körs på min webbsida?

Nej, det finns ingen programmerad fördröjning innan skriptet anropas.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Kan jag använda Faktisk slutanvändarmätning med endast de Azure-regioner som jag vill mäta?

Nej, varje gång den anropas mäter Faktisk slutanvändarmätning en uppsättning med sex Azure-regioner som bestäms av tjänsten. Den här uppsättningen ändras mellan olika anrop och när ett stort antal sådana anrop inträffar sträcker sig måtttäckningen över olika Azure-regioner.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Kan jag begränsa antalet mätningar som görs till ett visst tal?

JavaScript-måttet är inbäddat på din webbsida och du har fullständig kontroll över när du ska börja och sluta använda det. Så länge tjänsten Traffic Manager tar emot en begäran om att en lista över Azure-regioner ska mätas returneras en uppsättning regioner.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Kan jag se de mått som tas av mitt klientprogram som en del av Faktisk slutanvändarmätning?

Eftersom måttlogiken körs från klientprogrammet har du full kontroll över vad som händer, inklusive att se svarstidsmåtten. Traffic Manager rapporterar inte en sammanställd vy över de mått som tagits emot under nyckeln som är länkad till din prenumeration.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Kan jag ändra måttskriptet som tillhandahålls av Traffic Manager?

När du har kontroll över vad som är inbäddat på din webbsida avråder vi dig från att göra ändringar i måttskriptet för att säkerställa att det mäter och rapporterar svarstiderna korrekt.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Är det möjligt för andra att se den nyckel som jag använder med Faktisk slutanvändarmätning?

När du bäddar in måttskriptet på en webbsida kan andra se skriptet och din Faktisk slutanvändarmätning (RUM). Men det är viktigt att känna till att den här nyckeln skiljer sig från ditt prenumerations-ID och genereras av Traffic Manager endast ska användas för detta ändamål. Att känna till din RUM-nyckel kommer inte att äventyra säkerheten för ditt Azure-konto.

### <a name="can-others-abuse-my-rum-key"></a>Kan andra missbruka min RUM-nyckel?

Även om det är möjligt för andra att använda din nyckel för att skicka fel information till Azure, ändrar inte några felaktiga mått routningen eftersom den tas med i beräkningen tillsammans med alla andra mått som vi tar emot. Om du behöver ändra dina nycklar kan du generera nyckeln på ny gång då den gamla nyckeln tas bort.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Måste jag placera JavaScript-måttet på alla mina webbsidor?

Faktisk slutanvändarmätning ger mer värde när antalet mått ökar. Med detta sagt är det ditt beslut om du behöver placera det på alla dina webbsidor eller några få. Vår rekommendation är att börja med att placera den på din mest besökta sida där en användare förväntas stanna kvar på sidan fem sekunder eller mer.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Kan information om mina slutanvändare identifieras av Traffic Manager om jag använder Faktisk slutanvändarmätning?

När det angivna JavaScript-måttet används har Traffic Manager insyn i slutanvändarens klient-IP-adress och käll-IP-adressen för den lokala DNS-matchare som de använder. Traffic Manager använder klientens IP-adress först när den har trunkerats för att inte kunna identifiera den specifika slutanvändare som skickade måtten.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Behöver webbsidan som mäter Faktisk slutanvändarmätning använda en Traffic Manager för routning?

Nej, den behöver inte använda Traffic Manager. Routningssidan för Traffic Manager fungerar separat från den verkliga användarmåttdelen och även om det är en bra idé att ha dem båda i samma webbegenskap behöver de inte vara det.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Måste jag ha någon tjänst i Azure-regioner som ska användas med Faktisk slutanvändarmätning?

Nej, du behöver inte vara värd för någon komponent på serversidan i Azure för att Faktisk slutanvändarmätning fungera. Den bild med en bildpunkt som laddas ned av JavaScript-måttet och tjänsten som kör den i olika Azure-regioner hanteras och hanteras av Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Kommer min Azure-bandbredd att öka när jag använder Faktisk slutanvändarmätning?

Som vi nämnde i föregående svar ägs och hanteras komponenterna på Faktisk slutanvändarmätning serversidan av Azure. Det innebär att din Azure-bandbreddsanvändning inte ökar eftersom du använder Faktisk slutanvändarmätning. Detta omfattar inte någon bandbreddsanvändning utanför vad Azure debiterar. Vi minimerar bandbredden som används genom att bara ladda ned en bildpunkt för att mäta svarstiden till en Azure-region. 

## <a name="traffic-view"></a>Trafikvy

### <a name="what-does-traffic-view-do"></a>Vad gör Trafikvy?

Trafikvy är en funktion i Traffic Manager som hjälper dig att förstå mer om dina användare och hur deras upplevelse är. Den använder de frågor som tas emot av Traffic Manager och tabellerna för information om nätverksfördröjning som tjänsten upprätthåller för att ge dig följande:

- De regioner som användarna ansluter till dina slutpunkter i Azure från.
- Mängden användare som ansluter från dessa regioner.
- De Azure-regioner som de dirigeras till.
- Svarstiden för dessa Azure-regioner.

Den här informationen är tillgänglig för dig att använda via geografiskt kartöverlägg och tabellvyer i portalen, förutom att den är tillgänglig som rådata som du kan ladda ned.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hur kan jag dra nytta av att använda Trafikvy?

Trafikvy ger dig en övergripande vy över den trafik som dina Traffic Manager tar emot. I synnerhet kan den användas för att förstå var din användarbas ansluter från och lika viktigt som deras genomsnittliga svarstid. Du kan sedan använda den här informationen för att hitta områden där du behöver fokusera, till exempel genom att utöka ditt Azure-fotavtryck till en region som kan ge användarna kortare svarstider. En annan insikt som du kan härleda från att använda Trafikvy är att se trafikmönster till olika regioner, vilket i sin tur kan hjälpa dig att fatta beslut om att öka eller minska antalet inventerade i dessa regioner.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Hur skiljer sig Trafikvy från de Traffic Manager mått som är tillgängliga via Azure Monitor?

Azure Monitor kan användas för att på en aggregerad nivå förstå trafiken som tas emot av din profil och dess slutpunkter. Du kan också spåra hälsostatusen för slutpunkterna genom att exponera hälsokontrollresultatet. När du behöver gå längre än dessa och förstå slutanvändarens upplevelse av att ansluta till Azure på regional nivå kan du Trafikvy för att uppnå det.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Använder Trafikvy EDNS-klientundernätsinformation?

DNS-frågorna som Azure Traffic Manager tar hänsyn till ECS-information för att öka precisionen för routningen. Men när du skapar datauppsättningen som visar var användarna ansluter från Trafikvy bara IP-adressen för DNS-matcharen.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hur många dagars data Trafikvy använda?

Trafikvy skapar sina utdata genom att bearbeta data från de sju dagarna före dagen innan de visas av dig. Det här är ett glidande fönster och de senaste data kommer att användas varje gång du besöker.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hur hanterar Trafikvy externa slutpunkter?

När du använder externa slutpunkter som finns utanför Azure-regioner i en Traffic Manager-profil kan du välja att mappa den till en Azure-region som är en proxy för dess svarstidsegenskaper (detta behövs i själva verket om du använder prestandaroutningsmetoden). Om azure-regionen mappas så används svarstidsmåtten för Azure-regionen när du skapar Trafikvy utdata. Om ingen Azure-region anges är svarstidsinformationen tom i data för de externa slutpunkterna.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Måste jag aktivera Trafikvy varje profil i min prenumeration?

Under förhandsversionsperioden aktiverades Trafikvy på prenumerationsnivå. Som en del av de förbättringar som vi gjorde före den allmänna tillgängligheten kan du nu aktivera Trafikvy på profilnivå, så att du får mer detaljerad aktivering av den här funktionen. Som standard Trafikvy inaktiveras för en profil.

>[!NOTE]
>Om du Trafikvy på prenumerationsnivå under förhandsversionen måste du nu återaktivera det för var och en av profilerna under prenumerationen.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hur stänger jag av Trafikvy?

Du kan inaktivera Trafikvy alla profiler med hjälp av portalen eller REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Hur fungerar Trafikvy fakturering?

Trafikvy baseras på antalet datapunkter som används för att skapa utdata. Den enda datatypen som stöds för närvarande är de frågor som din profil tar emot. Dessutom debiteras du bara för bearbetningen som gjordes när du har Trafikvy aktiverat. Det innebär att om du aktiverar Trafikvy under en viss tidsperiod i en månad och inaktiverar den under andra tider, räknas endast de datapunkter som bearbetas medan du hade funktionen aktiverad på fakturan.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-slutpunkter

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Kan jag använda Traffic Manager med slutpunkter från flera prenumerationer?

Det går inte att använda slutpunkter från flera prenumerationer med Azure Web Apps. Azure Web Apps kräver att anpassade domännamn som används med Web Apps endast används inom en enda prenumeration. Det går inte att använda Web Apps från flera prenumerationer med samma domännamn.

För andra slutpunktstyper är det möjligt att använda Traffic Manager med slutpunkter från mer än en prenumeration. I Resource Manager kan slutpunkter från alla prenumerationer läggas till i Traffic Manager, så länge den person som konfigurerar Traffic Manager-profilen har läsbehörighet till slutpunkten. Dessa behörigheter kan beviljas med hjälp [av rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../role-based-access-control/role-assignments-portal.md) Slutpunkter från andra prenumerationer kan läggas till med [Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) eller [Azure CLI.](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create)

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Kan jag använda Traffic Manager med Cloud Service Staging-platser?

Ja. Platser för "mellanlagring av molntjänster" kan konfigureras i Traffic Manager som externa slutpunkter. Hälsokontroller debiteras fortfarande enligt priset för Azure-slutpunkter.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Stöder Traffic Manager IPv6-slutpunkter?

Traffic Manager tillhandahåller för närvarande inte IPv6-adresserbara namnservrar. Men Traffic Manager kan fortfarande användas av IPv6-klienter som ansluter till IPv6-slutpunkter. En klient gör inte DNS-begäranden direkt till Traffic Manager. Klienten använder i stället en rekursiv DNS-tjänst. En klient med endast IPv6 skickar begäranden till den rekursiva DNS-tjänsten via IPv6. Den rekursiva tjänsten ska sedan kunna kontakta servern Traffic Manager med hjälp av IPv4.

Traffic Manager svarar med DNS-namnet eller IP-adressen för slutpunkten. Det finns två alternativ för att stödja en IPv6-slutpunkt. Du kan lägga till slutpunkten som ett DNS-namn som har en associerad AAAA-post och Traffic Manager kontrollerar slutpunkten och returnerar den som en CNAME-posttyp i frågesvaret. Du kan också lägga till slutpunkten direkt med IPv6-adressen så Traffic Manager returnerar en AAAA-typpost i frågesvaret.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Kan jag använda Traffic Manager med mer än en webbapp i samma region?

Normalt används Traffic Manager för att dirigera trafik till program som distribueras i olika regioner. Det kan dock också användas om ett program har fler än en distribution i samma region. De Traffic Manager Azure-slutpunkterna tillåter inte att fler än en webbappslutpunkt från samma Azure-region läggs till i samma Traffic Manager profil.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Hur gör jag för att flytta azure-Traffic Manager-profilens Azure-slutpunkter till en annan resursgrupp eller prenumeration?

Azure-slutpunkter som är associerade med Traffic Manager en profil spåras med hjälp av deras resurs-ID: er. När en Azure-resurs som används som en slutpunkt (till exempel offentlig IP, klassisk molntjänst, WebApp eller en annan Traffic Manager-profil som används på ett kapslat sätt) flyttas till en annan resursgrupp eller prenumeration ändras dess resurs-ID. I det här scenariot måste du för närvarande uppdatera Traffic Manager genom att först ta bort och sedan lägga tillbaka slutpunkterna i profilen.

## <a name="traffic-manager-endpoint-monitoring"></a>Slutpunktsövervakning för Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Är Traffic Manager motståndskraftig mot fel i Azure-regioner?

Traffic Manager är en viktig del av leveransen av program med hög tillgängliga i Azure.
För att leverera hög Traffic Manager måste ha en exceptionellt hög tillgänglighet och vara motståndskraftig mot regionala haveri.

De är Traffic Manager motståndskraftiga mot ett fullständigt fel i alla Azure-regioner. Den här motståndskraften gäller för Traffic Manager komponenter: DNS-namnservrarna, API:et, lagringslagret och tjänsten för slutpunktsövervakning.

Om det osannolika skulle ske ett avbrott i en hel Azure-region Traffic Manager förväntas fortsätta att fungera normalt. Program som distribueras i flera Azure-regioner kan använda Traffic Manager dirigera trafik till en tillgänglig instans av programmet.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Hur påverkar valet av resursgruppsplats Traffic Manager?

Traffic Manager är en enda global tjänst. Det är inte regionalt. Valet av resursgruppsplats gör ingen skillnad Traffic Manager distribuerade i den resursgruppen.

Azure Resource Manager kräver att alla resursgrupper anger en plats, vilket avgör standardplatsen för resurser som distribueras i den resursgruppen. När du skapar Traffic Manager en profil skapas den i en resursgrupp. Alla Traffic Manager profiler **använder global** som plats, vilket åsidosätter resursgruppens standard.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hur gör jag för att du det aktuella hälsotillståndet för varje slutpunkt?

Den aktuella övervakningsstatusen för varje slutpunkt, förutom den övergripande profilen, visas i Azure Portal. Den här informationen är också tillgänglig via Traffic Monitor [REST API,](/rest/api/trafficmanager/) [PowerShell-cmdlets](/powershell/module/az.trafficmanager)och [plattformsoberoende Azure CLI.](/cli/azure/install-classic-cli)

Du kan också använda Azure Monitor för att spåra hälsotillståndet för dina slutpunkter och se en visuell representation av dem. Mer information om hur Azure Monitor finns i [azure monitoring-dokumentationen.](../azure-monitor/data-platform.md)

### <a name="can-i-monitor-https-endpoints"></a>Kan jag övervaka HTTPS-slutpunkter?

Ja. Traffic Manager stöder avsökning via HTTPS. Konfigurera **HTTPS** som protokoll i övervakningskonfigurationen.

Traffic Manager kan inte tillhandahålla någon certifikatverifiering, inklusive:

* Certifikat på serversidan verifieras inte
* SNI-certifikat på serversidan verifieras inte
* Klientcertifikat stöds inte

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Använder jag en IP-adress eller ett DNS-namn när jag lägger till en slutpunkt?

Traffic Manager har stöd för att lägga till slutpunkter på tre sätt – som ett DNS-namn, som en IPv4-adress och som en IPv6-adress. Om slutpunkten läggs till som en IPv4- eller IPv6-adress kommer frågesvaret att vara av posttyp A respektive AAAA. Om slutpunkten lades till som ett DNS-namn kommer frågesvaret att vara av posttyp CNAME. Det är endast tillåtet att lägga till slutpunkter som IPv4- eller IPv6-adress om slutpunkten är av typen **Extern**.
Alla routningsmetoder och övervakningsinställningar stöds av de tre slutpunktsadresseringstyperna.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Vilka typer av IP-adresser kan jag använda när jag lägger till en slutpunkt?

Traffic Manager kan du använda IPv4- eller IPv6-adresser för att ange slutpunkter. Det finns några begränsningar som anges nedan:

- Adresser som motsvarar reserverade privata IP-adressutrymmen tillåts inte. Dessa adresser omfattar de som anropas i RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 och RFC 5771
- Adressen får inte innehålla några portnummer (du kan ange vilka portar som ska användas i profilkonfigurationsinställningarna)
- Inga två slutpunkter i samma profil kan ha samma mål-IP-adress

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Kan jag använda olika slutpunktsadresseringstyper i en enda profil?

Nej, Traffic Manager tillåter inte att du blandar slutpunktsadresseringstyper i en profil, förutom för en profil med MultiValue-routningstyp där du kan blanda IPv4- och IPv6-adresseringstyper

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Vad händer när posttypen för en inkommande fråga skiljer sig från posttypen som är associerad med slutpunktens adresseringstyp?

När en fråga tas emot mot en profil Traffic Manager först slutpunkten som måste returneras enligt den angivna routningsmetoden och hälsostatusen för slutpunkterna. Den tittar sedan på den posttyp som begärdes i den inkommande frågan och den posttyp som är associerad med slutpunkten innan ett svar returneras baserat på tabellen nedan.

För profiler med någon annan routningsmetod än MultiValue:

|Inkommande frågebegäran|     Typ av slutpunkt|     Svaret har angetts|
|--|--|--|
|Någon |    A/AAAA/CNAME |    Målslutpunkt| 
|A |    A/CNAME |    Målslutpunkt|
|A |    AAAA |    NODATA |
|AAAA |    AAAA/CNAME |    Målslutpunkt|
|AAAA |    A |    NODATA |
|CNAME |    CNAME |    Målslutpunkt|
|CNAME     |A/AAAA |    NODATA |
|

För profiler med routningsmetod inställt på MultiValue:

|Inkommande frågebegäran|     Typ av slutpunkt |    Svaret har angetts|
|--|--|--|
|Någon |    Blandning av A och AAAA |    Målslutpunkter|
|A |    Blandning av A och AAAA |    Endast målslutpunkter av typ A|
|AAAA    |Blandning av A och AAAA|     Endast målslutpunkter av typen AAAA|
|CNAME |    Blandning av A och AAAA |    NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Kan jag använda en profil med IPv4/IPv6-adresserade slutpunkter i en kapslad profil?

Ja, du kan med undantaget att en profil av typen MultiValue inte kan vara en överordnad profil i en kapslad profiluppsättning.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Jag stoppade en slutpunkt för webbappen i min Traffic Manager profil, men jag får ingen trafik även efter att jag startade om den. Hur kan jag åtgärda detta?

När en slutpunkt för ett Azure-webbprogram stoppas Traffic Manager att kontrollera hälsotillståndet och startar om hälsokontrollerna först när den har upptäckt att slutpunkten har startats om. För att förhindra den här fördröjningen inaktiverar du och återaktiverar sedan slutpunkten i Traffic Manager profilen när du har startat om slutpunkten.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Kan jag använda Traffic Manager även om mitt program inte har stöd för HTTP eller HTTPS?

Ja. Du kan ange TCP som övervakningsprotokoll och Traffic Manager initiera en TCP-anslutning och vänta på ett svar från slutpunkten. Om slutpunkten svarar på anslutningsbegäran med ett svar för att upprätta anslutningen, inom tidsgränsen, markeras slutpunkten som felfri.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Vilka specifika svar krävs från slutpunkten när du använder TCP-övervakning?

När TCP-övervakning används startar Traffic Manager en trevägs TCP-handskakning genom att skicka en SYN-begäran till slutpunkten på den angivna porten. Den väntar sedan på ett SYN-ACK-svar från slutpunkten under en viss tidsperiod (anges i tidsgränsinställningarna).

- Om ett SYN-ACK-svar tas emot inom den tidsgränsperiod som anges i övervakningsinställningarna anses slutpunkten vara felfri. FIN eller FIN-ACK är det förväntade svaret från Traffic Manager när den regelbundet avslutar en socket.
- Om ett SYN-ACK-svar tas emot efter den angivna tidsgränsen svarar Traffic Manager RST för att återställa anslutningen.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Hur snabbt kan Traffic Manager bort mina användare från en felande slutpunkt?

Traffic Manager innehåller flera inställningar som kan hjälpa dig att styra redundansbeteendet för din Traffic Manager profil enligt följande:

- Du kan ange att Traffic Manager avsökningar av slutpunkterna oftare genom att ställa in avsökningsintervallet på 10 sekunder. Detta säkerställer att alla slutpunkter som inte är felhälsosamma kan identifieras så snart som möjligt. 
- du kan ange hur lång tid det tar att vänta innan en hälsokontrollbegäran tar för lång tid (minimivärdet för time out är 5 sek).
- du kan ange hur många fel som kan inträffa innan slutpunkten markeras som skadad. Det här värdet kan vara lågt som 0, vilket innebär att slutpunkten markeras som skadad så fort den första hälsokontrollen misslyckas. Men om du använder minimivärdet 0 för det tolererade antalet fel kan det leda till att slutpunkter tas bort från rotationen på grund av tillfälliga problem som kan uppstå vid avsökning.
- du kan ange time-to-live (TTL) för ATT DNS-svaret ska vara så lågt som 0. Detta innebär att DNS-matchare inte kan cachelagra svaret och att varje ny fråga får ett svar som innehåller den senaste hälsoinformationen som Traffic Manager har.

Med hjälp av de här Traffic Manager kan du tillhandahålla redundans under 10 sekunder efter att en slutpunkt har feltillstånd och en DNS-fråga görs mot motsvarande profil.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hur kan jag ange olika övervakningsinställningar för olika slutpunkter i en profil?

Traffic Manager övervakningsinställningar är på profilnivå. Om du bara behöver använda en annan övervakningsinställning för en slutpunkt kan [](traffic-manager-nested-profiles.md) du göra det genom att ha slutpunkten som en kapslad profil vars övervakningsinställningar skiljer sig från den överordnade profilen.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hur tilldelar jag HTTP-huvuden till Traffic Manager hälsokontroller till mina slutpunkter?

Traffic Manager kan du ange anpassade huvuden i de HTTP(S)-hälsokontroller som den initierar till dina slutpunkter. Om du vill ange en anpassad rubrik kan du göra det på profilnivå (gäller för alla slutpunkter) eller ange det på slutpunktsnivå. Om en rubrik definieras på båda nivåerna åsidosätter den som anges på slutpunktsnivå profilnivån ett.
Ett vanligt användningsfall för detta är att ange värdhuvuden så att Traffic Manager-begäranden kan dirigeras korrekt till en slutpunkt som finns i en miljö med flera klienter. Ett annat användningsfall är att identifiera Traffic Manager från en slutpunkts HTTP(S)-begärandeloggar

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Vilket värdhuvud använder slutpunktshälsokontroller?

Om ingen anpassad inställning för värdhuvud anges är värdhuvudet som används av Traffic Manager DNS-namnet för slutpunktsmålet som konfigurerats i profilen, om det är tillgängligt.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Vilka IP-adresser kommer hälsokontrollerna från?

Klicka [här](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) om du vill lära dig hur du hämtar listor över IP-adresser Traffic Manager hälsokontroller kan komma från. Du kan använda REST API, Azure CLI eller Azure PowerShell hämta den senaste listan. Granska IP-adresserna i listan för att se till att inkommande anslutningar från dessa IP-adresser tillåts vid slutpunkterna att kontrollera dess hälsostatus.

Exempel med Azure PowerShell:

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus
$result = $serviceTags.Values | Where-Object { $_.Name -eq "AzureTrafficManager" }
$result.Properties.AddressPrefixes
```

> [!NOTE]
> Offentliga IP-adresser kan ändras utan föregående meddelande. Se till att hämta den senaste informationen med hjälp av API:et för identifiering av tjänsttaggar eller en nedladdningsbar JSON-fil.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hur många hälsokontroller till min slutpunkt kan jag förvänta mig Traffic Manager?

Hur många Traffic Manager hälsokontroller som når slutpunkten beror på följande:

- det värde som du har angett för övervakningsintervallet (mindre intervall innebär att fler begäranden landar på slutpunkten under en viss tidsperiod).
- antalet platser som hälsokontrollerna kommer från (IP-adresserna som du kan förvänta dig att dessa kontroller kommer från visas i föregående vanliga frågor och svar).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hur får jag ett meddelande om en av mina slutpunkter går ned?

Ett av de mått som tillhandahålls Traffic Manager är hälsostatusen för slutpunkter i en profil. Du kan se detta som en sammanställning av alla slutpunkter i en profil (till exempel att 75 % av dina slutpunkter är felfria) eller på slutpunktsnivå. Traffic Manager mått exponeras via Azure Monitor och du kan använda [](../azure-monitor/alerts/alerts-metric.md) dess aviseringsfunktioner för att få meddelanden när hälsostatusen för slutpunkten ändras. Mer information finns i [Traffic Manager mått och aviseringar](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager kapslade profiler

### <a name="how-do-i-configure-nested-profiles"></a>Hur gör jag för att konfigurera kapslade profiler?

Kapslade Traffic Manager-profiler kan konfigureras med både Azure Resource Manager och klassiska Azure REST API:er, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI-kommandon. De stöds också via den nya Azure Portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hur många lager av kapsling stöder Traffic Manger?

Du kan kapsla profiler på upp till 10 nivåer. "Loopar" är inte tillåtna.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kan jag blanda andra slutpunktstyper med kapslade underordnade profiler i samma Traffic Manager profil?

Ja. Det finns inga begränsningar för hur du kombinerar slutpunkter av olika typer i en profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hur tillämpas faktureringsmodellen för kapslade profiler?

Det finns ingen negativ prispåverkan av att använda kapslade profiler.

Traffic Manager fakturering har två komponenter: hälsokontroller för slutpunkter och miljontals DNS-frågor

* Hälsokontroller för slutpunkter: Det finns ingen avgift för en underordnad profil när den konfigureras som en slutpunkt i en överordnad profil. Övervakning av slutpunkterna i den underordnade profilen faktureras som vanligt.
* DNS-frågor: Varje fråga räknas bara en gång. En fråga mot en överordnad profil som returnerar en slutpunkt från en underordnad profil räknas endast mot den överordnade profilen.

Fullständig information finns på Traffic Manager [prissättningssidan.](https://azure.microsoft.com/pricing/details/traffic-manager/)

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Påverkas prestanda för kapslade profiler?

Nej. Det uppstår ingen prestandapåverkan när du använder kapslade profiler.

De Traffic Manager-namnservrarna passerar profilhierarkin internt vid bearbetning av varje DNS-fråga. En DNS-fråga till en överordnad profil kan ta emot ett DNS-svar med en slutpunkt från en underordnad profil. En enda CNAME-post används oavsett om du använder en enskild profil eller kapslade profiler. Du behöver inte skapa en CNAME-post för varje profil i hierarkin.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hur beräknar Traffic Manager hälsotillståndet för en kapslad slutpunkt i en överordnad profil?

Den överordnade profilen utför inte hälsokontroller direkt på den underordnade profilen. I stället används hälsotillståndet för den underordnade profilens slutpunkter för att beräkna den underordnade profilens övergripande hälsa. Den här informationen sprids i den kapslade profilhierarkin för att fastställa hälsotillståndet för den kapslade slutpunkten. Den överordnade profilen använder den här sammanställda hälsan för att avgöra om trafiken kan dirigeras till den underordnade profilen.

I följande tabell beskrivs beteendet för att Traffic Manager hälsokontroller för en kapslad slutpunkt.

| Övervakarstatus för underordnad profil | Övervakarstatus för överordnad slutpunkt | Kommentarer |
| --- | --- | --- |
| Inaktiverat Den underordnade profilen har inaktiverats. |Stoppad |Det överordnade slutpunktstillståndet är Stoppad, inte Inaktiverad. Tillståndet Inaktiverad är reserverat för att ange att du har inaktiverat slutpunkten i den överordnade profilen. |
| Försämras. Minst en underordnad profilslutpunkt är i ett degraderat tillstånd. |Online: Antalet Online-slutpunkter i den underordnade profilen är minst värdet för MinChildEndpoints.<BR>CheckingEndpoint: Antalet Online plus CheckingEndpoint-slutpunkter i den underordnade profilen är minst värdet för MinChildEndpoints.<BR>Degraderad: annars. |Trafiken dirigeras till en slutpunkt med statusen CheckingEndpoint. Om MinChildEndpoints har angetts för högt nedgraderas alltid slutpunkten. |
| Online. Minst en underordnad profilslutpunkt är ett onlinetillstånd. Ingen slutpunkt är i degraderat tillstånd. |Se ovan. | |
| CheckingEndpoints. Minst en underordnad profilslutpunkt är "CheckingEndpoint". Inga slutpunkter är online eller degraderade |Samma som ovan. | |
| Inaktiva. Alla underordnade profilslutpunkter är antingen inaktiverade eller stoppade, eller så har den här profilen inga slutpunkter. |Stoppad | |

## <a name="next-steps"></a>Nästa steg:

- Läs mer om Traffic Manager [slutpunktsövervakning och automatisk redundans.](../traffic-manager/traffic-manager-monitoring.md)
- Läs mer om Traffic Manager [trafikroutningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).