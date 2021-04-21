---
title: 'Självstudie: Komma åt data med hanterad identitet'
description: Lär dig hur du gör databasanslutningar säkrare med hjälp av en hanterad identitet och hur du tillämpar den på andra Azure-tjänster.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 0012c3d208998786ef5aa34320f3bccc4e51ebe6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782791"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Självstudie: Säkra Azure SQL Database-anslutningar från App Service med en hanterad identitet

Med [App Service ](overview.md) får du en automatiskt uppdaterad webbvärdtjänst i Azure med hög skalbarhet. Dessutom får du en [hanterad identitet](overview-managed-identity.md) för din app. Det här är en användningsklar lösning som skyddar åtkomsten till [Azure SQL Database](/azure/sql-database/) och andra Azure-tjänster. Med hanterade identiteter i App Service blir dina appar säkrare eftersom du inte har några hemligheter i dina appar. Du har till exempel inga inloggningsuppgifter i anslutningssträngarna. I den här självstudien lägger du till en hanterad identitet i exempelwebbappen som du skapade i någon av följande självstudier: 

- [Självstudie: Skapa en ASP.NET-app i Azure med Azure SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Självstudie: Skapa en ASP.NET Core- Azure SQL Database-app i Azure App Service](tutorial-dotnetcore-sqldb-app.md)

När du är färdig ansluter exempelappen säkert till SQL Database utan att du behöver använda användarnamn och lösenord.

> [!NOTE]
> Stegen i den här självstudien stöder följande versioner:
> 
> - .NET Framework 4.7.2 och högre
> - .NET Core 2.2 och högre
>

Det här får du lära dig:

> [!div class="checklist"]
> * Aktivera hanterade identiteter
> * Ge SQL Database åtkomst till den hanterade identiteten
> * Konfigurera Entity Framework att använda Azure AD-autentisering med SQL Database
> * Ansluta till SQL Database från Visual Studio med Azure AD-autentisering

