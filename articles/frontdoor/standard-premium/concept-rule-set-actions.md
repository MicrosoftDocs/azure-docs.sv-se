---
title: Konfigurera åtgärder för regel uppsättning för Azure-front dörren standard/Premium
description: Den här artikeln innehåller en lista över de olika åtgärder som du kan utföra med Azures regel uppsättning för front dörren.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e4698a1c1576d15042dd050e0123b83dba39a3e3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064799"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Regel uppsättnings åtgärder för Azure front dörr standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

En [regel uppsättning](concept-rule-set.md) för standard/Premium i Azure-dörren består av regler med en kombination av matchnings villkor och åtgärder. Den här artikeln innehåller en detaljerad beskrivning av de åtgärder som du kan använda i Azures standard-/Premium regel uppsättning. Åtgärden definierar det beteende som tillämpas på en typ av begäran som ett matchnings villkor identifierar. I en regel uppsättning för Azure frontend-dörren (standard/Premium) kan en regel innehålla upp till fem åtgärder.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Följande åtgärder är tillgängliga för användning i Azures front dörrs regel uppsättning.  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a> Förfallo tid för cache

Använd timeout-åtgärden för **cache** för att skriva över TTL-värdet (Time to Live) för slut punkten för begär Anden som regler matchar villkoren anger.

> [!NOTE]
> Ursprung kan ange att de inte cachelagrar vissa svar med hjälp av `Cache-Control` rubriken med värdet `no-cache` , `private` , eller `no-store` . I dessa fall kommer front dörren aldrig att cachelagra innehållet och åtgärden kommer inte att ha någon verkan.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-------|------------------|
| Cache-beteende | <ul><li>**Kringgå cacheminne:** Innehållet ska inte cachelagras. I ARM-mallar anger du `cacheBehavior` egenskapen till `BypassCache` .</li><li>**Åsidosätt:** Det TTL-värde som returneras från ditt ursprung skrivs över med det värde som anges i åtgärden. Detta beteende gäller endast om svaret är cacheable. I ARM-mallar anger du `cacheBehavior` egenskapen till `Override` .</li><li>**Ange om det saknas:** Om inget TTL-värde returneras från ditt ursprung anger regeln TTL-värdet till det värde som anges i åtgärden. Detta beteende gäller endast om svaret är cacheable. I ARM-mallar anger du `cacheBehavior` egenskapen till `SetIfMissing` .</li></ul> |
| Varaktighet för cache | När _cache-beteendet_ är inställt på `Override` eller `Set if missing` , måste dessa fält ange den cache-tid som ska användas. Maximal varaktighet är 366 dagar.<ul><li>I Azure Portal: Ange dagar, timmar, minuter och sekunder.</li><li>I ARM-mallar: ange varaktigheten i formatet `d.hh:mm:ss` . |

### <a name="example"></a>Exempel

I det här exemplet åsidosätter vi cachens förfallo datum till 6 timmar, för matchade begär Anden som inte anger varaktighet för cachen.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="Portal skärm bild som visar förfallo åtgärd för cache.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a> Frågesträng för cache-nyckel

Använd åtgärden för **frågesträngen cache Key** för att ändra cache-nyckeln baserat på frågesträngar. Cache-nyckeln är det sätt som front dörren identifierar unika begär Anden som ska cachelagras.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-------|------------------|
| Beteende | <ul><li>**Inkludera:** Frågesträngar som anges i parametrarna kommer att tas med när cache-nyckeln genereras. I ARM-mallar anger du `queryStringBehavior` egenskapen till `Include` .</li><li>**Cachelagra varje unik URL:** Varje unik URL har sin egen cache-nyckel. I ARM-mallar använder du `queryStringBehavior` `IncludeAll` .</li><li>**Undanta:** Frågesträngarna som anges i parametrarna undantas när cache-nyckeln genereras. I ARM-mallar anger du `queryStringBehavior` egenskapen till `Exclude` .</li><li>**Ignorera frågesträngar:** Frågesträngar beaktas inte när cache-nyckeln genereras. I ARM-mallar anger du `queryStringBehavior` egenskapen till `ExcludeAll` .</li></ul>  |
| Parametrar | Listan över parameter namn för frågesträng, avgränsade med kommatecken. |

### <a name="example"></a>Exempel

I det här exemplet ändrar vi cache-nyckeln för att inkludera en frågesträngparametern med namnet `customerId` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="Portal skärm bild som visar åtgärd för frågesträng för cache-nyckel.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a> Ändra rubrik för begäran

