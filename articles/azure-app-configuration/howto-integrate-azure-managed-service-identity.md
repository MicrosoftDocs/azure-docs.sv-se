---
title: Använda hanterade identiteter för att få åtkomst till App Configuration
titleSuffix: Azure App Configuration
description: Autentisera till Azure App Configuration hanterade identiteter
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 591f767fe0ef2150f7fe180108f207b56f7c915f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764319"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Använda hanterade identiteter för att få åtkomst till App Configuration

Azure Active Directory [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md) förenklar hanteringen av hemligheter för ditt molnprogram. Med en hanterad identitet kan koden använda tjänstens huvudnamn som skapats för den Azure-tjänst som den körs på. Du använder en hanterad identitet i stället för en separat autentiseringsidentifiering som lagras Azure Key Vault eller en lokal anslutningssträng.

Azure App Configuration och klientbiblioteken för .NET Core, .NET Framework och Java Spring har inbyggt stöd för hanterade identiteter. Även om du inte behöver använda den eliminerar den hanterade identiteten behovet av en åtkomsttoken som innehåller hemligheter. Koden kan komma åt App Configuration med endast tjänstslutpunkten. Du kan bädda in den här URL:en i koden direkt utan att exponera någon hemlighet.

Den här artikeln visar hur du kan dra nytta av den hanterade identiteten för att komma App Configuration. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter skapar  [du en ASP.NET Core-app med App Configuration](./quickstart-aspnet-core-app.md) först.

> [!NOTE]
> Den här artikeln Azure App Service som exempel, men samma koncept gäller för alla andra Azure-tjänster som stöder hanterade identiteter, till exempel [Azure Kubernetes Service,](../aks/use-azure-ad-pod-identity.md) [virtuell Azure-dator](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) [och Azure Container Instances](../container-instances/container-instances-managed-identity.md). Om din arbetsbelastning finns i någon av dessa tjänster kan du även använda tjänstens stöd för hanterade identiteter.

Den här artikeln visar också hur du kan använda den hanterade identiteten tillsammans App Configuration de Key Vault referenserna. Med en enda hanterad identitet kan du sömlöst komma åt både hemligheter från Key Vault och konfigurationsvärden från App Configuration. Om du vill utforska den här funktionen slutför du [Använd Key Vault-referenser med ASP.NET Core](./use-key-vault-references-dotnet-core.md) först.

