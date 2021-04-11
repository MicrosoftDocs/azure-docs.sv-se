---
title: Användar flöden och anpassade principer i Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Lär dig mer om inbyggda användar flöden och den anpassade principen utöknings Bart princip ramverk för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2e4dbc5178bec3a5b1f0931267465879f604f36f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226016"
---
# <a name="user-flows-and-custom-policies-overview"></a>Översikt över användar flöden och anpassade principer

I Azure AD B2C kan du definiera affärs logiken som användarna följer för att få åtkomst till ditt program. Du kan till exempel bestämma hur många steg användarna ska följa när de loggar in, registrera dig, redigera en profil eller återställa ett lösen ord. När du har slutfört sekvensen erhåller användaren en token och får åtkomst till ditt program. 

I Azure AD B2C finns det två sätt att ge identitets användar upplevelser:

* **Användar flöden** är fördefinierade, inbyggda, konfigurerbara principer som vi tillhandahåller så att du kan skapa registrering, inloggning och princip redigerings upplevelser på några minuter.

* Med **anpassade principer** kan du skapa egna användar resor för komplexa identitets miljö scenarier.

Följande skärm bild visar användar flödets inställningar, jämfört med konfigurationsfiler för anpassade principer.

![Skärm bild som visar användar flödets inställnings gränssnitt, jämfört med konfigurationsfiler för anpassade principer.](media/user-flow-overview/user-flow-vs-custom-policy.png)

Den här artikeln ger en kort översikt över användar flöden och anpassade principer, och hjälper dig att bestämma vilken metod som passar bäst för dina affärs behov.

## <a name="user-flows"></a>Användarflöden

Om du vill ställa in de vanligaste identitets uppgifterna innehåller Azure Portal flera fördefinierade och konfigurerbara principer som kallas *användar flöden*.

Du kan konfigurera inställningar för användar flöden så att de kan styra identitets upplevelsens beteenden i dina program:

* Konto typer som används för inloggning, till exempel sociala konton som en Facebook eller lokala konton som använder en e-postadress och ett lösen ord för inloggning
* Attribut som ska samlas in från konsumenten, till exempel förnamn, post nummer eller land/region för placering
* Azure AD-Multi-Factor Authentication (MFA)
* Anpassning av användar gränssnittet
* Uppsättning anspråk i en token som programmet tar emot när användaren har slutfört användar flödet
* Sessionshantering
* ... och mer

De flesta vanliga identitets scenarier för appar kan definieras och implementeras effektivt med användar flöden. Vi rekommenderar att du använder de inbyggda användar flödena, om du inte har komplexa användar resa som kräver en fullständig flexibilitet i anpassade principer.

## <a name="custom-policies"></a>Anpassade principer

Anpassade principer är konfigurationsfiler som definierar beteendet för Azure AD B2C klientens användar upplevelse. Medan användar flöden är fördefinierade i Azure AD B2C Portal för de vanligaste identitets uppgifterna kan anpassade principer redige ras fullständigt av en identitets utvecklare för att utföra många olika uppgifter.

En anpassad princip är helt konfigurerbar och princip driven. Den dirigerar förtroende mellan entiteter i standard protokoll. Till exempel OpenID Connect, OAuth, SAML och några icke-standardvärden, till exempel REST API-baserade system-till-system-anspråk. Ramverket skapar användarvänliga upplevelser med vita etiketter.

Med den anpassade principen kan du skapa användar resor med valfri kombination av steg. Exempel:

* Federera med andra identitets leverantörer
* Krav för MFA (Multi-Factor Authentication) för första och tredje part
* Samla in indata från användaren
* Integrera med externa system med hjälp av REST API kommunikation

Varje användar resa definieras av en princip. Du kan skapa så många eller så få principer som du behöver för att aktivera den bästa användar upplevelsen för din organisation.

![Diagram som visar ett exempel på en komplex användar resa som har Aktiver ATS av IEF](media/user-flow-overview/custom-policy-diagram.png)

En anpassad princip definieras av flera XML-filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar anspråks schema, anspråks omvandlingar, innehålls definitioner, anspråks leverantörer, tekniska profiler, åtgärder för användar resans dirigering och andra aspekter av identitets upplevelsen.

Den kraftfulla flexibiliteten med anpassade principer är mest lämplig för när du behöver bygga komplexa identitets scenarier. Utvecklare som konfigurerar anpassade principer måste definiera de betrodda relationerna i noggrann detalj för att inkludera slut punkter för metadata, exakta definitioner för anspråk och konfigurera hemligheter, nycklar och certifikat efter behov av varje identitets leverantör.