Använd åtgärden **ändra begär ande huvud** för att ändra rubrikerna i begäran när den skickas till ditt ursprung.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-------|------------------|
| Operator | <ul><li>**Lägg till:** Det angivna sidhuvudet läggs till i begäran med det angivna värdet. Om rubriken redan finns läggs värdet till i det befintliga huvudet med hjälp av sträng sammanfogning. Inga avgränsare har lagts till. I ARM-mallar använder du `headerAction` `Append` .</li><li>**Skriv över:** Det angivna sidhuvudet läggs till i begäran med det angivna värdet. Om rubriken redan finns skriver det angivna värdet över det befintliga värdet. I ARM-mallar använder du `headerAction` `Overwrite` .</li><li>**Ta bort:** Om rubriken som anges i regeln finns, tas rubriken bort från begäran. I ARM-mallar använder du `headerAction` `Delete` .</li></ul> |
| Huvudnamn | Namnet på rubriken som ska ändras. |
| Huvudvärde | Värdet som ska läggas till eller skrivas över. |

### <a name="example"></a>Exempel

I det här exemplet lägger vi till värdet `AdditionalValue` i `MyRequestHeader` begär ande huvudet. Om det ursprungliga värdet för svars huvudet har angetts till ett värde på `ValueSetByClient` , skulle rubriken för begäran ha värdet `ValueSetByClientAdditionalValue` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="Portal skärm bild som visar åtgärden ändra begär ande huvud.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a> Ändra svars huvud

Använd åtgärden **ändra svars huvud** för att ändra huvuden som finns i svar innan de returneras till dina klienter.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-------|------------------|
| Operator | <ul><li>**Lägg till:** Det angivna sidhuvudet läggs till i svaret med det angivna värdet. Om rubriken redan finns läggs värdet till i det befintliga huvudet med hjälp av sträng sammanfogning. Inga avgränsare har lagts till. I ARM-mallar använder du `headerAction` `Append` .</li><li>**Skriv över:** Det angivna sidhuvudet läggs till i svaret med det angivna värdet. Om rubriken redan finns skriver det angivna värdet över det befintliga värdet. I ARM-mallar använder du `headerAction` `Overwrite` .</li><li>**Ta bort:** Om rubriken som anges i regeln finns, tas rubriken bort från svaret.  I ARM-mallar använder du `headerAction` `Delete` .</li></ul> |
| Huvudnamn | Namnet på rubriken som ska ändras. |
| Huvudvärde | Värdet som ska läggas till eller skrivas över. |

### <a name="example"></a>Exempel

I det här exemplet tar vi bort rubriken med namnet `X-Powered-By` från svaren innan de returneras till klienten.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="Portal skärm bild som visar åtgärd för att ändra svars huvud.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a> URL-omdirigering

Använd åtgärden **URL-omdirigering** för att omdirigera klienter till en ny URL. Klienterna skickar ett omdirigerings svar från Front dörren.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|----------|------------------|
| Omdirigera typ | Svars typen som ska returneras till begär Anden. <ul><li>I Azure Portal: påträffades (302), flyttades (301), tillfällig omdirigering (307), permanent omdirigering (308).</li><li>I ARM-mallar: `Found` ,, `Moved` `TemporaryRedirect` , `PermanentRedirect`</li></ul> |
| Omdirigera protokoll | <ul><li>I Azure Portal: `Match Request` , `HTTP` , `HTTPS`</li><li>I ARM-mallar: `MatchRequest` , `Http` , `Https`</li></ul> |
| Målvärd | Värd namnet som du vill att begäran ska omdirigeras till. Lämna tomt om du vill bevara den inkommande värden. |
| Målsökväg | Sökvägen som ska användas i omdirigeringen. Ta med rad avståndet `/` . Lämna tomt om du vill behålla den inkommande sökvägen. |
| Frågesträng | Frågesträngen som används i omdirigeringen. Ta inte med rad avståndet `?` . Lämna tomt om du vill behålla inkommande frågesträng. |
| Målcachen | Fragment som ska användas i omdirigeringen. Lämna tomt om du vill behålla det inkommande fragmentet. |

### <a name="example"></a>Exempel

