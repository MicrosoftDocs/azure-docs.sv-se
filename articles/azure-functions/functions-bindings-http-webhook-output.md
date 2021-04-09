---
title: Azure Functions HTTP-utgående bindningar
description: Lär dig hur du returnerar HTTP-svar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 6caf3847971c2dd39f1eea0a921891e4440373a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727475"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions HTTP-utgående bindningar

Använd bindningen för HTTP-utdata för att svara på avsändaren av HTTP-begäran. Den här bindningen kräver en HTTP-utlösare och du kan anpassa svaret som associeras med utlösarens begäran.

Standard retur värdet för en HTTP-utlöst funktion är:

- `HTTP 204 No Content` med en tom brödtext i funktionerna 2. x och högre
- `HTTP 200 OK` med en tom brödtext i funktionerna 1. x

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.jsi* filen. För klass bibliotek i C# finns det inga egenskaper för attribut som motsvarar dessa *function.jsi* egenskaper.

|Egenskap  |Beskrivning  |
|---------|---------|
| **bastyp** |Måste anges till `http` . |
| **position** | Måste anges till `out` . |
| **Namn** | Variabel namnet som används i funktions koden för svaret, eller `$return` för att använda det returnerade värdet. |

## <a name="usage"></a>Användning

Om du vill skicka ett HTTP-svar använder du svars mönster för språk standard. I C#-eller C#-skriptet ska du göra funktions retur typen `IActionResult` eller `Task<IActionResult>` . I C# krävs inget attribut för retur värde.

Till exempel svar, se [utlösaren exempel](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>host.jspå Inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet host.jspå filen nedan innehåller bara version 2. x +-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i versionerna 2. x finns i [host.jsreferens för Azure Functions](functions-host-json.md).

> [!NOTE]
> En referens för host.jspå i functions 1. x finns i [host.jsreferens för Azure Functions 1. x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
| customHeaders|inget|Gör att du kan ange anpassade rubriker i HTTP-svaret. I föregående exempel läggs `X-Content-Type-Options` rubriken till i svaret för att undvika avlyssning av innehålls typ. |
|dynamicThrottlesEnabled|värdet<sup>\*</sup>|När den här inställningen är aktive rad kommer pipeline för bearbetning av begär Anden att regelbundet kontrol lera system prestanda räknare `connections/threads/processes/memory/cpu/etc` , som och om någon av dessa räknare är över en inbyggd hög tröskel (80%), avvisas begär Anden med ett `429 "Too Busy"` svar tills räknarna återgår till normala nivåer.<br/><sup>\*</sup>Standardvärdet i en förbruknings plan är `true` . Standardvärdet i en dedikerad plan är `false` .|
|HSTS|inte aktiverat|När `isEnabled` är inställt på `true` , tillämpas [http Strict Transport Security (HSTS)-beteendet för .net Core](/aspnet/core/security/enforcing-ssl?tabs=visual-studio#hsts) enligt definitionen i [ `HstsOptions` klassen](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions). I exemplet ovan anges också [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) egenskapen till 10 dagar. Följande egenskaper stöds `hsts` : <table><tr><th>Egenskap</th><th>Beskrivning</th></tr><tr><td>excludedHosts</td><td>En sträng mat ris med värd namn som HSTS-huvudet inte har lagts till för.</td></tr><tr><td>includeSubDomains</td><td>Booleskt värde som anger om parametern includeSubDomain för rubriken Strict-Transport-Security är aktive rad.</td></tr><tr><td>maxAge</td><td>Sträng som definierar Max-ålders parametern för huvudet Strict-Transport-Security.</td></tr><tr><td>preload</td><td>Booleskt värde som anger om preload-parametern för ett strikt-Transport-Security-huvud är aktive rad.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Maximalt antal HTTP-funktioner som körs parallellt. Med det här värdet kan du kontrol lera samtidighet, vilket kan hjälpa dig att hantera resursutnyttjande. Du kan till exempel ha en HTTP-funktion som använder ett stort antal system resurser (minne/processor/Sockets) så att den orsakar problem när samtidigheten är för hög. Eller så kanske du har en funktion som gör utgående begär anden till en tjänst från tredje part, och dessa anrop måste vara begränsade. I dessa fall kan det hjälpa att tillämpa en begränsning. <br/><sup>*</sup>Standardvärdet för en förbruknings plan är 100. Standardvärdet för en dedikerad plan är obundet ( `-1` ).|
|maxOutstandingRequests|200<sup>\*</sup>|Det maximala antalet väntande begär Anden som innehas vid en specifik tidpunkt. Den här gränsen omfattar begär Anden som har placerats i kö, men som inte har börjat köras, samt alla pågående körningar. Inkommande begär anden över den här gränsen avvisas med ett svar på 429 "för upptagen". Det gör det möjligt för anropare att använda tidsbaserade återförsöks strategier och hjälper dig också att kontrol lera maximal fördröjning för begäran. Detta styr endast köer som inträffar inom skript värdens körnings Sök väg. Andra köer, t. ex. ASP.NET, kommer fortfarande att gälla och påverkas inte av den här inställningen. <br/><sup>\*</sup>Standardvärdet för en förbruknings plan är 200. Standardvärdet för en dedikerad plan är obundet ( `-1` ).|
|routePrefix|api|Det väg-prefix som gäller för alla vägar. Använd en tom sträng för att ta bort standardprefixet. |

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion från en HTTP-begäran](./functions-bindings-http-webhook-trigger.md)