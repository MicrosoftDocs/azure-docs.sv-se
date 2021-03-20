---
title: Använda API Management-tjänsten för att generera HTTP-begäranden
description: Lär dig att använda principer för begäran och svar i API Management för att anropa externa tjänster från ditt API
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2f4bd040d7e5858fd561444f56dbce7b3f940d9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92742392"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Använda externa tjänster från Azure API Management-tjänsten
De principer som är tillgängliga i Azure API Management-tjänsten kan göra en mängd användbart arbete baserat enbart på inkommande begäran, utgående svar och grundläggande konfigurations information. Men att kunna interagera med externa tjänster från API Management principer öppnar flera fler möjligheter.

Du har tidigare sett hur du interagerar med [tjänsten Azure Event Hub för loggning, övervakning och analys](api-management-log-to-eventhub-sample.md). Den här artikeln visar principer som gör att du kan interagera med en extern HTTP-baserad tjänst. Dessa principer kan användas för att utlösa fjärrhändelser eller för att hämta information som används för att ändra den ursprungliga begäran och svaret på något sätt.

## <a name="send-one-way-request"></a>Skicka – envägs-begäran
Den enklaste externa interaktionen är förmodligen en brand-och-glöm-stil för begäran som gör det möjligt för en extern tjänst att meddelas av någon typ av viktig händelse. Kontroll flödes principen `choose` kan användas för att identifiera vilken typ av villkor som du är intresse rad av.  Om villkoret är uppfyllt kan du göra en extern HTTP-begäran med hjälp av principen för att [skicka en-enkelriktad begäran](./api-management-advanced-policies.md#SendOneWayRequest) . Detta kan vara en begäran till ett meddelande system som HipChat eller slack, eller ett e-post-API som SendGrid eller MailChimp, eller för kritiska support ärenden som PagerDuty. Alla dessa meddelande system har enkla HTTP-API: er som kan anropas.

### <a name="alerting-with-slack"></a>Avisering med slack
Följande exempel visar hur du skickar ett meddelande till ett slack chatt-rum om HTTP-svarets status kod är större än eller lika med 500. Ett fel av 500-intervall indikerar ett problem med Server dels-API: et som klienten för API: et inte kan lösa sig själva. Det krävs vanligt vis en viss typ av åtgärder på API Management del.  

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>
```

Slacket har begreppet inkommande Webhooks. När du konfigurerar en inkommande Web Hook genererar slack en särskild URL, vilket gör att du kan göra en enkel POST-begäran och skicka ett meddelande till slack-kanalen. JSON-texten som du skapar baseras på ett format som definieras av slack.

![Slack-webbhook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Är brand och glömma tillräckligt?
Det finns vissa kompromisser när du använder en Fire-och-glömma-typ av begäran. Om det av någon anledning Miss lyckas, kommer begäran inte att rapporteras. I den här situationen är komplexiteten med att ha ett sekundärt rapporterings system och den ytterligare prestanda kostnaden för att vänta på responsen inte berättigad. För scenarier där det är viktigt att kontrol lera svaret är det ett bättre alternativ att [skicka begäran](./api-management-advanced-policies.md#SendRequest) .

## <a name="send-request"></a>Send-Request
`send-request`Principen gör det möjligt att använda en extern tjänst för att utföra komplexa bearbetnings funktioner och returnera data till API Management-tjänsten som kan användas för ytterligare princip bearbetning.

### <a name="authorizing-reference-tokens"></a>Auktoriserar referens-token
En större funktion i API Management skyddar Server dels resurser. Om auktoriseringsservern som används av ditt API skapar [JWT-token](https://jwt.io/) som en del av dess OAuth2-flöde, som [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) gör, så kan du använda `validate-jwt` principen för att kontrol lera giltigheten för token. Vissa auktoriseringsregler skapar vad som kallas [Reference-token](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) som inte kan verifieras utan att göra en motringning till auktoriseringsservern.

### <a name="standardized-introspection"></a>Standardiserade introspektionsfunktionerna
Tidigare har det inte gjorts något standardiserat sätt att verifiera en reference-token med en Authorization-Server. En nyligen föreslagen standard [RFC 7662](https://tools.ietf.org/html/rfc7662) publicerades av IETF som definierar hur en resurs Server kan verifiera att en token är giltig.

### <a name="extracting-the-token"></a>Extraherar token
Det första steget är att extrahera token från Authorization-huvudet. Huvudvärdet ska vara formaterat med `Bearer` Authorization-schemat, ett enda utrymme och sedan token-token enligt [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Det finns tyvärr fall där Authorization-schemat utelämnas. För att kontot ska kunna parsas, API Management delas huvudets värde på ett blank steg och den sista strängen från den returnerade matrisen med strängar väljs. Detta ger en lösning för dåligt formaterade auktoriseringsarkiv.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Göra verifierings förfrågan
När API Management har en autentiseringstoken kan API Management göra begäran om att validera token. RFC 7662 anropar den här processen introspektionsfunktionerna och kräver att du använder `POST` ett HTML-formulär till introspektionsfunktionerna-resursen. HTML-formuläret måste innehålla minst ett nyckel/värde-par med nyckeln `token` . Den här begäran till auktoriseringsservern måste också vara autentiserad, för att se till att skadliga klienter inte kan gå med på giltiga tokens.

```xml
<send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
  <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
  <set-method>POST</set-method>
  <set-header name="Authorization" exists-action="override">
    <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
  </set-header>
  <set-header name="Content-Type" exists-action="override">
    <value>application/x-www-form-urlencoded</value>
  </set-header>
  <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
</send-request>
```

### <a name="checking-the-response"></a>Kontrollerar svaret
`response-variable-name`Attributet används för att ge åtkomst till det returnerade svaret. Namnet som definieras i den här egenskapen kan användas som en nyckel i `context.Variables` ord listan för att komma åt `IResponse` objektet.

Från objektet Response kan du hämta bröd texten och RFC 7622 meddelar API Management att svaret måste vara ett JSON-objekt och måste innehålla minst en egenskap `active` som kallas ett booleskt värde. När `active` är sant anses token vara giltig.

Alternativt, om auktoriseringsservern inte innehåller fältet "aktiv" för att ange om token är giltig, använder du ett verktyg som Postman för att avgöra vilka egenskaper som anges i en giltig token. Om ett giltigt token-svar till exempel innehåller en egenskap som kallas "expires_in", kontrol lera om det här egenskaps namnet finns i svaret på auktoriseringsservern på följande sätt:

<när Condition = "@ ((IResponse)-kontexten. Variabler ["tokenstate"]). Body.As <JObject> (). Egenskap ("expires_in") = = null) ">

### <a name="reporting-failure"></a>Rapporterings problem
Du kan använda en `<choose>` princip för att identifiera om token är ogiltig och, i så fall, returnera ett 401-svar.

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>
```

Enligt [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) , som beskriver hur `bearer` tokens ska användas, kan API Management också returnera en `WWW-Authenticate` rubrik med 401-svaret. WWW-Authenticate är avsedd att instruera en klient om hur man skapar en korrekt auktoriserad begäran. På grund av de många metoder som är möjliga med OAuth2-ramverket är det svårt att kommunicera all nödvändig information. Lyckligt vis finns det ansträngningar för att hjälpa [klienter att identifiera hur de ska auktorisera begär anden till en resurs Server](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Slutgiltig lösning
I slutet får du följande princip:

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
          <!-- Check active property in response -->
          <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
              <!-- Return 401 Unauthorized with http-problem payload -->
              <return-response response-variable-name="existing response variable">
                  <set-status code="401" reason="Unauthorized" />
                  <set-header name="WWW-Authenticate" exists-action="override">
                      <value>Bearer error="invalid_token"</value>
                  </set-header>
              </return-response>
          </when>
      </choose>
  <base />
</inbound>
```

Detta är bara ett av många exempel på hur `send-request` principen kan användas för att integrera användbara externa tjänster i processen med begär Anden och svar som passerar genom den API Management tjänsten.

## <a name="response-composition"></a>Svars sammansättning
`send-request`Principen kan användas för att förbättra en primär begäran till ett Server dels system, som du såg i föregående exempel, eller så kan den användas som en fullständig ersättning för Server dels anropet. Med den här metoden kan du enkelt skapa sammansatta resurser som aggregeras från flera olika system.

### <a name="building-a-dashboard"></a>Skapa en instrument panel
Ibland vill du kunna visa information som finns i flera backend-system, till exempel för att driva en instrument panel. KPI: erna kommer från alla olika Server delar, men du föredrar att inte ge direkt åtkomst till dem och det skulle vara bra om all information kan hämtas i en enskild begäran. Kanske vissa av Server delens information behöver viss segmentering och finfördela och en lite sanerad första! Att kunna cachelagra den sammansatta resursen är ett bra sätt att minska Server dels belastningen när du vet att användarna har till följd av F5-nyckeln för att se om deras underpresterande mått kan ändras.    

### <a name="faking-the-resource"></a>Faking resursen
Det första steget för att skapa en instrument panels resurs är att konfigurera en ny åtgärd i Azure Portal. Detta är en plats hållare-åtgärd som används för att konfigurera en sammansättnings princip för att bygga den dynamiska resursen.

![Instrument panels åtgärd](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Göra begär Anden
När åtgärden har skapats kan du konfigurera en princip som är specifik för den åtgärden. 

![Skärm bild som visar skärmen princip omfång.](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Det första steget är att extrahera alla frågeparametrar från den inkommande begäran, så att du kan vidarebefordra dem till Server delen. I det här exemplet visar instrument panelen information som baseras på en tids period och därför har en- `fromDate` och- `toDate` parameter. Du kan använda `set-variable` principen för att extrahera informationen från fråge-URL: en.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

När du har den här informationen kan du göra förfrågningar till alla Server dels system. Varje begäran skapar en ny URL med parameter informationen och anropar dess respektive server och lagrar svaret i en Sammanhangs variabel.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Dessa begär Anden körs i följd, vilket inte är idealiskt. 

### <a name="responding"></a>Tillgodose
Om du vill skapa ett sammansatt svar kan du använda [Return-Response-](./api-management-advanced-policies.md#ReturnResponse) principen. `set-body`Elementet kan använda ett uttryck för att skapa en ny `JObject` med alla komponent representationer inbäddade som egenskaper.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

Den fullständiga principen ser ut så här:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

I konfigurationen av plats hållar åtgärden kan du konfigurera instrument panelens resurs att cachelagras i minst en timme. 

## <a name="summary"></a>Sammanfattning
Azure API Management-tjänsten ger flexibla principer som kan tillämpas selektivt på HTTP-trafik och möjliggör sammansättning av backend-tjänster. Oavsett om du vill förbättra API-gatewayen med aviserings funktioner, verifiering, verifierings funktioner eller skapa nya sammansatta resurser baserat på flera backend-tjänster, `send-request` öppnar och relaterade principerna en värld av möjligheter.
