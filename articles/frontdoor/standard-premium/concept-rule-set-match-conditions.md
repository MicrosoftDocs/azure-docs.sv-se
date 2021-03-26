---
title: Konfigurera matchnings villkor för Azure front dörr standard/Premium
description: Den här artikeln innehåller en lista över olika matchnings villkor som är tillgängliga med Azures standard-/Premium regel uppsättning.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: yuajia
ms.openlocfilehash: 039effb885463c1c53085535a6980601be890340
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561511"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Regel uppsättning matchnings villkor för Azure front dörr standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

I Azures front dörr standard/Premium [regel uppsättning](concept-rule-set.md)består en regel av noll eller flera matchnings villkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av matchnings villkoren som du kan använda i Azures standard-/Premium regel uppsättning.

Den första delen av en regel är ett matchnings villkor eller en uppsättning matchnings villkor. En regel kan bestå av upp till 10 matchnings villkor. Ett matchnings villkor identifierar vissa typer av begär Anden för vilka definierade åtgärder utförs. Om du använder flera matchnings villkor grupperas matchnings villkoren tillsammans med hjälp av och logik. För alla matchnings villkor som stöder flera värden, eller logik används.

Du kan använda ett matchnings villkor för att:

* Filtrera förfrågningar baserat på en speciell IP-adress, land eller region.
* Filtrera förfrågningar efter rubrik information.
* Filtrera begär Anden från mobila enheter eller Skriv bords enheter.
* Filtrera begär Anden från begär fil namn och fil namns tillägg.
* Filtrera förfrågningar från begärd URL, protokoll, sökväg, frågesträng, post argument osv.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Enhets typ

Använd villkoret **enhets typ** matchning för att identifiera begär Anden som har gjorts från en mobil enhet eller en stationär enhet.  

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-------|------------------|
| Operator | <ul><li>I Azure Portal: `Equal` , `Not Equal`</li><li>I ARM-mallar: `Equal` ; Använd `negateCondition` egenskapen för att ange _inte lika med_ |
| Värde | `Mobile`, `Desktop` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla begär Anden som har identifierats från en mobil enhet.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Portal skärm bild som visar matchnings villkor för enhets typ.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Publicera argument

Använd villkoret **post argument** matchning för att identifiera begär Anden baserat på de argument som anges i en post-begärans brödtext. Ett enda matchnings villkor matchar ett argument från POSTens brödtext. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

