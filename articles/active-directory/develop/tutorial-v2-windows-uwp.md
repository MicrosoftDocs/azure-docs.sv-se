---
title: 'Självstudie: skapa en Universell Windows-plattform-app (UWP) som använder Microsoft Identity Platform för autentisering | Azure'
titleSuffix: Microsoft identity platform
description: 'I den här självstudien skapar du ett UWP-program som använder Microsoft Identity Platform för att logga in användare och få en åtkomsttoken för att anropa Microsoft Graph-API: et för deras räkning.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 63d56d8afc584a760f4b31c6021d4c764afd52b3
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064427"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Självstudie: anropa Microsoft Graph API från ett Universell Windows-plattform-program (UWP)

I den här självstudien skapar du en UWP-app (Native Universell Windows-plattform) som loggar in användare och hämtar en åtkomsttoken för att anropa Microsoft Graph-API: et. 

I slutet av den här guiden anropar programmet ett skyddat API med hjälp av personliga konton. Exempel är outlook.com, live.com och andra. Ditt program anropar också arbets-och skol konton från alla företag eller organisationer som har Azure Active Directory (Azure AD).

I de här självstudierna har du

> [!div class="checklist"]
> * Skapa ett *universell Windows-plattform-projekt (UWP)* i Visual Studio
> * Registrera programmet i Azure Portal
> * Lägg till kod som stöd för användar inloggning och utloggning
> * Lägg till kod för anrop Microsoft Graph API
> * Testa appen

## <a name="prerequisites"></a>Förutsättningar

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) med arbets belastningen [universell Windows-plattform utveckling](/windows/uwp/get-started/get-set-up) installerad

## <a name="how-this-guide-works"></a>Så här fungerar den här guiden

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Den här guiden skapar ett exempel på ett UWP-program som frågar Microsoft Graph-API: et. I det här scenariot läggs en token till i HTTP-begäranden med hjälp av Authorization-huvudet. Microsoft Authentication Library hanterar hämtning av token och uppdateringar.

## <a name="nuget-packages"></a>NuGet-paket

I den här guiden används följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client)| Microsofts autentiseringsbibliotek|
|[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph)|Microsoft Graph klient bibliotek|

## <a name="set-up-your-project"></a>Konfigurera projektet

Det här avsnittet innehåller steg-för-steg-instruktioner för att integrera ett Windows Desktop .NET-program (XAML) med inloggning med Microsoft. Sedan kan programmet fråga webb-API: er som kräver en token, till exempel Microsoft Graph-API: et.

Den här guiden skapar ett program som visar en knapp som frågar Microsoft Graph-API och en knapp för att logga ut. Den visar också text rutor som innehåller resultatet av anropen.

