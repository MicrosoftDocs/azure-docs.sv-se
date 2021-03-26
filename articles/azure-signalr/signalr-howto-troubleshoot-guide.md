---
title: Felsökningsguide för Azure SignalR Service
description: Lär dig hur du felsöker vanliga problem
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: e26def56fbd03626c3efc660db57012ee1b767ea
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048212"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Fel söknings guide för Azure SignalR service vanliga problem

Den här vägledningen är att tillhandahålla en användbar fel söknings guide som baseras på vanliga problem som kunder uppfyllt och löst under de senaste åren.

## <a name="access-token-too-long"></a>Åtkomsttoken är för lång

### <a name="possible-errors"></a>Möjliga fel

* Klient Sidan `ERR_CONNECTION_`
* 414 URI är för lång
* 413-nyttolasten är för stor
* Åtkomsttoken får inte vara längre än 4K. 413 begär ande enheten är för stor

### <a name="root-cause"></a>Rotorsak

För HTTP/2 är max längden **4 K**, så om du använder webbläsare för att få åtkomst till Azure-tjänsten, kommer det att finnas ett fel `ERR_CONNECTION_` för den här begränsningen.

För HTTP/1.1-eller C#-klienter är den maximala URI-längden **12 k**, den maximala sidhuvuds längden är **16 KB**.

Med SDK-version **1.0.6** eller högre `/negotiate` utlöses `413 Payload Too Large` när den genererade åtkomsttoken är större än **4 KB**.

### <a name="solution"></a>Lösning

Som standard ingår anspråk från `context.User.Claims` när du genererar JWT-åtkomsttoken till **ASRS**(**en** zure **S** ignal **R** **s** servicenivåmå), så att anspråken bevaras och kan skickas från **ASRS** till `Hub` när klienten ansluter till `Hub` .

I vissa fall `context.User.Claims` används för att lagra massor av information för App Server, som de flesta inte används av `Hub` s utan av andra komponenter.

Den genererade åtkomsttoken skickas via nätverket, och för WebSocket/SSE-anslutningar skickas åtkomsttoken via frågesträngar. Vi rekommenderar att vi bara skickar **nödvändiga** anspråk från klienten via **ASRS** till din app server när hubben behöver det.

`ClaimsProvider`Du kan anpassa anspråk som skickas till **ASRS** i åtkomsttoken.

För ASP.NET Core:

```csharp
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

För ASP.NET:

```csharp
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tls-12-required"></a>TLS 1,2 krävs

### <a name="possible-errors"></a>Möjliga fel

