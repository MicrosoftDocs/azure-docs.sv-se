---
title: Azure frontend-rapporter, standard/Premium (för hands version)
description: Den här artikeln förklarar hur rapportering fungerar i Azures front dörr.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101100593"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Azure frontend-rapporter, standard/Premium (för hands version)

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure frontend-rapporter med standard-/Premium-rapporter ger en inbyggd och helt specifik vy över hur du använder Azures front dörr tillsammans med associerade webb programs brand Väggs mått. Du kan också dra nytta av åtkomst loggar för att ytterligare felsöka och felsöka. Azures analys rapporter för front dörr innehåller trafik rapporter och säkerhets rapporter.

| Rapporter | Information |
|---------|---------|
| Översikt över viktiga mått | Visar generella data som har skickats från Azures front dörrs kanter till klienter<br/>-Högsta bandbredd<br/>– Begär Anden <br/>– Förhållandet mellan cacheträffar<br/> -Total svars tid<br/>-5XX fel frekvens |
| Trafik efter domän | -Ger en översikt över alla domäner under profilen<br/>– Uppdelning av data som överförts från AFD Edge till klienten<br/>-Totalt antal förfrågningar<br/>-3XX/4XX/5XX Response Code by Domains |
| Trafik efter plats | – Visar en karta över begäran och användning per främsta länder<br/>– Trend visning av de främsta länderna |
| Användning | -Visar data överföring från Azures front dörr till klienter<br/>– Data överförs från ursprung till AFD-gränsen<br/>-Bandbredd från AFD Edge till klienter<br/>-Bandbredd från ursprung till AFD Edge<br/>– Begär Anden<br/>-Total svars tid<br/>– Trend för antal förfrågningar efter HTTP-statuskod |
| Cachelagring | -Visar förhållandet mellan cacheträffar och antal begär Anden<br/>– Trend visning av träff-och Cachemissar-begäranden |
| Översta URL | – Visar antal förfrågningar <br/>-Överförda data <br/>– Förhållandet mellan cacheträffar <br/>– Distribution av svars status kod för de mest efterfrågade 50-resurserna. |
| Främsta referent | – Visar antal förfrågningar <br/>-Överförda data <br/>– Förhållandet mellan cacheträffar <br/>-Svars status kod distribution för de översta 50-referenterna som genererar trafik. |
| Högsta användar agent | – Visar antal förfrågningar <br/>-Överförda data <br/>– Förhållandet mellan cacheträffar <br/>-Svars status kod distribution för de främsta 50 användar agenter som användes för att begära innehåll. |

| Säkerhetsrapporter | Information |
|---------|---------|
| Översikt över viktiga mått | -Visar matchade WAF-regler<br/>-Matchade OWASP-regler<br/>-Matchade BOT-regler<br/>-Matchade anpassade regler |
| Mått per dimension | – Analys av matchade WAF regler trend per åtgärd<br/>– Ring diagram över händelser efter regel uppsättnings typ och händelse per regel grupp<br/>– Dela upp listan över främsta händelser efter regel-ID, land, IP-adress, URL och användar agent  |

> [!NOTE]
> Säkerhets rapporter är bara tillgängliga med Azure front dörr Premium SKU.

De flesta rapporter baseras på åtkomst loggar och erbjuds kostnads fritt till kunder i Azures front dörr. Kunden behöver inte aktivera åtkomst loggar eller göra någon konfiguration för att visa dessa rapporter. Rapporter kan nås via portal och API. Hämtning av CSV stöds också. 

Rapporter stöder eventuella valda datum intervall från föregående 90 dagar. Med data punkter var 5: e minut, varje timme eller varje dag baserat på det datum intervall som valts. Normalt kan du visa data med en fördröjning på inom en timme och ibland med en fördröjning på upp till några timmar. 

## <a name="access-reports-using-the-azure-portal"></a>Få åtkomst till rapporter med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) och välj din Azure-profil för front dörr standard/Premium.

1. I navigerings fönstret väljer du **rapporter eller säkerhet** under *analys*.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Skärm bild av landnings sidan rapporter":::

1. Det finns sju flikar för olika dimensioner. Välj dimension för intresse.

   * Trafik efter domän
   * Användning 
   * Trafik efter plats
   * Cache
   * Översta URL
   * Främsta referent
   * Högsta användar agent

