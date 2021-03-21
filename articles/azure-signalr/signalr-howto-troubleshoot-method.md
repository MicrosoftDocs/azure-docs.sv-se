---
title: Felsöka praxis för Azure SignalR service
description: Lär dig hur du felsöker problem med anslutning och meddelande leverans
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 2e22777b747ae24c3e643cbd43bfdb0604d453a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97707664"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Så här felsöker du problem med anslutning och meddelande leverans

Den här vägledningen innehåller flera olika sätt att hjälpa till med självbetjäning för att hitta rotor saken direkt eller begränsa problemet. Resultatet av självdiagnostik är också användbart vid rapportering till oss för ytterligare undersökning.

Först måste du kontrol lera från Azure Portal vilka [ServiceMode](./concept-service-mode.md) är Azure SignalR-tjänsten (även kallat **ASRS**) konfigurerad för.

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* För `Default` läge, se [fel sökning av standard läge](#default_mode_tsg)

* För `Serverless` läge, se [fel sökning i Server lös läge](#serverless_mode_tsg)

* För `Classic` läge, se [fel sökning i klassiskt läge](#classic_mode_tsg)

<a name="default_mode_tsg"></a>

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="default-mode-troubleshooting"></a>Fel sökning av standard läge

När **ASRS** är i *standard* läge finns det **tre** roller: *klient*, *Server* och *tjänst*:

* *Klient*: *klienten* står för klienter som är anslutna till **ASRS**. De beständiga anslutningarna som ansluter klient-och **ASRS** kallas *klient anslutningar* i den här vägledningen.

* *Server*: *servern* står för den server som hanterar klient förhandlingen och är värd för signaler- `Hub` logik. Och beständiga anslutningar mellan *Server* -och **ASRS** kallas *Server anslutningar* i den här vägledningen.

* *Tjänst*: *tjänst* är kort namnet för **ASRS** i den här vägledningen.

Den detaljerade introduktionen av hela arkitekturen och arbets flödet hittar du i [Internal of Azure SignalR service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

Det finns flera sätt som kan hjälpa dig att begränsa problemet. 

* Om problemet fungerar som det ska, eller är återskapnings, så är det rakt framåtriktade sättet att visa den pågående trafiken. 

* Om problemet är svårt att återskapnings kan spår och loggar hjälpa dig.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Så här visar du trafiken och begränsar problemet

Att samla in den pågående trafiken är det mest effektiva sättet att begränsa problemet. Du kan avbilda [nätverks spåren](/aspnet/core/signalr/diagnostics#network-traces) med hjälp av alternativen som beskrivs nedan:

* [Samla in ett nätverks spår med Fiddler](/aspnet/core/signalr/diagnostics#network-traces)

* [Samla in ett nätverks spår med tcpdump](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Samla in ett nätverks spår i webbläsaren](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Klient begär Anden

För en Signals beständig anslutning är den först `/negotiate` till den värdbaserade app-servern och sedan Omdirigerad till Azure SignalR-tjänsten och upprättar sedan den verkliga permanenta anslutningen till Azure SignalR-tjänsten. Mer information om de detaljerade stegen hittar du i [interna Azure SignalR-tjänster](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

Med nätverks spårning på klient sidan kan du kontrol lera vilken begäran som Miss lyckas med vilken status kod och vilka svar och leta efter lösningar i [fel söknings guiden](./signalr-howto-troubleshoot-guide.md).

#### <a name="server-requests"></a>Server begär Anden

SignalR *Server* underhåller *Server anslutningen* mellan *Server* och *tjänst*. När app server startar startar den **WebSocket** -anslutningen till Azure SignalR-tjänsten. Alla klient trafik dirigeras via Azure SignalR service till dessa *Server anslutningar* och skickas sedan till `Hub` . När en *Server anslutning* släpps påverkas klienterna som dirigeras till den här *Server anslutningen* . Vår Azure SignalR SDK har en logik "Always retry" för att återansluta *Server anslutningen* med högst 1 minuters fördröjning för att minimera påverkan.

*Server anslutning* kan släppas på grund av instabilitet i nätverket eller regelbundet underhåll av Azure SignalR-tjänsten, eller din värdbaserade App Server-uppdatering/-underhåll. Så länge klient sidan har metoden koppla från/Återanslut är effekten minimal, precis som alla klient sidor orsakade från kopplings anslutning.

Visa nätverks spårning på Server sidan för att ta reda på status koden och fel information varför *Server anslutningen* tappas bort eller avvisas av *tjänsten*, och leta efter rotor saken i [fel söknings guiden](./signalr-howto-troubleshoot-guide.md).

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>Lägga till loggar

Loggar kan vara användbara för att diagnostisera problem och övervaka körnings statusen.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Så här aktiverar du loggen på klient Sidan

Loggning på klient sidan är exakt detsamma som när du använder en egen värdbaserad SignalR.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Aktivera loggning på klient sidan för `ASP.NET Core SignalR`

* [Loggning av JavaScript-klient](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET-klient loggning](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Aktivera loggning på klient sidan för `ASP.NET SignalR`

* [.NET-klient](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Aktivera spårning i Windows Phone 8-klienter](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Aktivera spårning i JavaScript-klienten](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Så här aktiverar du logg på Server Sidan

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Aktivera loggning på Server sidan för `ASP.NET Core SignalR`

Loggning på Server sidan för `ASP.NET Core SignalR` integreras med den `ILogger` baserade [loggning](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) som finns i `ASP.NET Core` ramverket. Du kan aktivera loggning på Server sidan genom att använda `ConfigureLogging` , en exempel användning på följande sätt:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Loggnings kategorier för Azure SignalR börjar alltid med `Microsoft.Azure.SignalR` . Om du vill aktivera detaljerade loggar från Azure-Signaleraren konfigurerar du föregående prefix till `Information` nivå i **appsettings.jspå** filen som nedan:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Kontrol lera om några felaktiga varnings-eller fel loggar har registrerats. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Aktivera spårning på Server sidan för `ASP.NET SignalR`

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

Kontrol lera om några felaktiga varnings-eller fel loggar har registrerats. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Så här aktiverar du loggar i Azure SignalR-tjänsten

Du kan också [Aktivera diagnostikloggar](./signalr-howto-diagnostic-logs.md) för Azure SignalR service. dessa loggar innehåller detaljerad information om varje anslutning som är ansluten till Azure SignalR-tjänsten.

<a name="serverless_mode_tsg"></a>

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>Fel sökning av Server lös läge

När **ASRS** är i ett *Server* fritt läge stöder endast **ASP.NET Core signalerer** `Serverless` , och **ASP.net-signaleraren** stöder **inte** det här läget.

För att diagnostisera anslutnings problem i `Serverless` läget är det vanligaste sättet att [Visa trafik på klient sidan](#view_traffic_client). Det kan också vara bra att aktivera [loggar på klient sidan](#add_logs_client) och [loggar på tjänst sidan](#add_logs_server) .

<a name="classic_mode_tsg"></a>

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>Fel sökning av klassiskt läge

`Classic` Läget är föråldrat och rekommenderas inte att användas. I det här läget använder Azure SignalR service de anslutna *Server anslutningarna* för att avgöra om den aktuella tjänsten är i `default` läge eller `serverless` läge. Detta kan leda till några mellanliggande klient anslutnings problem, eftersom det är ett plötsligt sätt att ta bort alla anslutna *Server anslutningar*, till exempel på grund av instabilitet i nätverket, att Azure SignalR nu är växlat till `serverless` läge och att klienter som är anslutna under denna period aldrig dirigeras till den värdbaserade program servern. Aktivera [loggar på tjänst sidan](#add_logs_server) och kontrol lera om det finns några klienter som har registrerats som `ServerlessModeEntered` om du har en app-server, men vissa klienter kommer aldrig åt app server-sidan. Om det finns några kan du [avbryta de här klient anslutningarna](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) och låta klienterna starta om kan hjälpa dig.

Fel söknings `classic` läge anslutnings-och meddelande leverans problem liknar [fel sökning av problem med standard läge](#default_mode_tsg).

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>Service Health:

Du kan kontrol lera hälso-API: t för tjänstens hälsa.

* Begäran: Hämta `https://{instance_name}.service.signalr.net/api/v1/health`

* Svars status kod:
  * 200: felfri.
  * 503: tjänsten är inte felfri. Du kan:
    * Vänta flera minuter för återskapning.
    * Kontrol lera att IP-adressen är samma som IP-adressen från portalen.
    * Eller starta om instans.
    * Om alla alternativ ovan inte fungerar kan du kontakta oss genom att lägga till nya support förfrågningar i Azure Portal.

Mer om [haveri beredskap](./signalr-concept-disaster-recovery.md).

[Har du problem eller feedback om fel sökningen? Berätta för oss.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du felsöker problem med anslutning och meddelande leverans. Du kan också lära dig hur du hanterar vanliga problem. 

> [!div class="nextstepaction"]
> [Felsökningsguide](./signalr-howto-troubleshoot-guide.md)