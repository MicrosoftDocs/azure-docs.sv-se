---
title: Principer för cachelagring av Azure API Management | Microsoft Docs
description: Läs mer om de cachelagrade principer som är tillgängliga för användning i Azure API Management. Se exempel och Visa fler tillgängliga resurser.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: f0aeef7bc67f5c59bb80d5ff24a97be737447a81
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510189"
---
# <a name="api-management-caching-policies"></a>API Management-cachelagringsprinciper
Det här avsnittet innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="caching-policies"></a><a name="CachingPolicies"></a> Principer för cachelagring

- Principer för cachelagring av svar
    - [Hämta från cache](api-management-caching-policies.md#GetFromCache) – kör cache slå upp och returnera ett giltigt cachelagrat svar när det är tillgängligt.
    - [Lagra i cache](api-management-caching-policies.md#StoreToCache) – cachelagrar svar enligt den angivna konfigurationen för cache-kontroll.
- Principer för cachelagring av värden
    - [Hämta värde från cache](#GetFromCacheByKey) – hämta ett cachelagrat objekt efter nyckel.
    - [Lagra värdet i cache](#StoreToCacheByKey) – lagra ett objekt i cacheminnet efter nyckel.
    - [Ta bort värdet från cache](#RemoveCacheByKey) – ta bort ett objekt i cache by-nyckeln.

## <a name="get-from-cache"></a><a name="GetFromCache"></a> Hämta från cache
Använd `cache-lookup` principen för att utföra cache-sökning och returnera ett giltigt cachelagrat svar när det är tillgängligt. Den här principen kan tillämpas i fall där svars innehållet förblir statiskt under en viss tids period. Cachelagring av svar minskar bandbredds-och bearbetnings kraven på backend-webbservern och ger lägre svars tid för API-konsumenter.

> [!NOTE]
> Den här principen måste ha en motsvarande [lagrings princip för cachelagring](api-management-caching-policies.md#StoreToCache) .

### <a name="policy-statement"></a>Princip kommentar

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Exempel som använder princip uttryck
I det här exemplet visas hur du konfigurerar API Management svars tid för svar som matchar Server dels tjänstens svars tid som anges i den säkerhetskopierade tjänstens `Cache-Control` direktiv. En demonstration av hur du konfigurerar och använder den här principen finns i avsnittet [Cloud Cover avsnitt 177: fler API Management funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och fast-forward till 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Mer information finns i [princip uttryck](api-management-policy-expressions.md) och [Sammanhangs variabel](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-lookup|Rot element.|Ja|
|variera beroende på rubrik|Starta cachelagring av svar per värde av angivet huvud, till exempel acceptera, acceptera-charset, Accept-Encoding, accept-language, auktorisering, vänta, från, värd, om-matchning.|Nej|
|variera-vid-fråga-parameter|Starta cachelagring av svar per värde för angivna frågeparametrar. Ange en eller flera parametrar. Använd semikolon som avgränsare. Om ingen anges används alla frågeparametrar.|Nej|

### <a name="attributes"></a>Attribut

| Namn                           | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standardvärde           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Tillåt-privat-Response-caching | När det är inställt på `true` , tillåter cachelagring av begär Anden som innehåller ett Authorization-huvud.                                                                                                                                                                                                                                                                        | Nej       | falskt             |
| cachelagring – typ               | Välj mellan följande värden för attributet:<br />- `internal` Om du vill använda den inbyggda API Management cachen<br />- `external`Om du vill använda den externa cachen enligt beskrivningen i [använda en extern Azure-cache för Redis i Azure API Management](api-management-howto-cache-external.md)<br />- `prefer-external` Om du vill använda extern cache om det är konfigurerat eller internt cacheminne i annat fall. | Nej       | `prefer-external` |
| underordnad cache-typ        | Det här attributet måste anges till ett av följande värden.<br /><br /> -ingen-underordnad cachelagring tillåts inte.<br />-privat-underordnad privat cachelagring tillåts.<br />-offentlig-privat och delad överordnad cachelagring tillåts.                                                                                                          | Nej       | inget              |
| måste omverifiera                | När cachelagring av underordnade poster är aktiverat aktiverar eller inaktiverar du `must-revalidate` direktivet för cache-kontroll i Gateway-svar.                                                                                                                                                                                                                      | Nej       | true              |
| Vary-för-utvecklare              | Ange till `true` att cachelagra svar per utvecklares konto som äger den [prenumerations nyckel](./api-management-subscriptions.md) som ingår i begäran.                                                                                                                                                                                                                                                                                                  | Ja      |         Falskt          |
| Vary-by-Developer-Groups       | Ställ in på `true` att cachelagra svar per [användar grupp](./api-management-howto-create-groups.md).                                                                                                                                                                                                                                                                                                             | Ja      |       Falskt            |

### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** inkommande
- **Princip omfattningar:** alla omfattningar

## <a name="store-to-cache"></a><a name="StoreToCache"></a> Lagra i cacheminnet
`cache-store`Principen cachelagrar svar enligt de angivna cacheinställningarna. Den här principen kan tillämpas i fall där svars innehållet förblir statiskt under en viss tids period. Cachelagring av svar minskar bandbredds-och bearbetnings kraven på backend-webbservern och ger lägre svars tid för API-konsumenter.

> [!NOTE]
> Den här principen måste ha en motsvarande princip [för att hämta från cachen](api-management-caching-policies.md#GetFromCache) .

### <a name="policy-statement"></a>Princip kommentar

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Exempel som använder princip uttryck
I det här exemplet visas hur du konfigurerar API Management svars tid för svar som matchar Server dels tjänstens svars tid som anges i den säkerhetskopierade tjänstens `Cache-Control` direktiv. En demonstration av hur du konfigurerar och använder den här principen finns i avsnittet [Cloud Cover avsnitt 177: fler API Management funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och fast-forward till 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Mer information finns i [princip uttryck](api-management-policy-expressions.md) och [Sammanhangs variabel](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-Store|Rot element.|Ja|

### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standardvärde           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| varaktighet         | Time-to-Live för cachelagrade poster, anges i sekunder.                                                                                                                                                                                                                                                                                                   | Ja      | Ej tillämpligt               |

### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** utgående
- **Princip omfattningar:** alla omfattningar

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> Hämta värde från cache
Använd `cache-lookup-value` principen för att utföra cache-sökning efter nyckel och returnera ett cachelagrat värde. Nyckeln kan ha ett godtyckligt sträng värde och anges vanligt vis med ett princip uttryck.

> [!NOTE]
> Den här principen måste ha ett motsvarande [lagrings värde i cache](#StoreToCacheByKey) -principen.

### <a name="policy-statement"></a>Princip kommentar

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exempel
Mer information och exempel på den här principen finns i [anpassad cachelagring i Azure API Management](./api-management-sample-cache-by-key.md).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-lookup-Value|Rot element.|Ja|

### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standardvärde           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cachelagring – typ | Välj mellan följande värden för attributet:<br />- `internal` Om du vill använda den inbyggda API Management cachen<br />- `external`Om du vill använda den externa cachen enligt beskrivningen i [använda en extern Azure-cache för Redis i Azure API Management](api-management-howto-cache-external.md)<br />- `prefer-external` Om du vill använda extern cache om det är konfigurerat eller internt cacheminne i annat fall. | Nej       | `prefer-external` |
| standardvärde-värde    | Ett värde som ska tilldelas variabeln om nyckels ökningen för cachen resulterade i ett missar. Om det här attributet inte anges `null` tilldelas.                                                                                                                                                                                                           | Nej       | `null`            |
| key              | Nyckel värde för cache som ska användas i sökningen.                                                                                                                                                                                                                                                                                                                       | Ja      | Ej tillämpligt               |
| variabel namn    | Namnet på den [Sammanhangs variabel](api-management-policy-expressions.md#ContextVariables) som sökt värde tilldelas, om sökningen lyckas. Om LETAUPP resulterar i ett saknat objekt, tilldelas variabeln värdet för `default-value` attributet eller `null` , om `default-value` attributet utelämnas.                                       | Ja      | Ej tillämpligt               |

### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** inkommande, utgående, Server del, på-fel
- **Princip omfattningar:** alla omfattningar

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> Lagra värdet i cachen
`cache-store-value`Nyckeln utför cache-lagring per nyckel. Nyckeln kan ha ett godtyckligt sträng värde och anges vanligt vis med ett princip uttryck.

> [!NOTE]
> Den här principen måste ha ett motsvarande [Get-värde från en princip för cachelagring](#GetFromCacheByKey) .

### <a name="policy-statement"></a>Princip kommentar

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exempel
Mer information och exempel på den här principen finns i [anpassad cachelagring i Azure API Management](./api-management-sample-cache-by-key.md).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-lagring-värde|Rot element.|Ja|

### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standardvärde           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cachelagring – typ | Välj mellan följande värden för attributet:<br />- `internal` Om du vill använda den inbyggda API Management cachen<br />- `external`Om du vill använda den externa cachen enligt beskrivningen i [använda en extern Azure-cache för Redis i Azure API Management](api-management-howto-cache-external.md)<br />- `prefer-external` Om du vill använda extern cache om det är konfigurerat eller internt cacheminne i annat fall. | Nej       | `prefer-external` |
| varaktighet         | Värdet cachelagras för angivet duration-värde, angivet i sekunder.                                                                                                                                                                                                                                                                                 | Ja      | Ej tillämpligt               |
| key              | Cache-nyckel värdet kommer att lagras under.                                                                                                                                                                                                                                                                                                                   | Ja      | Ej tillämpligt               |
| värde            | Värdet som ska cachelagras.                                                                                                                                                                                                                                                                                                                                     | Ja      | Ej tillämpligt               |
### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** inkommande, utgående, Server del, på-fel
- **Princip omfattningar:** alla omfattningar

### <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> Ta bort värde från cache
`cache-remove-value`Tar bort ett cachelagrat objekt som identifieras av dess nyckel. Nyckeln kan ha ett godtyckligt sträng värde och anges vanligt vis med ett princip uttryck.

#### <a name="policy-statement"></a>Princip kommentar

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Exempel

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-Remove-Value|Rot element.|Ja|

#### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standardvärde           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cachelagring – typ | Välj mellan följande värden för attributet:<br />- `internal` Om du vill använda den inbyggda API Management cachen<br />- `external`Om du vill använda den externa cachen enligt beskrivningen i [använda en extern Azure-cache för Redis i Azure API Management](api-management-howto-cache-external.md)<br />- `prefer-external` Om du vill använda extern cache om det är konfigurerat eller internt cacheminne i annat fall. | Nej       | `prefer-external` |
| key              | Nyckeln för det tidigare cachelagrade värdet som ska tas bort från cachen.                                                                                                                                                                                                                                                                                        | Ja      | Ej tillämpligt               |

#### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes) .

- **Princip avsnitt:** inkommande, utgående, Server del, på-fel
- **Princip omfattningar:** alla omfattningar

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Princip referens](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar
+ [Princip exempel](policy-samples.md)
