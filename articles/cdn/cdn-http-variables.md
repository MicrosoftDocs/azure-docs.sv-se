---
title: HTTP-variabler för Azure CDN-regel motor | Microsoft Docs
description: Lär dig mer om HTTP-variabler, som gör att du kan hämta metadata för HTTP-begäran och svar för vissa regel motor funktioner. Använd metadata för att ändra en begäran/ett svar.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: allensu
ms.openlocfilehash: a2d9fc98ba6f514afbd88e543a859a69e0fc6c6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192670"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>HTTP-variabler för Azure CDN-regel motor
HTTP-variabler tillhandahåller de metoder genom vilka du kan hämta metadata för HTTP-begäran och svar. Dessa metadata kan sedan användas för att dynamiskt ändra en begäran eller ett svar. Användningen av HTTP-variabler är begränsad till följande regel motor funktioner:

- [Cache-omarbetning av nyckel](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm)
- [Ändra huvudet för klientbegäran](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm)
- [Ändra huvudet för klient svaret](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm)
- [URL-omdirigering](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm)
- [URL-omskrivning](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm)

## <a name="definitions"></a>Definitioner
I följande tabell beskrivs de HTTP-variabler som stöds. Ett tomt värde returneras när GEO-metadata (till exempel post nummer) inte är tillgängliga för en viss begäran.


