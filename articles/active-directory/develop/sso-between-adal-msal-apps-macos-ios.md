---
title: SSO mellan ADAL & MSAL-appar (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du delar SSO mellan ADAL-och MSAL-appar
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 60189b8ba952bc3315bf514c5d0786601840665a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756200"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Så här: SSO mellan ADAL-och MSAL-appar på macOS och iOS

Microsoft Authentication Library (MSAL) för iOS kan dela SSO-tillstånd med [ADAL mål-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) mellan program. Du kan migrera dina appar till MSAL i din egen takt, och se till att användarna fortfarande kommer att ha nytta av Cross-app SSO – även med en blandning av ADAL-och MSAL-baserade appar.

Om du behöver hjälp med att konfigurera SSO mellan appar med MSAL SDK, se [tyst SSO mellan flera appar](single-sign-on-macos-ios.md#silent-sso-between-apps). Den här artikeln fokuserar på SSO mellan ADAL och MSAL.

Vilka som implementerar SSO är beroende av ADAL-versionen som du använder.

## <a name="adal-27x"></a>ADAL 2.7. x

I det här avsnittet beskrivs SSO-skillnader mellan MSAL och ADAL 2.7. x

### <a name="cache-format"></a>Cache-format

ADAL 2.7. x kan läsa MSAL cache-format. Du behöver inte göra något särskilt för enkel inloggning mellan appar med version ADAL 2.7. x. Du måste dock vara medveten om skillnaderna i konto identifierare som de två biblioteken stöder.

### <a name="account-identifier-differences"></a>Skillnader i konto identifierare

MSAL och ADAL använder olika konto identifierare. ADAL använder UPN som primär konto identifierare. MSAL använder en icke-avvisningsbar konto identifierare som baseras på ett objekt-ID och ett klient-ID för AAD-konton och ett `sub` anspråk för andra typer av konton.

När du tar emot ett `MSALAccount` objekt i MSAL-resultatet innehåller det en konto identifierare i `identifier` egenskapen. Programmet bör använda denna identifierare för efterföljande tysta begär Anden.

Förutom `identifier` , `MSALAccount` innehåller objektet en uppuppspelnings bara identifierare som kallas `username` . Detta översätts till `userId` i ADAL. `username` anses inte vara en unik identifierare och kan ändras när som helst, så den bör endast användas för scenarier med bakåtkompatibilitet med ADAL. MSAL har stöd för cache-frågor som använder antingen `username` eller `identifier` , där Query by `identifier` rekommenderas.

I följande tabell sammanfattas skillnader i konto identifierare mellan ADAL och MSAL:

| Konto identifierare                | MSAL                                                         | ADAL 2.7. x      | Äldre ADAL (före ADAL 2.7. x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| visnings bara identifierare            | `username`                                                   | `userId`        | `userId`                       |
| unikt ID som inte går att lyssna | `identifier`                                                 | `homeAccountId` | Ej tillämpligt                            |
| Inget konto-ID är känt               | Fråga alla konton via `allAccounts:` API i `MSALPublicClientApplication` | Saknas             | Saknas                            |

Detta är `MSALAccount` gränssnittet som tillhandahåller dessa identifierare:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO från MSAL till ADAL

Om du har en MSAL-app och en ADAL-app, och användaren först loggar in i den MSAL-baserade appen, kan du hämta SSO i ADAL-appen genom att spara `username` från `MSALAccount` objektet och skicka den till din ADAL-baserade app som `userId` . ADAL kan sedan hitta konto informationen i bakgrunden med `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API: et.

### <a name="sso-from-adal-to-msal"></a>SSO från ADAL till MSAL

Om du har en MSAL-app och en ADAL-app, och användaren först loggar in i den ADAL-baserade appen, kan du använda ADAL användar identifierare för konto uppslag i MSAL. Detta gäller även när du migrerar från ADAL till MSAL.

#### <a name="adals-homeaccountid"></a>ADAL-homeAccountId

ADAL 2.7. x returnerar `homeAccountId` i `ADUserInformation` objektet i resultatet via den här egenskapen:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` i ADAL är motsvarigheten till `identifier` i MSAL. Du kan spara den här identifieraren så att den används i MSAL för konto uppslag med `accountForIdentifier:error:` API: et.

#### <a name="adals-userid"></a>ADAL `userId`

Om `homeAccountId` inte är tillgängligt eller om du bara har den displayable identifieraren kan du använda ADAL `userId` för att söka efter kontot i MSAL.

I MSAL börjar du med att slå upp ett konto med `username` eller `identifier` . Använd alltid `identifier` för fråga om du har den och Använd den bara `username` som reserv. Om kontot hittas använder du kontot i `acquireTokenSilent` anropen.

Mål-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Införliva

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL-API: er som stöds för konto sökning:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2. x-2.6.6

I det här avsnittet beskrivs SSO-skillnader mellan MSAL och ADAL 2. x-2.6.6.

Äldre versioner av ADAL stöder inte MSAL cache-formatet. Men för att säkerställa en smidig migrering från ADAL till MSAL kan MSAL läsa det äldre ADAL cache-formatet utan att fråga användaren om autentiseringsuppgifter igen.

Eftersom `homeAccountId` inte är tillgängligt i äldre ADAL-versioner måste du leta upp konton med hjälp av `username` :

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Ett exempel:

Mål-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Införliva

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Alternativt kan du läsa alla konton, som även kommer att läsa konto information från ADAL:

Mål-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Införliva

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Nästa steg

Lär dig mer om [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)
