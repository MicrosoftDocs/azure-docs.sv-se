---
title: Azure front dörr standard/Premium (för hands version)-loggning
description: I den här artikeln förklaras hur loggning fungerar i Azure frontend-dörren standard/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 531f4a9c9f535779e451ca316a8a5867f6cdaba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573905"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure front dörr standard/Premium (för hands version)-loggning

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Azures front dörr tillhandahåller olika loggnings funktioner som hjälper dig att spåra, övervaka och felsöka din frontend-dörr. 

* Åtkomst loggarna innehåller detaljerad information om varje begäran som AFD tar emot och hjälper dig att analysera och övervaka åtkomst mönster och felsöka problem. 
* Aktivitets loggarna ger insyn i de åtgärder som utförs på Azure-resurser.  
* Hälso avsöknings loggar innehåller loggar för varje misslyckad avsökning till ditt ursprung. 
* Länkar för brand vägg för webbaserade program (WAF) innehåller detaljerad information om begär Anden som loggas via antingen identifierings-eller skydds läget i en Azure-frontend-slutpunkt. En anpassad domän som kan konfigureras med WAF kan också visas via dessa loggar.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Åtkomst loggar, hälso avsöknings loggar och WAF-loggar är inte aktiverade som standard. Använd stegen nedan för att aktivera loggning. Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal. Loggarna kan ha fördröjningar upp till några minuter. 

Du har tre alternativ för att lagra dina loggar: 

* **Lagrings konto:** Lagrings konton används bäst för scenarier när loggar lagras under en längre tid och granskas vid behov. 
* **Event Hub:** Event Hub är ett bra alternativ för att integrera med andra verktyg för säkerhets informations-och händelse hantering (SIEM) eller externa data lager. Till exempel: Splunk/DataDog/Sumo. 
* **Azure-Log Analytics:** Azure Log Analytics i Azure Monitor används bäst för allmän övervakning i real tid och analys av prestanda i Azures frontend-dörr.

## <a name="configure-logs"></a>Konfigurera loggar

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter Azure-front dörren standard/Premium och välj Azures profil för front dörr.

1. I profilen går du till **övervakning**, väljer **diagnostikinställningar**. Välj **Lägg till diagnostikinställning**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Skärm bild av landnings sidan för diagnostiska inställningar.":::

1. Under **diagnostikinställningar** anger du ett namn för  **namn på diagnostikinställningar**.

1. Välj  **loggen** från **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** och **FrontDoorWebApplicationFirewallLog**.

1. Välj **mål information**. Mål alternativen är: 

    * **Skicka till Log Analytics**
        * Välj *prenumerationen* och *Log Analytics arbets ytan*.
    * **Arkivera till ett lagrings konto**
        * Välj *prenumerationen* och *lagrings kontot*. och ange **kvarhållning (dagar)**.
    * **Strömma till en händelsehubb**
        * Välj *prenumeration, Event Hub-namnrymd, Event Hub-namn (valfritt)* och *princip namn för Event Hub*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Skärm bild av sidan diagnostiska inställningar.":::

1. Klicka på **Spara**.

## <a name="access-log"></a>Åtkomst logg

Azures front dörr tillhandahåller för närvarande enskilda API-begäranden med varje post med följande schema och inloggad JSON-format som visas nedan. 

