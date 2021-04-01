---
title: Program typer i v 1.0 | Azure
description: Beskriver de typer av appar och scenarier som stöds av Azure Active Directory v 2.0-slutpunkten.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5ff2858dd8b91ba036c517cbff07be96a729ef8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88116452"
---
# <a name="application-types-in-v10"></a>Program typer i v 1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) stöder autentisering för en mängd moderna app-arkitekturer, som alla baseras på bransch standard protokollen OAuth 2,0 eller OpenID Connect.

Följande diagram illustrerar scenarier och program typer och hur olika komponenter kan läggas till:

![Programtyper och scenarier](./media/authentication-scenarios/application-types-scenarios.png)

Det här är de fem primära program scenarier som stöds av Azure AD:

- **[Spa (Single-Side Application)](single-page-application.md)**: en användare måste logga in på ett program med en enda sida som skyddas av Azure AD.
- **[Webbläsare till webb program](web-app.md)**: en användare måste logga in på ett webb program som skyddas av Azure AD.
- **[Inbyggt program till webb-API](native-app.md)**: ett internt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att få resurser från ett webb-API som skyddas av Azure AD.
- **[Webb program till webb-API](web-api.md)**: ett webb program måste hämta resurser från ett webb-API som skyddas av Azure AD.
- **[Daemon-eller serverprogram till webb-API](service-to-service.md)**: ett daemon-program eller ett serverprogram utan webb användar gränssnitt behöver få resurser från ett webb-API som skyddas av Azure AD.

Följ länkarna för att lära dig mer om varje typ av app och förstå de övergripande scenarierna innan du börjar arbeta med koden. Du kan också lära dig om skillnaderna som du behöver känna till när du skriver en viss app som fungerar med slut punkten eller v 2.0-slutpunkten för v 1.0.

> [!NOTE]
> V 2.0-slutpunkten har inte stöd för alla Azure AD-scenarier och-funktioner. Läs om [v 2.0-begränsningar](./azure-ad-endpoint-comparison.md?bc=%2fazure%2factive-directory%2fazuread-dev%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fazuread-dev%2ftoc.json)för att avgöra om du ska använda v 2.0-slut punkten.

