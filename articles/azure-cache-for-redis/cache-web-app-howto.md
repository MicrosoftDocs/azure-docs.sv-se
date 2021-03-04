---
title: Skapa en ASP.NET-webbapp med Azure cache för Redis
description: I den här snabbstarten lär du dig hur du skapar en ASP.NET-webbapp med Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: yegu
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 342125da35868b2b0f71609c4114cc561821eb1a
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121141"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-web-app"></a>Snabb start: Använd Azure cache för Redis med en ASP.NET-webbapp 

I den här snabb starten använder du Visual Studio 2019 för att skapa ett ASP.NET-webbprogram som ansluter till Azure cache för Redis för att lagra och hämta data från cachen. Sedan distribuerar du appen till Azure App Service.

## <a name="skip-to-the-code-on-github"></a>Hoppa till koden på GitHub

Om du vill hoppa över direkt till koden går du till snabb starten för [ASP.net](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/aspnet) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) med arbets belastningarna **ASP.net och webb utveckling** och **Azure-utveckling** .

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

1. Öppna Visual Studio och välj sedan **Arkiv**  > **nytt**  >  **projekt**.

2. Gör följande i dialogrutan **Nytt projekt**:

    ![Skapa projekt](./media/cache-web-app-howto/cache-create-project.png)

    a. Expandera noden **Visual C#** i listan **Mallar**.

    b. Välj **moln**.

    c. Välj **ASP.NET-webbapp**.

    d. Kontrollera att **.NET Framework 4.5.2** eller senare har valts.

    e. Ge projektet ett namn i rutan **Namn**. I det här exemplet använde vi **ContosoTeamStats**.

    f. Välj **OK**.
   
3. Välj **MVC** som projekttyp.

4. Kontrollera att **Ingen autentisering** är angivet i **autentiseringsinställningarna**. Beroende på din version av Visual Studio kan det vara en annan standardinställning för **Autentisering**. För att ändra detta väljer du **Ändra autentisering** och sedan **Ingen autentisering**.

5. Klicka på **OK** för att skapa projektet.

## <a name="create-a-cache"></a>Skapa en cache

Nu ska skapa du cachen för appen.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

#### <a name="to-edit-the-cachesecretsconfig-file"></a>Redigera filen *CacheSecrets.config*

1. Skapa en fil på din dator med namnet *CacheSecrets.config*. Lägg till den på en plats där den inte checkas in med käll koden för ditt exempel program. För den här snabbstarten finns filen *CacheSecrets.config* i *C:\AppSecrets\CacheSecrets.config*.

1. Redigera filen *CacheSecrets.config*. Lägg sedan till följande innehåll:

    ```xml
    <appSettings>
        <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
    </appSettings>
    ```

1. Ersätt `<cache-name>` med din cachens värdnamn.

1. Ersätt `<access-key>` med primärnyckeln för cachen.

    > [!TIP]
    > Du kan använda den sekundära åtkomstnyckeln under nyckelrotation som en alternativ nyckel medan du återskapar den primära åtkomstnyckeln.
   >
1. Spara filen.

## <a name="update-the-mvc-application"></a>Uppdatera MVC-appen

I det här avsnittet uppdaterar du programet så att det stöder en ny vy som visar ett enkelt test mot Azure Cache for Redis.

* [Uppdatera web.config-filen med en appinställning för cachen](#update-the-webconfig-file-with-an-app-setting-for-the-cache)
* Konfigurera appen till att använda StackExchange.Redis-klienten
* Uppdatera HomeController och layout
* Lägga till en ny RedisCache-vy

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Uppdatera web.config-filen med en appinställning för cachen

När du kör appen lokalt används informationen i *CacheSecrets.config* för att ansluta till Azure Cache for Redis-instansen. Du kommer senare att distribuera appen till Azure. Då konfigurerar du en appinställning i Azure som appen kommer att använda för att hämta cachens anslutningsinformation i stället för den här filen. 

Eftersom *CacheSecrets.config* inte har distribuerats till Azure med din app använder du den bara när du testar appen lokalt. Var noga med att skydda informationen så att inte obehöriga kan komma åt cachelagrade data.

#### <a name="to-update-the-webconfig-file"></a>Uppdatera filen *web.config*
1. I **Solution Explorer** dubbelklickar du på *web.config* för att öppna den.

    ![Web.config](./media/cache-web-app-howto/cache-web-config.png)

2. Leta reda på elementet `<appSetting>` i filen *web.config*. Lägg sedan till följande `file`-attribut. Om du använder ett annat namn eller en annan plats, byter du ut dessa värden mot de som visas i exemplet.

* Innan: `<appSettings>`
* När  `<appSettings file="C:\AppSecrets\CacheSecrets.config">`

ASP.NET-körningsmiljön sammanfogar innehållet i den externa filen med markeringen i `<appSettings>`-elementet. Vid körningen ignoreras filattributet om det inte går att hitta den angivna filen. Din hemliga information (anslutningssträngen till cachen) ingår inte i källkoden för programmet. När du distribuerar din webbapp till Azure så distribueras inte filen *CacheSecrets.config*.

### <a name="to-configure-the-application-to-use-stackexchangeredis"></a>Konfigurera appen till att använda StackExchange.Redis

1. Konfigurera appen att använda NuGet-paketet i [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) för Visual Studio genom att välja **Verktyg > NuGet Package Manager > Package Manager-konsolen**.

2. Kör följande kommando från fönstret `Package Manager Console`:

    ```powershell
    Install-Package StackExchange.Redis
    ```

3. NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenserna för klientprogrammet för att få åtkomst till Azure Cache for Redis med cacheklienten StackExchange.Azure Cache for Redis. Om du vill använda en starkt krypterad version av `StackExchange.Redis` klientbiblioteket ska du installera paketet `StackExchange.Redis.StrongName`.

### <a name="to-update-the-homecontroller-and-layout"></a>Uppdatera HomeController och layout

1. I **Solution Explorer** expanderar du mappen **Controllers** och öppnar filen *HomeController.cs*.

2. Lägg till följande två `using`-instruktioner överst i filen för att stödja cacheklienten och appinställningarna.

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

3. Lägg till följande metod i klassen `HomeController` så att du kan använda en ny `RedisCache`-åtgärd som kör vissa kommandon mot den nya cachen.

    ```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET.";
            
        IDatabase cache = Connection.GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demonstrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        ViewBag.command5 = "CLIENT LIST";
        StringBuilder sb = new StringBuilder();

        var endpoint = (System.Net.DnsEndPoint)Connection.GetEndPoints()[0];
        var server = Connection.GetServer(endpoint.Host, endpoint.Port);
        var clients = server.ClientList();

        sb.AppendLine("Cache response :");
        foreach (var client in clients)
        {
            sb.AppendLine(client.Raw);
        }

        ViewBag.command5Result = sb.ToString();

        return View();
    }
                
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

    ```

4. I **Solution Explorer** expanderar du mappen **Vyer** > **Delad**. Öppna sedan filen *_Layout.cshtml*.

    Ersätt:
    
    ```csharp
    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    med:

    ```csharp
    @Html.ActionLink("Azure Cache for Redis Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

### <a name="to-add-a-new-rediscache-view"></a>Lägga till en ny RedisCache-vy

1. I **Solution Explorer** expanderar du mappen **Vyer** och högerklickar sedan på mappen **Start**. Välj **Lägg till**  >  **vy...**.

2. Ange **RedisCache** som vynamn i dialogrutan **Lägg till vy**. Välj **Lägg till**.

3. Ersätt koden i filen *RedisCache.cshtml* med följande kod:

    ```csharp
    @{
        ViewBag.Title = "Azure Cache for Redis Test";
    }

    <h2>@ViewBag.Title.</h2>
    <h3>@ViewBag.Message</h3>
    <br /><br />
    <table border="1" cellpadding="10">
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

## <a name="run-the-app-locally"></a>Köra appen lokalt

Som standard är projektet konfigurerat att vara värd för appen lokalt i [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) för testning och fel sökning.

### <a name="to-run-the-app-locally"></a>Köra appen lokalt
1. I Visual Studio väljer du **Felsök**  >  **Starta fel sökning** för att skapa och starta appen lokalt för testning och fel sökning.

2. Välj **Azure Redis Cache for Redis-test** i webbläsarens navigeringsfält.

3. I exemplet nedan ser du att `Message`-nyckeln tidigare hade ett cachelagrat värde som angavs med Azure Cache for Redis-konsolen i portalen. Appen uppdatera det cachelagrade värdet. Appen körde även kommandona `PING` och `CLIENT LIST`.

    ![Enkelt test slutfört – lokalt](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Publicera och köra i Azure

När du har testat appen lokalt distribuerar du den till Azure och kör den i molnet.

### <a name="to-publish-the-app-to-azure"></a>Publicera appen i Azure

1. Högerklicka på projektnoden i Solution Explorer i Visual Studio. Välj sedan **Publicera**.

    ![Publicera](./media/cache-web-app-howto/cache-publish-app.png)

2. Välj **Microsoft Azure App Service**, välj **Skapa ny** och välj sedan **Publicera**.

    ![Publicera i App Service](./media/cache-web-app-howto/cache-publish-to-app-service.png)

3. Gör följande ändringar i dialogrutan **Skapa App Service**:

    | Inställningen | Rekommenderat värde | Beskrivning |
    | ------- | :---------------: | ----------- |
    | **Appens namn** | Använd standardvärdet. | Appnamnet blir värdnamnet för appen när den har distribuerats till Azure. Namnet kan ha ett tidsstämpelsuffix som lagts till för att göra det unikt, om det behövs. |
    | **Prenumeration** | Välj din Azure-prenumeration. | Den här prenumerationen debiteras för eventuella relaterade värdkostnader. Om du har flera Azure-prenumerationer kontrollerar du att den önskade prenumerationen har valts.|
    | **Resursgrupp** | Använd den resursgrupp som du skapade cachen i (till exempel *TestResourceGroup*). | Resursgruppen hjälper dig att hantera alla resurser som en grupp. Senare när du vill ta bort appen är det bara att ta bort gruppen. |
    | **App Service-plan** | Välj **Nytt** och skapa en ny App Service-Plan med namnet *TestingPlan*. <br />Använd samma **plats** du använde när du skapade cachen. <br />Välj **Ledigt** som storlek. | En App Service-plan definierar en uppsättning beräkningsresurser för en webbapp att köra med. |

    ![Dialogrutan App Service](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

4. Välj **Skapa** när du har konfigurerat App Service-värdinställningarna.

5. I fönstret **Utdata** i Visual Studio kan du se publiceringsstatus. När appen har publicerats loggas URL:en för appen:

    ![Publicera utdata](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Lägga till appinställningen för cachen

Lägg till en ny appinställning när den nya appen har publicerats. Den här inställningen används för att lagra cacheanslutningsinformationen. 

#### <a name="to-add-the-app-setting"></a>Lägga till appinställningen 

1. Skriv appnamnet i sökfältet längst upp i Azure-portalen för att söka efter den nya appen som du skapade.

    ![Leta efter appen](./media/cache-web-app-howto/cache-find-app-service.png)

2. Lägg till en ny appinställning med namnet **CacheConnection** som appen ska använda för att ansluta till cachen. Använd samma värde som du har konfigurerat för `CacheConnection` i filen *CacheSecrets.config*. Värdet innehåller cachens värdnamn och åtkomstnyckel.

    ![Lägga till appinställning](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Köra appen i Azure

Gå till URL:en för appen i webbläsaren. URL:en visas i resultatet av publiceringsåtgärden i utdatafönstret i Visual Studio. Den finns också i Azure-portalen på översiktssidan för appen du skapade.

Testa cacheåtkomsten genom att välja **Azure Cache for Redis-test** i navigeringsfältet.

![Enkelt test slutfört – Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

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

I nästa kurs får du använda Azure Cache for Redis i ett mer realistiskt scenario så att du kan förbättra prestandan för en app. Du uppdaterar appen på cachens resultattavla med hjälp av cache-aside-mönstret med ASP.NET och en databas.

> [!div class="nextstepaction"]
> [Skapa en cache-aside-resultattavla för ASP.NET](cache-web-app-cache-aside-leaderboard.md)