| Egenskap | Beskrivning |
|----------|-------------| 
| TrackingReference | Den unika referens strängen som identifierar en begäran som hanteras av AFD, som också skickas som X-Azure-ref-huvud till klienten. Krävs för att söka efter information i åtkomst loggarna för en speciell begäran. |
| Tid | Datum och tid när AFD Edge levererade innehållet till klienten (i UTC). |
| HttpMethod | HTTP-metod som används av begäran: ta bort, Hämta, huvud, alternativ, korrigering, POST eller placering. |
| HttpVersion | Den HTTP-version som visnings programmet angav i begäran. |
| RequestUri | URI för den mottagna begäran. Det här fältet är ett fullständigt schema, port, domän, sökväg och frågesträng |
| HostName | Värd namnet i begäran från klienten. Om du aktiverar anpassade domäner och har en domän med jokertecken (*. contoso.com), är hostname a.contoso.com. Om du använder Azures frontend-domän (contoso.azurefd.net) är värd namnet contoso.azurefd.net. |
| RequestBytes | Storleken på HTTP Request-meddelandet i byte, inklusive begärandehuvuden och begär ande texten. Antalet byte i data som visas i förfrågan, inklusive rubriker. |
| ResponseBytes | Byte som skickats av backend-servern som svar. |
| UserAgent | Webbläsarens typ som används av klienten. |
| ClientIp | IP-adressen för klienten som gjorde den ursprungliga begäran. Om det fanns ett X-vidarebefordrat-för-huvud i begäran plockas klientens IP-adress från samma. |
| SocketIp | IP-adressen för den direkta anslutningen till AFD Edge. Om klienten använder en HTTP-proxy eller en belastningsutjämnare för att skicka begäran, är värdet för SocketIp IP-adressen för proxyservern eller belastningsutjämnaren. |
| Svarstid | Tiden från den tid AFD Edge-servern tar emot en klients begäran till den tid som AFD skickar den sista byten av svar till klienten, i millisekunder. Det här fältet tar inte hänsyn till nätverks svars tid och TCP-buffring. |
| RequestProtocol | Det protokoll som klienten angav i begäran: HTTP, HTTPS. |
| SecurityProtocol | TLS/SSL-protokollets version som används av begäran eller null om ingen kryptering. Möjliga värden är: SSLv3, TLSv1, TLSv 1.1, TLSv 1.2 |
| SecurityCipher | När värdet för protokollet Request är HTTPS, anger det här fältet TLS/SSL-chiffrering som förhandlas av klienten och AFD för kryptering. |
| Slutpunkt | Domän namnet för AFD-slutpunkten, till exempel contoso.z01.azurefd.net |
| HttpStatusCode | HTTP-statuskoden som returnerades från AFD. |
| Popup | Edge-pop, som svarade på användarens begäran.  |
| Cache-status | Visar status koden för hur begäran hanteras av CDN-tjänsten när den kommer att cachelagras. Möjliga värden påträffas: HTTP-begäran betjänades från AFD Edge POP cache. <br> **Saknar**: http-begäran har bearbetats från ursprung. <br/> **PARTIAL_HIT**: några av de byte från en begäran har tagits emot från AFD Edge pop-cache medan några av de byte som hämtades från ursprunget för objekt segment scenarier. <br> **CACHE_NOCONFIG**: vidarebefordrar begär Anden utan cacheinställningar, inklusive bypass-scenario. <br/> **PRIVATE_NOSTORE**: ingen cache har kon figurer ATS i cacheinställningar av kunder. <br> **REMOTE_HIT**: begäran betjänades av den överordnade nodens cache. <br/> **N/A**:* * begäran som nekades av den signerade URL-adressen och regel uppsättningen. |
| MatchedRulesSetName | Namnen på de regler som har bearbetats. |
| RouteName | Namnet på den väg som begäran matchade. |
| ClientPort | IP-porten för den klient som gjorde begäran. |
| Referent | URL till den plats där begäran kom. |
| TimetoFirstByte | Tiden i millisekunder från AFD tar emot begäran till den tidpunkt då den första byten skickas till klienten, mätt i Azures front dörr. Den här egenskapen mäter inte klient data. |
| Mängden | Det här fältet innehåller detaljerad information om feltoken för varje svar. <br> **Noerror**: indikerar att inget fel hittades. <br> **CertificateError**: allmänt SSL-certifikat fel. <br> **CertificateNameCheckFailed**: värd namnet i SSL-certifikatet är ogiltigt eller matchar inte. <br> **ClientDisconnected**: det gick inte att utföra begäran på grund av klient nätverks anslutning. <br> **ClientGeoBlocked**: klienten blockerades på grund av att IP-adressen var geografisk. <br> **UnspecifiedClientError**: allmänt klient fel. <br> **InvalidRequest**: ogiltig begäran. Det kan bero på en felaktig rubrik, brödtext och URL. <br> **DNSFailure**: DNS-haveri. <br> **DNSTimeout**: DNS-frågan för att lösa Server delens tids gräns nåddes. <br> **DNSNameNotResolved**: det gick inte att matcha Server namnet eller adressen. <br> **OriginConnectionAborted**: anslutningen till ursprungs platsen kopplades bort onormalt. <br> **OriginConnectionError**: fel i allmän ursprungs anslutning. <br> **OriginConnectionRefused**: det gick inte att upprätta en anslutning till ursprungs platsen. <br> **OriginError**: allmänt ursprungs fel. <br> **OriginInvalidRequest**: en ogiltig begäran skickades till ursprunget. <br> **ResponseHeaderTooBig**: ursprunget returnerade en för stor del av ett svars huvud. <br> **OriginInvalidResponse**:* * ursprung returnerade ett ogiltigt eller okänt svar. <br> **OriginTimeout**: tids gränsen för ursprungs förfrågan har gått ut. <br> **ResponseHeaderTooBig**: ursprunget returnerade en för stor del av ett svars huvud. <br> **RestrictedIP**: begäran blockerades på grund av en begränsad IP-adress. <br> **SSLHandshakeError**: det gick inte att upprätta en anslutning till sitt ursprung på grund av ett SYNKRONISERINGSFEL vid SSL-skakning. <br> **SSLInvalidRootCA**: RootCA var ogiltig. <br> **SSLInvalidCipher**: chiffrering var ogiltig för vilken https-anslutningen upprättades. <br> **OriginConnectionAborted**: anslutningen till ursprungs platsen kopplades bort onormalt. <br> **OriginConnectionRefused**: det gick inte att upprätta en anslutning till ursprungs platsen. <br> **UnspecifiedError**: ett fel inträffade som inte fick plats i något av felen i tabellen. |
| OriginURL | Den fullständiga URL: en för ursprunget där förfrågningar skickas. Bestående av schemat, värd rubriken, porten, sökvägen och frågesträngen. <br> **Omskrivning av URL**: om det finns en regel för att skapa en URL i regel uppsättning refererar sökvägen till en omskriven sökväg. <br> **Cachelagra på Edge-popup** Om det är en Cacheträff i Edge-POP, är ursprunget saknas. <br> **Stor begäran** Om det begärda innehållet är stort med flera segmenterade begär Anden som kommer tillbaka till ursprunget, motsvarar det här fältet den första begäran till ursprunget. Mer information finns i objekt segment. |
| OriginIP | Den ursprungliga IP-adressen som hanterade begäran. <br> Cacheträff **i Edge-popup** Om det är en Cacheträff i Edge-POP, är ursprunget saknas. <br> **Stor begäran** Om det begärda innehållet är stort med flera segmenterade begär Anden som kommer tillbaka till ursprunget, motsvarar det här fältet den första begäran till ursprunget. Mer information finns i objekt segment. |
| OriginName| Det fullständiga DNS-namnet (hostname i ursprungs-URL) till ursprunget. <br> Cacheträff **i Edge-popup** Om det är en Cacheträff i Edge-POP, är ursprunget saknas. <br> **Stor begäran** Om det begärda innehållet är stort med flera segmenterade begär Anden som kommer tillbaka till ursprunget, motsvarar det här fältet den första begäran till ursprunget. Mer information finns i objekt segment. |

