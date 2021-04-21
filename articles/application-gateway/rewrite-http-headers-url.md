---
title: Skriva om HTTP-huvuden och URL med Azure Application Gateway | Microsoft Docs
description: Den här artikeln innehåller en översikt över omskrivning av HTTP-huvuden och URL i Azure Application Gateway
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: b7cf7c98e71da215eb30dcab556a88d6d2701591
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789455"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Skriva om HTTP-huvuden och URL med Application Gateway

Application Gateway kan du skriva om valt innehåll för begäranden och svar. Med den här funktionen kan du översätta URL:er, frågesträngsparametrar samt ändra begäran- och svarshuvuden. Du kan också lägga till villkor för att säkerställa att URL:en eller de angivna rubrikerna skrivs om endast när vissa villkor uppfylls. Dessa villkor baseras på informationen för begäran och svar.

>[!NOTE]
>Funktioner för HTTP-huvud- och URL-omskrivning är endast tillgängliga [för Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Omskrivningstyper som stöds

### <a name="request-and-response-headers"></a>Sidhuvuden för begäran och svar

MED HTTP-huvuden kan en klient och server skicka ytterligare information med en begäran eller ett svar. Genom att skriva om dessa huvuden kan du utföra viktiga uppgifter, till exempel lägga till säkerhetsrelaterade huvudfält som HSTS/X-XSS-Protection, ta bort svarshuvudfält som kan avslöja känslig information och ta bort portinformation från X-Forwarded-For-huvuden.

Med Application Gateway kan du lägga till, ta bort eller uppdatera HTTP-huvuden för begäran och svar när begäran- och svarspaketen flyttas mellan klientens och serverdelens pooler.

Information om hur du skriver om begärande- och svarshuvuden med Application Gateway med Azure Portal finns [här.](rewrite-url-portal.md)

![bild](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Rubriker som stöds**

Du kan skriva om alla huvuden i begäranden och svar, förutom anslutnings- och uppgraderingshuvudena. Du kan också använda programgatewayen för att skapa anpassade huvuden och lägga till dem i de begäranden och svar som dirigeras genom den.

### <a name="url-path-and-query-string"></a>URL-sökväg och frågesträng

Med url-omskrivningsfunktion i Application Gateway kan du:

* Skriva om värdnamnet, sökvägen och frågesträngen för begärande-URL:en 

* Välj att skriva om URL:en för alla begäranden på en lyssnare eller bara de begäranden som matchar ett eller flera av de villkor som du anger. Dessa villkor baseras på egenskaperna för begäran och svar (begäran, huvud, svarshuvud och servervariabler).

* Välj att dirigera begäran (välj backend-poolen) baserat på antingen den ursprungliga URL:en eller den omskrivna URL:en

Information om hur du skriver om URL med Application Gateway med Azure Portal finns [här](rewrite-url-portal.md).

![Diagram som beskriver processen för att skriva om en URL med Application Gateway.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Skriva om åtgärder

Du använder omskrivningsåtgärder för att ange URL, begärandehuvuden eller svarshuvuden som du vill skriva om och det nya värdet som du vill skriva om dem till. Värdet för en URL eller en ny eller befintlig rubrik kan anges till följande typer av värden:

* Text
* Begärandehuvud. Om du vill ange ett begärandehuvud måste du använda syntaxen {http_req_ *headerName*}
* Svarshuvud. Om du vill ange ett svarshuvud måste du använda syntaxen {http_resp_ *headerName*}
* Servervariabel. Om du vill ange en servervariabel måste du använda syntaxen {var_ *serverVariable*}. Se listan över servervariabler som stöds
* En kombination av text, ett begärandehuvud, ett svarshuvud och en servervariabel. 

## <a name="rewrite-conditions"></a>Skriva om villkor

Du kan använda omskrivningsvillkor, en valfri konfiguration, för att utvärdera innehållet i HTTP(S)-begäranden och -svar och endast skriva om när ett eller flera villkor är uppfyllda. Application Gateway använder dessa typer av variabler för att utvärdera innehållet i begäranden och svar:

* HTTP-huvuden i begäran
* HTTP-huvuden i svaret
* Application Gateway servervariabler

Du kan använda ett villkor för att utvärdera om en angiven variabel finns, om en angiven variabel matchar ett visst värde eller om en angiven variabel matchar ett visst mönster. 


### <a name="pattern-matching"></a>Mönstermatchning 

Application Gateway använder reguljära uttryck för mönstermatchning i villkoret. Du kan använda [PCRE-biblioteket (Perl Compatible Regular Expressions)](https://www.pcre.org/) för att konfigurera matchning av mönster för reguljära uttryck i villkoren. Mer information om syntax för reguljära uttryck finns på huvudsidan [för Reguljära Perl-uttryck.](https://perldoc.perl.org/perlre.html)

### <a name="capturing"></a>Fånga

Om du vill avbilda en delsträng för senare användning lägger du parenteser runt undermönstret som matchar det i villkorets regex-definition. Det första parentesparet lagrar sin delsträng i 1, det andra paret i 2 och så vidare. Du kan använda så många parenteser som du vill; Perl fortsätter bara att definiera fler numrerade variabler så att du kan representera dessa avbildade strängar. Några exempel från [referens :](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm) 

*  /(\d)(\d)/ # Matcha två siffror och samla in dem i grupperna 1 och 2 

* /(\d+)/ # Matcha en eller flera siffror och samla in dem i grupp 1 

* /(\d)+/ # Matcha en siffra en eller flera gånger och samla in den sista i grupp 1

När de har avbildats kan du referera till dem i åtgärdsuppsättningen med hjälp av följande format:

* För en begärandehuvudinfångst måste du använda {http_req_headerName_groupNumber}. Till exempel {http_req_User-Agent_1} eller {http_req_User-Agent_2}
* För en svarshuvudinfångst måste du använda {http_resp_headerName_groupNumber}. Till exempel {http_resp_Location_1} eller {http_resp_Location_2}
* För en servervariabel måste du använda {var_serverVariableName_groupNumber}. Till exempel {var_uri_path_1} eller {var_uri_path_2}

Om du vill använda hela värdet bör du inte nämna talet. Använd bara formatet {http_req_headerName}, osv. utan groupNumber.

## <a name="server-variables"></a>Servervariabler

Application Gateway använder servervariabler för att lagra användbar information om servern, anslutningen till klienten och den aktuella begäran om anslutningen. Exempel på information som lagras är klientens IP-adress och webbläsartyp. Servervariabler ändras dynamiskt, till exempel när en ny sida läses in eller när ett formulär publiceras. Du kan använda dessa variabler för att utvärdera omskrivningsvillkor och skriva om rubriker. För att kunna använda värdet för servervariabler för att skriva om rubriker måste du ange dessa variabler i syntaxen {var_ *serverVariableName*}

Application Gateway stöder följande servervariabler:

|   Variabelnamn    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Fältet X-Forwarded-For client request header med variabeln (se förklaring senare i den här tabellen) tillagt i `client_ip` formatet IP1, IP2, IP3 och så vidare. Om fältet X-Forwarded-For inte finns i klientbegärandehuvudet är `add_x_forwarded_for_proxy` variabeln lika med `$client_ip` variabeln .   Den här variabeln är särskilt användbar när du vill skriva om huvudet X-Forwarded-For som angetts av Application Gateway så att rubriken bara innehåller IP-adressen utan portinformationen. |
| ciphers_supported         | En lista över chiffer som stöds av klienten.               |
| ciphers_used              | Strängen med chiffer som används för en upprättad TLS-anslutning. |
| client_ip                 | IP-adressen för klienten som programgatewayen tog emot begäran från. Om det finns en omvänd proxy före programgatewayen och den ursprungliga klienten returnerar `client_ip` IP-adressen för den omvända proxyn. |
| client_port               | Klientporten.                                             |
| client_tcp_rtt            | Information om klientens TCP-anslutning. Tillgängligt på system som stöder TCP_INFO socket. |
| client_user               | När HTTP-autentisering används anges användarnamnet för autentisering. |
| värd                      | I den här prioritetsordningen: värdnamnet från begäranderaden, värdnamnet från fältet Värdbegärandehuvud eller det servernamn som matchar en begäran. Exempel: I begäran `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` är värdvärdet `contoso.com` |
| cookie_ *namn*             | Namnet *cookie.*                                           |
| http_method               | Den metod som används för att göra URL-begäran. Till exempel GET eller POST. |
| http_status               | Sessionsstatus. Till exempel 200, 400 eller 403.           |
| http_version              | Begärandeprotokollet. Vanligtvis HTTP/1.0, HTTP/1.1 eller HTTP/2.0. |
| query_string              | Listan med variabel-/värdepar som följer "?" i den begärda URL:en. Exempel: I begäran `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` query_string värdet `id=123&title=fabrikam` |
| received_bytes            | Längden på begäran (inklusive begäranderaden, rubriken och begärandetexten). |
| request_query             | Argumenten på begäranderaden.                           |
| request_scheme            | Begärandeschemat: http eller https.                           |
| request_uri               | Den fullständiga ursprungliga begärande-URI:n (med argument). Exempel: i begäran `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` request_uri värdet `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Antalet byte som skickats till en klient.                        |
| server_port               | Porten för den server som godkände en begäran.              |
| ssl_connection_protocol   | Protokollet för en upprättad TLS-anslutning.               |
| ssl_enabled               | "På" om anslutningen fungerar i TLS-läge. Annars en tom sträng. |
| uri_path                  | Identifierar den specifika resursen i värden som webbklienten vill ha åtkomst till. Detta är den del av begärande-URI:t utan argumenten. Exempel: I begäran `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` uri_path värdet `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Servervariabler för ömsesidig autentisering (förhandsversion)

Application Gateway stöder följande servervariabler för scenarier med ömsesidig autentisering. Använd dessa servervariabler på samma sätt som ovan med de andra servervariablerna. 

|   Variabelnamn    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | Klientcertifikatet i PEM-format för en upprättad SSL-anslutning. |
| client_certificate_end_date| Slutdatumet för klientcertifikatet. |
| client_certificate_fingerprint| SHA1-fingeravtrycket för klientcertifikatet för en upprättad SSL-anslutning. |
| client_certificate_issuer | Strängen "utfärdar-DN" för klientcertifikatet för en upprättad SSL-anslutning. |
| client_certificate_serial | Serienumret för klientcertifikatet för en upprättad SSL-anslutning.  |
| client_certificate_start_date| Startdatumet för klientcertifikatet. |
| client_certificate_subject| Strängen "ämnes-DN" för klientcertifikatet för en upprättad SSL-anslutning. |
| client_certificate_verification| Resultatet av verifieringen av klientcertifikatet: *SUCCESS*, *FAILED: <reason>*, eller *NONE* om ett certifikat inte fanns. | 

## <a name="rewrite-configuration"></a>Omskrivningskonfiguration

Om du vill konfigurera en omskrivningsregel måste du skapa en regeluppsättning för omskrivning och lägga till konfigurationen för omskrivningsregeln i den.

En regeluppsättning för omskrivning innehåller:

* **Koppling av begäranderoutningsregel:** Omskrivningskonfigurationen är associerad med källlyssnaren via routningsregeln. När du använder en grundläggande routningsregel associeras omskrivningskonfigurationen med en källlyssnare och är en global omskrivning av rubrik. När du använder en sökvägsbaserad routningsregel definieras omskrivningskonfigurationen på URL-sökvägsmappningen. I så fall gäller den endast för den specifika sökvägen för en plats. Du kan skapa flera omskrivningsuppsättningar och tillämpa varje omskrivningsuppsättning på flera lyssnare. Men du kan bara använda en omskrivningsuppsättning för en specifik lyssnare.

* **Omskrivningsvillkor: Det** är en valfri konfiguration. Omskrivningsvillkor utvärderar innehållet i HTTP(S)-begäranden och -svar. Omskrivningsåtgärden inträffar om HTTP(S)-begäran eller -svaret matchar omskrivningsvillkoret. Om du associerar mer än ett villkor med en åtgärd, sker åtgärden bara när alla villkor är uppfyllda. Med andra ord är åtgärden en logisk AND-åtgärd.

* **Skriv om: Det** finns tre typer av omskrivningar:
   * Skriva om begärandehuvuden 
   * Skriva om svarshuvuden
   * Skriva om URL-komponenter
      * **URL-sökväg:** Det värde som sökvägen ska skrivas om till. 
      * **URL-frågesträng:** Det värde som frågesträngen ska skrivas om till. 
      * **Omvärdera sökvägsmappning:** Används för att avgöra om URL-sökvägskartan ska utvärderas om eller inte. Om den behålls avmarkerad används den ursprungliga URL-sökvägen för att matcha sökvägsmönstret i URL-sökvägsmappningen. Om det är inställt på true (sant) utvärderas URL-sökvägsmappningen om för att kontrollera matchningen med den omskrivna sökvägen. Genom att aktivera den här växeln kan du dirigera begäran till en annan serverpool efter omskrivning.

## <a name="rewrite-configuration-common-pitfalls"></a>Vanliga fallgropar för att skriva om konfigurationen

* Aktivering av "Omvärdera sökvägskarta" tillåts inte för grundläggande regler för routning av förfrågningar. Detta är för att förhindra oändlig utvärderingsloop för en grundläggande routningsregel.

* Det måste finnas minst en regel för villkorlig omskrivning eller 1 omskrivningsregel som inte har "Utvärdera om sökvägsmappning" aktiverat för sökvägsbaserade routningsregler för att förhindra oändlig utvärderingsloop för en sökvägsbaserad routningsregel.

* Inkommande begäranden avslutas med en 500-felkod om en loop skapas dynamiskt baserat på klientindata. Den Application Gateway fortsätter att betjäna andra begäranden utan försämring i ett sådant scenario.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Använda URL-omskrivning eller omskrivning av värdhuvud med Web Application Firewall (WAF_v2 SKU)

När du konfigurerar url-omskrivning eller omskrivning av värdhuvud sker WAF-utvärderingen efter ändringen av begärandehuvudet eller URL-parametrarna (efter omskrivning). Och när du tar bort konfigurationen för omskrivning av URL-adress eller värdhuvud på din Application Gateway görs WAF-utvärderingen innan rubriken skrivs om (förskrivning). Den här ordningen säkerställer att WAF-reglerna tillämpas på den slutliga begäran som tas emot av din backend-pool.

Säg till exempel att du har följande regel för omskrivning av rubriken – om värdet för rubriken är lika med skriver du om `"Accept" : "text/html"` `"Accept"` värdet till `"text/html"` `"image/png"` .

Här, när endast rubrikreskrivning har konfigurerats, görs WAF-utvärderingen på `"Accept" : "text/html"` . Men när du konfigurerar URL-omskrivning eller omskrivning av värdhuvud görs WAF-utvärderingen på `"Accept" : "image/png"` .

>[!NOTE]
> URL-omskrivningsåtgärder förväntas orsaka en mindre ökning av CPU-användningen av waf-Application Gateway. Vi rekommenderar att du övervakar [måttet för CPU-användning](high-traffic-support.md) under en kort tidsperiod efter att du har aktivera REGLER för URL-omskrivning på din WAF-Application Gateway.

### <a name="common-scenarios-for-header-rewrite"></a>Vanliga scenarier för omskrivning av huvud

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Ta bort portinformation från rubriken X-Forwarded-For

Application Gateway in en X-Forwarded-For-rubrik i alla begäranden innan den vidarebefordrar begäranden till backend. Det här huvudet är en kommaavgränsad lista över IP-portar. Det kan finnas scenarier där backend-servrarna bara behöver huvudena för att innehålla IP-adresser. Du kan använda omskrivning av sidhuvud för att ta bort portinformationen från huvudet X-Forwarded-For. Ett sätt att göra detta är att ange huvudet till add_x_forwarded_for_proxy servervariabeln. Du kan också använda variabeln client_ip:

![Ta bort port](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Ändra en omdirigerings-URL

När ett backend-program skickar ett omdirigeringssvar kanske du vill omdirigera klienten till en annan URL än den som anges av backend-programmet. Du kanske till exempel vill göra detta när en apptjänst finns bakom en programgateway och kräver att klienten gör en omdirigering till den relativa sökvägen. (Till exempel en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2.)

Eftersom App Service är en tjänst för flera klienttjänster används värdhuvudet i begäran för att dirigera begäran till rätt slutpunkt. App Services har standarddomännamnet .azurewebsites.net (till exempel contoso.azurewebsites.net) som skiljer sig från programgatewayens domännamn \* (till exempel contoso.com). Eftersom den ursprungliga begäran från klienten har programgatewayens domännamn (contoso.com) som värdnamn, ändrar programgatewayen värdnamnet till contoso.azurewebsites.net. Den gör den här ändringen så att App Service kan dirigera begäran till rätt slutpunkt.

När App Service skickar ett omdirigeringssvar använder den samma värdnamn i platshuvudet för svaret som i den begäran som den tar emot från Programgatewayen. Klienten skickar därför begäran direkt till i stället `contoso.azurewebsites.net/path2` för att gå via programgatewayen ( `contoso.com/path2` ). Det är inte önskvärt att kringgå programgatewayen.

Du kan lösa det här problemet genom att ange värdnamnet i platsrubriken till programgatewayens domännamn.

Här är stegen för att ersätta värdnamnet:

1. Skapa en omskrivningsregel med ett villkor som utvärderar om platsrubriken i svaret innehåller azurewebsites.net. Ange mönstret `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Utför en åtgärd för att skriva om platsrubriken så att den har programgatewayens värdnamn. Gör detta genom att `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` ange som rubrikvärde. Du kan också använda servervariabeln för `host` att ange värdnamnet så att det matchar den ursprungliga begäran.

![Ändra platsrubrik](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementera http-säkerhetshuvuden för att förhindra sårbarheter

Du kan åtgärda flera säkerhetsproblem genom att implementera nödvändiga huvuden i programsvaret. Dessa säkerhetshuvuden omfattar X-XSS-Protection, Strict-Transport-Security och Content-Security-Policy. Du kan använda Application Gateway för att ange dessa huvuden för alla svar.

![Säkerhetsrubrik](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Ta bort oönskade huvuden

Du kanske vill ta bort huvuden som visar känslig information från ett HTTP-svar. Du kanske till exempel vill ta bort information som servernamnet, operativsystemet eller biblioteksinformationen. Du kan använda programgatewayen för att ta bort dessa huvuden:

![Ta bort sidhuvud](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Kontrollera förekomsten av en rubrik

Du kan utvärdera en HTTP-begäran eller ett svarshuvud för förekomsten av ett huvud eller en servervariabel. Den här utvärderingen är användbar när du bara vill skriva om en rubrik när en viss rubrik finns.

![Kontrollera förekomsten av en rubrik](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Vanliga scenarier för URL-omskrivning

#### <a name="parameter-based-path-selection"></a>Val av parameterbaserad sökväg

Om du vill utföra scenarier där du vill välja serverdelspoolen baserat på värdet för en rubrik, en del av URL:en eller frågesträngen i begäran kan du använda en kombination av url-omskrivningsfunktioner och sökvägsbaserad routning. Om du till exempel har en shoppingwebbplats och produktkategorin skickas som frågesträng i URL:en och du vill dirigera begäran till backend baserat på frågesträngen gör du följande:

**Steg 1:**  Skapa en sökvägskarta enligt bilden nedan

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL-omskrivningsscenario 1–1.":::

**Steg 2 (a):** Skapa en omskrivningsuppsättning som har 3 omskrivningsregler: 

* Den första regeln har ett villkor som kontrollerar *query_string-variabeln* för *category=shoes* och har en åtgärd som skriver om URL-sökvägen till */listing1* och har **Omvärdera** sökvägskarta aktiverad

* Den andra regeln har ett villkor som kontrollerar *query_string-variabeln* för *category=bag* och har en åtgärd som skriver om URL-sökvägen till */listing2*  och har **Omvärdera** sökvägsmappning aktiverad

* Den tredje regeln har ett  villkor som kontrollerar query_string-variabeln för *category=accessories* och har en åtgärd som skriver om URL-sökvägen till */listing3* och har **Omvärdera sökvägsmappning** aktiverad

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL-omskrivningsscenario 1–2.":::

 

**Steg 2 (b):** Associera den här omskrivningsuppsättningen med standardsökvägen för ovanstående sökvägsbaserade regel

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL-omskrivningsscenario 1–3.":::

Om användaren nu *begär contoso.com/listing?category=any* matchas den med standardsökvägen eftersom inget av sökvägsmönstren i sökvägskartan (/listing1, /listing2, /listing3) matchar. Eftersom du har associerat ovanstående omskrivningsuppsättning med den här sökvägen utvärderas den här omskrivningsuppsättningen. Eftersom frågesträngen inte matchar villkoret i någon av de tre omskrivningsreglerna i den här omskrivningsuppsättningen sker ingen omskrivningsåtgärd och därför dirigeras begäran oförändrad till den backend som är associerad med standardsökvägen (som är *GenericList).*

Om användaren begär *contoso.com/listing?category=shoes* matchas standardsökvägen igen. Men i det här fallet kommer villkoret i den första regeln att matcha och därför körs åtgärden som är associerad med villkoret, vilket skriver om URL-sökvägen till */listing1*  och utvärderar sökvägskartan. När sökvägskartan omvärderas kommer begäran nu att matcha sökvägen som är associerad med mönstret */listing1* och begäran dirigeras till den backend som är associerad med det här mönstret, vilket är ShoesListBackendPool.

>[!NOTE]
>Det här scenariot kan utökas till val annat huvud- eller cookievärde, URL-sökväg, frågesträng eller servervariabler baserat på de definierade villkoren och gör att du kan dirigera begäranden baserat på dessa villkor.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Skriva om frågesträngsparametrar baserat på URL:en

Tänk dig ett scenario med en shoppingwebbplats där användarens synliga länk ska vara enkel och läsbar, men backend-servern behöver frågesträngparametrarna för att visa rätt innehåll.

I så fall kan Application Gateway hämta parametrar från URL:en och lägga till nyckel/värde-par för frågesträngar från dem från URL:en. Anta till exempel att användaren vill skriva om, till , det kan uppnås via följande konfiguration för `https://www.contoso.com/fashion/shirts` `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` URL-omskrivning.

**Villkor** – Om `uri_path` servervariabeln är lika med mönstret `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL-omskrivningsscenario 2–1.":::

**Åtgärd** – Ange URL-sökväg till `buy.aspx` och frågesträng till `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL-omskrivningsscenario 2–2.":::

En stegvis guide för att uppnå scenariot som beskrivs ovan finns i Skriva om URL med hjälp [Application Gateway med Azure Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>URL-omskrivning jämfört med url-omdirigering

När det gäller en URL-Application Gateway skriver om URL:en innan begäran skickas till backend-adressen. Detta ändrar inte vad användarna ser i webbläsaren eftersom ändringarna är dolda för användaren.

Om en URL omdirigeras skickar Application Gateway ett omdirigeringssvar till klienten med den nya URL:en. Det kräver i sin tur att klienten skickar sin begäran på nytt till den nya URL:en som anges i omdirigeringen. Den URL som användaren ser i webbläsaren uppdateras till den nya URL:en.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Omskrivning jämfört med omdirigering.":::

## <a name="limitations"></a>Begränsningar

- Om ett svar har fler än en rubrik med samma namn kommer omskrivning av värdet för en av dessa huvuden att resultera i att de andra huvudena i svaret blir borttryckt. Detta kan vanligtvis inträffa med Set-Cookie huvud eftersom du kan ha fler än en Set-Cookie i ett svar. Ett sådant scenario är när du använder en apptjänst med en programgateway och har konfigurerat cookiebaserad sessionstillhörighet på programgatewayen. I det här fallet innehåller svaret två Set-Cookie huvuden: en som används av apptjänsten, till exempel: och en annan för `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` Application Gateway-tillhörighet, till exempel `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Om du skriver om en av Set-Cookie-huvudena i det här scenariot kan det leda till att den andra Set-Cookie-huvudet tas bort från svaret.
- Omwrites stöds inte när programgatewayen har konfigurerats för att omdirigera begäranden eller för att visa en anpassad felsida.
- Rubriknamn kan innehålla alla alfanumeriska tecken och specifika symboler enligt definitionen i [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Vi stöder för närvarande inte specialtecknet ( \_ ) i Rubriknamn.
- Anslutnings- och uppgraderingshuvuden kan inte skrivas om

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du skriver om HTTP-huvuden med Application Gateway med Azure Portal](rewrite-http-headers-portal.md)
- [Lär dig hur du skriver om URL med Application Gateway med Azure Portal](rewrite-url-portal.md)
