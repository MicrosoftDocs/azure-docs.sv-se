---
title: Initiera klient program för MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att initiera offentliga klient program och konfidentiella klient program med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/18/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6616b0573019703f287d3be521569f5953aeb032
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600294"
---
# <a name="initialize-client-applications-using-msalnet"></a>Initiera klient program med MSAL.NET
I den här artikeln beskrivs hur du initierar offentliga klient program och konfidentiella klient program med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).  Läs [översikten](msal-client-applications.md)om du vill veta mer om klient program typer och program konfigurations alternativ.

Med MSAL.NET 3. x är det rekommenderade sättet att instansiera ett program med hjälp av program byggare: `PublicClientApplicationBuilder` och `ConfidentialClientApplicationBuilder` . De erbjuder en kraftfull mekanism för att konfigurera programmet antingen från koden eller från en konfigurations fil, eller till och med genom att kombinera båda metoderna.

## <a name="prerequisites"></a>Förutsättningar
Innan du initierar ett program måste du först [registrera det](quickstart-register-app.md) så att din app kan integreras med Microsoft Identity Platform.  Efter registreringen kan du behöva följande information (som du hittar i Azure Portal):

- Klient-ID (en sträng som representerar ett GUID)
- Identitets leverantörens URL (kallas instansen) och inloggnings gruppen för ditt program. Dessa två parametrar kallas gemensamt för utfärdaren.
- Klient-ID: t om du skriver ett affärs program enbart för din organisation (även kallat program med en enda klient).
- Program hemligheten (klientens hemliga sträng) eller certifikat (av typen X509Certificate2) om det är ett konfidentiellt klient program.
- För webbappar, och ibland för offentliga klient program (särskilt när appen behöver använda en Service Broker), har du också angett redirectUri där identitets leverantören ska kontakta ditt program igen med säkerhetstoken.

## <a name="ways-to-initialize-applications"></a>Sätt att initiera program
Det finns många olika sätt att instansiera klient program.

### <a name="initializing-a-public-client-application-from-code"></a>Initiera ett offentligt klient program från kod

Följande kod instansierar ett offentligt klient program, inloggnings användare i det Microsoft Azure offentliga molnet, med sina arbets-och skol konton eller deras personliga Microsoft-konton.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Initiera ett konfidentiellt klient program från kod

På samma sätt instansierar följande kod ett konfidentiellt program (en webbapp som finns på) som `https://myapp.azurewebsites.net` hanterar tokens från användare i det Microsoft Azure offentliga molnet, med sina arbets-och skol konton eller deras personliga Microsoft-konton. Programmet identifieras med identitets leverantören genom att dela en klient hemlighet:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Som du kanske vill, i produktion, i stället för att använda en klient hemlighet, kanske du vill dela med ett certifikat från Azure AD. Koden skulle då vara följande:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Initiera ett offentligt klient program från konfigurations alternativ

Följande kod instansierar ett offentligt klient program från ett konfigurations objekt, som kan fyllas i program mässigt eller läses från en konfigurations fil:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Initiera ett konfidentiellt klient program från konfigurations alternativ

Samma typ av mönster gäller för konfidentiella klient program. Du kan också lägga till andra parametrar med `.WithXXX` modifierare (här ett certifikat).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Builder-modifierare

I kodfragmenten med hjälp av program byggare kan ett antal `.With` metoder användas som modifierare (till exempel `.WithCertificate` och `.WithRedirectUri` ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Ändringar som är gemensamma för offentliga och konfidentiella klient program

De modifierare som du kan ställa in på en offentlig klient eller en konfidentiell klient Application Builder är:

|Modifieraren | Beskrivning|
|--------- | --------- |
|`.WithAuthority()` 7 åsidosättningar | Ställer in program standard auktoritet till en Azure AD-auktoritet, med möjligheten att välja Azure-moln, mål grupp, klient organisation (klient-ID eller domän namn) eller tillhandahålla direkt auktoritets-URI: n.|
|`.WithAdfsAuthority(string)` | Anger att program standard utfärdaren ska vara en ADFS-auktoritet.|
|`.WithB2CAuthority(string)` | Anger att programmets standard auktoritet ska vara en Azure AD B2C utfärdare.|
|`.WithClientId(string)` | Åsidosätter klient-ID: t.|
|`.WithComponent(string)` | Anger namnet på biblioteket med hjälp av MSAL.NET (för telemetri skäl). |
|`.WithDebugLoggingCallback()` | Om det här anropas anropar programmet `Debug.Write` helt enkelt genom att aktivera fel söknings spårning. Se [loggning](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) för mer information.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Ange ytterligare frågeparametrar för program nivån som ska skickas i alla autentiseringsbegäranden. Detta är åsidosättningsbar på varje nivå för token för hämtning (med samma `.WithExtraQueryParameters pattern` ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Aktiverar avancerade scenarier som att konfigurera för en HTTP-proxy eller tvinga MSAL att använda en viss HttpClient (till exempel i ASP.NET Core Web Apps/API: er).|
|`.WithLogging()` | Om det anropas anropar programmet ett återanrop med fel söknings spår. Se [loggning](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) för mer information.|
|`.WithRedirectUri(string redirectUri)` | Åsidosätter standardvärdet för omdirigerings-URI. Om det gäller offentliga klient program är detta användbart för scenarier som involverar Service Broker.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Anger det ombud som används för att skicka telemetri.|
|`.WithTenantId(string tenantId)` | Åsidosätter klient-ID eller klient beskrivning.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modifierare som är speciell för Xamarin. iOS-program

De modifierare som du kan ställa in i en offentlig klient programs Builder på Xamarin. iOS är:

|Modifieraren | Beskrivning|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin. iOS**: anger säkerhets gruppen för iOS-nyckelpar (för cacheminnet).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modifierare som är speciella för konfidentiella klient program

De modifierare som du kan ställa in i ett konfidentiellt klient program verktyg är:

|Modifieraren | Beskrivning|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Anger det certifikat som identifierar programmet med Azure AD.|
|`.WithClientSecret(string clientSecret)` | Anger klient hemligheten (app Password) som identifierar programmet med Azure AD.|

Dessa modifierare kan inte anges samtidigt. Om du anger båda kommer MSAL att utlösa ett meningsfullt undantag.

### <a name="example-of-usage-of-modifiers"></a>Exempel på användning av modifierare

Vi antar att ditt program är ett affärs program som endast gäller för din organisation.  Sedan kan du skriva:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Var den blir intressant är att programmering för nationella moln nu har förenklats. Om du vill att programmet ska vara ett program med flera klienter i ett nationellt moln kan du skriva, till exempel:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Det finns också en åsidosättning för ADFS (ADFS 2019 stöds inte för närvarande):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Slutligen, om du är Azure AD B2C utvecklare, kan du ange din klient organisation så här:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

## <a name="next-steps"></a>Nästa steg

När du har initierat klient programmet är nästa uppgift att lägga till stöd för användar inloggning, auktoriserad API-åtkomst eller både och.

Vår dokumentation om program scenario ger vägledning för att logga in en användare och förvärva en åtkomsttoken för att få åtkomst till ett API för den användarens räkning:

- [Webbapp som loggar in användare: inloggning och utloggning](scenario-web-app-sign-user-sign-in.md)
- [Webbapp som anropar webb-API: er: Hämta en token](scenario-web-app-call-api-acquire-token.md)
