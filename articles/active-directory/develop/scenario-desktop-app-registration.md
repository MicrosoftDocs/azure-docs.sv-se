---
title: 'Registrera skrivbordsappar som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er (app-registrering)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4031e43b3ec6f6f451fbc4888cc482249042690b
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582731"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Skriv bords app som anropar webb-API: registrera appar

Den här artikeln beskriver app-registreringen av specifika program för ett Skriv bords program.

## <a name="supported-account-types"></a>Kontotyper som stöds

De konto typer som stöds i ett Skriv bords program beror på vilken upplevelse du vill se. På grund av den här relationen är de konto typer som stöds beroende av de flöden som du vill använda.

### <a name="audience-for-interactive-token-acquisition"></a>Mål grupp för hämtning av interaktiva token

Om ditt Skriv bords program använder interaktiv autentisering kan du logga in användare från vilken [Kontotyp](quickstart-register-app.md)som helst.

### <a name="audience-for-desktop-app-silent-flows"></a>Mål grupp för tyst flöden i Desktop-appen

- Om du vill använda integrerad Windows-autentisering eller ett användar namn och ett lösen ord måste ditt program logga in användare i din egen klient, till exempel om du är en affärsutvecklare (LOB). Eller, i Azure Active Directory organisationer, måste ditt program logga in användare i din egen klient om det är ett ISV-scenario. Dessa autentiserings flöden stöds inte för Microsoft-personliga konton.
- Om du loggar in användare med sociala identiteter som skickar en B2C-auktoritet (Business-to-Commerce) och-princip kan du bara använda autentiseringen interaktiv och username-Password.

## <a name="redirect-uris"></a>Omdirigera URI: er

De omdirigerings-URI: er som ska användas i ett Skriv bords program beror på det flöde som du vill använda.

- Om du använder interaktiv autentisering eller enhets kod flöde använder du `https://login.microsoftonline.com/common/oauth2/nativeclient` . Välj motsvarande URL i avsnittet **autentisering** för ditt program för att uppnå den här konfigurationen.

  > [!IMPORTANT]
  > `https://login.microsoftonline.com/common/oauth2/nativeclient`Att använda som omdirigerings-URI rekommenderas som bästa säkerhets praxis.  Om ingen omdirigerings-URI anges används MSAL.NET som `urn:ietf:wg:oauth:2.0:oob` standard som inte är rekommenderade.  Det här standardvärdet kommer att uppdateras som en viktig ändring i nästa större version.

- Om du skapar en ursprunglig mål-C-eller Swift-app för macOS registrerar du omdirigerings-URI: n baserat på programmets paket identifierare i följande format: `msauth.<your.app.bundle.id>://auth` . Ersätt `<your.app.bundle.id>` med programmets paket-ID.
- Om din app endast använder integrerad Windows-autentisering eller ett användar namn och ett lösen ord, behöver du inte registrera en omdirigerings-URI för programmet. Dessa flöden gör en tur och retur till Microsoft Identity Platform v 2.0-slutpunkten. Programmet kommer inte att anropas igen på någon specifik URI.
- För att skilja [enhets kod flödet](scenario-desktop-acquire-token.md#device-code-flow), [integrerad Windows-autentisering](scenario-desktop-acquire-token.md#integrated-windows-authentication)och ett [användar namn och ett lösen ord](scenario-desktop-acquire-token.md#username-and-password) från ett konfidentiellt klient program med hjälp av ett flöde för autentiseringsuppgifter för klient som används i [daemon-program](scenario-daemon-overview.md), ingen som kräver en omdirigerings-URI, konfigurera den som ett offentligt klient program. För att uppnå den här konfigurationen:

    1. I <a href="https://portal.azure.com/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>väljer du din app i **Appregistreringar** och väljer sedan **autentisering**.
    1. I **Avancerade inställningar**  >  **kan offentliga klient flöden**  >  **Aktivera följande mobila och skriv bords flöden:** Välj **Ja**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Aktivera offentlig klient inställning i fönstret autentisering i Azure Portal":::

## <a name="api-permissions"></a>API-behörigheter

Skriv bords program anropar API: er för den inloggade användaren. De måste begära delegerade behörigheter. De kan inte begära program behörigheter, som endast hanteras i [daemon-program](scenario-daemon-overview.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [app Code Configuration](scenario-desktop-app-configuration.md).
