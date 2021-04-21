---
title: Självstudie för att Azure App Configuration Key Vault referenser i en ASP.NET Core-| Microsoft Docs
description: I den här självstudien får du lära dig hur Azure App Configuration använder Key Vault från en ASP.NET Core-app
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 57abbeefe8e3f2abe527f2b282d643db766b9dc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775753"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Självstudie: Använda Key Vault referenser i en ASP.NET Core-app

I den här självstudien lär du dig att använda Azure App Configuration-tjänsten tillsammans med Azure Key Vault. App Configuration och Key Vault kompletterande tjänster som används sida vid sida i de flesta programdistributioner.

App Configuration hjälper dig att använda tjänsterna tillsammans genom att skapa nycklar som refererar till värden som lagras i Key Vault. När App Configuration skapar sådana nycklar lagras URI:er för Key Vault snarare än själva värdena.

Programmet använder klientprovidern App Configuration för att hämta Key Vault referenser, precis som för andra nycklar som lagras i App Configuration. I det här fallet är värdena som lagras App Configuration URI:er som refererar till värdena i Key Vault. De är inte Key Vault värden eller autentiseringsuppgifter. Eftersom klientprovidern identifierar nycklarna som Key Vault referenser använder den Key Vault för att hämta deras värden.

Ditt program ansvarar för att autentisera korrekt till både App Configuration och Key Vault. De två tjänsterna kommunicerar inte direkt.

Den här självstudien visar hur du implementerar Key Vault referenser i koden. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter slutför du [Skapa en ASP.NET Core-app med App Configuration](./quickstart-aspnet-core-app.md) först.

Du kan använda valfri kodredigerare för att göra stegen i den här självstudien. Till exempel [Visual Studio Code](https://code.visualstudio.com/) är en plattformsoberoende kodredigerare som är tillgänglig för Windows-, macOS- och Linux-operativsystemen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en App Configuration nyckel som refererar till ett värde som lagras Key Vault.
> * Få åtkomst till värdet för den här nyckeln från ASP.NET Core-webbapp.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med den här självstudien installerar [du .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Skapa ett valv

1. Välj alternativet **Skapa en** resurs i det övre vänstra hörnet i Azure Portal:

    ![Skärmbild som visar alternativet Skapa en resurs i Azure Portal.](./media/quickstarts/search-services.png)
1. Ange **Key Vault** i sökrutan.
1. I resultatlistan väljer du **Nyckelvalv** till vänster.
1. I **Nyckelvalv väljer** du Lägg **till**.
1. Ange följande information **till höger i** Skapa nyckelvalv:
    - Välj **Prenumeration för** att välja en prenumeration.
    - I **Resursgrupp** väljer du **Skapa ny** och anger ett resursgruppsnamn.
    - I **Nyckelvalvsnamn** krävs ett unikt namn. I den här självstudien anger **du Contoso-vault2**.
    - Välj **en** plats i listrutan Region.
1. Lämna **standardvärdena för de andra** alternativen för Att skapa nyckelvalv.
1. Välj **Skapa**.

Nu är ditt Azure-konto det enda som har behörighet att komma åt det nya valvet.

![Skärmbild som visar nyckelvalvet.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Om du vill lägga till en hemlighet i valvet behöver du bara vidta några ytterligare steg. I det här fallet lägger du till ett meddelande som du kan använda för att Key Vault hämtning. Meddelandet kallas Meddelande **och** du lagrar värdet "Hello from Key Vault" i det.

1. Från sidan Key Vault egenskaper väljer du **Hemligheter.**
1. Välj **Generera/importera**.
1. I fönstret **Skapa en** hemlighet anger du följande värden:
    - **Uppladdningsalternativ:** Ange **Manuell**.
    - **Namn:** Ange **Meddelande**.
    - **Värde:** Ange **Hello från Key Vault**.
1. Lämna **standardvärdena för de andra egenskaperna** Skapa en hemlighet.
1. Välj **Skapa**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Lägga till en Key Vault-referens för App Configuration

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Alla resurser** och välj sedan den App Configuration Store-instans som du skapade i snabbstarten.

1. Välj **Configuration Explorer.**

1. Välj **+ Skapa**  >  **nyckelvalvsreferens** och ange sedan följande värden:
    - **Nyckel:** Välj **TestApp:Settings:KeyVaultMessage**.
    - **Etikett:** Lämna det här värdet tomt.
    - **Prenumeration,** **Resursgrupp** och **Nyckelvalv:** Ange de värden som motsvarar de i nyckelvalvet som du skapade i föregående avsnitt.
    - **Hemlighet:** Välj hemligheten med **namnet Meddelande** som du skapade i föregående avsnitt.

## <a name="connect-to-key-vault"></a>Ansluta till Key Vault

1. I den här självstudien använder du ett huvudnamn för tjänsten för autentisering för att Key Vault. Om du vill skapa tjänstens huvudnamn använder du kommandot Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Den här åtgärden returnerar en serie nyckel/värde-par:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Kör följande kommando för att ge tjänstens huvudnamn åtkomst till nyckelvalvet:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Lägg till miljövariabler för att lagra *värdena för clientId,* *clientSecret* och *tenantId*.

    #### <a name="windows-command-prompt"></a>[Kommandotolk i Windows](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Dessa Key Vault autentiseringsuppgifter används endast i ditt program. Ditt program autentiserar direkt till Key Vault med dessa autentiseringsuppgifter. De skickas aldrig till App Configuration tjänsten.

1. Starta om terminalen för att läsa in de nya miljövariablerna.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uppdatera koden så att den använder Key Vault referens

1. Lägg till en referens till de Nödvändiga NuGet-paketen genom att köra följande kommando:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Öppna *Program.cs* och lägg till referenser till följande nödvändiga paket:

    ```csharp
    using Azure.Identity;
    ```

1. Uppdatera metoden `CreateWebHostBuilder` för att använda App Configuration genom att anropa metoden `config.AddAzureAppConfiguration` . Inkludera `ConfigureKeyVault` alternativet och skicka rätt autentiseringsuppgifter till din Key Vault.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. När du initierade anslutningen till App Configuration konfigurerade du anslutningen till Key Vault genom att anropa `ConfigureKeyVault` metoden . Efter initieringen kan du komma åt värdena för Key Vault referenser på samma sätt som du kommer åt värdena för vanliga App Configuration nycklar.

    Om du vill se hur den här processen ser ut i praktiken *öppnar du Index.cshtml* i **mappen**  >  **Vyer.** Ersätt innehållet med följande kod:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Du kommer åt värdet för Key Vault **testapp:Settings:KeyVaultMessage** på samma sätt som för konfigurationsvärdet **TestApp:Settings:Message**.

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommandogränssnittet:

    ```dotnetcli
    dotnet build
    ```

1. När bygget är klart använder du följande kommando för att köra webbappen lokalt:

    ```dotnetcli
    dotnet run
    ```

1. Öppna ett webbläsarfönster och gå till `http://localhost:5000` , som är standard-URL:en för webbappen som finns lokalt.

    ![Snabbstart för lokal appstart](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat App Configuration nyckel som refererar till ett värde som lagras i Key Vault. Om du vill lära dig hur du lägger till en Azure-hanterad tjänstidentitet som effektiviserar åtkomsten till App Configuration och Key Vault kan du fortsätta till nästa självstudie.

> [!div class="nextstepaction"]
> [Integrering av hanterade identiteter](./howto-integrate-azure-managed-service-identity.md)