## <a name="health-probe-log"></a>Hälso avsöknings logg

Hälso avsöknings loggar innehåller loggning för varje misslyckad avsökning för att hjälpa dig att diagnostisera ditt ursprung.Loggarna ger dig information som du kan använda för att ta tillbaka ursprunget till tjänsten. Vissa scenarier den här loggen kan vara användbar för är:

* Du har märkt att Azures frontend-trafik har skickats till några av ursprungen. Till exempel är det bara tre av fyra ursprung som tar emot trafik. Du vill veta om ursprungen tar emot avsökningar och inte orsaken till problemet.  

* Du noterade att ursprungs hälsan% är lägre än förväntat och vill veta vilket ursprung som misslyckades och orsaken till problemet.

### <a name="health-probe-log-properties"></a>Egenskaper för hälso avsöknings logg

Varje hälso avsöknings logg har följande schema.

| Egenskap | Beskrivning |
| --- | --- |
| HealthProbeId  | Ett unikt ID som identifierar begäran. |
| Tid | Avsöknings slut tid |
| HttpMethod | HTTP-metod som används av hälso avsöknings förfrågan. Värdena inkluderar GET och HEAD, baserat på konfigurationer för hälso avsökning. |
| Resultat | Status för hälso avsökning till ursprung, värde innehåller lyckad och annan fel text. |
| HttpStatusCode  | HTTP-statuskod som returnerades från ursprunget. |
| ProbeURL (mål) | Den fullständiga URL: en för ursprunget där förfrågningar skickas. Bestående av schemat, värd rubriken, sökvägen och frågesträngen. |
| OriginName  | Ursprung där förfrågningar skickas. Det här fältet hjälper till att hitta intressanta ursprung om ursprunget är konfigurerat för FDQN. |
| POP | Edge-pop, som skickade ut avsöknings förfrågan. |
| Ursprungligt IP | Målets ursprungliga IP-adress. Det här fältet är användbart när du vill hitta intressanta ursprung om du konfigurerar ursprung med FDQN. |
| TotolaLatency | Tiden från AFDX Edge skickar begäran till ursprung till den tidpunkt då det senaste svaret till AFDX Edge skickas. |
| ConnectionLatency| Den varaktighets tid som krävs för att konfigurera TCP-anslutningen för att skicka begäran om HTTP-avsökning till ursprung. | 
| DNSResolution-svars tid | Varaktighets åtgången för DNS-matchning om ursprunget är konfigurerat att vara en FDQN i stället för IP. Ej tillämpligt om ursprunget är konfigurerat för IP. |

### <a name="health-probe-log-sample-in-json"></a>Exempel på hälso avsöknings logg i JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Aktivitetsloggar

Aktivitets loggar innehåller information om de åtgärder som utförts i Azures främre dörr standard/Premium. Loggarna innehåller information om vad, vem och när en Skriv åtgärd utfördes på Azures front dörr. 

> [!NOTE]
> Aktivitets loggar omfattar inte GET-åtgärder. De omfattar inte heller åtgärder som du utför med hjälp av antingen Azure Portal eller ursprungligt hanterings-API. 

Få åtkomst till aktivitets loggar i din front dörr eller alla loggar för dina Azure-resurser i Azure Monitor.

Så här visar du aktivitetsloggar:

1. Välj din profil för front dörren.

1. Välj **aktivitets logg.**

1. Välj en filtrerings omfattning och välj sedan **Använd**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure frontend standard/Premium-rapporter (för hands version)](how-to-reports.md).
- Lär dig mer om [Azure front dörr standard/Premium (för hands version) mått för real tids övervakning](how-to-monitor-metrics.md).
