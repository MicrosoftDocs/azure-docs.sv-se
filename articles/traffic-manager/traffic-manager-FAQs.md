---
title: Vanliga frågor och svar om Azure Traffic Manager
description: Den här artikeln innehåller svar på vanliga frågor om Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: duau
ms.openlocfilehash: fa8dba12a050e42e258e4224f29e379ff53f09d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576668"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Traffic Manager

## <a name="traffic-manager-basics"></a>Traffic Manager grunderna

### <a name="what-ip-address-does-traffic-manager-use"></a>Vilken IP-adress använder Traffic Manager?

Som förklaras i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager fungerar på DNS-nivå. Den skickar DNS-svar till direkta klienter till lämplig tjänst slut punkt. Klienterna ansluter sedan till tjänstens slut punkt direkt, inte via Traffic Manager.

Därför tillhandahåller Traffic Manager inte någon slut punkt eller IP-adress som klienterna kan ansluta till. Om du vill ha en statisk IP-adress för din tjänst måste den konfigureras på tjänsten, inte i Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Vilka typer av trafik kan dirigeras med hjälp av Traffic Manager?
Som förklaras i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)kan en Traffic Manager-slutpunkt vara vilken Internet tjänst som helst som finns i eller utanför Azure. Därför kan Traffic Manager dirigera trafik som härstammar från det offentliga Internet till en uppsättning slut punkter som också är riktade mot Internet. Om du har slut punkter som finns i ett privat nätverk (till exempel en intern version av [Azure Load Balancer](../load-balancer/components.md#frontend-ip-configurations)) eller har användare som gör DNS-begäranden från sådana interna nätverk, kan du inte använda Traffic Manager för att dirigera trafiken.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Stöder Traffic Manager "tröga" sessioner?

Som förklaras i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager fungerar på DNS-nivå. DNS-svar används för att dirigera klienter till lämplig tjänst slut punkt. Klienterna ansluter till tjänstens slut punkt direkt, inte via Traffic Manager. Därför ser Traffic Manager inte HTTP-trafiken mellan klienten och servern.

Dessutom tillhör käll-IP-adressen för den DNS-fråga som togs emot av Traffic Manager den rekursiva DNS-tjänsten, inte klienten. Traffic Manager har därför inget sätt att spåra enskilda klienter och kan inte implementera "tröga" sessioner. Den här begränsningen är gemensam för alla DNS-baserade trafik hanterings system och är inte särskilt för Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Varför ser jag ett HTTP-fel när jag använder Traffic Manager?

Som förklaras i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager fungerar på DNS-nivå. DNS-svar används för att dirigera klienter till lämplig tjänst slut punkt. Klienterna ansluter sedan till tjänstens slut punkt direkt, inte via Traffic Manager. Traffic Manager ser inte HTTP-trafik mellan klienten och servern. Det innebär att alla HTTP-fel som du ser måste komma från ditt program. För att klienten ska kunna ansluta till programmet slutförs alla steg i DNS-lösningen. Detta omfattar alla interaktioner som Traffic Manager har på program trafik flödet.

Ytterligare undersökning bör därför fokuseras på programmet.

HTTP-värd rubriken som skickas från klientens webbläsare är den vanligaste orsaken till problem. Kontrol lera att programmet har kon figurer ATS för att acceptera rätt värd huvud för det domän namn som du använder. För slut punkter som använder Azure App Service, se [Konfigurera ett anpassat domän namn för en webbapp i Azure App Service med Traffic Manager](../app-service/configure-domain-traffic-manager.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Vilken prestanda påverkas om du använder Traffic Manager?

Som förklaras i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager fungerar på DNS-nivå. Eftersom klienter ansluter till tjänstens slut punkter direkt, påverkas inte prestanda när du använder Traffic Manager när anslutningen har upprättats.

Eftersom Traffic Manager integreras med program på DNS-nivå kräver det ytterligare en DNS-sökning som ska läggas till i DNS-matchningstid. Effekten av Traffic Manager på DNS-matchningstid är minimal. Traffic Manager använder ett globalt nätverk med namnservrar, och använder [anycast](https://en.wikipedia.org/wiki/Anycast) -nätverk för att se till att DNS-frågor alltid dirigeras till den närmast tillgängliga namn servern. Dessutom innebär cachelagring av DNS-svar att den ytterligare DNS-fördröjningen som uppstår genom att använda Traffic Manager endast gäller för en bråkdel av sessioner.

Prestanda metoden dirigerar trafik till närmast tillgängliga slut punkt. Netto resultatet är att den övergripande prestanda påverkan som är associerad med den här metoden ska vara minimal. Eventuell ökning av DNS-fördröjningen ska förskjutas genom lägre nätverks fördröjning till slut punkten.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Vilka program protokoll kan jag använda med Traffic Manager?

Som förklaras i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager fungerar på DNS-nivå. När DNS-sökningen är klar ansluter klienterna direkt till program slut punkten, inte via Traffic Manager. Anslutningen kan därför använda valfritt program protokoll. Om du väljer TCP som övervaknings protokoll kan Traffic Managerens hälso övervakning av slut punkt utföras utan att använda några program protokoll. Om du väljer att verifiera hälso tillståndet med ett program protokoll måste slut punkten kunna svara på antingen HTTP-eller HTTPS GET-begäranden.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Kan jag använda Traffic Manager med domän namnet "blott"?

Ja. Information om hur du skapar en aliasresurspost för domän namns toppen för att referera till en Azure Traffic Manager-profil finns i [Konfigurera en aliasresurspost som stöder spetsig domän namn med Traffic Manager](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Anser Traffic Manager klient under nät adressen vid hantering av DNS-frågor? 

Ja, förutom käll-IP-adressen för den DNS-fråga som den tar emot (som vanligt vis är IP-adressen för DNS-matcharen), vid sökning efter geografiska metoder, prestanda-och under nätets routningsmetoder, betraktar Traffic Manager även klientens under näts adress om den tas med i frågan av den matchare som gör begäran å slutanvändaren.  
Mer specifikt, [RFC 7871 – klient under nät i DNS-frågor](https://tools.ietf.org/html/rfc7871) som tillhandahåller en [utöknings metod för DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) som kan överföra klient under nätets adress från matchare som stöder det.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Vad är DNS TTL och hur påverkar det mina användare?

När en DNS-fråga lägger på Traffic Manager anges ett värde i svaret som kallas Time-to-Live (TTL). Det här värdet, vars enhet är i sekunder, visar DNS-matchare efter hur lång tid det tar att cachelagra svaret. DNS-matchare är inte garanterade att cachelagra det här resultatet, men cachelagring gör det möjligt för dem att svara på eventuella efterföljande frågor från cachen i stället för att gå Traffic Manager DNS-servrar. Detta påverkar svaren enligt följande:

- en högre TTL minskar antalet frågor som landar på Traffic Manager DNS-servrar, vilket kan minska kostnaden för en kund eftersom antalet frågor som hanteras är en fakturerbar användning.
- en högre TTL kan eventuellt minska den tid det tar att göra en DNS-sökning.
- en högre TTL innebär också att dina data inte återspeglar den senaste hälso information som Traffic Manager har erhållit genom sina avsöknings agenter.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hur hög eller låg kan jag ange TTL för Traffic Manager svar?

Du kan ställa in, på per profil nivå, DNS-TTL: t vara så lågt som 0 sekunder och så högt som 2 147 483 647 sekunder (det maximala intervallet är kompatibelt med [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Ett TTL-värde på 0 innebär att underordnade DNS-matchare inte cachelagrar fråge svar och alla frågor förväntas uppnå de Traffic Manager DNS-servrarna för matchning.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hur kan jag förstå hur många frågor som kommer till min profil? 

Ett av måtten som tillhandahålls av Traffic Manager är antalet frågor som besvaras av en profil. Du kan hämta den här informationen på en profil nivå agg regering eller dela upp den ytterligare för att se hur många frågor som har returnerats av vissa slut punkter. Dessutom kan du ställa in aviseringar som meddelar dig om svars volymen för frågan passerar de villkor som du har angett. Mer information [Traffic Manager mått och aviseringar](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager geografisk Traffic routing-metod

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Vad är några användnings fall där geografisk routning är användbart?

Geografisk typ av routning kan användas i alla situationer där en Azure-kund behöver särskilja sina användare baserat på geografiska regioner. Du kan till exempel använda metoden för geografisk trafikroutning för att ge användare från vissa regioner en annan användar upplevelse än från andra regioner. Ett annat exempel är beroende av lokala data suveränitets uppdrag som kräver att användare från en speciell region endast betjänas av slut punkter i den regionen.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hur gör jag för att avgöra om jag bör använda routningsmetod för prestanda eller geografisk routningsmetod?

Den största skillnaden mellan dessa två populära routningsmetoder är att i metoden för prestanda cirkulation ditt primära mål är att skicka trafik till slut punkten som kan ge den lägsta svars tiden till anroparen, medan det primära målet är att tvinga ett geo-avgränsnings tecken för dina anropare i geografisk routning så att du kan dirigera dem till en speciell slut punkt. Överlappningen inträffar eftersom det finns en korrelation mellan geografisk stängning och lägre latens, även om detta inte alltid är sant. Det kan finnas en slut punkt i en annan geografi som kan ge uppmaningen bättre svars tid och i så fall kommer prestanda cirkulationen att skicka användaren till den slut punkten, men geografisk routning skickar dem alltid till den slut punkt som du har mappat för deras geografiska region. Om du vill göra det tydligare kan du tänka på följande exempel – med geografisk routning kan du göra ovanliga mappningar, till exempel skicka all trafik från Asien till slut punkter i oss och all amerikansk trafik till slut punkter i Asien. I så fall kan geografisk routning avsiktligt göra exakt vad du har konfigurerat för att utföra och prestanda optimering är inte ett övervägande. 
>[!NOTE]
>Det kan finnas scenarier där du kan behöva både prestanda-och geografisk routning för de här scenarierna som kapslade profiler kan vara ett bra alternativ. Du kan till exempel konfigurera en överordnad profil med geografisk routning där du skickar all trafik från Nordamerika till en kapslad profil som har slut punkter i USA och använder prestanda dirigering för att skicka trafiken till den bästa slut punkten i den uppsättningen. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Vilka regioner stöds av Traffic Manager för geografisk routning?

Hierarkin för land/region som används av Traffic Manager hittar du [här](traffic-manager-geographic-regions.md). Medan den här sidan hålls uppdaterad med eventuella ändringar kan du också hämta samma information via programmering med hjälp av [Azure Traffic Manager REST API](/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hur avgör Traffic Manager var en användare frågar från?

Traffic Manager tittar på käll-IP-adressen för frågan (detta är förmodligen en lokal DNS-matchare som utför frågan å användarens vägnar) och använder en intern IP-adress för att fastställa platsen. Den här kartan uppdateras regelbundet för att kunna beakta ändringar på Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Är det garanterat att Traffic Manager korrekt kan fastställa den exakta geografiska platsen för användaren i varje fall?

Nej, Traffic Manager kan inte garantera att den geografiska region som vi härleder från käll-IP-adressen för en DNS-fråga alltid motsvarar användarens plats på grund av följande orsaker:

- Först, enligt beskrivningen i föregående vanliga frågor och svar, är käll-IP-adressen som vi ser i en DNS-matchare som utför sökningen för användarens räkning. Även om den geografiska platsen för DNS-matcharen är en lämplig proxy för användarens geografiska plats, kan det också vara olika beroende på placeringen av tjänsten DNS-matchare och den specifika DNS-matchare-tjänsten som en kund har valt att använda. Till exempel kan en kund som finns i Malaysia anges i enhetens inställningar använder en DNS-matchare-tjänst vars DNS-server i Singapore kan plockas för att hantera fråge upplösningarna för den användaren/enheten. I så fall kan Traffic Manager bara se den matchare IP-adress som motsvarar Singapore-platsen. Se även de tidigare frågorna om support för klient under nätet på den här sidan.

- För det andra använder Traffic Manager en intern karta för att göra IP-adressen till översättning av geografiskt område. Även om den här kartan verifieras och uppdateras kontinuerligt för att öka noggrannheten och kontot för internetens föränderliga natur, är det fortfarande möjligt att vår information inte är en exakt representation av den geografiska platsen för alla IP-adresser.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Behöver en slut punkt vara fysiskt placerad i samma region som den har kon figurer ATS för geografisk routning?

Nej, platsen för slut punkten tillämpar inga begränsningar för vilka regioner som kan mappas till den. En slut punkt i US-Central Azure-region kan till exempel ha alla användare från Indien riktade till sig.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Kan jag tilldela geografiska regioner till slut punkter i en profil som inte har kon figurer ATS för att göra geografisk routning?

Ja, om Dirigerings metoden för en profil inte är geografisk, kan du använda [Azure Traffic Manager-REST API](/rest/api/trafficmanager/) för att tilldela geografiska regioner till slut punkter i profilen. Om det gäller profiler som inte är geografiska, ignoreras den här konfigurationen. Om du ändrar en sådan profil till geografisk typ av routning vid ett senare tillfälle kan Traffic Manager använda dessa mappningar.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Varför får jag ett fel när jag försöker ändra routningsmetod för en befintlig profil till geografisk?

Alla slut punkter under en profil med geografisk routning måste ha minst en region mappad. Om du vill konvertera en befintlig profil till geografisk typ av routning måste du först associera geografiska regioner med alla dess slut punkter med [Azure Traffic Manager REST API](/rest/api/trafficmanager/) innan du ändrar routningsmetod till geografisk. Om du använder portalen måste du först ta bort slut punkterna, ändra Dirigerings metoden för profilen till geografisk och sedan lägga till slut punkterna tillsammans med deras geografiska områdes mappning.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Varför rekommenderas det starkt att kunder skapar kapslade profiler i stället för slut punkter under en profil med geografisk routning aktive rad?

En region kan bara tilldelas till en slut punkt i en profil om den använder geografisk routningsmetod. Om den här slut punkten inte är en kapslad typ med en underordnad profil som är kopplad till den, kommer Traffic Manager fortsätta att skicka trafik till den eftersom alternativet att inte skicka någon trafik ännu bättre. Traffic Manager redundansväxlas inte till en annan slut punkt, även om den tilldelade regionen är en "överordnad" för den region som har tilldelats till slut punkten som gick in i fel tillstånd (till exempel om en slut punkt som har en region i Frankrike inte är felfri, ingen växling till en annan slut punkt som har det regions-Europa som tilldelats). Detta görs för att säkerställa att Traffic Manager respekterar de geografiska gränser som en kund har konfigurerat i sin profil. För att få förmånen av att redundansväxla till en annan slut punkt när en slut punkt slutar fungera, rekommenderas att geografiska regioner tilldelas till kapslade profiler med flera slut punkter i stället för enskilda slut punkter. På så sätt kan trafiken redundansväxlas till en annan slut punkt inuti samma kapslade underordnade profil om en slut punkt i den kapslade underordnade profilen Miss lyckas.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Finns det några begränsningar för API-versionen som stöder den här typen av cirkulations typ?

Ja, endast API version 2017-03-01 och nyare stöder geografisk typ av routning. Det går inte att använda alla äldre API-versioner för att skapa profiler av geografisk typ av routning eller tilldela geografiska regioner till slut punkter. Om en äldre API-version används för att hämta profiler från en Azure-prenumeration, returneras inte någon profil av typen geografisk routning. När du använder äldre API-versioner, har alla profiler som har slut punkter med en tilldelning av geografisk region ingen tilldelning av den geografiska regionen visas.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Routnings metod för Traffic Manager under näts trafik

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Vad är några användnings fall där under näts dirigering är användbart?

Med hjälp av under näts dirigering kan du särskilja den upplevelse du levererar för vissa uppsättningar av användare som identifieras av käll-IP för DNS-begärandena IP-adresser. Ett exempel skulle visa olika innehåll om användarna ansluter till en webbplats från företagets HQ. En annan begränsar användare från vissa Internet-leverantörer till att bara få åtkomst till slut punkter som endast stöder IPv4-anslutningar om dessa Internet leverantörer har underordnat prestanda när IPv6 används.
En annan orsak till att använda routningsmetod för undernät är tillsammans med andra profiler i en kapslad profil uppsättning. Om du till exempel vill använda geografisk routningsmetod för geo-avgränsning av dina användare, men för en särskild Internet leverantör som du vill använda en annan routningsmetod, kan du ha en profil med metoden för routning av undernät som överordnad profil och åsidosätta att Internet leverantören använder en särskild underordnad profil och har standard geografi profilen för alla andra.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Hur vet Traffic Manager IP-adressen för slutanvändaren?

Slutanvändarens enheter använder vanligt vis en DNS-matchare för att göra DNS-sökningen för deras räkning. Den utgående IP-adressen för sådana lösningar är vad Traffic Manager ser som käll-IP. Dessutom söker Subnet-metoden för att se om det finns en ECS-information (Extended client Subnet) som skickades med begäran. Om det finns ECS information, är det den adress som används för att fastställa routningen. I avsaknad av ECS-information används frågans käll-IP-adress i operationsföljd syfte.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hur kan jag ange IP-adresser när jag använder Subnet-routning?

De IP-adresser som ska associeras med en slut punkt kan anges på två sätt. Först kan du använda den fyr punktavgränsade decimal oktetten med en start-och slut adress för att ange intervallet (till exempel 1.2.3.4-5.6.7.8 eller 3.4.5.6-3.4.5.6). Sedan kan du använda CIDR-noteringen för att ange intervallet (till exempel 1.2.3.0/24). Du kan ange flera intervall och kan använda båda Notations typerna i en intervall uppsättning. Några begränsningar gäller.

-    Du får inte överlappa adress intervall eftersom varje IP-adress måste mappas till en enda slut punkt
-    Start adressen får inte vara större än slut adressen
-    Om CIDR-noteringen används måste IP-adressen före "/" vara start adressen för intervallet (till exempel 1.2.3.0/24 är giltigt men 1.2.3.4.4/24 är ogiltigt)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hur kan jag ange en återställnings slut punkt när jag använder under näts dirigering?

Om du har en slut punkt utan undernät som är mappad till den i en profil med under näts dirigering kommer alla begär Anden som inte matchar andra slut punkter att dirigeras till här. Vi rekommenderar starkt att du har en sådan återställnings slut punkt i din profil eftersom Traffic Manager returnerar ett NXDOMAIN-svar om en begäran kommer in och den inte är mappad till några slut punkter eller om den är mappad till en slut punkt, men slut punkten är skadad.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Vad händer om en slut punkt är inaktive rad i en under näts typ profil?

Om du har en slut punkt med som är inaktive rad i en profil med under näts dirigering fungerar Traffic Manager som om den slut punkten och under näts mappningarna inte finns. Om en fråga som skulle matchas med dess IP-adress mappning tas emot och slut punkten är inaktive rad, returnerar Traffic Manager en återställnings slut punkt (en utan mappningar) eller om en sådan slut punkt inte finns, kommer att returnera ett NXDOMAIN-svar.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Routnings metod för Traffic Manager Multivärdes trafik

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Vad är några användnings fall där multivärde-routning är användbart?

Multivärde-routning returnerar flera felfria slut punkter i ett enda fråge svar. Den största fördelen med detta är att om en slut punkt inte är felfri, har klienten fler alternativ att försöka igen utan att göra något annat DNS-anrop (som kan returnera samma värde från en överordnad cache). Detta gäller för känsliga program för tillgänglighet som vill minimera stillestånds tiden.
En annan användning av routningsmetod för flera värden är om en slut punkt är "dubbels homed" till både IPv4-och IPv6-adresser och du vill ge anroparen båda alternativen att välja från när den initierar en anslutning till slut punkten.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hur många slut punkter returneras när multivärde-routning används?

Du kan ange det maximala antalet slut punkter som ska returneras och multivärdet returnerar inte mer än så många felfria slut punkter när en fråga tas emot. Högsta möjliga värde för den här konfigurationen är 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Får jag samma uppsättning slut punkter när multivärde-routning används?

Vi kan inte garantera att samma uppsättning slut punkter returneras i varje fråga. Detta påverkas också av att några av slut punkterna kan hamna i fel tillstånd när de inte tas med i svaret

## <a name="real-user-measurements"></a>Faktisk slutanvändarmätning

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Vilka är fördelarna med att använda Faktisk slutanvändarmätning?

När du använder metoden för prestanda cirkulation väljer Traffic Manager den bästa Azure-regionen som slutanvändaren ska ansluta till genom att kontrol lera käll-IP och EDNS klient under nätet (om det har skickats in) och kontrol lera det mot den nätverks fördröjnings information som tjänsten underhåller. Faktisk slutanvändarmätning förbättrar det här för din slut användar bas genom att låta deras erfarenhet bidra till den här svars tabellen, förutom att se till att den här tabellen sträcker sig över slutanvändarens nätverk från vilka slutanvändarna ansluter till Azure. Detta leder till ökad exakthet i routningen av slutanvändaren.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Kan jag använda Faktisk slutanvändarmätning med icke-Azure-regioner?

Faktisk slutanvändarmätning mått och rapporter om bara svars tiden för att komma åt Azure-regioner. Om du använder prestandabaserade routning med slut punkter som finns i andra regioner än Azure-regioner, kan du fortfarande dra nytta av den här funktionen genom att få ökad latens information om den Azure-region som du valde att koppla till den här slut punkten.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Vilken routningsmetod fördelar från Faktisk slutanvändarmätning?

Ytterligare information som erhålls via Faktisk slutanvändarmätning gäller endast för profiler som använder routningsmetod för prestanda. Faktisk slutanvändarmätning länken är tillgänglig från alla profiler när du visar den via Azure Portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Måste jag aktivera Faktisk slutanvändarmätning varje profil separat?

Nej, du behöver bara aktivera den en gång per prenumeration och all svars information som mäts och rapporteras är tillgänglig för alla profiler.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hur gör jag för att inaktivera Faktisk slutanvändarmätning för min prenumeration?

Du kan sluta Periodisera avgifter som är relaterade till Faktisk slutanvändarmätning när du slutar samla in och skicka tillbaka svars tids mätningar från klient programmet. Om du till exempel har bäddat in ett JavaScript-skript inbäddat i webb sidor kan du sluta använda den här funktionen genom att ta bort JavaScript-filen eller genom att stänga av dess anrop när sidan återges.

Du kan också stänga av Faktisk slutanvändarmätning genom att ta bort nyckeln. När du tar bort nyckeln ignoreras alla mått som skickas till Traffic Manager med den nyckeln.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Kan jag använda Faktisk slutanvändarmätning med andra klient program än webb sidor?

Ja, Faktisk slutanvändarmätning har utformats för att mata in data som samlas in via olika typer av slut användar klienter. Dessa vanliga frågor och svar kommer att uppdateras när nya typer av klient program får stöd.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hur många mätningar görs varje gången mitt Faktisk slutanvändarmätning-aktiverade webb sida renderas?

När Faktisk slutanvändarmätning används med det angivna beräknings-JavaScript-värdet kommer varje sid åter givning att resultera i sex mätningar. De rapporteras sedan tillbaka till Traffic Manager-tjänsten. Du debiteras för den här funktionen baserat på antalet mätningar som rapporter ATS till Traffic Manager tjänsten. Om användaren till exempel navigerar bort från din webb sida medan mätningarna tas, men innan den rapporteras, beaktas inte dessa mått för fakturerings syfte.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Är det en fördröjning innan Faktisk slutanvändarmätning skript körs på min webb sida?

Nej, det finns ingen fördröjning innan skriptet anropas.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Kan jag bara använda Faktisk slutanvändarmätning med de Azure-regioner jag vill mäta?

Nej, varje gången den anropas mäter Faktisk slutanvändarmätning-skriptet en uppsättning sex Azure-regioner som fastställs av tjänsten. Den här uppsättningen ändringar mellan olika anrop och när ett stort antal sådana anrop sker omfattar mått täckningen över olika Azure-regioner.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Kan jag begränsa antalet mätningar som gjorts till ett speciellt tal?

Måttet java script är inbäddat på din webb sida och du har fullständig kontroll över när du ska börja och sluta använda det. Så länge Traffic Manager tjänsten tar emot en begäran om en lista över Azure-regioner som ska mätas returneras en uppsättning regioner.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Kan jag se de mått som utförs av klient programmet som en del av Faktisk slutanvändarmätning?

Eftersom mått logiken körs från klient programmet är du full kontroll över vad som händer, inklusive att se fördröjnings mätningarna. Traffic Manager rapporterar inte en sammanställd vy av de mått som tas emot under nyckeln som är länkad till din prenumeration.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Kan jag ändra mått skriptet som tillhandahålls av Traffic Manager?

Även om du har kontroll över vad som är inbäddat på din webb sida, förhindrar vi starkt att du gör några ändringar i Mät skriptet för att säkerställa att det mäter och rapporterar fördröjningen korrekt.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Kommer det att vara möjligt för andra att se vilken nyckel jag använder med Faktisk slutanvändarmätning?

När du bäddar in mått skriptet på en webb sida är det möjligt för andra att se skriptet och din Faktisk slutanvändarmätning (Rom)-nyckel. Men det är viktigt att veta att den här nyckeln skiljer sig från ditt prenumerations-ID och genereras av Traffic Manager som endast ska användas för detta ändamål. Att veta att din RUM nyckel inte äventyrar ditt Azure-kontos säkerhet.

### <a name="can-others-abuse-my-rum-key"></a>Kan andra missbruka min RUM nyckel?

Även om det är möjligt för andra att använda din nyckel för att skicka fel information till Azure, kommer några fel mätningar inte att ändra routningen eftersom den tas med i beräkningen tillsammans med alla andra mått som vi får. Om du behöver ändra dina nycklar, kan du återskapa nyckeln som leder till att den gamla nyckeln blir borttagen.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Måste jag ange måttet Java Script på alla mina webb sidor?

Faktisk slutanvändarmätning ger större värde för antalet mått ökningar. När du har sagt detta är det ditt beslut om huruvida du behöver placera det på alla dina webb sidor eller ett urval. Vår rekommendation är att börja med att placera den på den mest besökta sidan där en användare förväntas stanna på sidan fem sekunder eller mer.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Kan information om slutanvändarna identifieras av Traffic Manager om jag använder Faktisk slutanvändarmätning?

När det angivna Mät-JavaScript-värdet används får Traffic Manager insyn i klientens IP-adress för slutanvändaren och käll-IP-adressen för den lokala DNS-matchare som de använder. Traffic Manager använder bara klientens IP-adress när den har trunkerats till att inte identifiera den särskilda slutanvändaren som skickade måtten.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Behöver webb sidans mätnings Faktisk slutanvändarmätning använda Traffic Manager för routning?

Nej, det behöver inte använda Traffic Manager. Överförings sidan av Traffic Manager fungerar separat från den faktiska användar mått delen och även om det är en bra idé att ha dem både i samma webb egenskap behöver de inte vara det.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Måste jag vara värd för alla tjänster i Azure-regioner som ska användas med Faktisk slutanvändarmätning?

Nej, du behöver inte vara värd för någon komponent på Server sidan på Azure för att Faktisk slutanvändarmätning ska fungera. Bild punkts bilden som hämtas av mät-Java Script och tjänsten som kör den i olika Azure-regioner hanteras och hanteras av Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Ökar Azures bandbredds användning när jag använder Faktisk slutanvändarmätning?

Som vi nämnt i föregående svar ägs och hanteras Server sidans komponenter i Faktisk slutanvändarmätning och hanteras av Azure. Det innebär att din Azure bandbredds användning inte ökar eftersom du använder Faktisk slutanvändarmätning. Detta omfattar inte bandbredds användningen utanför Azure-avgifterna. Vi minimerar bandbredden som används genom att bara hämta en bild punkts bild för att mäta svars tiden för en Azure-region. 

## <a name="traffic-view"></a>Trafikvy

### <a name="what-does-traffic-view-do"></a>Vad gör Trafikvy?

Trafikvy är en funktion i Traffic Manager som hjälper dig att förstå mer om dina användare och hur deras upplevelse är. Den använder frågorna som tagits emot av Traffic Manager och de tabell över nätverks svars information som tjänsten underhåller för att ge dig följande:

- De regioner från vilka dina användare ansluter till dina slut punkter i Azure.
- Volymen av användare som ansluter från dessa regioner.
- De Azure-regioner som de dirigeras till.
- Svars tiden för dessa Azure-regioner.

Den här informationen är tillgänglig så att du kan använda det geografiska kart överlägg och tabellvy i portalen, förutom att vara tillgängliga som rå data som du kan hämta.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hur kan jag dra nytta av Trafikvy?

Trafikvy ger dig den övergripande vyn över trafiken som Traffic Manager profiler tar emot. I synnerhet kan den användas för att förstå var användar basen ansluter från och är lika viktiga som deras genomsnittliga svars tids upplevelse. Du kan sedan använda den här informationen för att hitta områden där du behöver fokusera, till exempel genom att utöka dina Azure-behov till en region som kan hantera dessa användare med lägre latens. En annan insikt som du kan härleda från att använda Trafikvy är att se mönstren för trafik till olika regioner som i sin tur kan hjälpa dig att fatta beslut om ökande eller minskning av lager i dessa regioner.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Hur skiljer sig Trafikvy från Traffic Manager mått som är tillgängliga via Azure Monitor?

Azure Monitor kan användas för att förstå den mängd trafik som tas emot av din profil och dess slut punkter. Du kan också spåra slut punkternas hälso status genom att exponera hälso kontroll resultatet. När du behöver gå utöver dessa och förstår slutanvändarens upplevelse av att ansluta till Azure på regional nivå, kan Trafikvy användas för att uppnå detta.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Använder Trafikvy EDNS-klientens under näts information?

DNS-frågorna som hanteras av Azure Traffic Manager ta hänsyn till ECS-information för att öka noggrannheten i operationsföljden. Men när du skapar data uppsättningen som visar var användarna ansluter från, använder Trafikvy bara IP-adressen för DNS-matcharen.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hur många dagars data Trafikvy använda?

Trafikvy skapar utdata genom att bearbeta data från sju dagar före dagen innan de visas av dig. Det här är ett rörligt fönster och de senaste data kommer att användas varje gång du besöker.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hur hanterar Trafikvy externa slut punkter?

När du använder externa slut punkter som ligger utanför Azure-regioner i en Traffic Manager profil kan du välja att låta den vara mappad till en Azure-region som är en proxy för dess svars tids egenskaper (detta är verkligen nödvändigt om du använder metoden för prestanda cirkulation). Om det finns den här mappningen för Azure-regionen används Azure-regionens svars värden när du skapar Trafikvy-utdata. Om ingen Azure-region har angetts kommer svars informationen att vara tom i data för dessa externa slut punkter.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Måste jag aktivera Trafikvy för varje profil i min prenumeration?

Under för hands versions perioden har Trafikvy Aktiver ATS på en prenumerations nivå. Som en del av förbättringarna som vi gjorde före den allmänna tillgängligheten kan du nu aktivera Trafikvy på en profil nivå, så att du kan få mer detaljerad möjlighet att aktivera den här funktionen. Som standard kommer Trafikvy att inaktive ras för en profil.

>[!NOTE]
>Om du har aktiverat Trafikvy på en prenumerations nivå under förhands gransknings tiden måste du aktivera den igen för varje profil under den prenumerationen.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hur kan jag stänga av Trafikvy?

Du kan inaktivera Trafikvy för alla profiler med hjälp av portalen eller REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Hur fungerar Trafikvy fakturering?

Trafikvy prissättning baseras på antalet data punkter som används för att skapa utdata. För närvarande är den enda data typen som stöds den fråga din profil tar emot. Dessutom faktureras du bara för den bearbetning som utfördes när du har Trafikvy aktiverat. Det innebär att om du aktiverar Trafikvy under en viss tids period under en månad och sedan stänger av den under andra tider, bearbetas bara de data punkter som bearbetades när funktionen har Aktiver ATS för din räkning.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-slutpunkter

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Kan jag använda Traffic Manager med slut punkter från flera prenumerationer?

Det går inte att använda slut punkter från flera prenumerationer med Azure Web Apps. Azure Web Apps kräver att alla anpassade domän namn som används med Web Apps bara används i en enda prenumeration. Det går inte att använda Web Apps från flera prenumerationer med samma domän namn.

För andra slut punkts typer är det möjligt att använda Traffic Manager med slut punkter från fler än en prenumeration. I Resource Manager kan du lägga till slut punkter från vilken prenumeration som helst i Traffic Manager, så länge som den person som konfigurerar Traffic Managers profilen har Läs behörighet till slut punkten. Dessa behörigheter kan beviljas med hjälp av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Slut punkter från andra prenumerationer kan läggas till med [Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) eller [Azure CLI](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Kan jag använda Traffic Manager med moln tjänstens mellanlagrings platser?

Ja. Mellanlagrings platser för moln tjänster kan konfigureras i Traffic Manager som externa slut punkter. Hälso kontroller debiteras fortfarande enligt Azure-slutpunkters taxan.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Stöder Traffic Manager IPv6-slutpunkter?

Traffic Manager tillhandahåller för närvarande inte IPv6-adresser bara namnservrar. Traffic Manager kan dock fortfarande användas av IPv6-klienter som ansluter till IPv6-slutpunkter. En klient gör DNS-förfrågningar direkt till Traffic Manager. Klienten använder i stället en rekursiv DNS-tjänst. En klient med enbart IPv6 skickar begär anden till den rekursiva DNS-tjänsten via IPv6. Sedan bör den rekursiva tjänsten kunna kontakta Traffic Manager namnservrar med hjälp av IPv4.

Traffic Manager svarar med slut punktens DNS-namn eller IP-adress. Det finns två alternativ för att stödja en IPv6-slutpunkt. Du kan lägga till slut punkten som ett DNS-namn som har en associerad AAAA-post och Traffic Manager hälso kontroll av slut punkten och returnera den som en CNAME-posttyp i svaret på frågan. Du kan också lägga till den slut punkten direkt med hjälp av IPv6-adressen och Traffic Manager returnerar en AAAA-typ post i svaret på frågan.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Kan jag använda Traffic Manager med mer än en webbapp i samma region?

Normalt används Traffic Manager för att dirigera trafik till program som distribuerats i olika regioner. Det kan dock också användas där ett program har fler än en distribution i samma region. Traffic Manager Azure-slutpunkter tillåter inte att fler än en webbappens slut punkt från samma Azure-region läggs till i samma Traffic Manager-profil.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Hur gör jag för att flytta min Traffic Managers profils Azure-slutpunkter till en annan resurs grupp eller prenumeration?

Azure-slutpunkter som är associerade med en Traffic Manager-profil spåras med hjälp av deras resurs-ID. När en Azure-resurs som används som en slut punkt (t. ex. offentlig IP, klassisk moln tjänst, WebApp eller en annan Traffic Manager profil som används på ett kapslat sätt) flyttas till en annan resurs grupp eller prenumeration ändras resurs-ID: t. I det här scenariot måste du för närvarande uppdatera Traffic Manager profilen genom att först ta bort och sedan lägga till tillbaka slut punkterna till profilen.

## <a name="traffic-manager-endpoint-monitoring"></a>Slutpunktsövervakning för Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Är Traffic Manager elastisk till Azures regions problem?

Traffic Manager är en viktig komponent i leverans av program med hög tillgänglighet i Azure.
För att tillhandahålla hög tillgänglighet måste Traffic Manager ha en undantags bar hög tillgänglighets nivå och vara elastisk till regionalt fel.

Enligt design är Traffic Manager-komponenter elastiska till ett fullständigt haveri i alla Azure-regioner. Den här återhämtningen gäller för alla Traffic Manager-komponenter: DNS-namnservrar, API, lagrings skikt och tjänsten för slut punkts övervakning.

Om det osannolikt uppstår ett avbrott i en hel Azure-region förväntas Traffic Manager fortsätta att fungera normalt. Program som distribueras i flera Azure-regioner kan förlita sig på Traffic Manager dirigera trafik till en tillgänglig instans av programmet.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Hur påverkar valet av resurs grupps plats Traffic Manager?

Traffic Manager är en enkel, global tjänst. Den är inte regional. Valet av resurs grupps plats ger ingen skillnad till Traffic Manager profiler som distribueras i den resurs gruppen.

Azure Resource Manager kräver att alla resurs grupper anger en plats, vilket avgör standard platsen för resurser som distribueras i den resurs gruppen. När du skapar en Traffic Manager-profil skapas den i en resurs grupp. Alla Traffic Manager profiler använder **Global** som plats och åsidosätter resurs gruppens standardvärde.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hur gör jag för att avgöra den aktuella hälsan för varje slut punkt?

Den aktuella övervaknings statusen för varje slut punkt, utöver den övergripande profilen, visas i Azure Portal. Den här informationen är också tillgänglig via trafik övervaknings [REST API](/rest/api/trafficmanager/), [PowerShell-cmdletar](/powershell/module/az.trafficmanager)och plattforms [oberoende Azure CLI](/cli/azure/install-classic-cli).

Du kan också använda Azure Monitor för att spåra hälso tillståndet för dina slut punkter och se en visuell representation av dem. Mer information om hur du använder Azure Monitor finns i [dokumentationen för Azure-övervakning](../azure-monitor/data-platform.md).

### <a name="can-i-monitor-https-endpoints"></a>Kan jag övervaka HTTPS-slutpunkter?

Ja. Traffic Manager stöder avsökning via HTTPS. Konfigurera **https** som protokoll i övervaknings konfigurationen.

Traffic Manager kan inte tillhandahålla certifikat validering, inklusive:

* Certifikat på Server sidan har inte verifierats
* SNI certifikat på Server sidan har inte verifierats
* Klient certifikat stöds inte

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Använder jag en IP-adress eller ett DNS-namn när jag lägger till en slut punkt?

Traffic Manager har stöd för att lägga till slut punkter med tre sätt att referera dem – som ett DNS-namn, som en IPv4-adress och som en IPv6-adress. Om slut punkten läggs till som en IPv4-eller IPv6-adress, kommer fråge svaret vara av typen post A respektive AAAA. Om slut punkten lades till som ett DNS-namn kommer fråge svaret att vara av typen CNAME. Det är bara tillåtet att lägga till slut punkter som IPv4 eller IPv6-adress om slut punkten är av typen **extern**.
Alla routningsmetoder och övervaknings inställningar stöds av de tre adress typerna för slut punkter.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Vilka typer av IP-adresser kan jag använda när jag lägger till en slut punkt?

Med Traffic Manager kan du använda IPv4-eller IPv6-adresser för att ange slut punkter. Det finns några begränsningar som anges nedan:

- Adresser som motsvarar reserverade privata IP-adresser är inte tillåtna. De här adresserna är de som anropas i RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 och RFC 5771
- Adressen får inte innehålla några port nummer (du kan ange vilka portar som ska användas i profil konfigurations inställningarna)
- Två slut punkter i samma profil kan ha samma mål-IP-adress

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Kan jag använda olika slut punkts typer för adressering i samma profil?

Nej, Traffic Manager tillåter inte att du blandar adress typer för slut punkter i en profil, förutom för en profil med multivärde-routningsmetod där du kan blanda IPv4-och IPv6-adress typer

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Vad händer när en post av typen inkommande fråga skiljer sig från den post typ som är kopplad till slut punkternas adress typ?

När en fråga tas emot mot en profil hittar Traffic Manager först den slut punkt som måste returneras enligt den angivna routningsmetod och hälso status för slut punkterna. Den söker sedan efter den posttyp som begärdes i den inkommande frågan och post typen som är associerad med slut punkten innan ett svar returneras baserat på tabellen nedan.

För profiler med någon annan routningsmetod än multivärde:

|Begäran om inkommande fråga|     Slut punkts typ|     Svar har angetts|
|--|--|--|
|HELST |    A/AAAA/CNAME |    Mål slut punkt| 
|A |    A/CNAME |    Mål slut punkt|
|A |    AAAA |    Inga DATA |
|AAAA |    AAAA/CNAME |    Mål slut punkt|
|AAAA |    A |    Inga DATA |
|CNAME |    CNAME |    Mål slut punkt|
|CNAME     |A/AAAA |    Inga DATA |
|

För profiler med routningsmetod inställd på multivärde:

|Begäran om inkommande fråga|     Slut punkts typ |    Svar har angetts|
|--|--|--|
|HELST |    Blandning av A och AAAA |    Mål slut punkter|
|A |    Blandning av A och AAAA |    Endast mål slut punkter av typen A|
|AAAA    |Blandning av A och AAAA|     Endast mål slut punkter av typen AAAA|
|CNAME |    Blandning av A och AAAA |    Inga DATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Kan jag använda en profil med IPv4/IPv6-adresserade slut punkter i en kapslad profil?

Ja, du kan med undantaget att en profil av typen multivärde inte kan vara en överordnad profil i en kapslad profil uppsättning.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Jag har stoppat en slut punkt för ett webb program i Traffic Manager profil, men jag får ingen trafik även när jag har startat om den. Hur kan jag åtgärda detta?

När en slut punkt för Azure-webbprogram stoppas Traffic Manager slutar att kontrol lera hälso tillståndet och startar om hälso kontrollerna först efter det att den har identifierat att slut punkten har startats om. Om du vill förhindra den här fördröjningen inaktiverar du och aktiverar sedan slut punkten i Traffic Manager profilen igen när du har startat om slut punkten.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Kan jag använda Traffic Manager även om mitt program saknar stöd för HTTP eller HTTPS?

Ja. Du kan ange TCP som övervaknings protokoll och Traffic Manager kan initiera en TCP-anslutning och vänta på ett svar från slut punkten. Om slut punkten svarar på anslutningsbegäran med ett svar för att upprätta anslutningen, så markeras den slut punkten som felfri i tids gränsen.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Vilka speciella svar krävs från slut punkten vid användning av TCP-övervakning?

När TCP-övervakning används startar Traffic Manager en tre-vägs TCP-handskakning genom att skicka en SYN-begäran till slut punkten på den angivna porten. Det väntar sedan på ett SYN bekräftelse svar från slut punkten under en tids period (anges i tids gräns inställningarna).

- Om ett SYN-ACK-svar tas emot inom den tids gräns som anges i övervaknings inställningarna betraktas slut punkten som felfri. En RÄNTETRANS eller RÄNTETRANS-ACK är det förväntade svaret från Traffic Manager när den stänger en socket med jämna mellanrum.
- Om ett SYN-ACK-svar tas emot efter den angivna tids gränsen, kommer Traffic Manager att svara med en tids gräns för att återställa anslutningen.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Hur snabbt kan Traffic Manager flytta mina användare bort från en felaktig slut punkt?

Traffic Manager innehåller flera inställningar som kan hjälpa dig att styra failover-beteendet för din Traffic Manager profil på följande sätt:

- Du kan ange att Traffic Manager avsöker slut punkterna oftare genom att ange ett avsöknings intervall på 10 sekunder. Detta säkerställer att alla slut punkter som går dåligt att identifiera kan identifieras så snart som möjligt. 
- Du kan ange hur lång tid som ska gå innan en tids gräns för en hälso kontroll begärs (lägsta timeout-värde är 5 SEK).
- Du kan ange hur många fel som kan inträffa innan slut punkten markeras som ohälsosam. Värdet kan vara lågt som 0, vilket innebär att slut punkten markeras som ohälsosam så fort den inte klarar den första hälso kontrollen. Men med det lägsta värdet 0 för det tolererade antalet fel kan det leda till slut punkter som tas bort från rotationen på grund av eventuella tillfälliga problem som kan uppstå vid tidpunkten för avsökningen.
- Du kan ange TTL (Time-to-Live) för DNS-svaret så att det blir så lågt som 0. Det innebär att DNS-matchare inte kan cachelagra svaret och varje ny fråga får ett svar som införlivar den senaste aktuella hälso informationen som Traffic Manager har.

Genom att använda de här inställningarna kan Traffic Manager tillhandahålla redundans under 10 sekunder efter att en slut punkt har skadats och en DNS-fråga görs mot motsvarande profil.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hur kan jag ange olika övervaknings inställningar för olika slut punkter i en profil?

Traffic Manager övervaknings inställningarna är på per profil nivå. Om du behöver använda en annan övervaknings inställning för endast en slut punkt kan du göra det genom att låta den slut punkten vara en [kapslad profil](traffic-manager-nested-profiles.md) vars övervaknings inställningar skiljer sig från den överordnade profilen.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hur kan jag tilldela HTTP-huvuden till Traffic Manager hälso kontroller i mina slut punkter?

Med Traffic Manager kan du ange anpassade huvuden i de HTTP-hälsokontroller som det initierar till dina slut punkter. Om du vill ange en anpassad rubrik kan du göra det på profil nivå (gäller för alla slut punkter) eller ange den på slut punkts nivå. Om en rubrik definieras på båda nivåerna åsidosätter den som anges på slut punkts nivån profil nivån en.
Ett vanligt användnings fall för detta är att ange värdhuvuden så att Traffic Manager begär Anden kan dirigeras korrekt till en slut punkt som finns i en miljö med flera innehavare. Ett annat användnings fall är att identifiera Traffic Manager begär Anden från en slut punkts HTTP (S) begär ande loggar

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Vilket värd huvud använder hälso kontroller för slut punkter?

Om ingen anpassad värd huvud inställning anges, är värd rubriken som används av Traffic Manager DNS-namnet på slut punkts målet som kon figurer ATS i profilen, om det är tillgängligt.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Vilka IP-adresser kommer hälso kontrollerna från?

Klicka [här](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) om du vill visa JSON-filen som visar de IP-adresser som Traffic Manager hälso kontroller kan härstamma från. Granska de IP-adresser som anges i JSON-filen för att se till att inkommande anslutningar från de här IP-adresserna är tillåtna vid slut punkterna för att kontrol lera dess hälso status.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hur många hälso kontroller till min slut punkt kan jag förväntar mig från Traffic Manager?

Antalet Traffic Manager hälso kontroller som når slut punkten beror på följande:

- det värde som du har angett för övervaknings intervallet (mindre intervall innebär att fler förfrågningar får landning på slut punkten under en viss tids period).
- det antal platser som hälso kontrollerna kommer från (IP-adresserna från där du kan vänta på att dessa kontroller visas i föregående frågor).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hur kan jag få ett meddelande om en av mina slut punkter upphör?

Ett av måtten som tillhandahålls av Traffic Manager är hälso status för slut punkter i en profil. Du kan se detta som en mängd av alla slut punkter i en profil (till exempel 75% av dina slut punkter är felfria) eller, på en nivå per slut punkt. Traffic Manager Mät värden exponeras genom Azure Monitor och du kan använda [aviserings funktionerna](../azure-monitor/alerts/alerts-metric.md) för att få meddelanden när slut punktens hälso status ändras. Mer information finns i [Traffic Manager mått och aviseringar](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager kapslade profiler

### <a name="how-do-i-configure-nested-profiles"></a>Hur gör jag för att konfigurera kapslade profiler?

Kapslade Traffic Manager profiler kan konfigureras med både Azure Resource Manager och de klassiska Azure REST-API: erna, Azure PowerShell-cmdlets och plattforms oberoende Azure CLI-kommandon. De stöds också via den nya Azure Portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hur många kapslings nivåer stöder Traffic Manager?

Du kan kapsla profiler upp till 10 nivåer djup. Loopar är inte tillåtna.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kan jag blanda andra slut punkts typer med kapslade underordnade profiler i samma Traffic Manager profil?

Ja. Det finns inga begränsningar för hur du kombinerar slut punkter av olika typer i en profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hur gäller fakturerings modellen för kapslade profiler?

Det finns ingen negativ inverkan på användningen av kapslade profiler.

Traffic Manager faktureringen har två komponenter: hälso kontroller för slut punkter och miljon tals DNS-frågor

* Hälso kontroller för slut punkt: det kostar inget att debitera en underordnad profil när den är konfigurerad som en slut punkt i en överordnad profil. Övervakning av slut punkterna i den underordnade profilen faktureras på vanligt sätt.
* DNS-frågor: varje fråga räknas bara en gång. En fråga mot en överordnad profil som returnerar en slut punkt från en underordnad profil räknas endast till den överordnade profilen.

Fullständig information finns på sidan med [Traffic Manager priser](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Påverkas prestandan för kapslade profiler?

Nej. Prestanda påverkas inte när du använder kapslade profiler.

Traffic Manager namnservrar bläddrar i profilmappar internt vid bearbetning av varje DNS-fråga. En DNS-fråga till en överordnad profil kan ta emot ett DNS-svar med en slut punkt från en underordnad profil. En enda CNAME-post används om du använder en enskild profil eller kapslade profiler. Du behöver inte skapa en CNAME-post för varje profil i hierarkin.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hur beräknar Traffic Manager hälsan för en kapslad slut punkt i en överordnad profil?

Den överordnade profilen utför inte hälso kontroller på den underordnade direkt. I stället används hälsan för den underordnade profilens slut punkter för att beräkna den övergripande hälsan för den underordnade profilen. Den här informationen sprider den kapslade profilmappar för att fastställa hälso tillståndet för den kapslade slut punkten. Den överordnade profilen använder denna aggregerade hälsa för att avgöra om trafiken kan dirigeras till den underordnade nivån.

I följande tabell beskrivs beteendet för Traffic Manager hälso kontroller för en kapslad slut punkt.

| Status för Övervakare för underordnad profil | Status för övervakaren för överordnad slut punkt | Kommentarer |
| --- | --- | --- |
| Inaktiverat Den underordnade profilen har inaktiverats. |Stoppad |Status för överordnad slut punkt har stoppats, inte inaktiverats. Inaktiverat tillstånd är reserverat för att indikera att du har inaktiverat slut punkten i den överordnade profilen. |
| Försämrad. Minst en underordnad profil slut punkt är i ett degraderat tillstånd. |Online: antalet Online-slutpunkter i den underordnade profilen är minst värdet för MinChildEndpoints.<BR>CheckingEndpoint: antalet online-och CheckingEndpoint-slutpunkter i den underordnade profilen är minst värdet för MinChildEndpoints.<BR>Degraderad: annars. |Trafiken dirigeras till en slut punkt med status CheckingEndpoint. Om MinChildEndpoints har angetts för hög försämras slut punkten alltid. |
| Online. Minst en underordnad profil slut punkt är ett online-tillstånd. Ingen slut punkt har statusen degraderat. |Se ovan. | |
| CheckingEndpoints. Minst en underordnad profil slut punkt är ' CheckingEndpoint '. Inga slut punkter är "online" eller "degraderade" |Samma som ovan. | |
| Inaktivera. Alla underordnade profil slut punkter är antingen inaktiverade eller stoppade, eller så har den här profilen inga slut punkter. |Stoppad | |

## <a name="next-steps"></a>Nästa steg:

- Läs mer om Traffic Manager [slut punkts övervakning och automatisk redundans](../traffic-manager/traffic-manager-monitoring.md).
- Läs mer om hur du Traffic Manager [metoder för trafik cirkulation](../traffic-manager/traffic-manager-routing-methods.md).