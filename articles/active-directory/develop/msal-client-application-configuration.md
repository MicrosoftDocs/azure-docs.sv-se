---
title: Konfiguration av klient program (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om konfigurations alternativ för offentliga klient program och konfidentiella klient program med hjälp av Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/20/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 00768f363d08bc476350e57a8eac69eafd9c3589
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99580946"
---
# <a name="application-configuration-options"></a>Program konfigurations alternativ

Om du vill autentisera och förvärva token initierar du ett nytt offentligt eller konfidentiellt klient program i din kod. Du kan ange flera konfigurations alternativ när du initierar-klient programmet i Microsoft Authentication Library (MSAL). De här alternativen är i två grupper:

- Registrerings alternativ, inklusive:
  - [Auktoritet](#authority) (som består av identitets leverantörs [instansen](#cloud-instance) och inloggnings [mål grupp](#application-audience) för appen, och eventuellt klient-ID)
  - [Klient-ID](#client-id)
  - [Omdirigerings-URI](#redirect-uri)
  - [Klient hemlighet](#client-secret) (för konfidentiella klient program)
- [Loggnings alternativ](#logging), inklusive loggnings nivå, kontroll av personliga data och namnet på komponenten med hjälp av biblioteket

## <a name="authority"></a>Myndighet

Auktoriteten är en URL som anger en katalog som MSAL kan begära token från.

Vanliga myndigheter är:

| Vanliga auktoritets-URL: er | När du ska använda detta |
|--|--|
| `https://login.microsoftonline.com/<tenant>/` | Logga bara in användare av en speciell organisation. `<tenant>`I URL: en är klient-ID: t för Azure Active Directory (Azure AD)-klienten (ett GUID) eller dess klient domän. |
| `https://login.microsoftonline.com/common/` | Logga in användare med arbets-och skol konton eller personliga Microsoft-konton. |
| `https://login.microsoftonline.com/organizations/` | Logga in användare med arbets-och skol konton. |
| `https://login.microsoftonline.com/consumers/` | Logga in användare med enbart personliga Microsoft-konton (MSA). |

Den myndighet som du anger i koden måste vara konsekvent med de **konto typer som stöds** och som du har angett för appen i **Appregistreringar** i Azure Portal.

Auktoriteten kan vara:

- En Azure AD-moln utfärdare.
- En Azure AD B2C utfärdare. Se [B2C-information](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- En Active Directory Federation Services (AD FS)-utfärdare (AD FS). Se [AD FS support](https://aka.ms/msal-net-adfs-support).

Azure AD Cloud-utfärdare har två delar:

- Identity Provider- *instansen*
- Inloggnings *mål grupp* för appen

Instansen och mål gruppen kan sammanfogas och anges som auktoritets-URL. Det här diagrammet visar hur auktoritets-URL: en består:

![Så här är auktoritets webb adressen sammansatt](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Moln instans

*Instansen* används för att ange om din app signerar användare från det offentliga Azure-molnet eller från nationella moln. Med MSAL i din kod kan du ställa in Azure-molnet genom att använda en uppräkning eller genom att skicka URL: en till den [nationella moln instansen](authentication-national-cloud.md#azure-ad-authentication-endpoints) som `Instance` medlem (om du känner till den).

MSAL.NET kommer att utlösa ett explicit undantag om både `Instance` och `AzureCloudInstance` anges.

Om du inte anger en instans, kommer din app att rikta in sig på Azures offentliga moln instans (URL-instansen `https://login.onmicrosoftonline.com` ).

## <a name="application-audience"></a>Program mål

Inloggnings mål gruppen beror på affärs behoven för din app:

- Om du är en LOB-utvecklare (Line of Business) kan du förmodligen skapa ett program med en enda klient som endast används i din organisation. I så fall anger du organisationens ID (ID för Azure AD-instansen) eller ett domän namn som är kopplat till Azure AD-instansen.
- Om du är en ISV kan du behöva logga in användare med sina arbets-och skol konton i valfri organisation eller i vissa organisationer (appar för flera innehavare). Men du kanske också vill att användarna ska logga in med sina personliga Microsoft-konton.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Så här anger du mål gruppen i din kod/konfiguration

Med MSAL i din kod anger du mål gruppen genom att använda något av följande värden:

- Azure AD-utfärdarens målgrupps uppräkning
- Klient-ID: t, som kan vara:
  - Ett GUID (ID: t för din Azure AD-instans) för program med en klient
  - Ett domän namn som är associerat med din Azure AD-instans (även för program med en klient)
- En av dessa plats hållare som klient-ID i stället för mål grupps uppräkningen i Azure AD-utfärdaren:
  - `organizations` för ett program med flera innehavare
  - `consumers` för att bara logga in användare med sina personliga konton
  - `common` Logga in användare med sina arbets-och skol konton eller deras personliga Microsoft-konton

MSAL kommer att utlösa ett meningsfullt undantag om du anger både Azure AD-utfärdarens publik och klient-ID.

Om du inte anger en mål grupp kommer din app att rikta in sig på Azure AD och personliga Microsoft-konton som mål grupp. (Det vill säga att det fungerar som om det har `common` angetts.)

### <a name="effective-audience"></a>Effektiv mål grupp

Den effektiva mål gruppen för ditt program är minimivärdet (om det finns en skärnings punkt) för den mål grupp som du har angett i appen och mål gruppen som anges i appens registrering. I själva verket kan [Appregistreringars](https://aka.ms/appregistrations) upplevelsen ange mål gruppen (de konto typer som stöds) för appen. Mer information finns i [snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md).

För närvarande är det enda sättet att få en app att bara logga in användare med personliga Microsoft-konton att konfigurera båda dessa inställningar:

- Ange mål grupp för appens registrering `Work and school accounts and personal accounts` .
- Ange mål gruppen i koden/konfigurationen till `AadAuthorityAudience.PersonalMicrosoftAccount` (eller `TenantID` = "konsumenter").

## <a name="client-id"></a>Klient-ID

Klient-ID är det unika program-ID (klient) som är tilldelat din app av Azure AD när appen registrerades.

## <a name="redirect-uri"></a>Omdirigerings-URI

Omdirigerings-URI: n är den URI som identitets leverantören skickar tillbaka säkerhetstoken till.

### <a name="redirect-uri-for-public-client-apps"></a>Omdirigerings-URI för offentliga klient program

Om du är en offentlig klient programs utvecklare som använder MSAL:

- Du vill använda `.WithDefaultRedirectUri()` i Desktop-eller UWP-program (MSAL.NET 4.1 +). Den här metoden anger den offentliga klient programmets omdirigerings-URI-egenskap till standard Rekommenderad omdirigerings-URI för offentliga klient program.

  | Plattform | Omdirigerings-URI |
  |--|--|
  | Desktop-app (.NET VB) | `https://login.microsoftonline.com/common/oauth2/nativeclient` |
  | UWP | värde för `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` . Detta möjliggör enkel inloggning med webbläsaren genom att ange värdet till resultatet av WebAuthenticationBroker. GetCurrentApplicationCallbackUri () som du behöver registrera |
  | .NET Core | `https://localhost`. Detta gör att användaren kan använda systemets webbläsare för interaktiv autentisering eftersom .NET Core inte har något användar gränssnitt för den inbäddade webbvy för tillfället. |

- Du behöver inte lägga till en omdirigerings-URI om du skapar ett Xamarin Android-och iOS-program som inte stöder omdirigerings-URI för Broker. Den ställs in automatiskt `msal{ClientId}://auth` för Xamarin Android och iOS.

- Konfigurera omdirigerings-URI: n i [Appregistreringar](https://aka.ms/appregistrations):

   ![Omdirigerings-URI i Appregistreringar](media/msal-client-application-configuration/redirect-uri.png)

Du kan åsidosätta omdirigerings-URI: n med hjälp av `RedirectUri` egenskapen (till exempel om du använder-hanterare). Här följer några exempel på omdirigerings-URI: er för det scenariot:

- `RedirectUriOnAndroid` = "msauth-5a434691-CCB2-4fd1-b97b-b64bcfbc03fc://com.Microsoft.Identity.client.Sample";
- `RedirectUriOnIos` = $ "msauth. {Bundle. ID}://auth ";

Mer information om iOS finns i [migrera iOS-program som använder Microsoft Authenticator från ADAL.net till MSAL.net](msal-net-migration-ios-broker.md) och [utnyttjar Service Broker på iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Mer information om Android finns i avsnittet om [Utjämnad autentisering i Android](msal-android-single-sign-on.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Omdirigerings-URI för konfidentiella klient program

För webbappar är omdirigerings-URI: n (eller svars-URL) den URI som Azure AD använder för att skicka tillbaka token till programmet. Denna URI kan vara webb adressen för webbappen/webb-API: et om den konfidentiella appen är en av dessa. Omdirigerings-URI: n måste registreras i appens registrering. Den här registreringen är särskilt viktig när du distribuerar en app som du först har testat lokalt. Därefter måste du lägga till svars-URL: en för den distribuerade appen i program registrerings portalen.

För daemon-appar behöver du inte ange en omdirigerings-URI.

## <a name="client-secret"></a>Klienthemlighet

Det här alternativet anger klient hemligheten för appen konfidentiell klient. Den här hemligheten (app Password) tillhandahålls av program registrerings portalen eller tillhandahålls till Azure AD vid registreringen av appen med PowerShell AzureAD, PowerShell AzureRM eller Azure CLI.

## <a name="logging"></a>Loggning
För att felsöka fel söknings scenarier och fel sökning av autentiseringsfel, innehåller Microsoft Authentication Library inbyggt loggnings stöd. Loggningen är att varje bibliotek beskrivs i följande artiklar:

:::row:::
    :::column:::
        - [Loggning i MSAL.NET](msal-logging-dotnet.md)
        - [Loggning i MSAL för Android](msal-logging-android.md)
        - [Loggning in MSAL.js](msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Loggning i MSAL för iOS/macOS](msal-logging-ios.md)
        - [Loggning i MSAL för Java](msal-logging-java.md)
        - [Loggning i MSAL för Python](msal-logging-python.md)
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att [instansiera klient program genom att använda MSAL.net](msal-net-initializing-client-applications.md) och [instansiera klient program med hjälp av MSAL.js](msal-js-initializing-client-applications.md).
