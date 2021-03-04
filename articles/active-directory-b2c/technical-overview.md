---
title: Teknisk och funktions översikt – Azure Active Directory B2C
description: En djupgående introduktion till funktionerna och teknikerna i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77dab865b2b023f59e6776349b4045107bdb3a29
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095501"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Teknisk och funktions översikt över Azure Active Directory B2C

Den här artikeln innehåller en mer djupgående introduktion till tjänsten. [Azure Active Directory B2C](overview.md) Som beskrivs här är de primära resurser som du arbetar med i tjänsten, dess funktioner och hur dessa gör att du kan tillhandahålla en helt anpassad identitets upplevelse för dina kunder i dina program.

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C klient

I Azure Active Directory B2C (Azure AD B2C) representerar en *klient* organisation och är en katalog med användare. Varje Azure AD B2C-klientorganisation är separat och åtskild från andra Azure AD B2C-klientorganisationer. En Azure AD B2C-klient skiljer sig från en Azure Active Directory klient som du kanske redan har.

De primära resurserna som du arbetar med i en Azure AD B2C klient organisation är:

* **Katalog** – *katalogen* är den plats där Azure AD B2C lagrar dina användares autentiseringsuppgifter och profil data, samt dina program registreringar.
* **Program registreringar** – du registrerar dina webb-, mobil-och interna program med Azure AD B2C för att aktivera identitets hantering. Dessutom kan du använda API: er som du vill skydda med Azure AD B2C.
* **Användar flöden** och **anpassade principer** – de inbyggda identitets funktionerna (användar flöden) och helt anpassningsbara (anpassade principer) för dina program.
  * Använd *användar flöden* för snabb konfiguration och aktivering av vanliga identitets uppgifter som registrering, inloggning och profil redigering.
  * Använd *anpassade principer* för att aktivera användar upplevelser för vanliga identitets uppgifter, utan även för att ge stöd för komplexa identitets arbets flöden som är unika för din organisation, kunder, anställda, partner och medborgarna.
* **Identitets leverantörer** – Federations inställningar för:
  * *Sociala* identitets leverantörer som Facebook, LinkedIn eller Twitter som du vill stödja i dina program.
  * *Externa* identitets leverantörer som stöder standard identitets protokoll som OAuth 2,0, OpenID Connect med mera.
  * *Lokala* konton som gör det möjligt för användare att registrera sig och logga in med ett användar namn (eller e-postadress eller annat ID) och lösen ord.
* **Nycklar** – Lägg till och hantera krypterings nycklar för signering och validering av tokens, klient hemligheter, certifikat och lösen ord.

