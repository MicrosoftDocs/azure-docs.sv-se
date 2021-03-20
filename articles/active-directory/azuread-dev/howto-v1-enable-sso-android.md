---
title: Aktivera enkel inloggning mellan appar på Android med ADAL | Microsoft Docs
description: Hur du använder funktionerna i ADAL SDK för att aktivera enkel inloggning i dina program.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev, devx-track-java
ROBOTS: NOINDEX
ms.openlocfilehash: 9d1faf7aed5cf4c4975925c7a56d169dc99a7bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87313599"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Gör så här: aktivera enkel inloggning mellan appar på Android med ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Enkel inloggning (SSO) gör det möjligt för användare att bara ange sina autentiseringsuppgifter en gång och låta dessa autentiseringsuppgifter automatiskt fungera mellan program och mellan plattformar som andra program kan använda (till exempel Microsoft-konton eller ett arbets konto från Microsoft 365) oavsett utgivaren.

Microsofts identitets plattform, tillsammans med SDK: er, gör det enkelt att aktivera SSO i din egen uppsättning appar, eller med funktionerna för Service Broker och autentiserare över hela enheten.

I den här instruktionen får du lära dig hur du konfigurerar SDK i ditt program för att tillhandahålla enkel inloggning till dina kunder.

## <a name="prerequisites"></a>Förutsättningar

Den här instruktionen förutsätter att du vet hur du:

