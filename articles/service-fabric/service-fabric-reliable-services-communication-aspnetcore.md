---
title: Tjänst kommunikation med ASP.NET Core
description: Lär dig hur du använder ASP.NET Core i tillstånds lösa och tillstånds känsliga Azure-Service Fabric Reliable Services program.
ms.topic: conceptual
ms.date: 10/12/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: a125c6a1972b51f518175a4c69248119f71ada7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791602"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core i Azure Service Fabric Reliable Services

ASP.NET Core är ett ramverk med öppen källkod och plattforms oberoende. Det här ramverket är utformat för att skapa molnbaserade, Internet-anslutna program, till exempel webbappar, IoT-appar och mobila backend-platser.

Den här artikeln är en djupgående guide för att vara värd för ASP.NET Core tjänster i Service Fabric Reliable Services med hjälp av **Microsoft. ServiceFabric. AspNetCore.** uppsättning NuGet-paket.

En introduktions kurs om ASP.NET Core i Service Fabric och instruktioner om hur du hämtar utvecklings miljön finns i [självstudie: skapa och distribuera ett program med en ASP.net Core webb-API-frontend-tjänst och en tillstånds känslig Server dels tjänst](service-fabric-tutorial-create-dotnet-app.md).

Resten av den här artikeln förutsätter att du redan är bekant med ASP.NET Core. Om inte, Läs mer i [ASP.net Core grunderna](/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core i Service Fabrics miljön

Både ASP.NET Core-och Service Fabric-appar kan köras på .NET Core eller fullständig .NET Framework. Du kan använda ASP.NET Core på två olika sätt i Service Fabric:
 - **Är värd för en körbar gäst**. Det här sättet används främst för att köra befintliga ASP.NET Core-program på Service Fabric utan kod ändringar.
 - **Körs inuti en tillförlitlig tjänst**. På så sätt kan du snabbare integrera med Service Fabric Runtime och tillstånds känsliga ASP.NET Core-tjänster.

Resten av den här artikeln förklarar hur du använder ASP.NET Core i en tillförlitlig tjänst genom ASP.NET Core integrations komponenter som medföljer Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Service Fabric tjänst värd

I Service Fabric körs en eller flera instanser och/eller repliker av tjänsten i en *tjänst värd process*: en körbar fil som kör tjänst koden. Du, som tjänst författare, äger tjänstens värd process och Service Fabric aktiverar och övervakar den åt dig.

Traditionell ASP.NET (upp till MVC 5) är nära kopplad till IIS via System.Web.dll. ASP.NET Core ger en åtskillnad mellan webb servern och ditt webb program. Den här separationen gör att webb program kan vara bärbara mellan olika webb servrar. Det gör det också möjligt för webb servrar att vara *egen värd*. Det innebär att du kan starta en webb server i din egen process, i stället för en process som ägs av en särskild webb serverprogram vara, till exempel IIS.

Om du vill kombinera en Service Fabric tjänst-och ASP.NET, antingen som en gäst-körbar fil eller i en tillförlitlig tjänst, måste du kunna starta ASP.NET inuti tjänstens värd process. Med ASP.NET Core själv-värdskap kan du göra detta.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Vara värd för ASP.NET Core i en tillförlitlig tjänst
Vanliga ASP.NET Core program för egen värd skapar en WebHost i ett programs start punkt, till exempel `static void Main()` metoden i `Program.cs` . I det här fallet är livs cykeln för WebHost kopplad till processens livs cykel.

![Vara värd för ASP.NET Core i en process][0]

Men program start punkten är inte rätt plats för att skapa en WebHost i en tillförlitlig tjänst. Det beror på att program start punkten bara används för att registrera en tjänst typ med Service Fabric runtime, så att den kan skapa instanser av tjänst typen. WebHost bör skapas i en tillförlitlig tjänst. I tjänstens värd process kan tjänst instanser och/eller repliker gå igenom flera livscykler. 

En tillförlitlig tjänst instans representeras av tjänst klassen som härleds från `StatelessService` eller `StatefulService` . Kommunikations stacken för en tjänst finns i en `ICommunicationListener` implementering i din tjänst klass. `Microsoft.ServiceFabric.AspNetCore.*`NuGet-paketen innehåller implementeringar av `ICommunicationListener` som startar och hanterar ASP.net Core webhost för antingen Kestrel eller HTTP.sys i en tillförlitlig tjänst.

![Diagram för att vara värd för ASP.NET Core i en tillförlitlig tjänst][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener`Implementeringarna för Kestrel och HTTP.sys i NuGet- `Microsoft.ServiceFabric.AspNetCore.*` paketen har liknande användnings mönster. Men de utför något annorlunda åtgärder som är beroende av varje webb server. 

Båda kommunikations lyssnarna tillhandahåller en konstruktor som tar följande argument:
 - **`ServiceContext serviceContext`**: Det här är det `ServiceContext` objekt som innehåller information om den aktiva tjänsten.
 - **`string endpointName`**: Det här är namnet på en `Endpoint` konfiguration i ServiceManifest.xml. Det är främst där de två kommunikations lyssnarna skiljer sig. HTTP.sys *kräver* en `Endpoint` konfiguration, medan Kestrel inte.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Det här är ett lambda-tecken som du implementerar, där du kan skapa och returnera en `IWebHost` . Det gör att du kan konfigurera `IWebHost` hur du normalt skulle göra i ett ASP.net Core-program. Lambda-filen innehåller en URL som genereras åt dig, beroende på Service Fabric integrerings alternativ som du använder och `Endpoint` konfigurationen du anger. Du kan sedan ändra eller använda den URL: en för att starta webb servern.

## <a name="service-fabric-integration-middleware"></a>Service Fabric integration mellan
`Microsoft.ServiceFabric.AspNetCore`NuGet-paketet innehåller `UseServiceFabricIntegration` tilläggs metoden i `IWebHostBuilder` som lägger till Service Fabric – medvetna mellanprogram. Detta mellanprogram konfigurerar Kestrel eller HTTP.sys `ICommunicationListener` för att registrera en unik tjänst-URL med Service Fabric Naming Service. Den verifierar sedan klient begär Anden för att säkerställa att klienterna ansluter till rätt tjänst. 

Det här steget är nödvändigt för att förhindra att klienter kan ansluta till fel tjänst av misstag. Det beror på att i en delad värd miljö, till exempel Service Fabric, kan flera webb program köras på samma fysiska eller virtuella dator, men använder inte unika värdnamn. Det här scenariot beskrivs i detalj i nästa avsnitt.

### <a name="a-case-of-mistaken-identity"></a>Ett fall av en förväxlad identitet
Tjänst repliker, oavsett protokoll, lyssnar på en unik IP: port kombination. När en tjänst replik har börjat Lyssna på en IP-port, rapporterar den att slut punkts adressen till Service Fabric Naming Service. Klienter eller andra tjänster kan identifiera den. Om tjänsterna använder dynamiskt tilldelade program portar, kan en tjänst replik använda samma IP-adress (port slut punkt) för en annan tjänst tidigare på samma fysiska eller virtuella dator. Detta kan orsaka att en klient av misstag ansluter till fel tjänst. Det här scenariot kan resultera i att följande händelse förlopp inträffar:

 1. Tjänst A lyssnar på 10.0.0.1:30000 över HTTP. 
 2. Klienten matchar tjänst A och får adressen 10.0.0.1:30000.
 3. Tjänst A flyttar till en annan nod.
 4. Service B placeras på 10.0.0.1 och använder samma port 30000.
 5. Klienten försöker ansluta till tjänst A med den cachelagrade adressen 10.0.0.1:30000.
 6. Klienten har nu anslutits till tjänst B, och det är inte riktigt anslutet till fel tjänst.

Detta kan orsaka buggar vid slumpmässiga tidpunkter som kan vara svåra att diagnostisera.

### <a name="using-unique-service-urls"></a>Använda unika tjänst-URL: er
För att förhindra dessa buggar kan tjänsterna publicera en slut punkt till Naming Service med en unik identifierare och sedan verifiera den unika identifieraren under klient begär Anden. Detta är en samarbets åtgärd mellan tjänster i en icke-filsäker, betrodd miljö. Den ger inte säker tjänst-autentisering i en skydds-och klient miljö.

I en betrodd miljö lägger det mellanliggande programmet som läggs till av `UseServiceFabricIntegration` metoden automatiskt till en unik identifierare till adressen som skickas till Naming Service. Den verifierar den identifieraren på varje begäran. Om identifieraren inte matchar, returnerar mellanprogram omedelbart en svars tid som är HTTP 410.

Tjänster som använder en dynamiskt tilldelad port ska använda den här mellanprogram varan.

Tjänster som använder en fast unik Port har inte det här problemet i en kooperativ miljö. En fast unik port används vanligt vis för externt riktade tjänster som behöver en välkänd port för klient program att ansluta till. De flesta Internet-riktade webb program kommer till exempel att använda port 80 eller 443 för webb läsar anslutningar. I det här fallet bör den unika identifieraren inte vara aktive rad.

Följande diagram visar det begärda flödet med mellanliggande aktiverade:

![Service Fabric ASP.NET Core-integrering][2]

Både Kestrel-och HTTP.sys- `ICommunicationListener` implementeringar använder den här mekanismen på exakt samma sätt. Även om HTTP.sys kan särskilja begär Anden internt baserat på unika URL-sökvägar med hjälp av den underliggande delnings funktionen för **HTTP.sys** -port, används *inte* funktionen av HTTP.sys- `ICommunicationListener` implementeringen. Det beror på att det resulterar i status koder för HTTP 503 och HTTP 404 i det scenario som beskrivs ovan. I sin tur gör det svårt för klienter att fastställa avsikten med felet, eftersom HTTP 503 och HTTP 404 ofta används för att ange andra fel. 

Därför är både Kestrel-och HTTP.sys- `ICommunicationListener` implementeringar standardiserade på mellanprogram som tillhandahålls av `UseServiceFabricIntegration` tilläggs metoden. Klienterna behöver därför bara utföra en åtgärd för att lösa tjänst slut punkter på HTTP 410-svar.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys i Reliable Services
Du kan använda HTTP.sys i Reliable Services genom att importera paketet **Microsoft. ServiceFabric. AspNetCore. https** NuGet. Det här paketet innehåller `HttpSysCommunicationListener` en implementering av `ICommunicationListener` . `HttpSysCommunicationListener` gör att du kan skapa en ASP.NET Core WebHost i en tillförlitlig tjänst genom att använda HTTP.sys som webb server.

HTTP.sys bygger på API: [t för Windows HTTP-server](/windows/win32/http/http-api-start-page). Detta API använder **HTTP.sys** kernel-drivrutin för att bearbeta HTTP-begäranden och dirigera dem till processer som kör webb program. På så sätt kan flera processer på samma fysiska eller virtuella dator vara värd för webb program på samma port, disambiguated av antingen en unik URL-sökväg eller ett värdnamn. Dessa funktioner är användbara i Service Fabric för att vara värd för flera webbplatser i samma kluster.

>[!NOTE]
>HTTP.sys implementering fungerar bara på Windows-plattformen.

Följande diagram illustrerar hur HTTP.sys använder driv rutinen **HTTP.sys** kernel på Windows för port delning:

![HTTP.sys diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys i en tillstånds lös tjänst
Om du vill använda `HttpSys` i en tillstånds lös tjänst åsidosätter du `CreateServiceInstanceListeners` metoden och returnerar en `HttpSysCommunicationListener` instans:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys i en tillstånds känslig tjänst

`HttpSysCommunicationListener` är för närvarande inte avsedd för användning i tillstånds känsliga tjänster på grund av komplikationer med den underliggande delnings funktionen för **HTTP.sys** -porten. Mer information finns i följande avsnitt om dynamisk port tilldelning med HTTP.sys. För tillstånds känsliga tjänster är Kestrel den föreslagna webb servern.

### <a name="endpoint-configuration"></a>Slut punkts konfiguration

En `Endpoint` konfiguration krävs för webb servrar som använder Windows http server API, inklusive HTTP.sys. Webb servrar som använder Windows HTTP server-API: n måste först reservera sin URL med HTTP.sys (detta utförs vanligt vis med [netsh](/windows/win32/http/netsh-commands-for-http) -verktyget). 

Den här åtgärden kräver förhöjda privilegier som dina tjänster inte har som standard. Alternativen "http" eller "https" för `Protocol` egenskapen i `Endpoint` konfigurationen i ServiceManifest.xml används specifikt för att instruera Service Fabric runtime att registrera en URL med HTTP.sys åt dig. Detta görs med hjälp av det starka URL-prefixet med [*stark jokertecken*](/windows/win32/http/urlprefix-strings) .

Om du till exempel vill reservera `http://+:80` för en tjänst använder du följande konfiguration i ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Och slut punkts namnet måste skickas till `HttpSysCommunicationListener` konstruktorn:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Använda HTTP.sys med en statisk port
Om du vill använda en statisk port med HTTP.sys anger du Port numret i `Endpoint` konfigurationen:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Använda HTTP.sys med en dynamisk port
Om du vill använda en dynamiskt tilldelad port med HTTP.sys utelämnar du `Port` egenskapen i `Endpoint` konfigurationen:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

En dynamisk port som tilldelas av en `Endpoint` konfiguration tillhandahåller bara en port *per värd process*. Den aktuella Service Fabric värd modellen gör att flera tjänst instanser och/eller repliker kan finnas i samma process. Det innebär att var och en delar samma port när den allokeras genom `Endpoint` konfigurationen. Flera **HTTP.sys** -instanser kan dela en port med hjälp av den underliggande delnings funktionen för **HTTP.sys** -porten. Men det stöds inte på `HttpSysCommunicationListener` grund av de komplikationer som den introducerar för klient begär Anden. För dynamisk port användning är Kestrel den föreslagna webb servern.

## <a name="kestrel-in-reliable-services"></a>Kestrel i Reliable Services
Du kan använda Kestrel i Reliable Services genom att importera paketet **Microsoft. ServiceFabric. AspNetCore. Kestrel** NuGet. Det här paketet innehåller `KestrelCommunicationListener` en implementering av `ICommunicationListener` . `KestrelCommunicationListener` gör att du kan skapa en ASP.NET Core WebHost i en tillförlitlig tjänst genom att använda Kestrel som webb server.

Kestrel är en plattforms oberoende webb server för ASP.NET Core. Till skillnad från HTTP.sys använder Kestrel inte en central slut punkts hanterare. Till skillnad från HTTP.sys stöder Kestrel inte port delning mellan flera processer. Varje instans av Kestrel måste använda en unik port. Mer information om Kestrel finns i [implementerings informationen](/aspnet/core/fundamentals/servers/kestrel).

![Kestrel-diagram][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel i en tillstånds lös tjänst
Om du vill använda `Kestrel` i en tillstånds lös tjänst åsidosätter du `CreateServiceInstanceListeners` metoden och returnerar en `KestrelCommunicationListener` instans:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel i en tillstånds känslig tjänst
Om du vill använda `Kestrel` i en tillstånds känslig tjänst åsidosätter du `CreateServiceReplicaListeners` metoden och returnerar en `KestrelCommunicationListener` instans:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

I det här exemplet tillhandahålls en singleton-instans av `IReliableStateManager` till behållaren för webhost-beroende insprutning. Detta är inte absolut nödvändigt, men du kan använda `IReliableStateManager` och pålitliga samlingar i dina åtgärds metoder för MVC-styrenheten.

Inget `Endpoint` konfigurations namn  har angetts för `KestrelCommunicationListener` i en tillstånds känslig tjänst. Detta beskrivs mer detaljerat i följande avsnitt.

### <a name="configure-kestrel-to-use-https"></a>Konfigurera Kestrel för användning av HTTPS
När du aktiverar HTTPS med Kestrel i din tjänst måste du ange flera lyssnings alternativ. Uppdatera `ServiceInstanceListener` för att använda en *EndpointHttps* -slutpunkt och lyssna på en speciell port (till exempel port 443). När du konfigurerar webb värden att använda Kestrel-webbservern måste du konfigurera Kestrel för att lyssna efter IPv6-adresser på alla nätverks gränssnitt: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Ett fullständigt exempel i en själv studie kurs finns i [Konfigurera Kestrel för att använda https](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Slut punkts konfiguration
Ingen `Endpoint` konfiguration krävs för att använda Kestrel. 

Kestrel är en enkel fristående webb server. Till skillnad från HTTP.sys (eller HttpListener) behöver den inte en `Endpoint` konfiguration i ServiceManifest.xml eftersom den inte kräver URL-registrering innan den startas. 

#### <a name="use-kestrel-with-a-static-port"></a>Använda Kestrel med en statisk port
Du kan konfigurera en statisk port i `Endpoint` konfigurationen av ServiceManifest.xml för användning med Kestrel. Även om detta inte är absolut nödvändigt erbjuder det två möjliga fördelar:
 - Om porten inte ligger inom program ports intervallet öppnas den via OS-brandväggen genom att Service Fabric.
 - Den URL som du fick via `KestrelCommunicationListener` kommer att använda den här porten.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Om en `Endpoint` har kon figurer ATS måste dess namn skickas till `KestrelCommunicationListener` konstruktorn: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Om ServiceManifest.xml inte använder en `Endpoint` konfiguration utelämnar du namnet i `KestrelCommunicationListener` konstruktorn. I det här fallet kommer den att använda en dynamisk port. Mer information om detta finns i nästa avsnitt.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Använda Kestrel med en dynamisk port
Kestrel kan inte använda den automatiska port tilldelningen från `Endpoint` konfigurationen i ServiceManifest.xml. Det beror på att automatisk port tilldelning från en `Endpoint` konfiguration tilldelar en unik port per *värd process*, och en enda värd process kan innehålla flera Kestrel-instanser. Detta fungerar inte med Kestrel eftersom det inte stöder port delning. Därför måste varje Kestrel-instans öppnas på en unik port.

Om du vill använda dynamisk port tilldelning med Kestrel ska du utelämna `Endpoint` konfigurationen i ServiceManifest.xml helt och inte skicka ett slut punkts namn till `KestrelCommunicationListener` konstruktorn enligt följande:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

I den här konfigurationen `KestrelCommunicationListener` väljer automatiskt en oanvänd port från program port intervallet.

För HTTPS ska den ha slut punkten konfigurerad med HTTPS-protokollet utan att ha en angiven port i ServiceManifest.xml och skicka slut punkts namnet till KestrelCommunicationListener-konstruktorn.


## <a name="service-fabric-configuration-provider"></a>Service Fabric Konfigurationsprovider
App-konfigurationen i ASP.NET Core baseras på nyckel/värde-par som skapats av konfigurationsprovidern. Läs [konfiguration i ASP.net Core](/aspnet/core/fundamentals/configuration/) om du vill veta mer om allmän ASP.net Core konfigurations stöd.

I det här avsnittet beskrivs hur Service Fabric-konfigurationsprovidern integreras med ASP.NET Core-konfiguration genom att importera `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-paketet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration start tillägg
När du har importerat `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-paketet måste du registrera Service Fabric konfigurations källa med ASP.net Core Konfigurations-API. Det gör du genom att kontrol lera **AddServiceFabricConfiguration** -tilläggen i `Microsoft.ServiceFabric.AspNetCore.Configuration` namn området mot `IConfigurationBuilder` .

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Nu kan ASP.NET Core-tjänsten komma åt Service Fabric konfigurations inställningar, precis som andra program inställningar. Du kan till exempel använda alternativ mönstret för att läsa in inställningar till starkt skrivna objekt.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Standard nyckel mappning
Som standard innehåller Service Fabric konfigurationsprovidern paket namn, avsnitts namn och egenskaps namn. Tillsammans utgör dessa följande ASP.NET Core konfigurations nyckel enligt följande:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Om du till exempel har ett konfigurations paket med namnet `MyConfigPackage` med följande innehåll är konfiguration svärdet tillgängligt på ASP.net Core `IConfiguration` via *MyConfigPackage: MyConfigSection: parameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric konfigurations alternativ
Service Fabric-konfigurationsprovidern har också stöd `ServiceFabricConfigurationOptions` för att ändra standard beteendet för nyckel mappning.

#### <a name="encrypted-settings"></a>Krypterade inställningar
Service Fabric stöder krypterade inställningar, som Service Fabric konfigurationsprovidern. De krypterade inställningarna dekrypteras inte till ASP.NET Core `IConfiguration` som standard. De krypterade värdena lagras där i stället. Men om du vill dekryptera värdet för lagring i ASP.NET Core IConfiguration, kan du ställa in flaggan *DecryptValue* på false i `AddServiceFabricConfiguration` tillägget enligt följande:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Flera konfigurations paket
Service Fabric stöder flera konfigurations paket. Paket namnet ingår som standard i konfigurations nyckeln. Men du kan ställa in `IncludePackageName` flaggan på false enligt följande:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Anpassad nyckel mappning, värde extrahering och data ifyllning
Service Fabric-konfigurationsprovidern har också stöd för mer avancerade scenarier för anpassning av nyckel mappningen med `ExtractKeyFunc` och anpassad-extraherar värdena med `ExtractValueFunc` . Du kan till och med ändra hela processen för att fylla i data från Service Fabric konfiguration till ASP.NET Core konfiguration med hjälp av `ConfigAction` .

Följande exempel visar hur du använder `ConfigAction` för att anpassa data populationen:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Konfigurations uppdateringar
Service Fabric-konfigurationsprovidern stöder också konfigurations uppdateringar. Du kan använda ASP.NET Core `IOptionsMonitor` för att ta emot ändrings meddelanden och sedan använda `IOptionsSnapshot` för att läsa in konfigurations data på nytt. Mer information finns i [ASP.net Core alternativ](/aspnet/core/fundamentals/configuration/options).

De här alternativen stöds som standard. Ingen ytterligare kod krävs för att aktivera konfigurations uppdateringar.

## <a name="scenarios-and-configurations"></a>Scenarier och konfigurationer
Det här avsnittet innehåller en kombination av webb server, port konfiguration, Service Fabric integrations alternativ och diverse inställningar som vi rekommenderar för att felsöka följande scenarier:
 - Externt exponerade ASP.NET Core tillstånds lösa tjänster
 - Endast internt ASP.NET Core tillstånds lösa tjänster
 - Endast internt ASP.NET Core tillstånds känsliga tjänster

En **externt exponerad tjänst** är en som exponerar en slut punkt som anropas från utanför klustret, vanligt vis via en belastningsutjämnare.

En **intern** tjänst är en vars slut punkt endast anropas inifrån klustret.

> [!NOTE]
> Tillstånds känsliga tjänst slut punkter bör normalt inte exponeras för Internet. Kluster bakom belastningsutjämnare som inte är medvetna om Service Fabric tjänst matchning, till exempel Azure Load Balancer, kommer inte att kunna exponera tillstånds känsliga tjänster. Det beror på att belastningsutjämnaren inte kan hitta och dirigera trafik till lämplig tillstånds känslig tjänst replik. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externt exponerade ASP.NET Core tillstånds lösa tjänster
Kestrel är den föreslagna webb servern för klient dels tjänster som exponerar externa HTTP-slutpunkter mot Internet. I Windows kan HTTP.sys tillhandahålla funktioner för delning av portar, vilket gör att du kan vara värd för flera webb tjänster på samma uppsättning noder med samma port. I det här scenariot särskiljs webb tjänsterna efter värdnamn eller sökväg, utan att det förlitar sig på en frontend-proxy eller gateway för att tillhandahålla HTTP-routning.
 
När de exponeras för Internet bör en tillstånds lös tjänst använda en välkänd och stabil slut punkt som kan kommas åt via en belastningsutjämnare. Du får den här URL: en till ditt programs användare. Vi rekommenderar följande konfiguration:

| Typ | Rekommendation | Kommentarer |
| ---- | -------------- | ----- |
| Webbserver | Kestrel | Kestrel är den önskade webb servern eftersom den stöds i Windows och Linux. |
| Port konfiguration | statiskt | En välkänd statisk port bör konfigureras i `Endpoints` konfigurationen av ServiceManifest.xml, till exempel 80 för http eller 443 för https. |
| ServiceFabricIntegrationOptions | Inget | Använd `ServiceFabricIntegrationOptions.None` alternativet när du konfigurerar Service Fabric integration mellanprogram, så att tjänsten inte försöker verifiera inkommande begär Anden för en unik identifierare. Externa användare av programmet vet inte den unika identifierings information som används i mellanprogram. |
| Antal instanser | -1 | I vanliga användnings fall ska inställningen för instans antal anges till *-1*. Detta görs så att en instans är tillgänglig på alla noder som tar emot trafik från en belastningsutjämnare. |

Om flera externt exponerade tjänster delar samma uppsättning noder, kan du använda HTTP.sys med en unik men stabil URL-sökväg. Du kan göra detta genom att ändra den URL som angavs när du konfigurerade IWebHost. Observera att detta endast gäller för HTTP.sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Endast internt ASP.NET Core tjänst med tillstånds skydd
Tillstånds lösa tjänster som bara anropas från i klustret bör använda unika URL: er och dynamiskt tilldelade portar för att säkerställa samarbetet mellan flera tjänster. Vi rekommenderar följande konfiguration:

| Typ | Rekommendation | Kommentarer |
| ---- | -------------- | ----- |
| Webbserver | Kestrel | Även om du kan använda HTTP.sys för interna tillstånds lösa tjänster är Kestrel den bästa servern för att tillåta flera tjänst instanser att dela en värd.  |
| Port konfiguration | dynamiskt tilldelad | Flera repliker av en tillstånds känslig tjänst kan dela en värd process eller ett värd operativ system, vilket kräver att unika portar krävs. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Med dynamisk port tilldelning förhindrar den här inställningen förskriven identitets problem som beskrivits tidigare. |
| InstanceCount | valfri | Inställningen för instans antal kan ställas in på ett värde som krävs för att köra tjänsten. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Endast internt tillstånds känslig ASP.NET Core-tjänst
Tillstånds känsliga tjänster som bara anropas från i klustret bör använda dynamiskt tilldelade portar för att säkerställa samarbetet mellan flera tjänster. Vi rekommenderar följande konfiguration:

| Typ | Rekommendation | Kommentarer |
| ---- | -------------- | ----- |
| Webbserver | Kestrel | Är `HttpSysCommunicationListener` inte avsedd att användas av tillstånds känsliga tjänster där repliker delar en värd process. |
| Port konfiguration | dynamiskt tilldelad | Flera repliker av en tillstånds känslig tjänst kan dela en värd process eller ett värd operativ system, vilket kräver att unika portar krävs. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Med dynamisk port tilldelning förhindrar den här inställningen förskriven identitets problem som beskrivits tidigare. |

## <a name="next-steps"></a>Nästa steg
[Felsöka ditt Service Fabric-program med Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
