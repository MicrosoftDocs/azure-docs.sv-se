---
title: Integrerings principer för Azure API Management Dapr | Microsoft Docs
description: Lär dig mer om Azure API Management-principer för att interagera med Dapr mikrotjänster-tillägg.
author: vladvino
ms.author: vlvinogr
ms.date: 02/18/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 051bf4398555f318f613c66d58ec65be1d30e215
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646817"
---
# <a name="api-management-dapr-integration-policies"></a>API Management integrerings principer för Dapr

Det här avsnittet innehåller en referens för Dapr-integration API Management-principer. Dapr är en portabel körning för att skapa tillstånds lösa och tillstånds känsliga mikrotjänster-baserade program med valfritt språk eller ramverk. Det codifies vanliga mikrotjänst mönster, t. ex. tjänst identifiering och anrop med hjälp av omprövnings logik, publicera-och-prenumerera med semantiskt inleveranser eller anslutnings bara bindnings resurser för att under lätta kompositionen med hjälp av externa tjänster. Gå till [dapr.io](https://dapr.io) om du vill ha detaljerad information och instruktioner om hur du kommer igång med dapr. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](api-management-howto-policies.md).

> [!CAUTION]
> Principer som refereras i det här avsnittet finns i offentlig för hands version och omfattas [av kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versioner.

> [!IMPORTANT]
> Principer som refereras i det här avsnittet fungerar endast i den [själv värdbaserade versionen av API Management Gateway](self-hosted-gateway-overview.md) med stöd för Dapr aktiverat.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Aktivera stöd för Dapr i den egna värdbaserade gatewayen

Om du vill aktivera Dapr-stöd i den lokala gatewayen lägger du till [Dapr-anteckningarna](https://github.com/dapr/docs/blob/master/README.md) nedan till [Kubernetes distributions mal len](how-to-deploy-self-hosted-gateway-kubernetes.md) och ersätter "App-Name" med önskat namn. En fullständig genom gång av hur du konfigurerar och använder API Management med Dapr finns [här](https://aka.ms/apim/dapr/walkthru).
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Integreringspolicyer för Distributed Apps Runtime (Dapr)

-  [Skicka begäran till en tjänst](api-management-dapr-policies.md#invoke): använder Dapr runtime för att hitta och tillförlitligt kommunicera med en Dapr-mikrotjänst. Mer information om tjänst anrop i Dapr finns i beskrivningen i den här [README](https://github.com/dapr/docs/blob/master/README.md#service-invocation) -filen.
-  [Skicka meddelande till pub/sub-ämnet](api-management-dapr-policies.md#pubsub): använder Dapr runtime för att publicera ett meddelande till en publicera/prenumerera-ämne. Mer information om publicerings-och prenumerations meddelanden i Dapr finns i beskrivningen i den här [README](https://github.com/dapr/docs/blob/master/README.md) -filen.
-  [Utlös utgående bindning](api-management-dapr-policies.md#bind): använder Dapr runtime för att anropa ett externt system via utgående bindning. Mer information om bindningar i Dapr finns i beskrivningen i den här [README](https://github.com/dapr/docs/blob/master/README.md) -filen.

## <a name="send-request-to-a-service"></a><a name="invoke"></a> Skicka begäran till en tjänst

Den här principen anger mål-URL: en för den aktuella begäran att `http://localhost:3500/v1.0/invoke/{app-id}[.{ns-name}]/method/{method-name}` ersätta mallparametrar med värden som anges i princip instruktionen.

Principen förutsätter att Dapr körs i en sidvagn-behållare i samma Pod som gatewayen. Vid mottagning av begäran utför Dapr-körningen tjänst identifiering och faktiskt anrop, inklusive möjlig protokoll översättning mellan HTTP och gRPC, nya försök, distribuerad spårning och fel hantering.

### <a name="policy-statement"></a>Princip kommentar

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" dapr-namespace="ns-name" />
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

Följande exempel visar hur du anropar metoden "back" på mikrotjänsten med namnet "echo". `set-backend-service`Principen anger mål-URL: en till `http://localhost:3500/v1.0/invoke/echo.echo-app/method/back` . `forward-request`Principen skickar begäran till Dapr runtime, som levererar den till mikrotjänsten.

`forward-request`Principen visas här för tydlighetens skull. Principen är vanligt vis "ärvd" från det globala omfånget via `base` nyckelordet.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" dapr-namespace="echo-app" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Element

| Element             | Beskrivning  | Krävs |
|---------------------|--------------|----------|
| Set-backend-tjänst | Rot element | Ja      |

### <a name="attributes"></a>Attribut

| Attribut        | Beskrivning                     | Krävs | Standardvärde |
|------------------|---------------------------------|----------|---------|
| backend-ID       | Måste anges till "dapr"           | Ja      | Ej tillämpligt     |
| dapr – app-ID      | Namnet på mål-mikrotjänsten. Används för att forma [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) -parametern i Dapr.| Ja | Ej tillämpligt |
| dapr-metod      | Namnet på metoden eller en URL som ska anropas på mål-mikrotjänsten. Mappar till [metod-namn-](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) parametern i Dapr.| Ja | Ej tillämpligt |
| dapr – namnrymd   | Namn på namn området som mål-mikrotjänsten är i. Används för att forma [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) -parametern i Dapr.| Inga | Ej tillämpligt |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** inkommande
- **Princip omfattningar:** alla omfattningar

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Skicka meddelande till pub/sub-ämne

Den här principen instruerar API Management Gateway att skicka ett meddelande till ett ämne för publicering/prenumeration av Dapr. Principen utför detta genom att göra en HTTP POST-begäran för att `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` ersätta mallparametrar och lägga till innehåll som anges i princip instruktionen.

Principen förutsätter att Dapr runtime körs i en sidvagn-behållare i samma Pod som gatewayen. Dapr runtime implementerar pub/sub-semantik.

### <a name="policy-statement"></a>Princip kommentar

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

I följande exempel visas hur bröd texten i den aktuella begäran skickas till [avsnittet](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters) "nytt" i pub/sub- [komponenten](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)"Orders". Svar som tas emot från Dapr-körningen lagras i posten "Dapr-Response" i variabeln Collection i objektet [context](api-management-policy-expressions.md#ContextVariables) .

Om Dapr runtime inte kan hitta mål avsnittet, till exempel och svarar med ett fel, utlöses avsnittet "On-Error". Svaret som togs emot från Dapr runtime returneras till anroparen orda Grant. Annars returneras standard `200 OK` svaret.

Avsnittet "backend" är tomt och begäran vidarebefordras inte till Server delen.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Element

| Element             | Beskrivning  | Krävs |
|---------------------|--------------|----------|
| publicera-till-dapr     | Rot element | Ja      |

### <a name="attributes"></a>Attribut

| Attribut        | Beskrivning                     | Krävs | Standardvärde |
|------------------|---------------------------------|----------|---------|
| pubsub-namn      | Namnet på mål PubSub-komponenten. Mappar till [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) -parametern i Dapr. Om det inte finns, måste __ämnets__ attributvärde vara i formatet `pubsub-name/topic-name` .    | Inga       | Inget    |
| ämne            | Namnet på ämnet. Mappar till [ämnes](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) parametern i Dapr.               | Ja      | Ej tillämpligt     |
| Ignorera-fel     | Om alternativet är inställt på `true` anger att principen inte ska utlösa ["på fel"](api-management-error-handling-policies.md) -avsnittet vid mottagning av fel från Dapr runtime | Inga | `false` |
| svar-variabel-namn | Namnet på den [variabel](api-management-policy-expressions.md#ContextVariables) samlings post som ska användas för att lagra svar från Dapr runtime | Inga | Inget |
| timeout | Tid (i sekunder) att vänta på att Dapr runtime svarar. Kan vara mellan 1 och 240 sekunder. | Inga | 5 |
| mall | Mall-motor som används för att transformera meddelande innehållet. "Flytande" är det enda värde som stöds. | Inga | Inget |
| innehålls typ | Typ av meddelande innehåll. "Application/JSON" är det enda värde som stöds. | Inga | Inget |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** inkommande, utgående, på-fel
- **Princip omfattningar:** alla omfattningar

## <a name="trigger-output-binding"></a><a name="bind"></a> Utlös utgående bindning

Den här principen instruerar API Management Gateway att utlösa en utgående Dapr- [bindning](https://github.com/dapr/docs/blob/master/README.md). Principen utför detta genom att göra en HTTP POST-begäran för att `http://localhost:3500/v1.0/bindings/{{bind-name}}` ersätta mallparameter och lägga till innehåll som anges i princip instruktionen.

Principen förutsätter att Dapr runtime körs i en sidvagn-behållare i samma Pod som gatewayen. Dapr runtime ansvarar för att anropa den externa resurs som representeras av bindningen.

### <a name="policy-statement"></a>Princip kommentar

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

I följande exempel visas Utlös ande av utgående bindning med namnet "External-Systems" med åtgärds namnet "Create", metadata som består av två nyckel/värde-objekt med namnet "källa" och "klient-IP", och brödtext som kommer från den ursprungliga begäran. Svar som tas emot från Dapr-körningen anges i posten "bind-Response" i variabeln Collection i objektet [context](api-management-policy-expressions.md#ContextVariables) .

Om Dapr runtime Miss lyckas av någon anledning och svarar med ett fel, utlöses avsnittet "On-Error" och svaret som tas emot från Dapr-körningen returneras till anroparen orda Grant. Annars returneras standard `200 OK` svaret.

Avsnittet "backend" är tomt och begäran vidarebefordras inte till Server delen.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Element

| Element             | Beskrivning  | Krävs |
|---------------------|--------------|----------|
| Invoke-dapr-binding | Rot element | Ja      |
| metadata            | Bindning av vissa metadata i form av nyckel/värde-par. Mappar till egenskapen [metadata](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) i Dapr. | Inga |
| data            | Meddelandets innehåll. Mappar till [data](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) -egenskapen i Dapr. | Inga |


### <a name="attributes"></a>Attribut

| Attribut        | Beskrivning                     | Krävs | Standardvärde |
|------------------|---------------------------------|----------|---------|
| name            | Mål bindnings namn. Måste matcha namnet på de bindningar som [definierats](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure) i Dapr.           | Ja      | Ej tillämpligt     |
| operation       | Mål åtgärds namn (bindande specifika). Mappar till egenskapen [operation](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) i Dapr. | Inga | Inget |
| Ignorera-fel     | Om alternativet är inställt på `true` anger att principen inte ska utlösa ["på fel"](api-management-error-handling-policies.md) -avsnittet vid mottagning av fel från Dapr runtime | Inga | `false` |
| svar-variabel-namn | Namnet på den [variabel](api-management-policy-expressions.md#ContextVariables) samlings post som ska användas för att lagra svar från Dapr runtime | Inga | Inget |
| timeout | Tid (i sekunder) att vänta på att Dapr runtime svarar. Kan vara mellan 1 och 240 sekunder. | Inga | 5 |
| mall | Mall-motor som används för att transformera meddelande innehållet. "Flytande" är det enda värde som stöds. | Inga | Inget |
| innehålls typ | Typ av meddelande innehåll. "Application/JSON" är det enda värde som stöds. | Inga | Inget |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** inkommande, utgående, på-fel
- **Princip omfattningar:** alla omfattningar
