---
title: ADAL till MSAL migration-guide för Android | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du migrerar din Android-app för Azure Active Directory Authentication Library (ADAL) till Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 10/14/2020
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: ba639bc023affc7c2e6b2b675cdedc1229636893
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581048"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>ADAL till MSAL migration guide för Android

I den här artikeln beskrivs de ändringar du behöver göra för att migrera en app som använder ADAL (Azure Active Directory Authentication Library) för att använda Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Skillnader i fokus

ADAL fungerar med Azure Active Directory v 1.0-slutpunkten. Microsoft Authentication Library (MSAL) fungerar med Microsoft Identity Platform – tidigare kallat Azure Active Directory v 2.0-slutpunkten. Microsoft Identity Platform skiljer sig från Azure Active Directory v 1.0 på så här:

Uppfyller
  - Organisations identitet (Azure Active Directory)
  - Icke-organisatoriska identiteter som Outlook.com, Xbox Live och så vidare
  - (Endast Azure AD B2C) Federerad inloggning med Google, Facebook, Twitter och Amazon

- Är standarder som är kompatibla med:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

MSAL offentliga API inför viktiga ändringar, inklusive:

- En ny modell för åtkomst till token:
  - ADAL ger åtkomst till token via `AuthenticationContext` , som representerar-servern. MSAL ger åtkomst till token via `PublicClientApplication` , som representerar-klienten. Klient utvecklare behöver inte skapa en ny `PublicClientApplication` instans för varje myndighet som de behöver för att interagera med. Endast en `PublicClientApplication` konfiguration krävs.
  - Stöd för att begära åtkomsttoken med hjälp av omfattningar, förutom resurs identifierare.
  - Stöd för stegvist godkännande. Utvecklare kan begära omfattningar när användaren får åtkomst till fler och fler funktioner i appen, inklusive de som inte ingår under registreringen av appen.
  - Myndigheterna verifieras inte längre vid körning. I stället deklareras utvecklaren av en lista med kända myndigheter under utvecklingen.
- API-ändringar för token:
  - I ADAL `AcquireToken()` skapar först en tyst begäran. Detta innebär att en interaktiv begäran görs. Detta beteende ledde till att vissa utvecklare bara förlitar sig på `AcquireToken` , vilket ledde till att användaren uppmanas att ange autentiseringsuppgifter vid ett tillfälle. MSAL kräver att utvecklare är avsiktliga när användaren får en UI-prompt.
    - `AcquireTokenSilent` resulterar alltid i en tyst begäran som antingen lyckas eller Miss lyckas.
    - `AcquireToken` alltid resulterar i en begäran som efterfrågar användaren via användar gränssnittet.
- MSAL stöder inloggning från antingen en standard webbläsare eller en inbäddad webbvy:
  - Som standard används standard webbläsaren på enheten. Detta gör att MSAL kan använda Authentication State (cookies) som redan finns för ett eller flera konton som är inloggade. Om det inte finns något autentiserings tillstånd kan autentisering under auktorisering via MSAL resultera i autentiserings tillstånd (cookies) som skapas för att dra nytta av andra webb program som ska användas i samma webbläsare.
- Ny undantags modell:
  - Undantagen tydligare definierar den typ av fel som inträffat och vad utvecklare måste göra för att lösa det.
- MSAL stöder parameter objekt för `AcquireToken` och- `AcquireTokenSilent` anrop.
- MSAL stöder deklarativ konfiguration för:
  - Klient-ID, omdirigerings-URI.
  - Inbäddad vs standard-webbläsare
  - Skattemyndigheter
  - HTTP-inställningar som Läs-och tids gräns för anslutning

## <a name="your-app-registration-and-migration-to-msal"></a>Din app-registrering och migrering till MSAL

Du behöver inte ändra din befintliga App-registrering för att använda MSAL. Om du vill dra nytta av stegvisa/progressiva medgivande kan du behöva granska registreringen för att identifiera de speciella omfattningar som du vill begära stegvis. Mer information om omfattningar och stegvisa godkännanden följer.

I din app-registrering i portalen visas fliken **API-behörigheter** . Detta ger en lista med de API: er och behörigheter (omfattningar) som din app är konfigurerad för att begära åtkomst till. Den visar också en lista över de omfångs namn som är associerade med varje API-behörighet.

### <a name="user-consent"></a>Användargodkännande

