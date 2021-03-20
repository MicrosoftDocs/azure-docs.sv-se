---
title: 'Övervaka API: er med Azure API Management, Event Hubs och Moesif'
titleSuffix: Azure API Management
description: Exempel program som demonstrerar logg-till-eventhub-principen genom att ansluta Azure API Management, Azure Event Hubs och Moesif för HTTP-loggning och övervakning
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: abb9cbb73f8957cec2cb3240bbf186623b9b2ef9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88205503"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Övervaka dina API: er med Azure API Management, Event Hubs och Moesif
[Tjänsten API Management](api-management-key-concepts.md) tillhandahåller många funktioner för att förbättra bearbetningen av HTTP-begäranden som skickas till http-API: et. Förekomsten av begär Anden och svar är dock tillfällig. Begäran görs och den flödar via API Management tjänsten till Server dels-API: et. Ditt API bearbetar begäran och ett svar flödar tillbaka till API-konsumenten. I API Managements tjänsten finns viktig statistik om API: erna för visning i Azure Portals instrument panel, men utöver detta är informationen borta.

Genom att använda logg-till-eventhub-principen i API Managements tjänsten kan du skicka all information från begäran och svaret till en [Azure Event Hub](../event-hubs/event-hubs-about.md). Det finns en mängd olika orsaker till varför du kanske vill skapa händelser från HTTP-meddelanden som skickas till dina API: er. Några exempel är gransknings historik för uppdateringar, användnings analys, undantags varningar och integreringar från tredje part.

Den här artikeln visar hur du fångar hela HTTP-begäran och svarsmeddelanden, skickar det till en Händelsehubben och sedan vidarebefordrar det meddelandet till en tjänst från tredje part som tillhandahåller HTTP-loggning och övervaknings tjänster.

## <a name="why-send-from-api-management-service"></a>Varför skicka från API Management-tjänsten?
Det går att skriva HTTP-mellanprogram som kan ansluta till HTTP API-ramverk för att fånga HTTP-förfrågningar och svar och mata in dem i loggnings-och övervaknings system. Nack delen med den här metoden är att HTTP-mellanprogram måste integreras i Server dels-API: et och måste matcha API: ns plattform. Om det finns flera API: er måste var och en distribuera mellanprogram. Det finns ofta orsaker till varför Server dels-API: er inte kan uppdateras.

Att använda Azure API Management-tjänsten för att integrera med loggnings infrastruktur tillhandahåller en centraliserad och plattforms oberoende lösning. Den är också skalbar, delvis på grund av funktioner för [geo-replikering](api-management-howto-deploy-multi-region.md) i Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Varför skicka till en Azure Event Hub?
Det är rimligt att fråga, varför skapa en princip som är speciell för Azure Event Hubs? Det finns många olika platser där jag kanske vill logga mina förfrågningar. Varför inte bara skicka begär Anden direkt till slut destinationen?  Det är ett alternativ. När du gör loggnings begär Anden från en API Management-tjänst är det dock nödvändigt att fundera över hur loggnings meddelanden påverkar prestandan för API: et. Gradvis ökning i belastningen kan hanteras genom att öka tillgängliga instanser av system komponenter eller genom att dra nytta av geo-replikering. Kort toppar i trafik kan dock orsaka att begär Anden fördröjs om förfrågningar till loggning av infrastruktur börjar bli långsam under belastningen.

Azure-Event Hubs är utformad för att ingressa enorma data volymer, med kapacitet för att hantera ett mycket högre antal händelser än antalet HTTP-begäranden för de flesta API-processer. Händelsehubben fungerar som en typ av sofistikerad buffert mellan API Management-tjänsten och infrastrukturen som lagrar och bearbetar meddelanden. Detta säkerställer att API-prestandan inte påverkas av loggnings infrastrukturen.

När data har skickats till en Händelsehubben är den beständig och väntar på att Event Hub-användare ska bearbeta den. Event Hub bryr sig inte om hur det bearbetas, det är bara värnar om att se till att meddelandet har levererats.

