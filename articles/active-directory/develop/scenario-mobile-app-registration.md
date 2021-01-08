---
title: 'Registrera mobilappar som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er (appens registrering)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: cdd32185d1ffddb4c6bc3d62b91690f68dec17ca
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014730"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registrera mobilappar som anropar webb-API: er

Den här artikeln innehåller anvisningar som hjälper dig att registrera ett mobil program som du skapar.

## <a name="supported-account-types"></a>Kontotyper som stöds

De konto typer som mobil programmen stöder beror på vilken upplevelse du vill aktivera och vilka flöden du vill använda.

### <a name="audience-for-interactive-token-acquisition"></a>Mål grupp för hämtning av interaktiva token

De flesta mobila program använder interaktiv autentisering. Om din app använder den här typen av autentisering, kan du logga in användare från vilken [Kontotyp](quickstart-register-app.md)som helst.

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Mål grupp för integrerad Windows-autentisering, username-Password och B2C

Om du har en Universell Windows-plattform-app (UWP) kan du använda integrerad Windows-autentisering för att logga in användare. Om du vill använda integrerad Windows-autentisering eller användar namn – lösenordsautentisering måste ditt program logga in användare i din egen affärsrelaterad (LOB) utvecklare. I ett oberoende program varu leverantörs scenario (ISV) kan ditt program logga in användare i Azure Active Directory organisationer. Dessa autentiserings flöden stöds inte för Microsoft-personliga konton.

Du kan också logga in användare genom att använda sociala identiteter som skickar en B2C-myndighet och-princip. Om du vill använda den här metoden kan du endast använda interaktiv autentisering och autentisering med användar namn och lösen ord. Username-lösenordsautentisering stöds för närvarande endast på Xamarin. iOS, Xamarin. Android och UWP.

Mer information finns i [scenarier och stödda autentiserings flöden](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) och [scenarier och plattformar och språk som stöds](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Plattforms konfiguration och omdirigering av URI: er

### <a name="interactive-authentication"></a>Interaktiv autentisering

När du skapar en mobilapp som använder interaktiv autentisering är den mest kritiska registreringen den omdirigerings-URI: n. Du kan ställa in interaktiv autentisering genom [plattforms konfigurationen på bladet **autentisering**](https://aka.ms/MobileAppReg).

Den här funktionen gör att din app kan hämta enkel inloggning (SSO) via Microsoft Authenticator (och Intune-företagsportal på Android). Den kommer också att ha stöd för enhets hanterings principer.

Registrerings portalen för appar ger en förhands granskning som hjälper dig att beräkna den asynkrona svars-URI: n för iOS-och Android-program:

1. I app Registration-portalen väljer   >  **du autentisering testa den nya upplevelsen**.

   ![Bladet autentisering, där du väljer en ny upplevelse](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Välj **Lägg till en plattform**.

   ![Lägg till en plattform](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. När listan över plattformar stöds väljer du **iOS**.

   ![Välj ett mobil program](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Ange ditt paket-ID och välj sedan **Registrera**.

   ![Ange ditt paket-ID](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

När du slutför stegen beräknas omdirigerings-URI: n, som i följande bild.

![Den resulterande omdirigerings-URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Om du föredrar att konfigurera omdirigerings-URI: n manuellt kan du göra det via applikations manifestet. Här är det rekommenderade formatet för manifestet:

- **iOS**: `msauth.<BUNDLE_ID>://auth`
  - Ange till exempel `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Du kan generera Android-signaturens hash med hjälp av versions nyckeln eller fel söknings nyckeln via kommando kommandot.

### <a name="username-password-authentication"></a>Användar namn – lösenordsautentisering

Om din app endast använder username-Password Authentication behöver du inte registrera en omdirigerings-URI för programmet. Det här flödet gör en tur och retur till Microsoft Identity Platform version 2,0-slutpunkten. Programmet kommer inte att anropas igen på någon specifik URI.

Du måste dock identifiera ditt program som ett offentligt klient program. Så här gör du:

1. I <a href="https://portal.azure.com/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>väljer du din app i **Appregistreringar** och väljer sedan **autentisering**.
1. I **Avancerade inställningar**  >  **kan offentliga klient flöden**  >  **Aktivera följande mobila och skriv bords flöden:** Välj **Ja**.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Aktivera offentlig klient inställning i fönstret autentisering i Azure Portal":::

## <a name="api-permissions"></a>API-behörigheter

Mobil program anropar API: er å den inloggade användarens vägnar. Appen måste begära delegerade behörigheter. Dessa behörigheter kallas även omfång. Beroende på vilken upplevelse du vill ha kan du begära delegerade behörigheter statiskt via Azure Portal. Eller så kan du begära dem dynamiskt vid körning.

Genom att registrera behörigheter med statiskt tillstånd kan administratörer enkelt godkänna din app. Statisk registrering rekommenderas.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [app Code Configuration](scenario-mobile-app-configuration.md).
