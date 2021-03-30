---
title: Konfigurations fil för Android-MSAL | Azure
titleSuffix: Microsoft identity platform
description: En översikt över Android Microsoft Authentication Library (MSAL)-konfigurations filen som representerar ett programs konfiguration i Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: aa0ce6a5f909e67f0551c8667bb7e5c5e6d7eb04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92275609"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Konfigurations fil för Android Microsoft Authentication Library

Android Microsoft Authentication Library (MSAL) levereras med en [standard konfigurations-JSON-fil](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) som du anpassar för att definiera beteendet för din offentliga klient app för t. ex. standard utfärdare, vilka myndigheter du använder, och så vidare.

I den här artikeln får du hjälp att förstå de olika inställningarna i konfigurations filen och hur du anger konfigurations filen som ska användas i din MSAL-baserade app.

## <a name="configuration-settings"></a>Konfigurationsinställningar

### <a name="general-settings"></a>Allmänna inställningar

| Egenskap | Datatyp | Obligatorisk | Kommentarer |
|-----------|------------|-------------|-------|
| `client_id` | Sträng | Ja | Appens klient-ID från [sidan program registrering](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Sträng | Ja | Appens omdirigerings-URI från [program registrerings sidan](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `broker_redirect_uri_registered` | Boolesk | Inga | Möjliga värden: `true` , `false` |
| `authorities` | Lista\<Authority> | Inga | Listan över myndigheter som appen behöver |
| `authorization_user_agent` | AuthorizationAgent (Enum) | Inga | Möjliga värden: `DEFAULT` , `BROWSER` , `WEBVIEW` |
| `http` | HttpConfiguration | Inga | Konfigurera `HttpUrlConnection` `connect_timeout` och `read_timeout` |
| `logging` | LoggingConfiguration | Inga | Anger nivån för loggnings information. Valfria konfigurationer är: `pii_enabled` , som tar ett booleskt värde och `log_level` , som tar `ERROR` , `WARNING` , `INFO` eller `VERBOSE` . |

### <a name="client_id"></a>client_id

Det klient-ID eller app-ID som skapades när du registrerade ditt program.

### <a name="redirect_uri"></a>redirect_uri

Den omdirigerings-URI som du registrerade när du registrerade ditt program. Om omdirigerings-URI: n är till en Service Broker-app, se [omdirigerings-URI för offentliga klient program](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) för att se till att du använder rätt URI-format för omdirigering för din Service Broker

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Om du vill använda Brokered Authentication `broker_redirect_uri_registered` måste egenskapen anges till `true` . Om programmet inte har rätt format för att kommunicera med koordinatorn enligt beskrivningen i [omdirigerings-URI för offentliga klient program](msal-client-application-configuration.md#redirect-uri-for-public-client-apps), verifierar programmet omdirigerings-URI och genererar ett undantag när det startar.

### <a name="authorities"></a>skattemyndigheter

Listan över utfärdade myndigheter som är kända och betrodda av dig. Utöver de myndigheter som anges här frågar MSAL även Microsoft för att hämta en lista över moln och myndigheter som är kända för Microsoft. I den här listan över utfärdare anger du typ av myndighet och eventuella ytterligare valfria parametrar `"audience"` som, som ska anpassas efter appens mål grupp baserat på appens registrering. Följande är ett exempel på en lista över utfärdare:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenant_id": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Mappa AAD-auktoritet & mål grupp till Microsoft Identity Platform-slutpunkter

| Typ | Målgrupp | Klient-ID:t | Authority_Url | Resulterande slut punkt | Kommentarer |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common` är ett klient Ali Aset för var kontot finns. Till exempel en speciell Azure Active Directory klient organisation eller Microsoft-konto systemet. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Endast konton som finns i contoso.com kan hämta en token. En verifierad domän eller klient-GUID kan användas som klient-ID. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Endast Azure Active Directory-konton kan användas med den här slut punkten. Microsoft-konton kan vara medlemmar i organisationer. Om du vill hämta en token med hjälp av en Microsoft-konto för en resurs i en organisation anger du den organisations klient som du vill använda token från. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Endast Microsoft-konton kan använda den här slut punkten. |
| B2C | | | Se resulterande slut punkt | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Endast konton som finns i contoso.onmicrosoft.com-klienten kan hämta en token. I det här exemplet är B2C-principen en del av sökvägen till auktoritets-URL: en. |

> [!NOTE]
> Det går inte att aktivera verifiering av auktoritet och inaktive rad i MSAL.
> Myndigheterna är antingen kända för dig som utvecklare som de anges via konfiguration eller känd till Microsoft via metadata.
> Om MSAL tar emot en begäran om en token till en okänd auktoritet, ett `MsalClientException` av typ `UnknownAuthority` resultaten.
> Brokered Authentication fungerar inte för Azure AD B2C.

#### <a name="authority-properties"></a>Egenskaper för utfärdare

| Egenskap | Datatyp  | Obligatorisk | Kommentarer |
|-----------|-------------|-----------|--------|
| `type` | Sträng | Ja | Speglar mål gruppen eller konto typen för appens mål. Möjliga värden: `AAD` , `B2C` |
| `audience` | Objekt | Inga | Gäller endast när Type = `AAD` . Anger den identitet som appen är mål för. Använd värdet från din app-registrering |
| `authority_url` | Sträng | Ja | Krävs endast när Type = `B2C` . Anger auktoritets-URL eller princip som din app ska använda  |
| `default` | boolean | Ja | En enskild `"default":true` krävs när en eller flera utfärdare har angetts. |

#### <a name="audience-properties"></a>Egenskaper för publik

| Egenskap | Datatyp  | Obligatorisk | Kommentarer |
|-----------|-------------|------------|-------|
| `type` | Sträng | Ja | Anger den mål grupp som appen vill rikta sig mot. Möjliga värden: `AzureADandPersonalMicrosoftAccount` , `PersonalMicrosoftAccount` , `AzureADMultipleOrgs` , `AzureADMyOrg` |
| `tenant_id` | Sträng | Ja | Krävs endast när `"type":"AzureADMyOrg"` . Valfritt för andra `type` värden. Detta kan vara en klient domän, till exempel `contoso.com` eller ett klient-ID som till exempel `72f988bf-86f1-41af-91ab-2d7cd011db46` ) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Indikerar om en inbäddad webbvy ska användas eller standard webbläsaren på enheten när du loggar in ett konto eller auktoriserar åtkomst till en resurs.

Möjliga värden:
- `DEFAULT`: Föredrar systemets webbläsare. Använder den inbäddade webbvy om en webbläsare inte är tillgänglig på enheten.
- `WEBVIEW`: Använd den inbäddade webb visningen.
- `BROWSER`: Använder standard webbläsaren på enheten.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Ange för klienter som har stöd för flera nationella moln `true` . Microsoft Identity Platform omdirigeras sedan automatiskt till rätt nationellt moln under auktorisering och token-inlösen. Du kan fastställa det nationella molnet för det inloggade kontot genom att undersöka utfärdaren som är associerad med `AuthenticationResult` . Observera att `AuthenticationResult` inte tillhandahåller den nationella molnbaserade slut punkts adressen för den resurs som du begär en token för.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Ett booleskt värde som anger om du använder en Microsoft Identity Broker-kompatibel omdirigerings-URI. Ange som `false` om du inte vill använda Service Broker i din app.

Om du använder AAD-utfärdaren som är inställd på `"MicrosoftPersonalAccount"` , används inte Broker.

### <a name="http"></a>http

Konfigurera globala inställningar för HTTP-timeout, till exempel:

| Egenskap | Datatyp | Obligatorisk | Kommentarer |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Inga | Tid i millisekunder |
| `read_timeout` | int | Inga | Tid i millisekunder |

### <a name="logging"></a>logging

Följande globala inställningar gäller för loggning:

| Egenskap | Datatyp  | Obligatorisk | Kommentarer |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | Inga | Om du vill generera personliga data |
| `log_level`   | sträng | No | Vilka logg meddelanden som ska matas ut. De logg nivåer som stöds är `ERROR` , `WARNING` , `INFO` och `VERBOSE` . |
| `logcat_enabled` | boolean | Inga | Om du vill skriva utdata till log katt förutom loggnings gränssnittet |

### <a name="account_mode"></a>account_mode

Anger hur många konton som kan användas i din app i taget. Möjliga värden är:

- `MULTIPLE` Objekt
- `SINGLE`

Om du skapar ett `PublicClientApplication` med ett konto läge som inte matchar den här inställningen uppstår ett undantag.

Mer information om skillnaderna mellan enkla och flera konton finns i appar för [enkel och flera](single-multi-account.md)konton.

### <a name="browser_safelist"></a>browser_safelist

En lista över tillåtna webbläsare som är kompatibla med MSAL. De här webbläsarna hanterar omdirigeringar korrekt till anpassade avsikter. Du kan lägga till i den här listan. Standardvärdet anges i standard konfigurationen som visas nedan.
``
## <a name="the-default-msal-configuration-file"></a>Standard konfigurations filen för MSAL

Standard konfigurationen för MSAL som levereras med MSAL visas nedan. Du kan se den senaste versionen på [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Den här konfigurationen kompletteras av värden som du anger. Värdena som du anger åsidosätter standardinställningarna.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Exempel på grundläggande konfiguration

I följande exempel visas en grundläggande konfiguration som anger klient-ID, omdirigerings-URI, om en reservice Broker-omdirigering är registrerad och en lista över utfärdare.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Så här använder du en konfigurations fil

1. Skapa en konfigurations fil. Vi rekommenderar att du skapar en anpassad konfigurations fil i `res/raw/auth_config.json` . Men du kan göra det var du vill.
2. Berätta för MSAL var du ska leta efter din konfiguration när du skapar `PublicClientApplication` . Exempel:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