Event Hubs kan strömma händelser till flera konsument grupper. Detta gör att händelser kan bearbetas av olika system. Detta möjliggör stöd för många integrerings scenarier utan att lägga till extra fördröjningar vid bearbetningen av API-begäran i API Management tjänsten eftersom endast en händelse behöver genereras.

## <a name="a-policy-to-send-applicationhttp-messages"></a>En princip för att skicka program/http-meddelanden
En Event Hub accepterar händelse data som en enkel sträng. Innehållet i den strängen är upp till dig. För att kunna packa upp en HTTP-begäran och skicka den till Event Hubs, måste vi formatera strängen med förfrågnings-eller svars informationen. I situationer som detta, om det finns ett befintligt format som vi kan återanvända, kanske vi inte behöver skriva vår egen tolknings kod. Från början jag övervägde [att använda har för att](http://www.softwareishard.com/blog/har-12-spec/) skicka HTTP-förfrågningar och-svar. Detta format är dock optimerat för att lagra en sekvens med HTTP-begäranden i ett JSON-baserat format. Det innehåller ett antal obligatoriska element som lade till onödig komplexitet för scenariot att skicka HTTP-meddelandet över kabeln.

Ett alternativt alternativ var att använda `application/http` medie typen enligt beskrivningen i HTTP-specifikationen [RFC 7230](https://tools.ietf.org/html/rfc7230). Den här medie typen använder exakt samma format som används för att skicka HTTP-meddelanden via kabeln, men hela meddelandet kan placeras i bröd texten i en annan HTTP-begäran. I vårt fall kommer vi bara att använda bröd texten som vårt meddelande att skicka till Event Hubs. Bekvämt finns det en tolk som finns i klient biblioteken [Microsoft ASP.net Web API 2,2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) som kan tolka det här formatet och konvertera det till det `HttpRequestMessage` ursprungliga `HttpResponseMessage` objektet och objekten.

För att kunna skapa det här meddelandet måste vi dra nytta av C#-baserade [princip uttryck](./api-management-policy-expressions.md) i Azure API Management. Här är principen, som skickar ett meddelande om HTTP-begäran till Azure Event Hubs.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Princip deklaration
Det finns några saker som är värda att nämna detta policy uttryck. Logg-till-eventhub-principen har ett attribut med namnet logga-ID som refererar till namnet på den loggning som har skapats i API Managements tjänsten. Information om hur du konfigurerar en Event Hub-logg i API Management-tjänsten finns i dokumentet [så här loggar du händelser till azure Event Hubs i azure API Management](api-management-howto-log-event-hubs.md). Det andra attributet är en valfri parameter som instruerar Event Hubs vilken partition som ska lagra meddelandet i. Event Hubs använder partitioner för att aktivera skalbarhet och kräva minst två. Den beställda leveransen av meddelanden garanteras endast inom en partition. Om vi inte instruerar Händelsehubben i vilken partition som ska placera meddelandet, används en algoritm för resursallokering för att distribuera belastningen. Men det kan orsaka att några av våra meddelanden bearbetas i fel ordning.

### <a name="partitions"></a>Partitioner
För att säkerställa att våra meddelanden levereras till konsumenter i rätt ordning och dra nytta av belastnings fördelnings funktionen för partitioner väljer jag att skicka HTTP-begäranden till en partition och HTTP-svarsmeddelanden till en andra partition. Detta garanterar en jämn belastnings fördelning och vi kan garantera att alla begär Anden förbrukas i ordning och att alla svar används i ordning. Det är möjligt att ett svar förbrukas före motsvarande begäran, men eftersom vi inte är ett problem eftersom vi har en annan mekanism för att korrelera begär anden till svar och vi vet att förfrågningar alltid kommer före svar.

### <a name="http-payloads"></a>HTTP-nyttolaster
När du har skapat den `requestLine` kontrollerar vi om texten i begäran ska trunkeras. Begär ande texten trunkeras till endast 1024. Detta kan ökas, men enskilda Event Hub-meddelanden är begränsade till 256 KB, så det är troligt att vissa HTTP-meddelande kroppar inte får plats i ett enda meddelande. När du loggar och analyserar en stor mängd information kan härledas från bara HTTP-begärans rad och sidhuvud. Dessutom returnerar många API: er bara små organ, så data förlust genom att trunkera stora kroppar är ganska minimal i jämförelse med minskningen av överförings-, bearbetnings-och lagrings kostnader för att hålla all brödtext. En slutgiltig kommentar om att bearbeta texten är att vi måste skicka `true` till `As<string>()` metoden eftersom vi läser innehållet i den, men ville också att Server dels-API: et skulle kunna läsa bröd texten. Genom att skicka sant till den här metoden gör vi att bröd texten buffras så att den kan läsas en andra gång. Detta är viktigt att vara medveten om om du har ett API som laddar upp stora filer eller använder lång avsökning. I dessa fall är det bäst att undvika att läsa bröd texten.

### <a name="http-headers"></a>HTTP-rubriker
HTTP-huvuden kan överföras till meddelande formatet i ett enkelt format för nyckel/värde-par. Vi har valt att ta bort vissa säkerhets känsliga fält för att undvika att onödigt avslöja autentiseringsinformation. Det är inte troligt att API-nycklar och andra autentiseringsuppgifter används i analys syfte. Om vi vill göra analyser för användaren och den specifika produkt som de använder, kan vi hämta det från `context` objektet och lägga till det i meddelandet.

### <a name="message-metadata"></a>Metadata för meddelande
När du skapar hela meddelandet som ska skickas till händelsehubben är den första raden inte en del av `application/http` meddelandet. Den första raden är ytterligare metadata som består av om meddelandet är ett begär ande-eller svarsmeddelande och ett meddelande-ID som används för att korrelera begär anden till svar. Meddelande-ID: t skapas med hjälp av en annan princip som ser ut så här:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Vi kunde ha skapat begär ande meddelandet, som lagrats i en variabel tills svaret returnerades och sedan skickade begäran och svaret som ett enda meddelande. Men genom att skicka begäran och svaret oberoende av varandra och med ett meddelande-ID för att korrelera de två, får vi en mer flexibel storlek på meddelande storleken, möjligheten att dra nytta av flera partitioner samtidigt som meddelande ordningen upprätthålls och begäran visas i vår loggnings instrument panel tidigare. Det kan också finnas några scenarier där ett giltigt svar aldrig skickas till händelsehubben, eventuellt på grund av ett allvarligt fel i tjänsten API Management, men vi har fortfarande en post i begäran.

Principen för att skicka svars-HTTP-meddelandet ser ut ungefär som begäran och den fullständiga princip konfigurationen ser ut så här:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

`set-variable`Principen skapar ett värde som är tillgängligt för både `log-to-eventhub` principen i `<inbound>` avsnittet och i `<outbound>` avsnittet.

## <a name="receiving-events-from-event-hubs"></a>Ta emot händelser från Event Hubs
Händelser från Azure Event Hub tas emot med [AMQP-protokollet](https://www.amqp.org/). Microsoft Service Bus-teamet har gjort klient bibliotek tillgängliga för att göra det enklare att använda händelser. Det finns två olika metoder som stöds, en är en *direkt konsument* och den andra använder- `EventProcessorHost` klassen. Exempel på dessa två metoder finns i [Event Hubs programmerings guide](../event-hubs/event-hubs-programming-guide.md). Den korta versionen av skillnaderna är, `Direct Consumer` ger dig fullständig kontroll och gör `EventProcessorHost` en del av arbetet för dig, men gör vissa antaganden om hur du behandlar dessa händelser.

### <a name="eventprocessorhost"></a>EventProcessorHost
I det här exemplet använder vi `EventProcessorHost` för enkelhetens skull, men det kanske inte är det bästa valet för just det här scenariot. `EventProcessorHost` gör det svårt att se till att du inte behöver oroa dig för problem med trådar inom en viss händelse processor klass. I vårt scenario konverterar vi dock bara meddelandet till ett annat format och skickar det till en annan tjänst med hjälp av en asynkron metod. Det finns inget behov av att uppdatera delat tillstånd och därför är risken för problem med trådar. I de flesta fall `EventProcessorHost` är det förmodligen det bästa valet och det är verkligen det enklaste alternativet.

### <a name="ieventprocessor"></a>IEventProcessor
Det centrala konceptet när `EventProcessorHost` du använder är att skapa en implementering av `IEventProcessor` gränssnittet, som innehåller metoden `ProcessEventAsync` . Grunden för den här metoden visas här:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

En lista över EventData-objekt överförs till-metoden och vi itererar över listan. Byte för varje metod parsas till ett HttpMessage-objekt och objektet skickas till en instans av IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
`HttpMessage`Instansen innehåller tre data typer:

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

`HttpMessage`Instansen innehåller ett `MessageId` GUID som gör att vi kan ansluta http-begäran till motsvarande http-svar och ett booleskt värde som identifierar om objektet innehåller en instans av en HttpRequestMessage och HttpResponseMessage. Genom att använda de inbyggda HTTP-klasserna från `System.Net.Http` kunde jag dra nytta av den `application/http` tolknings kod som ingår i `System.Net.Http.Formatting` .  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
`HttpMessage`Instansen vidarebefordras sedan till implementering av `IHttpMessageProcessor` , vilket är ett gränssnitt som jag har skapat för att ta del av mottagning och tolkning av händelsen från Azure Event Hub och den faktiska bearbetningen av det.

## <a name="forwarding-the-http-message"></a>Vidarebefordrar HTTP-meddelandet
I det här exemplet bestämde jag att det var intressant att skicka HTTP-begäran till [MOESIF API Analytics](https://www.moesif.com). Moesif är en molnbaserad tjänst som specialiserar sig på HTTP-analys och fel sökning. De har en kostnads fri nivå, så det är enkelt att testa och det gör att vi kan se HTTP-begärandena i real tid genom vår API Management-tjänst.

`IHttpMessageProcessor`Implementeringen ser ut så här,

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

`MoesifHttpMessageProcessor`Drar nytta av ett [C# API-bibliotek för Moesif](https://www.moesif.com/docs/api?csharp#events) som gör det enkelt att skicka http-Datadata till deras tjänster. Du behöver ett konto och ett program-ID för att kunna skicka HTTP-data till Moesif Collector-API: et. Du får ett Moesif program-ID genom att skapa ett konto på [Moesif-webbplatsen](https://www.moesif.com) och gå sedan till den _översta högra menyn_ för att  ->  _ställa in appar_.

## <a name="complete-sample"></a>Fullständigt exempel
[Käll koden](https://github.com/dgilling/ApimEventProcessor) och testerna för exemplet finns på GitHub. Du behöver en [API Management tjänst](get-started-create-service-instance.md), [en ansluten Händelsehubben](api-management-howto-log-event-hubs.md)och ett [lagrings konto](../storage/common/storage-account-create.md) för att köra exemplet själv.   

Exemplet är bara ett enkelt konsol program som lyssnar efter händelser som kommer från Händelsehubben, konverterar dem till en Moesif `EventRequestModel` och `EventResponseModel` objekt och vidarebefordrar dem sedan till Moesif Collector-API: et.

I följande animerade bild kan du se en begäran till ett API i Developer-portalen, konsol programmet som visar meddelandet som tas emot, bearbetas och vidarebefordras och sedan begäran och svaret visas i händelse strömmen.

![Demonstration av begäran som vidarebefordras till Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Sammanfattning
Azure API Management-tjänsten är en idealisk plats där du kan samla in HTTP-trafik som reser till och från dina API: er. Azure Event Hubs är en mycket skalbar och låg kostnads lösning för att fånga den trafiken och mata in den i sekundära bearbetnings system för loggning, övervakning och annan avancerad analys. Att ansluta till trafik övervaknings system från tredje part som Moesif är lika enkelt som några dussin rader kod.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Event Hubs
  * [Kom igång med Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ta emot meddelanden med EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Programmerings guide för Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Läs mer om API Management och Event Hubs-integrering
  * [Logga händelser till Azure Event Hubs i Azure API Management](api-management-howto-log-event-hubs.md)
  * [Referens för entiteten loggning](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [logg-till-eventhub-princip referens](./api-management-advanced-policies.md#log-to-eventhub)