Med ADAL och Azure AD v1-slutpunkten beviljades användaren de resurser de äger vid första användningen. Med MSAL och Microsoft Identity Platform kan medgivande begäras stegvis. Ett stegvist godkännande är användbart för behörigheter som en användare kan överväga med hög behörighet eller som annars kan frågas om det inte finns en tydlig förklaring av varför behörigheten krävs. I ADAL kan de behörigheterna ha resulterat i att användaren överger inloggningen i din app.

> [!TIP]
> Använd ett stegvist medgivande för att ge ytterligare kontext till dina användare om varför appen behöver en behörighet.

### <a name="admin-consent"></a>Administratörsmedgivande

Organisations administratörer kan samtycka till att ge ditt program behörighet för alla medlemmar i organisationen. Vissa organisationer tillåter bara administratörer att samtycka till program. Administratörs medgivande kräver att du inkluderar alla API-behörigheter och omfattningar som används av ditt program i din app-registrering.

> [!TIP]
> Även om du kan begära ett omfång med MSAL för något som inte ingår i din app-registrering, rekommenderar vi att du uppdaterar registreringen av appen så att den innehåller alla resurser och omfattningar som en användare någonsin kan bevilja behörighet till.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrera från resurs-ID till omfång

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autentisera och begär auktorisering för alla behörigheter vid första användningen

Om du för närvarande använder ADAL och inte behöver använda ett stegvist tillstånd är det enklaste sättet att börja använda MSAL att göra en `acquireToken` begäran med det nya `AcquireTokenParameter` objektet och ange resurs-ID-värdet.

> [!CAUTION]
> Det går inte att ange både scope och resurs-ID. Om du försöker ange båda leder det till en `IllegalArgumentException` .

Detta leder till samma v1-beteende som du använder. Alla behörigheter som begärs i din app-registrering begärs från användaren under den första interaktionen.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Autentisera och begär bara behörigheter vid behov

Om du vill dra nytta av ett stegvist godkännande ska du göra en lista över behörigheter (omfattningar) som appen använder från din app-registrering och organisera dem i två listor baserat på:

- Vilka omfattningar du vill begära under användarens första interaktion med din app under inloggningen.
- De behörigheter som associeras med en viktig funktion i din app som du också måste förklara för användaren.

När du har ordnat omfattningarna ordnar du varje lista efter vilken resurs (API) du vill begära en token för. Samt andra omfång som du vill att användaren ska auktorisera samtidigt.

Parameter-objektet som används för att göra din begäran till MSAL stöder:

- `Scope`: Listan över omfattningar som du vill begära auktorisering för och ta emot en åtkomsttoken.
- `ExtraScopesToConsent`: En ytterligare lista med omfattningar som du vill begära auktorisering för när du begär en åtkomsttoken för en annan resurs. Med hjälp av den här listan över omfång kan du minimera antalet gånger som du behöver begära användar godkännande. Vilket innebär färre användares auktorisering eller medgivande.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrera från AuthenticationContext till PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Konstruera PublicClientApplication

När du använder MSAL instansierar du en `PublicClientApplication` . Det här objektet modellerar appens identitet och används för att göra förfrågningar till en eller flera utfärdare. Med det här objektet konfigurerar du din klient identitet, omdirigerings-URI, standard auktoritet, om du vill använda enhetens webbläsare jämfört med inbäddad webbvy, loggnings nivå och mycket mer.

Du kan deklarativ konfigurera det här objektet med JSON, som du antingen anger som en fil eller lagra som en resurs i din APK.

Även om det här objektet inte är en singleton, används internt `Executors` av det delade för både interaktiva och tysta begär Anden.

### <a name="business-to-business"></a>Företag till företag

I ADAL kräver varje organisation som du begär åtkomst-token från en separat instans av `AuthenticationContext` . I MSAL är detta inte längre ett krav. Du kan ange från vilken myndighet du vill begära en token som en del av din obevakade eller interaktiva begäran.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrera från auktoritets verifiering till kända myndigheter

MSAL har inte någon flagga för att aktivera eller inaktivera verifiering av utfärdare. Verifiering av auktoritet är en funktion i ADAL och i tidiga versioner av MSAL som förhindrar din kod från att begära token från en potentiellt skadlig auktoritet. MSAL hämtar nu en lista över myndigheter som är kända för Microsoft och sammanfogar listan med de myndigheter som du har angett i konfigurationen.

