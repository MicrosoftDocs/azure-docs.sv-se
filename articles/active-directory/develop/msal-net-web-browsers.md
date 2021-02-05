---
title: Använda webbläsare (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 23ba50a6eca1e398b9d459153b84719909f2ecac
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583747"
---
# <a name="using-web-browsers-msalnet"></a>Använda webbläsare (MSAL.NET)

Webbläsaren krävs för interaktiv autentisering. Som standard har MSAL.NET stöd för [system webbläsare](#system-web-browser-on-xamarinios-xamarinandroid) på Xamarin. iOS och Xamarin. Android. Men [du kan också aktivera den inbäddade webbläsaren](#enable-embedded-webviews-on-ios-and-android) beroende på dina krav (UX, behov av enkel inloggning (SSO), säkerhet) i [Xamarin. iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) och [Xamarin. Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) -appar. Du kan även [välja dynamiskt](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) vilken webbläsare som ska användas baserat på förekomsten av Chrome eller en webbläsare som stöder Chrome anpassade flikar i Android. MSAL.NET stöder endast system webbläsare i .NET Core Desktop-program.

## <a name="web-browsers-in-msalnet"></a>Webbläsare i MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interaktionen sker i en webbläsare

Det är viktigt att förstå att när du hämtar en token interaktivt, har innehållet i dialog rutan inte tillhandahållits av biblioteket, men av STS (Security Token Service). Autentiseringens slut punkt skickar tillbaka viss HTML och Java Script som styr interaktionen, som återges i en webbläsare eller webb kontroll. Att tillåta STS att hantera HTML-interaktionen har många fördelar:

- Lösen ordet (om ett sådant har skrivits) lagras aldrig av programmet, eller med autentiseringsläget.
- Aktiverar omdirigering till andra identitets leverantörer (till exempel inloggning med ett arbets skol konto eller ett personligt konto med MSAL, eller med ett socialt konto med Azure AD B2C).
- Tillåter STS-kontroll av villkorlig åtkomst, till exempel genom att användaren utför [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) under autentiseringsläget (genom att ange en Windows Hello-PIN-kod eller anropas på deras telefon eller i en webbapp på telefonen). I de fall där den nödvändiga Multi-Factor Authentication inte har kon figurer ATS ännu, kan användaren konfigurera den precis i tid i samma dialog ruta.  Användaren anger sitt mobiltelefon nummer och vägleder dig för att installera ett program för autentisering och skanna en QR-tagg för att lägga till sitt konto. Den här server drivna interaktionen är en bra upplevelse!
- Låter användaren ändra sitt lösen ord i den här dialog rutan när lösen ordet har upphört att gälla (vilket ger ytterligare fält för det gamla lösen ordet och det nya lösen ordet).
- Aktiverar varumärkes innehavare, eller programmet (avbildningar) som styrs av Azure AD-klientens administratör/program ägare.
- Gör det möjligt för användarna att tillåta åtkomst till resurserna/omfattningarna i programmet, precis efter autentiseringen.

### <a name="embedded-vs-system-web-ui"></a>Webb gränssnitt för Embedded vs-systemet

MSAL.NET är ett bibliotek med flera ramverk och har en Framework-specifik kod som är värd för en webbläsare i en GRÄNSSNITTs kontroll (till exempel på den klassiska .NET-IT använder WinForms, på Xamarin den använder sig av inbyggda mobila kontroller osv.). Den här kontrollen kallas `embedded` webb gränssnitt. Du kan också starta MSAL.NET i systemets OS-webbläsare.

I allmänhet rekommenderar vi att du använder plattforms standarden och det är vanligt vis system läsaren. System läsaren är bättre för att komma ihåg vilka användare som har loggat in tidigare. Om du vill ändra det här beteendet använder du `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>I korthet

| Ramverk        | Inbäddat | System | Standardvärde |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Ja | Ja ^ | Inbäddat |
| .NET Core     | Inga | Ja ^ | System |
| .NET Standard | Inga | Ja ^ | System |
| UWP | Ja | Inga | Inbäddat |
| Xamarin.Android | Ja | Ja  | System |
| Xamarin.iOS | Ja | Ja  | System |
| Xamarin. Mac| Ja | Inga | Inbäddat |

^ Kräver " http://localhost " omdirigerings-URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>System webbläsare på Xamarin. iOS, Xamarin. Android

Som standard har MSAL.NET stöd för system webbläsare på Xamarin. iOS, Xamarin. Android och .NET Core. För alla plattformar som tillhandahåller användar gränssnitt (dvs. inte .NET Core) tillhandahålls en dialog ruta av biblioteket som bäddar in en webb läsar kontroll. MSAL.NET använder också en inbäddad webbvy för .NET Desktop och WAB för UWP-plattformen. Det utnyttjar dock som standard **system webbläsare** för Xamarin iOS-och Xamarin Android-program. På iOS väljer den även den webbvy som ska användas beroende på operativ systemets version (iOS12, iOS11 och tidigare).

Att använda system webbläsare har stor nytta av att dela SSO-tillstånd med andra program och med webb program utan att behöva en Service Broker (företags Portal/autentiserare). System läsaren användes som standard i MSAL.NET för Xamarin iOS-och Xamarin Android-plattformarna på grund av att på dessa plattformar upptar system webbläsarens hela skärm och användar upplevelsen är bättre. Det går inte att skilja i system webb visningen från en dialog ruta. På iOS kan användaren, om användaren måste ge sitt medgivande för att webbläsaren ska kunna ringa tillbaka programmet, vilket kan vara irriterande.

## <a name="system-browser-experience-on-net-core"></a>System webbläsarens upplevelse av .NET Core

I .NET Core startar MSAL.NET system läsaren som en separat process. MSAL.NET har inte kontroll över den här webbläsaren, men när användaren har slutfört autentiseringen omdirigeras webb sidan på ett sådant sätt att MSAL.NET kan fånga upp URI: n.

Du kan också konfigurera appar som skrivits för .NET Classic för att använda den här webbläsaren genom att ange

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET kan inte identifiera om användaren navigerar bort eller bara stänger webbläsaren. Appar som använder den här metoden uppmuntras att definiera en tids gräns (via `CancellationToken` ). Vi rekommenderar en tids gräns på minst några minuter, för att ta hänsyn till de fall där användaren uppmanas att ändra lösen ordet eller utföra Multi-Factor-Authentication.

### <a name="how-to-use-the-default-os-browser"></a>Använda standard webbläsaren för OS

MSAL.NET måste lyssna på `http://localhost:port` och avlyssna koden som AAD skickar när användaren är färdig med autentisering (se [auktoriseringskod](v2-oauth2-auth-code-flow.md) för information)

Så här aktiverar du systemets webbläsare:

1. Under registrering av appar konfigurerar du `http://localhost` som en omdirigerings-URI (stöds för närvarande inte av B2C)
2. När du skapar din PublicClientApplication anger du denna omdirigerings-URI:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Om du konfigurerar `http://localhost` kommer internt MSAL.net att hitta en slumpmässig öppen port och använda den.

### <a name="linux-and-mac"></a>Linux och MAC

I Linux öppnar MSAL.NET standard webbläsaren för operativ systemet med hjälp av xdg-Open-verktyget. Du kan felsöka genom att köra verktyget från en Terminal, till exempel `xdg-open "https://www.bing.com"` . I Mac öppnas webbläsaren genom att anropa `open <url>` .

### <a name="customizing-the-experience"></a>Anpassa upplevelsen

> [!NOTE]
> Anpassning är tillgänglig i MSAL.NET 4.1.0 eller senare.

MSAL.NET kan svara med ett HTTP-meddelande när en token tas emot eller om det är fel. Du kan visa ett HTML-meddelande eller omdirigera till en valfri URL:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Öppna en speciell webbläsare (experimentell)

Du kan anpassa hur MSAL.NET öppnar webbläsaren. I stället för att använda den webbläsare som är standard kan du tvinga öppna en speciell webbläsare:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP använder inte systemets webbvy

För Skriv bords program kan dock en system WebView leda till en subpar användar upplevelse, eftersom användaren ser webbläsaren, där de redan har öppnat andra flikar. Och när autentiseringen har skett får användarna en sida där de kan stänga det här fönstret. Om användaren inte betalar uppmärksamheten kan de stänga hela processen (inklusive andra flikar, som inte är relaterade till autentiseringen). Om du använder system läsaren på Skriv bordet måste du också öppna lokala portar och lyssna på dem, vilket kan kräva avancerade behörigheter för programmet. Du, som utvecklare, användare eller administratör, kan vara ovilliga om detta krav.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Aktivera inbäddade webbvyer på iOS och Android

Du kan också aktivera inbäddade webvyer i Xamarin. iOS och Xamarin. Android-appar. Från och med MSAL.NET 2.0.0 – för hands version stöder MSAL.NET även med hjälp av det **inbäddade** WebView-alternativet. För ADAL.NET är inbäddad webbvy det enda alternativ som stöds.

Som utvecklare som använder MSAL.NET Target Xamarin kan du välja att använda antingen inbäddade webbvyer eller system webbläsare. Det här är ditt val beroende på användar upplevelsen och de säkerhets problem som du vill använda.

MSAL.NET har för närvarande inte stöd för Android-och iOS-mäklare. För att tillhandahålla enkel inloggning (SSO) kan system läsaren ändå vara ett bättre alternativ. Det finns stöd för utjämnare med den inbäddade webbläsaren på MSAL.NET efter släpning.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Skillnader mellan inbäddad webbvy och systemets webbläsare
Det finns vissa visuella skillnader mellan inbäddad webbvy och systemets webbläsare i MSAL.NET.

**Interaktiv inloggning med MSAL.NET med hjälp av den inbäddade webbvy:**

![inbäddad](media/msal-net-web-browsers/embedded-webview.png)

**Interaktiv inloggning med MSAL.NET med hjälp av system webbläsare:**

![System webbläsare](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Alternativ för utvecklare

Som utvecklare som använder MSAL.NET har du flera alternativ för att visa den interaktiva dialog rutan från STS:

- **System webbläsare.** System webbläsaren ställs in som standard i biblioteket. Om du använder Android läser du [system webbläsare](msal-net-system-browser-android-considerations.md) för information om vilka webbläsare som stöds för autentisering. När du använder system webbläsaren i Android rekommenderar vi att enheten har en webbläsare som stöder Chrome-anpassade flikar.  Annars kan autentiseringen Miss lyckas.
- **Inbäddad webbvy.** Om du vill använda en inbäddad webbvy i MSAL.NET `AcquireTokenInteractively` innehåller parameter verktyget en `WithUseEmbeddedWebView()` metod.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Välja mellan en inbäddad webbläsare eller system webbläsare på Xamarin. iOS

I iOS-appen `AppDelegate.cs` kan du initiera `ParentWindow` till `null` . Den används inte i iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Välja mellan en inbäddad webbläsare eller system webbläsare på Xamarin. Android

I din Android-app, i `MainActivity.cs` kan du ange den överordnade aktiviteten, så att autentiseringen blir tillbaka till den:

```csharp
 App.ParentWindow = this;
```

Sedan i `MainPage.xaml.cs` :

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Identifiera förekomsten av anpassade flikar på Xamarin. Android

Om du vill använda system webbläsare för att aktivera SSO med appar som körs i webbläsaren, men oroar dig om användar upplevelsen för Android-enheter som inte har en webbläsare med stöd för anpassade flikar, har du möjlighet att välja genom att anropa- `IsSystemWebViewAvailable()` metoden i `IPublicClientApplication` . Den här metoden returnerar `true` om PackageManager identifierar anpassade flikar och `false` om de inte identifieras på enheten.

Utifrån det värde som returneras av den här metoden, och dina krav, kan du fatta ett beslut:

- Du kan returnera ett anpassat fel meddelande till användaren. Exempel: "installera Chrome för att fortsätta med autentisering"-eller-
- Du kan gå tillbaka till det inbäddade alternativet för webbvy och starta användar gränssnittet som en inbäddad webbvy.

I koden nedan visas alternativet inbäddad webbvy:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core stöder inte interaktiv autentisering med en inbäddad webbläsare

För .NET Core är hämtning av token interaktivt bara tillgängligt via system webbläsare, inte med inbäddade webbvyer. .NET Core tillhandahåller i själva verket inget användar gränssnitt än.
Om du vill anpassa webbläsarens utseende med system webbläsare, kan du implementera [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) -gränssnittet och till och med Ange din egen webbläsare.
