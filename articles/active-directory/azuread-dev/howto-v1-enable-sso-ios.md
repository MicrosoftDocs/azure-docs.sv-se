---
title: Aktivera enkel inloggning mellan appar på iOS med ADAL | Microsoft Docs
description: Så här använder du funktionerna i ADAL SDK för att aktivera enkel inloggning i dina program.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 08b018082c753b9524cb12a72d637fe5458d9114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383707"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Gör så här: aktivera enkel inloggning mellan appar på iOS med ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Enkel inloggning (SSO) gör det möjligt för användare att bara ange sina autentiseringsuppgifter en gång och låta dessa autentiseringsuppgifter automatiskt fungera mellan program och mellan plattformar som andra program kan använda (till exempel Microsoft-konton eller ett arbets konto från Microsoft 365) oavsett utgivaren.

Microsofts identitets plattform, tillsammans med SDK: er, gör det enkelt att aktivera SSO i din egen uppsättning appar, eller med funktionerna för Service Broker och autentiserare över hela enheten.

I den här instruktionen får du lära dig hur du konfigurerar SDK i ditt program för att tillhandahålla enkel inloggning till dina kunder.

Den här instruktionen gäller för:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory villkorlig åtkomst

## <a name="prerequisites"></a>Förutsättningar

Den här instruktionen förutsätter att du vet hur du:

* Etablera din app med hjälp av den äldre portalen för Azure AD. Mer information finns i [Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Integrera ditt program med [Azure AD iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Begrepp för enkel inloggning

### <a name="identity-brokers"></a>Identitets hanterare

Microsoft tillhandahåller program för alla mobila plattformar som gör det möjligt att överbrygga autentiseringsuppgifter mellan program från olika leverantörer och för förbättrade funktioner som kräver en säker plats varifrån autentiseringsuppgifterna ska verifieras. Dessa kallas för- **hanterare**.

På iOS och Android tillhandahålls utbetalningar genom nedladdnings bara program som kunder antingen installerar oberoende eller som skickas till enheten av ett företag som hanterar vissa, eller alla, av enheterna för sina anställda. Utjämnare stöder hantering av säkerhet för vissa program eller hela enheten baserat på IT-administratörens konfiguration. I Windows tillhandahålls den här funktionen av en konto väljare som är inbyggd i operativ systemet, vilket är känt tekniskt som Web Authentication Broker.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Mönster för att logga in på mobila enheter

Åtkomst till autentiseringsuppgifter på enheter följer två grundläggande mönster:

* Stödda inloggningar utan Service Broker
* Stöd för Broker-inloggning

#### <a name="non-broker-assisted-logins"></a>Stödda inloggningar utan Service Broker

Icke-Service Broker-inloggningar är inloggnings upplevelser som sker intill programmet och använder den lokala lagringen på enheten för programmet. Den här lagringen kan delas mellan program, men autentiseringsuppgifterna är nära kopplade till appen eller programsviten med den autentiseringsuppgiften. Du har troligen drabbats av detta i många mobilappar när du anger ett användar namn och lösen ord i själva programmet.

Dessa inloggningar har följande fördelar:

* Användar upplevelsen finns helt och hållet i programmet.
* Autentiseringsuppgifter kan delas mellan program som är signerade av samma certifikat, vilket ger en enkel inloggnings upplevelse till din program serie.
* Kontroll över upplevelsen av inloggningen tillhandahålls till programmet före och efter inloggning.

Dessa inloggningar har följande nack delar:

* Användare kan inte uppleva enkel inloggning i alla appar som använder en Microsoft-identitet, bara för de Microsoft-identiteter som programmet har konfigurerat.
* Programmet kan inte användas med mer avancerade företags funktioner som villkorlig åtkomst eller för att använda produkter i Intune-serien.
* Ditt program har inte stöd för certifikatbaserad autentisering för företags användare.

Här är en representation av hur SDK: er fungerar med den delade lagringen av dina program för att aktivera SSO:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Stöd för Broker-inloggning

Service Broker-assisterade inloggningar är inloggnings upplevelser som inträffar i Service Broker-programmet och använder tjänstens lagring och säkerhet för att dela autentiseringsuppgifter i alla program på enheten som använder identitets plattformen. Det innebär att programmen använder koordinatorn för att logga in användare i. På iOS och Android tillhandahålls dessa mäklare genom nedladdnings bara program som kunder antingen installerar separat eller som push-överförts till enheten av ett företag som hanterar enheten för sin användare. Ett exempel på den här typen av program är Microsoft Authenticator-programmet på iOS. I Windows tillhandahålls den här funktionen av en konto väljare som är inbyggd i operativ systemet, vilket är känt tekniskt som Web Authentication Broker.

Upplevelsen varierar beroende på plattform och kan ibland vara störande för användare om de inte hanteras korrekt. Du är förmodligen bekant med det här mönstret om du har Facebook-programmet installerat och använder Facebook Connect från ett annat program. Identitets plattformen använder samma mönster.

För iOS leder detta till en "över gång"-animering där ditt program skickas till bakgrunden medan Microsoft Authenticator program kommer till förgrunden för användaren att välja vilket konto de vill logga in med.

För Android och Windows visas konto väljaren ovanpå ditt program, vilket är mindre störande för användaren.

#### <a name="how-the-broker-gets-invoked"></a>Hur Broker anropas

Om en kompatibel Broker är installerad på enheten, t. ex. Microsoft Authenticator programmet, kommer SDK: er automatiskt att utföra Service Broker åt dig när en användare anger att de vill logga in med ett konto från identitets plattformen. Det här kontot kan vara ett personligt Microsoft-konto, ett arbets-eller skol konto eller ett konto som du tillhandahåller och är värd för i Azure med våra B2C-och B2B-produkter.

#### <a name="how-we-ensure-the-application-is-valid"></a>Så här ser vi till att programmet är giltigt

Behovet av att se till att identiteten för ett program som anropar Service Broker är avgörande för den säkerhet vi tillhandahåller i Service Broker-inloggningar. Varken iOS eller Android framtvingar unika identifierare som endast är giltiga för ett angivet program, så att skadliga program kan "falska" ett legitimt programs ID och ta emot de token som är avsedda för det legitima programmet. För att säkerställa att vi alltid kommunicerar med rätt program vid körning ber vi utvecklaren att tillhandahålla en anpassad redirectURI när han registrerar sitt program hos Microsoft. Hur utvecklare bör utforma denna omdirigerings-URI beskrivs i detalj nedan. Den här anpassade redirectURI innehåller paket-ID: t för programmet och är säkerställt att vara unikt för programmet av Apple App Store. När ett program anropar koordinatorn ber Service Broker operativ systemet för iOS att tillhandahålla det paket-ID som anropade Service Broker. Service Broker innehåller det här paket-ID: t för Microsoft i anropet till vårt identitets system. Om paket-ID: t för programmet inte matchar det paket-ID som tillhandahölls av utvecklaren under registreringen, kommer vi att neka åtkomst till de token för resursen som programmet begär. Den här kontrollen säkerställer att endast det program som har registrerats av utvecklaren tar emot tokens.

**Utvecklaren har möjlighet att välja om SDK: n anropar Broker eller använder det stödda flödet som inte har stöd för Service Broker.** Men om utvecklaren väljer att inte använda det Broker-baserade flödet förlorar du fördelarna med att använda SSO-autentiseringsuppgifter som användaren redan har lagt till på enheten och förhindrar att deras program används med företags funktioner som Microsoft tillhandahåller sina kunder, till exempel villkorlig åtkomst, Intune-hanterings funktioner och certifikatbaserad autentisering.

Dessa inloggningar har följande fördelar:

* Användare upplever enkel inloggning för alla sina program oavsett leverantör.
* Ditt program kan använda mer avancerade företags funktioner, till exempel villkorlig åtkomst eller produkter i Intune-serien.
* Programmet har stöd för certifikatbaserad autentisering för företags användare.
* Mycket säkrare inloggnings upplevelse som identitet för programmet och användaren verifieras av Service Broker-programmet med ytterligare säkerhetsalgoritmer och kryptering.

Dessa inloggningar har följande nack delar:

* I iOS övergår användaren från din program upplevelse medan autentiseringsuppgifter väljs.
* Förlust av möjligheten att hantera inloggnings upplevelsen för dina kunder i ditt program.

Här är en representation av hur SDK: er fungerar med Broker-programmen för att aktivera SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
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

## <a name="enabling-cross-app-sso-using-adal"></a>Aktivera enkel inloggning mellan appar med ADAL

Här använder vi ADAL iOS SDK för att:

* Aktivera non-Broker Assisted SSO för din serie appar
* Aktivera stöd för Service Broker-assisterad SSO

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Aktivera SSO för icke-Broker assisterad SSO

För icke-Broker Assisted SSO över flera program, kan SDK: er hantera en mycket stor komplexitet av enkel inloggning. Detta omfattar att hitta rätt användare i cacheminnet och underhålla en lista över inloggade användare som du kan fråga.

Om du vill aktivera SSO mellan program som du äger måste du göra följande:

1. Se till att alla program använder samma klient-ID eller program-ID.
2. Se till att alla dina program delar samma signerings certifikat från Apple så att du kan dela nyckel ringar.
3. Begär samma nyckel rings rättigheter för var och en av dina program.
4. Berätta för SDK: er om den delade nyckel ring som vi vill använda.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Använda samma klient-ID/program-ID för alla program i din serie appar

För att identitets plattformen ska kunna dela tokens i dina program, måste var och en av dina program dela samma klient-ID eller program-ID. Detta är den unika identifierare som du fick när du registrerade ditt första program i portalen.

Med omdirigerings-URI: er kan du identifiera olika appar för Microsoft Identity service om den använder samma program-ID. Varje program kan ha flera omdirigerings-URI: er registrerade i onboarding-portalen. Varje app i din svit har en annan omdirigerings-URI. Ett exempel på hur det ser ut är nedan:

APP1 omdirigerings-URI: `x-msauth-mytestiosapp://com.myapp.mytestapp`

APP2 omdirigerings-URI: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 omdirigerings-URI: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Dessa är kapslade under samma klient-ID/program-ID och slås upp baserat på omdirigerings-URI: n som du kommer tillbaka till oss i din SDK-konfiguration.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

Formatet för dessa omdirigerings-URI: er förklaras nedan. Du kan använda en omdirigerings-URI om du inte vill stödja Service Broker, i så fall måste de se ut ungefär så här *

#### <a name="create-keychain-sharing-between-applications"></a>Skapa nyckel rings delning mellan program

Att aktivera delning av nyckel ringar ligger utanför det här dokumentets omfattning och omfattas av Apple i dokument som [lägger till funktioner](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Det är viktigt att du bestämmer vad du vill att nyckel ringen ska anropas och lägger till den funktionen i alla dina program.

När du har rättigheter som har ställts in korrekt bör du se en fil i din projekt katalog `entitlements.plist` som innehåller något som ser ut ungefär så här:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

När nyckel ringen har Aktiver ATS i varje program och du är redo att använda SSO, kan du be identitets-SDK: n om nyckel ringen genom att använda följande inställning i `ADAuthenticationSettings` med följande inställning:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> När du delar en nyckel Ring i dina program kan alla program ta bort användare eller förvärra alla tokens i programmet. Detta är särskilt katastrofal om du har program som är beroende av att token ska fungera i bakgrunden. Delning av en nyckel Ring innebär att du måste vara mycket försiktig i alla och alla borttagnings åtgärder via identitets-SDK: er.

Klart! SDK kommer nu att dela autentiseringsuppgifter för alla dina program. Användar listan kommer också att delas mellan program instanser.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivera SSO för Service Broker via enkel inloggning

Möjligheten för ett program att använda en Service Broker som är installerad på enheten är **inaktive rad som standard**. För att kunna använda programmet med Service Broker måste du göra ytterligare konfiguration och lägga till kod i programmet.

De steg du följer är:

1. Aktivera Broker-läge i program kodens anrop till MS SDK.
2. Upprätta en ny omdirigerings-URI och ange både appen och appens registrering.
3. Registrerar ett URL-schema.
4. Lägg till en behörighet i filen info. plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Steg 1: Aktivera Broker-läge i ditt program

Möjligheten för ditt program att använda Service Broker aktive ras när du skapar kontexten "context" eller den första konfigurationen av ditt autentiseringscertifikat. Du gör detta genom att ange dina inloggnings uppgifter i koden:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO`Inställningen tillåter att SDK: n försöker anropa Service Broker, `AD_CREDENTIALS_EMBEDDED` vilket gör att SDK: n inte kan anropa Service Broker.

#### <a name="step-2-registering-a-url-scheme"></a>Steg 2: registrera ett URL-schema

Identitets plattformen använder URL: er för att anropa Service Broker och sedan återgår kontrollen tillbaka till ditt program. Om du vill slutföra den här omresan behöver du ett URL-schema som är registrerat för ditt program som identitets plattformen känner till. Detta kan förutom andra app-scheman som du tidigare har registrerat i ditt program.

> [!WARNING]
> Vi rekommenderar att du gör URL-schemat relativt unikt för att minimera sannolikheten för en annan app med samma URL-schema. Apple upprätthåller inte det unika URL-scheman som är registrerade i App Store.

Nedan visas ett exempel på hur detta visas i projekt konfigurationen. Du kan också göra detta i XCode:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Steg 3: upprätta en ny omdirigerings-URI med URL-schemat

För att säkerställa att vi alltid returnerar token för autentiseringsuppgifter till rätt program, måste vi se till att vi går tillbaka till ditt program på ett sätt som operativ systemet iOS kan verifiera. Operativ systemet iOS rapporteras till Microsoft Broker-programmen paket-ID: t för programmet som anropar det. Detta kan inte manipuleras av otillåtna program. Därför utnyttjar vi detta tillsammans med URI: n för vårt Service Broker-program för att se till att tokens returneras till rätt program. Vi kräver att du upprättar denna unika omdirigerings-URI både i ditt program och anger som en omdirigerings-URI i vår Developer-Portal

Din omdirigerings-URI måste ha rätt format:

`<app-scheme>://<your.bundle.id>`

t. ex. *x-msauth-mytestiosapp://com.myapp.mytestapp*

Den här omdirigerings-URI: n måste anges i appens registrering med hjälp av [Azure Portal](https://portal.azure.com/). Mer information om registrering av Azure AD-appar finns i [integrera med Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Steg 3a: Lägg till en omdirigerings-URI i appen och dev-portalen för att stödja certifikatbaserad autentisering

För att ge stöd för certifikatbaserad autentisering måste en andra "msauth" registreras i ditt program och [Azure Portal](https://portal.azure.com/) för att hantera certifikatautentisering om du vill lägga till det stödet i ditt program.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

till exempel: *msauth://Code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Steg 4: Lägg till en konfigurations parameter till din app

ADAL använder – canOpenURL: för att kontrol lera om Service Broker är installerat på enheten. I iOS 9 på är Apple låst nedåt vilka scheman ett program kan fråga efter. Du måste lägga till "msauth" i LSApplicationQueriesSchemes-avsnittet i `info.plist file` .

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Du har konfigurerat SSO!

Nu kommer Identity SDK automatiskt att både dela autentiseringsuppgifter i dina program och anropa Service Broker om den finns på deras enhet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
