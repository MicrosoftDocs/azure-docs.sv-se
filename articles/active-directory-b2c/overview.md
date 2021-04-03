---
title: Vad är Azure Active Directory B2C?
description: Lär dig hur du kan använda Azure Active Directory B2C för att stödja externa identiteter i dina program, inklusive sociala inloggningar med Facebook, Google och andra identitets leverantörer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29b34041042f00d72b8a9fe86e31bd80f8c77dc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953958"
---
# <a name="what-is-azure-active-directory-b2c"></a>Vad är Azure Active Directory B2C?

Azure Active Directory B2C ger affärs-till-kund-identitet som en tjänst. Dina kunder använder sina prioriterade sociala konton, företags-eller lokala konto identiteter för att få åtkomst till enkel inloggning till dina program och API: er.

![Informations grafiken av Azure AD B2C identitets leverantörer och underordnade program](./media/overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C) är en CIAM-lösning (Customer Identity Access Management) som har stöd för miljontals användare och miljarder autentiseringar per dag. Det tar hand om skalning och säkerhet för Autentiseringstjänsten, övervakning och hantering av hot som denial-of-service, lösen ords spray eller brute force-attacker.

## <a name="custom-branded-identity-solution"></a>Lösning för anpassad identitet

Azure AD B2C är en autentiseringsmetod med vita etiketter. Du kan anpassa hela användar upplevelsen med ditt varumärke så att den blandar sömlöst med dina webb-och mobil program.

Anpassa varje sida som visas av Azure AD B2C när användarna registrerar sig, loggar in och ändrar deras profil information. Anpassa HTML, CSS och Java Script i din användar resa så att Azure AD B2C-upplevelsen ser ut och ser ut som om den är en inbyggd del av ditt program.