> [!TIP]
> Om du är en Azure Business to Consumer-användare (B2C) innebär det att du inte längre behöver inaktivera verifiering av utfärdare. Inkludera i stället var och en av de Azure AD B2C principerna som stöds som utfärdare i din MSAL-konfiguration.

Om du försöker använda en auktoritet som inte är känd för Microsoft, och som inte ingår i din konfiguration, får du en `UnknownAuthorityException` .

### <a name="logging"></a>Loggning
Du kan nu deklarativ Konfigurera loggning som en del av konfigurationen, så här:

```json
"logging": {
  "pii_enabled": false,
  "log_level": "WARNING",
  "logcat_enabled": true
}
```

## <a name="migrate-from-userinfo-to-account"></a>Migrera från UserInfo till konto

I ADAL `AuthenticationResult` tillhandahåller ett- `UserInfo` objekt som används för att hämta information om det autentiserade kontot. Termen "användare", som avsåg en mänsklig-eller program varu agent, tillämpades på ett sätt som gjorde det svårt att kommunicera med att vissa appar har stöd för en enskild användare (oavsett om det gäller en mänsklig-eller program varu agent) som har flera konton.

Överväg ett bank konto. Du kan ha fler än ett konto på mer än ett finansiellt institut. När du öppnar ett konto tilldelas du (användaren) autentiseringsuppgifter, till exempel ett ATM-kort & PIN-kod, som används för att få åtkomst till saldot, faktura betalningar och så vidare för varje konto. Dessa autentiseringsuppgifter kan endast användas på det finansiella institutet som utfärdade dem.

Med hjälp av analogt, t. ex. konton i ett finansiellt institut, kan konton i Microsoft Identity Platform nås med hjälp av autentiseringsuppgifter. Dessa autentiseringsuppgifter är antingen registrerade med eller utfärdade av, Microsoft. Eller av Microsoft på uppdrag av en organisation.

Om Microsofts identitets plattform skiljer sig från ett finansiellt institut är det i den här analoga fallet att Microsoft Identity Platform tillhandahåller ett ramverk som gör att en användare kan använda ett konto och dess associerade autentiseringsuppgifter för att få åtkomst till resurser som tillhör flera personer och organisationer. Detta är som att kunna använda ett kort som utfärdats av en bank, men ännu en annan finansiell institution. Detta fungerar eftersom alla organisationer i fråga använder Microsofts identitets plattform, vilket gör att ett konto kan användas i flera organisationer. Här är ett exempel:

Sam fungerar för Contoso.com men hanterar virtuella Azure-datorer som tillhör Fabrikam.com. För att Sam ska kunna hantera virtuella datorer i Fabrikam måste han ha behörighet att komma åt dem. Den här åtkomsten kan beviljas genom att lägga till Sam-kontot i Fabrikam.com och beviljar sitt konto en roll som gör att han kan arbeta med de virtuella datorerna. Detta gör du med Azure Portal.

Om du lägger till Sams Contoso.com-konto som en medlem i Fabrikam.com skulle det leda till att en ny post skapas i Fabrikam. com: s Azure Active Directory för Sam. Sam-posten i Azure Active Directory kallas för ett användar objekt. I det här fallet pekar användarobjektet tillbaka till Sams användar objekt i Contoso.com. Sam: s Fabrikam User-objekt är den lokala åter givningen av Sam och används för att lagra information om det konto som är associerat med Sam i kontexten för Fabrikam.com. I Contoso.com är Sam-titeln erfaren DevOps-konsult. I Fabrikam är Sam-titeln Contractor-Virtual datorer. I Contoso.com är Sam inte ansvarigt eller auktoriserat för att hantera virtuella datorer. I Fabrikam.com är det bara jobb funktionen. Sam har fortfarande bara en uppsättning autentiseringsuppgifter för att hålla reda på, vilka är de autentiseringsuppgifter som utfärdats av Contoso.com.

När ett lyckat `acquireToken` anrop görs visas en referens till ett `IAccount` objekt som kan användas i senare `acquireTokenSilent` förfrågningar.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Om du har en app som har åtkomst till anspråk om ett konto från var och en av de klienter där kontot visas, kan du omvandla `IAccount` objekt till `IMultiTenantAccount` . Det här gränssnittet innehåller en karta över `ITenantProfiles` , som ställts in efter klient-ID, som gör att du kan komma åt de anspråk som tillhör kontot i varje klient organisation som du har begärt en token från, i förhållande till det aktuella kontot.