| Name | Variabel | Beskrivning | Exempelvärde |
| ---- | -------- | ----------- | ------------ |
| ASN (beställare) | % {geo_asnum} | Anger beställarens AS-nummer. <br /><br />**Föråldrad:** % {virt_dst_asnum}. <br />Den här variabeln har ersatts av% {geo_asnum}. Även om en regel som använder den här inaktuella variabeln fortsätter att fungera, bör du uppdatera den så att den använder den nya variabeln. | AS15133 |
| Ort (beställare) | % {geo_city} | Anger beställarens ort. | Los Angeles |
| Kontinent (beställare) | % {geo_continent} | Anger förfrågans kontinent via dess förkortning. <br />Giltiga värden är: <br />AF: Afrika<br />SOM: Asien<br />EU: Europa<br />NA: Nordamerika<br />OC: Oceanien<br />SA: södra Amerika<br /><br />**Föråldrad:** % {virt_dst_continent}. <br />Den här variabeln har ersatts av% {geo_continent}. <br />Även om en regel som använder den här inaktuella variabeln fortsätter att fungera, bör du uppdatera den så att den använder den nya variabeln.| Ej tillämpligt |
| Cookie-värde | % {cookie_Cookie} | Returnerar värdet som motsvarar cookie-nyckeln som identifieras av cookie-termen. | Exempel på användning: <br />% {cookie__utma}<br /><br />Exempel värde:<br />111662281.2.10.1222100123 |
| Land/region (beställare) | % {geo_country} | Anger förfrågans land/region med hjälp av lands-/region koden. <br />**Föråldrad:** % {virt_dst_country}. <br /><br />Den här variabeln har ersatts av% {geo_country}. Även om en regel som använder den här inaktuella variabeln fortsätter att fungera, bör du uppdatera den så att den använder den nya variabeln. | USA |
| Utsedd marknads region (beställare) | % {geo_dma_code} |Anger förfrågans medie marknad enligt dess regions kod. <br /><br />Det här fältet gäller endast för begär Anden som kommer från USA.| 745 |
| HTTP-förfrågningsmetod | % {request_method} | Anger metoden för HTTP-begäran. | GET |
| HTTP-statuskod | % {status} | Anger HTTP-statuskod för svaret. | 200 |
| IP-adress (beställare) | % {virt_dst_addr} | Anger begär andeens IP-adress. | 192.168.1.1 |
| Latitude (beställare) | % {geo_latitude} | Anger förfrågans latitud. | 34,0995 |
| Longitud (beställare) | % {geo_longitude} | Anger beställarens longitud. | – 118,4143 |
| Europeiska gemenskapernas statistiska områden (beställare) | % {geo_metro_code} | Anger beställarens huvudstads utrymme. <br /><br />Det här fältet gäller endast för begär Anden som kommer från USA.<br />| 745 |
| Port (beställare) | % {virt_dst_port} | Anger den tillfälliga porten för beställaren. | 55885 |
| Post nummer (beställare) | % {geo_postal_code} | Anger beställarens post nummer. | 90210 |
| Frågesträng hittades | % {is_args} | Värdet för den här variabeln varierar beroende på om begäran innehåller en frågesträng.<br /><br />– Frågesträng påträffades:?<br />-Ingen frågesträng: NULL | ? |
| Frågesträngparametern hittades | % {is_amp} | Värdet för den här variabeln varierar beroende på om begäran innehåller minst en frågeparameter-parameter.<br /><br />-Parameter hittades: &<br />-Inga parametrar: NULL | & |
| Parameter värde för frågesträng | % {arg_ &lt; parameter &gt; } | Returnerar värdet som motsvarar frågesträngparametern som identifieras av &lt; parameter &gt; termen. | Exempel på användning: <br />% {arg_language}<br /><br />Exempel på Frågesträngs parameter: <br />? språk = en<br /><br />Exempel värde: en |
| Frågesträngs värde | % {query_string} | Anger hela frågesträng svärdet som definierats i fråge-URL: en. |key1 = val1&key2 = val2&key3 = val3 |
| Referent domän | % {referring_domain} | Anger den domän som definierats i rubriken för referentens begäran. | <www.google.com> |
| Region (beställare) | % {geo_region} | Anger beställarens region (till exempel delstat eller provins) genom sin alfanumeriska förkortning. | CA |
| Värde för begär ande huvud | % {http_RequestHeader} | Returnerar det värde som motsvarar det begär ande huvud som identifieras av RequestHeader-termen. <br /><br />Om namnet på begär ande rubriken innehåller ett bindestreck (till exempel User-Agent) ersätter du det med ett under streck (till exempel User_Agent).| Exempel på användning:% {http_Connection}<br /><br />Exempel värde: Keep-Alive | 
| Begär värd | % {Host} | Anger värden som definierats i fråge-URL: en. | <www.mydomain.com> |
| Protokoll för begäran | % {request_protocol} | Anger protokollet för begäran. | HTTP/1.1 |
| Begär ande schema | % {schema} | Anger schemat för begäran. |http |
| URI för begäran (relativ) | % {request_uri} | Anger den relativa sökvägen, inklusive frågesträngen, som definieras i begärande-URI: n. | /Marketing/foo.js? loggad = sant |
| URI för begäran (relativt utan frågesträng) | % {URI} | Anger den relativa sökvägen till det begärda innehållet. <br /><br/>Viktig information:<br />-Den här relativa sökvägen utesluter frågesträngen.<br />-Den här relativa sökvägen visar att URL-omskrivning. En webb adress skrivs om under följande förhållanden:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Funktionen för URL-omskrivning: funktionen skriver om den relativa sökvägen som definierats i URI: n för begäran.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME-URL: den här typen av begäran skrivs om till motsvarande CDN-URL. |/800001/corigin/rewrittendir/foo.js |
| URI för förfrågan | % {Request} | Beskriver begäran. <br />Syntax: &lt; http-metod &gt; &lt; relativ sökväg &gt; &lt; http-protokoll&gt; | Hämta/Marketing/foo.js? loggad = True http/1.1 |
| Svarets huvud värde | % {resp_ &lt; ResponseHeader &gt; } | Returnerar värdet som motsvarar svars huvudet som identifieras av &lt; ResponseHeader- &gt; termen. <br /><br />Om namnet på svars huvudet innehåller ett bindestreck (till exempel User-Agent) ersätter du det med ett under streck (till exempel User_Agent). | Exempel på användning:% {resp_Content_Length}<br /><br />Exempel värde: 100 |

