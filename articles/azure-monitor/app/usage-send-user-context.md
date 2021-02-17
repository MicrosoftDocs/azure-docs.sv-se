---
title: 'Användar kontext-ID: n för att spåra aktiviteter – Azure Application insikter'
description: Spåra hur användare går igenom tjänsten genom att tilldela var och en av dem en unik, beständig ID-sträng i Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: 021c76bcd03bbe35eabec5611fe0cc1e2c7c4427
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583337"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Skicka användar kontext-ID: n för att aktivera användnings upplevelser i Azure Application Insights

## <a name="tracking-users"></a>Spåra användare

Med Application Insights kan du övervaka och spåra dina användare via en uppsättning produkt användnings verktyg:

- [Användare, sessioner, händelser](./usage-segmentation.md)
- [Trattar](./usage-funnels.md)
- [Kvarhållning](./usage-retention.md) Kohorter
- [Arbetsböcker](../visualize/workbooks-overview.md)

För att kunna spåra vad en användare använder med tiden behöver Application Insights ett ID för varje användare eller session. Inkludera följande ID: n i varje anpassad händelse-eller sid visning.

- Användare, trattar, kvarhållning och kohorter: inkludera användar-ID.
- Sessioner: ta med sessions-ID.

> [!NOTE]
> Det här är en avancerad artikel som beskriver de manuella stegen för att spåra användar aktivitet med Application Insights. Med många webb program behöver **de här stegen inte utföras** eftersom standard-SDK: er för Server sidan tillsammans med [klient-och webb läsar sidans JavaScript SDK](./website-monitoring.md)är ofta tillräckliga för att automatiskt spåra användar aktivitet. Om du inte har konfigurerat [övervakning på klient sidan](./website-monitoring.md) utöver SDK för Server sidan, gör du det först och testar för att se om verktygen för användar beteende analys fungerar som förväntat.

## <a name="choosing-user-ids"></a>Välja användar-ID

Användar-ID: n bör bevaras över användarsessioner som spårar hur användare beter sig över tid. Det finns olika metoder för att bevara ID: t.

- En definition av en användare som du redan har i din tjänst.
- Om tjänsten har åtkomst till en webbläsare kan den skicka en cookie med ett ID i webbläsaren. ID: t är kvar så länge cookien finns kvar i användarens webbläsare.
- Om det behövs kan du använda ett nytt ID varje session, men resultatet om användarna är begränsat. Du kan till exempel inte se hur en användares beteende förändras över tid.

ID: t ska vara ett GUID eller en annan sträng som är tillräckligt komplex för att identifiera varje användare unikt. Det kan till exempel vara ett långt slumptal.

Om ID: t innehåller personligt identifierad information om användaren, är det inte ett lämpligt värde att skicka till Application Insights som ett användar-ID. Du kan skicka ett sådant ID som ett [autentiserat användar-ID](./api-custom-events-metrics.md#authenticated-users), men det uppfyller inte användar-ID-kravet för användnings scenarier.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET appar: Ange användar kontexten i en ITelemetryInitializer

Skapa en telemetri-initierare enligt beskrivningen i detalj [här](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer). Skicka sessions-ID: t via telemetri för begäran och ange Context.User.Id och Context.Session.Id.

I det här exemplet anges användar-ID: t till en identifierare som upphör att gälla efter sessionen. Använd om möjligt ett användar-ID som finns kvar mellan sessioner.

### <a name="telemetry-initializer"></a>Telemetri-initierare

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Börja skicka [anpassade händelser](./api-custom-events-metrics.md#trackevent) eller [sid visningar](./api-custom-events-metrics.md#page-views)om du vill aktivera användnings upplevelser.
- Om du redan skickar anpassade händelser eller sid visningar, utforska användnings verktygen för att lära dig hur användarna använder tjänsten.
    - [Översikt över användning](usage-overview.md)
    - [Användare, sessioner och händelser](usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](usage-retention.md)
    - [Arbetsböcker](../visualize/workbooks-overview.md)