* ASP.NET "det finns inga tillgängliga servrar"-fel [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "anslutningen är inte aktiv, det går inte att skicka data till tjänsten." fel [#324](https://github.com/Azure/azure-signalr/issues/324)
* "Ett fel uppstod när HTTP-begäran skulle utföras till https:// <API endpoint> . Det här felet kan bero på att Server certifikatet inte har kon figurer ATS korrekt med HTTP.SYS i HTTPS-fallet. Det här felet kan också orsakas av en matchning av säkerhets bindningen mellan klienten och servern. "

### <a name="root-cause"></a>Rotorsak

Azure-tjänsten stöder bara TLS 1.2 för säkerhets frågor. Med .NET Framework är det möjligt att TLS 1.2 inte är standard protokollet. Det innebär att Server anslutningarna till ASRS inte kan upprättas.

### <a name="troubleshooting-guide"></a>Felsökningsguide

1. Om det här felet kan återskapas lokalt avmarkerar du *just My Code* och returnerar alla CLR-undantag och felsöker app server lokalt för att se vilket undantag som uppstår.
    * Avmarkera *just My Code*

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Avmarkera Just My Code":::

    * Utlös CLR-undantag

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="Utlös CLR-undantag":::

    * Se undantagen som utlöses när du felsöker appens kod på Server sidan:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="Undantags Throw":::

2. För ASP.NET kan du också lägga till följande kod i `Startup.cs` för att aktivera detaljerad spårning och se felen från loggen.

    ```cs
    app.MapAzureSignalR(this.GetType().FullName);
    // Make sure this switch is called after MapAzureSignalR
    GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
    ```

### <a name="solution"></a>Lösning

Lägg till följande kod i Start programmet:

```csharp
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="400-bad-request-returned-for-client-requests"></a>400 Felaktig begäran returnerades för klient begär Anden

### <a name="root-cause"></a>Rotorsak

Kontrol lera om din klientbegäran har flera `hub` frågesträngar. `hub` är en konserverad frågeparameter och 400 kommer att utlösa om tjänsten identifierar fler än en `hub` i frågan.

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="401-unauthorized-returned-for-client-requests"></a>401 Behörighet saknas returneras för klientbegäran

### <a name="root-cause"></a>Rotorsak

För närvarande är standardvärdet JWT-tokens livs längd 1 timme.

När du använder WebSocket transport-typ är det OK för ASP.NET Core SignalR.

För ASP.NET Core signalers andra transport typ, SSE och lång avsökning innebär detta som standard att anslutningen kan ha flest kvar i 1 timme.

För ASP.NET-signaler skickar klienten en keepalive- `/ping` begäran till tjänsten, och när den `/ping` Miss lyckas **avbryter** klienten anslutningen och återansluter aldrig. Det innebär att standardvärdet för token för ASP.NET-signaler är att anslutningen varar **högst** 1 timme för alla transport typer.

### <a name="solution"></a>Lösning

För säkerhets problem rekommenderas inte utöka TTL. Vi rekommenderar att du lägger till åter anslutnings logik från klienten för att starta om anslutningen när sådan 401 inträffar. När klienten startar om anslutningen kommer den att förhandlas med app server för att hämta JWT-token igen och få en förnyad token.

Sök [här](#restart_connection) för att starta om klient anslutningar.

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-client-requests"></a>404 returneras för klientbegäran

För en SignalR beständig anslutning är den först `/negotiate` Azure SignalR-tjänsten och upprättar sedan den riktiga anslutningen till Azure SignalR-tjänsten.

### <a name="troubleshooting-guide"></a>Felsökningsguide

* Så här [visar du utgående begär Anden](#view_request) för att hämta begäran från klienten till tjänsten.
* Kontrol lera URL: en för begäran när 404 inträffar. Om webb adressen är riktad mot din webbapp, och liknar `{your_web_app}/hubs/{hubName}` , kontrollerar du om klienten `SkipNegotiation` är `true` . När du använder Azure-SignalR tar klienten emot en omdirigerings-URL när den först förhandlas med app-servern. Klienten bör **inte** hoppa över förhandling när Azure-signalerare används.
* En annan 404 kan inträffa när anslutningsbegäran hanteras mer än **5** sekunder efter att det `/negotiate` har anropats. Kontrol lera tidsstämpeln för klientbegäran och öppna ett problem till oss om begäran till tjänsten har ett långsamt svar.

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>404 returnerade begäran om åter anslutning av ASP.NET SignalR

När [klient anslutningen](#client_connection_drop)går ur ASP.net SignalR, återansluter den igen med samma `connectionId` tre gånger innan anslutningen stoppas. `/reconnect` kan hjälpa dig om anslutningen bryts på grund av tillfälliga nätverks problem som `/reconnect` kan återupprätta den beständiga anslutningen. Under andra omständigheter släpps klient anslutningen till exempel på grund av att den omdirigerade Server anslutningen har släppts, eller så har signal tjänsten några interna fel som instansen för omstart/redundansväxling/distribution, anslutningen inte längre finns, så `/reconnect` returneras `404` . Det är det förväntade beteendet för `/reconnect` och efter tre gånger försöker anslutningen stoppas. Vi rekommenderar att du har en logik för att [starta om anslutningen](#restart_connection) när anslutningen stoppas.

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (för många begär Anden) returnerades för klient begär Anden

Det finns två fall.

### <a name="concurrent-connection-count-exceeds-limit"></a>Antalet **samtidiga** anslutningar överskrider gränsen

För **lediga** instanser är gränsen för **samtidiga** anslutningar 20 för **standard** instanser, gränsen för antal **samtidiga** anslutningar **per enhet** är 1 K, vilket innebär att Unit100 tillåter 100 samtidiga anslutningar.

Anslutningarna omfattar både klient-och Server anslutningar. Sök [efter](./signalr-concept-messages-and-connections.md#how-connections-are-counted) hur anslutningar räknas.

### <a name="too-many-negotiate-requests-at-the-same-time"></a>För många Negotiate-begäranden på samma tid

Vi rekommenderar att du har en slumpmässig fördröjning innan du [ansluter igen.](#restart_connection) om du vill försöka igen kan du söka igen.

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500 fel vid Negotiate: Azure SignalR-tjänsten är inte ansluten ännu, försök igen senare

### <a name="root-cause"></a>Rotorsak

Det här felet rapporteras när det inte finns någon server anslutning till Azure SignalR-tjänsten ansluten.

### <a name="troubleshooting-guide"></a>Felsökningsguide

Aktivera spårning på Server sidan för att ta reda på fel informationen när servern försöker ansluta till Azure SignalR-tjänsten.

### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Aktivera loggning på Server sidan för ASP.NET Core Signalerare

Loggning på Server sidan för ASP.NET Core Signalerare integreras med den `ILogger` baserade [loggningen](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1&preserve-view=true) som finns i ASP.net Core Framework. Du kan aktivera loggning på Server sidan genom att använda `ConfigureLogging` , en exempel användning på följande sätt:

```csharp
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Loggnings kategorier för Azure SignalR börjar alltid med `Microsoft.Azure.SignalR` . Om du vill aktivera detaljerade loggar från Azure-Signaleraren konfigurerar du föregående prefix till `Debug` nivå i **appsettings.jspå** filen som nedan:

```json
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Aktivera spårning på Server sidan för ASP.NET-Signalerare

När du använder SDK-version >= `1.0.0` kan du aktivera spår genom att lägga till följande i `web.config` : ([information](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-drops"></a>Klient anslutning tappas

När klienten är ansluten till Azure-Signaleraren kan den permanenta anslutningen mellan klienten och Azure-signaler ibland släppas av olika orsaker. I det här avsnittet beskrivs flera möjligheter som gör att den här anslutningen släpps och ger viss vägledning om hur du identifierar rotor saken.

### <a name="possible-errors-seen-from-the-client-side"></a>Möjliga fel som visas från klient Sidan

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Rotorsak

Klient anslutningar kan släppas under olika omständigheter:
* Vid `Hub` Utlös ande av undantag med inkommande begäran.
* När Server anslutningen, som klienten dirigerade till, släpps, finns mer information om [Server anslutning](#server_connection_drop)i avsnittet nedan.
* När ett problem med nätverks anslutningen sker mellan klienten och SignalR-tjänsten.
* När SignalR-tjänsten har interna fel som instans omstart, redundansväxling, distribution och så vidare.

### <a name="troubleshooting-guide"></a>Felsökningsguide

1. Öppna App Server-Side log för att se om något onormalt ägt rum
2. Kontrol lera händelse loggen för App Server-sidan för att se om App Server har startats om
3. Skapa ett problem för att tillhandahålla tids ramen och skicka e-post till resurs namnet till oss

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-increases-constantly"></a>Klient anslutning ökar ständigt

Det kan bero på felaktig användning av klient anslutningen. Om någon glömmer att stoppa eller ta bort en signal klient är anslutningen fortfarande öppen.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Möjliga fel visas från signalens mått i avsnittet övervakning i Azure Portal resurs-menyn

Klient anslutningar stiger ständigt under lång tid i Azure signalers mått.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="Klient anslutningen ökar ständigt":::

### <a name="root-cause"></a>Rotorsak

SignalR klient anslutning har `DisposeAsync` aldrig anropats. anslutningen förblir öppen.

### <a name="troubleshooting-guide"></a>Felsökningsguide

Kontrol lera om signal klienten **aldrig** stängs.

### <a name="solution"></a>Lösning

Kontrol lera om du stänger anslutningen. Anropa manuellt `HubConnection.DisposeAsync()` för att stoppa anslutningen när du har använt den.

Exempel:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Vanlig felaktig användning av klient anslutning

#### <a name="azure-function-example"></a>Azure Function-exempel 

Det här problemet uppstår ofta när någon upprättar signal klient anslutning i Azure Function-metoden i stället för att göra den till en statisk medlem i din funktions klass. Du kanske bara förväntar dig att en klient anslutning har upprättats, men du ser antalet klient anslutningar ökar ständigt i mått som finns i avsnittet övervakning i Azure Portal resurs menyn. alla dessa anslutningar tas bara bort när Azure-funktionen eller Azure SignalR-tjänsten har startats om. Detta beror på att för **varje** begäran skapar Azure-funktionen **en** klient anslutning, om du inte stoppar klient anslutningen i funktions metoden, så behåller klienten anslutningarna till Azure SignalR-tjänsten.

#### <a name="solution"></a>Lösning

* Kom ihåg att stänga klient anslutning om du använder signalera-klienter i Azure Function eller använder signal klienten som singleton.
* I stället för att använda signalerar-klienter i Azure-funktionen kan du skapa Signalerare-klienter var som helst och använda [Azure Functions-bindningar för Azure SignalR-tjänsten](https://github.com/Azure/azure-functions-signalrservice-extension) för att [förhandla](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) klienten till Azure-signalerare. Du kan också använda bindningen för att [skicka meddelanden](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40). Exempel på hur du kan förhandla fram klient och skicka meddelanden finns [här](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples). Ytterligare information hittar du [här](https://github.com/Azure/azure-functions-signalrservice-extension).
* När du använder Signalerer-klienter i Azure-funktionen kan det finnas en bättre arkitektur för ditt scenario. Kontrol lera om du utformar en korrekt arkitektur utan server. Du kan referera till [Server lös program i real tid med signal tjänst bindningarna i Azure Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService).

<a name="server_connection_drop"></a>

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="server-connection-drops"></a>Server anslutning tappas

När app server startar i bakgrunden börjar Azure SDK initiera Server anslutningar till Azure-fjärrsignaler. Som beskrivs i [interna Azure SignalR-tjänsten](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)dirigerar Azure-signaler inkommande klient trafik till dessa Server anslutningar. När en server anslutning har släppts stängs alla klient anslutningar som den fungerar.

Eftersom anslutningarna mellan app server och SignalR-tjänsten är beständiga anslutningar kan de drabbas av problem med nätverks anslutningen. I Server SDK har vi **alltid återansluter** strategin till Server anslutningar. Vi rekommenderar också att användarna lägger till kontinuerlig åter anslutnings logik till klienterna med en slumpmässig fördröjnings tid för att undvika enorma samtidiga förfrågningar till servern.

Med jämna mellanrum finns det nya versions versioner för Azure SignalR-tjänsten, och ibland kan Azure-OPERATIVSYSTEMs uppdatering eller uppgraderingar eller ibland störas från våra beroende tjänster. Dessa kan leda till en kort tids krävande drifts avbrott, men om klient sidan har en metod för att koppla från/återansluta är påverkan minimal, precis som alla klient sidor orsakade från kopplings anslutning.

I det här avsnittet beskrivs flera möjligheter som leder till att Server anslutning släpps och ger viss vägledning om hur du identifierar rotor saken.

### <a name="possible-errors-seen-from-the-server-side"></a>Möjliga fel visas från Server Sidan

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Rotorsak

Server-tjänst anslutning har stängts av **ASRS**(**en** zure **s** ignal **R** **s** servicenivåmå).

För ping-tidsgräns kan det bero på hög CPU-användning eller effekter på Server sidan.

För ASP.NET-signaler har ett känt problem åtgärd ATS i SDK-1.6.0. Uppgradera SDK till den senaste versionen.

## <a name="thread-pool-starvation"></a>Effekter för tråd-pool

Om din server är svälter innebär det att inga trådar fungerar vid meddelande bearbetning. Alla trådar svarar inte i en viss metod.

Normalt orsakas det här scenariot av asynkron över synkronisering eller av `Task.Result` / `Task.Wait()` i asynkrona metoder.

Se [metod tips för ASP.net core prestanda](/aspnet/core/performance/performance-best-practices#avoid-blocking-calls).

Läs mer om [effekter för tråd-pool](https://docs.microsoft.com/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall).

### <a name="how-to-detect-thread-pool-starvation"></a>Så här identifierar du effekter för tråds bassäng

Kontrol lera antalet trådar. Gör så här om det inte finns några toppar:
* Om du använder Azure App Service kontrollerar du antalet trådar i mått. Kontrol lera `Max` agg regeringen:
    
  :::image type="content" source="media/signalr-howto-troubleshoot-guide/metrics-thread-count.png" alt-text="Skärm bild av fönstret maximalt antal trådar i Azure App Service.":::

* Om du använder .NET Framework kan du hitta [mått](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/performance-counters#lock-and-thread-performance-counters) i prestanda övervakaren på den virtuella Server-datorn.
* Om du använder .NET core i en behållare, se [samla in diagnostik i behållare](https://docs.microsoft.com/dotnet/core/diagnostics/diagnostics-in-containers).

Du kan också använda kod för att identifiera effekter för trådpoolen:

```csharp
public class ThreadPoolStarvationDetector : EventListener
{
    private const int EventIdForThreadPoolWorkerThreadAdjustmentAdjustment = 55;
    private const uint ReasonForStarvation = 6;

    private readonly ILogger<ThreadPoolStarvationDetector> _logger;

    public ThreadPoolStarvationDetector(ILogger<ThreadPoolStarvationDetector> logger)
    {
        _logger = logger;
    }

    protected override void OnEventSourceCreated(EventSource eventSource)
    {
        if (eventSource.Name == "Microsoft-Windows-DotNETRuntime")
        {
            EnableEvents(eventSource, EventLevel.Informational, EventKeywords.All);
        }
    }

    protected override void OnEventWritten(EventWrittenEventArgs eventData)
    {
        // See: https://docs.microsoft.com/en-us/dotnet/framework/performance/thread-pool-etw-events#threadpoolworkerthreadadjustmentadjustment
        if (eventData.EventId == EventIdForThreadPoolWorkerThreadAdjustmentAdjustment &&
            eventData.Payload[3] as uint? == ReasonForStarvation)
        {
            _logger.LogWarning("Thread pool starvation detected!");
        }
    }
}
```
    
Lägg till den i din tjänst:
    
```csharp
service.AddSingleton<ThreadPoolStarvationDetector>();
```

Kontrol lera sedan loggen när Server anslutningen är frånkopplad via ping-timeout.

### <a name="how-to-find-the-root-cause-of-thread-pool-starvation"></a>Så här hittar du rotor saken till effekter för tråd-pool

Så här hittar du rotor saken till effekter för trådens pool:

* Dumpa minnet och analysera sedan anrops stacken. Mer information finns i [samla in och analysera minnes dum par](https://devblogs.microsoft.com/dotnet/collecting-and-analyzing-memory-dumps/).
* Använd [clrmd](https://github.com/microsoft/clrmd) för att dumpa minnet när trådpoolen effekter identifieras. Logga sedan anrops stacken.

### <a name="troubleshooting-guide"></a>Felsökningsguide

1. Öppna App Server-Side-loggen för att se om något onormalt ägde rum.
2. Kontrol lera händelse loggen för App Server-sidan för att se om App Server har startats om.
3. Skapa ett problem. Ange tidsram och e-posta resurs namnet till oss.

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tips"></a>Tips

<a name="view_request"></a>

* Hur visar du utgående begäran från klienten?
Ta ASP.NET Core ett till exempel (ASP.NET One liknar):
    * Från webbläsare:

        Ta Chrome som exempel kan du använda **F12** för att öppna konsol fönstret och växla till fliken **nätverk** . Du kan behöva uppdatera sidan med **F5** för att avbilda nätverket från början.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome Visa nätverk":::

    * Från C#-klient:

        Du kan visa lokala webb trafik med hjälp av [Fiddler](https://www.telerik.com/fiddler). WebSocket-trafik stöds sedan Fiddler 4,5.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler Visa nätverk" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* Hur startar jag om klient anslutning?
    
    Här är [exempel koderna](https://github.com/Azure/azure-signalr/tree/dev/samples) som innehåller omstart av anslutnings logiken med en strategi för *alltid återförsök* :

    * [ASP.NET Core C#-klient](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Core JavaScript-klient](https://github.com/Azure/azure-signalr/blob/release/1.0.0-preview1/samples/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET C#-klient](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET JavaScript-klient](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Nästa steg

I den här hand boken har du lärt dig hur du hanterar vanliga problem. Du kan också lära dig mer generiska fel söknings metoder. 

> [!div class="nextstepaction"]
> [Så här felsöker du problem med anslutning och meddelande leverans](./signalr-howto-troubleshoot-method.md)
