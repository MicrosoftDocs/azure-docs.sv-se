---
title: Spara hemliga program inställningar för ett webb program på ett säkert sätt – Azure Key Vault | Microsoft Docs
description: Hur du säkert sparar inställningar för hemliga program, till exempel Azure-autentiseringsuppgifter eller API-nycklar från tredje part med hjälp av ASP.NET Core Key Vault Provider, användar hemlighet eller .NET 4.7.1 Configuration Builder
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: f20a40603916e703d6f3cfc13ee2d165675f3ca2
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588508"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Spara hemliga program inställningar för ett webb program på ett säkert sätt

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du säkert sparar konfigurations inställningar för hemliga program för Azure-program.

Traditionellt har alla konfigurations inställningar för webb program sparats i konfigurationsfiler som Web.config. Den här övningen leder till att kontrol lera hemliga inställningar som till exempel moln uppgifter för offentliga käll kontrolls system, t. ex. GitHub. Under tiden kan det vara svårt att följa säkerhets praxis på grund av den omkostnader som krävs för att ändra käll koden och konfigurera om utvecklings inställningarna.

För att säkerställa att utvecklings processen är säker skapas verktygs-och Ramverks bibliotek för att spara inställningar för program hemlighet på ett säkert sätt med minimal eller ingen käll kods ändring.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET-och .NET Core-program

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Spara hemliga inställningar i användarens hemliga arkiv som ligger utanför käll kontroll mappen
Om du utför en snabb prototyp eller om du inte har till gång till Internet, börjar du med att flytta dina hemliga inställningar utanför käll kontrollens mapp till användarens hemliga arkiv. Användarens hemliga arkiv är en fil som sparats under mappen User profiler, så hemligheterna är inte incheckade i käll kontrollen. Följande diagram visar hur [användar hemlighet](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) fungerar.

![Användar hemlighet behåller hemliga inställningar utanför käll kontrollen](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Om du kör .NET Core-konsolen använder du Key Vault för att spara din hemliga information på ett säkert sätt.

### <a name="save-secret-settings-in-azure-key-vault"></a>Spara hemliga inställningar i Azure Key Vault
Om du utvecklar ett projekt och behöver dela käll koden på ett säkert sätt, använder du [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Skapa en Key Vault i din Azure-prenumeration. Fyll i alla obligatoriska fält i användar gränssnittet och klicka på *skapa* längst ned på bladet

    ![Skapa Azure Key Vault](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Ge dig och ditt team medlemmar åtkomst till Key Vault. Om du har ett stort team kan du skapa en [Azure Active Directory-grupp](../../active-directory/active-directory-groups-create-azure-portal.md) och lägga till den säkerhets gruppen till Key Vault. I list rutan *hemliga behörigheter* kontrollerar du *Hämta* och *lista* under *hemliga hanterings åtgärder*.
Om du redan har skapat din webbapp ger du webbappen åtkomst till den Key Vault så att den kan komma åt nyckel valvet utan att lagra hemlig konfiguration i appinställningar eller filer. Sök efter din webbapp med sitt namn och Lägg till den på samma sätt som du beviljar användarna åtkomst.

    ![Lägg till Key Vault åtkomst princip](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Lägg till din hemlighet i Key Vault på Azure Portal. För kapslade konfigurations inställningar ersätter du ":" med "-", så att Key Vault hemliga namnet är giltigt. ":" får inte vara i namnet på en Key Vault hemlighet.

    ![Lägg till Key Vault hemlighet](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Före Visual Studio 2017 V-15,6 vi vi använde för att rekommendera att installera Azure-tjänstens autentiserings-tillägg för Visual Studio. Men det är föråldrat nu när funktionen är integrerad i Visual Studio. Om du använder en äldre version av Visual Studio 2017, rekommenderar vi att du uppdaterar till minst VS 2017 15,6 eller upp så att du kan använda den här funktionen internt och komma åt nyckel valvet från att använda Visual Studio-inloggning identiteten.
    >

4. Lägg till följande NuGet-paket i projektet:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Lägg till följande kod i Program.cs-filen:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                        keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Lägg till din Key Vault-URL i launchsettings.jspå filen. Miljö variabel namnet *KEYVAULT_ENDPOINT* definieras i den kod som du lade till i steg 6.

    ![Lägg till Key Vault URL som en projekt miljö variabel](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Starta fel sökningen av projektet. Den bör kunna köras.

## <a name="aspnet-and-net-applications"></a>ASP.NET-och .NET-program

.NET 4.7.1 stöder Key Vault-och hemligheter för konfigurations verktyg, som säkerställer att hemligheter kan flyttas utanför käll kontrolls-mappen utan kod ändringar.
Fortsätt genom att [Ladda ned .NET-4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) och migrera ditt program om det använder en äldre version av .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Spara hemliga inställningar i en hemlig fil utanför käll kontroll mappen
Om du skriver en snabb prototyp och inte vill etablera Azure-resurser går du till det här alternativet.

1. Installera följande NuGet-paket i projektet
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Skapa en fil som liknar följande. Spara den under en plats utanför projektmappen.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Definiera den hemliga filen som konfigurations verktyg i Web.configs filen. Lägg till det här avsnittet i avsnittet *appSettings* .

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Avsnittet appSettings använder det hemliga konfigurations verktyget. Se till att det finns en post för den hemliga inställningen med ett dummy-värde.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Felsök din app. Den bör kunna köras.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Spara hemliga inställningar i en Azure Key Vault
Följ anvisningarna från avsnittet ASP.NET Core om du vill konfigurera ett Key Vault för projektet.

1. Installera följande NuGet-paket i projektet
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Definiera Key Vault Configuration Builder i Web.config. Lägg till det här avsnittet i avsnittet *appSettings* . Ersätt *vaultName* till Key Vault namnet om Key Vault finns i en offentlig Azure-eller fullständig URI om du använder det suveräna molnet.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. Ange appSettings-avsnittet använder Key Vault Configuration Builder. Se till att det finns en post för den hemliga inställningen med ett värde för provdocka.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Starta fel sökningen av projektet. Den bör kunna köras.
