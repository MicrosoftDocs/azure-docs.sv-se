---
title: Publicera din app i Azure AD App-galleriet
description: Lär dig hur du visar ett program som stöder enkel inloggning i Azure Active Directory app-galleriet.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 3d810d14dd6b49bc054e3844a60ec33c62dc084c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271251"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Publicera din app i Azure AD App-galleriet

Du kan publicera din app i Azure AD App-galleriet. När appen publiceras visas den som ett alternativ för kunder när de lägger till appar till klienten. 

Några av fördelarna med att lägga till din app i Azure AD-galleriet är:

- Kunderna hittar den bästa möjliga inloggnings upplevelsen för din app.
- Programmets konfiguration är enkel och minimal.
- En snabb sökning hittar ditt program i galleriet.
- Kostnads fria, grundläggande och Premium Azure AD-kunder kan använda denna integrering.
- Ömsesidiga kunder får en steg-för-steg-konfigurations guide.

Dessutom finns det många fördelar när dina kunder använder Azure AD som identitets leverantör för din app. Några av dessa är:

- Tillhandahålla enkel inloggning för dina användare. Med enkel inloggning minskar du support kostnaderna genom att göra det enklare för dina kunder med enkel inloggning. Om enkel inloggning är aktive rad behöver kundernas IT-administratörer inte lära sig hur man konfigurerar programmet för användning i organisationen. Mer information om enkel inloggning finns i [Vad är enkel inloggning?](../manage-apps/what-is-single-sign-on.md).
- Din app kan vara synlig i Microsoft 365 App-galleriet, Microsoft 365 App-start och inom Microsoft Search på Office.com. 
- Integrerad hantering av appar. Mer information om hantering av appar i Azure AD finns i [Vad är program hantering?](../manage-apps/what-is-application-management.md).
- Din app kan använda [Graph API](https://docs.microsoft.com/graph/) för att komma åt data som driver användar produktivitet i Microsofts eko system.
- Programspecifik dokumentation som skapas tillsammans med Azure AD-teamet för våra ömsesidiga kunder underlättar implementeringen.
- Du ger kunderna möjlighet att fullständigt hantera sin autentisering och auktorisering av personal identiteter.
- Placera alla konto hanterings-och efterlevnads ansvar med kundens ägare av dessa identiteter.
- Tillhandahålla möjlighet att aktivera eller inaktivera SSO för specifika identitets leverantörer, grupper eller användare för att uppfylla företagets behov.
- Du ökar din marknadsmässighet och dina antagande. Många stora organisationer kräver att (eller strävar efter till) sina anställda har sömlös SSO-upplevelse i alla program. Enkel inloggning är viktigt.
- Du minskar friktionen för slutanvändare, vilket kan öka slut användar användningen och öka intäkterna.
- Kunder som använder systemet för[scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(Cross-Domain Identity Management) kan använda etablering för samma app.
- Lägg till säkerhet och bekvämlighet när användare loggar in på program med hjälp av Azure AD SSO och tar bort behovet av separata autentiseringsuppgifter.

> [!TIP]
> När du erbjuder ditt program för användning av andra företag via ett köp eller en prenumeration gör du ditt program tillgängligt för kunder i sina egna Azure-klienter. Detta kallas att skapa ett program med flera innehavare. En översikt över det här konceptet finns i [program med flera klient organisationer i Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) och [innehav i Azure Active Directory](single-and-multi-tenant-apps.md).

> [!IMPORTANT]
> Om du vill publicera din app i Azure AD-galleriet måste du godkänna särskilda villkor. Innan du börjar, se till att läsa och godkänna de [allmänna](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)villkoren.

Stegen för att publicera din app i Azure AD App-galleriet är:
1. Välj den rätta enkla inloggnings standarden för din app.
2. Implementera enkel inloggning i din app.
3. Skapa din Azure-klient och testa din app.
4. Skapa och publicera dokumentation.
5. Skicka in din app.
6. Delta i Microsoft Partner Network.


## <a name="prerequisites"></a>Krav

Du behöver ett permanent konto för testning med minst två användare registrerade.


## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>Steg 1 – Välj rätt enkel inloggnings standard för appen

Om du vill visa ett program i Azure AD App-galleriet måste du implementera minst ett av de alternativ för enkel inloggning som stöds. Om du vill förstå alternativen för enkel inloggning och hur kunderna ska konfigurera dem i Azure AD, se [SSO-alternativ](../manage-apps/sso-options.md).

I följande tabell jämförs huvud standarder: Open Authentication 2,0 (OAuth 2,0) med OpenID Connect (OIDC), Security Assertion Markup Language (SAML) och Web Services Federation (WS-utfodras).

| Kapacitet| OAuth/OIDC| SAML/WS-utfodras |
| - |-|-|
| Webbaserad enkel inloggning| √| √ |
| Webbaserad enkel utloggning| √| √ |
| Mobil-baserad enkel inloggning| √| √* |
| Mobil-baserad enkel utloggning| √| √* |
| Principer för villkorlig åtkomst för mobila program| √| X |
| Sömlös MFA-upplevelse för mobila program| √| X |
| Åtkomst Microsoft Graph| √| X |

* Möjligt, men Microsoft tillhandahåller inte exempel eller rikt linjer.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 och OpenID Connect
OAuth 2,0 är ett [bransch standard](https://oauth.net/2/) protokoll för auktorisering. OpenID Connect (OIDC) är ett huvud för identitets lager för [bransch standard](https://openid.net/connect/) som byggts ovanpå OAuth 2,0-protokollet. 

**Orsaker till att välja OAuth/OIDC**
- Auktoriseringen i dessa protokoll gör att ditt program kan komma åt och integrera med omfattande användar-och organisations data via Microsoft Graph API.
- Fören klar användarnas upplevelse när de använder SSO för ditt program. Du kan enkelt definiera de behörighets uppsättningar som krävs, som sedan visas automatiskt för administratören eller slutanvändaren som godkänner.
- Genom att använda dessa protokoll kan dina kunder använda principer för villkorlig åtkomst och Multi-Factor Authentication (MFA) för att kontrol lera åtkomsten till programmen. 
- Microsoft tillhandahåller bibliotek och [kod exempel över flera teknik plattformar](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) för att hjälpa dig att utveckla.  

**Några saker att tänka på**
- Om du redan har implementerat SAML-baserad enkel inloggning för ditt program kanske du inte vill implementera en ny standard för att hämta din app i galleriet.

### <a name="saml-20-or-ws-fed"></a>SAML 2,0 eller WS-utfodras

SAML är en vuxen, och ofta förekommande, enkel inloggnings standard för webb program. Mer information om hur Azure använder SAML finns i [hur Azure använder SAML-protokollet](active-directory-saml-protocol-reference.md). 

Web Services Federation (WS-utfodras) är en [bransch standard](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) som används vanligt vis för webb program som utvecklas med hjälp av .NET-plattformen.

**Orsaker till att välja SAML**
- SAML 2,0 är en vuxen standard och de flesta teknik plattformarna stöder bibliotek med öppen källkod för SAML 2,0. 
- Du kan ge dina kunder ett administrations gränssnitt för att konfigurera SAML SSO. De kan konfigurera SAML SSO för Microsoft Azure AD och andra identitets leverantörer som stöder SAML.

**Några saker att tänka på**
- När du använder SAML 2,0-eller WSFed-protokoll för mobila program, kommer vissa principer för villkorlig åtkomst inklusive Multi-Factor Authentication (MFA) att ha en försämrad upplevelse.
- Om du vill ha åtkomst till Microsoft Graph måste du implementera auktorisering via OAuth 2,0 för att generera nödvändiga tokens. 

### <a name="password-based"></a>Lösenordsbaserade
Lösenordsbaserad SSO, som även kallas lösen ords valv, gör att du kan hantera användar åtkomst och lösen ord för webb program som inte stöder identitets Federation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, t. ex. till din organisations appar för sociala media.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>Steg 2 – implementera enkel inloggning i din app
Varje app i galleriet måste implementera ett av de alternativ för enkel inloggning som stöds. Mer information om vilka alternativ som stöds finns i [SSO-alternativ](../manage-apps/sso-options.md).

För OAuth och OIDC, se [rikt linjer för autentiserings mönster](v2-app-types.md) och [kod exempel för Azure Active Directory](sample-v2-code.md).

För SAML och WS-utfodras måste ditt program ha möjlighet att integrera SSO-integration i SP-eller IDP-läge. Kontrol lera att den här funktionen fungerar korrekt innan du skickar in begäran.

Mer information om autentisering finns i [Vad är autentisering?](../azuread-dev/v1-authentication-scenarios.md).

> [!IMPORTANT]
> För federerade program (OpenID och SAML/WS-utfodras) måste appen ha stöd för SaaS-modellen (Software as a Service). Azure AD Gallery-program måste ha stöd för flera kundkonfigurationer och bör inte vara specifika för någon enskild kund.

### <a name="implement-oauth-20-and-openid-connect"></a>Implementera OAuth 2,0 och OpenID Connect

För att OpenID ska kunna ansluta måste programmet ha flera innehavare och [Azure AD medgivande Framework](consent-framework.md) måste implementeras korrekt för programmet. Användaren kan skicka inloggnings förfrågan till en gemensam slut punkt så att alla kunder kan ge sitt medgivande till programmet. Du kan styra användar åtkomst baserat på klient-ID: t och användarens UPN som togs emot i token.

Information om hur du granskar vissa exempel finns i [kod exempel för Microsoft Identity Platform](sample-v2-code.md). 

Information om hur du granskar Mobile Specific-exempel finns i: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Universell Windows-plattform](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>Implementera SAML 2,0

Om din app stöder SAML 2,0 kan du integrera den direkt med en Azure AD-klient. Mer information om SAML-konfiguration med Azure AD finns i [Konfigurera SAML-baserad enkel inloggning](../manage-apps/configure-saml-single-sign-on.md).

Microsoft tillhandahåller inte eller rekommenderar bibliotek för SAML-implementeringar. Det finns många tillgängliga bibliotek med öppen källkod.

### <a name="implement-ws-fed"></a>Implementera WS-utfodras
Mer information om WS-utfodras i ASP.NET Core finns i [autentisera användare med WS-Federation i ASP.net Core](https://docs.microsoft.com/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>Implementera lösen ords valv

Skapa ett webb program som har en HTML-inloggnings sida. Kontrol lera att ditt program stöder formulärautentisering så att lösen ords valvet kan göras för att få enkel inloggning att fungera som förväntat.


## <a name="step-3---create-your-azure-tenant-and-test-your-app"></a>Steg 3 – skapa din Azure-klient och testa din app

Du behöver en Azure AD-klient för att testa appen. Information om hur du konfigurerar din utvecklings miljö finns i [snabb start: Konfigurera en klient](quickstart-create-new-tenant.md).

En Azure AD-klient levereras med varje Microsoft 365 prenumeration. Information om hur du konfigurerar en kostnads fri Microsoft 365 utvecklings miljö finns i [delta i Microsoft 365 Developer-programmet](https://docs.microsoft.com/office/developer-program/microsoft-365-developer-program).

När du har en klient måste du aktivera och testa åtkomst med enkel inloggning. 

[Registrera ditt program](quickstart-register-app.md) som ett program med flera innehavare **för OIDC-eller Oath-program**. Välj kontona i valfri organisations katalog och alternativet personliga Microsoft-konton i de konto typer som stöds.

**För SAML-och WS-utfodras-baserade program**konfigurerar du [SAML-baserade enkla inloggnings](../manage-apps/configure-saml-single-sign-on.md) program med hjälp av en allmän SAML-mall i Azure AD.

Du kan också [konvertera ett program med en enda klient till flera klienter](howto-convert-app-to-be-multi-tenant.md) om det behövs.


## <a name="step-4---create-and-publish-documentation"></a>Steg 4 – Skapa och publicera dokumentation

### <a name="documentation-on-your-site"></a>Dokumentation på din webbplats

Ett enkelt sätt att införa är en betydande faktor i företags program varu beslut. Rensa användarvänlig dokumentation som stöder dina kunder vid deras införande av transporter och minskar support kostnaderna. Genom att arbeta med tusentals program varu leverantörer har Microsoft sett vad som fungerar.

Vi rekommenderar att dokumentationen på din webbplats minst omfattar följande objekt.

* Introduktion till dina SSO-funktioner
  * Protokoll som stöds
  * Version och SKU
  * Lista med identitets leverantörer som stöds med dokumentations länkar
* Licens information för ditt program
* Rollbaserad åtkomst kontroll för att konfigurera SSO
* Konfigurations steg för SSO
  * GRÄNSSNITTs konfigurations element för SAML med förväntade värden från providern
  * Information om service providern som ska skickas till identitets leverantörer
* Om OIDC/OAuth
  * Lista över behörigheter som krävs för godkännande med affärs skäl
* Test steg för pilot användare
* Fel söknings information, inklusive felkoder och meddelanden
* Support metoder för kunder

### <a name="documentation-on-the-microsoft-site"></a>Dokumentation på Microsofts webbplats

När du visar ditt program med Azure Active Directory program galleriet, som även publicerar ditt program på Azure Marketplace, kommer Microsoft att generera dokumentation för våra ömsesidiga kunder som förklarar steg för steg-processen. Du kan se ett exempel [här](https://aka.ms/appstutorial). Den här dokumentationen skapas baserat på ditt bidrag till galleriet och du kan enkelt uppdatera det om du gör ändringar i programmet med ditt GitHub-konto.


## <a name="step-5---submit-your-app"></a>Steg 5 – skicka in din app

När du har testat att program integrationen fungerar med Azure AD skickar du din programbegäran i [Microsoft-programmets nätverks Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Första gången du försöker logga in på portalen visas en av två skärmar. 

Om du får meddelandet "som inte fungerade" måste du kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Ange det e-postkonto som du vill använda för att skicka begäran. En e-postadress till företaget, till exempel `name@yourbusiness.com` föredra. Azure AD-teamet kommer att lägga till kontot i Microsoft-programmets nätverks Portal.

Om du ser sidan "åtkomstbegäran" fyller du i affärs justeringen och väljer **begär åtkomst**.

När kontot har lagts till kan du logga in på Microsoft-programmets nätverks Portal och skicka begäran genom att välja panelen för att **skicka förfrågningar (ISV)** på Start sidan.

![Sändnings panelen för begäran (ISV) på Start Sidan](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Problem med att logga in på portalen

Om du ser det här felet när du loggar in, är det Detaljer om problemet och hur du kan åtgärda det.

* Om inloggningen blockerades enligt nedan:

  ![problem med att lösa program i galleriet](./media/howto-app-gallery-listing/blocked.png)

**Vad händer:**

Gäst användaren är federerad till en hem klient, som också är en Azure AD. Gäst användaren har hög risk. Microsoft tillåter inte att användare med hög risk får åtkomst till sina resurser. Alla användare med hög risk (anställda eller gäster/leverantörer) måste åtgärda/stänga sin risk för att få åtkomst till Microsoft-resurser. För gäst användare kommer den här användar risken från hem klienten och principen kommer från resurs klienten (Microsoft i detta fall).
 
**Säkra lösningar:**

* MFA-registrerade gäst användare reparerar sina egna användar risker. Detta kan göras av gäst användaren som utför en säker ändring eller återställning av lösen ord ( https://aka.ms/sspr) på hem klient organisationen (Detta kräver MFA och SSPR på hem klienten). Den skyddade lösen ords ändringen eller återställningen måste initieras på Azure AD och inte på lokal.

* Gäst användare får sina administratörer att reparera sina risker. I det här fallet utför administratören en lösen ords återställning (tillfälliga lösen ords generering). Detta kräver inte identitets skydd. Gäst användarens administratör kan gå till https://aka.ms/RiskyUsers och klicka på Återställ lösen ord.

* Gäst användare får sina administratörer att stänga/stänga av sin risk. Detta kräver inte identitets skydd. Administratören kan gå till https://aka.ms/RiskyUsers och klicka på "ignorera användar risk". Administratören måste dock göra en noggrannhet för att säkerställa att detta var en falsk positiv riskbedömning innan användar risken stängs. Annars sätter de ut sina och Microsofts resurser i fara genom att undertrycka en riskbedömning utan undersökning.

> [!NOTE]
> Om du har problem med åtkomst kan du kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="implementation-specific-options"></a>Implementering av vissa alternativ
Om du vill lägga till programmet i listan i galleriet med hjälp av OpenID Connect väljer du **OpenID connect & OAuth 2,0** som det visas.

![Visa ett OpenID Connect-program i galleriet](./media/howto-app-gallery-listing/openid.png)

Om du vill lägga till ditt program i listan i galleriet med **saml 2,0** eller **WS-utfodras**väljer du **SAML 2.0/WS-utfodras** som visas.

![Visar ett SAML 2,0-eller WS-utfodras program i galleriet](./media/howto-app-gallery-listing/saml.png)

Om du vill lägga till ditt program i listan i galleriet med hjälp av lösen ord för enkel inloggning väljer du **lösen ord för inloggning** som visas.

![Visar en lista med ett SSO-program i galleriet](./media/howto-app-gallery-listing/passwordsso.png)

Om du implementerar en SCIM 2,0-slutpunkt för användar etablering väljer du alternativet som visas. 

   ![Begäran om användar etablering](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Uppdatera eller ta bort en befintlig lista

Du kan uppdatera eller ta bort en befintlig Galleri app i [Microsoft-programmets nätverks Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

![Lista ett SAML-program i galleriet](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Om du har problem med åtkomsten läser du det föregående avsnittet om hur du skapar ditt konto. Kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)om det inte fungerar.


### <a name="timelines"></a>Tidslinjer

Tids linjen för processen med att lista ett SAML 2,0-eller WS-utfodras program i galleriet är 7 till 10 arbets dagar.

![Tids linje för att visa ett SAML-program i galleriet](./media/howto-app-gallery-listing/timeline.png)

Tids linjen för processen med att ange ett OpenID Connect-program i galleriet är 2 till 5 arbets dagar.

![Tids linje för att visa ett OpenID Connect-program i galleriet](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Förfrågningar

För alla eskaleringar skickar du e-post till [Azure AD SSO integration-teamet](mailto:SaaSApplicationIntegrations@service.microsoft.com)och vi svarar så snart som möjligt.


## <a name="step-6---join-the-microsoft-partner-network"></a>Steg 6 – Anslut till Microsoft Partner Network
Microsoft Partner Network ger direkt åtkomst till exklusiva resurser, program, verktyg och anslutningar. Information om hur du ansluter till nätverket och skapar din marknads plan finns i [Kontakta kommersiella kunder](https://partner.microsoft.com/explore/commercial#gtm).


## <a name="next-steps"></a>Nästa steg

Mer information om hur du skapar program som stöder Azure AD-inloggningar finns i [autentiserings scenarier för Azure AD](authentication-flows-app-scenarios.md).
