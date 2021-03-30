---
title: Custom caching in Azure API Management (Anpassad cachelagring i Azure API Management)
description: Lär dig hur du cachelagrar objekt efter nyckel i Azure API Management. Du kan ändra nyckeln med hjälp av begärandehuvuden.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: cf9901b4e49460dd2fb91dceaf239571058c5284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88213318"
---
# <a name="custom-caching-in-azure-api-management"></a>Custom caching in Azure API Management (Anpassad cachelagring i Azure API Management)
Azure API Management-tjänsten har inbyggt stöd för [cachelagring av HTTP-svar](api-management-howto-cache.md) med hjälp av resurs-URL som nyckel. Nyckeln kan ändras av begärandehuvuden med hjälp av `vary-by` egenskaperna. Detta är användbart för cachelagring av hela HTTP-svar (kallas även för representationer), men ibland är det praktiskt att bara cachelagra en del av en representation. De nya principerna [cache-lookup-Value](./api-management-caching-policies.md#GetFromCacheByKey) och [cache-Store-Value](./api-management-caching-policies.md#StoreToCacheByKey) ger möjlighet att lagra och hämta godtyckliga data typer från princip definitioner. Den här möjligheten lägger också till värde till den tidigare skapade principen för att [Skicka begär Anden](./api-management-advanced-policies.md#SendRequest) eftersom du nu kan cachelagra svar från externa tjänster.

## <a name="architecture"></a>Arkitektur
API Management tjänsten använder en delad datacache per klient, så att du, när du skalar upp till flera enheter, fortfarande får åtkomst till samma cachelagrade data. Men när du arbetar med en distribution i flera regioner finns det oberoende cacheminnen i varje region. Det är viktigt att inte behandla cachen som ett data lager, där det är den enda källan till viss information. Om du har gjort det och senare beslutade att dra nytta av distributionen i flera regioner kan kunder med användare som reser förlora åtkomsten till dessa cachelagrade data.

## <a name="fragment-caching"></a>Fragmentering av fragment
Det finns vissa fall där svar som returneras innehåller en del av data som är dyra att fastställa och som ännu inte är uppdaterade under en rimlig tids period. Ett exempel är att en tjänst som skapats av ett flyg bolag som tillhandahåller information om flyg bokningar, flyg status osv. Om användaren är medlem i programmet flyg plats program, hade de också information om deras aktuella status och ackumulerad mil. Den här användarspecifika informationen kan lagras i ett annat system, men det kan vara önskvärt att inkludera den i svar som returneras om flyg status och reservationer. Detta kan göras med hjälp av en process som kallas fragmentering av fragment. Den primära representationen kan returneras från ursprungs servern med någon typ av token för att indikera var användar relaterad information ska infogas. 

Överväg följande JSON-svar från ett Server dels-API.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

Och sekundär resurs `/userprofile/{userid}` som ser ut som,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

För att avgöra vilken användar information som ska inkluderas måste API Management identifiera vem användaren är. Den här mekanismen är implementerings beroende. Som exempel använder jag `Subject` ett `JWT` token-anspråk. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management lagrar `enduserid` värdet i en kontext variabel för senare användning. Nästa steg är att ta reda på om en tidigare begäran redan har hämtat användar informationen och lagrats i cacheminnet. För detta använder API Management `cache-lookup-value` principen.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Om det inte finns någon post i cachen som motsvarar nyckelvärdet, `userprofile` skapas ingen Sammanhangs variabel. API Management kontrollerar att sökningen lyckas med `choose` kontroll flödes principen.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Om `userprofile` kontext variabeln inte finns måste API Management göra en HTTP-begäran för att hämta den.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management använder `enduserid` för att konstruera URL: en till användar profil resursen. När API Management har svar hämtar den bröd texten från svaret och lagrar den i en Sammanhangs variabel.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

För att undvika API Management att göra HTTP-begäran igen, när samma användare gör en annan begäran, kan du ange att användar profilen ska lagras i cacheminnet.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management lagrar värdet i cachen med exakt samma nyckel som API Management ursprungligen försökte hämta det med. Den varaktighet som API Management väljer för att lagra värdet ska baseras på hur ofta informationen ändras och hur toleranta användare är inaktuella information. 

Det är viktigt att du inser att hämtning från cachen fortfarande är en process som inte är i processen, och kan ändå lägga till flera millisekunder i begäran. Fördelarna med att fastställa information om användar profilen tar längre tid än det som beror på att du behöver göra databas frågor eller samla in information från flera backend-ändar.

Det sista steget i processen är att uppdatera det returnerade svaret med användar profil informationen.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Du kan välja att inkludera citat tecken som en del av token så att även om ersättningen inte inträffar är svaret fortfarande en giltig JSON.  

När du kombinerar alla dessa steg är slut resultatet en princip som ser ut ungefär så här.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Den här metoden för cachelagring används främst på webbplatser där HTML består av Server sidan så att den kan återges som en enda sida. Det kan också vara användbart i API: er där klienter inte kan utföra HTTP-cachelagring på klient sidan eller inte är önskvärt att placera detta ansvar på klienten.

Samma typ av fragmentering av fragment kan också göras på backend-webbservrar med en Redis Caching-Server, men med hjälp av API Management tjänsten för att utföra det här arbetet är det användbart när de cachelagrade fragmenten kommer från olika Server delar än de primära svaren.

## <a name="transparent-versioning"></a>Transparent versions hantering
Det är vanligt att flera olika implementerings versioner av ett API stöds vid ett och samma tillfälle. Till exempel för att stödja olika miljöer (utveckling, testning, produktion osv.) eller för att stödja äldre versioner av API: et för att ge tid för API-konsumenter att migrera till nyare versioner. 

En metod för att hantera detta, i stället för att kräva att klient utvecklare ändrar webb adresserna från till, lagras `/v1/customers` `/v2/customers` i konsumentens profil data, vilken version av API de för närvarande vill använda och anropa lämplig URL för Server delen. För att fastställa rätt server dels-URL för att anropa en viss klient, är det nödvändigt att fråga vissa konfigurations data. Genom att cachelagra dessa konfigurations data kan API Management minimera prestanda försämringen för att göra sökningen.

Det första steget är att fastställa den identifierare som används för att konfigurera den önskade versionen. I det här exemplet valde jag att koppla versionen till produkt prenumerations nyckeln. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management gör sedan en cache-sökning för att se om den redan har hämtat den önskade klient versionen.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Sedan kontrollerar API Management om den inte hittades i cacheminnet.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

API Management hämtar det om API Management inte hittade det.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Extrahera texten i svars texten från svaret.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Lagra den igen i cacheminnet för framtida användning.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

Och uppdatera sedan Server dels-URL: en för att välja den version av tjänsten som önskas av klienten.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Den fullständiga principen är följande:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Att aktivera API-konsumenter för transparent styrning av vilken backend-version som används av klienter utan att behöva uppdatera och distribuera om klienter är en elegant lösning som åtgärdar många API-versioner.

## <a name="tenant-isolation"></a>Klient isolering
I större distributioner av flera innehavare, skapar vissa företag separata grupper av klienter i olika distributioner av Server dels maskin vara. Detta minimerar antalet kunder som påverkas av ett maskin varu problem på Server delen. Det gör det också möjligt att distribuera nya program varu versioner i steg. Vi rekommenderar att denna server dels arkitektur är transparent för API-konsumenter. Detta kan uppnås på ett liknande sätt som transparent versions hantering eftersom den baseras på samma teknik som att ändra server dels-URL: en med konfigurations tillstånd per API-nyckel.  

I stället för att returnera en prioriterad version av API: n för varje prenumerations nyckel returnerar du en identifierare som relaterar en klient till den tilldelade maskin gruppen. Den identifieraren kan användas för att skapa en lämplig URL för Server delen.

## <a name="summary"></a>Sammanfattning
Möjligheten att använda Azure API Management cache för att lagra alla typer av data ger effektiv åtkomst till konfigurations data som kan påverka hur en inkommande begäran bearbetas. Den kan också användas för att lagra datafragment som kan utöka svar som returneras från ett Server dels-API.