En Azure AD B2C klient är den första resurs som du måste skapa för att komma igång med Azure AD B2C. Lär dig mer i [Självstudier: skapa en Azure Active Directory B2C-klient](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Konton i Azure AD B2C

Azure AD B2C definierar flera typer av användar konton. Azure Active Directory, Azure Active Directory B2B och Azure Active Directory B2C dela dessa konto typer.

* **Arbets konto** – användare med arbets konton kan hantera resurser i en klient, och med en administratörs roll kan de även hantera klienter. Användare med arbets konton kan skapa nya konsument konton, återställa lösen ord, blockera/avblockera konton och ange behörigheter eller tilldela ett konto till en säkerhets grupp.
* **Gäst konto** – externa användare som du bjuder in till din klient som gäster. Ett typiskt scenario för att bjuda in en gäst användare till din Azure AD B2C klient organisation är att dela administrations ansvar.
* **Konsument konto** – konsument konton är de konton som skapas i Azure AD B2C-katalogen när användare slutför inloggnings användar resan i ett program som du har registrerat i din klient.

![Sidan Azure AD B2C användar hantering i Azure Portal](media/technical-overview/portal-01-users.png)<br/>*Bild: användar katalog i en Azure AD B2C-klient i Azure Portal*

### <a name="consumer-accounts"></a>Konsument konton

Med ett *konsument* konto kan användarna logga in till de program som du har skyddat med Azure AD B2C. Användare med konsument konton kan dock inte komma åt Azure-resurser, till exempel Azure Portal.

Ett konsument konto kan associeras med dessa identitets typer:

* **Lokal** identitet med det användar namn och lösen ord som lagras lokalt i Azure AD B2C-katalogen. Vi refererar ofta till dessa identiteter som "lokala konton".
* **Sociala** eller **företags** identiteter, där användarens identitet hanteras av en federerad identitets leverantör som Facebook, Microsoft, ADFS eller Salesforce.

En användare med ett konsument konto kan logga in med flera identiteter, till exempel användar namn, e-post, anställnings-ID, myndighets-ID och andra. Ett enda konto kan ha flera identiteter, både lokala och sociala.

![Konsument konto identiteter](media/technical-overview/identities.png)<br/>*Bild: ett enda konsument konto med flera identiteter i Azure AD B2C*

Med Azure AD B2C kan du hantera vanliga attribut för konsument konto profiler, t. ex. visnings namn, efter namn, förnamn, stad och andra. Du kan också utöka Azure AD-schemat för att lagra ytterligare information om dina användare. Till exempel land/region eller placering, önskat språk och inställningar som om de vill prenumerera på ett nyhets brev eller aktivera Multi-Factor Authentication.

Läs mer om användar konto typerna i Azure AD B2C i [Översikt över användar konton i Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Externa identitetsprovidrar

Du kan konfigurera Azure AD B2C så att användarna kan logga in till ditt program med autentiseringsuppgifter från externa sociala eller företags identitets leverantörer (IdP). Azure AD B2C stöder externa identitets leverantörer som Facebook, Microsoft-konto, Google, Twitter och alla identitets leverantörer som stöder OAuth 1,0-, OAuth 2,0-, OpenID Connect-och SAML-protokoll.

![Externa identitetsprovidrar](media/technical-overview/external-idps.png)

Med extern Identity Provider Federation kan du erbjuda konsumenterna möjlighet att logga in med sina befintliga sociala konton eller företags konton, utan att behöva skapa ett nytt konto för ditt program.

På sidan för registrering eller inloggning visar Azure AD B2C en lista över externa identitets leverantörer som användaren kan välja för inloggning. När de har valt en av de externa identitets leverantörerna tas de (omdirigeras) till den valda providerns webbplats för att slutföra inloggnings processen. När användaren har loggat in returneras de till Azure AD B2C för autentisering av kontot i ditt program.

![Mobilt inloggnings exempel med ett socialt konto (Facebook)](media/technical-overview/external-idp.png)

Information om hur du lägger till identitets leverantörer i Azure AD B2C finns i [lägga till identitets leverantörer i dina program i Azure Active Directory B2C](add-identity-provider.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Identitets upplevelser: användar flöden eller anpassade principer

Utöknings bara princip ramverket för Azure AD B2C är dess kärn styrka. Principer beskriver användarnas identitets upplevelser, till exempel registrering, inloggning och profil redigering.

I Azure AD B2C finns det två primära sökvägar som du kan vidta för att ge dessa identitets upplevelser: användar flöden och anpassade principer.

* **Användar flöden** är fördefinierade, inbyggda, konfigurerbara principer som vi tillhandahåller så att du kan skapa registrering, inloggning och princip redigerings upplevelser på några minuter.

* Med **anpassade principer** kan du skapa egna användar resor för komplexa identitets miljö scenarier.

Både användar flöden och anpassade principer drivs av *identitets miljö ramverket*, Azure AD B2C's princip Orchestration-motor.

### <a name="user-flow"></a>Användarflöde

För att hjälpa dig att snabbt skapa de vanligaste identitets uppgifterna innehåller Azure Portal flera fördefinierade och konfigurerbara principer som kallas *användar flöden*.

Du kan konfigurera inställningar för användar flöden så att de kan styra identitets upplevelsens beteenden i dina program:

* Konto typer som används för inloggning, till exempel sociala konton som en Facebook eller lokala konton som använder en e-postadress och ett lösen ord för inloggning
* Attribut som ska samlas in från konsumenten, till exempel förnamn, post nummer eller land/region för placering
* Azure AD-Multi-Factor Authentication (MFA)
* Anpassning av användar gränssnittet
* Uppsättning anspråk i en token som programmet tar emot när användaren har slutfört användar flödet
* Sessionshantering
* ... med mera.

De vanligaste identitets scenarierna för majoriteten av mobil-, webb-och program med en enda sida kan definieras och implementeras effektivt med användar flöden. Vi rekommenderar att du använder de inbyggda användar flödena, om du inte har avancerade användar körnings scenarier som kräver full flexibilitet för anpassade principer.

Lär dig mer om användar flöden i [användar flöden i Azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Anpassad princip

Anpassade principer låser upp åtkomst till den fulla kraften i IEF-Orchestration-motorn (Identity Experience Framework). Med anpassade principer kan du använda IEF för att bygga nästan all autentisering, användar registrering eller profil redigerings upplevelse som du kan föreställa dig.

Med Identity Experience Framework kan du skapa användar resor med valfri kombination av steg. Exempel:

* Federera med andra identitets leverantörer
* Krav för MFA (Multi-Factor Authentication) för första och tredje part
* Samla in indata från användaren
* Integrera med externa system med hjälp av REST API kommunikation

Varje sådan användar resa definieras av en princip och du kan skapa så många eller så få principer som du behöver för att aktivera den bästa användar upplevelsen för din organisation.

![Diagram som visar ett exempel på en komplex användar resa som har Aktiver ATS av IEF](media/technical-overview/custom-policy.png)

En anpassad princip definieras av flera XML-filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar anspråks schema, anspråks omvandlingar, innehålls definitioner, anspråks leverantörer, tekniska profiler, åtgärder för användar resans dirigering och andra aspekter av identitets upplevelsen.

Den kraftfulla flexibiliteten med anpassade principer är mest lämplig för när du behöver bygga komplexa identitets scenarier. Utvecklare som konfigurerar anpassade principer måste definiera de betrodda relationerna i noggrann detalj för att inkludera slut punkter för metadata, exakta definitioner för anspråk och konfigurera hemligheter, nycklar och certifikat efter behov av varje identitets leverantör.

Lär dig mer om anpassade principer i [anpassade principer i Azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protokoll och token

- För program har Azure AD B2C stöd för [OAuth 2,0](protocols-overview.md)-, [OpenID Connect](openid-connect.md)-och [SAML-protokoll](saml-service-provider.md) för användar resor. Ditt program startar användar resan genom att utfärda autentiseringsbegäranden till Azure AD B2C. Resultatet av en begäran till Azure AD B2C är en säkerhetstoken, till exempel en [ID-token, en åtkomsttoken](tokens-overview.md)eller en SAML-token. Den här säkerhetstoken definierar användarens identitet i programmet.

- För externa identiteter stöder Azure AD B2C Federation med alla OAuth 1,0-, OAuth 2,0-, OpenID Connect-och SAML Identity-providers.

Följande diagram visar hur Azure AD B2C kan kommunicera med en mängd olika protokoll inom samma autentiseringspaket:

![Diagram över OIDC-baserade klient program som federeras med en SAML-baserad IdP](media/technical-overview/protocols.png)

1. Det förlitande parts programmet initierar en auktoriseringsbegäran för Azure AD B2C att använda OpenID Connect.
1. När en användare av programmet väljer att logga in med en extern identitetsprovider som använder SAML-protokollet anropar Azure AD B2C SAML-protokollet för att kommunicera med den identitets leverantören.
1. När användaren har slutfört inloggnings åtgärden med den externa identitets leverantören, returnerar Azure AD B2C token till det förlitande part programmet med OpenID Connect.

## <a name="application-integration"></a>Integrering av program

När en användare vill logga in i ditt program, oavsett om det är en webb-, mobil-, skriv bords-eller Enkels Ides applikation (SPA), initierar programmet en auktoriseringsbegäran till en användar flöde eller en anpassad princip slut punkt. Användar flödet eller den anpassade principen definierar och styr användarens upplevelse. När de slutför ett användar flöde, till exempel *registrerings-eller inloggnings* flödet, Azure AD B2C skapar en token och omdirigerar sedan tillbaka användaren till ditt program.

![Mobilapp med pilar som visar flödet mellan Azure AD B2C inloggnings sida](media/technical-overview/app-integration.png)

Flera program kan använda samma användar flöde eller anpassad princip. Ett enda program kan använda flera användar flöden eller anpassade principer.

Om du till exempel vill logga in i ett program använder programmet *inloggnings-eller inloggnings* användar flödet. När användaren har loggat in kanske de vill redigera sin profil, så att programmet initierar en annan auktoriseringsbegäran, den här gången med *profil redigera* användar flöde.

## <a name="seamless-user-experiences"></a>Sömlösa användar upplevelser

I Azure AD B2C kan du utforma användarnas identitets upplevelser så att de sidor som visas i kombination med utseendet och känslan av ditt varumärke är smidigare. Du får nästan fullständig kontroll över HTML-och CSS-innehåll som visas för användarna när de fortsätter genom ditt programs identitets transporter. Med den här flexibiliteten kan du bibehålla varumärkes-och visualiserings konsekvens mellan ditt program och Azure AD B2C.

![Skärm dum par av varumärkes anpassad inloggnings inloggnings sida](media/technical-overview/seamless-ux.png)

Information om hur du anpassar gränssnitt finns [i avsnittet om anpassning av användar gränssnitt i Azure Active Directory B2C](customize-ui-with-html.md).

## <a name="localization"></a>Lokalisering

Med språk anpassning i Azure AD B2C kan du hantera olika språk som passar dina kund behov. Microsoft tillhandahåller översättningarna för 36-språk, men du kan också tillhandahålla egna översättningar för alla språk. Även om din upplevelse bara är avsedd för ett enda språk kan du anpassa valfri text på sidorna.

![Tre inloggnings sidor som visar användar gränssnitts text på olika språk](media/technical-overview/localization.png)

Se hur lokalisering fungerar i [språk anpassning i Azure Active Directory B2C](language-customization.md).

## <a name="add-your-own-business-logic"></a>Lägg till din egen affärslogik

Om du väljer att använda anpassade principer kan du integrera med ett RESTful-API i en användar resa för att lägga till din egen affärs logik i resan. Azure AD B2C kan till exempel utbyta data med en RESTful-tjänst för att:

* Visa anpassade användarvänliga fel meddelanden.
* Verifiera användarindata för att förhindra att felaktiga data sparas i din användar katalog. Du kan till exempel ändra de data som anges av användaren, t. ex. med versaler för deras första namn om de anges i gemener.
* Utöka användar data genom att integrera med företagets affärs program.
* Med RESTful-anrop kan du skicka push-meddelanden, uppdatera företags databaser, köra en användarmigrering, hantera behörigheter, granska databaser med mera.

Förmåns program är ett annat scenario som aktive ras av Azure AD B2C's-stöd för att anropa REST API: er Till exempel kan din RESTful-tjänst ta emot en användares e-postadress, fråga din kund databas och sedan returnera användarens lojalitets nummer till Azure AD B2C. Returnerade data kan lagras i användarens katalog konto i Azure AD B2C. därefter utvärderas de efterföljande stegen i principen eller inkluderas i åtkomsttoken.

![Branschspecifika integrering i ett mobil program](media/technical-overview/lob-integration.png)

Du kan lägga till ett REST API-anrop i alla steg i användar resan som definieras av en anpassad princip. Du kan till exempel anropa en REST API:

* Under inloggningen, precis innan Azure AD B2C validerar autentiseringsuppgifterna
* Omedelbart efter inloggning
* Innan Azure AD B2C skapar ett nytt konto i katalogen
* När Azure AD B2C har skapat ett nytt konto i katalogen
* Innan Azure AD B2C utfärdar en åtkomsttoken

Om du vill se hur du använder anpassade principer för RESTful API-integrering i Azure AD B2C, se [integrera REST API anspråk utbyten i din Azure AD B2C anpassade princip](custom-policy-rest-api-intro.md).

## <a name="protect-customer-identities"></a>Skydda kund identiteter

Azure AD B2C följer säkerhets-, sekretess-och andra åtaganden som beskrivs i [Microsoft Azure säkerhets Center](https://www.microsoft.com/trustcenter/cloudservices/azure).

Sessioner är modellerade som krypterade data, med krypterings nyckeln som endast är känd för tjänsten Azure AD B2C Security token. En stark krypteringsalgoritm, AES-192, används. Alla kommunikations vägar skyddas med TLS för konfidentialitet och integritet. Vår säkerhetstokentjänst använder ett EV-certifikat (Extended Validation) för TLS. I allmänhet minimerar säkerhetstokentjänst risken för Cross-Site Scripting (XSS) genom att inte åter givning av icke-betrodda indatamängdar.

![Diagram över säkra data under överföring och i vila](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Åtkomst till användar data

Azure AD B2C klienter delar många egenskaper med företags Azure Active Directory-klienter som används för anställda och partner. Delade aspekter innehåller mekanismer för att Visa administrativa roller, tilldela roller och gransknings aktiviteter.

Du kan tilldela roller för att kontrol lera vem som kan utföra vissa administrativa åtgärder i Azure AD B2C, inklusive:

* Skapa och hantera alla aspekter av användar flöden
* Skapa och hantera attributet schema tillgängligt för alla användar flöden
* Konfigurera identitets leverantörer för användning i direkt Federation
* Skapa och hantera förtroende Ramverks principer i Identity Experience Framework (anpassade principer)
* Hantera hemligheter för Federation och kryptering i Identity Experience Framework (anpassade principer)

Mer information om Azure AD-roller, inklusive Azure AD B2C administrations roll stöd, finns [i administratörs roll behörigheter i Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Azure AD B2C Multi-Factor Authentication (MFA) hjälper till att skydda åtkomsten till data och program samtidigt som användarnas skull bibehålls. Den ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering genom att erbjuda en mängd användarvänliga autentiseringsmetoder. Dina användare kan kanske inte anropas för MFA baserat på konfigurations beslut som du kan göra som administratör.

Se Aktivera MFA i användar flöden i [Aktivera Multi-Factor Authentication i Azure Active Directory B2C](multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Utelåsning av Smart konto

För att förhindra lösen ords gissnings försök i brutet läge, Azure AD B2C använda en sofistikerad strategi för att låsa konton baserat på IP-adressen för begäran, lösen orden som anges och flera andra faktorer. Låsningens varaktighet höjs automatiskt baserat på risk och antalet försök.

![Konto Smart utelåsning](media/technical-overview/smart-lockout1.png)

Mer information om hur du hanterar lösen ords skydds inställningar finns i [Hantera hot mot resurser och data i Azure Active Directory B2C](threat-management.md).

### <a name="password-complexity"></a>Lösenordskomplexitet

Under registreringen eller lösen ords återställning måste användarna ange ett lösen ord som uppfyller komplexitets reglerna. Som standard tillämpar Azure AD B2C en stark lösen ords princip. Azure AD B2C innehåller också konfigurations alternativ för att ange komplexitets kraven för de lösen ord som kunderna använder.

Du kan konfigurera krav för lösen ords komplexitet i både [användar flöden](password-complexity.md) och [anpassade principer](password-complexity.md).

## <a name="auditing-and-logs"></a>Granskning och loggar

Azure AD B2C utvärderar gransknings loggar som innehåller aktivitets information om dess resurser, utfärdade tokens och administratörs åtkomst. Du kan använda dessa gransknings loggar för att förstå plattforms aktivitet och diagnostisera problem. Gransknings logg poster är tillgängliga strax efter att aktiviteten som skapade händelsen inträffar.

I en Gransknings logg, som är tillgänglig för din Azure AD B2C klient eller för en viss användare, kan du hitta information, inklusive:

* Aktiviteter som rör auktorisering av en användare för att få åtkomst till B2C-resurser (till exempel en administratör som har åtkomst till en lista över B2C-principer)
* Aktiviteter relaterade till katalogattribut som hämtats när en administratör loggar in med hjälp av Azure Portal
* Skapa, läsa, uppdatera och ta bort (CRUD) åtgärder i B2C-program
* CRUD-åtgärder för nycklar som lagras i en B2C-nyckel behållare
* CRUD-åtgärder på B2C-resurser (till exempel principer och identitets leverantörer)
* Validering av användarautentiseringsuppgifter och utfärdande av token

![Gransknings loggen för enskilda användare visas i Azure Portal](media/technical-overview/audit-log.png)

Mer information om gransknings loggar finns i avsnittet [komma åt Azure AD B2C gransknings loggar](view-audit-logs.md).

### <a name="usage-insights"></a>Användnings insikter

Med Azure AD B2C kan du upptäcka när personer registrerar sig eller loggar in i din webbapp, var dina användare finns och vilka webbläsare och operativ system de använder. Genom att integrera Azure Application insikter i Azure AD B2C med anpassade principer kan du få information om hur personer registrerar sig, loggar in, återställer sitt lösen ord eller redigerar sina profiler. Med sådan kunskap kan du fatta data drivna beslut för dina kommande utvecklings cyklar.

Lär dig mer om användnings analys i [spåra användar beteende i Azure Active Directory B2C att använda Application Insights](analytics-with-application-insights.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har djupare erfarenhet av de funktioner och tekniska aspekterna av Azure Active Directory B2C, kom igång med vår [självstudie för att skapa en Azure Active Directory B2C-klient](tutorial-create-tenant.md).