- Etablera din app med hjälp av den äldre portalen för Azure Active Directory (Azure AD). Mer information finns i [Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Integrera ditt program med [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Begrepp för enkel inloggning

### <a name="identity-brokers"></a>Identitets hanterare

Microsoft tillhandahåller program för alla mobila plattformar som gör det möjligt att överbrygga autentiseringsuppgifter mellan program från olika leverantörer och för förbättrade funktioner som kräver en säker plats varifrån autentiseringsuppgifterna ska verifieras. Dessa kallas för- **hanterare**.

På iOS och Android tillhandahålls utbetalningar genom nedladdnings bara program som kunder antingen installerar oberoende eller som skickas till enheten av ett företag som hanterar vissa, eller alla, av enheterna för sina anställda. Utjämnare stöder hantering av säkerhet för vissa program eller hela enheten baserat på IT-administratörens konfiguration. I Windows tillhandahålls den här funktionen av en konto väljare som är inbyggd i operativ systemet, vilket är känt tekniskt som Web Authentication Broker.

#### <a name="broker-assisted-login"></a>Service Broker-inloggning

Service Broker-assisterade inloggningar är inloggnings upplevelser som inträffar i Service Broker-programmet och använder tjänstens lagring och säkerhet för att dela autentiseringsuppgifter i alla program på enheten som använder identitets plattformen. Indirekt som dina program kommer att förlita sig på att logga in användare i. På iOS och Android tillhandahålls dessa mäklare genom nedladdnings bara program som kunder antingen installerar oberoende eller som kan skickas till enheten av ett företag som hanterar enheten för sin användare. Ett exempel på den här typen av program är Microsoft Authenticator-programmet på iOS. I Windows tillhandahålls den här funktionen av en konto väljare som är inbyggd i operativ systemet, vilket är känt tekniskt som Web Authentication Broker.
Upplevelsen varierar beroende på plattform och kan ibland vara störande för användare om de inte hanteras korrekt. Du är förmodligen bekant med det här mönstret om du har Facebook-programmet installerat och använder Facebook Connect från ett annat program. Identitets plattformen använder samma mönster.

På Android visas konto väljaren ovanpå ditt program, vilket är mindre störande för användaren.

#### <a name="how-the-broker-gets-invoked"></a>Hur Broker anropas

Om en kompatibel Broker är installerad på enheten, t. ex. Microsoft Authenticator programmet, kommer identitets-SDK: er automatiskt att utföra Service Broker åt dig när en användare anger att de vill logga in med ett konto från identitets plattformen.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Hur Microsoft ser till att programmet är giltigt

Behovet av att se till att identiteten för ett program som anropar Service Broker är avgörande för den säkerhet som anges i Service Broker-inloggningar. iOS och Android tillämpar inte unika identifierare som endast är giltiga för ett angivet program, så att skadliga program kan "falska" ett legitimt programs ID och ta emot de token som är avsedda för det legitima programmet. För att säkerställa att Microsoft alltid kommunicerar med rätt program vid körning uppmanas utvecklaren att tillhandahålla en anpassad redirectURI när de registrerar sitt program hos Microsoft. **Hur utvecklare bör utforma denna omdirigerings-URI beskrivs i detalj nedan.** Den här anpassade redirectURI innehåller certifikatets tumavtryck för programmet och är säkerställt att vara unikt för programmet av Google Play Butik. När ett program anropar koordinatorn ber Service Broker operativ systemet Android att tillhandahålla det med det tumavtryck för certifikatet som anropade Service Broker. Service Broker innehåller det här certifikatets tumavtryck till Microsoft i anropet till identitets systemet. Om certifikatets tumavtryck för programmet inte matchar certifikatets tumavtryck som tillhandahölls av utvecklaren under registreringen, nekas åtkomst till de token för resursen som programmet begär. Den här kontrollen säkerställer att endast det program som har registrerats av utvecklaren tar emot tokens.

Brokered-SSO-inloggningar har följande fördelar:

* Användare upplever enkel inloggning för alla sina program oavsett leverantör.
* Ditt program kan använda mer avancerade företags funktioner, till exempel villkorlig åtkomst och stöd för Intune-scenarier.
* Programmet har stöd för certifikatbaserad autentisering för företags användare.
* Säkrare inloggnings upplevelse som identitet för programmet och användaren verifieras av Service Broker-programmet med ytterligare säkerhetsalgoritmer och kryptering.

Här är en representation av hur SDK: er fungerar med Broker-programmen för att aktivera SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivera SSO för Service Broker via enkel inloggning

Möjligheten för ett program att använda en Service Broker som är installerad på enheten är inaktive rad som standard. För att kunna använda programmet med Service Broker måste du göra ytterligare konfiguration och lägga till kod i programmet.

De steg du följer är:

1. Aktivera Broker-läge i program kodens anrop till MS SDK
2. Upprätta en ny omdirigerings-URI och ange att både appen och appens registrering
3. Konfigurera rätt behörigheter i Android-manifestet

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Steg 1: Aktivera Broker-läge i ditt program

Möjligheten för ditt program att använda Service Broker aktive ras när du skapar inställningarna "Settings" eller den första konfigurationen av din autentiserings instans. Så här gör du i appen:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Steg 2: upprätta en ny omdirigerings-URI med URL-schemat

För att säkerställa att rätt program tar emot de returnerade autentiseringsuppgifterna, måste du se till att anropa programmet på ett sätt som kan verifieras av Android-operativsystemet. Android-operativsystemet använder hash-värdet för certifikatet i Google Play-butiken. Denna hash för certifikatet kan inte manipuleras av ett falskt program. Tillsammans med URI: n för Service Broker-programmet säkerställer Microsoft att tokens returneras till rätt program. En unik omdirigerings-URI måste registreras i programmet.

Din omdirigerings-URI måste ha rätt format:

`msauth://packagename/Base64UrlencodedSignature`

till exempel: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Du kan registrera denna omdirigerings-URI i appens registrering med hjälp av [Azure Portal](https://portal.azure.com/). Mer information om registrering av Azure AD-appar finns i [integrera med Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Steg 3: Konfigurera rätt behörigheter i ditt program

Service Broker-programmet i Android använder funktionen Accounts Manager i Android-OS för att hantera autentiseringsuppgifter mellan program. För att kunna använda Service Broker i Android måste appens manifest ha behörighet att använda AccountManager-konton. Dessa behörigheter beskrivs i detalj i Google- [dokumentationen för konto hanteraren här](https://developer.android.com/reference/android/accounts/AccountManager.html)

Dessa behörigheter är särskilt:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Du har konfigurerat SSO!

Nu kommer Identity SDK automatiskt att både dela autentiseringsuppgifter i dina program och anropa Service Broker om den finns på deras enhet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
