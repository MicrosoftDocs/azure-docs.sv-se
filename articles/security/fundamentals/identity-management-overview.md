---
title: Azure-säkerhetsfunktioner som hjälper dig med identitets hantering | Microsoft Docs
description: Lär dig mer om de grundläggande säkerhetsfunktionerna i Azure som hjälper dig med identitets hantering. Se information om ämnen som enkel inloggning och omvänd proxy.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2021
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: d931d3923ff49dde2bea234278c995e79670429f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627641"
---
# <a name="azure-identity-management-security-overview"></a>Översikt över säker identitetshantering i Azure

 Identitets hantering är en process som autentiserar och auktoriserar [säkerhets objekt](/windows/security/identity-protection/access-control/security-principals). Det omfattar också att kontrol lera information om dessa huvud konton (identiteter). Säkerhets objekt (identiteter) kan omfatta tjänster, program, användare, grupper osv. Microsofts identitets-och åtkomst hanterings lösningar hjälper till att skydda åtkomsten till program och resurser i företags data centret och i molnet. Detta skydd möjliggör ytterligare verifierings nivåer, till exempel Multi-Factor Authentication och principer för villkorlig åtkomst. Övervakning av misstänkt aktivitet via avancerad säkerhets rapportering, granskning och avisering bidrar till att minska potentiella säkerhets problem. [Azure Active Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) tillhandahåller enkel inloggning (SSO) till tusentals moln program vara som en tjänst (SaaS) och åtkomst till webbappar som du kör lokalt.
 
Genom att dra nytta av säkerhets fördelarna med Azure Active Directory (Azure AD) kan du:

* Skapa och hantera en enda identitet för varje användare i hybridföretaget och synkronisera användare, grupper och enheter. 
* Ge SSO åtkomst till dina program, inklusive tusentals förintegrerade SaaS-appar.
* Skydda programåtkomsten genom att tillämpa regelbaserad multifaktorautentisering får både lokala program och molnprogram.
* Etablera säker fjärråtkomst till lokala webb program via Azure AD-programproxy.

Målet med den här artikeln är att ge en översikt över de centrala Azure-säkerhetsfunktioner som hjälper dig med identitets hantering. Vi tillhandahåller även länkar till artiklar som ger information om varje funktion så att du kan lära dig mer.  

Artikeln fokuserar på följande viktiga funktioner i Azure Identity Management:

* Enkel inloggning
* Omvänd proxy
* Multi-Factor Authentication
* Azure RBAC (rollbaserad åtkomstkontroll)
* Säkerhetsövervakning, varningar och Machine Learning-baserade rapporter
* Konsument identitets-och åtkomst hantering
* Enhetsregistrering
* Privileged Identity Management
* Identity Protection
* Hybrid identitets hantering/Azure AD Connect
* Azure AD-åtkomstgranskningar

## <a name="single-sign-on"></a>Enkel inloggning

SSO innebär att du kan komma åt alla program och resurser som du behöver göra affärer genom att bara logga in en gång med ett enda användar konto. När du har loggat in, kan du komma åt alla de program du behöver utan att behöva autentisera (till exempel skriva ett lösen ord) en gång.

Många organisationer förlitar sig på SaaS-program som Microsoft 365, Box och Salesforce för användar produktivitet. Tidigare var IT-personal tvungen att individuellt skapa och uppdatera användar konton i varje SaaS-program, och användarna var tvungna att komma ihåg ett lösen ord för varje SaaS-program.

Azure AD utökar lokala Active Directory miljöer till molnet, vilket gör det möjligt för användare att använda sitt primära organisations konto för att logga in till sina domänanslutna enheter och företags resurser, utan även till alla webb-och SaaS program som de behöver för sina jobb.

Användarna behöver inte bara hantera flera uppsättningar användar namn och lösen ord, du kan etablera eller avetablera program åtkomst automatiskt, baserat på deras organisations grupper och deras anställnings status. Azure AD introducerar säkerhets-och åtkomst styrnings kontroller med vilka du centralt kan hantera användarnas åtkomst i SaaS-program.

Läs mer:

* [Översikt på SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Video om grundläggande autentisering](https://www.youtube.com/watch?v=fbSVgC8nGz4&feature=emb_title)
* [Snabb starts serie för program hantering](../../active-directory/manage-apps/view-applications-portal.md)

## <a name="reverse-proxy"></a>Omvänd proxy

Med Azure AD-programproxy kan du publicera lokala program, till exempel [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) -webbplatser, [Outlook Web App](/Exchange/clients/outlook-on-the-web/outlook-on-the-web)och [IIS](https://www.iis.net/)-baserade appar i ditt privata nätverk och ger säker åtkomst till användare utanför nätverket. Programproxyn ger fjärråtkomst och SSO för många typer av lokala webb program med tusentals SaaS-program som stöds av Azure AD. Anställda kan logga in på dina appar från start sidan på sina egna enheter och autentisera via den här molnbaserade proxyn.

Läs mer:

* [Aktivera Azure AD-programproxy](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Publicera program med Azure AD Application Proxy](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Enkel inloggning med programproxy](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Arbeta med villkorlig åtkomst](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure AD Multi-Factor Authentication är en autentiseringsmetod som kräver användning av mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhets lager för användar inloggningar och transaktioner. Multi-Factor Authentication hjälper till att skydda åtkomsten till data och program samtidigt som du kan möta användarnas behov av en enkel inloggnings process. Den ger stark autentisering via en mängd verifierings alternativ: telefonsamtal, textmeddelanden eller aviseringar för mobilappar eller verifierings koder och OAuth-token från tredje part.

Läs mer:

* [Multifaktorautentisering](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Vad är Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Så fungerar Azure AD-multifaktorautentisering](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="azure-rbac"></a>Azure RBAC

Azure RBAC är ett auktoriserings system som bygger på Azure Resource Manager som ger detaljerad åtkomst hantering av resurser i Azure. Med Azure RBAC kan du styra den åtkomst nivå som användarna har. Du kan till exempel begränsa en användare till att endast hantera virtuella nätverk och en annan användare för att hantera alla resurser i en resurs grupp. Azure innehåller flera inbyggda roller som du kan använda. Följande listar fyra grundläggande inbyggda roller. De första tre gäller för alla resurstyper.

- [Ägare](../../role-based-access-control/built-in-roles.md#owner) – Har fullständig åtkomst till alla resurser, inklusive rätten att delegera åtkomst till andra. 
- [Deltagare](../../role-based-access-control/built-in-roles.md#contributor) – Kan skapa och hantera alla typer av Azure-resurser, men kan inte bevilja åtkomst till andra.
- [Reader](../../role-based-access-control/built-in-roles.md#reader) – kan visa befintliga Azure-resurser.
- [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) – Kan hantera användarnas åtkomst till Azure-resurser.

Läs mer:

* [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
* [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Säkerhetsövervakning, varningar och Machine Learning-baserade rapporter

Säkerhetsövervakning, varningar och Machine Learning-baserade rapporter som identifierar inkonsekventa åtkomst mönster kan hjälpa dig att skydda din verksamhet. Du kan använda Azure AD-åtkomst och användnings rapporter för att få insyn i integriteten och säkerheten i din organisations katalog. Med den här informationen kan en katalog administratör bättre avgöra var möjliga säkerhets risker kan vara så att de kan planera för att minska riskerna.

I den Azure Portal, hamnar rapporter i följande kategorier:

* **Avvikelse rapporter**: innehåller inloggnings händelser som vi har identifierat som avvikande. Målet är att du ska känna till sådan aktivitet och göra det möjligt för dig att avgöra om en händelse är misstänkt.
* **Integrerade program rapporter**: ger insikter om hur moln program används i din organisation. Azure AD erbjuder integrering med tusentals moln program.
* **Fel rapporter**: ange fel som kan uppstå när du etablerar konton till externa program.
* **Användarspecifika rapporter**: Visa information om enhets inloggnings aktiviteter för en speciell användare.
* **Aktivitets loggar**: innehåller en lista över alla granskade händelser under de senaste 24 timmarna, senaste 7 dagarna eller de senaste 30 dagarna samt grupp aktivitets ändringar och registrering av lösen ord.

Läs mer:

* [Visa åtkomst- och användningsrapporterna](../../active-directory/reports-monitoring/overview-reports.md)
* [Kom igång med Azure Active Directory rapportering](../../active-directory/reports-monitoring/overview-reports.md)
* [Azure Active Directory rapporterings guide](../../active-directory/reports-monitoring/overview-reports.md)

## <a name="consumer-identity-and-access-management"></a>Konsument identitets-och åtkomst hantering

Azure AD B2C är en global, identitets hanterings tjänst med hög tillgänglighet för klientbaserade program som kan skalas till hundratals miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Dina kunder kan logga in på alla dina program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa nya autentiseringsuppgifter.

Tidigare hade programutvecklare som ville registrera sig för kunder och logga in dem i sina program har skrivit sin egen kod. Och de använde lokala databaser eller system för att lagra användarnamn och lösenord. Azure AD B2C ger organisationen ett bättre sätt att integrera konsument identitets hantering i program med hjälp av en säker, standard-baserad plattform och en stor uppsättning utöknings bara principer.

När du använder Azure AD B2C kan dina konsumenter registrera sig för dina program genom att använda sina befintliga sociala konton (Facebook, Google, Amazon, LinkedIn) eller genom att skapa nya autentiseringsuppgifter (e-postadress och lösen ord eller användar namn och lösen ord).

Läs mer:

* [Vad är Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C Preview: Registrera och logga in användare i dina program](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C för hands version: typer av program](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Enhetsregistrering

Azure AD Device Registration är grunden för enhets scenarier för [villkorlig åtkomst](../../active-directory/devices/device-management-azure-portal.md) . När en enhet registreras förser Azure AD Device Registration enheten med en identitet som används för att autentisera enheten när användaren loggar in. Den autentiserade enheten och attributen för enheten kan sedan användas för att tillämpa principer för villkorlig åtkomst för program som finns i molnet och lokalt.

När den kombineras med en hanterings lösning för mobila enheter, till exempel Intune, uppdateras enhetens attribut i Azure AD med ytterligare information om enheten. Du kan sedan skapa regler för villkorlig åtkomst som tvingar åtkomst från enheter att uppfylla dina standarder för säkerhet och efterlevnad.

Läs mer:

* [Kom igång med registrering av Azure AD-enheter](../../active-directory/devices/device-management-azure-portal.md)
* [Automatisk enhets registrering med Azure AD för domänanslutna Windows-enheter](../../active-directory/devices/hybrid-azuread-join-plan.md)
* [Konfigurera automatisk registrering av Windows-domänanslutna enheter med Azure AD](../../active-directory/devices/hybrid-azuread-join-plan.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Med Azure AD Privileged Identity Management kan du hantera, kontrol lera och övervaka dina privilegierade identiteter och åtkomst till resurser i Azure AD samt andra Microsoft-onlinetjänster, till exempel Microsoft 365 och Microsoft Intune.

Användare behöver ibland utföra privilegierade åtgärder i Azure eller Microsoft 365 resurser eller i andra SaaS-appar. Detta behöver ofta betyda att organisationer måste ge användare permanent privilegie rad åtkomst i Azure AD. Sådan åtkomst är en växande säkerhets risk för resurser som är värd för molnet, eftersom organisationer inte kan övervaka vad användarna gör med sina administratörs privilegier. Om ett användar konto med privilegie rad åtkomst komprometteras, kan det dessutom påverka organisationens övergripande moln säkerhet. Azure AD Privileged Identity Management hjälper till att minimera den här risken.

Med Azure AD Privileged Identity Management kan du:

* Se vilka användare som är Azure AD-administratörer.
* Aktivera administrativ åtkomst på begäran, just-in-Time (JIT) till Microsoft-tjänster, till exempel Microsoft 365 och Intune.
* Hämta rapporter om administratörs åtkomst historik och ändringar i administratörs tilldelningar.
* Få aviseringar om åtkomst till en privilegie rad roll.

Läs mer:

* [Vad är Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Tilldela Azure AD Directory-roller i PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection är en säkerhets tjänst som tillhandahåller en samlad vy över risk identifieringar och potentiella sårbarheter som påverkar organisationens identiteter. Identitets skydd drar nytta av befintliga funktioner för avvikelse identifiering i Azure AD, som är tillgängliga via rapporter om avvikande aktivitets rapporter i Azure AD. Identitets skydd introducerar också nya risk identifierings typer som kan identifiera avvikelser i real tid.

Läs mer:

* [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Kanal 9: Azure AD och identitet Visa: identitets skydd för hands version](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybrid identitets hantering/Azure AD Connect

Microsofts identitetslösningar omfattar både lokala och molnbaserade funktioner, skapar en enda användaridentitet för autentisering och auktorisering till alla resurser, oavsett plats. Vi kallar det för hybrididentitet. Azure AD Connect är Microsoft-verktyget som har utformats för att uppfylla och uppnå dina hybrididentitetsmål. På så sätt kan du erbjuda en gemensam identitet för dina användare för Microsoft 365, Azure och SaaS-program som är integrerade med Azure AD. Den tillhandahåller följande funktioner:

* Synkronisering
* AD FS-och Federations integrering
* Passera igenom autentisering
* Hälsoövervakning

Läs mer:

* [Hybrid identitets white paper](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD-teamets blogg](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD-åtkomstgranskningar

Med åtkomstgranskningar i Azure Active Directory (Azure AD) kan organisationer effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och priviligierade rolltilldelningar.

Läs mer:

* [Azure AD-åtkomstgranskningar](../../active-directory/governance/access-reviews-overview.md)
* [Hantera användare med Azure AD-åtkomstgranskningar](../../active-directory/governance/access-reviews-overview.md)