> [!NOTE]
> Matchnings villkoret **post argument** fungerar med `application/x-www-form-urlencoded` innehålls typen.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Publicera argument | Ett sträng värde som representerar namnet på POST-argumentet. |
| Operator | Valfri operator från [standard operator listan](#operator-list). |
| Värde | Ett eller flera sträng-eller heltals värden som representerar värdet för POST-argumentet som ska matcha. Om flera värden anges utvärderas de med hjälp av eller logik. |
| Skift läges omvandling | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla POST-förfrågningar där ett `customerName` argument anges i begär ande texten och där värdet `customerName` börjar med bokstaven `J` eller `K` . Vi använder en Case-transformering för att konvertera indatavärdena till versaler, så att värdena som börjar med `J` , `j` , `K` och `k` matchar alla.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Portal skärm bild som visar matchnings villkor för post argument.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Frågesträng

Använd villkoret för **frågesträngs** matchning för att identifiera begär Anden som innehåller en speciell frågesträng. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

> [!NOTE]
> Hela frågesträngen matchas som en enskild sträng, utan inledande `?` .

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Operator | Valfri operator från [standard operator listan](#operator-list). |
| Frågesträng | Ett eller flera sträng-eller heltals värden som representerar värdet för frågesträngen som ska matchas. Ta inte med `?` i början av frågesträngen. Om flera värden anges utvärderas de med hjälp av eller logik. |
| Skift läges omvandling | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla förfrågningar där frågesträngen innehåller strängen `language=en-US` . Vi vill att matchnings villkoret ska vara Skift läges känsligt, så vi omformar inte ärendet.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Portal skärm bild som visar villkor för frågesträngs matchning.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Fjärradress

Villkoret **Fjärradress** matchning identifierar begär Anden baserat på beställarens plats eller IP-adress. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

* Använd CIDR-notering när du anger IP-adressblock. Det innebär att syntaxen för ett IP-adressblock är bas-IP-adressen följt av ett snedstreck och prefixets storlek. Exempel:
    * **IPv4-exempel**: `5.5.5.64/26` matchar alla begär Anden som kommer från adresser 5.5.5.64 till 5.5.5.127.
    * **IPv6-exempel**: `1:2:3:/48` matchar alla begär Anden som kommer från adresser 1:2:3:0:0:0:0:0 till 1:2:3: FFFF: FFFF: FFFF: FFFF: FFFF.
* När du anger flera IP-adresser och IP-adressblock används "OR"-logik.
    * **IPv4-exempel**: om du lägger till två IP-adresser `1.2.3.4` och `10.20.30.40` matchas villkoret för alla begär Anden som kommer från adressen 1.2.3.4 eller 10.20.30.40.
    * **IPv6-exempel**: om du lägger till två IP-adresser `1:2:3:4:5:6:7:8` och `10:20:30:40:50:60:70:80` matchas villkoret för alla begär Anden som kommer från adress 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Operator | <ul><li>I Azure Portal: `Geo Match` , `Geo Not Match` , `IP Match` eller `IP Not Match`</li><li>I ARM-mallar: `GeoMatch` , `IPMatch` ; Använd `negateCondition` egenskapen för att ange _geo som inte matchar_ eller IP som _inte matchar_</li></ul> |
| Värde | <ul><li>För `IP Match` `IP Not Match` operatorerna eller: Ange ett eller flera IP-adressintervall. Om flera IP-adressintervall anges utvärderas de med hjälp av eller logik.</li><li>För `Geo Match` `Geo Not Match` operatorerna eller: Ange en eller flera platser med hjälp av lands koden.</li></ul> |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla förfrågningar där begäran inte har sitt ursprung i USA.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Portal skärm bild som visar villkor för Fjärradress matchning.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Brödtext i begäran

Villkoret för text matchning för **begäran** identifierar begär Anden baserat på en speciell text som visas i bröd texten i begäran. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

> [!NOTE]
> Om en begär ande text överskrider 64 KB i storlek, kommer endast de första 64 KB att beaktas för matchnings villkoret för **begär ande texten** .

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Operator | Valfri operator från [standard operator listan](#operator-list). |
| Värde | Ett eller flera sträng-eller heltals värden som representerar värdet för texten i begär ande texten som ska matchas. Om flera värden anges utvärderas de med hjälp av eller logik. |
| Skift läges omvandling | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla förfrågningar där begär ande texten innehåller strängen `ERROR` . Vi omvandlar begär ande texten till versaler innan du utvärderar matchningen, så `error` och andra fall variationer kommer även att utlösa detta matchnings villkor.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="Portal skärm bild som visar villkor för begärans text matchning.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Fil namn för begäran

Villkoret för **begäran om fil namns** matchning identifierar begär Anden som innehåller det angivna fil namnet i fråge-URL: en. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Operator | Valfri operator från [standard operator listan](#operator-list). |
| Värde | Ett eller flera sträng-eller heltals värden som representerar värdet för det begär ande fil namn som ska matchas. Om flera värden anges utvärderas de med hjälp av eller logik. |
| Skift läges omvandling | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla förfrågningar där fil namnet för begäran är `media.mp4` . Vi transformerar fil namnet till gemener innan du utvärderar matchningen, så `MEDIA.MP4` och andra fall variationer kommer även att utlösa detta matchnings villkor.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="Portal skärm bild som visar villkor för begärans matchnings fil namn.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Begär fil tillägg

Tillståndet för matchning av **fil namns tillägg** identifierar begär Anden som innehåller det angivna fil namns tillägget i fil namnet i fråge-URL: en. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

> [!NOTE]
> Ta inte med en inledande period. Använd till exempel `html` i stället för `.html`.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Operator | Valfri operator från [standard operator listan](#operator-list). |
| Värde | Ett eller flera sträng-eller heltals värden som representerar värdet för det begär ande fil namns tillägg som ska matchas. Ta inte med en inledande period. Om flera värden anges utvärderas de med hjälp av eller logik. |
| Skift läges omvandling | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla begär Anden där fil namns tillägget för begäran är `pdf` eller `docx` . Vi transformerar fil tillägget för begäran till gemener innan du utvärderar matchningen, så `PDF` , `DocX` och andra fall variationer kommer också att utlösa detta matchnings villkor.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Portal skärm bild som visar matchnings villkor för begär ande fil tillägg.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> Begär ande huvud

Matchnings villkoret för **begärans huvud** identifierar förfrågningar som innehåller en angiven rubrik i begäran. Du kan använda det här matchnings villkoret för att kontrol lera om en rubrik finns, oavsett dess värde, eller om du vill kontrol lera om rubriken matchar ett angivet värde. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Huvudnamn | Ett sträng värde som representerar namnet på POST-argumentet. |
| Operator | Valfri operator från [standard operator listan](#operator-list). |
| Värde | Ett eller flera sträng-eller heltals värden som representerar värdet för begär ande rubriken som ska matchas. Om flera värden anges utvärderas de med hjälp av eller logik. |
| Skift läges omvandling | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla begär Anden där begäran innehåller ett huvud namn `MyCustomHeader` , oavsett dess värde.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="Portal skärm bild som visar matchnings villkor för begär ande huvud.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Metod för begäran

Villkoret för matchning av **begäran** identifierar begär Anden som använder den angivna http-förfrågnings metoden. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Operator | <ul><li>I Azure Portal: `Equal` , `Not Equal`</li><li>I ARM-mallar: `Equal` ; Använd `negateCondition` egenskapen för att ange _inte lika med_ |
| Metod för begäran | En eller flera HTTP-metoder från:,,,,,, `GET` `POST` `PUT` `DELETE` `HEAD` `OPTIONS` `TRACE` . Om flera värden anges utvärderas de med hjälp av eller logik. |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla förfrågningar där begäran använder- `DELETE` metoden.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="Portal skärm bild som visar villkor för begärans metod matchning.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Sökväg för begäran

Villkoret för matchning av **sökväg för begäran** identifierar begär Anden som innehåller den angivna sökvägen i fråge-URL: en. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

> [!NOTE]
> Sökvägen är en del av URL: en efter hostname och ett snedstreck. Till exempel är sökvägen i URL: en `https://www.contoso.com/files/secure/file1.pdf` `files/secure/file1.pdf` .

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Operator | Valfri operator från [standard operator listan](#operator-list). |
| Värde | Ett eller flera sträng-eller heltals värden som representerar värdet för begär ande Sök vägen som ska matchas. Ta inte med det inledande snedstrecket. Om flera värden anges utvärderas de med hjälp av eller logik. |
| Skift läges omvandling | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla begär Anden där sökvägen till filen för begäran börjar med `files/secure/` . Vi transformerar fil tillägget för begäran till gemener innan du utvärderar matchningen, så förfrågningar till `files/SECURE/` och andra fall variationer kommer också att utlösa detta matchnings villkor.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="Portal skärm bild som visar villkor för matchning av sökväg för begäran.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Protokoll för begäran

Villkoret för matchning av **begär ande protokoll** identifierar begär Anden som använder det angivna protokollet (http eller https).

> [!NOTE]
> *Protokollet* kallas ibland även *schema*.

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Operator | <ul><li>I Azure Portal: `Equal` , `Not Equal`</li><li>I ARM-mallar: `Equal` ; Använd `negateCondition` egenskapen för att ange _inte lika med_ |
| Metod för begäran | `HTTP`, `HTTPS` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla förfrågningar där begäran använder `HTTP` protokollet.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="Portal skärm bild som visar villkor för begäran protokoll matchning.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> URL för begäran

Identifierar begär Anden som matchar angiven URL. Hela URL: en utvärderas. Du kan ange flera värden som ska matchas, som kombineras med eller Logic.

> [!TIP]
> När du använder det här regel villkoret ska du se till att ta med protokollet. Använd till exempel `https://www.contoso.com` istället för bara `www.contoso.com` .

### <a name="properties"></a>Egenskaper

| Egenskap | Värden som stöds |
|-|-|
| Operator | Valfri operator från [standard operator listan](#operator-list). |
| Värde | Ett eller flera sträng-eller heltals värden som representerar värdet för den begär ande-URL som ska matchas. Om flera värden anges utvärderas de med hjälp av eller logik. |
| Skift läges omvandling | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exempel

I det här exemplet matchar vi alla förfrågningar där URL: en för begäran börjar med `https://api.contoso.com/customers/123` . Vi transformerar fil tillägget för begäran till gemener innan du utvärderar matchningen, så förfrågningar till `https://api.contoso.com/Customers/123` och andra fall variationer kommer också att utlösa detta matchnings villkor.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Portal skärm bild som visar villkor för begäran URL-matchning.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> Operator lista

Följande operatorer är giltiga för regler som accepterar värden från standard operator listan:

| Operator                   | Beskrivning                                                                                                                    | Stöd för ARM-mall                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Valfri                        | Matchar när det finns något värde, oavsett vad det är.                                                                     | `operator`: `Any`                                               |
| Lika med                      | Matchar när värdet exakt matchar den angivna strängen.                                                                   | `operator`: `Equal`                                             |
| Innehåller                   | Matchar när värdet innehåller den angivna strängen.                                                                          | `operator`: `Contains`                                          |
| Mindre än                  | Matchar när längden på värdet är mindre än det angivna heltalet.                                                       | `operator`: `LessThan`                                          |
| Större än               | Matchar när längden på värdet är större än det angivna heltalet.                                                    | `operator`: `GreaterThan`                                       |
| Mindre än eller lika med         | Matchar när längden på värdet är mindre än eller lika med det angivna heltalet.                                           | `operator`: `LessThanOrEqual`                                   |
| Större än eller lika med      | Matchar när längden på värdet är större än eller lika med det angivna heltalet.                                        | `operator`: `GreaterThanOrEqual`                                |
| Börjar med                | Matchar när värdet börjar med den angivna strängen.                                                                       | `operator`: `BeginsWith`                                        |
| Slutar med                  | Matchar när värdet slutar med den angivna strängen.                                                                         | `operator`: `EndsWith`                                          |
| Verifiering                      | Matchar när värdet matchar det angivna reguljära uttrycket. [Se nedan för mer information.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Inte alla                    | Matchar när det inte finns något värde.                                                                                                | `operator`: `Any` och `negateCondition` : `true`                |
| Inte lika med                  | Matchar när värdet inte matchar den angivna strängen.                                                                    | `operator`: `Equal` och `negateCondition` : `true`              |
| Innehåller inte               | Matchar när värdet inte innehåller den angivna strängen.                                                                  | `operator`: `Contains` och `negateCondition` : `true`           |
| Inte mindre än              | Matchar när längden på värdet är inte mindre än det angivna heltalet.                                                   | `operator`: `LessThan` och `negateCondition` : `true`           |
| Inte större än           | Matchar när längden på värdet inte är större än det angivna heltalet.                                                | `operator`: `GreaterThan` och `negateCondition` : `true`        |
| Inte mindre än eller lika med     | Matchar när längden på värdet är inte mindre än eller lika med det angivna heltalet.                                       | `operator`: `LessThanOrEqual` och `negateCondition` : `true`    |
| Inte större än eller lika med | Matchar när längden på värdet inte är större än eller lika med det angivna heltalet.                                    | `operator`: `GreaterThanOrEqual` och `negateCondition` : `true` |
| Börjar inte med            | Matchar när värdet inte börjar med den angivna strängen.                                                               | `operator`: `BeginsWith` och `negateCondition` : `true`         |
| Slutar inte med              | Matchar när värdet inte slutar med den angivna strängen.                                                                 | `operator`: `EndsWith` och `negateCondition` : `true`           |
| Inte RegEx                  | Matchar när värdet inte matchar det angivna reguljära uttrycket. [Se nedan för mer information.](#regular-expressions) | `operator`: `RegEx` och `negateCondition` : `true`              |

> [!TIP]
> För numeriska operatorer som *mindre än* och *större än eller lika med*, baseras jämförelsen på längd. Värdet i matchnings villkoret ska vara ett heltal som anger den längd som du vill jämföra.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Reguljära uttryck

Reguljära uttryck stöder inte följande åtgärder:

* Referens och inhämtning av under uttryck.
* Godtyckliga kontroller med noll bredd.
* Subrutin-referenser och rekursiva mönster.
* Villkorliga mönster.
* Spårar kontroll verb.
* `\C`Single-byte-direktivet.
* `\R`Direktivet för ny rad träff.
* `\K`Början av matchnings omställnings direktivet.
* Bild texter och inbäddad kod.
* Atomiska grupperingar och possessive-kvantifierare.

## <a name="arm-template-support"></a>Stöd för ARM-mall

Regel uppsättningar kan konfigureras med hjälp av Azure Resource Manager mallar. [Se en exempel mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set). Du kan lägga till matchnings villkor med hjälp av JSON-eller bicep-kodfragmenten som ingår i exemplen ovan.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [regel uppsättning](concept-rule-set.md).
* Lär dig hur du [konfigurerar din första regel uppsättning](how-to-configure-rule-set.md).
* Läs mer om [regel uppsättnings åtgärder](concept-rule-set-actions.md).
