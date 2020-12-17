---
title: Azure AD B2C och MSAL.NET
titleSuffix: Microsoft identity platform
description: Att tänka på när du använder Azure AD B2C med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 15f65da83f14f43a7892d52c6a2ed4e08580d367
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614922"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Använda MSAL.NET för att logga in användare med sociala identiteter

Du kan använda MSAL.NET för att logga in användare med sociala identiteter genom att använda [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/overview.md). Azure AD B2C är byggd runt begreppet principer. I MSAL.NET anger du en princip som översätter för att tillhandahålla en utfärdare.

- När du instansierar det offentliga klient programmet måste du ange principen som en del av utfärdaren.
- När du vill tillämpa en princip anropar du en åsidosättning av `AcquireTokenInteractive` som accepterar `authority` parametern.

Den här artikeln gäller MSAL.NET 3. x. För MSAL.NET 2. x, se [Azure AD B2C information i MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) i MSAL.net wiki på GitHub.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Auktoritet för en Azure AD B2C klient och princip

Auktoritets formatet för Azure AD B2C är: `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` – Namnet på Azure AD B2C klient organisationen plus värden. Till exempel *contosob2c.b2clogin.com*.
- `tenant` – Domän namnet eller klient-ID: t för Azure AD B2C klient organisationen. Till exempel *contosob2c.onmicrosoft.com* eller ett GUID.
- `policyName` – Namnet på det användar flöde eller den anpassade principen som ska användas. Till exempel en registrerings-/inloggnings princip som *b2c_1_susi*.