## <a name="usage"></a>Användning
I följande tabell beskrivs rätt syntax för att ange en HTTP-variabel.


| Syntax | Exempel | Beskrivning |
| ------ | -------- | ---------- |
| % { &lt; HTTPVariable &gt; } | % {Host} | Använd den här syntaxen för att hämta hela värdet som motsvarar det angivna &lt; HTTPVariable &gt; . |
| % { &lt; HTTPVariableDelimiter &gt; } | % {Host,} | Använd den här syntaxen för att ställa in Skift läget för hela värdet som motsvarar det angivna  &lt; HTTPVariableDelimiter &gt; . |
| % { &lt; HTTPVariableDelimiterExpression &gt; } | % {Host/= ^ www \. ([^ \. ] +) \. ([^ \. :] +)/CDN. $2. $3:80} | Använd ett reguljärt uttryck för &lt; HTTPVariableDelimiterExpression &gt; för att ersätta, ta bort eller manipulera ett värde för en http-variabel. |

HTTP-variabel namn har bara stöd för alfabetiska tecken och under streck. Konvertera tecken som inte stöds till under streck.

## <a name="delimiter-reference"></a>Referens för avgränsare
En avgränsare kan anges efter en HTTP-variabel för att uppnå någon av följande effekter:

- Transformera värdet som är kopplat till variabeln.

     Exempel: konvertera hela värdet till gemener.

- Ta bort värdet som är kopplat till variabeln.

- Ändrar värdet som är kopplat till variabeln.

     Exempel: Använd reguljära uttryck för att ändra värdet som är kopplat till HTTP-variabeln.

Avgränsarna beskrivs i följande tabell.

| Avgränsare | Beskrivning |
| --------- | ----------- |
| := | Anger att ett standardvärde tilldelas variabeln när den är antingen: <br />-Saknas <br />-Ange som NULL. |
| :+ | Anger att ett standardvärde ska tilldelas variabeln när ett värde har tilldelats till den. |
| : | Anger att en del sträng av värdet som tilldelats variabeln ska expanderas. |
| # | Anger att mönstret som anges efter den här avgränsaren ska tas bort när det hittas i början av det värde som är kopplat till variabeln. |
| % | Anger att mönstret som anges efter den här avgränsaren ska tas bort när det hittas i slutet av värdet som är kopplat till variabeln. <br />Den här definitionen gäller endast när%-symbolen används som avgränsare. |
| / | Avgränsar en HTTP-variabel eller ett mönster. |
| // | Sök efter och Ersätt alla instanser av det angivna mönstret. |
| /= | Sök, kopiera och skriv om alla förekomster av det angivna mönstret. |
| , | Konvertera värdet som är kopplat till HTTP-variabeln till gemener. |
| ^ | Konvertera värdet som är kopplat till HTTP-variabeln till versaler. |
| ,, | Konvertera alla instanser av det angivna specialtecknet i värdet som är associerat med HTTP-variabeln till gemener. |
| ^^ | Konvertera alla förekomster av det angivna specialtecknet i värdet som är associerat med HTTP-variabeln till versaler. |

## <a name="exceptions"></a>Undantag
I följande tabell beskrivs under vilka omständigheter den angivna texten inte behandlas som en HTTP-variabel.

