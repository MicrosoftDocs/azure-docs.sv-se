---
title: Återhämtning genom bästa metoder för utvecklare som använder Azure AD B2C | Microsoft Docs
description: Återhämtning till bästa praxis för utvecklare i kund identitets-och åtkomst hantering med hjälp av Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff7505e7c47b93f32efd9de60463873026247329
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724933"
---
# <a name="resilience-through-developer-best-practices"></a>Återhämtning genom bästa praxis för utvecklare

I den här artikeln delar vi vissa information som baseras på vår erfarenhet av att arbeta med stora kunder. Du kan tänka på följande rekommendationer när du utformar och implementerar dina tjänster.

![Bild som visar komponenter för utvecklings upplevelse](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Använda Microsoft Authentication Library (MSAL)

[Microsoft Authentication Library (MSAL)](../develop/msal-overview.md) och [Microsoft Identity Web Authentication Library för ASP.net](../develop/reference-v2-libraries.md) fören klar förvärv, hantering, cachelagring och uppdatering av de token som ett program kräver. Biblioteken är optimerade specifikt för att stödja Microsoft-identitet, inklusive funktioner som förbättrar program återhämtning.

Utvecklare bör införa de senaste versionerna av MSAL och hålla sig uppdaterade. Se [hur du ökar återhämtningen av autentisering och auktorisering](resilience-app-development-overview.md) i dina program. Undvik, om möjligt, att implementera din egen autentiserings-stack och använda väletablerade bibliotek.

## <a name="optimize-directory-reads-and-writes"></a>Optimera katalog läsningar och skrivningar

Microsoft Azure AD B2C-katalogtjänsten stöder miljard tals autentiseringar per dag. Den har utformats för en hög takt läsning per sekund. Optimera dina skrivningar för att minimera beroenden och öka återhämtningen.

### <a name="how-to-optimize-directory-reads-and-writes"></a>Optimera katalog läsningar och skrivningar

- **Undvik att skriva funktioner till katalogen vid inloggning**: Kör aldrig en skrivning vid inloggning utan villkor (if-sats) i dina anpassade principer. Ett användnings fall som kräver en skrivning till en inloggning är [just-in-Time-migrering av användar lösen ord](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Undvik alla scenarier som kräver Skriv åtgärder vid varje inloggning.

  - [Villkor](../../active-directory-b2c/userjourneys.md) i en användar resa ser ut så här:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - Bygg motstånds kraft mot bot driven [Sign-UPS genom att integrera med ett Captcha-system](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration).

  - Använd ett [exempel på belastnings test](../../active-directory-b2c/best-practices.md#testing) för att simulera registrering och inloggning. 

- **Förstå begränsning**: katalogen implementerar begränsnings regler för både program-och klient nivå. Det finns ytterligare hastighets begränsningar för Läs-/GET-, Write-/POST-, Update-/tag-och Delete-/DELETE-åtgärder och varje åtgärd har olika gränser.

  - En skrivning vid tidpunkten för inloggning kommer att falla under ett inlägg för nya användare eller för befintliga användare.

  - En anpassad princip som skapar eller uppdaterar en användare på varje inloggnings sätt kan eventuellt nå en gräns för program nivå eller POST hastighet. Samma begränsningar gäller när du uppdaterar katalog objekt via Azure AD eller Microsoft Graph. På samma sätt kan du undersöka läsningarna för att hålla antalet läsningar på varje inloggning till minimum.

  - Beräkna den högsta belastningen för att förutsäga antalet katalog skrivningar och undvika begränsning. Topp trafik uppskattningar bör inkludera uppskattningar för åtgärder som registrering, inloggning och multifaktorautentisering (MFA). Se till att testa både det Azure AD B2C systemet och ditt program för högsta trafik. Det är möjligt att Azure AD B2C kan hantera belastningen utan begränsning, när det inte går att använda de underordnade programmen eller tjänsterna.

  - Förstå och planera din tids linje för migrering. När du planerar att migrera användare till Azure AD B2C att använda Microsoft Graph bör du tänka på program-och klient gränser för att beräkna den tid som krävs för att slutföra migreringen av användare. Om du delar ditt jobb eller skript med hjälp av två program kan du använda gränsen per program. Den måste fortfarande vara lägre än tröskelvärdet för per klient.

  - Förstå effekterna av ditt migreringsjobb på andra program. Överväg att använda den aktiva trafiken som hanteras av andra förlitande program för att se till att du inte orsakar begränsning på klient nivå och resurs effekter för ditt Live-program. Mer information finns i [rikt linjerna för Microsoft Graph begränsning](/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Förläng livs längd för token

Om tjänsten Azure AD B2C-autentisering inte kan slutföra nya inloggningar och inloggningar kan du fortfarande undvika att användare som är inloggade är i ett osannolikt fall. Med [konfiguration](../../active-directory-b2c/configure-tokens.md)kan du tillåta att användare som redan är inloggade att fortsätta använda programmet utan att något avbrott sker förrän användaren loggar ut från programmet eller tids gränsen för [sessionen](../../active-directory-b2c/session-behavior.md) upphör att gälla på grund av inaktivitet.

Dina verksamhets krav och önskad slut användar upplevelse kommer att diktera din frekvens av token-uppdatering för både webb-och SPAs-program.

### <a name="how-to-extend-token-lifetimes"></a>Så här utökar du livs längd för token

- **Webb program**: för webb program där autentiseringstoken verifieras i början av inloggningen är programmet beroende av sessionens cookie för att fortsätta att förlänga sessionens giltighet.

  - Gör det möjligt för användare att förbli inloggade genom att implementera rullande sessions tider som fortsätter att förnya sessioner baserat på användar aktivitet. Om det finns ett långvarigt utfärdande av token, kan dessa tider för sessionen ökas ytterligare som en Databasmigrering-konfiguration i programmet. Behåll sessionens livs längd till det högsta tillåtna antalet.

- **SPAs**: ett spa kan bero på åtkomsttoken för att anropa API: erna. Ett SPA använder traditionellt det implicita flödet som inte resulterar i en uppdateringstoken. SPA kan använda en dold iframe för att utföra nya Tokenbegäran mot behörighets slut punkten om webbläsaren fortfarande har en aktiv session med Azure AD B2C. För SPAs finns det några tillgängliga alternativ som gör att användaren kan fortsätta att använda programmet.

  - Förläng åtkomsttokennas giltighets tid för att uppfylla dina affärs behov.

  - Bygg ditt program så att det använder en API-gateway som proxyserver för autentisering. I den här konfigurationen kommer SPA-inläsningar utan autentisering och API-anropen att göras till API-gatewayen. API-gatewayen skickar användaren genom inloggnings processen med hjälp av en [auktoriseringskod](https://oauth.net/2/grant-types/authorization-code/) som baseras på en princip och autentiserar användaren. Därefter behålls autentiseringsprocessen mellan API-gatewayen och klienten med hjälp av en autentiserings-cookie. API: erna hanteras från API-gatewayen med hjälp av den token som erhålls av API-gatewayen eller någon annan metod för direkt autentisering, till exempel certifikat, klientautentiseringsuppgifter eller API-nycklar.

  - [Migrera din Spa från implicit beviljande](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) av [auktoriseringskod för utfärdande av auktoriseringskod](../../active-directory-b2c/implicit-flow-single-page-application.md) med bevis nyckel för Code Exchange (PKCE) och CORS-stöd (Cross-Origin Resource Sharing). Migrera programmet från MSAL.js 1. x till MSAL.js 2. x för att inse återhämtningen hos webb program.

  - För mobila program rekommenderar vi att du utökar både livstiden för uppdatering och åtkomst-token.

- **Server dels-eller mikrotjänst program**: eftersom backend-program (Server dels) är icke-interaktiva och inte finns i en användar kontext, minskas den potentiella biljett stölden avsevärt. Rekommendationen är att skapa ett saldo mellan säkerhet och livs längd och ange en lång livs längd för token.

## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning

Med [enkel inloggning (SSO)](../manage-apps/what-is-single-sign-on.md)loggar användare in en gång med ett enda konto och får åtkomst till flera program. Programmet kan vara en webb-, mobil-eller ett enda sid program (SPA), oavsett plattform eller domän namn. När användaren loggar in till ett program från början behåller Azure AD B2C en [cookie-baserad session](../../active-directory-b2c/session-behavior.md).

Vid efterföljande autentiseringsbegäranden läser Azure AD B2C och verifierar den cookie-baserade sessionen och utfärdar en åtkomsttoken utan att användaren uppmanas att logga in igen. Om SSO har kon figurer ATS med ett begränsat omfång i en princip eller ett program, kräver senare åtkomst till andra principer och program nya autentisering.

### <a name="how-to-configure-sso"></a>Konfigurera SSO

[Konfigurera SSO](../hybrid/how-to-connect-sso-quick-start.md) så att det är klient-wide (standard) för att tillåta att flera program och användar flöden i klienten delar samma användarsession. Konfigurationen för hela klient organisationen ger störst återhämtning till ny autentisering.  

## <a name="safe-deployment-practices"></a>Säkra distributionsmetoder

De vanligaste avbrottarna i tjänsten är kod och konfigurations ändringar. Implementering av processer för kontinuerlig integrering och kontinuerlig leverans (CICD) och verktyg hjälper till med snabb distribution i stor skala och minskar mänskliga fel vid testning och distribution i produktion. Anta CICD för fel minskning, effektivitet och konsekvens. [Azure-pipeliner](/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) är ett exempel på CICD.

## <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Skydda dina program mot kända säkerhets risker som DDoS-attacker (distributed denial of Service), SQL-injektioner, Cross-Site-skript, fjärrkörning av kod och många andra som dokumenteras i [OWASP översta 10](https://owasp.org/www-project-top-ten/). Distribution av en brand vägg för webbaserade program (WAF) kan försvara sig mot vanliga sårbarheter och sårbarheter.

- Använd Azure- [WAF](../../web-application-firewall/overview.md), som ger centraliserat skydd mot attacker.

- Använd WAF med Azure AD [Identity Protection och villkorlig åtkomst för att tillhandahålla skydd på flera lager](../../active-directory-b2c/conditional-access-identity-protection-overview.md) när du använder Azure AD B2C.  

## <a name="secrets-rotation"></a>Rotering av hemligheter

Azure AD B2C använder hemligheter för program, API: er, principer och kryptering. Hemligheterna säker autentisering, externa interaktioner och lagring. National Institute of Standards and Technology (NIST) anropar det tidsintervall under vilket en speciell nyckel är tillåten för att användas av legitima entiteter a cryptoperiod. Välj rätt längd på [cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) för att uppfylla dina affärs behov. Utvecklare måste manuellt ange förfallo datum och rotera hemligheter korrekt innan de upphör att gälla.

### <a name="how-to-implement-secret-rotation"></a>Så här implementerar du hemlig rotation

- Använd [hanterade identiteter](../managed-identities-azure-resources/overview.md) för resurser som stöds för att autentisera till en tjänst som stöder Azure AD-autentisering. När du använder hanterade identiteter kan du hantera resurser automatiskt, inklusive rotation av autentiseringsuppgifter.

- Ta en förteckning över alla [nycklar och certifikat som kon figurer ATS](../../active-directory-b2c/policy-keys-overview.md) i Azure AD B2C. Den här listan innehåller förmodligen nycklar som används i anpassade principer, [API: er](../../active-directory-b2c/secure-rest-api.md), signerings-ID-token och certifikat för SAML.

- Med CICD kan du rotera hemligheter som håller på att gå ut inom två månader från den förväntade hög säsongen. Den rekommenderade maximala cryptoperiod för privata nycklar som är kopplade till ett certifikat är ett år.

- Övervaka och rotera autentiseringsuppgifter för API-åtkomst, till exempel lösen ord och certifikat.

## <a name="test-rest-apis"></a>Testa REST-API: er

I samband med återhämtning måste testning av REST-API: er innehålla verifiering av – HTTP-koder, svars nytto Last, huvuden och prestanda. Testningen bör inte omfatta bara test av Grattis till sökvägen, utan även kontrol lera om API: n hanterar problem scenarier på ett smidigt sätt.

### <a name="how-to-test-apis"></a>Testa API: er

Vi rekommenderar att test planen innehåller [omfattande API-tester](../../active-directory-b2c/best-practices.md#testing). Om du planerar för en kommande överspänning på grund av befordran eller semester trafik måste du ändra belastnings testningen med de nya uppskattningarna. Genomför belastnings testning av dina API: er och Content Delivery Network (CDN) i en utvecklings miljö och inte i produktion.

## <a name="next-steps"></a>Nästa steg

- [Återhämtnings resurser för Azure AD B2C utvecklare](resilience-b2c.md)
  - [Elastisk slut användar upplevelse](resilient-end-user-experience.md)
  - [Elastiska gränssnitt med externa processer](resilient-external-processes.md)
  - [Återhämtning genom övervakning och analys](resilience-with-monitoring-alerting.md)
- [Bygg återhämtning i din infrastruktur för autentisering](resilience-in-infrastructure.md)
- [Öka återhämtningen av autentisering och auktorisering i dina program](resilience-app-development-overview.md)