> [!NOTE]
>Azure AD-autentisering _skiljer sig_ [från integrerad Windows-autentisering](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) i lokal Active Directory (AD DS). AD DS och Azure AD använder helt olika autentiseringsprotokoll. Mer information finns i [Azure AD Domain Services-dokumentationen](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln fortsätter där du slutade i [Självstudie: Skapa en ASP.NET-app](app-service-web-tutorial-dotnet-sqldatabase.md) i Azure med SQL Database eller Självstudie: Skapa en [ASP.NET Core- och SQL Database-app i Azure App Service](tutorial-dotnetcore-sqldb-app.md). Om du inte redan har gjort det följer du en av de två självstudierna först. Du kan också anpassa stegen för din egen .NET-app med SQL Database.

Om du vill felsöka din app SQL Database som backend-klient ser du till att du har tillåtit klientanslutningen från datorn. Om inte lägger du till klientens IP-adress genom att följa stegen i Hantera [IP-brandväggsregler](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)på servernivå med hjälp av Azure Portal .

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Bevilja databasåtkomst till Azure AD-användare

Aktivera först Azure AD-SQL Database genom att tilldela en Azure AD-användare som Active Directory-administratör för servern. Den här användaren skiljer sig från den Microsoft-konto du använde för att registrera dig för din Azure-prenumeration. Det måste vara en användare som du har skapat, importerat, synkroniserat eller bjudit in till Azure AD. Mer information om tillåtna Azure AD-användare finns i [Azure AD-funktioner och begränsningar i SQL Database](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

Om din Azure AD-klient inte har någon användare än skapar du en genom att följa stegen i Lägga till eller ta bort [användare med hjälp av Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Hitta objekt-ID:t för Azure AD-användaren med hjälp [`az ad user list`](/cli/azure/ad/user#az_ad_user_list) av och ersätt *\<user-principal-name>* . Resultatet sparas i en variabel.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Om du vill se en lista över alla användarens huvudnamn i Azure AD kör du `az ad user list --query [].userPrincipalName` .
>

Lägg till den här Azure AD-användaren som Active [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) Directory-administratör med kommandot i Cloud Shell. I följande kommando ersätter du *\<server-name>* med servernamnet (utan `.database.windows.net` suffixet ).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Mer information om hur du lägger till en Active Directory-administratör finns i [Etablera Azure Active Directory administratör för din server](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## <a name="set-up-visual-studio"></a>Konfigurera Visual Studio

### <a name="windows-client"></a>Windows-klient
Visual Studio för Windows är integrerad med Azure AD-autentisering. Om du vill aktivera utveckling och felsökning i Visual Studio lägger du till Azure AD-användaren i Visual Studio genom att välja Inställningar för filkonto på menyn och klicka  >   på Lägg till **ett konto**.

Om du vill ange Azure AD-användaren för Azure-tjänstautentisering väljer du Verktygsalternativ på   >   menyn och väljer sedan **Kontoval för Azure-tjänstautentisering.**  >   Välj den Azure AD-användare som du lade till och klicka på **OK.**

Nu är du redo att utveckla och felsöka din app med SQL Database server som server, med hjälp av Azure AD-autentisering.

### <a name="macos-client"></a>macOS-klient

Visual Studio för Mac är inte integrerad med Azure AD-autentisering. Men biblioteket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) som du kommer att använda senare kan använda token från Azure CLI. Om du vill aktivera utveckling och felsökning i Visual Studio måste du först [installera Azure CLI](/cli/azure/install-azure-cli) på den lokala datorn.

När Azure CLI har installerats på den lokala datorn loggar du in på Azure CLI med följande kommando med hjälp av Azure AD-användaren:

```azurecli
az login --allow-no-subscriptions
```
Nu är du redo att utveckla och felsöka din app med SQL Database server som server med hjälp av Azure AD-autentisering.

## <a name="modify-your-project"></a>Ändra ditt projekt

Vilka steg du följer för projektet beror på om det är ett ASP.NET eller ett ASP.NET Core-projekt.

- [Ändra ASP.NET](#modify-aspnet)
- [Ändra ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Ändra ASP.NET

I Visual Studio öppnar du Package Manager-konsolen och lägger till NuGet-paketet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

I *Web.config* arbetar du överst i filen och gör följande ändringar:

- I `<configSections>` lägger du till följande avsnittsdeklaration i den:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- under den avslutande `</configSections>` -taggen lägger du till följande XML-kod för `<SqlAuthenticationProviders>` .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Leta upp anslutningssträngen `MyDbConnection` med namnet och ersätt dess värde med `connectionString` `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` . Ersätt _\<server-name>_ och _\<db-name>_ med servernamnet och databasnamnet.

> [!NOTE]
> Den SqlAuthenticationProvider som du just registrerade baseras på appauthentication-biblioteket som du installerade tidigare. Som standard använder den en system tilldelad identitet. Om du vill använda en användar tilldelad identitet måste du ange ytterligare en konfiguration. Se stöd [för anslutningssträngar](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support) för AppAuthentication-biblioteket.

Det är allt du behöver för att ansluta till SQL Database. Vid felsökning i Visual Studio använder koden den Azure AD-användare som du konfigurerade [i Konfigurera Visual Studio](#set-up-visual-studio). Du kommer att konfigurera SQL Database att tillåta anslutning från den hanterade identiteten för din App Service app.

Skriv `Ctrl+F5` för att köra appen igen. Samma CRUD-app i webbläsaren ansluter nu till Azure SQL Database direkt med hjälp av Azure AD-autentisering. Med den här konfigurationen kan du köra databasmigrering från Visual Studio.

### <a name="modify-aspnet-core"></a>Ändra ASP.NET Core

I Visual Studio öppnar du Package Manager-konsolen och lägger till NuGet-paketet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

I [självstudien ASP.NET Core och SQL Database](tutorial-dotnetcore-sqldb-app.md)används inte anslutningssträngen alls eftersom den lokala utvecklingsmiljön använder en Sqlite-databasfil och Azure-produktionsmiljön använder en anslutningssträng `MyDbConnection` från App Service. Med Active Directory-autentisering vill du att båda miljöerna ska använda samma anslutningssträng. I *appsettings.jspå* ersätter du värdet för `MyDbConnection` anslutningssträngen med:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Därefter kan du ange Entity Framework databaskontext med åtkomsttoken för SQL Database. I *Data\MyDatabaseContext.cs* lägger du till följande kod inuti kparenteserna för den tomma `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktorn:

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Den här demonstrationskoden är synkron för tydlighetens och enkelhetens skull.

Det är allt du behöver för att ansluta till SQL Database. Vid felsökning i Visual Studio använder koden den Azure AD-användare som du konfigurerade [i Konfigurera Visual Studio](#set-up-visual-studio). Du kommer att konfigurera SQL Database att tillåta anslutning från den hanterade identiteten för din App Service app. Klassen `AzureServiceTokenProvider` cachelagrar token i minnet och hämtar den från Azure AD precis före förfallodatumet. Du behöver ingen anpassad kod för att uppdatera token.

> [!TIP]
> Om Den Azure AD-användare som du konfigurerade har åtkomst till flera klienter anropar du med önskat `GetAccessTokenAsync("https://database.windows.net/", tenantid)` klient-ID för att hämta rätt åtkomsttoken.

Skriv `Ctrl+F5` för att köra appen igen. Samma CRUD-app i webbläsaren ansluter nu till Azure SQL Database direkt med hjälp av Azure AD-autentisering. Med den här konfigurationen kan du köra databasmigrering från Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Använda hanterad identitetsanslutning

Sedan konfigurerar du din App Service för att ansluta till SQL Database med en system tilldelad hanterad identitet.

> [!NOTE]
> Även om anvisningarna i det här avsnittet gäller en system tilldelad identitet kan en användar tilldelad identitet lika enkelt användas. Det gör du genom att göra detta. behöver du ändra för att `az webapp identity assign command` tilldela önskad användar-tilldelad identitet. När du sedan skapar SQL-användaren ska du se till att använda namnet på den användar tilldelade identitetsresursen i stället för platsnamnet.

### <a name="enable-managed-identity-on-app"></a>Aktivera hanterad identitet i appen

När du ska aktivera en hanterad identitet för din Azure-app använder du kommandot [az webapp identity assign](/cli/azure/webapp/identity#az_webapp_identity_assign) i Cloud Shell. I följande kommando ersätter du *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Här är ett exempel på utdata:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Bevilja behörigheter till hanterad identitet

> [!NOTE]
> Om du vill kan du lägga till identiteten i en [Azure AD-grupp](../active-directory/fundamentals/active-directory-manage-groups.md)och sedan SQL Database åtkomst till Azure AD-gruppen i stället för identiteten. Följande kommandon lägger till exempel till den hanterade identiteten från föregående steg i en ny grupp med namnet _myAzureSQLDBAccessGroup:_
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

Öppna Cloud Shell och logga in på SQL Database med kommandot SQLCMD. Ersätt _\<server-name>_ med ditt servernamn, _\<db-name>_ med databasnamnet som din app använder och _\<aad-user-name>_ och med din Azure _\<aad-password>_ AD-användares autentiseringsuppgifter.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

I SQL-prompten för den databas som du vill använda kör du följande kommandon för att bevilja de behörigheter som din app behöver. Exempel: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* är namnet på den hanterade identiteten i Azure AD. Om identiteten är system tilldelad är namnet alltid detsamma som namnet på din App Service appen. Om du vill bevilja behörigheter för en Azure AD-grupp använder du gruppens visningsnamn i stället (till exempel *myAzureSQLDBAccessGroup*).

Skriv `EXIT` för att återgå till Cloud Shell-prompten.

> [!NOTE]
> Backend-tjänsterna för hanterade identiteter har också en [tokencache](overview-managed-identity.md#obtain-tokens-for-azure-resources) som uppdaterar token för en målresurs endast när den upphör att gälla. Om du gör ett misstag när du konfigurerar dina  SQL Database-behörigheter och försöker ändra behörigheterna efter att ha försökt hämta en token med din app får du inte en ny token med de uppdaterade behörigheterna förrän den cachelagrade token upphör att gälla.

> [!NOTE]
> AAD stöds inte för lokala lokala enheter SQL Server detta omfattar MIS. 

### <a name="modify-connection-string"></a>Ändra anslutningssträngen

Kom ihåg att samma ändringar som du gjorde i *Web.config* eller *appsettings.jspå* fungerar med den hanterade identiteten, så det enda du behöver göra är att ta bort den befintliga anslutningssträngen i App Service, som Visual Studio skapade när du distribuerade appen första gången. Använd följande kommando, men ersätt *\<app-name>* med namnet på din app.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicera dina ändringar

Nu behöver du bara publicera ändringarna till Azure.

**Om du kommer från [Självstudie: Skapa en ASP.NET-app](app-service-web-tutorial-dotnet-sqldatabase.md)** i Azure med SQL Database publicerar du ändringarna i Visual Studio. I **Solution Explorer**: Högerklicka på projektet **DotNetAppSqlDb** och välj **Publicera**.

![Publicera från Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klicka på **Publicera** på publiceringssidan. 

**Om du kommer från [Självstudie: Skapa en ASP.NET Core- och SQL Database-app i Azure App Service](tutorial-dotnetcore-sqldb-app.md)** publicerar du ändringarna med Git, med följande kommandon:

```bash
git commit -am "configure managed identity"
git push azure main
```

När du ser din att göra-lista på den nya webbsidan ansluter din app till databasen med hjälp av den hanterade identiteten.

![Azure-app efter Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Du ska nu kunna redigera att göra-listan som innan.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Aktivera hanterade identiteter
> * Ge SQL Database åtkomst till den hanterade identiteten
> * Konfigurera Entity Framework att använda Azure AD-autentisering med SQL Database
> * Ansluta till SQL Database från Visual Studio med Azure AD-autentisering

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md)