Du kan använda valfri kodredigerare för att göra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformarna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Ge en hanterad identitet åtkomst till App Configuration.
> * Konfigurera din app för att använda en hanterad identitet när du ansluter till App Configuration.
> * Du kan också konfigurera din app så att den använder en hanterad identitet när du ansluter Key Vault via en App Configuration Key Vault referens.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna slutföra den här självstudiekursen:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell konfigurerat](../cloud-shell/quickstart.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Lägga till en hanterad identitet

Om du vill konfigurera en hanterad identitet i portalen skapar du först ett program och aktiverar sedan funktionen.

1. Skapa en App Services instans i [Azure Portal](https://portal.azure.com) som vanligt. Gå till den i portalen.

1. Rulla ned till **gruppen Inställningar** i det vänstra fönstret och välj **Identitet.**

1. På fliken **System tilldelad** växlar du **Status** till **På** och väljer **Spara.**

1. Svara **Ja när** du uppmanas att aktivera system tilldelad hanterad identitet.

    ![Ange hanterad identitet i App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Bevilja åtkomst till App Configuration

1. I [Azure Portal](https://portal.azure.com)väljer du **Alla resurser och** väljer App Configuration som du skapade i snabbstarten.

1. Välj **Åtkomstkontroll (IAM)** .

1. På fliken **Kontrollera åtkomst väljer** du Lägg till **i** användargränssnittet för kortet **Lägg till** rolltilldelning.

1. Under **Roll** väljer du **App Configuration Dataläsare**. Under **Tilldela åtkomst till väljer** du App Service under System  **tilldelad hanterad identitet**.

1. Under **Prenumeration** väljer du din Azure-prenumeration. Välj App Service resurs för din app.

1. Välj **Spara**.

    ![Lägga till en hanterad identitet](./media/add-managed-identity.png)

1. Valfritt: Om du även vill bevilja åtkomst Key Vault följer du anvisningarna i Tilldela [en Key Vault åtkomstprincip](../key-vault/general/assign-access-policy-portal.md).

## <a name="use-a-managed-identity"></a>Använda en hanterad identitet

1. Lägg till en referens till *Azure.Identity-paketet:*

    ```bash
    dotnet add package Azure.Identity
    ```

1. Hitta slutpunkten till App Configuration store. Den här URL:en visas **på fliken** Åtkomstnycklar för arkivet i Azure Portal.

1. Öppna *appsettings.jspå* och lägg till följande skript. Ersätt *\<service_endpoint>* , inklusive hakparenteserna, med URL:en till App Configuration arkivet.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Öppna *Program.cs* och lägg till en referens till `Azure.Identity` `Microsoft.Azure.Services.AppAuthentication` namnrymderna och :

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Om du bara vill komma åt värden som lagras direkt i App Configuration uppdaterar du metoden genom att ersätta metoden `CreateWebHostBuilder` `config.AddAzureAppConfiguration()` (detta finns i `Microsoft.Azure.AppConfiguration.AspNetCore` paketet).

    > [!IMPORTANT]
    > `CreateHostBuilder` ersätter `CreateWebHostBuilder` i .NET Core 3.0.  Välj rätt syntax baserat på din miljö.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    > [!NOTE]
    > Om du vill använda en användar tilldelad hanterad identitet **måste** du ange clientId när du skapar [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential).
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Som förklaras i [vanliga frågor och svar om hanterade identiteter](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)för Azure-resurser finns det ett standard sätt att lösa vilken hanterad identitet som används. I det här fallet gör Azure Identity-biblioteket att du måste ange önskad identitet för att undvika eventuella körningsproblem i framtiden (till exempel om en ny användar tilldelad hanterad identitet läggs till eller om den system tilldelade hanterade identiteten har aktiverats). Därför måste du ange clientId även om endast en användardefinierad hanterad identitet har definierats och det inte finns någon system tilldelad hanterad identitet.


1. Om du vill App Configuration använda både Key Vault och referenser uppdaterar du *Program.cs* enligt nedan. Den här koden anropar som en del av för att berätta för konfigurationsprovidern vilka autentiseringsuppgifter som ska användas `SetCredential` `ConfigureKeyVault` vid autentisering Key Vault.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   var credentials = new ManagedIdentityCredential();

                   config.AddAzureAppConfiguration(options =>
                   {
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                              .ConfigureKeyVault(kv =>
                              {
                                 kv.SetCredential(credentials);
                              });
                   });
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    var credentials = new ManagedIdentityCredential();

                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                               .ConfigureKeyVault(kv =>
                               {
                                   kv.SetCredential(credentials);
                               });
                    });
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    Nu kan du komma Key Vault referenser precis som andra App Configuration nyckel. Konfigurationsprovidern använder `ManagedIdentityCredential` för att autentisera Key Vault och hämta värdet.

    > [!NOTE]
    > fungerar `ManagedIdentityCredential` endast i Azure-miljöer med tjänster som stöder hanterad identitetsautentisering. Det fungerar inte i den lokala miljön. Använd för att koden ska fungera i både lokala miljöer och Azure-miljöer eftersom den kommer att gå tillbaka [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) till några autentiseringsalternativ, inklusive hanterad identitet.
    > 
    > Om du vill använda en **användarsignerad** hanterad identitet med när den distribueras till `DefaultAzureCredential` Azure anger du [clientId](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Distribuera från lokal Git

Det enklaste sättet att aktivera lokal Git-distribution för din app med Kudu-byggservern är att använda [Azure Cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivera lokal Git med Kudu
Om du inte har en lokal git-lagringsplats för din app måste du initiera en. Initiera en lokal git-lagringsplats genom att köra följande kommandon från appens projektkatalog:

```cmd
git init
git add .
git commit -m "Initial version"
```

Om du vill aktivera lokal Git-distribution för din app med Kudu-byggservern kör [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az_webapp_deployment_source_config_local_git) du i Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Det här kommandot ger dig något som liknar följande utdata:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Distribuera projektet

I det _lokala terminalfönstret lägger du_ till en Azure-fjärrplats till din lokala Git-lagringsplats. Ersätt _\<url>_ med URL:en för den fjärranslutna Git som du fick [från Aktivera lokal Git med Kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När du uppmanas att ange ett lösenord anger du det lösenord som du skapade i [Konfigurera en distributionsanvändare.](#configure-a-deployment-user) Använd inte det lösenord som du använder för att logga in på Azure Portal.

```bash
git push azure main
```

Du kan se körningsspecifik automatisering i utdata, till exempel MSBuild för ASP.NET, `npm install` för Node.js och för `pip install` Python.

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till webbappen med hjälp av en webbläsare för att verifiera att innehållet har distribuerats.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Använd hanterad identitet på andra språk

App Configuration-providrar för .NET Framework och Java Spring har också inbyggt stöd för hanterad identitet. Du kan använda butikens URL-slutpunkt i stället för dess fullständiga anslutningssträng när du konfigurerar någon av dessa providers.

Du kan till exempel uppdatera .NET Framework-konsolappen som skapades i snabbstarten  för att ange följande inställningar iApp.configfilen:

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg
I den här självstudien lade du till en hanterad Azure-identitet för att effektivisera åtkomsten App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Om du vill veta mer om hur App Configuration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)