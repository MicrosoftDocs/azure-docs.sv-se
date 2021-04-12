---
title: 'Skriv över HTTP-sidhuvuden och URL: en med Azure Application Gateway | Microsoft Docs'
description: 'Den här artikeln innehåller en översikt över hur du skriver om HTTP-rubriker och URL: er i Azure Application Gateway'
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: 3e7bdc92dc6268c712eecbd69ff014e2229b3b84
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490972"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Skriv om HTTP-sidhuvuden och URL: en med Application Gateway

 Med Application Gateway kan du skriva om det valda innehållet i begär Anden och svar. Med den här funktionen kan du översätta URL: er, fråga sträng parametrar och ändra begäran och svarshuvuden. Du kan också lägga till villkor för att se till att URL: en eller de angivna rubrikerna bara skrivs om när vissa villkor är uppfyllda. Dessa villkor baseras på informationen för begäran och svar.

>[!NOTE]
>Funktioner för HTTP-huvud-och URL-omskrivning är bara tillgängliga för [Application Gateway v2-SKU: n](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Omskrivnings typer som stöds

### <a name="request-and-response-headers"></a>Sidhuvuden för begäran och svar

HTTP-huvuden låter en klient och Server skicka ytterligare information med en begäran eller ett svar. Genom att skriva om dessa rubriker kan du utföra viktiga uppgifter, till exempel lägga till säkerhetsrelaterade huvud fält som HSTS/X-XSS-Protection, ta bort svars huvud fält som kan avslöja känslig information och ta bort portinformation från X-vidarebefordrade-för-rubriker.

Med Application Gateway kan du lägga till, ta bort eller uppdatera HTTP-huvuden för begäran och svar när begäran- och svarspaketen flyttas mellan klientens och serverdelens pooler.

Information om hur du skriver om begäran och svarshuvuden med Application Gateway som använder Azure Portal finns [här](rewrite-url-portal.md).

![bild](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Rubriker som stöds**

Du kan skriva om alla huvuden i begär Anden och svar, förutom för anslutningen och uppgraderings rubriker. Du kan också använda Application Gateway för att skapa anpassade huvuden och lägga till dem i de begär Anden och svar som dirigeras genom den.

### <a name="url-path-and-query-string"></a>URL-sökväg och frågesträng

Med funktionen för URL-omskrivning i Application Gateway kan du:

* Skriv om värd namnet, sökvägen och frågesträngen för begärd URL 

* Välj att skriva om URL: er för alla begär Anden på en lyssnare eller bara de begär Anden som matchar ett eller flera av de villkor som du anger. Dessa villkor baseras på egenskaperna för begäran och svar (begäran, sidhuvud, svars rubrik och servervariabler).

* Välj att dirigera begäran (Välj backend-poolen) baserat antingen på den ursprungliga URL: en eller den omskrivna URL: en

Information om hur du skriver om URL: en med Application Gateway med Azure Portal finns [här](rewrite-url-portal.md).

![Diagram som beskriver processen för att skriva om en URL med Application Gateway.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Skriv om åtgärder

Du använder återskrivnings åtgärder för att ange den URL, de begärandehuvuden eller de svarshuvuden som du vill skriva om och det nya värdet som du vill skriva om dem till. Värdet för en URL eller en ny eller befintlig rubrik kan anges till följande typer av värden:

* Text
* Begär ande huvud. Om du vill ange ett rubrik för begäran måste du använda syntaxen {http_req_ *huvud*}
* Svarshuvud. Om du vill ange ett svars huvud måste du använda syntaxen {http_resp_ *huvud*}
* Server variabel. Om du vill ange en server variabel måste du använda syntaxen {var_ *serverVariable*}. Se listan över servervariabler som stöds
* En kombination av text, ett begär ande huvud, ett svars huvud och en server variabel. 

## <a name="rewrite-conditions"></a>Skriv om villkor

Du kan använda omskrivnings villkor, en valfri konfiguration för att utvärdera innehållet i HTTP (S)-begär Anden och svar och bara utföra en omskrivning när ett eller flera villkor är uppfyllda. Programgatewayen använder dessa typer av variabler för att utvärdera innehållet i begär Anden och svar:

* HTTP-huvuden i begäran
* HTTP-rubriker i svaret
* Application Gateway servervariabler

Du kan använda ett villkor för att utvärdera om en angiven variabel finns, om en angiven variabel matchar ett specifikt värde, eller om en angiven variabel matchar ett specifikt mönster. 


### <a name="pattern-matching"></a>Mönster matchning 

Application Gateway använder reguljära uttryck för mönster matchning i villkoret. Du kan använda [pcre-biblioteket (perl-kompatibla reguljära uttryck)](https://www.pcre.org/) för att konfigurera mönster matchning för reguljära uttryck i villkoren. Mer information om syntax för reguljära uttryck finns i [huvud sidan för vanliga](https://perldoc.perl.org/perlre.html)uttryck i perl.

### <a name="capturing"></a>Övervakning

Om du vill avbilda en under sträng för senare användning sätter du parenteser runt det under mönster som matchar det i villkors regex definitionen. Det första paren tes paret lagrar dess del sträng i 1, det andra paret i 2 och så vidare. Du kan använda så många parenteser som du vill. Perl håller bara på att definiera fler numrerade variabler så att du kan representera dessa insamlade strängar. Några exempel från [Ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d) (\d)/# matcha två siffror, samla in dem i grupper 1 och 2 

* /(\d +)/# matcha en eller flera siffror, samla in dem i grupp 1 

* /(\d) +/# matcha en siffra en eller flera gånger, samla in den sista i grupp 1

När du har samlat in kan du referera till dem i den angivna åtgärden med följande format:

* För registrering av begär ande huvud måste du använda {http_req_headerName_groupNumber}. Till exempel {http_req_User-Agent_1} eller {http_req_User-Agent_2}
* För en registrering av svars huvud måste du använda {http_resp_headerName_groupNumber}. Till exempel {http_resp_Location_1} eller {http_resp_Location_2}
* För en server variabel måste du använda {var_serverVariableName_groupNumber}. Till exempel {var_uri_path_1} eller {var_uri_path_2}

Om du vill använda hela värdet ska du inte nämna numret. Använd bara formatet {http_req_headerName} osv. utan groupNumber.

## <a name="server-variables"></a>Servervariabler

Application Gateway använder servervariabler för att lagra användbar information om servern, anslutningen till klienten och den aktuella begäran på anslutningen. Exempel på information som lagras är klientens IP-adress och webbläsarens typ. Servervariabler ändras dynamiskt, till exempel när en ny sida läses in eller när ett formulär publiceras. Du kan använda dessa variabler för att utvärdera Skriv villkor och skriva om rubriker. För att kunna använda värdet för servervariabler för att skriva om rubriker måste du ange dessa variabler i syntaxen {var_ *serverVariableName*}

Application Gateway stöder följande servervariabler:

|   Variabelnamn    |                   Beskrivning                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Det X-vidarebefordrade – för fältet för klient begär ande huvud med `client_ip` variabeln (se förklaringen senare i den här tabellen) som läggs till i formatet IP1, IP2, IP3 och så vidare. Om fältet X-forwarded inte finns i klient begär ande huvudet `add_x_forwarded_for_proxy` är variabeln lika med `$client_ip` variabeln.   Den här variabeln är särskilt användbar när du vill skriva om den X-vidarebefordrade-för-rubrik som angetts av Application Gateway så att sidhuvudet endast innehåller IP-adressen utan portinformation. |
| ciphers_supported         | En lista över de chiffer som stöds av klienten.               |
| ciphers_used              | Den sträng med chiffer som används för en upprättad TLS-anslutning. |
| client_ip                 | IP-adressen för den klient som Application Gateway tog emot begäran från. Om det finns en omvänd proxy före programgatewayen och den ursprungliga klienten, `client_ip` returnerar IP-adressen för den omvända proxyn. |
| client_port               | Klient porten.                                             |
| client_tcp_rtt            | Information om klientens TCP-anslutning. Tillgängligt på system som har stöd för alternativet TCP_INFO socket. |
| client_user               | När HTTP-autentisering används anges användar namnet för autentisering. |
| värd                      | I den här prioritetsordningen: värd namnet från begär ande raden, värd namnet från fältet värd begär ande huvud eller Server namnet som matchar en begäran. Exempel: i begäran `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` är värd värdet `contoso.com` |
| cookie_ *namn*             | Cookie- *namn* .                                           |
| http_method               | Den metod som används för att göra URL-begäran. Till exempel GET eller POST. |
| http_status               | Sessionens status. Till exempel 200, 400 eller 403.           |
| http_version              | Protokollet för begäran. Vanligt vis HTTP/1.0, HTTP/1.1 eller HTTP/2.0. |
| query_string              | Listan över variabel/värde-par som följer "?" i den begärda URL: en. Exempel: i begäran `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` visas QUERY_STRING värde `id=123&title=fabrikam` |
| received_bytes            | Längden på begäran (inklusive raden för begäran, sidhuvud och brödtext). |
| request_query             | Argumenten på raden för begäran.                           |
| request_scheme            | Begär ande schema: http eller https.                           |
| request_uri               | Fullständig URI för ursprunglig begäran (med argument). Exempel: i begäran `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` visas REQUEST_URI värde `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Antalet byte som har skickats till en klient.                        |
| server_port               | Porten för den server som godkände en begäran.              |
| ssl_connection_protocol   | Protokollet för en etablerad TLS-anslutning.               |
| ssl_enabled               | "On" om anslutningen fungerar i TLS-läge. Annars är en tom sträng. |
| uri_path                  | Identifierar den angivna resursen i värden som webb klienten vill ha åtkomst till. Detta är en del av URI: n för begäran utan argumenten. Exempel: i begäran `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` visas uri_path värde `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Variabler för ömsesidig autentisering av Server (för hands version)

Application Gateway stöder följande servervariabler för scenarier med ömsesidig autentisering. Använd dessa servervariabler på samma sätt som ovan med de andra servervariabler. 

|   Variabelnamn    |                   Beskrivning                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | Klient certifikatet i PEM-format för en etablerad SSL-anslutning. |
| client_certificate_end_date| Slutdatumet för klient certifikatet. |
| client_certificate_fingerprint| SHA1-finger avtryck för klient certifikatet för en etablerad SSL-anslutning. |
| client_certificate_issuer | Strängen "Issuer DN" för klient certifikatet för en etablerad SSL-anslutning. |
| client_certificate_serial | Serie numret för klient certifikatet för en etablerad SSL-anslutning.  |
| client_certificate_start_date| Start datumet för klient certifikatet. |
| client_certificate_subject| Strängen "subject DN" för klient certifikatet för en etablerad SSL-anslutning. |
| client_certificate_verification| Resultatet av verifieringen av klient certifikatet: *lyckades*, *misslyckades: <reason>* eller *inget* om ett certifikat saknas. | 

## <a name="rewrite-configuration"></a>Skriv om konfigurationen

Om du vill konfigurera en omskrivnings regel måste du skapa en regel uppsättning för omskrivning och lägga till konfigurationen för den omskrivna regeln i den.

En regel uppsättning för omskrivning innehåller:

* **Regel koppling för att begära Routning:** Den omskrivna konfigurationen är kopplad till käll lyssnaren via regeln för routning. När du använder en regel för grundläggande routning associeras den omskrivnings konfiguration som är kopplad till en käll lyssnare och är en omskrivning av global rubrik. När du använder en regel för väg-baserad routning definieras konfigurationen för att skriva om i sökvägen till URL-sökvägen. I så fall gäller det bara för det angivna Sök vägs området på en plats. Du kan skapa flera omskrivnings uppsättningar och tillämpa varje skrivnings uppsättning på flera lyssnare. Men du kan endast använda en omskrivnings uppsättning för en speciell lyssnare.

* **Omskrivnings villkor**: det är en valfri konfiguration. Omskrivnings villkor utvärderar innehållet i HTTP (S)-begär Anden och svar. Återskrivning görs om HTTP (S)-begäran eller-svaret matchar omskrivnings villkoret. Om du associerar fler än ett villkor med en åtgärd sker åtgärden endast när alla villkor är uppfyllda. Med andra ord är åtgärden ett logiskt och en åtgärd.

* **Typ** av omskrivning: det finns tre typer av Skriv åtgärder tillgängliga:
   * Omskrivning av begärandehuvuden 
   * Svarshuvuden skrivs om.
   * Skriver om URL: en omskrivnings-URL har 3 komponenter
      * **URL-sökväg**: det värde som sökvägen ska skrivas om till. 
      * **URL-frågesträng**: värdet som frågesträngen ska skrivas om till. 
      * **Sök igenom Sök vägs kartan igen**: används för att avgöra om sökvägen till URL-sökvägen ska utvärderas igen eller inte. Om den är omarkerad kommer den ursprungliga URL-sökvägen att användas för att matcha Sök vägs mönstret i URL-sökvägen. Om värdet är true, kommer URL-sökvägen att utvärderas igen för att kontrol lera matchningen med den omskrivna sökvägen. Genom att aktivera den här växeln kan du dirigera begäran till en annan backend-pool efter omskrivning.

## <a name="rewrite-configuration-common-pitfall"></a>Skriv över vanliga Pitfall för konfiguration

* Det går inte att aktivera "reevaluate Path Map" för grundläggande regler för routning av förfrågningar. Detta är för att förhindra oändlig utvärderings slinga för en grundläggande routningsprincip.

* Det måste finnas minst 1 regel för villkorlig Rewrite-regel eller 1-omskrivning som inte har "reevaluate Sök vägs karta" aktive rad för vägbaserade routningsregler för att förhindra oändlig utvärderings slinga för en Sök vägs baserad routningsregler.

* Inkommande begär Anden avslutas med en 500-felkod i händelse av att en loop skapas dynamiskt baserat på klientens indata. Application Gateway fortsätter att betjäna andra begär Anden utan att det är någon försämring i ett sådant scenario.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Använd URL-omskrivning eller omskrivning av värd huvud med brand vägg för webbaserade program (WAF_v2 SKU)

När du konfigurerar URL-omskrivning eller omskrivning av värd huvud sker utvärderingen av WAF efter ändringen av begär ande huvudet eller URL-parametrarna (efter omskrivning). När du tar bort konfigurationen av URL-omskrivning eller konfiguration av värd huvud på din Application Gateway, görs utvärderingen av WAF innan huvud skrivningen (förskriven). Den här ordningen säkerställer att WAF-regler tillämpas på den slutgiltiga begäran som skulle tas emot av din backend-pool.

Anta till exempel att du har följande huvud regel för att skriva över rubriken `"Accept" : "text/html"` – om värdet för rubrik `"Accept"` är lika med `"text/html"` , skriver du om värdet till `"image/png"` .

Här, med enbart omskrivning av huvuden, är utvärderingen av WAF slutförd `"Accept" : "text/html"` . Men när du konfigurerar URL-omskrivning eller omskrivning av värd huvud görs en utvärdering av WAF `"Accept" : "image/png"` .

>[!NOTE]
> Åtgärder för URL-omskrivning förväntas orsaka en mindre ökning av processor användningen av WAF-Application Gateway. Vi rekommenderar att du övervakar [processor användnings måttet](high-traffic-support.md) under en kort tids period när du har aktiverat reglerna för att skriva om URL: er på WAF Application Gateway.

### <a name="common-scenarios-for-header-rewrite"></a>Vanliga scenarier för header-omskrivning

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Ta bort portinformation från det X-vidarebefordrade – för sidhuvudet

Application Gateway infogar en X-vidarebefordrad-för-rubrik i alla begär Anden innan den vidarebefordrar begär anden till Server delen. Den här rubriken är en kommaavgränsad lista med IP-portar. Det kan finnas scenarier där backend-servrarna bara behöver rubrikerna för att innehålla IP-adresser. Du kan använda omskrivning av huvuden för att ta bort portinformation från den X-vidarebefordrade-for-rubriken. Ett sätt att göra detta är att ange rubriken till add_x_forwarded_for_proxy Server-variabeln. Du kan också använda variabeln client_ip:

![Ta bort port](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Ändra en URL för omdirigering

När ett backend-program skickar ett svar på omdirigering kanske du vill omdirigera klienten till en annan URL än den som anges av Server dels programmet. Du kanske till exempel vill göra detta när en app service finns bakom en Programgateway och kräver att klienten utför en omdirigering till dess relativa sökväg. (Till exempel en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2.)

Eftersom App Service är en tjänst för flera innehavare använder den värd rubriken i begäran för att dirigera begäran till rätt slut punkt. App Services har ett standard domän namn på *. azurewebsites.net (säg contoso.azurewebsites.net) som skiljer sig från programgatewayens domän namn (t. ex. contoso.com). Eftersom den ursprungliga begäran från klienten har Application gateways domän namn (contoso.com) som värdnamn, ändrar programgatewayen värd namnet till contoso.azurewebsites.net. Den här ändringen görs så att App Service kan dirigera begäran till rätt slut punkt.

När App Service skickar ett svar för omdirigering använder den samma värdnamn i plats huvudet för sitt svar som det i den begäran som den tar emot från programgatewayen. Klienten kommer därför att göra begäran direkt till `contoso.azurewebsites.net/path2` i stället för att gå igenom Application Gateway ( `contoso.com/path2` ). Att kringgå Application Gateway är inte önskvärt.

Du kan lösa det här problemet genom att ange värd namnet i plats rubriken till Application gatewayens domän namn.

Här följer stegen för att ersätta värd namnet:

1. Skapa en Rewrite-regel med ett villkor som utvärderar om plats rubriken i svaret innehåller azurewebsites.net. Ange mönstret `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Utför en åtgärd för att skriva om plats rubriken så att den har Application Gateway-värdnamnet. Gör detta genom att ange `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` som rubrik värde. Du kan också använda Server variabeln `host` för att ange värd namnet som matchar den ursprungliga begäran.

![Ändra plats rubrik](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementera säkerhets-HTTP-huvuden för att förhindra sårbarheter

Du kan åtgärda flera säkerhets problem genom att implementera nödvändiga huvuden i program svaret. Dessa säkerhets rubriker inkluderar X-XSS-skydd, strikt-Transport-Security och Content-Security-Policy. Du kan använda Application Gateway för att ange de här rubrikerna för alla svar.

![Säkerhets huvud](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Ta bort oönskade rubriker

Du kanske vill ta bort huvuden som avslöjar känslig information från ett HTTP-svar. Till exempel kanske du vill ta bort information som server namn, operativ system eller biblioteks information för Server delen. Du kan använda Application Gateway för att ta bort dessa huvuden:

![Tar bort rubrik](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Sök efter en rubriks förekomst

Du kan utvärdera en HTTP-begäran eller ett svars huvud för förekomst av en huvud-eller Server variabel. Den här utvärderingen är användbar när du vill utföra en skrivskyddad rubrik endast när det finns en viss rubrik.

![Kontrollerar närvaron av ett sidhuvud](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Vanliga scenarier för URL-omskrivning

#### <a name="parameter-based-path-selection"></a>Val av parameter baserat Sök väg

Om du vill göra scenarier där du vill välja backend-poolen baserat på värdet för en rubrik, en del av URL: en eller frågesträngen i begäran, kan du använda kombinationen av URL-omskrivning och vägs-baserad routning.  Om du till exempel har en shopping webbplats och produkt kategorin skickas som frågesträng i URL: en och du vill dirigera begäran till backend-servern baserat på frågesträngen:

**Steg 1:**  Skapa en sökväg – karta som visas på bilden nedan

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL-omskrivning scenario 1-1.":::

**Steg 2 (a):** Skapa en omskrivnings uppsättning som har tre omskrivnings regler: 

* Den första regeln har ett villkor som kontrollerar *QUERY_STRING*  -variabeln för *kategori = skor* och har en åtgärd som skriver om URL-sökvägen till/*listing1* och har **omutvärderat Sök vägs mappning** aktive rad

* Den andra regeln har ett villkor som kontrollerar *QUERY_STRING*  -variabeln för *kategori = påsar* och har en åtgärd som skriver om URL-sökvägen till/*listing2*  och har **omutvärderat Sök vägs kartan** aktiverat

* Den tredje regeln har ett villkor som kontrollerar *QUERY_STRING*  -variabeln för *Category = Accessories* och har en åtgärd som skriver om URL-sökvägen till/*listing3* och har **omutvärderat Sök vägs mappning** aktive rad

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL-omskrivning scenario 1-2.":::

 

**Steg 2 (b):** Associera denna omarbetnings uppsättning med standard Sök vägen för ovanstående Sök vägs regel

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL-omskrivning scenario 1-3.":::

Om användaren begär *contoso.com/Listing?Category=any* matchas den nu med standard Sök vägen eftersom inget av Sök vägs mönstren i Sök vägs kartan (/listing1,/listing2,/listing3) matchar. Eftersom du kopplade ovanstående omskrivnings uppsättning med den här sökvägen kommer den här omskrivnings uppsättningen att utvärderas. Eftersom frågesträngen inte matchar villkoret i någon av reglerna för 3-omskrivning i den här Skriv åtgärden kommer ingen omskrivning att ske, och därför kommer begäran att dirigeras oförändrad till Server delen som är kopplad till standard Sök vägen (som är *GenericList*).

 Om användaren begär *contoso.com/Listing?Category=Shoes* så matchas standard Sök vägen igen. I det här fallet kommer dock villkoret i den första regeln att matchas och därför körs åtgärden som är associerad med villkoret, vilket kommer att skriva om URL-sökvägen till/*listing1*  och sedan utvärdera sökvägen till kartan igen. När sökvägen – kartan har utvärderats på nytt matchar begäran nu den sökväg som är kopplad till mönstret */listing1* och begäran dirigeras till Server delen som är kopplad till det här mönstret, som är ShoesListBackendPool

>[!NOTE]
>Det här scenariot kan utökas till alla sidhuvuds-eller cookie-värden, URL-sökvägar, frågesträngar eller servervariabler baserat på villkoret som definierats och innebär att du kan dirigera begär Anden baserat på dessa villkor.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Skriv om Frågesträngens parametrar baserat på URL

Överväg ett scenario för en shopping webbplats där den synliga länken för användaren bör vara enkel och läslig, men backend-servern behöver parametrarna för frågesträngen för att visa rätt innehåll.

I så fall kan Application Gateway avbilda parametrar från URL: en och lägga till frågesträngar nyckel/värde-par från URL: en. Anta till exempel att användaren vill skriva om, `https://www.contoso.com/fashion/shirts` till `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` , det kan uppnås genom följande URL-omskrivning av konfigurationen.

**Villkor** – om Server variabeln `uri_path` är lika med mönstret `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL-omskrivning scenario 2-1.":::

**Åtgärd** – ange URL-sökvägen till `buy.aspx` och frågesträng till `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL-omskrivning scenario 2-2.":::

En steg-för-steg-guide för att uppnå scenariot som beskrivs ovan finns i [omskrivnings-URL med Application Gateway med Azure Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>URL-omskrivning vs URL omdirigering

Om URL-omskrivning görs om, Application Gateway skriver om URL: en innan begäran skickas till Server delen. Detta påverkar inte vad användarna ser i webbläsaren eftersom ändringarna är dolda från användaren.

I händelse av URL-omdirigering skickar Application Gateway ett omdirigerings svar till klienten med den nya URL: en. Som i sin tur kräver att klienten skickar en begäran till den nya URL: en som finns i omdirigeringen. URL som användaren ser i webbläsaren kommer att uppdateras till den nya URL: en

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Skriv om vs Redirect.":::

## <a name="limitations"></a>Begränsningar

- Om ett svar har fler än en rubrik med samma namn, kommer de andra rubrikerna att tas bort om du skriver om värdet för en av dessa huvuden. Detta kan vanligt vis inträffa med Set-Cookie sidhuvud eftersom du kan ha fler än ett Set-Cookie-huvud i ett svar. Ett sådant scenario är när du använder en app service med en Application Gateway och har konfigurerat cookie-baserad mappning mellan sessioner på Application Gateway. I det här fallet kommer svaret att innehålla två Set-Cookie huvuden: en som används av App Service, till exempel: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` och en annan för Application Gateway-tillhörighet, till exempel `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Om du skriver om en av Set-Cookie rubrikerna i det här scenariot kan det leda till att den andra Set-Cookies rubriken tas bort från svaret.
- Omskrivningar stöds inte när Application Gateway har kon figurer ATS för att omdirigera begär Anden eller för att visa en anpassad felsida.
- Rubrik namn får innehålla alfanumeriska tecken och vissa symboler som definieras i [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Vi stöder för närvarande inte under streck (_) specialtecken i rubrik namn.
- Det går inte att skriva om anslutnings-och uppgraderings rubriker

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du skriver om HTTP-huvuden med Application Gateway med Azure Portal](rewrite-http-headers-portal.md)
- [Lär dig hur du skriver om URL: en med Application Gateway med Azure Portal](rewrite-url-portal.md)
