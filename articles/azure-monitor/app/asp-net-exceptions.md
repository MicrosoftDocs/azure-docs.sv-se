---
title: Diagnostisera fel och undantag med Azure Application insikter
description: Fånga undantag från ASP.NET-appar tillsammans med telemetri för begäran.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/11/2019
ms.openlocfilehash: 36e916eabfca8e997fc3d46ff10f6201203457cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936511"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnostisera undantag i dina webbappar med Application Insights
Undantag i din Live-webbapp rapporteras av [Application Insights](./app-insights-overview.md). Du kan korrelera misslyckade förfrågningar med undantag och andra händelser på både klienten och servern, så att du snabbt kan diagnostisera orsakerna.

## <a name="set-up-exception-reporting"></a>Konfigurera undantags rapportering
* Så här har du undantag som rapporter ATS från din server app:
  * Azure-Webbappar: Lägg till [Application Insights-tillägget](./azure-web-apps.md)
  * Virtuella Azure-datorer och skalnings uppsättningar för virtuella Azure-datorer: Lägg till [tillägget för program övervakning](./azure-vm-vmss-apps.md)
  * Installera [Application Insights SDK](./asp-net.md) i din app-kod eller
  * IIS-webbservrar: kör [Application Insights agent](./monitor-performance-live-website-now.md); eller
  * Java-webbappar: aktivera [Java-agenten](./java-in-process-agent.md)