Mer information om Azure AD B2C-myndigheter finns i [Ange omdirigerings-URL: er till b2clogin.com](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Instansiera programmet

Ange utfärdaren genom `WithB2CAuthority()` att anropa när du skapar programobjektet:

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Hämta en token för att tillämpa en princip

Att skaffa en token för ett Azure AD B2C-skyddat API i ett offentligt klient program kräver att du använder åsidosättningarna med en utfärdare:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

I föregående kodfragment:

- `policy` är en sträng som innehåller namnet på ditt Azure AD B2C användar flöde eller en anpassad princip (till exempel `PolicySignUpSignIn` ).
- `ParentActivityOrWindow` krävs för Android (aktiviteten) och är valfritt för andra plattformar som har stöd för ett överordnat användar gränssnitt som Windows på Microsoft Windows och UIViewController i iOS. Mer information om UI-dialogrutan finns i [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) på MSAL-wikin.
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` är en metod som söker efter ett konto för en specifik princip. Exempel:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
      foreach (var account in accounts)
      {
          string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
          if (userIdentifier.EndsWith(policy.ToLower()))
              return account;
      }
      return null;
  }
  ```

Att använda ett användar flöde eller en anpassad princip (till exempel att låta användaren redigera sin profil eller återställa sina lösen ord) utförs för tillfället genom att anropa `AcquireTokenInteractive` . För de här två principerna använder du inte resultatet för returnerat token/autentisering.

## <a name="profile-edit-policies"></a>Profil redigerings principer

Om du vill göra det möjligt för användarna att logga in med en social identitet och sedan redigera profilen, använder du Azure AD B2C Redigera profil princip.

Gör detta genom att anropa `AcquireTokenInteractive` med behörigheten för principen. Eftersom användaren redan är inloggad och har en aktiv cookie-session, använder `Prompt.NoPrompt` du för att förhindra att dialog rutan konto val visas.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
    try
    {
        var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                            .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                            .WithPrompt(Prompt.NoPrompt),
                            .WithB2CAuthority(App.AuthorityEditProfile)
                            .ExecuteAsync();
        DisplayBasicTokenInfo(authResult);
    }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Autentiseringsuppgifter för resurs ägar lösen ord (ROPC)

Mer information om ROPC-flödet finns i [Logga in med autentiseringsuppgifter för resurs ägarens lösen ord](v2-oauth-ropc.md).

ROPC-flödet **rekommenderas inte** eftersom det inte är säkert att fråga användaren om lösen ordet i ditt program. Mer information om det här problemet finns i [Vad är lösningen på det växande problemet med lösen ord?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Genom att använda användar namn/lösen ord i ett ROPC-flöde kan du offra flera saker:

- Kärn Tenets av modern identitet: lösen ordet kan behållas eller spelas upp eftersom den delade hemligheten kan fångas upp. Efter definition är ROPC inkompatibelt med lösen flöden utan lösen ord.
- Användare som behöver göra MFA kan inte logga in (eftersom det inte finns någon interaktion).
- Användare kan inte använda enkel inloggning (SSO).

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurera ROPC-flödet i Azure AD B2C

Skapa ett nytt användar flöde i Azure AD B2C klient och välj Logga in **med ROPC** för att aktivera ROPC för användar flödet. Mer information finns i [Konfigurera flödet för autentiseringsuppgifter för resurs ägar lösen ord](../../active-directory-b2c/add-ropc-policy.md).

`IPublicClientApplication` innehåller `AcquireTokenByUsernamePassword` metoden:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Den här `AcquireTokenByUsernamePassword` metoden använder följande parametrar:

- De *omfattningar* som en åtkomsttoken ska hämtas för.
- Ett *användar namn*.
- Ett SecureString- *lösenord* för användaren.

### <a name="limitations-of-the-ropc-flow"></a>Begränsningar för ROPC-flödet

ROPC-flödet **fungerar bara för lokala konton** där dina användare har registrerats med Azure AD B2C med hjälp av en e-postadress eller ett användar namn. Det här flödet fungerar inte när du federerar till en extern identitetsprovider som stöds av Azure AD B2C (Facebook, Google osv.).

## <a name="google-auth-and-embedded-webview"></a>Google auth och Embedded webbvy

Om du använder Google som en identitets leverantör rekommenderar vi att du använder system läsaren som Google tillåter inte [autentisering från inbäddade webviews](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). För närvarande `login.microsoftonline.com` är en betrodd utfärdare med Google och fungerar med inbäddad webbvy. Men `b2clogin.com` är inte en betrodd utfärdare med Google, så användarna kan inte autentisera sig.

Vi tillhandahåller en uppdatering av det här [problemet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) om saker ändras.

## <a name="token-caching-in-msalnet"></a>Cachelagring av token i MSAL.NET

### <a name="known-issue-with-azure-ad-b2c"></a>Känt problem med Azure AD B2C

MSAL.NET stöder [token cache](/dotnet/api/microsoft.identity.client.tokencache). Nyckeln för cachelagring av token baseras på de anspråk som returneras av identitets leverantören (IdP).

För närvarande behöver MSAL.NET två anspråk för att bygga en nyckel för token-cache:

- `tid` (klient-ID för Azure AD)
- `preferred_username`

Båda dessa anspråk kan saknas i Azure AD B2C scenarier eftersom inte alla sociala identitets leverantörer (Facebook, Google och andra) returnerar dem i de tokens som de returnerar till Azure AD B2C.

Ett symtom på ett sådant scenario är att MSAL.NET returnerar `Missing from the token response` när du har åtkomst till `preferred_username` anspråks värdet i tokens som utfärdats av Azure AD B2C. MSAL använder `Missing from the token response` värdet för `preferred_username` för att bevara cache-mellan-kompatibilitet mellan bibliotek.

### <a name="workarounds"></a>Provisoriska lösningar

#### <a name="mitigation-for-missing-tenant-id"></a>Minskning av klient-ID som saknas

Den föreslagna lösningen är att använda [cachelagring enligt princip](#acquire-a-token-to-apply-a-policy) som beskrivs ovan.

Du kan också använda `tid` anspråket om du använder [anpassade principer](../../active-directory-b2c/custom-policy-get-started.md) i Azure AD B2C. Anpassade principer kan returnera ytterligare anspråk till ditt program genom att använda [anspråks omvandling](../../active-directory-b2c/claims-transformation-technical-profile.md).

#### <a name="mitigation-for-missing-from-the-token-response"></a>Minskning av "saknas från" token Response "

Ett alternativ är att använda `name` anspråket i stället för `preferred_username` . Om du vill inkludera `name` anspråk i ID-token som utfärdats av Azure AD B2C väljer du **visnings namn** när du konfigurerar ditt användar flöde.

Mer information om hur du anger vilka anspråk som returneras av dina användar flöden finns i [Självstudier: skapa användar flöden i Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hämtar token interaktivt med MSAL.NET för Azure AD B2C program finns i följande exempel.

| Exempel | Plattform | Beskrivning|
|------ | -------- | -----------|
|[Active-Directory-B2C-Xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | En Xamarin Forms-app som använder MSAL.NET för att autentisera användare via Azure AD B2C och sedan få åtkomst till ett webb-API med de token som returneras.|