I det här exemplet omdirigerar vi begäran till `https://contoso.com/exampleredirection?clientIp={client_ip}` , samtidigt som du behåller fragmentet. En tillfällig HTTP-omdirigering (307) används. Klientens IP-adress används i stället för `{client_ip}` token i URL: en med hjälp av `client_ip` [Server variabeln](#server-variables).

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="Portal skärm bild som visar Omdirigerad URL-åtgärd.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a> URL-omskrivning

Använd åtgärden för **URL-omskrivning** för att skriva om sökvägen för en begäran som är en väg till ditt ursprung.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|----------|------------------|
| Käll mönster | Definiera käll mönstret i URL-sökvägen som ska ersättas. För närvarande använder käll mönstret en prefix-baserad matchning. Använd ett snedstreck ( `/` ) som käll mönster värde för att matcha alla URL-sökvägar. |
| Mål | Definiera den mål Sök väg som ska användas vid omskrivning. Mål Sök vägen skriver över käll mönstret. |
| Bevara omatchad sökväg | Om värdet är _Ja_, läggs den återstående sökvägen efter käll mönstret till i den nya mål Sök vägen. |

### <a name="example"></a>Exempel

I det här exemplet skriver vi om alla begär anden till sökvägen `/redirection` och bevarar inte resten av sökvägen.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="Portal skärm bild som visar åtgärden för URL-omskrivning.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Servervariabler

Regel uppsättningens servervariabler ger till gång till strukturerad information om begäran. Du kan använda servervariabler för att dynamiskt ändra begär ande-/svarshuvuden eller URL-omskrivna sökvägar/frågesträngar, till exempel när en ny sid inläsning eller när ett formulär publiceras.

### <a name="supported-variables"></a>Variabler som stöds

| Variabelnamn    | Beskrivning                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | IP-adressen för den direkta anslutningen till Azures främre dörr kant. Om klienten använder en HTTP-proxy eller en belastningsutjämnare för att skicka begäran, är värdet för `socket_ip` proxyserverns IP-adress eller belastningsutjämnare.                                                                      |
| `client_ip`      | IP-adressen för klienten som gjorde den ursprungliga begäran. Om det fanns en `X-Forwarded-For` rubrik i begäran plockas klientens IP-adress från rubriken.                                                                                                               |
| `client_port`    | IP-porten för den klient som gjorde begäran.                                                                                                                                                                                                                                          |
| `hostname`       | Värd namnet i begäran från klienten.                                                                                                                                                                                                                                             |
| `geo_country`    | Anger förfrågans land/region med hjälp av lands-/region koden.                                                                                                                                                                                                       |
| `http_method`    | Den metod som används för att göra URL-begäran, till exempel `GET` eller `POST` .                                                                                                                                                                                                                         |
| `http_version`   | Protokollet för begäran. Vanligt vis `HTTP/1.0` , `HTTP/1.1` eller `HTTP/2.0` .                                                                                                                                                                                                                      |
| `query_string`   | Listan över variabel/värde-par som följer "?" i den begärda URL: en.<br />I begäran är till exempel `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `query_string` värdet `id=123&title=fabrikam` .                                                      |
| `request_scheme` | Schemat för begäran: `http` eller `https` .                                                                                                                                                                                                                                                    |
| `request_uri`    | Fullständig URI för ursprunglig begäran (med argument).<br />I begäran är till exempel `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `request_uri` värdet `/article.aspx?id=123&title=fabrikam` .                                                                     |
| `ssl_protocol`   | Protokollet för en etablerad TLS-anslutning.                                                                                                                                                                                                                                            |
| `server_port`    | Porten för den server som godkände en begäran.                                                                                                                                                                                                                                           |
| `url_path`       | Identifierar den angivna resursen i värden som webb klienten vill ha åtkomst till. Detta är en del av URI: n för begäran utan argumenten.<br />I begäran är till exempel `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `uri_path` värdet `/article.aspx` . |

### <a name="server-variable-format"></a>Server variabel format    

Servervariabler kan anges med följande format:

* `{variable}`: Ta med hela Server variabeln. Om klientens IP-adress till exempel är, `111.222.333.444` `{client_ip}` skulle token utvärderas till `111.222.333.444` .
* `{variable:offset}`: Inkludera Server variabeln efter en bestämd förskjutning, fram till slutet av variabeln. Förskjutningen är noll-baserad. Om klientens IP-adress till exempel är, `111.222.333.444` `{client_ip:3}` skulle token utvärderas till `.222.333.444` .
* `{variable:offset:length}`: Inkludera Server variabeln efter en viss förskjutning, upp till den angivna längden. Förskjutningen är noll-baserad. Om klientens IP-adress till exempel är, `111.222.333.444` `{client_ip:4:3}` skulle token utvärderas till `222` .

### <a name="supported-actions"></a>Åtgärder som stöds

Servervariabler stöds för följande åtgärder:

* Frågesträng för cache-nyckel
* Ändra rubrik för begäran
* Ändra svars huvud
* URL-omdirigering
* URL-omskrivning

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Standard-/Premium regel uppsättningen för Azures front dörr](concept-rule-set.md).
* Lär dig mer om [matchnings villkor för regel uppsättning](concept-rule-set-match-conditions.md).
