---
title: Aktivera enkel inloggning mellan appar på Android med MSAL | Azure
titleSuffix: Microsoft identity platform
description: Hur du använder Microsoft Authentication Library (MSAL) för Android för att aktivera enkel inloggning i dina program.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f3d4ec8db89e9bebfdcd594e842a6c19d3d66d54
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104101"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Gör så här: aktivera enkel inloggning mellan appar på Android med MSAL

Enkel inloggning (SSO) gör det möjligt för användare att bara ange sina autentiseringsuppgifter en gång och låta dessa autentiseringsuppgifter automatiskt fungera mellan program.

[Microsoft Identity Platform](./index.yml) och Microsoft Authentication Library (MSAL) hjälper dig att aktivera SSO i din egen uppsättning appar. Med hanterings funktionerna för Service Broker och autentiserare kan du utöka SSO över hela enheten.

I den här instruktionen får du lära dig hur du konfigurerar SDK: er som används av ditt program för att tillhandahålla enkel inloggning till dina kunder.

## <a name="prerequisites"></a>Förutsättningar

Den här instruktionen förutsätter att du vet hur du:

- Etablera din app med hjälp av Azure Portal. Mer information om det här ämnet finns i anvisningarna för att skapa en app i [Android-kursen](./tutorial-v2-android.md#create-a-project)
- Integrera ditt program med [Microsoft Authentication Library för Android](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Metoder för enkel inloggning

Det finns två sätt för program som använder MSAL för Android att uppnå SSO:

* Via ett [Service Broker-program](#sso-through-brokered-authentication)
* Via [system webbläsare](#sso-through-system-browser)


   Vi rekommenderar att du använder en Service Broker-applikation för fördelar som enkel inloggning på enhets nivå, konto hantering och villkorlig åtkomst. Det kräver dock att användarna hämtar ytterligare program.

## <a name="sso-through-brokered-authentication"></a>Enkel inloggning via Broker-autentisering

Vi rekommenderar att du använder någon av Microsofts autentiserings mäklare för att delta i enkel inloggning (SSO) för hela enheten och för att uppfylla organisationens principer för villkorlig åtkomst. Att integrera med en Service Broker ger följande fördelar:

- Enkel inloggning för enhet
- Villkorlig åtkomst för:
  - Intune App Protection
  - Enhets registrering (Workplace Join)
  - Hantering av mobila enheter
- Konto hantering för hela enheten
  -  via Android-AccountManager & konto inställningar
  - "Arbets konto" – anpassad kontotyp

På Android är Microsoft Authentication Broker en komponent som ingår i [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) och [Intune-företagsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) appar.

Följande diagram illustrerar förhållandet mellan appen, Microsoft Authentication Library (MSAL) och Microsofts autentiserings mäklare.

![Diagram över hur ett program relaterar till MSAL, Broker-appar och Android-konto hanteraren.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Installera appar som är värdar för en Broker

Broker-värdbaserade appar kan installeras av enhetens ägare från App Store (vanligt vis Google Play Butik). Vissa API: er (resurser) skyddas dock av principer för villkorlig åtkomst som kräver att enheter:

- Registrerad (arbets plats ansluten) och/eller
- Registrerad i enhets hantering eller
- Registrerad i Intune-appskydd

Om en enhet inte redan har en Service Broker-app installerad instruerar MSAL användaren att installera en så fort appen försöker hämta en token interaktivt. Appen måste sedan leda användaren genom stegen för att göra enheten kompatibel med den princip som krävs.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effekter av installation och avinstallation av en Broker

#### <a name="when-a-broker-is-installed"></a>När en Service Broker installeras

När en Broker installeras på en enhet hanteras alla efterföljande interaktiva Tokenbegäran (anrop till `acquireToken()` ) av Service Broker i stället för lokalt av MSAL. SSO-tillstånd som tidigare var tillgängligt för MSAL är inte tillgängligt för Broker. Detta innebär att användaren måste autentisera igen eller välja ett konto i den befintliga listan över konton som är kända för enheten.

Att installera en Broker kräver inte att användaren loggar in igen. Endast när användaren behöver matcha en `MsalUiRequiredException` skickas nästa begäran till Service Broker. `MsalUiRequiredException` kan genereras av flera olika orsaker och måste lösas interaktivt. Exempel:

- Användaren ändrade lösen ordet som är kopplat till sitt konto.
- Användarens konto uppfyller inte längre en princip för villkorlig åtkomst.
- Användaren återkallade sitt medgivande för att appen ska associeras med sitt konto.

**Multiple-hanterare** – om flera hanterare är installerade på en enhet är den Broker som installerades först alltid den aktiva Broker. Endast en enskild Broker kan vara aktiv på en enhet.

#### <a name="when-a-broker-is-uninstalled"></a>När en Broker avinstalleras

Om det bara finns en Service Broker-värd app installerad och den tas bort måste användaren logga in igen. Om du avinstallerar den aktiva Broker tas kontot och tillhör ande token bort från enheten.

Om Intune-företagsportal är installerat och fungerar som den aktiva utjämningen, och Microsoft Authenticator också är installerat, kommer användaren att behöva logga in igen om Intune-företagsportal (aktiv Broker) har avinstallerats. När de loggar in igen blir Microsoft Authenticator-appen den aktiva koordinatorn.

### <a name="integrating-with-a-broker"></a>Integrera med en Service Broker

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Generera en omdirigerings-URI för en Broker

Du måste registrera en omdirigerings-URI som är kompatibel med Broker. Omdirigerings-URI: n för Service Broker ska innehålla appens paket namn och den base64-kodade åter givningen av appens signatur.

Formatet för omdirigerings-URI: n är: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Du kan använda [verktyget](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) för att generera en Base64-kodad signatur hash med hjälp av appens signerings nycklar och sedan använda Azure Portal för att generera omdirigerings-URI: n med denna hash.

Linux och macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

När du har genererat en signatur-hash med ett- *verktyg* använder du Azure Portal för att generera omdirigerings-URI: n:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> och välj din Android-app i **Appregistreringar**.
1. Välj **autentisering**  >  **Lägg till en plattforms**-  >  **Android**.
1. I fönstret **Konfigurera din Android-app** som öppnas anger du **signaturens hash** som du skapade tidigare och ett **paket namn**.
1. Välj knappen **Konfigurera** .

Azure Portal genererar omdirigerings-URI: n åt dig och visas i fältet för **omdirigerings-URI** för **Android** -fönstret.

Mer information om hur du signerar din app finns i [signera din app](https://developer.android.com/studio/publish/app-signing) i användar handboken för Android Studio.

> [!IMPORTANT]
> Använd din produktions signerings nyckel för produktions versionen av din app.

#### <a name="configure-msal-to-use-a-broker"></a>Konfigurera MSAL för att använda en Service Broker

Om du vill använda en Broker i din app måste du intyga att du har konfigurerat omdirigeringen av Service Broker. Inkludera till exempel både din Broker-aktiverade omdirigerings-URI – och indikerar att du har registrerat den, genom att inkludera följande inställningar i MSAL-konfigurations filen:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Undantag som rör Broker

MSAL kommunicerar med Service Broker på två sätt:

- Service Broker-bindning
- Android-AccountManager

MSAL använder först den Broker-kopplade tjänsten eftersom det inte krävs några Android-behörigheter för att anropa den här tjänsten. Om bindningen till den kopplade tjänsten Miss lyckas använder MSAL Android AccountManager-API: et. MSAL gör det bara om din app redan har beviljats `"READ_CONTACTS"` behörigheten.

Om du får en `MsalClientException` med-felkod `"BROKER_BIND_FAILURE"` finns det två alternativ:

- Be användaren att inaktivera energi optimering för Microsoft Authenticator-appen och Intune-företagsportal.
- Be användaren att bevilja `"READ_CONTACTS"` behörigheten

### <a name="verify-broker-integration"></a>Verifiera Broker-integrering

Det kan hända att den inte omedelbart rensas om Broker-integreringen fungerar, men du kan använda följande steg för att kontrol lera:

1. På din Android-enhet slutför du en begäran med hjälp av Service Broker.
1. I inställningarna på din Android-enhet letar du efter ett nytt konto som motsvarar det konto som du autentiserade med. Kontot måste vara av typen *arbets konto*.

Du kan ta bort kontot från inställningarna om du vill upprepa testet.

## <a name="sso-through-system-browser"></a>Enkel inloggning via system webbläsare

Android-program har möjlighet att använda webbvyerna webbvy, system Browser eller Chrome anpassade flikar för användar upplevelsen för autentisering. Om programmet inte använder reserverad autentisering måste du använda systemets webbläsare i stället för den inbyggda WebView för att kunna uppnå SSO.

### <a name="authorization-agents"></a>Auktoriseringsagenter

Att välja en speciell strategi för auktoriserings agenter är valfritt och representerar ytterligare funktioner som kan anpassas. De flesta appar använder MSAL-standardvärdena (se [förstå konfigurations filen för Android-MSAL](msal-configuration.md) för att se de olika standardvärdena).

MSAL stöder auktorisering med hjälp av en `WebView` eller system webbläsare.  Bilden nedan visar hur den ser ut med hjälp av `WebView` , eller system läsaren med CustomTabs eller utan CustomTabs:

![Exempel på MSAL-inloggning](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Konsekvenser för enkel inloggning

Som standard använder program som är integrerade med MSAL de anpassade flikarna i system webbläsare för att godkänna. Till skillnad från webbvyer delar de anpassade flikarna med standard system webbläsaren och gör färre inloggningar med webb-eller andra inbyggda appar som är integrerade med anpassade flikar.

Om programmet använder en `WebView` strategi utan att integrera Microsoft Authenticator eller företagsportal stöd i appen, kommer användarna inte att ha en enkel inloggnings upplevelse på enheten eller mellan inbyggda appar och webbappar.

Om programmet använder MSAL med en Service Broker som Microsoft Authenticator eller Intune-företagsportal kan användarna ha en SSO-upplevelse över program om de har en aktiv inloggning med en av apparna.

### <a name="webview"></a>WebView

Om du vill använda webbvy i appen, Lägg följande rad i JSON-appens konfiguration som skickas till MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

När du använder appen i appen `WebView` loggar användaren in direkt till appen. Token behålls inuti appens Sand låda och är inte tillgängliga utanför appens jar-jar. Det innebär att användaren inte kan ha en SSO-upplevelse över program om inte apparna integreras med autentiseraren eller Företagsportal.

Men `WebView` ger dig möjlighet att anpassa utseendet och känslan för inloggnings gränssnittet. Mer information om hur du gör denna anpassning finns i avsnittet om [Android-webbvyer](https://developer.android.com/reference/android/webkit/WebView) .

### <a name="default-browser-plus-custom-tabs"></a>Standard webbläsare plus anpassade flikar

Som standard använder MSAL webbläsaren och en strategi för [anpassade flikar](https://developer.chrome.com/multidevice/android/customtabs) . Du kan uttryckligen ange den här strategin för att förhindra ändringar i framtida versioner till `DEFAULT` genom att använda följande JSON-konfiguration i den anpassade konfigurations filen:

```json
"authorization_user_agent" : "BROWSER"
```

Använd den här metoden för att tillhandahålla enkel inloggning via enhetens webbläsare. MSAL använder en gemensam cookie-jar, som gör att andra inbyggda appar eller webbappar kan nå SSO på enheten med hjälp av den sparade sessions-cookien som anges av MSAL.

### <a name="browser-selection-heuristic"></a>Heuristiska val av webbläsare

Eftersom det är omöjligt för MSAL att ange det exakta webb läsar paket som ska användas på var och en av de breda Android-telefoner, implementerar MSAL en heuristik för val av webbläsare som försöker tillhandahålla det bästa SSO-värdet mellan enheter.

MSAL hämtar främst standard webbläsaren från Package Manager och kontrollerar om den finns i en testad lista över säkra webbläsare. Om inte, går MSAL tillbaka med webbvy i stället för att starta en annan webbläsare som inte är standard från listan över betrodda användare. Standard webbläsaren väljs oavsett om den stöder anpassade flikar eller inte. Om webbläsaren har stöd för anpassade flikar, kommer MSAL att starta den anpassade fliken. anpassade flikar har en känsla som är närmare för en app i appen `WebView` och möjliggör grundläggande anpassning av gränssnittet. Mer information finns i [anpassade flikar i Android](https://developer.chrome.com/multidevice/android/customtabs) .

Om det inte finns några webb läsar paket på enheten använder MSAL i appen `WebView` . Om enhetens standardinställning inte ändras ska samma webbläsare startas för varje inloggning för att säkerställa en SSO-upplevelse.

#### <a name="tested-browsers"></a>Testade webbläsare

Följande webbläsare har testats för att se om de är korrekt omdirigering till den `"redirect_uri"` angivna i konfigurations filen:

| Enhet | Inbyggd webbläsare | Chrome | Opera  | Microsoft Edge | UC-webbläsare | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | pass | pass |ej tillämpligt |ej tillämpligt |ej tillämpligt |ej tillämpligt |
| Samsung S7 (API 25) | pass<sup>1</sup> | pass | pass | pass | kanske |pass |
| Huawei (API 26) |pass<sup>2</sup> | pass | kanske | pass | pass |pass |
| Vivo (API 26) |pass|pass|pass|pass|pass|kanske|
| Pixel 2 (API 26) |pass | pass | pass | pass | kanske |pass |
| OPPO | pass | inte tillämpligt<sup>3</sup>|ej tillämpligt  |ej tillämpligt |ej tillämpligt | ej tillämpligt|
| OnePlus (API 25) |pass | pass | pass | pass | kanske |pass |
| Nexus (API 28) |pass | pass | pass | pass | kanske |pass |
|MI | pass | pass | pass | pass | kanske |pass |

<sup>1</sup> Samsung: s inbyggda webbläsare är Samsung Internet.<br/>
<sup>2</sup> Huawei: s inbyggda webbläsare är Huawei webbläsare.<br/>
<sup>3</sup> Det går inte att ändra standard webbläsaren i enhets inställningen OPPO.

## <a name="next-steps"></a>Nästa steg

Med [delad enhets läge för Android-enheter](msal-android-shared-devices.md) kan du konfigurera en Android-enhet så att den enkelt kan delas av flera anställda.