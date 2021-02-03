---
title: Transformerings principer för Azure API Management | Microsoft Docs
description: Lär dig mer om de omvandlings principer som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: c0c7a6b25c15be2e521e0985c315baf819650aa5
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491764"
---
# <a name="api-management-transformation-policies"></a>Omvandlingsprinciper för API Management
Det här avsnittet innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](./api-management-policies.md).

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a> Omvandlings principer

-   [Konvertera JSON till XML](api-management-transformation-policies.md#ConvertJSONtoXML) – konverterar begäran eller svars text från JSON till XML.

-   [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – konverterar begäran eller svars text från XML till JSON.

-   [Sök och ersätt sträng i brödtext](api-management-transformation-policies.md#Findandreplacestringinbody) – söker efter en begäran eller svars under sträng och ersätter den med en annan under sträng.

-   [Maskera URL: er i](api-management-transformation-policies.md#MaskURLSContent) Länkar för innehålls skrivning (masker) i svars texten så att de pekar på motsvarande länk via gatewayen.

-   [Ange server dels tjänst](api-management-transformation-policies.md#SetBackendService) – ändrar backend-tjänsten för en inkommande begäran.

-   [Ange brödtext](api-management-transformation-policies.md#SetBody) – anger meddelande texten för inkommande och utgående begär Anden.

-   [Ange HTTP-huvud](api-management-transformation-policies.md#SetHTTPheader) – tilldelar ett värde till ett befintligt svar och/eller begär ande huvud eller lägger till ett nytt svar och/eller begär ande huvud.

-   [Ange frågesträngparametern-parameter](api-management-transformation-policies.md#SetQueryStringParameter) – lägger till, ersätter värdet eller tar bort förfrågan om frågesträngparametern.

-   [Skriv om URL](api-management-transformation-policies.md#RewriteURL) – KONVERTERAR en URL för begäran från det offentliga formuläret till det formulär som förväntas av webb tjänsten.

-   [TRANSFORMERA XML med hjälp av en XSLT](api-management-transformation-policies.md#XSLTransform) – använder en XSL-omvandling till XML i begäran eller svars texten.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a> Konvertera JSON till XML
 `json-to-xml`Principen konverterar en begäran eller en svars text från JSON till XML.

### <a name="policy-statement"></a>Princip kommentar

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|JSON-till-XML|Rot element.|Ja|

### <a name="attributes"></a>Attribut

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|apply|Attributet måste anges till ett av följande värden.<br /><br /> -alway-Always Always enconversion.<br />-Content-Type-JSON-Convert endast om svarets Content-Type-huvud indikerar förekomst av JSON.|Ja|Ej tillämpligt|
|Överväg-accept-header|Attributet måste anges till ett av följande värden.<br /><br /> -True-Använd Conversion om XML har begärts i rubriken för begäran accept.<br />-falskt-Använd alltid konvertering.|Inga|true|
|parse-datum|När ställs in på `false` datum värden kopieras bara under omvandlingen|Inga|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, utgående, på-fel

-   **Princip omfattningar:** alla omfattningar

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a> Konvertera XML till JSON
 `xml-to-json`Principen konverterar en begäran eller svars text från XML till JSON. Den här principen kan användas för att modernisera-API: er baserat på Server dels webb tjänster för endast XML.

### <a name="policy-statement"></a>Princip kommentar

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|XML-till-JSON|Rot element.|Ja|

### <a name="attributes"></a>Attribut

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|metod|Attributet måste anges till ett av följande värden.<br /><br /> – anpassad Java Script – den konverterade JSON-filen har ett formulär som är användarvänligt för JavaScript-utvecklare.<br />– direkt – konverterad JSON visar strukturen för det ursprungliga XML-dokumentet.|Ja|Ej tillämpligt|
|apply|Attributet måste anges till ett av följande värden.<br /><br /> -Always-Convert Always.<br />– Content-Type-XML-Convert endast om Content-Type-huvudet för svar anger förekomst av XML.|Ja|Ej tillämpligt|
|Överväg-accept-header|Attributet måste anges till ett av följande värden.<br /><br /> -Sant-Använd konvertering om JSON begärs i rubriken för begäran accept.<br />-falskt-Använd alltid konvertering.|Inga|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, utgående, på-fel

-   **Princip omfattningar:** alla omfattningar

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a> Sök och ersätt sträng i brödtext
 `find-and-replace`Principen söker efter en begär ande eller en svars under sträng och ersätter den med en annan under sträng.

### <a name="policy-statement"></a>Princip kommentar

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Exempel

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|Sök och ersätt|Rot element.|Ja|

### <a name="attributes"></a>Attribut

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|Från|Strängen att söka efter.|Ja|Ej tillämpligt|
|på|Ersättningssträngen. Ange en ersättnings sträng med längden noll för att ta bort Sök strängen.|Ja|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a> Maskera URL: er i innehåll
 `redirect-content-urls`(Maskera) länkar (maskerar) länkar i svars texten så att de pekar på motsvarande länk via gatewayen. Använd i avsnittet utgående om du vill skriva över länkar för svars text så att de pekar på gatewayen. Använd i avsnittet inkommande för en motsatt påverkan.

> [!NOTE]
>  Den här principen ändrar inte några rubrik värden, till exempel `Location` sidhuvuden. Om du vill ändra rubrik värden använder du [Ange huvud](api-management-transformation-policies.md#SetHTTPheader) princip.

### <a name="policy-statement"></a>Princip kommentar

```xml
<redirect-content-urls />
```

### <a name="example"></a>Exempel

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|omdirigera-innehålls-URL: er|Rot element.|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, utgående

-   **Princip omfattningar:** alla omfattningar

##  <a name="set-backend-service"></a><a name="SetBackendService"></a> Ange server dels tjänst
 Använd `set-backend-service` principen för att omdirigera en inkommande begäran till en annan server del än den som anges i API-inställningarna för den åtgärden. Den här principen ändrar Server delens bas-URL för den inkommande begäran till den som anges i principen.

### <a name="policy-statement"></a>Princip kommentar

```xml
<set-backend-service base-url="base URL of the backend service" />
```

eller

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Backend-enheter kan hanteras via [Azure Portal](how-to-configure-service-fabric-backend.md), hanterings- [API](/rest/api/apimanagement)och [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
I det här exemplet anger princip för Server dels tjänst begär Anden baserat på version svärdet som skickades i frågesträngen till en annan server dels tjänst än den som angetts i API: et.

Från början är bas-URL: en för backend-tjänsten härledd från API-inställningarna. URL `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` -adressen är då den URL för Server del som `http://contoso.com/api/10.4/` anges i API-inställningarna.

När [<Välj \> ](api-management-advanced-policies.md#choose) princip uttryck tillämpas, kan bas-URL: en för Server delen ändras igen antingen till `http://contoso.com/api/8.2` eller `http://contoso.com/api/9.1` , beroende på värdet för Frågeparametern för versions förfrågan. Om värdet till exempel är `"2013-15"` den slutgiltiga URL: en för begäran blir det `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef` .

Om ytterligare omvandling av begäran önskas kan du använda andra [omvandlings principer](api-management-transformation-policies.md#TransformationPolicies) . Om du till exempel vill ta bort versions fråge parametern nu att begäran dirigeras till en versions Server del, kan du använda  [parameter principen ange frågesträng](api-management-transformation-policies.md#SetQueryStringParameter) för att ta bort det nu redundanta versions-attributet.

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
I det här exemplet dirigerar principen begäran till en Service Fabric-Server del med hjälp av userId-frågesträngen som partitionsnyckel och använder den primära repliken i partitionen.

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|Set-backend-tjänst|Rot element.|Ja|

### <a name="attributes"></a>Attribut

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|bas-URL|Bas-URL för ny server dels tjänst.|En av `base-url` eller `backend-id` måste vara närvarande.|Ej tillämpligt|
|backend-ID|Identifierare för den server del som ska skickas till. (Backend-entiteter hanteras via [Azure Portal](how-to-configure-service-fabric-backend.md), [API](/rest/api/apimanagement)och [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|En av `base-url` eller `backend-id` måste vara närvarande.|Ej tillämpligt|
|SF-partition – nyckel|Endast tillämpligt om Server delen är en Service Fabric tjänst och anges med backend-ID. Används för att matcha en speciell partition från namn matchnings tjänsten.|Inga|Ej tillämpligt|
|SF-Replica-typ|Endast tillämpligt om Server delen är en Service Fabric tjänst och anges med backend-ID. Kontrollerar om begäran ska gå till den primära eller sekundära repliken av en partition. |Inga|Ej tillämpligt|
|SF-resolve-Condition|Endast tillgängligt om Server delen är en Service Fabric-tjänst. Villkor som identifierar om anropet till Service Fabric Server del måste upprepas med ny lösning.|Inga|Ej tillämpligt|
|SF-tjänst-instans-namn|Endast tillgängligt om Server delen är en Service Fabric-tjänst. Gör det möjligt att ändra tjänst instanser vid körning. |Inga|Ej tillämpligt|
|SF-Listener-Name|Endast tillämpligt om Server delen är en Service Fabric tjänst och anges med backend-ID. Med Service Fabric Reliable Services kan du skapa flera lyssnare i en tjänst. Det här attributet används för att välja en speciell lyssnare när en server del Reliable service har fler än en lyssnare. Om det här attributet inte anges kommer API Management att försöka använda en lyssnare utan ett namn. En lyssnare utan ett namn är vanligt för Reliable Services som bara har en lyssnare. |Inga|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, backend

-   **Princip omfattningar:** alla omfattningar

##  <a name="set-body"></a><a name="SetBody"></a> Ange brödtext
 Använd `set-body` principen för att ange meddelande texten för inkommande och utgående begär Anden. För att komma åt meddelande texten kan du använda `context.Request.Body` -egenskapen eller `context.Response.Body` , beroende på om principen finns i avsnittet ingående eller utgående.

> [!IMPORTANT]
>  Observera att som standard när du öppnar meddelande texten med `context.Request.Body` eller `context.Response.Body` , går den ursprungliga meddelande texten förlorad och måste anges genom att returnera bröd texten i uttrycket. Om du vill bevara bröd innehållet anger du `preserveContent` parametern till `true` vid åtkomst till meddelandet. Om `preserveContent` är inställt på `true` och en annan brödtext returneras av uttrycket används den returnerade texten.
>
>  Tänk på följande när du använder `set-body` principen.
>
> - Om du använder `set-body` principen för att returnera en ny eller uppdaterad text behöver du inte ange `preserveContent` till `true` eftersom du uttryckligen levererar det nya innehållet.
>   -   Att bevara innehållet i ett svar i den inkommande pipelinen är inte meningsfullt eftersom det inte finns något svar ännu.
>   -   Att bevara innehållet i en begäran i den utgående pipelinen är inte meningsfullt eftersom begäran redan har skickats till Server delen i det här läget.
>   -   Om den här principen används när det inte finns någon meddelande text, till exempel i ett inkommande GET, uppstår ett undantag.

 Mer information finns i `context.Request.Body` `context.Response.Body` avsnitten, och `IMessage` i [Sammanhangs variabel](api-management-policy-expressions.md#ContextVariables) tabellen.

### <a name="policy-statement"></a>Princip kommentar

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Exempel

#### <a name="literal-text-example"></a>Exempel på litteral text

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Exempel på att komma åt bröd texten som en sträng. Observera att vi behåller den ursprungliga begär ande texten så att vi kan komma åt den senare i pipelinen.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Exempel på att komma åt bröd texten som en JObject. Observera att eftersom vi inte reserverar den ursprungliga begär ande texten, leder det till ett undantag om du kommer åt det senare i pipelinen.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Filtrera svar baserat på produkt
 I det här exemplet visas hur du utför innehålls filtrering genom att ta bort data element från svaret som tagits emot från backend-tjänsten när du använder `Starter` produkten. En demonstration av hur du konfigurerar och använder den här principen finns i avsnittet [Cloud Cover avsnitt 177: fler API Management funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och fast-forward till 34:30. Börja vid 31:50 för att se en översikt över [den mörke prognos-API: et](https://developer.forecast.io/) som används för den här demon.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="using-liquid-templates-with-set-body"></a>Använda flytande mallar med uppsättnings text
`set-body`Principen kan konfigureras för att använda [vätske](https://shopify.github.io/liquid/basics/introduction/) mall-språket för att transformera bröd texten i en begäran eller ett svar. Detta kan vara mycket effektivt om du behöver ändra form på formatet fullständigt för ditt meddelande.

> [!IMPORTANT]
> Implementeringen av vätska som används i `set-body` principen konfigureras i C#-läge. Detta är särskilt viktigt när du gör saker som filtrering. Som exempel kräver ett datum filter användning av Pascal-hölje och C#-datum format, t. ex.:
>
> {{Body. foo. startDateTime | Date: "yyyyMMddTHH: mm: ddZ"}}

> [!IMPORTANT]
> För att korrekt binda till en XML-text med hjälp av vätske mal len använder du en `set-header` princip för att ange innehålls typ till antingen Application/XML, text/XML (eller någon typ som slutar med + XML). för en JSON-text måste den vara Application/JSON, text/JSON (eller någon typ som slutar med + JSON).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Konvertera JSON till SOAP med en flytande mall
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>Transformera JSON med en flytande mall
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|Set-Body|Rot element. Innehåller bröd texten eller ett uttryck som returnerar en brödtext.|Ja|

### <a name="properties"></a>Egenskaper

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|mall|Används för att ändra det mall-läge som den angivna text principen ska köras i. För närvarande är det enda värde som stöds:<br /><br />– flytande – den uppsättnings huvud principen använder vätske mall-motorn |Inga||

För att få åtkomst till information om begäran och svar kan den flytande mallen binda till ett kontext objekt med följande egenskaper: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, utgående, backend

-   **Princip omfattningar:** alla omfattningar

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a> Ange HTTP-huvud
 `set-header`Principen tilldelar ett värde till ett befintligt svar och/eller begär ande huvud eller lägger till ett nytt svar och/eller begär ande huvud.

 Infogar en lista med HTTP-huvuden i ett HTTP-meddelande. När den placeras i en inkommande pipeline anger den här principen HTTP-huvuden för den begäran som skickas till mål tjänsten. När den placeras i en utgående pipeline anger den här principen HTTP-huvudena för det svar som skickas till gatewayens klient.

### <a name="policy-statement"></a>Princip kommentar

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Exempel

#### <a name="example---adding-header-override-existing"></a>Exempel – lägga till sidhuvud, Åsidosätt befintlig

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Exempel – tar bort rubrik

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Vidarebefordra kontext information till backend-tjänsten
 Det här exemplet visar hur du tillämpar principer på API-nivå för att tillhandahålla kontext information till backend-tjänsten. En demonstration av hur du konfigurerar och använder den här principen finns i avsnittet [Cloud Cover avsnitt 177: fler API Management funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och fast-forward till 10:30. Vid 12:10 finns det en demonstration av att anropa en åtgärd i Developer-portalen där du kan se principen på arbetet.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Mer information finns i [princip uttryck](api-management-policy-expressions.md) och [Sammanhangs variabel](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Flera värden i ett sidhuvud sammanfogas till en CSV-sträng, till exempel: `headerName: value1,value2,value3`
>
> Undantag inkluderar standardiserade rubriker, vilka värden:
> - får innehålla kommatecken ( `User-Agent` , `WWW-Authenticate` , `Proxy-Authenticate` ),
> - kan innehålla datum ( `Cookie` , `Set-Cookie` , `Warning` ),
> - innehåller datum ( `Date` ,,,,, `Expires` `If-Modified-Since` `If-Unmodified-Since` `Last-Modified` `Retry-After` ).
>
> I händelse av dessa undantag kommer flera huvud värden inte att sammanfogas till en sträng och skickas som separata huvuden, till exempel: `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|Ange rubrik|Rot element.|Ja|
|värde|Anger värdet för huvudet som ska ställas in. För flera rubriker med samma namn lägger du till ytterligare `value` element.|Inga|

### <a name="properties"></a>Egenskaper

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|exists-åtgärd|Anger vilken åtgärd som ska vidtas när rubriken redan har angetts. Det här attributet måste ha ett av följande värden.<br /><br /> -override-ersätter värdet i den befintliga rubriken.<br />-Skip-ersätter inte det befintliga huvud-värdet.<br />-append – lägger till värdet i det befintliga huvud-värdet.<br />-Delete – tar bort rubriken från begäran.<br /><br /> När du har angett `override` flera poster med samma namn resulterar det i att rubriken anges enligt alla poster (som visas flera gånger). endast listade värden anges i resultatet.|Inga|åsidosättningsinställning|
|name|Anger namnet på den rubrik som ska anges.|Ja|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a> Ange frågesträngparametern
 `set-query-parameter`Principen lägger till, ersätter värdet eller tar bort förfrågan om frågesträngparametern. Kan användas för att skicka frågeparametrar som förväntas av backend-tjänsten som är valfria eller aldrig finns i begäran.

### <a name="policy-statement"></a>Princip kommentar

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

#### <a name="example"></a>Exempel

```xml

<set-query-parameter name="api-key" exists-action="skip">
  <value>12345678901</value>
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Vidarebefordra kontext information till backend-tjänsten
 Det här exemplet visar hur du tillämpar principer på API-nivå för att tillhandahålla kontext information till backend-tjänsten. En demonstration av hur du konfigurerar och använder den här principen finns i avsnittet [Cloud Cover avsnitt 177: fler API Management funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och fast-forward till 10:30. Vid 12:10 finns det en demonstration av att anropa en åtgärd i Developer-portalen där du kan se principen på arbetet.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Mer information finns i [princip uttryck](api-management-policy-expressions.md) och [Sammanhangs variabel](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|Set-Query-parameter|Rot element.|Ja|
|värde|Anger värdet på frågeparametern som ska ställas in. För flera frågeparametrar med samma namn lägger du till ytterligare `value` element.|Ja|

### <a name="properties"></a>Egenskaper

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|exists-åtgärd|Anger vilken åtgärd som ska vidtas när frågeparametern redan är angiven. Det här attributet måste ha ett av följande värden.<br /><br /> -override-ersätter värdet för den befintliga parametern.<br />-Skip-ersätter inte det befintliga värdet för Frågeparametern.<br />-append – lägger till värdet i det befintliga värdet för Frågeparametern.<br />-Delete-tar bort Frågeparametern från begäran.<br /><br /> När du har angett `override` flera poster med samma namn resulterar det i att Frågeparametern anges enligt alla poster (som visas flera gånger). endast listade värden anges i resultatet.|Inga|åsidosättningsinställning|
|name|Anger namnet på frågeparametern som ska anges.|Ja|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, backend

-   **Princip omfattningar:** alla omfattningar

##  <a name="rewrite-url"></a><a name="RewriteURL"></a> Skriv om URL
 `rewrite-uri`Principen konverterar en URL för begäran från det offentliga formuläret till det formulär som förväntas av webb tjänsten, som visas i följande exempel.

- Offentlig URL- `http://api.example.com/storenumber/ordernumber`

- Begär URL – `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Den här principen kan användas när en mänsklig och/eller webbläsarbaserad URL ska omvandlas till det URL-format som förväntas av webb tjänsten. Den här principen måste tillämpas när du exponerar ett alternativt URL-format, t. ex. rensade URL: er, RESTful URL: er, användarvänliga URL: er eller SEO-vänliga URL: er som är helt strukturella URL: er som inte innehåller en frågesträng och som i stället bara innehåller sökvägen till resursen (efter schemat och utfärdaren). Detta görs ofta för användning av "smak, användbarhet" eller sökmotor optimering (SEO).

> [!NOTE]
>  Du kan bara lägga till parametrar för frågesträngar med hjälp av principen. Du kan inte lägga till extra Sök vägs parametrar i URL: en för omskrivning.

### <a name="policy-statement"></a>Princip kommentar

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|omskrivning – URI|Rot element.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standardvärde|
|---------------|-----------------|--------------|-------------|
|mall|Den faktiska webb tjänst-URL: en med parametrar för frågesträng. När du använder uttryck måste hela värdet vara ett uttryck.|Ja|Ej tillämpligt|
|Kopiera omatchade-params|Anger om frågeparametrar i den inkommande begäran som inte finns i den ursprungliga URL-mallen läggs till i URL: en som definieras av mallen för omskrivning|Inga|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande

-   **Princip omfattningar:** alla omfattningar

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a> Transformera XML med hjälp av en XSLT
 `Transform XML using an XSLT`Principen använder en XSL-transformering för XML i begäran eller svars texten.

### <a name="policy-statement"></a>Princip kommentar

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Exempel

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
          <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
          </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|XSL-Transform|Rot element.|Ja|
|parameter|Används för att definiera variabler som används i transformeringen|Inga|
|XSL: stylesheet|Rot format element. Alla element och attribut som definieras i följer standard [specifikationen för XSLT](https://www.w3.org/TR/xslt)|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, utgående

-   **Princip omfattningar:** alla omfattningar

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande avsnitt:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Princip referens](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar
+ [Principexempel](./policy-reference.md)