---
title: 'Självstudie: ASP.NET Core med Azure SQL Database'
description: Lär dig hur du får en .NET Core-app att Azure App Service med anslutning till en Azure SQL Database.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp, mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 52a5b127312ef979791d17b27ca67b21a779e310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765767"
---
# <a name="tutorial-build-an-aspnet-core-and-azure-sql-database-app-in-azure-app-service"></a>Självstudie: Skapa en ASP.NET Core- Azure SQL Database-app i Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) en mycket skalbar och självkorrigeringsbar webbvärdtjänst i Azure. Den här självstudien visar hur du skapar en .NET Core-app och ansluter den till SQL Database. När du är klar har du en .NET Core MVC-app som körs i App Service i Windows.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) en mycket skalbar och självkorrigeringstjänst för webbvärdtjänster med Linux-operativsystemet. Den här självstudiekursen visar hur du skapar en .NET Core-app och ansluter den till en SQL Database. När du är färdig har du en .NET Core MVC-app som körs i App Service i Linux.

::: zone-end

![app som körs i App Service](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapa en SQL Database i Azure
> * ansluta en .NET Core-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- <a href="https://git-scm.com/" target="_blank">Installera Git</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installera den senaste .NET Core 3.1 SDK</a>

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Skapa en lokal .NET Core-app

I det här steget konfigurerar du det lokala .NET Core-projektet.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.

Kör följande kommandon för att klona exempellagringsplatsen och ändra dess rot.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Exempelprojektet innehåller en grundläggande CRUD-app (create-read-update-delete) med [Entity Framework Core](/ef/core/).

### <a name="run-the-application"></a>Kör programmet

Kör följande kommandon för att installera de nödvändiga paketen, köra databasmigreringar och starta programmet.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

Gå till `http://localhost:5000` i en webbläsare. Välj länken **Skapa nytt** och skapa några _att-göra_-objekt.

![ansluter till SQL Database](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Du kan när som helst stoppa .NET Core genom att trycka på `Ctrl+C` i terminalen.

## <a name="create-production-sql-database"></a>Skapa SQL Database för produktion

I det här steget skapar du en SQL Database i Azure. När appen har distribuerats till Azure används den här molndatabasen.

För SQL Database används [Azure SQL Database](/azure/sql-database/) i den här självstudien.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Skapa en logisk SQL Database-server

I Cloud Shell du en SQL Database logisk server med [`az sql server create`](/cli/azure/sql/server#az_sql_server_create) kommandot .

Ersätt *\<server-name>* platshållaren med ett *unikt SQL Database* namn. Det här namnet används som en del av den globalt unika SQL Database slutpunkten, `<server-name>.database.windows.net` . Giltiga tecken är `a` - `z` , `0` - `9` , `-` . Ersätt också och *\<db-username>* med ett användarnamn och lösenord som du själv *\<db-password>* väljer. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

När den logiska SQL Database-servern har skapats visar Azure CLI information som liknar följande exempel:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [brandväggsregel på servernivå för Azure SQL Database](../azure-sql/database/firewall-configure.md) via kommandot[`az sql server firewall create`](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create). När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](overview-inbound-outbound-ips.md#find-outbound-ips).
>

I den Cloud Shell du kommandot igen för att tillåta åtkomst från din lokala dator genom att ersätta med *\<your-ip-address>* [din lokala IPv4 IP-adress](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>Skapa en databas

Skapa en databas med en [S0-prestandanivå](../azure-sql/database/service-tiers-dtu.md) på servern med kommandot [`az sql db create`](/cli/azure/sql/db#az_sql_db_create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Skapa anslutningssträng

Hämta anslutningssträngen med [`az sql db show-connection-string`](/cli/azure/sql/db#az_sql_db_show_connection_string) kommandot .

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

I kommandoutdata ersätter du *\<username>* och med *\<password>* de autentiseringsuppgifter för databasadministratör som du använde tidigare.

Detta är anslutningssträngen för .NET Core-appen. Kopiera den för senare bruk.

### <a name="configure-app-to-connect-to-production-database"></a>Konfigurera appen för att ansluta till produktionsdatabasen

Öppna Startup.cs från din lokala lagringsplats och leta upp följande kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Ersätt den med följande kod.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> För produktionsappar som behöver skala ut följer du bästa praxis i avsnittet om att [tillämpa migreringar i produktion](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

### <a name="run-database-migrations-to-the-production-database"></a>Köra databasmigrering till produktionsdatabasen

Din app ansluter för närvarande till en lokal Sqlite-databas. Nu när du har konfigurerat en Azure SQL Database återskapar du den första migreringen för att rikta in dig på den. 

Kör följande kommandon från lagringsplatsens rot. Ersätt *\<connection-string>* med anslutningssträngen som du skapade tidigare.

```
# Delete old migrations
rm -r Migrations
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Köra appen med ny konfiguration

Nu när databasmigreringarna körs på produktionsdatabasen testar du appen genom att köra:

```
dotnet run
```

Gå till `http://localhost:5000` i en webbläsare. Välj länken **Skapa nytt** och skapa några _att-göra_-objekt. Din app läser och skriver nu data till produktionsdatabasen.

Genomför dina lokala ändringar och spara dem sedan på Git-lagringsplatsen. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

Nu är du redo att distribuera koden.

## <a name="deploy-app-to-azure"></a>Distribuera app till Azure

I det här steget distribuerar du din SQL Database-anslutna .NET Core-app till App Service.

### <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

### <a name="create-a-web-app"></a>Skapa en webbapp

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

::: zone-end

### <a name="configure-connection-string"></a>Konfigurera anslutningssträng

Om du vill ange anslutningssträngar för din Azure-app [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) använder du kommandot i Cloud Shell. I följande kommando ersätter du *\<app-name>* , samt parametern med *\<connection-string>* anslutningssträngen som du skapade tidigare.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

I ASP.NET Core kan du använda den här namngivna anslutningssträngen ( ) med hjälp av standardmönstret, precis som alla anslutningssträngar `MyDbConnection` som anges iappsettings.js *på*. I det här fallet `MyDbConnection` definieras också i *appsettings.jspå*. När du kör i App Service har anslutningssträngen som definierats i App Service företräde framför den anslutningssträng som definierats *iappsettings.jspå*. Koden använderappsettings.js *värdet* under lokal utveckling, och samma kod använder App Service när den distribueras.

Information om hur anslutningssträngen refereras i koden finns i [Konfigurera appen för att ansluta till produktionsdatabasen](#configure-app-to-connect-to-production-database).

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

::: zone pivot="platform-windows"  

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 273, done.
Counting objects: 100% (273/273), done.
Delta compression using up to 4 threads
Compressing objects: 100% (175/175), done.
Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
Total 273 (delta 96), reused 259 (delta 88)
remote: Resolving deltas: 100% (96/96), done.
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cccecf86c5'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Done.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till den distribuerade appen i webbläsaren.

```bash
http://<app-name>.azurewebsites.net
```

Lägg till några att-göra-uppgifter.

![app som körs i App Service](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Grattis!** Du kör en datadriven .NET Core-app i App Service.

## <a name="update-locally-and-redeploy"></a>Uppdatera lokalt och omdistribuera

I det här steget gör du en ändring i ditt databasschema och publicerar den till Azure.

### <a name="update-your-data-model"></a>Uppdatera datamodellen

Öppna _Models/Todo.cs_ i kodredigeraren. Lägg till följande egenskap i klassen `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Köra databasmigrering igen

Kör några kommandon för att göra uppdateringar av produktionsdatabasen.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> Om du öppnar ett nytt terminalfönster måste du ange anslutningssträngen till produktionsdatabasen i terminalen, som du gjorde i [Köra databasmigrering till produktionsdatabasen](#run-database-migrations-to-the-production-database).
>

### <a name="use-the-new-property"></a>Använda den nya egenskapen

Gör några ändringar i koden så att du använder egenskapen `Done`. För att göra självstudien enklare ska du bara ändra vyerna `Index` och `Create` så att du ser hur egenskapen fungerar.

Öppna _Controllers/TodosController.cs_.

Leta rätt på metoden `Create([Bind("ID,Description,CreatedDate")] Todo todo)` och lägg till `Done` i listan med egenskaper för attributet `Bind`. När du är klar ser signaturen för metoden `Create()` ut som följande kod:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Öppna _Views/Todos/Create.cshtml_.

I Razor-koden bör du se ett `<div class="form-group">`-element för `Description` och sedan ett annat `<div class="form-group">`-element för `CreatedDate`. Direkt efter dessa två element ska du lägga till ett annat `<div class="form-group">`-element för `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Öppna _Views/Todos/Index.cshtml_.

Sök efter det tomma `<th></th>`-elementet. Lägg till följande Razor-kod direkt ovanför det här elementet:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Hitta elementet `<td>` som innehåller ”tag helpers” `asp-action`. Lägg till följande Razor-kod direkt ovanför det här elementet:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Det är allt som krävs för att du ska se ändringarna i vyerna `Index` och `Create`.

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

Kör appen lokalt.

```bash
dotnet run
```

> [!NOTE]
> Om du öppnar ett nytt terminalfönster måste du ange anslutningssträngen till produktionsdatabasen i terminalen, som du gjorde i Köra databasmigrering till [produktionsdatabasen](#run-database-migrations-to-the-production-database).
>

Öppna webbläsaren och navigera till `http://localhost:5000/`. Du kan nu lägga till en att-göra-uppgift och markera **Klart**. Den ska sedan visas på din startsida som en slutförd uppgift. Kom ihåg att vyn `Edit` inte innehåller fältet `Done` eftersom du inte ändrade vyn `Edit`.

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

```bash
git add .
git commit -m "added done field"
git push azure main
```

När är `git push` klar går du till din App Service och provar att lägga till ett att göra-objekt och markerar **Klar.**

![Azure-app efter Code First Migration](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Alla befintliga att-göra-uppgifter visas fortfarande. När du publicerar din ASP.NET Core-app går inte befintliga data SQL Database bort. Med Entity Framework Core Migrations ändras endast dataschemat, så att befintliga data lämnas intakta.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

När ASP.NET Core-appen körs i Azure App Service kan du skicka konsolloggarna till Cloud Shell. På så sätt kan du få samma diagnostikmeddelanden för att felsöka programfel.

Exempelprojektet följer redan riktlinjerna i [ASP.NET Core-loggning i Azure](/aspnet/core/fundamentals/logging#azure-app-service-provider) med två konfigurationsändringar:

- Innehåller en referens till `Microsoft.Extensions.Logging.AzureAppServices` i *DotNetCoreSqlDb.csproj*.
- Anropar `loggerFactory.AddAzureWebAppDiagnostics()` *i Program.cs*.

För att ange [loggnivå](/aspnet/core/fundamentals/logging#log-level) för ASP.NET Core i App Service till `Information` från standardnivån `Error`använder du kommandot [`az webapp log config`](/cli/azure/webapp/log#az_webapp_log_config) i Cloud Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging filesystem --level information
```

> [!NOTE]
> Projektets loggnivå är redan inställd på `Information` i *appsettings.json*.

Starta loggströmningen med kommandot [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) i Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Uppdatera Azure-app i webbläsaren så hämtas webbtrafik när loggströmningen har startats. Du kan nu se konsolloggarna som skickas till terminalen. Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

Skriv `Ctrl`+`C` när som helst för att stoppa loggströmningen.

Mer information om att anpassa ASP.NET Core-loggar finns i [Loggning i ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Hantera din Azure-app

Om du vill se den app som du [skapade Azure Portal](https://portal.azure.com)du efter och väljer **App Services**.

![Välj App Services i Azure Portal](./media/tutorial-dotnetcore-sqldb-app/app-services.png)

På **App Services** väljer du namnet på din Azure-app.

![Portalnavigering till Azure-app](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * skapa en SQL Database i Azure
> * ansluta en .NET Core-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma loggar från Azure till terminalen
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till appen.

> [!div class="nextstepaction"]
> [Självstudie: Mappa anpassat DNS-namn till din app](app-service-web-tutorial-custom-domain.md)

Eller så kan du kolla in andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core-app](configure-language-dotnetcore.md)
