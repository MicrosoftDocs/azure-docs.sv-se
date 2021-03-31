---
title: Skala med flera instanser – Azure SignalR-tjänsten
description: I många skalnings scenarier behöver kunden ofta etablera flera instanser och konfigurera att använda dem tillsammans för att skapa en storskalig distribution. Horisontell partitionering kräver exempelvis stöd för flera instanser.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: fd6ac8c4d4fc4c3fec4f549f8ef4f955e2b1c637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89439222"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Hur skalar jag SignalR-tjänsten med flera instanser?
Den senaste tjänsten för SignalR service SDK stöder flera slut punkter för signalerande tjänst instanser. Du kan använda den här funktionen för att skala samtidiga anslutningar eller använda den för meddelanden över flera regioner.

## <a name="for-aspnet-core"></a>För ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hur lägger du till flera slut punkter från config?

Config med nyckel `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` -eller signal tjänst anslutnings sträng.

Om nyckeln börjar med `Azure:SignalR:ConnectionString:` ska den vara i format `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` , där `Name` och `EndpointType` är egenskaper för `ServiceEndpoint` objektet och kan nås från kod.

Du kan lägga till flera instans anslutnings strängar med följande `dotnet` kommandon:

```cmd
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hur lägger jag till flera slut punkter från koden?

En `ServicEndpoint` klass introduceras för att beskriva egenskaperna för en Azure SignalR service-slutpunkt.
Du kan konfigurera flera instans slut punkter när du använder Azure SignalR service SDK via:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Hur anpassar du slut punkts router?

Som standard använder SDK [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) för att hämta slut punkter.

#### <a name="default-behavior"></a>Standardbeteende 
1. Routning av klient begär Anden

    När klienten `/negotiate` har en app-server. Som standard väljer SDK **slumpmässigt** en slut punkt från uppsättningen tillgängliga tjänst slut punkter.

2. Routning av Server meddelande

    När * skickar ett meddelande till en unik * *-anslutning * * * och mål anslutningen dirigeras till den aktuella servern, skickas meddelandet direkt till den anslutna slut punkten. Annars skickas meddelandena till varje Azure SignalR-slutpunkt.

#### <a name="customize-routing-algorithm"></a>Anpassa algoritmen för routning
Du kan skapa en egen router när du har särskilda kunskaper för att identifiera vilka slut punkter som meddelandena ska gå till.

En anpassad router definieras nedan som ett exempel när grupper som börjar med `east-` alltid går till slut punkten med namnet `east` :

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Ett annat exempel nedan, som åsidosätter standard förhandlings beteendet, för att välja slut punkterna beror på var app-servern finns.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Glöm inte att registrera routern på en DI-behållare med:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>För ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hur lägger du till flera slut punkter från config?

Config med nyckel `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` -eller signal tjänst anslutnings sträng.

Om nyckeln börjar med `Azure:SignalR:ConnectionString:` ska den vara i format `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` , där `Name` och `EndpointType` är egenskaper för `ServiceEndpoint` objektet och kan nås från kod.

Du kan lägga till flera instans anslutnings strängar i `web.config` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hur lägger jag till flera slut punkter från koden?

En `ServicEndpoint` klass introduceras för att beskriva egenskaperna för en Azure SignalR service-slutpunkt.
Du kan konfigurera flera instans slut punkter när du använder Azure SignalR service SDK via:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Så här anpassar du router?

Den enda skillnaden mellan ASP.NET-Signaleraren och ASP.NET Core SignalR är http-kontexten för `GetNegotiateEndpoint` . För ASP.NET-signaler är det av [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) -typ.

Nedan visas det anpassade förhandlings exemplet för ASP.NET-Signalerare:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Glöm inte att registrera routern på en DI-behållare med:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Konfiguration i scenarier med flera regioner

`ServiceEndpoint`Objektet har en `EndpointType` egenskap med Value `primary` eller `secondary` .

`primary` slut punkter är önskade slut punkter för att ta emot klient trafik och anses ha mer pålitliga nätverks anslutningar. `secondary` slut punkter anses ha mindre pålitliga nätverks anslutningar och används endast för att ta server till klient trafik, till exempel broadcast-meddelanden, inte för att ta klient till Server trafik.

I frågor över flera regioner kan nätverket vara instabilt. För en app-server i *östra USA* kan SignalR service-slutpunkten i samma region i *östra USA* konfigureras som `primary` och slut punkter i andra regioner som har marker ATS som `secondary` . I den här konfigurationen kan tjänst slut punkter i andra regioner **ta emot** meddelanden från den här *amerikanska* app-servern, men det finns inga klienter **över flera regioner** som skickas till den här program servern. Arkitekturen visas i diagrammet nedan:

![Mellan geo-infraröd](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

När en klient försöker `/negotiate` med app-servern, med standardroutern, **väljer SDK slumpmässigt** en slut punkt från uppsättningen tillgängliga `primary` slut punkter. När den primära slut punkten inte är tillgänglig väljer SDK **slumpmässigt** från alla tillgängliga `secondary` slut punkter. Slut punkten är markerad som **tillgänglig** när anslutningen mellan servern och tjänst slut punkten är Alive.

När en klient försöker `/negotiate` med den app-server som finns i *östra USA* som standard, returnerar den alltid den `primary` slut punkt som finns i samma region. När alla slut punkter för *östra USA* inte är tillgängliga omdirigeras klienten till slut punkter i andra regioner. I avsnittet redundans beskrivs scenariot i detalj.

![Normal förhandling](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Redundans

När alla `primary` slut punkter inte är tillgängliga, kan klientens `/negotiate` plockningar från tillgängliga `secondary` slut punkter. Den här redundansväxla mekanismen kräver att varje slut punkt ska fungera som `primary` slut punkt till minst en app server.

![Redundans](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du konfigurerar flera instanser i samma program för skalnings-, horisontell partitionering-och kors regions scenarier.

Stöd för flera slut punkter kan också användas i scenarier med hög tillgänglighet och haveri beredskap.

> [!div class="nextstepaction"]
> [Konfigurera signal tjänsten för haveri beredskap och hög tillgänglighet](./signalr-concept-disaster-recovery.md)