1. När du har valt dimensionen kan du välja olika filter.
  
    1. **Visa data för** – Välj det datum intervall som du vill visa trafik efter domän. Tillgängliga intervall är:
        
        * Senaste 24 timmarna
        * Senaste 7 dagarna
        * Senaste 30 dagarna
        * Senaste 90 dagarna
        * Den här månaden
        * Senaste månaden
        * Anpassat datum

         Som standard visas data för de senaste sju dagarna. För flikar med linje diagram, kan data precisionen visas med de datum intervall som du har valt som standard beteende. 
    
        * 5 minuter – en data punkt var 5: e minut för datum intervall som är mindre än eller lika med 24 timmar.
        * Per timme – en data varje timme för datum intervall mellan 24 timmar och 30 dagar
        * Per dag – en data per dag för datum intervall som är större än 30 dagar.

        Du kan alltid använda agg regering för att ändra standard granularitet för sammansättning. Obs: 5 minuter fungerar inte för data intervallet längre än 14 dagar. 

    1. **Plats** – Välj en eller flera klient platser efter land. Länderna är grupperade i sex regioner: Nordamerika, Asien, Europa, Afrika, Oceanien och Sydamerika. Referera till [region/land-mappning](https://en.wikipedia.org/wiki/Subregion). Som standard väljs alla länder.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Skärm bild av rapporter för plats dimension.":::
   
    1. **Protokoll** – Välj antingen http eller https för att Visa trafik data.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Skärm bild av rapporter för protokoll dimension.":::
    
    1. **Domäner** – Välj en eller flera slut punkter eller anpassade domäner. Som standard är alla slut punkter och anpassade domäner markerade. 
    
        * Om du tar bort en slut punkt eller en anpassad domän i en profil och sedan återskapar samma slut punkt eller domän i en annan profil. Slut punkten kommer att anses som en andra slut punkt.  
        * Om du visar rapporter efter anpassad domän – när du tar bort en anpassad domän och binder den till en annan slut punkt. De behandlas som en anpassad domän. Om Visa efter slut punkt – de behandlas som separata objekt. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Skärm bild av rapporter för domän dimension.":::

1. Om du vill exportera data till en CSV-fil väljer du länken *Hämta CSV* på den valda fliken.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Skärm bild av hämtning av CSV-fil för rapporter.":::

### <a name="key-metrics-for-all-reports"></a>Viktiga mått för alla rapporter

| Metric | Beskrivning |
|---------|---------|
| Överförda data | Visar data som överförts från AFD Edge-pop till klienten för den valda tids perioden, klient platser, domäner och protokoll. |
| Högsta bandbredd | Högsta bandbredds användning i bitar per sekund från Azures front dörr pop till klienten för den valda tids perioden, klient platser, domäner och protokoll. | 
| Totalt antal förfrågningar | Antalet förfrågningar som AFD Edge-pop har svarat på klienten för den valda tids perioden, klient platser, domäner och protokoll. |
| Cacheträffsförhållande | Procent andelen av alla cacheable-begäranden för vilka AFD betjänade innehållet från dess Edge-cacheminnen för den valda tids perioden, klient platser, domäner och protokoll. |
| 5XX fel frekvens | Procent andelen förfrågningar för vilka HTTP-statuskoden till klienten var en 5XX för den valda tids perioden, klient platser, domäner och protokoll. |
| Total svars tid | Genomsnittlig svars tid för alla begär Anden för den valda tids perioden, klient platser, domäner och protokoll. Svars tiden för varje begäran mäts som den totala tiden då klient förfrågan tas emot av Azures frontend tills den sista svars byte som har skickats från Azure-frontend till klienten. |

## <a name="traffic-by-domain"></a>Trafik efter domän

Trafik per domän ger en rutnätsvy över alla domäner under den här Azures frontend-profil. I den här rapporten kan du Visa: 
* Begäranden
* Data som överförs från Azures frontend-dörr till klienten
* Begär Anden med status kod (3XX, 4Xx och 5XX) för varje domän

Domäner innehåller slut punkter och anpassade domäner, som förklaras i rapport sessionen för åtkomst.  

Du kan gå till andra flikar för att undersöka ytterligare eller Visa åtkomst loggen för mer information om du hittar måtten under förväntan. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Skärm bild av landnings sidan för rapporter":::


## <a name="usage"></a>Användning

Den här rapporten visar trender för trafik-och svars status kod efter olika dimensioner, inklusive:

* Data överförs från Edge till klient och från ursprung till kanten i linje diagrammet. 

* Data som överförs från Edge till klient efter protokoll i linje diagram. 

* Antal förfrågningar från Edge till klienter i linje diagrammet.  

* Antal begär Anden från Edge till klienter efter protokoll, HTTP och HTTPS i linje diagram. 

* Bandbredd från Edge till klient i linje diagram. 

* Total svars tid, som mäter den totala tiden från klientbegäran som mottagits av front dörren tills den sista svars byte som skickades från frontend-dörren till klienten.

* Antal förfrågningar från Edge till klienter efter HTTP-statuskod i linje diagram. Varje begäran genererar en HTTP-statuskod. HTTP-statuskod visas i HTTPStatusCode i rå loggen. Status koden beskriver hur CDN Edge hanterade begäran. Till exempel anger en 2xx status kod att begäran har betjänats till en klient. En 4xx status kod indikerar att ett fel har inträffat. Mer information om HTTP-statuskod finns i lista över HTTP-statuskod. 

* Antal begär Anden från gränsen till klienter efter HTTP-statuskod. Procent andel begär Anden efter HTTP-statuskod bland alla förfrågningar i rutnätet. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Skärm bild av rapporter efter användning" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Trafik efter plats

I den här rapporten visas de översta 50 platserna av de besökare som har åtkomst till din till gång. Rapporten innehåller också en uppdelning av mått per land och ger dig en övergripande vy över de länder där den mest trafik som genereras. Slutligen kan du se vilket land som har högre träff grad för cache eller 4XX/5XX-felkoder.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Skärm bild av rapporter efter plats" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

Följande ingår i rapporterna:

* En världs karta över de översta 50 länderna med data som överförts eller efter önskemål.
* Två linje diagram trenden för de fem främsta länderna efter data som överförts och begär Anden som du själv väljer. 
* Ett rutnät av de främsta länderna med motsvarande data som överförs från AFD till-klienter, data som överförs från% av alla länder, begär Anden, begäran% bland alla länder, träffar i cachen, 4XX svars kod och 5XX svars kod.

## <a name="caching"></a>Cachelagring

Med cachelagring av rapporter får du en diagramvy över cacheträffar/missar och träffar på cacheträffar baserat på begär Anden. De här nyckel måtten förklarar hur CDN cachelagrar innehåll sedan det snabbaste prestanda resultatet från cacheträffar. Du kan optimera data leverans hastigheten genom att minimera Cachemissar. Den här rapporten innehåller:

* Trend för cacheträffar och antal missar i linje diagram.

* Förhållandet mellan cacheträffar i linje diagrammet.

Cacheträffar/missar beskriver antalet cacheträffar i begäran och cache missar för klient begär Anden.

* Träffar: klient begär Anden som hanteras direkt från Azure CDN Edge-servrar. Refererar till de begär Anden vars värden för CacheStatus i obehandlade loggar visas, PARTIAL_HIT eller fjärrträff. 

* Saknar: klient begär Anden som hanteras av Azure CDN Edge-servrar som hämtar innehåll från ursprung. Refererar till de begär Anden vars värden för fältet CacheStatus i obehandlade loggar saknas. 

**Träff grad för cache** beskriver procent andelen cachelagrade begär Anden som betjänas direkt. Formeln för förhållandet mellan cacheträffar är: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` . 

Den här rapporten tar med i steg om cachelagring och begär Anden som uppfyller följande krav beaktas i beräkningen. 

* Det begärda innehållet cachelagrades på den POP som är närmast den begär ande eller ursprungs skölden. 

* Partiellt cachelagrat innehåll för objekt segment.

Alla följande fall undantas: 

* Begär Anden som nekas på grund av regel uppsättning.

* Förfrågningar som innehåller matchande regel uppsättning som har ställts in på inaktive rad cache. 

* Begär Anden som blockeras av WAF. 

* Ursprungets svarshuvuden visar att de inte ska cachelagras. Till exempel Cache-Control: privat, Cache-Control: no-cache eller pragma: no-cache-huvuden förhindrar att en till gång cachelagras. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Skärm bild av rapporter för cachelagring.":::

## <a name="top-urls"></a>Populära URL: er

Med de vanligaste webb adresserna kan du Visa mängden trafik som påförts över en viss slut punkt eller anpassad domän. Du ser data för de mest efterfrågade 50-resurserna under en period under de senaste 90 dagarna. Populära webb adresser visas med följande värden. Användaren kan sortera webb adresser efter antal förfrågningar, begär Anden%, överförda data och överförda data%. Alla mått sammanställs per timme och kan variera beroende på vilken tidsram som valts. URL: en refererar till värdet för RequestUri i Access log. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Skärm bild av rapporter för högsta URL.":::

* URL, refererar till den fullständiga sökvägen för den begärda till gången i formatet `http(s)://contoso.com/index.html/images/example.jpg` . 
* Antal begär Anden. 
* Begäran% av totalt antal förfrågningar som betjänas av Azures frontend-dörr. 
* Överförda data. 
* Överförda data%. 
* Träff grad i cache%
* Begär Anden med svars kod som 4XX
* Begär Anden med svars kod som 5XX

> [!NOTE]
> De största URL-adresserna kan ändras med tiden och för att få en korrekt lista över de översta 50-URL: erna räknar Azures front dörr alla dina URL-begäranden per timme och behåller den löpande summan under en dag. URL: erna längst ned i 500-URL: erna kan leda till eller ta bort listan över dagen, så det totala antalet URL: er är ungefärliga.  
>
> De översta 50-URL: erna kan leda till och falla i listan, men de försvinner sällan från listan, så siffrorna för de översta URL: erna är vanligt vis pålitliga. När en URL försvinner från listan och ökar efter en dag, uppskattas antalet begär Anden under perioden när de saknas i listan, baserat på begär ande numret för den URL som visas under den perioden.  
>
> Samma logik gäller för topp användar agenten. 

## <a name="top-referrers"></a>Främsta referenter

Främsta referenter gör det möjligt för kunder att visa den översta 50-referenten som gav flest begär anden till innehållet på en viss slut punkt eller anpassad domän. Du kan visa data för en period under de senaste 90 dagarna. En referent anger den URL som en begäran genererades från. Referent kan komma från en sökmotor eller andra webbplatser. Om en användare skriver en URL (t. ex. http (s)://contoso.com/index.html) direkt till adress raden i en webbläsare, är referenten för den begärda "Tom". Rapporten främsta referenter innehåller följande värden. Du kan sortera efter antal förfrågningar, begär Anden%, överförda data och överförda data%. Alla mått sammanställs per timme och kan variera beroende på vilken tidsram som valts. 

* Referent, värdet referent i råa loggar 
* Antal förfrågningar 
* Begäran% av totalt antal förfrågningar som betjänas av Azure CDN under den valda tids perioden. 
* Överförda data 
* Överförda data% 
* Träff grad i cache%
* Begär Anden med svars kod som 4XX
* Begär Anden med svars kod som 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Skärm bild av rapporter för främsta referenter.":::

## <a name="top-user-agent"></a>Högsta användar agent

Med den här rapporten kan du Visa grafisk och statistisk visning av de översta 50 användar agenter som användes för att begära innehåll. Exempel:
* Mozilla/5.0 (Windows NT 10,0; WOW64 
* AppleWebKit/537.36 (KHTML, t. ex. Gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

Ett rutnät visar antalet begär Anden, begäran%, överförda data och överförda data, träffar i cacheminnet%, förfrågningar med svarskod som 4XX och förfrågningar med svarskod som 5XX. Användar agenten refererar till värdet för UserAgent i Access-loggar.

## <a name="security-report"></a>Säkerhets rapport

Med den här rapporten kan du Visa grafisk och statistisk visning av WAF mönster efter olika dimensioner.

| Dimensioner | Beskrivning |
|---------|---------|
| Översikts mått – matchade WAF-regler | Begär Anden som matchar anpassade WAF-regler, hanterade WAF-regler och robot Manager. |
| Översikts mått – blockerade begär Anden | Procent andelen begär Anden som blockeras av WAF-regler bland alla begär Anden som matchade WAF-regler. |
| Översikts mått – matchade hanterade regler | Fyra linje diagram trend för begär Anden som blockerar, loggar, tillåter och omdirigerar. |
| Översikts mått – matchad anpassad regel | Begär Anden som matchar anpassade WAF-regler. |
| Översikts mått – matchad bot-regel | Begär Anden som matchar robot Manager. |
| WAF begär trend per åtgärd | Fyra linje diagram trend för begär Anden som blockerar, loggar, tillåter och omdirigerar. |
| Händelser efter regeltyp | Ring diagram över WAF begär distribution efter regel typ, t. ex. robot, anpassade regler och hanterade regler. |
| Händelser efter regel grupp | Ring diagram över WAF begär distribution per regel grupp. |
| Begär Anden efter åtgärder | En tabell med begär Anden efter åtgärder, i fallande ordning. |
| Begär Anden efter topp regel-ID: n | En tabell med begär Anden enligt de översta 50 regel-ID: n, i fallande ordning. |
| Begär Anden per främsta länder |  En tabell med begär Anden från de översta 50 länderna, i fallande ordning. |
| Begär Anden efter främsta klient-IP-adresser |  En tabell med begär Anden som de översta 50 IP-adresserna, i fallande ordning. |
| URL för begär Anden efter högsta begäran |  En tabell med begär Anden från de översta 50 URL: erna, i fallande ordning. |
| Begär Anden efter främsta värdnamn | En tabell med begär Anden per topp 50-värdnamn, i fallande ordning. |
| Begär Anden per topp användar agenter | En tabell med begär Anden per de översta 50 användar agenterna, i fallande ordning. |

## <a name="cvs-format"></a>CVS-format

Du kan ladda ned CSV-filer för olika flikar i rapporter. I det här avsnittet beskrivs värdena i varje CSV-fil.

### <a name="general-information-about-the-cvs-report"></a>Allmän information om CVS-rapporten

Varje CSV-rapport innehåller viss allmän information och informationen är tillgänglig i alla CSV-filer. med variabler baserat på den rapport som du hämtar. 


| Värde | Beskrivning |
|---------|---------|
| Rapport | Rapportens namn. | 
| Domains | Listan över slut punkter eller anpassade domäner för rapporten. |
| StartDateUTC | Början på datum intervallet som du genererade rapporten för, i UTC (Coordinated Universal Time) |
| EndDateUTC | Slutet av datum intervallet som du genererade rapporten för, i UTC (Coordinated Universal Time) |
| GeneratedTimeUTC | Datum och tid när du genererade rapporten, i UTC-tid (Coordinated Universal Time) |
| Location | Listan över de länder där klienten begär sitt ursprung. Värdet är som standard. Gäller inte för säkerhets rapporten. |
| Protokoll | Protokollet för begäran, HTTP eller HTTPs. Gäller inte för Top URL och trafik av användar agent i rapporter och säkerhets rapporter. |
| Aggregering | Granularitet för data agg regering på varje rad, var 5: e minut, varje timme och varje dag. Gäller inte trafik efter domän, högsta URL och trafik per användar agent i rapporter och säkerhets rapporter. |

### <a name="data-in-traffic-by-domain"></a>Data i trafik efter domän

* Domain 
* Totalt antal förfrågningar 
* Cacheträffsförhållande 
* 3XX-begäranden 
* 4XX-begäranden 
* 5XX-begäranden 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Data i trafik efter plats

* Location
* TotalRequests
* Anmoda
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Data som används

Det finns tre rapporter i den här CSV-filen. Ett för HTTP-protokoll, ett för HTTPS-protokoll och en för HTTP-statuskod. 

Rapporter för HTTP och HTTPs delar samma data uppsättning. 

* Tid 
* Protokoll 
* DataTransferred (byte) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Rapport för HTTP-statuskod. 

* Tid 
* DataTransferred (byte) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Data i cachelagring 

* Tid
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Data i översta URL 

* URL 
* TotalRequests 
* Anmoda 
* DataTransferred (byte) 
* DataTransferred% 

### <a name="data-in-user-agent"></a>Data i användar agent 

* UserAgent 
* TotalRequests 
* Anmoda 
* DataTransferred (byte) 
* DataTransferred% 

### <a name="security-report"></a>Säkerhets rapport 

Det finns sju tabeller med samma fält nedan.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

De sju tabellerna är för tid, regel-ID, land, IP-adress, URL, värdnamn, användar agent. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azures front dörr standard/Premium real tids övervaknings mått](how-to-monitor-metrics.md).
