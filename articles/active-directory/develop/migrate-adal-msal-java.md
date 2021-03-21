---
title: ADAL till MSAL migration guide (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du migrerar din ADAL-java-app (Azure Active Directory Authentication Library) till Microsoft Authentication Library (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 620f77655f8281919ba0831a7e53af8cd28bc5f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584001"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>ADAL till MSAL migration-guide för Java

I den här artikeln beskrivs de ändringar du behöver göra för att migrera en app som använder ADAL (Azure Active Directory Authentication Library) för att använda Microsoft Authentication Library (MSAL).

Både Microsoft Authentication Library för Java (MSAL4J) och Azure AD Authentication Library för Java (ADAL4J) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Fram till nu har de flesta utvecklare arbetat med Azure AD för utvecklare Platform (v 1.0) för att autentisera Azure AD-identiteter (arbets-och skol konton) genom att begära token med Azure AD Authentication Library (ADAL).

MSAL har följande fördelar:

- Eftersom den använder den nyare Microsoft Identity Platform kan du autentisera en bredare uppsättning Microsoft-identiteter, till exempel Azure AD-identiteter, Microsoft-konton och sociala och lokala konton via Azure AD Business till konsument (B2C).
- Användarna får bästa möjliga upplevelse med enkel inloggning.
- Ditt program kan möjliggöra stegvist tillstånd och stöd för villkorlig åtkomst är enklare.

MSAL för Java är auth Library som vi rekommenderar att du använder med Microsoft Identity Platform. Inga nya funktioner kommer att implementeras på ADAL4J. Alla ansträngningar som går framåt fokuserar på att förbättra MSAL.

## <a name="differences"></a>Skillnader

Om du har arbetat med slut punkten för Azure AD för utvecklare (v 1.0) (och ADAL4J) kanske du vill läsa vad som [skiljer sig från Microsoft Identity Platform?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="scopes-not-resources"></a>Webbprogramsomfattande omfattningar

ADAL4J hämtar tokens för resurser medan MSAL för Java hämtar token för omfattningar. Ett antal MSAL för Java-klasser kräver en omfattnings parameter. Den här parametern är en lista med strängar som deklarerar önskade behörigheter och resurser som begärs. Se [Microsoft graphs omfång](/graph/permissions-reference) för att se exempel omfattningar.

Du kan lägga till `/.default` scope-suffixet till resursen för att hjälpa till att migrera dina appar från ADAL till MSAL. För resurs värdet för är till exempel `https://graph.microsoft.com` värdet för motsvarande omfång `https://graph.microsoft.com/.default` .  Om resursen inte finns i URL-formuläret, men ett resurs-ID för formuläret `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , kan du fortfarande använda omfångs värdet som `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

För mer information om de olika typerna av omfattningar, se [behörigheter och medgivande i Microsoft Identity Platform](./v2-permissions-and-consent.md) och [scope för ett webb-API som accepterar v 1.0-token-](./msal-v1-app-scopes.md) artiklar.

## <a name="core-classes"></a>Core-klasser

I ADAL4J representerar- `AuthenticationContext` klassen din anslutning till säkerhetstokentjänst (STS) eller auktoriseringsservern via en utfärdare. MSAL för Java är dock utformad runt klient program. Den innehåller två separata klasser: `PublicClientApplication` och `ConfidentialClientApplication` för att representera klient program.  Det sistnämnda `ConfidentialClientApplication` representerar ett program som är utformat för att på ett säkert sätt upprätthålla en hemlighet, till exempel ett program-ID för en daemon-app.

I följande tabell visas hur ADAL4J Functions mappar till den nya MSAL för Java functions:

| ADAL4J-metod| MSAL4J-metod|
|------|-------|
|acquireToken (sträng resurs, ClientCredential-autentiseringsuppgift, AuthenticationCallback motringning) | acquireToken(ClientCredentialParameters)|
|acquireToken (sträng resurs, ClientAssertion-kontroll, AuthenticationCallback motringning)|acquireToken(ClientCredentialParameters)|
|acquireToken (sträng resurs, AsymmetricKeyCredential-autentiseringsuppgift, AuthenticationCallback motringning)|acquireToken(ClientCredentialParameters)|
|acquireToken (sträng resurs, sträng clientId, sträng användar namn, sträng lösen ord, AuthenticationCallback motringning)| acquireToken(UsernamePasswordParameters)|
|acquireToken (sträng resurs, sträng clientId, sträng användar namn, sträng lösen ord = null, AuthenticationCallback motringning)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken (sträng resurs, UserAssertion userAssertion, ClientCredential Credential, AuthenticationCallback motringning)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode () och acquireTokenByDeviceCode ()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount i stället för IUser

ADAL4J manipulerade användare. Även om en användare representerar en enda mänsklig-eller program varu agent kan den ha ett eller flera konton i Microsoft Identity system. En användare kan till exempel ha flera Azure AD-, Azure AD B2C-eller Microsoft-personliga konton.

MSAL for Java definierar konceptet konto via `IAccount` gränssnittet. Detta är en större förändring från ADAL4J, men det är en lämplig eftersom den fångar upp det faktum att samma användare kan ha flera konton och kanske även i olika Azure AD-kataloger. MSAL för Java ger bättre information i gäst scenarier eftersom information om hem kontot tillhandahålls.

## <a name="cache-persistence"></a>Beständighet i cacheminnet

ADAL4J har inte stöd för token-cache.
MSAL för Java lägger till en [token-cache](msal-acquire-cache-tokens.md) för att förenkla hanteringen av livs längd för token genom att automatiskt uppdatera utgångna token när det är möjligt och förhindra att användaren anger autentiseringsuppgifter när det är möjligt.

## <a name="common-authority"></a>Gemensam auktoritet

Om du använder `https://login.microsoftonline.com/common` -utfärdaren i v 1.0 kan användarna logga in med valfritt Azure Active Directory (AAD)-konto (för alla organisationer).

Om du använder `https://login.microsoftonline.com/common` -utfärdaren i v 2.0 kan användarna logga in med valfri AAD-organisation eller till och med ett personligt Microsoft-konto (MSA). I MSAL för Java, om du vill begränsa inloggningen till ett AAD-konto, använder du `https://login.microsoftonline.com/organizations` auktoriteten (vilket är samma beteende som med ADAL4J). Ange en utfärdare genom att ange `authority` parametern i [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) -metoden när du skapar din `PublicClientApplication` klass.

## <a name="v10-and-v20-tokens"></a>v 1.0-och v 2.0-token

V 1.0-slutpunkten (används av ADAL) avger endast v 1.0-token.

V 2.0-slutpunkten (används av MSAL) kan generera v 1.0-och v 2.0-token. En egenskap hos applikations manifestet för webb-API: et gör det möjligt för utvecklare att välja vilken version av token som godkänns. Se `accessTokenAcceptedVersion` i referens dokumentationen för [applikations manifestet](./reference-app-manifest.md) .

Mer information om v 1.0-och v 2.0-token finns i [Azure Active Directory åtkomsttoken](./access-tokens.md).

## <a name="adal-to-msal-migration"></a>ADAL till MSAL-migrering

I ADAL4J exponerades uppdateringstoken, vilket gör att utvecklarna kan cachelagra dem. De använder sedan `AcquireTokenByRefreshToken()` för att aktivera lösningar som att implementera tjänster som körs länge och som uppdaterar instrument paneler för användarens räkning när användaren inte längre är ansluten.

MSAL för Java visar inte uppdateringstoken av säkerhets skäl. I stället hanterar MSAL en uppdatering av tokens åt dig.

MSAL för Java har ett API som gör att du kan migrera uppdateringstoken som du har köpt med ADAL4j till ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Med den här metoden kan du ange den tidigare använda uppdateringstoken tillsammans med eventuella omfång (resurser) som du vill ha. Uppdateringstoken kommer att utbytas för en ny och cachelagras för användning av ditt program.

Följande kodfragment visar en kod för migrering i ett konfidentiellt klient program:

```java
String rt = GetCachedRefreshTokenForSignedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult`Returnerar en åtkomsttoken och ID-token, medan den nya uppdateringstoken lagras i cacheminnet.
Programmet kommer nu även att innehålla en IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Om du vill använda token som nu finns i cacheminnet anropar du:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