Anspråk i roten av `IAccount` och `IMultiTenantAccount` innehåller alltid anspråk från hem klienten. Om du ännu inte har gjort en begäran om en token inom hem klienten, kommer den här samlingen att vara tom.

## <a name="other-changes"></a>Andra ändringar

### <a name="use-the-new-authenticationcallback"></a>Använd den nya AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}
```

## <a name="migrate-to-the-new-exceptions"></a>Migrera till de nya undantagen

I ADAL finns en typ av undantag, `AuthenticationException` som innehåller en metod för att hämta `ADALError` Enum-värdet.
I MSAL finns det en hierarki med undantag och var och en har en egen uppsättning av tillhör ande specifika felkoder.

| Undantag                                        | Beskrivning                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalArgumentException`                          | Utlöstes om ett eller flera argument för indata är ogiltiga.                 |
| `MsalClientException`                            | Genereras om felet är klient sidan.                                 |
| `MsalDeclinedScopeException`                     | Utlöses om en eller flera begärda omfattningar nekades av servern. |
| `MsalException`                                  | Ett checkat standard undantag utlöstes av MSAL.                           |
| `MsalIntuneAppProtectionPolicyRequiredException` | Utlöses om MAMCA Protection-princip är aktive rad i resursen.         |
| `MsalServiceException`                           | Utlöstes om felet är Server sidan.                                 |
| `MsalUiRequiredException`                        | Genereras om token inte kan uppdateras tyst.                    |
| `MsalUserCancelException`                        | Utlöses om användaren avbröt verifierings flödet.                |

### <a name="adalerror-to-msalexception-translation"></a>ADALError till MsalException-Översättning

| Om du fångar de här felen i ADAL...  | ... fånga dessa MSAL-undantag:                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| *Ingen motsvarande ADALError* | `MsalArgumentException`                          |
| <ul><li>`ADALError.ANDROIDKEYSTORE_FAILED`<li>`ADALError.AUTH_FAILED_USER_MISMATCH`<li>`ADALError.DECRYPTION_FAILED`<li>`ADALError.DEVELOPER_AUTHORITY_CAN_NOT_BE_VALIDED`<li>`ADALError.EVELOPER_AUTHORITY_IS_NOT_VALID_INSTANCE`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_URL`<li>`ADALError.DEVICE_CONNECTION_IS_NOT_AVAILABLE`<li>`ADALError.DEVICE_NO_SUCH_ALGORITHM`<li>`ADALError.ENCODING_IS_NOT_SUPPORTED`<li>`ADALError.ENCRYPTION_ERROR`<li>`ADALError.IO_EXCEPTION`<li>`ADALError.JSON_PARSE_ERROR`<li>`ADALError.NO_NETWORK_CONNECTION_POWER_OPTIMIZATION`<li>`ADALError.SOCKET_TIMEOUT_EXCEPTION`</ul> | `MsalClientException`                            |
| *Ingen motsvarande ADALError* | `MsalDeclinedScopeException`                     |
| <ul><li>`ADALError.APP_PACKAGE_NAME_NOT_FOUND`<li>`ADALError.BROKER_APP_VERIFICATION_FAILED`<li>`ADALError.PACKAGE_NAME_NOT_FOUND`</ul> | `MsalException`                                  |
| *Ingen motsvarande ADALError* | `MsalIntuneAppProtectionPolicyRequiredException` |
| <ul><li>`ADALError.SERVER_ERROR`<li>`ADALError.SERVER_INVALID_REQUEST`</ul> | `MsalServiceException`                           |
| <ul><li>`ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED` | `MsalUiRequiredException`</ul>                        |
| *Ingen motsvarande ADALError* | `MsalUserCancelException`                        |

### <a name="adal-logging-to-msal-logging"></a>ADAL-loggning till MSAL-loggning

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
      @Override
      public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
          logs.append(message).append('\n');
      }
  });

// New Log Levels:
public enum LogLevel
{
    /**
     * Error level logging.
     */
    ERROR,
    /**
     * Warning level logging.
     */
    WARNING,
    /**
     * Info level logging.
     */
    INFO,
    /**
     * Verbose level logging.
     */
    VERBOSE
}
```
