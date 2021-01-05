---
title: Planera distribution av en Azure Active Directory Mina appar
description: Vägledning om hur du distribuerar Azure Active Directory Mina appar
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: 209760b51e0f3ab931fa43ef88406f15c75ac079
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832547"
---
# <a name="plan-an-azure-active-directory-my-apps-deployment"></a>Planera distribution av en Azure Active Directory Mina appar

Azure Active Directory (Azure AD) Mina appar är en webbaserad portal som hjälper till att sänka support kostnaderna, öka produktiviteten och säkerheten och minska användarnas besvär. Systemet innehåller detaljerad rapportering som spårar när du kommer åt systemet och meddelar administratörer om missbruk eller missbruk. Mer information om hur du använder Mina appar från en slut användar perspektiv finns i [hjälpen för min Apps-portalen](../user-help/my-apps-portal-end-user-access.md).

Genom att använda Azure AD Mina appar kan du:

* Identifiera och få åtkomst till alla företagets Azure AD-anslutna resurser, till exempel program
* Begär åtkomst till nya appar och grupper
* Hantera åtkomst till dessa resurser för andra
* Hantera lösen ords återställning med självbetjäning och Azure AD Multi-Factor Authentication inställningar
* Hantera sina enheter

Administratörer kan också hantera:

* Villkor för tjänsten
* Organisationer
* Åtkomstgranskningar


## <a name="benefits-of-azure-ad-my-apps-integration"></a>Fördelar med Azure AD My Apps-integrering

Azure AD Mina appar fördelar företag på följande sätt:

**Ger intuitiv användar upplevelse**: Mina appar ger dig en enda plattform för alla dina Azure-anslutna program för enkel inloggning (SSO). Du har en enhetlig Portal för att hitta befintliga inställningar och nya funktioner, t. ex. grupp hantering och lösen ords återställning via självbetjäning, när de läggs till. Den intuitiva upplevelsen gör det möjligt för användarna att återgå till att arbeta snabbare och bli mer produktiva, samtidigt som de minskar sin syn.

**Ökar produktiviteten**: alla användar program i Mina appar har SSO aktive rad. Att aktivera SSO över företags program och Microsoft 365 skapar en överlägsen inloggning genom att minska eller eliminera ytterligare prompter för inloggning. Mina appar använder självbetjäning och dynamiskt medlemskap och förbättrar den övergripande säkerheten för ditt identitets system. Mina appar säkerställer att rätt personer hanterar åtkomst till programmen. Mina appar fungerar som en enhetlig landnings sida så att du snabbt kan hitta resurser och fortsätta arbeta aktiviteter.

**Hanterar kostnad**: att aktivera Mina appar med Azure AD kan hjälpa dig med divestment av lokala infrastrukturer. Det minskar support kostnaderna genom att tillhandahålla en konsekvent Portal för att hitta alla dina appar, begära åtkomst till resurser och hantera konton.

**Ökar flexibiliteten och säkerheten**: Mina appar ger dig till gång till den säkerhet och flexibilitet som en moln plattform erbjuder. Administratörer kan enkelt ändra inställningar till program och resurser och kan hantera nya säkerhets krav utan att påverka användarna.

**Möjliggör robust gransknings-och användnings spårning**: gransknings-och användnings spårning för alla användar funktioner gör att du vet när användarna använder sina resurser och ser till att du kan utvärdera säkerheten.

### <a name="licensing-considerations"></a>Licens överväganden

Mina appar är kostnads fria och kräver inga licenser för användning på en grundläggande nivå. Antalet objekt i din katalog och de ytterligare funktioner som du vill distribuera kan dock kräva ytterligare licenser. Några vanliga Azure AD-scenarier som har licensierings krav omfattar följande säkerhetsfunktioner:

* [Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
* [Grupp-baserat medlemskap](../fundamentals/active-directory-manage-groups.md)
* [Återställning av lösenord för självbetjäning](../authentication/tutorial-enable-sspr.md)
* [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)

Se den [fullständiga licens guiden för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-azure-ad-my-apps"></a>Krav för att distribuera Azure AD Mina appar

Slutför följande krav innan du påbörjar det här projektet:

* [Integrera Application SSO](./plan-sso-deployment.md)
* [Hantera Azure AD-användare och-grupp-infrastruktur](../fundamentals/active-directory-manage-groups.md)

## <a name="plan-azure-ad-my-apps-deployment"></a>Planera distributionen av Azure AD Mina appar

I följande tabell beskrivs de viktigaste användnings fallen för distribution av mina appar:

| Område| Beskrivning |
| - | - |
| Åtkomst| Min Apps-Portal kan nås från företagets och personliga enheter inom företags nätverket. |
|Åtkomst | Min Apps-Portal är tillgänglig från företags enheter utanför företags nätverket. |
| Granskning| Användnings data hämtas till företags system minst var 29: e dag. |
| Styrning| Livs cykeln för användar tilldelningarna till Azure AD-anslutna program och grupper definieras och övervakas. |
| Säkerhet| Åtkomst till resurser styrs via användar-och grupp tilldelningar. Endast auktoriserade användare kan hantera resurs åtkomst. |
| Prestanda| Tids linjer för distribution av åtkomst tilldelningar dokumenteras och övervakas. |
| Användarupplevelse| Användarna är medvetna om mina apps-funktioner och hur de används.|
| Användarupplevelse| Användare kan hantera sina åtkomst till program och grupper.|
| Användarupplevelse| Användare kan hantera sina konton. |
| Användarupplevelse| Användarna är medvetna om webbläsarkompatibilitet. |
| Support| Användare kan hitta support för mina apps-problem. |


> [!TIP]
> Mina appar kan användas med interna företags-URL: er vid fjärr anslutning med hjälp av programproxy. Mer information finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

### <a name="best-practices-for-deploying-azure-ad-my-apps"></a>Metod tips för att distribuera Azure AD Mina appar

Funktionerna i Mina appar kan aktive ras gradvis. Vi rekommenderar följande distributions ordning:

1. Mina appar
   * Starta App
   * Hantering av självbetjänings program
   * Microsoft 365-integrering

1. Identifiering av självbetjänings program
   * Återställning av lösenord för självbetjäning
   * Multi-Factor Authentication inställningar
   * Enhetshantering
   * Villkor för användning
   * Hantera organisationer

1. Mina grupper
   * Självbetjäning, grupphantering
1. Åtkomstgranskningar
   * Åtkomst gransknings hantering

Från och med mina appar introducerar användarna till portalen som en gemensam plats för att få åtkomst till resurser. Tillägget av självbetjänings-och program identifiering bygger på mina apps-upplevelser. Mina grupper och åtkomst granskningar bygger på självbetjänings funktionerna.

### <a name="plan-configurations-for-azure-my-apps"></a>Planera konfigurationer för Azure mina appar

I följande tabell visas flera viktiga konfigurationer för Mina appar och de typiska värden som du kan använda:

| Konfiguration| Typiska värden |
| - | - |
| Fastställa pilot grupper| Identifiera Azure AD-säkerhetsgruppen som ska användas och se till att alla pilot medlemmar ingår i gruppen. |
| Bestäm vilka grupper eller grupper som ska aktive ras för produktion.| Identifiera Azure AD-säkerhetsgrupper, eller de Active Directory grupper som synkroniserats till Azure AD, som ska användas. Se till att alla pilot medlemmar är en del av gruppen. |
| Tillåt användare att använda SSO för vissa typer av program| Federerad SSO, OAuth, lösen ords inloggning, App-proxy |
| Tillåt användare att använda självbetjäning för återställning av lösen ord | Ja |
| Tillåt användare att använda Multi-Factor Authentication| Ja |
| Tillåt användare att använda självbetjänings grupp hantering för vissa typer av grupper| Säkerhets grupper, Microsoft 365 grupper |
| Tillåt användare att använda självbetjänings program hantering| Ja |
| Tillåt användare att använda åtkomst granskningar| Ja |

### <a name="plan-consent-strategy"></a>Planera godkännande strategin

Användare eller administratörer måste godkänna användnings villkoren och sekretess policyn för ett program. Om möjligt kan du använda administratörs medgivande, vilket ger användarna en bättre upplevelse.

Om du vill använda administratörs medgivande måste du vara global administratör för organisationen och programmen måste vara antingen:

* Registrerad i din organisation
* Registreras i en annan Azure AD-organisation och har tidigare godkänts av minst en användare

Mer information finns i Konfigurera hur slutanvändare [godkänner ett program i Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felmatchade förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att från intressenter-rollerna i projektet är väl förstå.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Informera användarna om hur och när deras upplevelse kommer att ändras och hur de får support om det behövs.

Även om Mina appar vanligt vis inte skapar användar problem är det viktigt att du förbereder. Skapa guider och en lista över alla resurser för support personalen innan du startar.

#### <a name="communications-templates"></a>Kommunikations mallar

Microsoft tillhandahåller [anpassningsbara mallar för e-post och annan kommunikation](https://aka.ms/APTemplates) för Mina appar. Du kan anpassa dessa till gångar för användning i andra kommunikations kanaler efter behov för din företags kultur.

## <a name="plan-your-sso-configuration"></a>Planera SSO-konfigurationen

När en användare loggar in till ett program, går de igenom en autentiseringsprocess och de måste bevisa vem de är. Utan SSO lagras ett lösen ord i programmet och användaren måste känna till det här lösen ordet. Med SSO skickas användarnas autentiseringsuppgifter till programmet, så de behöver inte ange lösen orden för varje program igen.

SSO måste vara aktiverat för att du ska kunna starta program i Mina appar. Azure AD stöder flera SSO-alternativ. Mer information finns i [alternativ för enkel inloggning i Azure AD](sso-options.md).

> [!NOTE]
> Mer information om hur du använder Azure AD som identitets leverantör för en app finns i [snabb starts serien för program hantering](view-applications-portal.md).

För bästa möjliga upplevelse med sidan Mina appar börjar du med integrering av moln program som är tillgängliga för federerad SSO. Federerad SSO gör det möjligt för användare att ha en enhetlig upplevelse med ett klick i appen och är mer robust i konfigurations kontrollen.

Använd federerad enkel inloggning med Azure AD (OpenID Connect/SAML) när ett program stöder det, i stället för lösenordsbaserad SSO och ADFS.

Mer information om hur du distribuerar och konfigurerar dina SaaS-program finns i [distributions planen SaaS SSO](./plan-sso-deployment.md).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planera för att distribuera webb läsar tillägget Mina appar

När användarna loggar in på lösenordsbaserade SSO-program måste de installera och använda säkra inloggnings tillägg för Mina appar. Tillägget kör ett skript som skickar lösen ordet till programmets inloggnings formulär. Användarna uppmanas att installera tillägget när de först startar det lösenordsbaserade SSO-programmet. Mer information om tillägget finns i den här dokumentationen om [installation av webb läsar tillägget Mina appar]().

Om du måste integrera lösenordsbaserade SSO-program bör du definiera en mekanism för att distribuera tillägget i skala med webbläsare som [stöds](../user-help/my-apps-portal-end-user-access.md). Alternativen är:

* [grupprincip för Internet Explorer]()
* [Configuration Manager för Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
* [Användar driven nedladdning och konfiguration för Chrome, Firefox, Microsoft Edge eller IE](../user-help/my-apps-portal-end-user-access.md)

Användare som inte använder lösenordsbaserade SSO-program drar också nytta av tillägget. De här fördelarna är möjligheten att starta alla appar från Sök fältet, hitta åtkomst till nyligen använda program och att ha en länk till sidan Mina appar.

#### <a name="plan-for-mobile-access"></a>Planera för mobil åtkomst

En webbläsare som skyddas med Intune-principer (Microsoft Edge eller Intune Managed Browser) krävs för att mobila användare ska kunna starta lösenordsbaserade SSO-program. En princip – skyddad webbläsare möjliggör överföring av lösen ordet som sparats för programmet. Microsoft Edge eller Managed Browser innehåller en uppsättning funktioner för webb data skydd. Du kan också använda Microsoft Edge för företags scenarier på iOS-och Android-enheter. Microsoft Edge stöder samma hanterings scenarier som Intune Managed Browser och förbättrar användar upplevelsen. Läs mer: [Hantera webb åtkomst med hjälp av en Microsoft Intune-princip-skyddad webbläsare](/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Planera distributionen av mina appar

Grunden för Mina appar är programmets start Portal, som användarna kommer åt på [https://myapps.microsoft.com](https://myapps.microsoft.com/) . Sidan Mina appar ger användare en enda plats för att starta sitt arbete och få de program som behövs. Här hittar användarna en lista över alla program som de har enkel inloggning till. 

> [!NOTE]
> Samma program visas i Microsoft 365 App-startaren.

Planera i vilken ordning du ska lägga till program i Start programmet för Mina appar och bestäm om du ska lägga ut dem gradvis eller samtidigt. Om du vill göra det skapar du en program inventering som visar typen av autentisering och eventuella befintliga SSO-integrationer för varje program.

#### <a name="add-applications-to-the-my-apps-panel"></a>Lägg till program i panelen Mina appar

Alla Azure AD SSO-aktiverade program kan läggas till i Start programmet för Mina appar. Andra program läggs till med hjälp av det länkade SSO-alternativet. Du kan konfigurera en program panel som länkar till URL: en för ditt befintliga webb program. Med länkad SSO kan du börja dirigera användare till portalen för Mina appar utan att migrera alla program till Azure AD SSO. Du kan gradvis flytta till Azure AD SSO-konfigurerade program utan att störa användarnas upplevelse.

#### <a name="use-my-apps-collections"></a>Använda Mina appar-samlingar

Som standard visas alla program på en enda sida. Men du kan använda samlingar för att gruppera relaterade program och presentera dem på en separat flik, vilket gör dem lättare att hitta. Du kan till exempel använda samlingar för att skapa logiska grupperingar av program för vissa jobb roller, aktiviteter, projekt och så vidare. Mer information finns i [så här använder du Mina appar-samlingar](access-panel-collections.md). 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planera om du ska använda Mina appar eller en befintlig Portal

Dina användare kanske redan har ett program eller en annan portal än mina appar. I så fall, Bestäm om du vill stödja båda portalerna eller bara använda en.

Om en befintlig Portal redan används som utgångs punkt för användare kan du integrera mina apps-funktioner med hjälp av URL: er för användar åtkomst. URL: er för användar åtkomst fungerar som direkt länkar till de program som är tillgängliga i portalen Mina appar. Dessa URL: er kan bäddas in i en befintlig webbplats. När en användare väljer länken öppnas programmet från portalen Mina appar.

Du kan hitta URL-egenskapen för användar åtkomst i avsnittet **Egenskaper** i programmet i Azure Portal.

![En skärm bild av panelen appar](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planera identifiering och åtkomst av självbetjänings program

När en central uppsättning program har distribuerats till en användares Mina appar, bör du aktivera funktioner för hantering av självbetjänings program. Med hjälp av appen för självbetjänings identifiering kan användarna:

* Hitta nya appar som ska läggas till i sina appar. 
* Lägg till valfria appar som de kanske inte vet att de behöver under installationen.

Godkännande arbets flöden är tillgängliga för explicit godkännande för åtkomst till program. Användare som är god kännare får meddelanden i portalen Mina appar när det finns väntande begäran om åtkomst till programmet.

## <a name="plan-self-service-group-membership"></a>Plan för självbetjänings grupp medlemskap 

Du kan göra det möjligt för användare att skapa och hantera egna säkerhets grupper eller Microsoft 365 grupper i Azure AD. Ägare av gruppen kan godkänna eller neka medlemskaps begär Anden och delegera kontrollen av grupp medlemskap. Funktioner för grupp hantering via självbetjäning är inte tillgängliga för e-postaktiverade säkerhets grupper eller distributions listor.

Om du vill planera för grupp medlemskap i självbetjäning kontrollerar du om du ska tillåta alla användare i din organisation att skapa och hantera grupper eller endast en delmängd av användarna. Om du tillåter en delmängd av användarna måste du konfigurera en grupp som dessa personer läggs till i. Mer information om hur du aktiverar de här scenarierna finns i Konfigurera självbetjänings [grupp hantering i Azure Active Directory](../enterprise-users/groups-self-service-management.md) .

## <a name="plan-reporting-and-auditing"></a>Planera rapportering och granskning

Azure AD tillhandahåller [rapporter som ger teknisk och affärs insikter](../reports-monitoring/overview-reports.md). Samar beta med dina affärs-och teknik program ägare för att ta över ägandet av dessa rapporter och använda dem regelbundet. Följande tabell innehåller några exempel på typiska rapporterings scenarier.

| Exempel | Hantera risk| Öka produktiviteten| Styrning och efterlevnad |
|  - |- | - | - |
| Rapporttyper|  Program behörigheter och användning| Konto etablerings aktivitet| Granska vem som har åtkomst till programmen |
| Möjliga åtgärder| Granska åtkomst; återkalla behörigheter| Åtgärda eventuella etablerings fel| Återkalla åtkomst |

Azure AD behåller de flesta gransknings data i 30 dagar. Data är tillgängliga via Azure Admin Portal eller API som du kan ladda ned till analys systemen.

#### <a name="auditing"></a>Granskning

Gransknings loggar för program åtkomst är tillgängliga i 30 dagar. Om säkerhets granskning i företaget kräver längre kvarhållning måste loggarna exporteras till ett SIEM-verktyg (Security information Event and Management), till exempel Splunk eller ArcSight.

För säkerhets kopiering, rapportering och haveri beredskap, dokumentera den nödvändiga hämtnings frekvensen, vad mål systemet är och vem som ansvarar för att hantera varje säkerhets kopia. Du kanske inte behöver separata säkerhets kopior av granskning och rapportering. Din säkerhets kopiering av haveri beredskap bör vara en separat entitet.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Distribuera program till användarnas panel för Mina appar

När ett program har kon figurer ATS för SSO tilldelas grupper åtkomst. Användare i de tilldelade grupperna har åtkomst, och de kommer att se programmet i sina appar och i den Microsoft 365 App-starta.

Se [tilldela användare och grupper till ett program i Active Directory](./assign-user-or-group-access-portal.md).

Om du under testning eller distribution vill lägga till grupperna, men ännu inte tillåta att programmen visas i Mina appar, se [Dölj ett program från användarens upplevelse i Azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-microsoft-365-applications-to-my-apps"></a>Distribuera Microsoft 365 program till Mina appar

För Microsoft 365 program får användarna en kopia av Office baserat på licenser som tilldelats dem. En förutsättning för att få åtkomst till Office-program är att användarna ska tilldelas rätt licenser knutna till Office-programmen. När du tilldelar en licens för en användare ser de automatiskt de program som är associerade med licensen på sidan Mina appar och i Microsoft 365 App Launcher.

Om du vill dölja en uppsättning Office-program från användare finns det ett alternativ för att dölja appar från portalen Mina appar, samtidigt som åtkomsten till den Microsoft 365 portalen tillåts. Läs mer: [Dölj ett program från användarens upplevelse i Azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-application-self-service-capabilities"></a>Distribuera självbetjänings funktioner för program

Med program åtkomst via självbetjäning kan användare själv identifiera och begära åtkomst till program. Användare har frihet att komma åt de appar som de behöver utan att gå igenom en IT-grupp varje gång de begär åtkomst. När en användare begär åtkomst och godkänns, antingen automatiskt eller manuellt av en app-ägare, läggs de till i en grupp på Server delen. Rapportering är aktiverat för vem som har begärt, godkänt eller tagit bort åtkomst och ger dig kontroll över hanteringen av de tilldelade rollerna.

Du kan delegera godkännande av program åtkomst förfrågningar till affärs god kännare. Affärs god kännaren kan ställa in appens åtkomst lösen ord på sidan Mina appar i affärs god kännaren.

Läs mer: [så här använder du självbetjäning för program åtkomst](access-panel-manage-self-service-access.md).

## <a name="validate-your-deployment"></a>Verifiera distributionen

Se till att distributionen av mina appar är grundligt testade och att det finns en återställnings plan på plats.

Följande tester bör utföras med både företagsägda enheter och personliga enheter. Dessa test fall bör även avspegla affärs användnings fall. Följande är några fall baserat på exempel affärs kraven i det här dokumentet och på vanliga tekniska scenarier. Lägg till andra som är speciella för dina behov.

#### <a name="application-sso-access-test-case-examples"></a>Exempel på exempel på SSO-åtkomst för programkompatibilitet:


| Affärs ärende| Förväntat resultat |
| - | -|
| Användaren loggar in på Mina appar-portalen| Användaren kan logga in och se sina program |
| Användaren startar ett externt SSO-program| Användaren loggas in automatiskt till programmet |
| Användaren startar ett SSO-program för första gången| Användaren måste installera tillägget Mina appar |
| Användaren startar ett SSO-program en gång efter| Användaren loggas in automatiskt till programmet |
| Användaren startar en app från Microsoft 365 portal| Användaren loggas in automatiskt till programmet |
| Användaren startar en app från Managed Browser| Användaren loggas in automatiskt till programmet |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Exempel på test fall för program självbetjänings funktioner

| Affärs ärende| Förväntat resultat |
| - | - |
| Användaren kan hantera medlemskap för programmet| Användare kan lägga till/ta bort medlemmar som har åtkomst till appen |
| Användaren kan redigera programmet| Användaren kan redigera programmets beskrivning och autentiseringsuppgifter för SSO-program för lösen ord |

### <a name="rollback-steps"></a>Återställnings steg

Det är viktigt att planera vad som ska göras om distributionen inte fungerar som planerat. Om SSO-konfigurationen Miss lyckas under distributionen måste du förstå hur du [felsöker SSO-problem](../hybrid/tshoot-connect-sso.md) och minskar påverkan på användarna. I extrema fall kan du behöva [återställa SSO](../manage-apps/plan-sso-deployment.md#rollback-process).


## <a name="manage-your-implementation"></a>Hantera din implementering

Använd den minst privilegierade rollen för att utföra en obligatorisk uppgift i Azure Active Directory. [Granska de olika roller som är tillgängliga](../roles/permissions-reference.md) och välj rätt för att lösa dina behov för varje person för det här programmet. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Profiler| Roller| Azure AD-roll  |
| - | -| -|
| Supportavdelningen-administratör| Nivå 1-stöd| Ingen |
| Identitets administratör| Konfigurera och Felsök när problem påverkar Azure AD| Global administratör |
| Program administratör| Användar attestering i program, konfiguration av användare med behörigheter| Ingen |
| Infrastruktur administratörer| Certifikat förnyelse ägare| Global administratör |
| Företags ägare/från intressenter| Användar attestering i program, konfiguration av användare med behörigheter| Ingen |

Du kan använda [Privileged Identity Management](../privileged-identity-management/pim-configure.md) för att hantera roller för att ge ytterligare gransknings-, kontroll-och åtkomst granskning för användare med katalog behörigheter.

## <a name="next-steps"></a>Nästa steg
[Planera en distribution av Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
