---
title: Hämta en token i ett Android-program
titleSuffix: Azure AD B2C
description: Så här skapar du en Android-app som använder AppAuth med Azure Active Directory B2C för att hantera användar identiteter och autentisera användare.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: acbd2918bd311cec1c27018763ad10771d779d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953329"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Logga in med ett Android-program i Azure Active Directory B2C

Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Med dessa standarder kan du använda alla bibliotek som du vill integrera med Azure Active Directory B2C. För att hjälpa dig att använda andra bibliotek kan du använda en genom gång som här för att demonstrera hur du konfigurerar bibliotek från tredje part för att ansluta till Microsoft Identity Platform. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft Identity Platform.

> [!WARNING]
> Microsoft tillhandahåller inte korrigeringar för bibliotek från tredje part och har inte utfört en granskning av dessa bibliotek. Det här exemplet använder ett bibliotek från tredje part med namnet AppAuth som har testats för kompatibilitet i grundläggande scenarier med Azure AD B2C. Problem och funktions begär Anden ska dirigeras till bibliotekets projekt med öppen källkod. Mer information finns i [den här artikeln](../active-directory/develop/reference-v2-libraries.md) .
>
>

Om du inte har erfarenhet av OAuth2 eller OpenID Connect kanske du inte får ut så mycket av den här exempelkonfigurationen. Vi rekommenderar att du läser en kort [översikt över protokollet som vi har dokumenterat här](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Hämta en Azure AD B2C-katalog

Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en container för alla användare, appar, grupper och mer. Om du inte redan har en [skapar du en B2C-katalog](tutorial-create-tenant.md) innan du fortsätter.

## <a name="create-an-application"></a>Skapa ett program

Registrera sedan ett program i Azure AD B2C klient organisationen. Det ger Azure AD den information som krävs för att kommunicera säkert med din app.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registrera **program-ID: t (Client)** för användning i ett senare steg.

Registrera även en anpassad omdirigerings-URI för användning i ett senare steg. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Skapa dina användar flöden

I Azure AD B2C definieras varje användar upplevelse av ett [användar flöde](user-flow-overview.md), vilket är en uppsättning principer som styr beteendet för Azure AD. Det här programmet kräver ett användar flöde för inloggning och registrering. När du skapar användar flödet måste du se till att:

* Välj **visnings namnet** som ett registrerings-attribut i ditt användar flöde.
* Välj **visnings namn** och **objekt-ID** för program anspråk i varje användar flöde. Du kan också välja andra anspråk.
* Kopiera **namnet** på varje användar flöde efter att du har skapat det. Det bör ha prefixet `b2c_1_`.  Du behöver användar flödes namnet senare.

När du har skapat dina användar flöden är du redo att skapa din app.

## <a name="download-the-sample-code"></a>Hämta exempel koden

Vi har tillhandahållit ett fungerande exempel som använder AppAuth med Azure AD B2C [på GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Du kan ladda ned koden och köra den. Du kan snabbt komma igång med din egen app med din egen Azure AD B2C konfiguration genom att följa anvisningarna i [Readme.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Exemplet är en ändring av exemplet som tillhandahålls av [AppAuth](https://openid.github.io/AppAuth-Android/). Besök sidan om du vill veta mer om AppAuth och dess funktioner.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändra appen så att den använder Azure AD B2C med AppAuth

> [!NOTE]
> AppAuth stöder Android API 16 (JellyBean) och senare. Vi rekommenderar att du använder API 23 och senare.
>

### <a name="configuration"></a>Konfiguration

Du kan konfigurera kommunikation med Azure AD B2C genom att antingen ange identifierings-URI eller genom att ange både behörighets slut punkt och token slut punkt URI. I båda fallen behöver du följande information:

* Klient-ID (t. ex. contoso.onmicrosoft.com)
* Användar flödes namn (t. ex. B2C \_ 1 \_ -registrering)

Om du väljer att automatiskt identifiera auktoriserings-och token-slutpunkts-URI: erna måste du hämta information från identifierings-URI: n. Identifierings-URI: n kan genereras genom att ersätta `<tenant-id>` och `<policy-name>` i följande URL:

```java
String mDiscoveryURI = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/v2.0/.well-known/openid-configuration";
```

Du kan sedan hämta token för auktorisering och token för slut punkt och skapa ett AuthorizationServiceConfiguration-objekt genom att köra följande:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

I stället för att använda identifiering för att hämta slut punkts-URI: erna för auktorisering och token, kan du också ange dem explicit genom att ersätta `<tenant-id>` och `<policy-name>` i URL: erna nedan:

```java
String mAuthEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/authorize";

String mTokenEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/token";
```

Kör följande kod för att skapa AuthorizationServiceConfiguration-objektet:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Auktorisera

När du har konfigurerat eller hämtat en Authorization service-konfiguration kan en auktoriseringsbegäran konstrueras. Om du vill skapa begäran behöver du följande information:

* Klient-ID (program-ID) som du registrerade tidigare. Till exempel `00000000-0000-0000-0000-000000000000`.
* Anpassad omdirigerings-URI som du registrerade tidigare. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Båda objekten bör ha sparats när du [registrerade appen](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

I [AppAuth-guiden](https://openid.github.io/AppAuth-Android/) finns information om hur du Slutför resten av processen. Om du snabbt behöver komma igång med en fungerande app kan du titta på [vårt exempel](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Följ stegen i [Readme.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) för att ange din egen Azure AD B2C konfiguration.