Lär dig mer om anpassade principer i [anpassade principer i Azure Active Directory B2C](custom-policy-overview.md).

## <a name="comparing-user-flows-and-custom-policies"></a>Jämföra användar flöden och anpassade principer

Följande tabell innehåller en detaljerad jämförelse av de scenarier som du kan använda för att Azure AD B2C användar flöden och en anpassad princip.

| Kontext | Användarflöden | Anpassade principer |
|-|-------------------|-----------------|
| Mål användare | Alla program utvecklare med eller utan identitets kunskaper. | Identitets tekniker, system integrerare, konsulter och interna identitets team. De är bekvämt med OpenID Connect-flöden och förstå identitets leverantörer och anspråksbaserad autentisering. |
| Konfigurations metod | Azure Portal med ett användarvänligt användar gränssnitt (UI). | Redigera XML-filer direkt och ladda upp till Azure Portal. |
| UI-anpassning | [Fullständig anpassning av gränssnitt](customize-ui-with-html.md) , inklusive HTML, CSS och [Java Script](javascript-and-page-layout.md).<br><br>[Stöd för flera språk](language-customization.md) med anpassade strängar. | Samma |
| Attribut anpassning | Standard-och anpassade attribut. | Samma |
| Hantering av token och sessioner | [Anpassa token](configure-tokens.md) och [sessioner](session-behavior.md). | Samma |
| Identitetsprovidrar | [Fördefinierad lokal](identity-provider-local.md) eller [social Provider](add-identity-provider.md), till exempel Federation med Azure Active Directory klienter. | Standardbaserad OIDC, OAUTH och SAML.  Autentisering är också möjligt med hjälp av integrering med REST API: er. |
| Identitets uppgifter | [Registrera dig eller logga](add-sign-up-and-sign-in-policy.md) in med lokala eller många sociala konton.<br><br>[Lösen ords återställning via självbetjäning](add-password-reset-policy.md).<br><br>[Redigera profil](add-profile-editing-policy.md).<br><br>Multi-Factor Authentication.<br><br>Flöden för åtkomsttoken. | Slutför samma uppgifter som användar flöden med anpassade identitets leverantörer eller Använd anpassade omfattningar.<br><br>Etablera ett användar konto i ett annat system vid tidpunkten för registreringen.<br><br>Skicka ett välkomst meddelande med din egen e-posttjänstprovider.<br><br>Använd ett användar Arkiv utanför Azure AD B2C.<br><br>Verifiera användarens angivna information med ett betrott system med hjälp av ett API. |

## <a name="application-integration"></a>Integrering av program

Du kan skapa många användar flöden eller anpassade principer för olika typer av klienter och använda dem i dina program efter behov. Både användar flöden och anpassade principer kan återanvändas i olika program. Den här flexibiliteten gör det möjligt att definiera och ändra identitets upplevelser med minimala eller inga ändringar i koden. 

När en användare vill logga in i ditt program initierar programmet en auktoriseringsbegäran till en användar flöde eller en anpassad princip angiven slut punkt. Användar flödet eller den anpassade principen definierar och styr användarens upplevelse. När de slutför ett användar flöde genererar Azure AD B2C en token och omdirigerar sedan tillbaka användaren till ditt program.

![Mobilapp med pilar som visar flödet mellan Azure AD B2C inloggnings sida](media/user-flow-overview/app-integration.png)

Flera program kan använda samma användar flöde eller anpassad princip. Ett enda program kan använda flera användar flöden eller anpassade principer.

Om du till exempel vill logga in i ett program använder programmet *inloggnings-eller inloggnings* användar flödet. När användaren har loggat in kan de vilja redigera sin profil. Om du vill redigera profilen initierar programmet en annan auktoriseringsbegäran, den här gången använder *profil redigera* användar flöde.

Ditt program utlöser ett användar flöde genom att använda en standard-HTTP-autentiseringsbegäran som innehåller användar flödet eller namnet på den anpassade principen. En anpassad [token](tokens-overview.md) tas emot som ett svar.


## <a name="next-steps"></a>Nästa steg

- Om du vill skapa de rekommenderade användar flödena följer du anvisningarna i [Självstudier: skapa ett användar flöde](tutorial-create-user-flows.md).
- Lär dig mer om [användar flödes versionerna i Azure AD B2C](user-flow-versions.md).
- Läs mer om att [Azure AD B2C anpassade principer](custom-policy-overview.md).