Du kan utveckla alla appar och scenarier som beskrivs här med olika språk och plattformar. De backas upp av fullständiga kod exempel som finns i kod exempel guiden: [v 1.0-kod exempel enligt](sample-v1-code.md) scenariot och [v 2.0 kod exempel](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Du kan också hämta kod exemplen direkt från motsvarande [GitHub-exempel Arkiv](https://github.com/Azure-Samples?q=active-directory).

Om ditt program behöver en viss del av ett scenario från slut punkt till slut punkt, i de flesta fall kan du lägga till funktioner oberoende av varandra. Om du till exempel har ett internt program som anropar ett webb-API kan du enkelt lägga till ett webb program som också anropar webb-API: et.

## <a name="app-registration"></a>Appregistrering

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registrera en app som använder Azure AD v 1.0-slutpunkten

Alla program som utvärderar autentisering till Azure AD måste vara registrerade i en katalog. Det här steget omfattar att berätta för Azure AD om ditt program, inklusive URL: en där den finns, URL: en för att skicka svar efter autentisering, URI: n för att identifiera ditt program och mycket mer. Den här informationen krävs för några viktiga orsaker:

* Azure AD måste kommunicera med programmet vid hantering av inloggnings-eller utbytes-token. Informationen som skickas mellan Azure AD och programmet innehåller följande:
  
  * **Program-ID-URI** -identifieraren för ett program. Det här värdet skickas till Azure AD under autentiseringen för att ange vilket program som anroparen vill ha en token för. Dessutom ingår det här värdet i token så att programmet vet att det är det avsedda målet.
  * **Svars-URL** och **omdirigerings-URI** – för ett webb-API eller webb program är SVARs-URL: en den plats där Azure AD kommer att skicka autentiserings svaret, inklusive en token om autentiseringen lyckades. För ett internt program är omdirigerings-URI: n en unik identifierare som Azure AD ska omdirigera användar agenten till i en OAuth 2,0-begäran.
  * **Program-ID** : ID för ett program, som genereras av Azure AD när programmet registreras. När du begär en auktoriseringskod eller token skickas programmets ID och nyckel till Azure AD under autentiseringen.
  * **Nyckel** – nyckeln som skickas tillsammans med ett program-ID när du autentiserar till Azure AD för att anropa ett webb-API.
* Azure AD måste se till att programmet har de behörigheter som krävs för att komma åt dina katalog data, andra program i din organisation och så vidare.

Mer information finns i så här [registrerar du en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="single-tenant-and-multi-tenant-apps"></a>Appar för en enda klient och flera innehavare

Etableringen blir tydligare när du förstår att det finns två kategorier av program som kan utvecklas och integreras med Azure AD:

* **Enda klient program** – ett enda klient program är avsett att användas i en organisation. Detta är vanligt vis branschspecifika program (LoB) som skrivits av en företags utvecklare. Ett enda klient program behöver bara användas av användare i en katalog och därför behöver det bara tillhandahållas i en katalog. De här programmen registreras vanligt vis av en utvecklare i organisationen.
* **Program för flera innehavare** – ett program med flera innehavare är avsett att användas i många organisationer, inte bara en organisation. Det kallas vanligen för SaaS-webbprogram (Software as a Service) och skrivs av en oberoende programvaruleverantör (ISV). Program med flera klienter måste tillhandahållas i varje katalog där de ska användas, vilket kräver att användaren eller administratören registrerar sig för att registrera dem. Medgivandeprocessen startar när ett program har registrerats i katalogen och ges åtkomst till Graph API eller kanske någon annan webb-API. När en användare eller administratör från en annan organisation registrerar sig för att använda programmet visas en dialog ruta som visar de behörigheter som krävs för programmet. Användaren eller administratören kan sedan godkänna programmet, vilket ger programmet åtkomst till angivna data, och registrerar slutligen programmet i sin katalog. Mer information finns i [Översikt över medgivande ramverket](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Ytterligare överväganden när du utvecklar en enskild klient eller appar för flera klient organisationer

Vissa ytterligare överväganden uppstår när du utvecklar ett program med flera klienter i stället för ett enda klient program. Om du till exempel gör ditt program tillgängligt för användare i flera kataloger, behöver du en mekanism för att avgöra vilken klient de befinner sig i. Ett enda klient program behöver bara titta i sin egen katalog för en användare, medan ett program med flera innehavare måste identifiera en viss användare från alla kataloger i Azure AD. För att utföra den här uppgiften tillhandahåller Azure AD en gemensam autentiserings-slutpunkt där ett program med flera klienter kan dirigera inloggnings begär Anden, i stället för en klient specifik slut punkt. Den här slut punkten är `https://login.microsoftonline.com/common` för alla kataloger i Azure AD, medan en klient särskild slut punkt kan vara `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Den vanliga slut punkten är särskilt viktig att tänka på när du utvecklar ditt program eftersom du behöver den nödvändiga logiken för att hantera flera klienter vid inloggning, utloggning och verifiering av token.

Om du för närvarande utvecklar ett enda klient program, men vill göra det tillgängligt för många organisationer, kan du enkelt göra ändringar i programmet och dess konfiguration i Azure AD så att det kan användas med flera innehavare. Dessutom använder Azure AD samma signerings nyckel för alla tokens i alla kataloger, oavsett om du tillhandahåller autentisering i en enda klient eller ett program för flera innehavare.

Varje scenario som anges i det här dokumentet innehåller ett underavsnitt som beskriver dess etablerings krav. Mer detaljerad information om hur du konfigurerar ett program i Azure AD och skillnaderna mellan en och flera klient program finns i [integrera program med Azure Active Directory](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för mer information. Fortsätt att läsa för att förstå vanliga program scenarier i Azure AD.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om grunderna i Azure AD- [autentisering](v1-authentication-scenarios.md)