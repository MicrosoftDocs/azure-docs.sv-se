---
title: Fel hantering i Azure API Management-principer | Microsoft Docs
description: Lär dig hur du svarar på fel tillstånd som kan uppstå under bearbetningen av begär anden i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: a3b6f90d0aa26b478c0f2fcefac55dcd509da437
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070953"
---
# <a name="error-handling-in-api-management-policies"></a>Felhantering i API Management-principer

Genom att tillhandahålla ett `ProxyError` objekt tillåter Azure-API Management att utgivare svarar på fel tillstånd, vilket kan inträffa under bearbetning av begär Anden. `ProxyError`Objektet nås via [kontexten. LastError](api-management-policy-expressions.md#ContextVariables) -egenskap och kan användas av principer i `on-error` avsnittet princip. Den här artikeln innehåller en referens för fel hanterings funktionerna i Azure API Management.

## <a name="error-handling-in-api-management"></a>Fel hantering i API Management

Principer i Azure API Management är indelade i `inbound` ,, `backend` `outbound` och `on-error` avsnitt som visas i följande exempel.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

Under bearbetningen av en begäran utförs inbyggda steg tillsammans med alla principer som omfattas av begäran. Om ett fel inträffar hoppar bearbetningen omedelbart till `on-error` princip avsnittet.
`on-error`Princip avsnittet kan användas i valfri omfattning. API-utgivare kan konfigurera anpassade beteenden, t. ex. loggning av fel till händelse nav eller att skapa ett nytt svar som ska returneras till anroparen.

> [!NOTE]
> `on-error`Avsnittet finns inte i principer som standard. Om du vill lägga till `on-error` avsnittet i en princip bläddrar du till önskad princip i princip redigeraren och lägger till den. Mer information om hur du konfigurerar principer finns [i principer i API Management](./api-management-howto-policies.md).
>
> Om det inte finns något `on-error` avsnitt får anroparna 400 eller 500 HTTP-svarsmeddelanden om ett fel inträffar.

### <a name="policies-allowed-in-on-error"></a>Principer som tillåts vid vid fel

Följande principer kan användas i `on-error` avsnittet princip.

-   [använder](api-management-advanced-policies.md#choose)
-   [Set-Variable](api-management-advanced-policies.md#set-variable)
-   [Sök och ersätt](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [returnera svar](api-management-advanced-policies.md#ReturnResponse)
-   [Ange rubrik](api-management-transformation-policies.md#SetHTTPheader)
-   [set-metod](api-management-advanced-policies.md#SetRequestMethod)
-   [Ange status](api-management-advanced-policies.md#SetStatus)
-   [skicka begäran](api-management-advanced-policies.md#SendRequest)
-   [Skicka – envägs-begäran](api-management-advanced-policies.md#SendOneWayRequest)
-   [Logga till eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [JSON-till-XML](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [XML-till-JSON](api-management-transformation-policies.md#ConvertXMLtoJSON)
-   [gräns-concurrency](api-management-advanced-policies.md#LimitConcurrency)
-   [modeller – svar](api-management-advanced-policies.md#mock-response)
-   [försök igen](api-management-advanced-policies.md#Retry)
-   [Rita](api-management-advanced-policies.md#Trace)

## <a name="lasterror"></a>LastError

När ett fel uppstår och styr hopp till `on-error` princip avsnittet lagras felet i [kontexten. LastError](api-management-policy-expressions.md#ContextVariables) -egenskap, som kan nås av principer i `on-error` avsnittet. LastError har följande egenskaper.

| Namn       | Typ   | Beskrivning                                                                                               | Krävs |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | sträng | Namnger elementet där felet inträffade. Kan vara antingen en princip eller ett inbyggt steg namn för pipelinen.      | Ja      |
| `Reason`   | sträng | Maskin vänlig felkod som kan användas vid fel hantering.                                       | Inga       |
| `Message`  | sträng | Fel Beskrivning av människo läsbarhet.                                                                         | Ja      |
| `Scope`    | sträng | Namnet på det omfång där felet inträffade och kan vara en av "global", "Product", "API" eller "operation" | Inga       |
| `Section`  | sträng | Avsnitts namn där fel uppstod. Möjliga värden: "inkommande", "backend", "utgående" eller "på-fel".      | Inga       |
| `Path`     | sträng | Anger kapslad princip, till exempel "Välj [3]/when [2]".                                                 | Inga       |
| `PolicyId` | sträng | Värdet för `id` attributet, om det anges av kunden, på principen där felet uppstod             | Inga       |

> [!TIP]
> Du kan komma åt status koden via kontexten. Response. StatusCode.

> [!NOTE]
> Alla principer har ett valfritt `id` attribut som kan läggas till i principens rot element. Om det här attributet finns i en princip när ett fel inträffar kan värdet för attributet hämtas med hjälp av `context.LastError.PolicyId` egenskapen.

## <a name="predefined-errors-for-built-in-steps"></a>Fördefinierade fel för inbyggda steg

Följande fel är fördefinierade för fel villkor som kan uppstå under utvärderingen av de inbyggda bearbetnings stegen.

| Källa        | Villkor                                 | Anledning                  | Meddelande                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfiguration | URI: n matchar inte någon API eller åtgärd | OperationNotFound       | Det gick inte att matcha inkommande begäran till en åtgärd.                                                                      |
| auktorisering | Ingen prenumerations nyckel har angetts             | SubscriptionKeyNotFound | Åtkomst nekad på grund av saknad prenumerations nyckel. Se till att inkludera prenumerations nyckel när du gör förfrågningar till detta API. |
| auktorisering | Värdet för prenumerations nyckeln är ogiltigt         | SubscriptionKeyInvalid  | Åtkomst nekad på grund av ogiltig prenumerations nyckel. Se till att ange en giltig nyckel för en aktiv prenumeration.            |
| multipel | Överordnad anslutning (från en klient till en API Management Gateway) avbröts av klienten medan begäran väntades | ClientConnectionFailure | multipel |
| multipel | Överordnad anslutning (från en API Management-Gateway till en backend-tjänst) har inte upprättats eller avbröts av Server delen | BackendConnectionFailure | multipel |
| multipel | Det uppstod ett körnings undantag vid utvärderingen av ett visst uttryck | ExpressionValueEvaluationFailure | multipel |

## <a name="predefined-errors-for-policies"></a>Fördefinierade fel för principer

Följande fel är fördefinierade för fel villkor som kan uppstå under utvärdering av principer.

| Källa       | Villkor                                                       | Anledning                    | Meddelande                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| hastighets begränsning   | Hastighets gränsen har överskridits                                             | RateLimitExceeded         | Hastighets gränsen har överskridits                                                                                                               |
| kvot        | Kvoten överskreds                                                  | QuotaExceeded             | Slut på kvot för samtalsvolym. Kvoten kommer att fyllas i XX: XX: xx. -eller-slut på bandbredds kvot. Kvoten kommer att fyllas i XX: XX: xx. |
| JSONP        | Parametervärdet för återanrop är ogiltigt (innehåller fel tecken) | CallbackParameterInvalid  | Värdet för callback-parametern {callback-parameter-Name} är inte en giltig JavaScript-identifierare.                                          |
| IP-filter    | Det gick inte att parsa anropar-IP från begäran                          | FailedToParseCallerIP     | Det gick inte att upprätta en IP-adress för anroparen. Åtkomst nekad.                                                                        |
| IP-filter    | Uppringaren IP är inte i listan över tillåtna                                | CallerIpNotAllowed        | Uppringaren IP-adress {IP-Address} är inte tillåten. Åtkomst nekad.                                                                        |
| IP-filter    | Uppringaren IP är i listan över blockerade                                    | CallerIpBlocked           | Anrops-IP-adressen är blockerad. Åtkomst nekad.                                                                                         |
| Check-sidhuvud | Obligatorisk rubrik saknas eller så saknas värdet               | HeaderNotFound            | Det gick inte att hitta huvudet {header-Name} i begäran. Åtkomst nekad.                                                                    |
| Check-sidhuvud | Obligatorisk rubrik saknas eller så saknas värdet               | HeaderValueNotAllowed     | Huvudet {header-name} värdet {header-Value} är inte tillåtet. Åtkomst nekad.                                                          |
| verifiera-JWT | JWT-token saknas i begäran                                 | TokenNotFound             | JWT hittades inte i begäran. Åtkomst nekad.                                                                                         |
| verifiera-JWT | Det gick inte att verifiera signaturen                                     | TokenSignatureInvalid     | <meddelande från JWT-biblioteket \> . Åtkomst nekad.                                                                                          |
| verifiera-JWT | Ogiltig mål grupp                                                | TokenAudienceNotAllowed   | <meddelande från JWT-biblioteket \> . Åtkomst nekad.                                                                                          |
| verifiera-JWT | Ogiltig utfärdare                                                  | TokenIssuerNotAllowed     | <meddelande från JWT-biblioteket \> . Åtkomst nekad.                                                                                          |
| verifiera-JWT | Token har upphört att gälla                                                   | TokenExpired              | <meddelande från JWT-biblioteket \> . Åtkomst nekad.                                                                                          |
| verifiera-JWT | Signaturnyckel matchades inte av ID                            | TokenSignatureKeyNotFound | <meddelande från JWT-biblioteket \> . Åtkomst nekad.                                                                                          |
| verifiera-JWT | Obligatoriska anspråk saknas från token                          | TokenClaimNotFound        | JWT-token saknar följande anspråk: <C1 \> , <C2 \> ,... Åtkomst nekad.                                                            |
| verifiera-JWT | Felaktig matchning av anspråks värden                                           | TokenClaimValueNotAllowed | Anspråk {Claim-name} värdet {Claim-Value} är inte tillåtet. Åtkomst nekad.                                                             |
| verifiera-JWT | Andra verifierings problem                                       | JwtInvalid                | <meddelande från JWT-biblioteket\>                                                                                                          |
| vidarebefordra-begäran eller skicka begäran | HTTP-svarets status kod och huvuden togs inte emot från Server delen inom den angivna tids gränsen | Tidsgräns | multipel |

## <a name="example"></a>Exempel

Ange en API-princip till:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

och sändning av en otillåten begäran kommer att resultera i följande svar:

![Obehörigt fel svar](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

-   [Principer i API Management](api-management-howto-policies.md)
-   [Transformera API: er](transform-api.md)
-   [Princip referens](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar
-   [Principexempel](./policy-reference.md)