> [!NOTE]
> Vill du hämta det här exemplets Visual Studio-projekt i stället för att skapa det? [Hämta ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)och gå vidare till [programmets registrerings](#register-your-application "program registrerings steg") steg för att konfigurera kod exemplet innan det körs.

### <a name="create-your-application"></a>Skapa ditt program

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt** väljer du **Tom app (Universal Windows)** för C# och väljer **sedan nästa**.
1. I **Konfigurera ditt nya projekt** namnger du appen och väljer **skapa**.
1. Om du uppmanas att göra det väljer du en version för **mål** och **lägsta** versioner i **nytt universell Windows-plattform projekt** och väljer **OK**.

   ![Minimi-och mål versioner](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-the-microsoft-authentication-library-to-your-project"></a>Lägg till Microsoft Authentication Library i projektet

1. I Visual Studio väljer du **verktyg**  >  **NuGet Package Manager**  >  **Package Manager-konsolen**.
1. Kopiera och klistra in följande kommandon i fönstret **Package Manager-konsol** :

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > Det första kommandot installerar [Microsoft Authentication Library (MSAL.net)](https://aka.ms/msal-net). MSAL.NET skaffar, cachelagrar och uppdaterar användartoken som har åtkomst till API: er som skyddas av Microsoft Identity Platform. Det andra kommandot installerar [Microsoft Graph .net-klient bibliotek](https://github.com/microsoftgraph/msgraph-sdk-dotnet) för att autentisera begär anden till Microsoft Graph och göra anrop till tjänsten.

### <a name="create-your-applications-ui"></a>Skapa ditt programs användar gränssnitt

Visual Studio skapar *mainpage. XAML* som en del av projekt mal len. Öppna den här filen och ersätt sedan programmets **Grid** -nod med följande kod:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-the-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Använd Microsoft Authentication Library för att hämta en token för Microsoft Graph-API: et

Det här avsnittet visar hur du använder Microsoft Authentication Library för att hämta en token för Microsoft Graph-API: et. Gör ändringar i *mainpage.XAML.cs* -filen.

1. I *mainpage.XAML.cs* lägger du till följande referenser:

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Ersätt `MainPage` klassen med följande kod:

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt<a name="more-information"></a>

`AcquireTokenInteractive`Metoden resulterar i ett fönster som gör att användarna kan logga in. Program kräver vanligt vis att användare loggar in interaktivt första gången för att få åtkomst till en skyddad resurs. De kan också behöva logga in när en tyst åtgärd för att hämta en token Miss lyckas. Ett exempel är när en användares lösen ord har upphört att gälla.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

`AcquireTokenSilent`Metoden hanterar hämtning av token och förnyelser utan några åtgärder från användaren. När `AcquireTokenInteractive` körningarna första gången har körts och uppmanas användaren att ange autentiseringsuppgifter använder du `AcquireTokenSilent` metoden för att begära token för senare anrop. Den här metoden hämtar token tyst. Microsoft Authentication Library hanterar token cache och förnyelse.

Slutligen kan `AcquireTokenSilent` metoden Miss lyckas. Orsaken till problemet är en användare som loggat ut eller ändrat sitt lösen ord på en annan enhet. När Microsoft Authentication Library identifierar att problemet kräver en interaktiv åtgärd, utlöses ett `MsalUiRequiredException` undantag. Ditt program kan hantera detta undantag på två sätt:

* Dina program anropas `AcquireTokenInteractive` omedelbart. Det här anropet resulterar i att användaren uppmanas att logga in. Använd normalt den här metoden för online-program där det inte finns något tillgängligt offline-innehåll för användaren. Exemplet som genereras av den här guidade installationen följer mönstret. Du ser det i åtgärd första gången du kör exemplet.

   Eftersom ingen användare har använt programmet, `accounts.FirstOrDefault()` innehåller ett null-värde och genererar ett `MsalUiRequiredException` undantag.

   Koden i exemplet hanterar sedan undantaget genom att anropa `AcquireTokenInteractive` . Det här anropet resulterar i att användaren uppmanas att logga in.

* Ditt program visar en visuell information till användare som de behöver för att logga in. Sedan kan de välja rätt tid för att logga in. Programmet kan försöka igen `AcquireTokenSilent` senare. Använd den här metoden när användare kan använda andra program funktioner utan avbrott. Ett exempel är när offlineinnehåll är tillgängligt i programmet. I det här fallet kan användarna bestämma när de vill logga in. Programmet kan försöka igen `AcquireTokenSilent` när nätverket är tillfälligt otillgängligt.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>Instansiera Microsoft Graph tjänst klienten genom att hämta token från SignInUserAndGetTokenUsingMSAL-metoden

Lägg till följande nya metod i *mainpage.XAML.cs*:

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddat API

I det här exempel programmet `GetGraphServiceClient` instansierar-metoden `GraphServiceClient` med hjälp av en åtkomsttoken. Sedan `GraphServiceClient` används den för att hämta användarens profil information från **mig** -slutpunkten.

### <a name="add-a-method-to-sign-out-the-user"></a>Lägg till en metod för att logga ut användaren

Om du vill logga ut användaren lägger du till följande metod i *mainpage.XAML.cs*:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

> [!NOTE]
> MSAL.NET använder asynkrona metoder för att förvärva token eller manipulera konton. Du måste ha stöd för UI-åtgärder i UI-tråden. Detta är orsaken till `Dispatcher.RunAsync` anropet och de försiktighets åtgärder som krävs för att anropa `ConfigureAwait(false)` .

#### <a name="more-information-about-signing-out"></a>Mer information om att logga ut<a name="more-information-on-sign-out"></a>

`SignOutButton_Click`Metoden tar bort användaren från användar-cachen i Microsoft Authentication Library. Den här metoden instruerar Microsoft Authentication Library att glömma den aktuella användaren. En framtida begäran om att hämta en token Miss lyckas endast om den är interaktiv.

Programmet i det här exemplet stöder en enskild användare. Microsoft Authentication Library stöder scenarier där användaren kan logga in på mer än ett konto. Ett exempel är ett e-postprogram där en användare har flera konton.

### <a name="display-basic-token-information"></a>Visa grundläggande information om token

Lägg till följande metod i *mainpage.XAML.cs* för att visa grundläggande information om token:

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>Mer information<a name="more-information-1"></a>

ID-token som förvärv ATS med hjälp av **OpenID Connect** innehåller också en liten delmängd av information som är relevant för användaren. `DisplayBasicTokenInfo` visar grundläggande information som finns i token. Den här informationen omfattar användarens visnings namn och ID. Den innehåller också utgångs datumet för token och strängen som representerar själva åtkomsttoken. Om du väljer API-knappen **anropa Microsoft Graph** flera gånger ser du att samma token återanvändes för senare förfrågningar. Du kan också se förfallo datumet när Microsoft Authentication Library bestämmer sig för att förnya token.

### <a name="display-message"></a>Visa meddelande

Lägg till följande nya metod i *mainpage.XAML.cs*:

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>Registrera ditt program

Nu måste du registrera ditt program:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
1. Ange ett **namn** för programmet, till exempel `UWP-App-calling-MSGraph` . Användare av appen kan se det här namnet och du kan ändra det senare.
1. Under **konto typer som stöds** väljer du **konton i valfri organisations katalog (alla Azure AD-klienter-flera klienter) och personliga Microsoft-konton (t. ex. Skype, Xbox)**. 
1. Välj **Register** (Registrera).
1. På sidan Översikt hittar du **ID-värdet för programmet (klienten)** och kopierar det. Gå tillbaka till Visual Studio, öppna *mainpage.XAML.cs* och Ersätt värdet för `ClientId` med det här värdet.

Konfigurera autentisering för ditt program:

1. Gå tillbaka till <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span></a>, under **Hantera**, Välj **autentisering**  >  **Lägg till en plattform** och välj sedan **mobil-och skriv bords program**.
1. I avsnittet **omdirigerings-URI: er** , kontrollerar du **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Välj **Konfigurera**.

Konfigurera API-behörigheter för ditt program:

1. Under **Hantera** väljer du **API-behörigheter**  >  **Lägg till en behörighet**.
1. Välj **Microsoft Graph**.
1. Välj **delegerade behörigheter**, Sök efter *användare. Läs* och kontrol lera att **User. Read** är markerat.
1. Om du har gjort några ändringar väljer du **Lägg till behörigheter** för att spara dem.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Aktivera integrerad autentisering på federerade domäner (valfritt)

Om du vill aktivera integrerad Windows-autentisering när den används med en federerad Azure AD-domän måste applikations manifestet aktivera ytterligare funktioner. Gå tillbaka till programmet i Visual Studio.

1. Öppna *Package. appxmanifest*.
1. Välj **funktioner** och aktivera följande inställningar:

   * **Enterprise-autentisering**
   * **Privata nätverk (klient & Server)**
   * **Delade användarcertifikat**

> [!IMPORTANT]
> [Integrerad Windows-autentisering](https://aka.ms/msal-net-iwa) är inte konfigurerat som standard för det här exemplet. Program som begär `Enterprise Authentication` eller- `Shared User Certificates` funktioner kräver en högre verifierings nivå av Windows Store. Alla utvecklare vill inte heller utföra den högre verifierings nivån. Aktivera endast den här inställningen om du behöver integrerad Windows-autentisering med en federerad Azure AD-domän.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>Alternativ metod för att använda WithDefaultRedirectURI ()

I det aktuella exemplet `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` används metoden. `WithDefaultRedirectURI()`Slutför följande steg för att använda:

1. I *mainpage.XAML.cs* ersätter du `WithRedirectUri` med `WithDefaultRedirectUri` :

   **Aktuell kod**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **Uppdaterad kod**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  Hitta återanrops-URI för appen genom att lägga till `redirectURI` fältet i *mainpage.XAML.cs* och ange en Bryt punkt för den:

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    Kör appen och kopiera sedan värdet för `redirectUri` när Bryt punkten är påslagen. Värdet bör se ut ungefär så här: `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    Du kan sedan ta bort kodraden eftersom den endast krävs en gång, för att hämta värdet.

3. I appens registrerings Portal lägger du till det returnerade värdet i **RedirectUri** i fönstret **autentisering** .

## <a name="test-your-code"></a>Testa koden

Testa programmet genom att välja **F5** -nyckeln för att köra projektet i Visual Studio. Ditt huvud fönster visas:

![Programmets användar gränssnitt](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

När du är redo att testa väljer du **anropa Microsoft Graph API**. Använd sedan ett organisations konto i Azure AD eller en Microsoft-konto, till exempel live.com eller outlook.com, för att logga in. Första gången en användare kör det här testet visar programmet ett fönster där användaren uppmanas att logga in.

### <a name="consent"></a>Samtycke

Första gången du loggar in på ditt program visas en medgivande skärm som liknar följande bild. Välj **Ja** om du uttryckligen vill ge åtkomst:

![Åtkomst till medgivande skärmen](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Förväntat resultat

Du ser information om användar profiler som returneras av Microsoft Graph API-anropet på skärmen med **API-anrops resultat** :

![Resultat skärm för API-anrop](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Du ser också grundläggande information om den token som hämtats via `AcquireTokenInteractive` eller `AcquireTokenSilent` i rutan **token-information** :

|Egenskap  |Format  |Beskrivning |
|---------|---------|---------|
|`Username` |`user@domain.com` |Det användar namn som identifierar användaren.|
|`Token Expires` |`DateTime` |Tiden då token upphör att gälla. Microsoft Authentication Library utökar förfallo datumet genom att förnya token vid behov.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om omfattningar och delegerade behörigheter

Microsoft Graph-API: t kräver `user.read` att omfattningen läser en användar profil. Det här omfånget läggs till som standard i alla program som är registrerade i program registrerings portalen. Andra API: er för Microsoft Graph och anpassade API: er för backend-servern kan kräva ytterligare omfång. Till exempel kräver Microsoft Graph-API: n `Calendars.Read` för att visa en lista över användarens kalendrar.

Om du vill komma åt användarens kalendrar i ett programs kontext lägger du till den `Calendars.Read` delegerade behörigheten till program registrerings informationen. Lägg sedan till `Calendars.Read` omfånget i `acquireTokenSilent` anropet.

> [!NOTE]
> Användare kan tillfrågas om ytterligare medgivanden när du ökar antalet omfång.

## <a name="known-issues"></a>Kända problem

### <a name="issue-1"></a>Problem 1

Du får ett av följande fel meddelanden när du loggar in på ditt program på en federerad Azure AD-domän:

* "Det gick inte att hitta något giltigt klient certifikat i begäran."
* "Inga giltiga certifikat hittades i användarens certifikat arkiv."
* "Försök igen att välja en annan autentiseringsmetod."

**Orsak:** Företags-och certifikat funktioner är inte aktiverade.

**Lösning:** Följ stegen i [Aktivera integrerad autentisering på federerade domäner (valfritt)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problem 2

Du aktiverar [integrerad autentisering på federerade domäner](#enable-integrated-authentication-on-federated-domains-optional) och försöker använda Windows Hello på en Windows 10-dator för att logga in i en miljö med multifaktorautentisering konfigurerad. Listan över certifikat visas. Om du väljer att använda din PIN-kod visas inte PIN-fönstret.

**Orsak:** Det här problemet är en känd begränsning i Web Authentication Broker i UWP-program som körs på Windows 10-datorer. Det fungerar bra på Windows 10 Mobile.

**Lösning:** Välj **Logga in med andra alternativ**. Välj sedan **Logga in med ett användar namn och lösen ord**. Välj **Ange ditt lösen ord**. Gå sedan igenom processen för att autentisera telefonen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att använda Microsoft Authentication Library (MSAL) för auktorisering och autentisering i .NET-program:

> [!div class="nextstepaction"]
> [Översikt över Microsoft Authentication Library (MSAL)](msal-overview.md)
