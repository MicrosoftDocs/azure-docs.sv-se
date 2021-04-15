---
title: Använda webbläsare (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om specifika överväganden när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.openlocfilehash: 4121d4b9ac73ed18da7dce0e397fe919589ac6f0
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478768"
---
# <a name="using-web-browsers-msalnet"></a>Använda webbläsare (MSAL.NET)

Webbläsare krävs för interaktiv autentisering. Som standard MSAL.NET systemets [webbläsare på](#system-web-browser-on-xamarinios-xamarinandroid) Xamarin.iOS och Xamarin.Android. Men [du kan också](#enable-embedded-webviews-on-ios-and-android) aktivera den inbäddade webbläsaren beroende på dina krav (UX, behov av enkel inloggning (SSO), säkerhet) i [Xamarin.iOS-](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) och [Xamarin.Android-appar.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) Du kan även [välja dynamiskt vilken webbläsare](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) som ska användas baserat på förekomsten av Chrome eller en webbläsare som stöder anpassade Chrome-flikar i Android. MSAL.NET stöder endast systemwebbläsaren i .NET Core-skrivbordsprogram.

## <a name="web-browsers-in-msalnet"></a>Webbläsare i MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interaktion sker i en webbläsare

Det är viktigt att förstå att när du hämtar en token interaktivt så tillhandahålls inte innehållet i dialogrutan av biblioteket utan av STS (Security Token Service). Autentiseringsslutpunkten skickar tillbaka viss HTML- och JavaScript-kod som styr interaktionen, som återges i en webbläsare eller webbkontroll. Att låta STS hantera HTML-interaktionen har många fördelar:

- Lösenordet (om det har skrivits) lagras aldrig av programmet eller autentiseringsbiblioteket.
- Aktiverar omdirigeringar till andra identitetsproviders (till exempel inloggning med ett arbets skolkonto eller ett personligt konto med MSAL eller med ett socialt konto med Azure AD B2C).
- Låter STS styra villkorlig åtkomst, till exempel genom att låta användaren göra multifaktorautentisering [(MFA)](../authentication/concept-mfa-howitworks.md) under autentiseringsfasen (ange en PIN-kod för Windows Hello eller bli upp anropad på telefonen eller i en autentiseringsapp på telefonen). I de fall där nödvändig multifaktorautentisering inte har ställts in ännu kan användaren konfigurera just-in-time i samma dialogruta.  Användaren anger sitt mobiltelefonnummer och vägleds att installera ett autentiseringsprogram och skanna en QR-tagg för att lägga till sitt konto. Den här serverdrivna interaktionen är en bra upplevelse!
- Låter användaren ändra sitt lösenord i samma dialogruta när lösenordet har upphört att gälla (ange ytterligare fält för det gamla lösenordet och det nya lösenordet).
- Aktiverar profilering av klientorganisationen eller programmet (avbildningar) som styrs av Azure AD-klientadministratören/programägaren.
- Gör det möjligt för användarna att godkänna att programmet får åtkomst till resurser/omfång i sina namn precis efter autentiseringen.

### <a name="embedded-vs-system-web-ui"></a>Inbäddat användargränssnitt jämfört med systemwebbgränssnitt

MSAL.NET är ett bibliotek med flera ramverk och har ramverksspecifik kod som värd för en webbläsare i en UI-kontroll (på .NET Classic används t.ex. WinForms, på Xamarin används interna mobila kontroller osv.). Den här kontrollen kallas `embedded` webbgränssnitt. Alternativt kan MSAL.NET också starta systemoperativsystemets webbläsare.

I allmänhet rekommenderar vi att du använder plattformens standardinställning, och detta är vanligtvis systemwebbläsaren. Systemwebbläsaren är bättre på att komma ihåg de användare som har loggat in tidigare. Om du vill ändra det här beteendet använder du `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>I korthet

| Ramverk        | Inbäddat | System | Standardvärde |
| ------------- |-------------| -----| ----- |
| Klassisk .NET     | Ja | Ja^ | Inbäddat |
| .NET Core     | Inga | Ja^ | System |
| .NET Standard | Inga | Ja^ | System |
| UWP | Ja | Inga | Inbäddat |
| Xamarin.Android | Ja | Ja  | System |
| Xamarin.iOS | Ja | Ja  | System |
| Xamarin.Mac| Ja | Inga | Inbäddat |

^ Kräver " http://localhost " omdirigerings-URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systemwebbläsare på Xamarin.iOS, Xamarin.Android

Som standard MSAL.NET systemets webbläsare på Xamarin.iOS, Xamarin.Android och .NET Core. För alla plattformar som tillhandahåller användargränssnitt (det vill säga inte .NET Core) tillhandahålls en dialogruta av biblioteket som bäddar in en webbläsarkontroll. MSAL.NET använder också en inbäddad webbvy för .NET Desktop och WAB för UWP-plattformen. Men som standard används **systemwebbläsaren för** Xamarin iOS- och Xamarin Android-program. I iOS väljer den även den webbvy som ska användas beroende på operativsystemsversionen (iOS12, iOS11 och tidigare).

Att använda systemwebbläsaren har den stora fördelen med att dela SSO-tillståndet med andra program och med webbprogram utan att behöva en koordinator (företagsportalen/Authenticator). Systemwebbläsaren användes som standard i MSAL.NET för Xamarin iOS- och Xamarin Android-plattformarna eftersom systemets webbläsare tar upp hela skärmen på dessa plattformar och användarupplevelsen är bättre. Systemets webbvy kan inte skiljas från en dialogruta. I iOS kan användaren dock behöva ge sitt medgivande till att webbläsaren anropar programmet igen, vilket kan vara besvärligt.

## <a name="system-browser-experience-on-net"></a>Systemwebbläsare på .NET 

På .NET Core MSAL.NET du systemwebbläsaren som en separat process. MSAL.NET har inte kontroll över den här webbläsaren, men när användaren är klar med autentiseringen omdirigeras webbsidan på ett sådant sätt att MSAL.NET kan fånga upp URI:en.

Du kan också konfigurera appar som skrivits för .NET Classic eller .NET 5 att använda den här webbläsaren genom att ange:

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET kan inte identifiera om användaren navigerar bort eller bara stänger webbläsaren. Appar som använder den här tekniken uppmuntras att definiera en tidsgräns (via `CancellationToken` ). Vi rekommenderar en tidsgräns på minst några minuter för att ta hänsyn till fall där användaren uppmanas att ändra lösenord eller utföra multifaktorautentisering.

### <a name="how-to-use-the-default-os-browser"></a>Så här använder du standardoperativsystemwebbläsaren

MSAL.NET lyssna på och fånga upp koden som AAD skickar när användaren är autentiserad (mer information finns i `http://localhost:port` Auktoriseringskod) [](v2-oauth2-auth-code-flow.md)

Så här aktiverar du systemwebbläsaren:

1. Under appregistreringen konfigurerar `http://localhost` du som en omdirigerings-URI (stöds för närvarande inte av B2C)
2. När du skapar publicClientApplication anger du den här omdirigerings-URI:en:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Om du `http://localhost` konfigurerar hittar MSAL.NET en slumpmässig öppen port och använder den.

### <a name="linux-and-mac"></a>Linux och MAC

I Linux MSAL.NET standardwebbläsaren för operativsystemet med verktyget xdg-open. Om du vill felsöka kör du verktyget från en terminal, till exempel `xdg-open "https://www.bing.com"` . På Mac öppnas webbläsaren genom att du anropar `open <url>` .

### <a name="customizing-the-experience"></a>Anpassa upplevelsen

> [!NOTE]
> Anpassning är tillgänglig i MSAL.NET 4.1.0 eller senare.

MSAL.NET kan svara med ett HTTP-meddelande när en token tas emot eller vid fel. Du kan visa ett HTML-meddelande eller omdirigera till valfri URL:

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

### <a name="opening-a-specific-browser-experimental"></a>Öppna en specifik webbläsare (experimentell)

Du kan anpassa hur MSAL.NET öppnar webbläsaren. I stället för att använda den webbläsare som är standard kan du tvinga fram en specifik webbläsare:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP använder inte systemwebbvyn

För skrivbordsprogram leder dock start av en systemwebbvy till en underanvändarupplevelse, som användaren ser i webbläsaren, där de kanske redan har andra flikar öppna. Och när autentiseringen har skett får användarna en sida där de uppmanas att stänga det här fönstret. Om användaren inte är uppmärksam kan han eller hon stänga hela processen (inklusive andra flikar som inte är relaterade till autentiseringen). Att använda systemwebbläsaren på skrivbordet kräver också att du öppnar lokala portar och lyssnar på dem, vilket kan kräva avancerade behörigheter för programmet. Som utvecklare, användare eller administratör kanske du är ovillig när det gäller det här kravet.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Aktivera inbäddade webbvyer på iOS och Android

Du kan också aktivera inbäddade webbvyer i Xamarin.iOS- och Xamarin.Android-appar. Från och MSAL.NET 2.0.0-förhandsversion har MSAL.NET också stöd för att använda **det inbäddade** webbvyalternativet. För ADAL.NET är inbäddad webbvy det enda alternativ som stöds.

Som utvecklare med MSAL.NET med Xamarin som mål kan du välja att använda antingen inbäddade webbvyer eller systemwebbläsare. Det här är ditt val beroende på användarupplevelsen och säkerhetsproblemen som du vill rikta in dig på.

För närvarande MSAL.NET inte stöd för Android- och iOS-brokers. För att tillhandahålla enkel inloggning (SSO) kan därför systemwebbläsaren fortfarande vara ett bättre alternativ. Stöd för a brokers med den inbäddade webbläsaren finns i MSAL.NET eftersläpning.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Skillnader mellan inbäddad webbvy och systemwebbläsare
Det finns vissa visuella skillnader mellan inbäddad webbvy och systemwebbläsare i MSAL.NET.

**Interaktiv inloggning med MSAL.NET embedded-webbvyn:**

![inbäddad](media/msal-net-web-browsers/embedded-webview.png)

**Interaktiv inloggning med MSAL.NET i System Browser:**

![Systemwebbläsare](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Alternativ för utvecklare

Som utvecklare med MSAL.NET har du flera alternativ för att visa den interaktiva dialogrutan från STS:

- **Systemwebbläsare.** Systemwebbläsaren anges som standard i biblioteket. Om du använder Android läser [du systemwebbläsare](msal-net-system-browser-android-considerations.md) för specifik information om vilka webbläsare som stöds för autentisering. När du använder systemwebbläsaren i Android rekommenderar vi att enheten har en webbläsare som stöder anpassade Chrome-flikar.  Annars kan autentiseringen misslyckas.
- **Inbäddad webbvy.** Om du bara vill använda inbäddad webbvy MSAL.NET innehåller `AcquireTokenInteractively` parameterverktyget en `WithUseEmbeddedWebView()` -metod.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Välja mellan inbäddad webbläsare eller systemwebbläsare i Xamarin.iOS

I din iOS-app `AppDelegate.cs` i kan du initiera till `ParentWindow` `null` . Den används inte i iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Välja mellan inbäddad webbläsare eller systemwebbläsare på Xamarin.Android

I Din Android-app i `MainActivity.cs` kan du ange den överordnade aktiviteten så att autentiseringsresultatet kommer tillbaka till den:

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

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Identifiera förekomsten av anpassade flikar på Xamarin.Android

Om du vill använda systemets webbläsare för att aktivera enkel inloggning med de appar som körs i webbläsaren, men är orolig för användarupplevelsen för Android-enheter som inte har en webbläsare med stöd för anpassad flik, kan du välja genom att anropa `IsSystemWebViewAvailable()` metoden i `IPublicClientApplication` . Den här `true` metoden returnerar om PackageManager identifierar anpassade `false` flikar och om de inte identifieras på enheten.

Baserat på värdet som returneras av den här metoden och dina krav kan du fatta ett beslut:

- Du kan returnera ett anpassat felmeddelande till användaren. Exempel: "Installera Chrome för att fortsätta med autentisering" -OR-
- Du kan gå tillbaka till det inbäddade webbvyalternativet och starta användargränssnittet som en inbäddad webbvy.

Koden nedan visar det inbäddade webbvyalternativet:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core stöder inte interaktiv autentisering med en inbäddad webbläsare

För .NET Core är interaktivt anskaffning av token endast tillgängligt via systemets webbläsare, inte med inbäddade webbvyer. .NET Core tillhandahåller inte något användargränssnitt ännu.
Om du vill anpassa webbläsarupplevelsen med systemets webbläsare kan du implementera [gränssnittet IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) och även tillhandahålla en egen webbläsare.