* Installera [JavaScript-kodfragmentet](./javascript.md) på dina webb sidor för att fånga webb läsar undantag.
* I vissa program ramverk eller med vissa inställningar måste du vidta några extra steg för att fånga fler undantag:
  * [Webb formulär](#web-forms)
  * [MVC](#mvc)
  * [Webb-API 1. *](#web-api-1x)
  * [Webb-API 2. *](#web-api-2x)
  * [WCF](#wcf)

  Den här artikeln fokuserar särskilt på .NET Framework appar från ett kod exempel perspektiv. Några av de metoder som fungerar för .NET Framework är föråldrade i .NET Core SDK. Läs .NET Core SDK- [dokumentationen](./asp-net-core.md) om du har en .net Core-app.

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnostisera undantag med Visual Studio
Öppna App-lösningen i Visual Studio för att få hjälp med fel sökning.

Kör appen, antingen på din server eller på din utvecklings dator med hjälp av F5.

Öppna fönstret Application Insights sökning i Visual Studio och Ställ in det för att visa händelser från din app. När du felsöker kan du göra detta genom att klicka på knappen Application Insights.

![Högerklicka på projektet och välj Application Insights, öppna.](./media/asp-net-exceptions/34.png)

Observera att du kan filtrera rapporten så att endast undantag visas.

*Visas inga undantag? Se [avbildnings undantag](#exceptions).*

Klicka på en undantags rapport om du vill visa dess stack spårning.
Klicka på en rad referens i stack spårningen för att öppna relevant kod fil.

Observera att CodeLens visar data om undantagen i koden:

![CodeLens meddelande om undantag.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnostisera fel med hjälp av Azure Portal
Application Insights innehåller en granskad APM-upplevelse som hjälper dig att diagnostisera fel i dina övervakade program. Starta genom att klicka på alternativet för att Miss lyckas på Application Insights resurs-menyn som finns i avsnittet Undersök.
Du bör se en hel skärms visning som visar fel frekvensen för dina begär Anden, hur många som är felaktiga och hur många användare som påverkas. Till höger ser du några av de mest användbara distributionerna som är speciella för den valda åtgärden som misslyckades, inklusive de tre översta svars koderna, de tre vanligaste undantags typerna och de tre vanligaste typerna av beroende typer.

![Prioritering visare (åtgärd-fliken)](./media/asp-net-exceptions/failures0719.png)

I ett enda klick kan du granska representativa exempel för var och en av dessa under uppsättningar av åtgärder. För att diagnostisera undantag kan du i synnerhet Klicka på antalet för ett visst undantag som ska visas på fliken transaktions information från slut punkt till slut punkt, till exempel den här:

![Fliken transaktions information från slut punkt till slut punkt](./media/asp-net-exceptions/end-to-end.png)

**Alternativt** kan du, i stället för att titta på undantag för en åtgärd som inte kan utföras, starta från den övergripande vyn över undantag genom att växla till fliken undantag överst. Här kan du se alla undantag som har samlats in för din övervakade app.

*Visas inga undantag? Se [avbildnings undantag](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Anpassad spårning och loggdata
Om du vill hämta diagnostikdata som är specifika för din app kan du infoga kod för att skicka egna telemetridata. Detta visas i diagnostisk sökning tillsammans med begäran, sid visning och andra automatiskt insamlade data.

I det syftet har du flera alternativ:

* [TrackEvent ()](./api-custom-events-metrics.md#trackevent) används vanligt vis för att övervaka användnings mönster, men data som skickas visas också under anpassade händelser i diagnostisk sökning. Händelser namnges och kan innehålla sträng egenskaper och numeriska mått som du kan använda för [att filtrera diagnostiska sökningar](./diagnostic-search.md).
* Med [TrackTrace ()](./api-custom-events-metrics.md#tracktrace) kan du skicka längre data, till exempel post uppgifter.
* [TrackException ()](#exceptions) skickar stack spår. [Mer om undantag](#exceptions).
* Om du redan använder ett loggnings ramverk som Log4Net eller NLog kan du [samla in dessa loggar](asp-net-trace-logs.md) och se dem i diagnostisk sökning, tillsammans med förfrågningar och undantags data.

Om du vill se de här händelserna öppnar du [Sök](./diagnostic-search.md) på menyn till vänster, väljer **händelse typerna** för List rutan och väljer sedan anpassad händelse, spårning eller undantag.

![Visa detaljerad information](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Om din app genererar mycket telemetri minskar den anpassningsbara insamlingsmodulen automatiskt den mängd som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som är en del av samma åtgärd markeras eller avmarkeras som en grupp, så att du kan navigera mellan relaterade händelser. [Lär dig mer om sampling.](./sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Så här visar du begäran om att publicera data
Information om begäran innehåller inte de data som skickas till din app i ett POST-anrop. För att dessa data ska rapporteras:

* [Installera SDK](./asp-net.md) i ditt program projekt.
* Infoga kod i programmet för att anropa [Microsoft. ApplicationInsights. TrackTrace ()](./api-custom-events-metrics.md#tracktrace). Skicka POST-data i meddelande parametern. Det finns en gräns för tillåten storlek, så du bör försöka skicka enbart viktiga data.
* När du undersöker en misslyckad begäran hittar du de associerade spårningarna.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> Fånga undantag och relaterade diagnostikdata
Först visas inte i portalen alla undantag som orsakar fel i din app. Du ser eventuella webb läsar undantag (om du använder [JavaScript SDK](./javascript.md) på dina webb sidor). Men de flesta Server undantagen fångas av IIS och du måste skriva kod för att se dem.

Du kan:

* **Logga undantag explicit** genom att infoga kod i undantags hanterare för att rapportera undantagen.
* **Samla in undantag automatiskt** genom att konfigurera ASP.net-ramverket. De nödvändiga tilläggen skiljer sig mellan olika typer av ramverk.

## <a name="reporting-exceptions-explicitly"></a>Rapportera undantag uttryckligen
Det enklaste sättet är att infoga ett anrop till TrackException () i en undantags hanterare.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

Parametrarna för egenskaper och mätningar är valfria, men är användbara för [att filtrera och lägga till](./diagnostic-search.md) extra information. Om du till exempel har en app som kan köra flera spel kan du hitta alla undantags rapporter som är relaterade till ett visst spel. Du kan lägga till så många objekt som du vill i varje ord lista.

## <a name="browser-exceptions"></a>Webbläsarundantag
De flesta webb läsar undantag rapporteras.

Om din webb sida innehåller skriptfiler från nätverk för innehålls leverans eller andra domäner, se till att skript tag gen har attributet ```crossorigin="anonymous"``` och att servern skickar [CORS-huvuden](https://enable-cors.org/). På så sätt kan du hämta en stack spårning och information för ohanterade JavaScript-undantag från dessa resurser.

## <a name="reuse-your-telemetry-client"></a>Återanvänd din telemetri-klient

> [!NOTE]
> TelemetryClient rekommenderas att instansieras en gång och sedan återanvändas under hela livs längden för ett program.

Nedan visas ett exempel som använder TelemetryClient korrekt.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Webb formulär
För webb formulär kommer HTTP-modulen att kunna samla in undantag när det inte finns några omdirigeringar konfigurerade med CustomErrors.

Men om du har aktiva omdirigeringar lägger du till följande rader i Application_Error-funktionen i global. asax. cs. (Lägg till en global. ASAX-fil om du inte redan har en.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
Från och med Application Insights Web SDK version 2,6 (beta3 och senare), samlar Application Insights ut ohanterade undantag som har utlösts i metoderna MVC 5 + controllers automatiskt. Om du tidigare har lagt till en anpassad hanterare för att spåra sådana undantag (enligt beskrivningen i följande exempel) kan du ta bort den för att förhindra dubbel spårning av undantag.

Det finns ett antal fall som undantags filtren inte kan hantera. Exempel:

* Undantag som utlöses från styrenhetskonstruktörer.
* Undantag som utlöses av meddelandehanterare.
* Undantag som utlöses under routning.
* Undantag som utlöses under serialisering av svarsinnehåll.
* Undantag som utlöses vid programstart.
* Undantag som utlöses i bakgrundsaktiviteter.

Alla undantag som *hanteras* av programmet måste fortfarande spåras manuellt.
Ohanterade undantag från kontrollanter resulterar vanligt vis i svar på 500 "internt Server fel". Om ett sådant svar manuellt konstrueras till följd av ett hanterat undantag (eller inget undantag alls) spåras det i motsvarande telemetri för begäran med `ResultCode` 500, men Application Insights SDK kan inte spåra motsvarande undantag.

### <a name="prior-versions-support"></a>Stöd för tidigare versioner
Om du använder MVC 4 (och tidigare) av Application Insights Web SDK 2,5 (och tidigare) kan du läsa följande exempel för att spåra undantag.

Om [customErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) -konfigurationen är `Off` , blir undantag tillgängliga för [http-modulen](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85)) att samla in. Men om det är `RemoteOnly` (standard) eller, så `On` rensas undantaget och är inte tillgängligt för Application Insights att samlas in automatiskt. Du kan åtgärda detta genom att åsidosätta [klassen system. Web. MVC. HandleErrorAttribute](/dotnet/api/system.web.mvc.handleerrorattribute?view=aspnet-mvc-5.2)och tillämpa den åsidosatta klassen som visas för de olika MVC-versionerna nedan ([GitHub source](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Ersätt attributet HandleError med ditt nya attribut i dina styrenheter.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Exempel](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registrera `AiHandleErrorAttribute` som ett globalt filter i global. asax. CS:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Exempel](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Registrera AiHandleErrorAttribute som ett globalt filter i FilterConfig. CS:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Exempel](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Webb-API
Från och med Application Insights Web SDK version 2,6 (beta3 och senare) samlar Application Insights ut ohanterade undantag som har utlösts i styrenhets metoderna automatiskt för WebAPI 2 +. Om du tidigare har lagt till en anpassad hanterare för att spåra sådana undantag (enligt beskrivningen i följande exempel) kan du ta bort den för att förhindra dubbel spårning av undantag.

Det finns ett antal fall som undantags filtren inte kan hantera. Exempel:

* Undantag som utlöses från styrenhetskonstruktörer.
* Undantag som utlöses av meddelandehanterare.
* Undantag som utlöses under routning.
* Undantag som utlöses under serialisering av svarsinnehåll.
* Undantag som utlöses vid programstart.
* Undantag som utlöses i bakgrundsaktiviteter.

Alla undantag som *hanteras* av programmet måste fortfarande spåras manuellt.
Ohanterade undantag från kontrollanter resulterar vanligt vis i svar på 500 "internt Server fel". Om ett sådant svar manuellt konstrueras till följd av ett hanterat undantag (eller inget undantag alls) spåras det i en motsvarande begäran om telemetri med `ResultCode` 500, men Application Insights SDK kan inte spåra motsvarande undantag.

### <a name="prior-versions-support"></a>Stöd för tidigare versioner
Om du använder WebAPI 1 (och tidigare) av Application Insights Web SDK 2,5 (och tidigare) kan du läsa följande exempel för att spåra undantag.

#### <a name="web-api-1x"></a>Webb-API 1. x
Åsidosätt system. Web. http. filters. ExceptionFilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Du kan lägga till detta åsidosatta attribut till vissa styrenheter eller lägga till det i den globala filter konfigurationen i WebApiConfig-klassen:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Exempel](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Webb-API 2. x
Lägg till en implementering av IExceptionLogger:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Lägg till detta i tjänsterna i WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Exempel](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Som alternativ kan du:

1. Ersätt den enda ExceptionHandler med en anpassad implementering av IExceptionHandler. Detta anropas endast när ramverket fortfarande kan välja vilket svarsmeddelande som ska skickas (inte när anslutningen avbryts för instansen)
2. Undantags filter (enligt beskrivningen i avsnittet om webb-API 1. x styrenheter ovan) – inte anropat i samtliga fall.

## <a name="wcf"></a>WCF
Lägg till en klass som utökar attribut och implementerar IErrorHandler och IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Exempel](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Prestanda räknare för undantag
Om du har [installerat Application Insights agent](./monitor-performance-live-website-now.md) på servern kan du få ett diagram över undantags frekvensen, mätt av .net. Detta inkluderar både hanterade och ohanterade .NET-undantag.

Öppna fliken Metric Explorer, Lägg till ett nytt diagram och välj **undantags frekvens** i listan under prestanda räknare.

.NET Framework beräknar frekvensen genom att räkna antalet undantag i ett intervall och dividera med längden på intervallet.

Detta skiljer sig från antalet undantag som beräknas av Application Insights portal som räknar TrackException-rapporter. Samplings intervallen är olika och SDK: n skickar inte TrackException-rapporter för alla hanterade och ohanterade undantag.

## <a name="next-steps"></a>Nästa steg
* [Övervaka REST, SQL och andra anrop till beroenden](./asp-net-dependencies.md)
* [Övervaka sid inläsnings tider, webb läsar undantag och AJAX-anrop](./javascript.md)
* [Övervaka prestanda räknare](./performance-counters.md)

