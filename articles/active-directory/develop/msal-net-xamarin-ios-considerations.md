---
title: Xamarin iOS-överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder Xamarin iOS med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 62eb4ab9eb6e4b0e7be0f7aadae1173950d21615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064495"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Att tänka på när du använder Xamarin iOS med MSAL.NET

När du använder Microsoft Authentication Library för .NET (MSAL.NET) på Xamarin iOS bör du:

- Åsidosätt och implementera `OpenUrl` funktionen i `AppDelegate` .
- Aktivera nyckel rings grupper.
- Aktivera delning av token cache.
- Aktivera nyckel rings åtkomst.
- Förstå kända problem med iOS 12 och iOS 13 och autentisering.

## <a name="implement-openurl"></a>Implementera OpenUrl

Åsidosätt `OpenUrl` metoden för den `FormsApplicationDelegate` härledda klassen och anropet `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Här är ett exempel:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Utför även följande uppgifter:

* Definiera ett omdirigerings-URI-schema.
* Kräv att appen ska anropa en annan app.
* Ha ett enskilt formulär för omdirigerings-URI: n.
* [Registrera en omdirigerings-URI](quickstart-register-app.md#add-a-redirect-uri) i Azure Portal.

### <a name="enable-keychain-access"></a>Aktivera nyckel rings åtkomst

Om du vill aktivera nyckel rings åtkomst kontrollerar du att programmet har en åtkomst grupp för nyckel ringar. Du kan ställa in åtkomst gruppen för nyckel ringar när du skapar programmet med hjälp av `WithIosKeychainSecurityGroup()` API: et.

Om du vill dra nytta av cachen och enkel inloggning (SSO) anger du åtkomst gruppen för nyckel ringar till samma värde i alla dina program.

I det här exemplet i installations programmet används MSAL 4. x:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Aktivera också nyckel rings åtkomst i `Entitlements.plist` filen. Använd antingen följande åtkomst grupp eller din egen åtkomst grupp.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

När du använder `WithIosKeychainSecurityGroup()` API: et lägger MSAL automatiskt till din säkerhets grupp i slutet av programmets *Team-ID* ( `AppIdentifierPrefix` ). MSAL lägger till din säkerhets grupp eftersom när du skapar ditt program i Xcode. Det är därför som rättigheterna i `Entitlements.plist` filen måste inkluderas `$(AppIdentifierPrefix)` före åtkomst gruppen för nyckel ringar.

Mer information finns i dokumentationen om [iOS-rättigheter](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps).

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Aktivera delning av token cache i iOS-program

Från och med MSAL 2. x kan du ange en åtkomst grupp för nyckel ringar för att bevara token-cachen över flera program. Med den här inställningen kan du dela token-cachen mellan flera program som har samma åtkomst grupp för nyckel ringar. Du kan dela token-cachen bland [ADAL.net](https://aka.ms/adal-net) -program, MSAL.net Xamarin. iOS-program och inbyggda iOS-program som har utvecklats i [ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) eller [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Genom att dela token cache kan du tillåta enkel inloggning (SSO) bland alla program som använder samma nyckel rings åtkomst grupp.

Om du vill aktivera denna cache-delning använder du `WithIosKeychainSecurityGroup()` metoden för att ange åtkomst gruppen för nyckel ringar till samma värde i alla program som delar samma cacheminne. Det första kod exemplet i den här artikeln visar hur du använder-metoden.

Tidigare i den här artikeln har du lärt dig att MSAL läggs till `$(AppIdentifierPrefix)` när du använder `WithIosKeychainSecurityGroup()` API: et. MSAL lägger till det här elementet eftersom Team-ID: t `AppIdentifierPrefix` ser till att endast program som har skapats av samma utgivare kan dela nyckel ringar.

> [!NOTE]
> `KeychainSecurityGroup`Egenskapen är föråldrad. Använd `iOSKeychainSecurityGroup` egenskapen i stället. `TeamId`Prefixet krävs inte när du använder `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>Använd Microsoft Authenticator

Programmet kan använda Microsoft Authenticator som en koordinator för att aktivera:

- **SSO**: när du aktiverar SSO behöver användarna inte logga in på varje program.
- **Enhets identifiering**: Använd enhets identifiering för att autentisera genom att komma åt enhets certifikatet. Det här certifikatet skapas på enheten när det är anslutet till arbets platsen. Programmet är redo om klient administratörerna aktiverar villkorlig åtkomst för enheterna.
- **Verifiering av program identifiering**: när ett program anropar Service Broker skickas dess omdirigerings-URL. Service Broker verifierar omdirigerings-URL: en.

Mer information om hur du aktiverar en Service Broker finns i [använda Microsoft Authenticator eller Microsoft Intune företagsportal på Xamarin iOS-och Android-program](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Kända problem med iOS 12 och autentisering

Microsoft har publicerat en [säkerhets rekommendation](https://github.com/aspnet/AspNetCore/issues/4647) om inkompatibilitet mellan iOS 12 och vissa typer av autentisering. Inkompatibiliteten bryter mot sociala, WSFed och OIDC inloggningar. Säkerhets rekommendationen hjälper dig att förstå hur du tar bort ASP.NET säkerhets begränsningar från dina program så att de blir kompatibla med iOS 12.

När du utvecklar MSAL.NET-program på Xamarin iOS kan du se en oändlig loop när du försöker logga in på webbplatser från iOS 12. Detta beteende liknar detta ADAL-problem på GitHub: [oändlig loop vid försök att logga in på webbplatsen från iOS 12 #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329).

Du kan också se en rast i ASP.NET Core OIDC-autentisering med iOS 12 Safari. Mer information finns i det här [webkit-problemet](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="known-issues-with-ios-13-and-authentication"></a>Kända problem med iOS 13 och autentisering

Om din app kräver villkorlig åtkomst eller stöd för certifikatautentisering, så kan appen kommunicera med Microsoft Authenticator Broker-appen. MSAL ansvarar sedan för att hantera begär Anden och svar mellan ditt program och Microsoft Authenticator.

På iOS 13 gjorde Apple en brytande API-ändring genom att ta bort programmets möjlighet att läsa käll programmet när du tar emot ett svar från ett externt program via anpassade URL-scheman.

Apples dokumentation för [UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) -tillstånd:

> *Om begäran kommer från en annan app som tillhör ditt team, ställer UIKit in värdet för den här nyckeln till appens ID. Om Team identifieraren för den ursprungliga appen skiljer sig från Team-ID: n för den aktuella appen är värdet för nyckeln Nil.*

Den här ändringen delas upp för MSAL eftersom den förlitar `UIApplication.SharedApplication.OpenUrl` sig på att verifiera kommunikationen mellan MSAL och Microsoft Authenticator-appen.

I iOS 13 krävs dessutom utvecklaren för att tillhandahålla en presentations kontrollant när du använder `ASWebAuthenticationSession` .

Din app påverkas om du skapar med Xcode 11 och använder antingen iOS-Broker eller `ASWebAuthenticationSession` .

I sådana fall använder du [MSAL.NET 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) för att aktivera lyckad autentisering.

### <a name="additional-requirements"></a>Ytterligare krav

- När du använder de senaste MSAL-biblioteken bör du se till att **Microsoft Authenticator version 6.3.19 +** är installerad på enheten.
- När du uppdaterar till MSAL.NET 4.4.0 + uppdaterar du `LSApplicationQueriesSchemes` i filen *info. plist* och lägger till `msauthv3` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    Lägger till `msauthv3` i *info. plist* krävs för att identifiera förekomsten av den senaste Microsoft Authenticator-appen på enheten som stöder iOS 13.

## <a name="report-an-issue"></a>Rapportera ett problem

Om du har frågor eller vill rapportera ett problem som du har hittat i MSAL.NET, öppnar du ett problem i lagrings platsen [AzureAD/Microsoft-Authentication-Library-for-dotNet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) på GitHub.

## <a name="next-steps"></a>Nästa steg

Information om egenskaper för Xamarin iOS finns i stycket om [iOS-/regionsspecifika överväganden](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) i följande exempel: s Readme.MD-fil:

Exempel | Plattform | Beskrivning
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Universell Windows-plattform (UWP) | En enkel Xamarin Forms-app som visar hur du använder MSAL för att autentisera Microsoft-personliga konton och Azure AD via Azure AD 2,0-slutpunkten. Appen visar också hur du använder den resulterande token för att få åtkomst till Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
