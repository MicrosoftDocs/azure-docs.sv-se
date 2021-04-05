---
title: Offentliga klient program för enskilda och flera konton | Azure
description: En översikt över offentliga klient program för enskilda och flera konton.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 5bea1753c87c11094e78f95a1bbadb02fb0b95e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752989"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Offentliga klient program för enskilda och flera konton

Den här artikeln hjälper dig att förstå de typer som används i en och flera offentliga klient program, med fokus på offentliga klient program för enskilda konton. 

ADAL (Azure Active Directory Authentication Library) modellerar servern.  Microsoft Authentication Library (MSAL) i stället modellerar ditt klient program.  Majoriteten av Android-appar betraktas som offentliga klienter. En offentlig klient är en app som inte kan behålla hemligheten på ett säkert sätt.  

MSAL specialiserar sig på API-ytan för `PublicClientApplication` att förenkla och klargöra utvecklings upplevelsen för appar som endast tillåter att ett konto används i taget. `PublicClientApplication` är underklassad av `SingleAccountPublicClientApplication` och `MultipleAccountPublicClientApplication` .  I följande diagram visas relationen mellan dessa klasser.

![Diagram över SingleAccountPublicClientApplication UML-klass](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Offentligt klient program för ett enda konto

Med `SingleAccountPublicClientApplication` klassen kan du skapa en MSAL-baserad app som endast tillåter att ett enda konto är inloggat i taget. `SingleAccountPublicClientApplication` skiljer sig från `PublicClientApplication` på följande sätt:

- MSAL spårar det för tillfället inloggade kontot.
  - Om din app använder en Service Broker (standardinställningen under Azure Portal app Registration) och installeras på en enhet där en Broker finns, verifierar MSAL att kontot fortfarande är tillgängligt på enheten.
- `signIn` gör att du kan logga in ett konto explicit och separat från begär ande omfattningar.
- `acquireTokenSilent` kräver ingen konto parameter.  Om du anger ett konto och det konto som du anger inte matchar det aktuella kontot som spåras av MSAL, `MsalClientException` genereras ett.
- `acquireToken` tillåter inte att användaren växlar konton. Om användaren försöker växla till ett annat konto genereras ett undantag.
- `getCurrentAccount` Returnerar ett resultat objekt som tillhandahåller följande:
  - Ett booleskt värde som anger om kontot har ändrats. Ett konto kan till exempel ändras på grund av att det har tagits bort från enheten.
  - Föregående konto. Detta är användbart om du behöver göra några lokala data rensningar när kontot tas bort från enheten eller när ett nytt konto har loggats in.
  - CurrentAccount.
- `signOut` tar bort alla token som är associerade med din klient från enheten.  

När en Android-autentiseringsprovider som Microsoft Authenticator eller Intune-företagsportal är installerad på enheten och din app har kon figurer ATS för att använda Broker, `signOut` tar du inte bort kontot från enheten.

## <a name="single-account-scenario"></a>Scenario med enskilt konto

Följande pseudo-kod illustrerar användningen av `SingleAccountPublicClientApplication` .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Offentligt klient program för flera konton

`MultipleAccountPublicClientApplication`Klassen används för att skapa MSAL-baserade appar som låter flera konton vara inloggade på samma gång. Det gör att du kan hämta, lägga till och ta bort konton på följande sätt:

### <a name="add-an-account"></a>Lägga till ett konto

Använd ett eller flera konton i ditt program genom att anropa `acquireToken` en eller flera gånger.  

### <a name="get-accounts"></a>Hämta konton

- Anrop `getAccount` för att hämta ett speciellt konto.
- Anropa `getAccounts` för att hämta en lista över konton som är kända för appen.

Din app kan inte räkna upp alla Microsoft Identity Platform-konton på enheten som är känd för Service Broker-appen. Det kan bara räkna upp konton som har använts av din app.  Konton som har tagits bort från enheten returneras inte av dessa funktioner.

### <a name="remove-an-account"></a>Ta bort ett konto

Ta bort ett konto genom att anropa `removeAccount` med en konto identifierare.

Om din app har kon figurer ATS för att använda en Broker och en Broker är installerad på enheten, tas kontot inte bort från Service Broker när du anropar `removeAccount` .  Endast tokens som är kopplade till klienten tas bort.

## <a name="multiple-account-scenario"></a>Scenario med flera konton

Följande pseudo-kod visar hur du skapar en app för flera konton, listar konton på enheten och hämtar tokens.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
