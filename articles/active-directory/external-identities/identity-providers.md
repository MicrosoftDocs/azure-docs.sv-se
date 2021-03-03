---
title: Identitets leverantörer för externa identiteter – Azure AD
description: Lär dig hur du använder Azure AD som standard identitets leverantör för delning med externa användare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdef929b27c636b3908dd7a88eb93adc2382a53f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687755"
---
# <a name="identity-providers-for-external-identities"></a>Identitets leverantörer för externa identiteter

> [!NOTE]
> Några av de funktioner som nämns i den här artikeln är offentliga för hands versions funktioner i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

En *identitetsprovider* skapar, underhåller och hanterar identitetsinformation samt autentiseringstjänster till program. När du delar dina appar och resurser med externa användare är Azure AD standard identitets leverantören för delning. Det innebär att när du bjuder in externa användare som redan har en Azure AD eller Microsoft-konto, kan de automatiskt logga in utan ytterligare konfiguration på din sida.

Förutom Azure AD-konton erbjuder externa identiteter olika identitets leverantörer.

- **Microsoft-konton** (för hands version): gäst användare kan använda sina egna personliga Microsoft-konto (MSA) för att lösa in dina inbjudningar för B2B-samarbete. När du konfigurerar ett användar flöde för självbetjänings registrering kan du lägga till ett [Microsoft-konto (för hands version)](microsoft-account.md) som en av de tillåtna identitets leverantörerna. Ingen ytterligare konfiguration krävs för att göra den här identitets leverantören tillgänglig för användar flöden.

- **E-post med eng ång slö sen ord** (för hands version): när du löser in en inbjudan eller använder en delad resurs kan en gäst användare begära en tillfällig kod som skickas till sin e-postadress. Sedan anger de den här koden för att fortsätta logga in. Funktionen för eng ång slö sen ord autentiserar B2B-gäst användare när de inte kan autentiseras på annat sätt. När du konfigurerar ett användar flöde för självbetjänings registrering kan du lägga till **e-One-Time lösen ord (för hands version)** som en av de tillåtna identitets leverantörerna. Vissa inställningar krävs; Se [e-postautentisering med eng ång slö sen ord](one-time-passcode.md).

- **Google**: Google Federation låter externa användare lösa in inbjudningar från dig genom att logga in på dina appar med egna Gmail-konton. Google Federation kan också användas i dina självbetjänings registrerings användar flöden. Se hur du [lägger till Google som en identitets leverantör](google-federation.md).
   > [!IMPORTANT]
   > Från och med den **4 januari 2021** är Google [inaktuell WebView-inloggning support](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Om du använder Google Federation eller självbetjänings registrering med Gmail bör du [testa dina verksamhetsbaserade interna program för kompatibilitet](google-federation.md#deprecation-of-webview-sign-in-support).

- **Facebook**: när du skapar en app kan du konfigurera registrering av självbetjäning och aktivera Facebook-Federation så att användarna kan registrera sig för din app med sina egna Facebook-konton. Facebook kan bara användas för självbetjänings registrering av användar flöden och är inte tillgängligt som ett inloggnings alternativ när användarna löser in inbjudningar från dig. Se hur du [lägger till Facebook som en identitets leverantör](facebook-federation.md).

- **Direkt Federation**: du kan också konfigurera direkt Federation med en extern identitetsprovider som stöder SAML-eller WS-Fed protokoll. Med direkt Federation kan externa användare lösa in inbjudningar från dig genom att logga in på dina appar med sina befintliga sociala konton eller företags konton. Se [Konfigurera direkt Federation](direct-federation.md).
   > [!NOTE]
   > Direkta Federations identitets leverantörer kan inte användas i dina självbetjänings registrerings användar flöden.

## <a name="adding-social-identity-providers"></a>Lägga till sociala identitets leverantörer

Azure AD är aktiverat som standard för registrering via självbetjäning så att användarna alltid har möjlighet att registrera sig med ett Azure AD-konto. Du kan dock aktivera andra identitets leverantörer, inklusive sociala identitets leverantörer som Google eller Facebook. Om du vill konfigurera sociala identitets leverantörer i din Azure AD-klient skapar du ett program hos identitets leverantören och konfigurerar autentiseringsuppgifter. Du får ett klient-eller app-ID och en klient-eller app-hemlighet, som du sedan kan lägga till i din Azure AD-klient.

När du har lagt till en identitetsprovider till din Azure AD-klient:

- När du bjuder in en extern användare till appar eller resurser i organisationen kan den externa användaren logga in med sitt eget konto hos den identitets leverantören.
- När du aktiverar [självbetjänings registrering](self-service-sign-up-overview.md) för dina appar kan externa användare registrera sig för dina appar med hjälp av sina egna konton med de identitets leverantörer som du har lagt till. De kan välja mellan de alternativ för sociala identitets leverantörer som du har gjort tillgängliga på registrerings sidan:

   ![Skärm bild som visar inloggnings skärmen med Google-och Facebook-alternativ](media/identity-providers/sign-in-with-social-identity.png)

För en optimal inloggnings upplevelse kan du federera med identitets leverantörer närhelst det är möjligt, så att du kan ge dina inbjudna gäster en sömlös inloggnings upplevelse när de kommer åt dina appar.  

## <a name="next-steps"></a>Nästa steg

Information om hur du lägger till identitets leverantörer för inloggning till dina program finns i följande artiklar:

- [Lägg till e-post för autentisering med eng ång slö sen ord](one-time-passcode.md)
- [Lägg till Google](google-federation.md) som en tillåten leverantör av sociala identiteter
- [Lägg till Facebook](facebook-federation.md) som en tillåten Provider för sociala identiteter
- [Konfigurera direkt Federation](direct-federation.md) med en organisation vars IDENTITETSPROVIDER stöder SAML 2,0 eller WS-Fed-protokollet. Observera att direkt Federation inte är ett alternativ för självbetjänings registrering av användar flöden.