![Anpassade registrerings-och inloggnings sidor och bakgrunds bild](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Åtkomst med enkel inloggning med en användardefinierad identitet

Azure AD B2C använder standardbaserad autentiseringsprotokoll, inklusive OpenID Connect, OAuth 2,0 och SAML. Den integreras med de flesta moderna program och kommersiella program som inte är i hylla.

![Diagram över identiteter från tredje part som federerar till Azure AD B2C](./media/overview/scenario-singlesignon.png)

Genom att betjäna den centrala autentiseringsmetoden för dina webb program, mobilappar och API: er, kan du med Azure AD B2C bygga en enkel inloggnings lösning (SSO) för alla. Centralisera insamlingen av användar profils-och inställnings information och samla in detaljerad analys om inloggnings beteende och registrerings omvandling.

## <a name="integrate-with-external-user-stores"></a>Integrera med externa användar lager

Azure AD B2C tillhandahåller en katalog som kan innehålla 100 anpassade attribut per användare. Du kan dock även integrera med externa system. Du kan till exempel använda Azure AD B2C för autentisering, men delegera till en extern kund Relations hantering (CRM) eller kund förmåns databas som källa till sanningen för kund information.

Ett annat externt användar lagrings scenario är att Azure AD B2C hantera autentiseringen för ditt program, men integrera med ett externt system som lagrar användar profil eller personliga data. Till exempel för att uppfylla data placering krav som regionala eller lokala data lagrings principer.

![Ett logiskt diagram för Azure AD B2C som kommunicerar med ett externt användar Arkiv](./media/overview/scenario-remoteprofile.png)

Azure AD B2C kan under lätta insamlingen av informationen från användaren under registrering eller profil redigering, och sedan lämna ut data till det externa systemet. Under framtida autentiseringar kan Azure AD B2C hämta data från det externa systemet och, om det behövs, inkludera det som en del av svaret på autentiseringstoken som skickas till ditt program.

## <a name="progressive-profiling"></a>Progressiv profilering

Ett annat alternativ för användar resa innehåller progressiv profilering. Med progressiv profilering kan dina kunder snabbt slutföra sin första transaktion genom att samla in en minimal mängd information. Sedan kan du gradvis samla in mer profil data från kunden i kommande inloggningar.

![En visuell visning av progressiv profilering](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Identitets verifiering från tredje part och språk kontroll

Använd Azure AD B2C för att under lätta identitets verifiering och-korrektur genom att samla in användar data och sedan skicka det till ett tredje parts system för att utföra verifiering, förtroende bedömning och godkännande för skapande av användar konto.

![Ett diagram som visar användar flödet för identitets bevis från tredje part](./media/overview/scenario-idproofing.png)

Dessa är bara några av de saker som du kan göra med Azure AD B2C som din affärs-till-kund-identitets plattform. Följande avsnitt i den här översikten vägleder dig genom ett demo program som använder Azure AD B2C. Du är också välkommen att gå direkt till en mer djupgående [teknisk översikt över Azure AD B2C](technical-overview.md).

## <a name="example-woodgrove-groceries"></a>Exempel: Sparbankens livsmedels varor

[Sparbanken][woodgrove] är ett Live-webbprogram som skapats av Microsoft för att demonstrera flera Azure AD B2C-funktioner. I följande avsnitt granskas några av de autentiseringsalternativ som tillhandahålls av Azure AD B2C på Sparbankens webbplats.

### <a name="business-overview"></a>Företags översikt

Sparbanken är en inköps lager online som säljer varor till både enskilda konsumenter och företags kunder. Deras företags kunder köper mat varor på uppdrag av företaget eller företag som de hanterar.

### <a name="sign-in-options"></a>Inloggningsalternativ

Sparbankens sparbanker erbjuder flera inloggnings alternativ baserat på relationen deras kunder har med butiken:

* **Enskilda** kunder kan registrera sig eller logga in med enskilda konton, till exempel med en social identitetsprovider eller en e-postadress och ett lösen ord.
* **Företags** kunder kan registrera sig och logga in med sina företags uppgifter.
* **Partner** och leverantörer är personer som tillhandahåller en livsmedels lagrings plats med produkter att sälja. Partner identitet tillhandahålls av [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).

![Enskilda (B2C), Business (B2C) och partner (B2B) inloggnings sidor](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Autentisera enskilda kunder

När en kund väljer **Logga in med ditt personliga konto** omdirigeras de till en anpassad inloggnings sida med Azure AD B2C. Du kan se följande bild som vi har anpassat användar gränssnittet (UI) för att se och känna sig precis som webbplatsen för Sparbanken. Sparbankens kunder bör vara medvetna om att autentiseringen är värdbaserad och skyddas av Azure AD B2C.

![Anpassad inloggnings sida för Sparbanken som finns Azure AD B2C](./media/overview/sign-in.png)

Med Sparbanken kan kunderna registrera sig och logga in med sina Google-, Facebook-eller Microsoft-konton som identitets leverantör. Eller så kan de registrera sig genom att använda sin e-postadress och ett lösen ord för att skapa vad som kallas *lokalt konto*.

När en kund väljer **att registrera sig med ditt personliga konto** och sedan **Registrera dig nu** visas en anpassad registrerings sida.

![Anpassad inloggnings sida för Sparbanken som finns Azure AD B2C](./media/overview/sign-up.png)

När du har angett en e-postadress och valt **Skicka verifierings kod** skickar Azure AD B2C koden. När de har angett sin kod väljer du **verifiera kod** och anger sedan den andra informationen i formuläret. de måste också godkänna villkoren i tjänsten.

Genom att klicka på knappen **skapa** kan Azure AD B2C omdirigera användaren tillbaka till webbplatsen Sparbanken. När den omdirigeras skickar Azure AD B2C en OpenID Connect-autentiseringstoken till Sparbank-webbappen. Användaren är nu inloggad och redo att gå och visnings namnet visas i det övre högra hörnet för att indikera att de är inloggade.

![Webbplats rubrik för sparbanker som visar att användaren är inloggad](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Autentisera företags kunder

När en kund väljer något av alternativen under **företags kunder**, anropar webbplatsen Sparbanken en annan Azure AD B2C princip än för enskilda kunder.

Den här principen visar användaren med ett alternativ för att använda sina företags uppgifter för registrering och inloggning. I exempel på Sparbanken uppmanas användarna att logga in med ett arbets-eller skol konto. Den här principen använder ett [Azure AD-program med flera innehavare](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) och `/common` Azure AD-slutpunkten för att federera Azure AD B2C med valfri Microsoft 365 kund i världen.

### <a name="authenticate-partners"></a>Autentisera partners

Länken **Logga in med ditt leverantörs konto** använder Azure Active Directory B2B's samarbets funktioner. Azure AD B2B är en serie funktioner i Azure Active Directory för att hantera partner identiteter. Dessa identiteter kan vara federerade från Azure Active Directory för åtkomst till Azure AD B2C-skyddade program.

Lär dig mer om Azure AD B2B i [Vad är gäst användar åtkomst i Azure Active Directory B2B?](../active-directory/external-identities/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Nästa steg

Nu när du har en uppfattning om vad Azure AD B2C är och några av de scenarier som de kan hjälpa dig med, kan du gräva lite djupare i dess funktioner och tekniska aspekter.

> [!div class="nextstepaction"]
> [Azure AD B2C teknisk översikt >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo