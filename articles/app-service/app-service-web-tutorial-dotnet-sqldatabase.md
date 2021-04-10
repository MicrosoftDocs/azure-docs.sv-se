---
title: 'Självstudie: ASP.NET-app med Azure SQL Database'
description: Lär dig hur du distribuerar en C# ASP.NET-app till Azure och Azure SQL Database
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 533bd817b704db9976624b356a9950a9c48b8339
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606027"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>Självstudie: Distribuera en ASP.NET-app till Azure med Azure SQL Database

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst. Den här självstudien visar hur du distribuerar en datadriven ASP.NET-app i App Service och ansluter den till [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). När du är klar har du en ASP.NET-app som körs i Azure och är ansluten till SQL Database.

![Publicerat ASP.NET-program i Azure App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en databas i Azure SQL Database
> * ansluta en ASP.NET-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma loggar från Azure till terminalen
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

Installera <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> med arbets belastningen **ASP.net och webb utveckling** .

Om du redan har installerat Visual Studio lägger du till arbets belastningarna i Visual Studio genom att klicka på **verktyg**  >  **Hämta verktyg och funktioner**.

## <a name="download-the-sample"></a>Ladda ned exemplet

1. [Ladda ned exempelprojektet](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

1. Extrahera (zippa upp)  *dotnet-sqldb-tutorial-master.zip* -filen.

Exempelprojektet innehåller en enkel [ASP.NET MVC](https://www.asp.net/mvc) CRUD-app (create-read-update-delete) som använder [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Kör appen

1. Öppna filen *dotnet-sqldb-kursen-master/DotNetAppSqlDb.sln* i Visual Studio.

1. Skriv `Ctrl+F5` för att köra appen utan felsökning. Appen visas i din standardwebbläsare.

1. Välj länken **Skapa nytt** och skapa några *att-göra*-objekt.

    ![Dialogrutan Nytt ASP.NET-projekt](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. Testa länkarna **Edit** (Redigera), **Details** (Information) och **Delete** (Ta bort).

Appen använder en databaskontext för att ansluta till databasen. I det här exemplet använder databaskontexten en anslutningssträng med namnet `MyDbConnection`. Anslutningssträngen har angetts i filen *Web.config* och refereras till i filen *Models/MyDatabaseContext.cs*. Anslutningssträngens namn används senare under självstudien för att ansluta Azure-appen till Azure SQL Database.

## <a name="publish-aspnet-application-to-azure"></a>Publicera ASP.NET-program till Azure

1. I **Solution Explorer**: Högerklicka på projektet **DotNetAppSqlDb** och välj **Publicera**.

    ![Publicera från Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. Välj **Azure** som mål och klicka på **Nästa**.

1. Kontrol lera att **Azure App Service (Windows)** är markerat och klicka på **Nästa**.

#### <a name="sign-in-and-add-an-app"></a>Logga in och Lägg till en app

1. Klicka på **Lägg till ett konto** från List rutan konto hanteraren i dialog rutan **publicera** .

1. Logga in på din Azure-prenumeration. Om du redan är inloggad på ett Microsoft-konto kontrollerar du att kontot tillhör din Azure-prenumeration. Om kontot inte tillhör din Azure-prenumeration klickar du på den för att lägga till rätt konto.

1. I fönstret **App Service instanser** klickar du på **+** .

    ![Logga in på Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>Konfigurera webbappnamnet

Du kan behålla det genererade webbappnamnet eller ändra det till ett annat unikt namn (giltiga tecken är `a-z`, `0-9` och `-`). Webbappnamnet används som en del av standard-URL:en för din app (`<app_name>.azurewebsites.net`, där `<app_name>` är webbappnamnet). Webbappnamnet måste vara unikt inom alla appar i Azure.

> [!NOTE]
> Välj inte **skapa** ännu.

![Dialogrutan Skapa App Service](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. Klicka på **Ny** bredvid **Resursgrupp**.

   ![Klicka på Ny bredvid Resursgrupp.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. Ge resursgruppen namnet **myResourceGroup**.

#### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Klicka på **ny** bredvid **värd plan**.

1. I dialog rutan **konfigurera App Service plan** konfigurerar du det nya app service plan med följande inställningar och klickar på **OK**:

   | Inställning  | Föreslaget värde | Mer information |
   | ----------------- | ------------ | ----|
   |**App Service-plan**| myAppServicePlan | [App Service-planer](../app-service/overview-hosting-plans.md) |
   |**Plats**| Europa, västra | [Azure-regioner](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**Storlek**| Kostnadsfri | [Prisnivåer](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![Skapa apptjänstplan](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. Klicka på **skapa** och vänta tills Azure-resurserna har skapats.

1. Dialog rutan **publicera** visar de resurser som du har konfigurerat. Klicka på **Finish**.

   ![de resurser du har skapat](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>Skapa en server och en databas

Innan du skapar en databas behöver du en [logisk SQL-Server](../azure-sql/database/logical-servers.md). En logisk SQL-Server är en logisk konstruktion som innehåller en grupp databaser som hanteras som en grupp.

1. Rulla ned till avsnittet **tjänst beroenden** i dialog rutan **publicera** . Klicka på **Konfigurera** bredvid **SQL Server databas**.

   ![Konfigurera SQL Database beroende](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. Välj **Azure SQL Database** och klicka på **Nästa**.

1. I dialog rutan **konfigurera Azure SQL Database** klickar du på **+** .

1. Klicka på **ny** bredvid **databas server**.

   Ett server namn genereras. Det här namnet används som en del av standard-URL: en för servern `<server_name>.database.windows.net` . Det måste vara unikt för alla servrar i Azure SQL. Du kan byta namn på servern, men behåll det genererade värdet för den här självstudiekursen.

1. Lägg till ett användarnamn och lösenord med administratörsbehörighet. För krav på lösenordskomplexitet, se [Lösenordsprincip](/sql/relational-databases/security/password-policy).

   Kom ihåg det här användarnamnet och lösenordet. Du behöver dem för att kunna hantera servern senare.

   ![Skapa server](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > Även om ditt lösenord maskeras i anslutningssträngar (i Visual Studio och i App Service) så ökar din apps riskexponering eftersom lösenordet är sparat någonstans. App Service kan använda [hanterade tjänstidentiteter](overview-managed-identity.md) till att eliminera den här risken, eftersom du inte behöver ha med några hemligheter alls i koden eller appkonfigurationen. Mer information finns under [Nästa steg](#next-steps).

1. Klicka på **OK**.

1. Behåll det genererade standard **databas namnet** i dialog rutan **Azure SQL Database** . Välj **skapa** och vänta tills databas resurserna har skapats.

    ![Konfigurera databas](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>Konfigurera databas anslutning

1. När du har skapat databas resurserna klickar du på **Nästa**.

1. I **databas anslutnings strängens namn** skriver du _MyDbConnection_. Det här namnet måste överensstämma med den anslutningssträng som refereras till i _Models/MyDatabaseContext.cs_.

1. I **användar namn** och **lösen ord** för databas anslutning anger du det administratörs användar namn och lösen ord som du använde i [skapa en server](#create-a-server-and-database).

1. Kontrol lera att **Azure app inställningar** är markerat och klicka på **Slutför**.

    ![Konfigurera databas anslutnings sträng](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. Vänta tills konfigurations guiden har slutförts och klicka på **Stäng**.

#### <a name="deploy-your-aspnet-app"></a>Distribuera din ASP.NET-app

1. På fliken **publicera** rullar du tillbaka uppåt till toppen och klickar på **publicera**. När din ASP.NET-app har distribuerats till Azure. Din standardwebbläsare startas med URL:en till den distribuerade appen.

1. Lägg till några att-göra-uppgifter.

    ![Publicerat ASP.NET-program i Azure app](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    Grattis! Din datadrivna ASP.NET-app körs live i Azure App Service.

## <a name="access-the-database-locally"></a>Få åtkomst till databasen lokalt

Med Visual Studio kan du enkelt utforska och hantera den nya databasen i Azure i **SQL Server Object Explorer**. Den nya databasen har redan öppnat sin brand vägg till den App Service app som du har skapat, men för att få åtkomst till den från din lokala dator (till exempel från Visual Studio) måste du öppna en brand vägg för den lokala datorns offentliga IP-adress. Om Internet leverantören ändrar din offentliga IP-adress måste du konfigurera om brand väggen för att få åtkomst till Azure-databasen igen.

#### <a name="create-a-database-connection"></a>Skapa en databasanslutning

1. Gå till **Visa**-menyn och välj **SQL Server Object Explorer**.

1. Klicka högst upp i **SQL Server Object Explorer** på knappen **Add SQL Server** (Lägg till SQL Server).

#### <a name="configure-the-database-connection"></a>Konfigurera databasanslutningen

1. Visa noden **Azure** i dialogrutan **Connect** (Anslut). Alla dina SQL Database-instanser i Azure visas här.

1. Välj den databas som du skapade tidigare. Den anslutning som du skapade tidigare fylls i automatiskt längst ned.

1. Ange databasadministratörens lösenord som du skapade tidigare och klicka på **Connect** (Anslut).

    ![Konfigurera databasanslutningen från Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>Tillåta klientanslutning från datorn

Dialogrutan **Create a new firewall rule** (Skapa en ny brandväggsregel) öppnas. Som standard tillåter en server bara anslutningar till databaserna från Azure-tjänster, till exempel din Azure-App. Om du vill ansluta till din databas utanför Azure skapar du en brand Väggs regel på server nivå. Brandväggsregeln tillåter din lokala dators offentliga IP-adress.

Dialogrutan är redan ifylld med datorns offentliga IP-adress.

1. Se till att **Add my client IP** (Lägg till min klient-IP) är markerat och klicka på **OK**.

    ![Skapa brandväggsregel](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    När Visual Studio har skapat brandväggsinställningen för SQL Database-instansen visas anslutningen i **SQL Server Object Explorer**.

    Här kan du utföra de vanligaste databasåtgärderna, till exempel köra frågor, skapa vyer och lagrade procedurer och mycket mer.

1. Expandera din anslutning > **databaser**  >  **&lt; databasen>**  >  **tabeller**. Högerklicka på tabellen `Todoes` och välj **Visa data**.

    ![Utforska SQL Database-objekt](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Uppdatera app med Code First Migrations

Du kan använda välbekanta verktyg i Visual Studio för att uppdatera din databas och app i Azure. I det här steget använder du Code First Migrations i Entity Framework för att göra en ändring i databasschemat och publicera det i Azure.

Mer information om att använda Entity Framework Code First Migrations finns i artikeln om att [komma igång med Entity Framework 6 Code First med MVC 5](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

#### <a name="update-your-data-model"></a>Uppdatera datamodellen

Öppna _Models\Todo.cs_ i kodredigeraren. Lägg till följande egenskap i klassen `ToDo`:

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>Kör Code First Migrations lokalt

Kör några kommandon och gör uppdateringar i den lokala databasen.

1. Gå till **Verktyg**-menyn och klicka på **NuGet Package Manager** > **Package Manager-konsolen**.

1. Aktivera Code First Migrations i fönstret Package Manager-konsolen:

    ```powershell
    Enable-Migrations
    ```
    
1. Lägg till en migrering:

    ```powershell
    Add-Migration AddProperty
    ```
    
1. Uppdatera den lokala databasen:

    ```powershell
    Update-Database
    ```
    
1. Skriv `Ctrl+F5` för att köra appen. Testa länkarna Edit (Redigera), Details (Information) och Create (Skapa).

Om programmet läses in utan fel har Code First Migrations slutförts. Dock ser sidan fortfarande likadan ut eftersom programlogiken ännu inte använder den nya egenskapen.

#### <a name="use-the-new-property"></a>Använda den nya egenskapen

Gör några ändringar i koden så att du använder egenskapen `Done`. För att göra självstudien enklare ska du bara ändra vyerna `Index` och `Create` så att du ser hur egenskapen fungerar.

1. Öppna _Controllers\TodosController.cs_.

1. Leta upp metoden `Create()` på rad 52 och lägg till `Done` i egenskapslistan i attributet `Bind`. När du är klar ser signaturen för metoden `Create()` ut som följande kod:

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. Öppna _Views\Todos\Create.cshtml_.

1. I Razor-koden bör du se ett `<div class="form-group">`-element som använder `model.Description` och sedan ett annat `<div class="form-group">`-element som använder `model.CreatedDate`. Direkt efter dessa två element ska du lägga till ett annat `<div class="form-group">`-element som använder `model.Done`:

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. Öppna _Views\Todos\Index.cshtml_.

1. Sök efter det tomma `<th></th>`-elementet. Lägg till följande Razor-kod direkt ovanför det här elementet:

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. Leta upp `<td>`-elementet som innehåller hjälpmetoderna `Html.ActionLink()`. _Ovanför_ detta `<td>` ska du lägga till ett `<td>`-element med följande Razor-kod:

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    Det är allt som krävs för att du ska se ändringarna i vyerna `Index` och `Create`.

1. Skriv `Ctrl+F5` för att köra appen.

Du kan nu lägga till en att-göra-uppgift och markera **Klart**. Den ska sedan visas på din startsida som en slutförd uppgift. Kom ihåg att vyn `Edit` inte innehåller fältet `Done` eftersom du inte ändrade vyn `Edit`.

#### <a name="enable-code-first-migrations-in-azure"></a>Aktivera Code First Migrations i Azure

Nu när kodändringen fungerar, inklusive databasmigreringen, publicerar du den i din Azure-app och uppdaterar även din SQL Database med Code First Migrations.

1. Precis som tidigare högerklickar du på projektet och väljer **Publish** (Publicera).

1. Klicka på **fler åtgärder**  >  **Redigera** för att öppna publicerings inställningarna.

    ![Öppna publiceringsinställningar](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. I list rutan **MyDatabaseContext** väljer du databas anslutningen för din Azure SQL Database.

1. Välj **Execute Code First Migrations (runs on application start)** (Utför Code First Migrations (körs när programmet startar)) och klicka på **Save** (Spara).

    ![Aktivera Code First Migrations i Azure-app](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>Publicera dina ändringar

Nu när du har aktiverat Code First Migrations i Azure-appen kan du publicera kodändringarna.

1. Klicka på **Publicera** på publiceringssidan.

1. Försök att lägga till uppgifter att göra igen och välj **Done** (Klar). De ska då visas på din startsida som en slutförd punkt.

    ![Azure-app efter Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Alla befintliga att-göra-uppgifter visas fortfarande. När du återpublicerar ASP.NET-appen går inte befintliga data i SQL Database förlorade. Code First Migrations ändrar dessutom endast dataschemat, så att befintliga data lämnas intakta.

## <a name="stream-application-logs"></a>Strömma programloggar

Du kan strömma spårningsmeddelanden direkt från din Azure-app till Visual Studio.

Öppna _Controllers\TodosController.cs_.

Varje åtgärd börjar med en `Trace.WriteLine()`-metod. Den här koden har lagts till för att visa hur du lägger till spårningsmeddelanden i din Azure-app.

#### <a name="enable-log-streaming"></a>Aktivera loggströmning

1. I menyn **Visa** väljer du **Cloud Explorer**.

1. I **Cloud Explorer** expanderar du Azure-prenumerationen som har din app och expanderar **App Service**.

1. Högerklicka på din Azure-app och markera **View Streaming Logs** (Visa strömningsloggar).

    ![Aktivera loggströmning](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    Loggarna strömmas nu till fönstret **Output** (Utdata).

    ![Loggströmning i utdatafönstret](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    Men än ser du inga spårningsmeddelanden. Det beror på att när du väljer **View Streaming Logs** (Visa strömningsloggar) anges spårningsnivån i Azure-appen till `Error`, då endast felhändelser loggas (med `Trace.TraceError()`-metoden).

#### <a name="change-trace-levels"></a>Ändra spårningsnivåer

1. Om du vill ändra spårnings nivåer för att mata ut andra spårnings meddelanden går du tillbaka till **Cloud Explorer**.

1. Högerklicka på din app igen och välj **Öppna i portalen**.

1. På sidan Portal hantering för din app väljer du **App Service loggar** på den vänstra menyn.

1. Under **program loggning (fil system)** väljer du **utförlig** på **nivå**. Klicka på **Spara**.

    ![Ändra spårningsnivån till utförlig](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > Du kan experimentera med olika spårningsnivåer och se vilka typer av meddelanden som visas för varje nivå. Till exempel innehåller **informations** nivån alla loggar som skapats av `Trace.TraceInformation()` , `Trace.TraceWarning()` och `Trace.TraceError()` , men inte loggar som skapats av `Trace.WriteLine()` .

1. Gå till webbläsaren och navigera till appen igen på *http://&lt;appens namn >. azurewebsites.net*. Försök klicka i programmet med att göra-listan i Azure. Spårningsmeddelanden strömmas nu till fönstret **Output** (Utdata) i Visual Studio.

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>Stoppa loggströmning

Om du vill avsluta loggströmningstjänsten klickar du på knappen **Stop monitoring** (Stoppa övervakning) i fönstret **Output** (Utdata).

![Stoppa loggströmning](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Hantera din Azure-app

Gå till [Azure Portal](https://portal.azure.com) för att hantera webbappen. Sök efter och välj **app Services**.

![Sök efter Azure App tjänster](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Välj namnet på din Azure-App.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Du har landat på appens sida.

Som standard visar portalen sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Skapa en databas i Azure SQL Database
> * ansluta en ASP.NET-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma loggar från Azure till terminalen
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig hur du enkelt kan förbättra säkerheten för din Azure SQL Database-anslutning.

> [!div class="nextstepaction"]
> [Säker åtkomst till SQL-databaser med hanterade identiteter för Azure-resurser](app-service-web-tutorial-connect-msi.md)

Fler resurser:

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET-app](configure-language-dotnet-framework.md)

Vill du optimera och Spara på dina moln utgifter?

> [!div class="nextstepaction"]
> [Börja analysera kostnaderna med Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)