| Villkor | Beskrivning | Exempel |
| --------- | ----------- | --------|
| Hoppar över% symbol | Procent symbolen kan undantas genom att använda ett omvänt snedstreck. <br />Exempel värdet till höger kommer att behandlas som ett litteralt värde och inte som en HTTP-variabel.| \%värd |
| Okända variabler | En tom sträng returneras alltid för okända variabler. | % {unknown_variable} |
| Ogiltiga tecken eller ogiltig syntax | Variabler som innehåller ogiltiga tecken eller syntax behandlas som litterala värden. <br /><br />Exempel #1: det angivna värdet innehåller ett ogiltigt Character (till exempel-). <br /><br />Exempel #2: det angivna värdet innehåller en dubbel uppsättning klammerparenteser. <br /><br />Exempel #3: det angivna värdet saknar en avslutande klammerparentes.<br /> | Exempel #1:% {resp_user-agent} <br /><br />Exempel #2:% {{Host}} <br /><br />Exempel #3:% {Host |
| Variabel namn saknas | Ett NULL-värde returneras alltid när en variabel inte har angetts. | %{} |
| Efterföljande tecken | Tecken som spårar en variabel behandlas som litterala värden. <br />Exempel värdet till höger innehåller en avslutande klammer som kommer att behandlas som ett litteralt värde. | % {Host}} |

## <a name="setting-default-header-values"></a>Ange standard huvud värden
Ett standardvärde kan tilldelas till ett sidhuvud när det uppfyller något av följande villkor:
- Saknas/unset
- Ange som NULL.

I följande tabell beskrivs hur du definierar ett standardvärde.

| Villkor | Syntax | Exempel | Beskrivning |
| --------- | ------ | --------| ----------- |
| Ange ett huvud värde för ett standardvärde när det uppfyller något av följande villkor: <br /><br />– Rubrik saknas <br /><br />– Huvud värde är inställt på NULL.| % {Variable: = värde} | % {http_referrer: = ospecificerad} | Referent-rubriken anges bara till *ospecificerad* när den antingen saknas eller har angetts till null. Ingen åtgärd sker om den har angetts. |
| Ange ett huvud värde för ett standardvärde när det saknas. | % {Variable = värde} | % {http_referrer = ospecificerad} | Referent-rubriken anges bara till *ospecificerad* när den saknas. Ingen åtgärd sker om den har angetts. |
| Ange ett standardvärde för sidhuvudet när det inte uppfyller något av följande villkor: <br /><br />-Saknas<br /><br /> -Ange som NULL. | % {Variabel: + värde} | % {http_referrer: + ospecificerad} | Referent-rubriken anges bara till *ospecificerad* när ett värde har tilldelats till den. Ingen åtgärd utförs om den antingen saknas eller har angetts till NULL. |

## <a name="manipulating-variables"></a>Manipulera variabler
Variabler kan manipuleras på följande sätt:

- Expanderande del strängar
- Ta bort mönster

### <a name="substring-expansion"></a>Del Strängs expansion
Som standard expanderas en variabel till dess fulla värde. Använd följande syntax för att bara expandera en del sträng i variabelns värde.

`%<Variable>:<Offset>:<Length>}`

Viktig information:

- Värdet som tilldelas till förskjutnings termen bestämmer start-tecken för under strängen:

     - Positivt: under strängens start tecken beräknas från det första tecken i strängen.
     - Noll: under strängens start tecken är det första bokstaven i strängen.
     - Negativt: den första bokstaven i del strängen beräknas från det sista specialtecknet i strängen.

- Längden på del strängen bestäms av *längden* :

     - Utelämnad: utesluta längd termen tillåter att del strängen innehåller alla tecken mellan start tecknet och slutet av strängen.
     - Positivt: anger del strängens längd från start-tecken till höger.
     - Negativ: anger del strängens längd från start tecken till vänster.

#### <a name="example"></a>Exempel:

I följande exempel förlitar sig följande URL för exempel förfrågan:

https: \/ /cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Följande sträng visar olika metoder för att manipulera variabler:

https: \/ /www%{http_host: 3}/mobile/%{REQUEST_URI: 7:10}/% {REQUEST_URI:-5:-8}. htm

Baserat på URL: en för exempel förfrågan kommer ovanstående variabel manipulation att skapa följande värde:

https: \/ /www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Borttagning av mönster
Text som matchar ett speciellt mönster kan tas bort från antingen början eller slutet av en variabels värde.

| Syntax | Action |
| ------ | ------ |
| % {Variabel # mönster} | Ta bort text när det angivna mönstret finns i början av en variabels värde. |
| % {Variabel% Pattern} | Ta bort text när det angivna mönstret finns i slutet av en variabels värde. |

#### <a name="example"></a>Exempel:

I det här exempel scenariot anges variabeln *REQUEST_URI* till:

`/800001/myorigin/marketing/product.html?language=en-US`

Följande tabell visar hur den här syntaxen fungerar.

| Exempel-syntax | Resultat | Beskrivning |
| ------------- | ------- | --- |
| % {request_uri #/800001}/customerorigin | /customerorigin/myorigin/Marketing/product.html? språk = en-US | Eftersom variabeln börjar med mönstret ersattes den. |
| % {request_uri% html} htm | /800001/myorigin/Marketing/product.html? språk = en-US | Eftersom variabeln inte slutar med mönstret, fanns det ingen ändring.|

### <a name="find-and-replace"></a>Sök och ersätt
Syntaxen find och replace beskrivs i följande tabell.

| Syntax | Action |
| ------ | ------ |
| % {Variabel/Sök/Ersätt} | Sök efter och ersätt den första förekomsten av det angivna mönstret. |
| % {Variabel//Sök/Ersätt} | Sök efter och Ersätt alla förekomster av det angivna mönstret. |
| % {Variable ^} |Konvertera hela värdet till versaler. |
| % {Variabel ^ Sök} | Omvandla den första förekomsten av det angivna mönstret till versaler. |
| % {Variable,} | Konvertera hela värdet till gemener. |
| % {Variabel, Sök} | Konvertera den första förekomsten av det angivna mönstret till gemener. |

### <a name="find-and-rewrite"></a>Sök och skriv om
Om du vill ha en variation för Sök och ersätt använder du texten som matchar det angivna mönstret när du skriver om den. Syntaxen find och Write beskrivs i följande tabell.

| Syntax | Action |
| ------ | ------ |
| % {Variable/= Sök/Skriv över} | Sök, kopiera och skriv om alla förekomster av det angivna mönstret. |
| % {Variabel/^ Sök/Skriv över} | Sök, kopiera och skriv om det angivna mönstret när det inträffar i början av variabeln. |
| % {Variabel/$Find/Rewrite} | Sök, kopiera och skriv om det angivna mönstret när det sker i slutet av variabeln. |
| % {Variabel/Sök} | Sök efter och ta bort alla förekomster av det angivna mönstret. |

Viktig information:

- Expandera text som matchar det angivna mönstret genom att ange en dollar tecken följt av ett heltal (till exempel $1).

- Flera mönster kan anges. Ordningen som mönstret anges i avgör det heltal som ska tilldelas. I följande exempel matchar det första mönstret "www.," det andra mönstret matchar den andra nivå domänen och det tredje mönstret matchar domänen på den översta nivån:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Det omskrivna värdet kan bestå av valfri kombination av text och de här plats hållarna.

    I föregående exempel skrivs värd namnet om till `cdn.$2.$3:80` (till exempel CDN.mydomain.com:80).

- Fallet med en mönster plats hållare (till exempel $1) kan ändras genom följande flaggor:
     - U: versaler expanderat värde.

         Exempelsyntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: gemener i det expanderade värdet.

         Exempelsyntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- En operator måste anges före mönstret. Den angivna operatorn fastställer beteendet för mönster insamling:

     - `=`: Anger att alla förekomster av det angivna mönstret måste samlas in och skrivas om.
     - `^`: Anger att endast text som börjar med det angivna mönstret ska fångas.
     - `$`: Anger att endast text som slutar med det angivna mönstret ska fångas.
 
- Om du utelämnar värdet */Rewrite* raderas texten som matchar mönstret.


