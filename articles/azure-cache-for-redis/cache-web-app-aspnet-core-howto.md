---
title: Skapa en ASP.NET Core webbapp med Azure cache för Redis
description: I den här snabb starten får du lära dig hur du skapar en ASP.NET Core webbapp med Azure cache för Redis
author: brendanzagaeski
ms.author: brzaga
ms.service: cache
ms.devlang: dotnet
ms.custom: devx-track-csharp, mvc
ms.topic: quickstart
ms.date: 03/31/2021
ms.openlocfilehash: 19c346bd3bdc0a5882244ff595bfeedbde8c06e4
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123633"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-core-web-app"></a>Snabb start: Använd Azure cache för Redis med en ASP.NET Core webbapp 

I den här snabb starten införlivar du Azure cache för Redis i ett ASP.NET Core-webbprogram som ansluter till Azure cache för Redis för att lagra och hämta data från cachen.

## <a name="skip-to-the-code-on-github"></a>Hoppa till koden på GitHub

Om du vill hoppa över direkt till koden går du till [ASP.net Core snabb start](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/aspnet-core) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-a-cache"></a>Skapa en cache

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Anteckna **VÄRDNAMN** och den **Primära** åtkomstnyckeln. Du använder dessa värden senare för att konstruera *CacheConnection*-hemligheten.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Öppna ett nytt kommando fönster och kör följande kommando för att skapa en ny ASP.NET Core-webbapp (modell-View-Controller):

```dotnetcli
dotnet new mvc -o ContosoTeamStats
```

I kommando fönstret ändrar du till den nya *ContosoTeamStats* -projekt katalogen.

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

Kör följande kommando för att lägga till paketet *Microsoft.Extensions.Configuration.UserSecrets* i projektet:

```dotnetcli
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

Kör följande kommando för att återställa dina paket:

```dotnetcli
dotnet restore
```

I ditt kommandofönster, kör du följande kommandon för att lagra en ny hemlighet med namnet *CacheConnection* efter att du ersätter platshållarna (inklusive hakparenteser) för ditt cachenamn och primära åtkomstnyckel:

```dotnetcli
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<primary-access-key>"
```

## <a name="configure-the-cache-client"></a>Konfigurera cacheklienten

I det här avsnittet ska du konfigurera programmet till att använda [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) -klienten för .net.

Kör följande kommando i *ContosoTeamStats* -projekt katalogen i kommando fönstret:

```dotnetcli
dotnet add package StackExchange.Redis
```

När installationen är klar är *StackExchange.Redis*-cacheklienten tillgänglig för användning med ditt projekt.

## <a name="update-the-homecontroller-and-layout"></a>Uppdatera HomeController och layout

Lägg till följande- `using` instruktioner i *Controllers\HomeController.cs*:

```csharp
using System.Net.Sockets;
using System.Text;
using System.Threading;

using Microsoft.Extensions.Configuration;

using StackExchange.Redis;
```

Ersätt:

```csharp
private readonly ILogger<HomeController> _logger;

public HomeController(ILogger<HomeController> logger)
{
    _logger = logger;
}
```

med:

```csharp
private readonly ILogger<HomeController> _logger;
private static IConfiguration Configuration { get; set; }

public HomeController(ILogger<HomeController> logger, IConfiguration configuration)
{
    _logger = logger;
    if (Configuration == null)
        Configuration = configuration;
}
```

Lägg till följande medlemmar i `HomeController` klassen för att stödja en ny `RedisCache` åtgärd som kör vissa kommandon mot den nya cachen.

```csharp
public ActionResult RedisCache()
{
    ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET Core.";

    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    ViewBag.command1 = "PING";
    ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

    // Simple get and put of integral data types into the cache
    ViewBag.command2 = "GET Message";
    ViewBag.command2Result = cache.StringGet("Message").ToString();

    ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET Core!\"";
    ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET Core!").ToString();

    // Demonstrate "SET Message" executed as expected...
    ViewBag.command4 = "GET Message";
    ViewBag.command4Result = cache.StringGet("Message").ToString();

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    ViewBag.command5 = "CLIENT LIST";
    StringBuilder sb = new StringBuilder();
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    sb.AppendLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        sb.AppendLine(client.Raw);
    }

    ViewBag.command5Result = sb.ToString();

    return View();
}

private const string SecretName = "CacheConnection";

private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

private static readonly object reconnectLock = new object();

// In general, let StackExchange.Redis handle most reconnects,
// so limit the frequency of how often ForceReconnect() will
// actually reconnect.
public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

// If errors continue for longer than the below threshold, then the
// multiplexer seems to not be reconnecting, so ForceReconnect() will
// re-create the multiplexer.
public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

public static int RetryMaxAttempts => 5;

private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}

private static Lazy<ConnectionMultiplexer> CreateConnection()
{
    return new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = Configuration[SecretName];
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}

private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
{
    if (oldConnection == null)
        return;

    try
    {
        oldConnection.Value.Close();
    }
    catch (Exception)
    {
        // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
    }
}

/// <summary>
/// Force a new ConnectionMultiplexer to be created.
/// NOTES:
///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
///     3. Call this method every time you see a connection exception. The code will:
///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
/// </summary>
public static void ForceReconnect()
{
    var utcNow = DateTimeOffset.UtcNow;
    long previousTicks = Interlocked.Read(ref lastReconnectTicks);
    var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
    TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

    // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
    if (elapsedSinceLastReconnect < ReconnectMinFrequency)
        return;

    lock (reconnectLock)
    {
        utcNow = DateTimeOffset.UtcNow;
        elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        if (firstErrorTime == DateTimeOffset.MinValue)
        {
            // We haven't seen an error since last reconnect, so set initial values.
            firstErrorTime = utcNow;
            previousErrorTime = utcNow;
            return;
        }

        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return; // Some other thread made it through the check and the lock, so nothing to do.

        TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
        TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

        bool shouldReconnect =
            elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
            && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

        // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
        previousErrorTime = utcNow;

        if (!shouldReconnect)
            return;

        firstErrorTime = DateTimeOffset.MinValue;
        previousErrorTime = DateTimeOffset.MinValue;

        Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
        CloseConnection(oldConnection);
        lazyConnection = CreateConnection();
        Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
    }
}

// In real applications, consider using a framework such as
// Polly to make it easier to customize the retry approach.
private static T BasicRetry<T>(Func<T> func)
{
    int reconnectRetry = 0;
    int disposedRetry = 0;

    while (true)
    {
        try
        {
            return func();
        }
        catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
        {
            reconnectRetry++;
            if (reconnectRetry > RetryMaxAttempts)
                throw;
            ForceReconnect();
        }
        catch (ObjectDisposedException)
        {
            disposedRetry++;
            if (disposedRetry > RetryMaxAttempts)
                throw;
        }
    }
}

public static IDatabase GetDatabase()
{
    return BasicRetry(() => Connection.GetDatabase());
}

public static System.Net.EndPoint[] GetEndPoints()
{
    return BasicRetry(() => Connection.GetEndPoints());
}

public static IServer GetServer(string host, int port)
{
    return BasicRetry(() => Connection.GetServer(host, port));
}
```

Öppna *Views\Shared \\ _Layout. cshtml*.

Ersätt:

```cshtml
<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">ContosoTeamStats</a>
```

med:

```cshtml
<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="RedisCache">Azure Cache for Redis Test</a>
```

## <a name="add-a-new-rediscache-view-and-update-the-styles"></a>Lägg till en ny RedisCache-vy och uppdatera formaten

Skapa en ny fil *Views\Home\RedisCache.cshtml* med följande innehåll:

```cshtml
@{
    ViewBag.Title = "Azure Cache for Redis Test";
}

<h2>@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3>
<br /><br />
<table border="1" cellpadding="10" class="redis-results">
    <tr>
        <th>Command</th>
        <th>Result</th>
    </tr>
    <tr>
        <td>@ViewBag.command1</td>
        <td><pre>@ViewBag.command1Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command2</td>
        <td><pre>@ViewBag.command2Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command3</td>
        <td><pre>@ViewBag.command3Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command4</td>
        <td><pre>@ViewBag.command4Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command5</td>
        <td><pre>@ViewBag.command5Result</pre></td>
    </tr>
</table>
```

Lägg till följande rader i *wwwroot\css\site.CSS*:

```css
.redis-results pre {
  white-space: pre-wrap;
}
```

## <a name="run-the-app-locally"></a>Köra appen lokalt

Kör följande kommando i ditt kommandofönster för att skapa appen:

```dotnetcli
dotnet build
```

Kör sedan appen med följande kommando:

```dotnetcli
dotnet run
```

Bläddra till `https://localhost:5001` i webbläsaren.

Välj **Azure cache för Redis-test** i navigerings fältet på webb sidan för att testa cache-åtkomst.

I exemplet nedan ser du att `Message`-nyckeln tidigare hade ett cachelagrat värde som angavs med Redis-konsolen i Azure Portal. Appen uppdatera det cachelagrade värdet. Appen körde även kommandona `PING` och `CLIENT LIST`.

![Enkelt test slutfört – lokalt](./media/cache-web-app-aspnet-core-howto/cache-simple-test-complete-local.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker fortsätta till nästa självstudie kan du behålla resurserna som du har skapat i den här självstudien och använda dem igen.

Om du är klar med exempelappen i snabbstarten kan du ta bort Azure-resurserna som du skapade i snabbstarten för att undvika kostnader.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. När du tar bort en resursgrupp tas alla resurser som ingår i den bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från deras respektive blad istället för att ta bort resursgruppen.

### <a name="to-delete-a-resource-group"></a>Ta bort en resursgrupp

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Resursgrupper**.

2. Skriv namnet på din resursgrupp i rutan **Filtrera efter namn...**. Anvisningarna för den här artikeln använde en resursgrupp med namnet *TestResources*. På din resursgrupp i resultatlistan väljer du **...** och sedan **Ta bort resursgrupp**.

    ![Ta bort](./media/cache-web-app-howto/cache-delete-resource-group.png)

Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp för att bekräfta och välj sedan **Ta bort**.

Efter en liten stund tas resursgruppen och de resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar till Azure finns i:

> [!div class="nextstepaction"]
> [Självstudie: Bygg en ASP.NET Core-och SQL Database-app i Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app)

Information om hur du lagrar hemligheten för cache-anslutningen i Azure Key Vault finns i:

> [!div class="nextstepaction"]
> [Azure Key Vault Konfigurationsprovider i ASP.NET Core](/aspnet/core/security/key-vault-configuration)

Vill du skala cacheminnet från en lägre nivå till en högre nivå?

> [!div class="nextstepaction"]
> [Så här skalar du Azure Cache for Redis](./cache-how-to-scale.md)

Vill du optimera och Spara på dina moln utgifter?

> [!div class="nextstepaction"]
> [Börja analysera kostnaderna med Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
