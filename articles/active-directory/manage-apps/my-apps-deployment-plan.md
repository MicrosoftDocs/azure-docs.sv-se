---
title: Planera Azure Active Directory Mina appar konfiguration
description: Planeringsguide för att effektivt Mina appar i din organisation.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: baselden
ms.openlocfilehash: 777daecc119a158f11d865489e4eb497c3bc7899
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376604"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Planera Azure Active Directory Mina appar konfiguration

> [!NOTE]
> Den här artikeln är utformad för IT-proffs som behöver planera konfigurationen av organisationens Mina appar portalen. 
>
> **Slutanvändardokumentation finns [i Logga in och starta appar från Mina appar portalen.](../user-help/my-apps-portal-end-user-access.md)**

Azure Active Directory (Azure AD) Mina appar är en webbaserad portal för att starta och hantera appar. Sidan Mina appar ger användarna en enda plats där de kan börja sitt arbete och hitta alla program som de har åtkomst till. Användare kommer Mina appar på [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Varför konfigurera Mina appar

Portalen Mina appar tillgänglig för användare som standard och kan inte stängas av. Det är viktigt att konfigurera den så att de får bästa möjliga upplevelse och att portalen förblir användbar. 

Alla program i Azure Active Directory lista över företagsprogram visas när båda följande villkor är uppfyllda:

* Synlighetsegenskapen för appen har angetts till true. 

* Appen tilldelas till valfri användare eller grupp. Den visas för tilldelade användare.

Genom att konfigurera portalen ser du till att rätt personer enkelt kan hitta rätt appar.

 
### <a name="how-is-the-my-apps-portal-used"></a>Hur används Mina appar portalen?

Användare kommer åt Mina appar portalen för att:

* Identifiera och få åtkomst till alla organisationens Azure AD-anslutna program som de har åtkomst till.

   * Det är bäst att se till att apparna är konfigurerade för enkel inloggning (SSO) för att ge användarna bästa möjliga upplevelse.

* Begär åtkomst till nya appar som har konfigurerats för självbetjäning.

* Skapa personliga samlingar av appar.

* Hantera åtkomst till appar för andra när de har tilldelats rollen som gruppägare eller delegerad kontroll för den grupp som används för att bevilja åtkomst till programmen.

Administratörer kan konfigurera:

* [Medgivandeupplevelser,](../manage-apps/configure-user-consent.md)  inklusive användningsvillkor.

* [Identifiering av självbetjäningsprogram och åtkomstbegäranden](../manage-apps/access-panel-manage-self-service-access.md).

* [Samlingar med program](../manage-apps/access-panel-collections.md).

* Tilldelning av ikoner till program

* Användarvänliga namn för program

* Företags varumärke som visas på Mina appar

 

## <a name="plan-consent-configuration"></a>Planera konfiguration av medgivande

### <a name="user-consent-for-applications"></a>Användarmedgivande för program 

Innan en användare kan logga in på ett program och programmet kan komma åt organisationens data måste en användare eller en administratör bevilja programbehörigheterna. Du kan konfigurera om användarmedgivande tillåts och under vilka villkor. **Microsoft rekommenderar att du endast tillåter användarmedgivande för program från verifierade utgivare.**

Mer information finns i [Konfigurera hur slutanvändare godkänner program](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Medgivande från gruppägare för appar som har åtkomst till data

Grupp- och teamägare kan ge program, till exempel program som publicerats av tredjepartsleverantörer, åtkomst till organisationens data som är associerade med en grupp. Mer [information finns i Resursspecifikt medgivande i Microsoft Teams.](https://docs.microsoft.com/microsoftteams/resource-specific-consent) 

Du kan konfigurera om du vill tillåta eller inaktivera den här funktionen.

Mer information finns i Konfigurera [behörigheter för gruppmedgivande.](../manage-apps/configure-user-consent-groups.md)

### <a name="plan-communications"></a>Planera kommunikation

Kommunikation är avgörande för att en ny tjänst ska lyckas. Informera användarna proaktivt om hur och när deras upplevelse kommer att ändras och hur de kan få support om det behövs.

Även Mina appar vanligtvis inte skapar användarproblem, är det viktigt att förbereda. Skapa guider och en lista över alla resurser för supportpersonal innan du startar.

#### <a name="communications-templates"></a>Kommunikationsmallar

Microsoft tillhandahåller [anpassningsbara mallar för e-post och annan kommunikation](https://aka.ms/APTemplates) för Mina appar. Du kan anpassa dessa tillgångar för användning i andra kommunikationskanaler efter behov för din företagskultur.

 

## <a name="plan-your-sso-configuration"></a>Planera konfigurationen av enkel inloggning

Det är bäst om enkel inloggning är aktiverat för alla appar i Mina appar-portalen så att användarna får en sömlös upplevelse utan att de behöver ange sina autentiseringsuppgifter.

Azure AD stöder flera alternativ för enkel inloggning. 

* Mer information finns i [Alternativ för enkel inloggning i Azure AD.](sso-options.md)

* Mer information om hur du använder Azure AD som identitetsprovider för en app finns i [Snabbstartsserie om programhantering.](../manage-apps/view-applications-portal.md)

### <a name="use-federated-sso-if-possible"></a>Använd federerad enkel inloggning om det är möjligt

För bästa användarupplevelse med Mina appar börjar du med integreringen av molnprogram som är tillgängliga för federerad enkel inloggning (OpenID Connect eller SAML). Med federerad enkel inloggning kan användarna få en konsekvent upplevelse med ett klick på appens startytor och tenderar att vara mer robusta i konfigurationskontrollen.

Mer information om hur du konfigurerar saaS-program (programvara som en tjänst) för enkel inloggning finns i [SaaS SSO-distributionsplan].. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Överväganden för särskilda SSO-omständigheter

> [!TIP]
> För en bättre användarupplevelse kan du använda Federerad enkel inloggning med Azure AD (OpenID Connect/SAML) när ett program stöder det, i stället för lösenordsbaserad enkel inloggning och ADFS.

För att logga in på lösenordsbaserade SSO-program eller till program som nås av Azure AD Programproxy måste användarna installera och använda Mina appar-tillägget för säker inloggning. Användarna uppmanas att installera tillägget första gången de startar lösenordsbaserad enkel inloggning eller Programproxy program. 

![Skärmbild av](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Detaljerad information om tillägget finns i Installera [Mina appar webbläsartillägget](../user-help/my-apps-portal-end-user-access.md).

Om du måste integrera dessa program bör du definiera en mekanism för att distribuera tillägget i stor skala med [webbläsare som stöds.](../user-help/my-apps-portal-end-user-access.md) Alternativen är:

* [Användardriven nedladdning och konfiguration för Chrome, Firefox, Microsoft Edge eller IE](../user-help/my-apps-portal-end-user-access.md)

* [Konfigurationshanteraren för Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

Tillägget gör att användare kan starta alla appar från sökfältet, söka efter åtkomst till nyligen använda program och ha en länk till Mina appar sidan.

![Skärmbild av sökning efter tillägg för mina appar](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Planera för mobil åtkomst

För program som använder lösenordsbaserad enkel inloggning eller som nås [med hjälp Microsoft Azure AD Programproxy](../manage-apps/application-proxy.md)måste du använda Microsoft Edge mobile. För andra program kan alla mobila webbläsare användas. 

### <a name="linked-sso"></a>Länkad enkel inloggning

Program kan läggas till med alternativet Länkad enkel inloggning. Du kan konfigurera en programpanel som länkar till URL:en för ditt befintliga webbprogram. Med länkad enkel inloggning kan du börja dirigera användare till Mina appar-portalen utan att migrera alla program till Azure AD SSO. Du kan gradvis gå över till Azure AD SSO-konfigurerade program utan att störa användarnas upplevelse.

## <a name="plan-the-user-experience"></a>Planera användarupplevelsen

Som standard visas alla program som användaren har åtkomst till och alla program som har konfigurerats för självbetjäning på användarens Mina appar panelen. För många organisationer kan detta vara en mycket stor lista, vilket kan bli besvärligt om det inte organiseras

### <a name="plan-my-apps-collections"></a>Planera Mina appar samlingar

Alla Azure AD-program som en användare har åtkomst till visas Mina appar i samlingen Alla appar. Använd samlingar för att gruppera relaterade program och presentera dem på en separat flik, vilket gör dem lättare att hitta. Du kan till exempel använda samlingar för att skapa logiska grupper av program för specifika jobbroller, aktiviteter, projekt och så vidare. 

Slutanvändarna kan också anpassa sin upplevelse genom att

* Skapa egna appsamlingar.

* [Dölja och ordna om appsamlingar](access-panel-collections.md).

![Skärmbild av självbetjäningskonfiguration](./media/my-apps-deployment-plan/collections.png)

Det finns ett alternativ för att dölja appar från Mina appar-portalen och samtidigt tillåta åtkomst från andra platser, till exempel Microsoft 365 portalen. Läs mer: [Dölj ett program från användarens upplevelse i Azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Endast 950 appar som en användare har åtkomst till kan nås via Mina appar. Detta inkluderar appar som är dolda av antingen användaren eller administratören. 

### <a name="plan-self-service-group-management-membership"></a>Planera grupphanteringsmedlemskap med självbetjäning

Du kan låta användare skapa och hantera sina egna säkerhetsgrupper eller Microsoft 365 i Azure AD. Gruppens ägare kan godkänna eller neka medlemskapsbegäranden och delegera kontroll över gruppmedlemskap. Funktioner för grupphantering via självbetjäning är inte tillgängliga för e-postaktiverade säkerhetsgrupper eller distributionslistor.

Om du vill planera för gruppmedlemskap med självbetjäning avgör du om du tillåter att alla användare i din organisation skapar och hanterar grupper eller bara en delmängd av användarna. Om du tillåter en delmängd av användarna måste du konfigurera en grupp som dessa personer läggs till i. 

Se [Konfigurera grupphantering med självbetjäning i Azure Active Directory](../enterprise-users/groups-self-service-management.md) mer information om hur du aktiverar dessa scenarier.

### <a name="plan-self-service-application-access"></a>Planera programåtkomst via självbetjäning

Du kan låta användare identifiera och begära åtkomst till program via Mina appar panelen. Om du vill göra det måste du först 

* aktivera grupphantering med självbetjäning

* aktivera app för enkel inloggning

* skapa en grupp för programåtkomst

![Skärmbild av konfiguration Mina appar självbetjäning](./media/my-apps-deployment-plan/my-apps-self-service.png)

När användare begär åtkomst begär de åtkomst till den underliggande gruppen och gruppägare kan delegera behörighet att hantera gruppmedlemskapet och därmed programåtkomst. Arbetsflöden för godkännande är tillgängliga för explicit godkännande för att få åtkomst till program. Användare som är godkännare får meddelanden i Mina appar portalen när det finns en väntande begäran om åtkomst till programmet.

## <a name="plan-reporting-and-auditing"></a>Planera rapportering och granskning

Azure AD tillhandahåller [rapporter som erbjuder tekniska insikter och affärsinsikter].. /reports-monitoring/overview-reports.md). Arbeta med företagets och tekniska programägare för att ta över ägarskapet för dessa rapporter och använda dem regelbundet. Följande tabell innehåller några exempel på vanliga rapporteringsscenarier.

| Exempel| Hantera risk| Öka produktiviteten| Styrning och efterlevnad |
| - | - | - | -|
| Rapporttyper| Programbehörigheter och användning| Kontoetableringsaktivitet| Granska vem som har åtkomst till programmen |
| Potentiella åtgärder| Granska åtkomst; återkalla behörigheter| Åtgärda eventuella etableringsfel| Återkalla åtkomst |


Azure AD behåller de flesta granskningsdata i 30 dagar. Data är tillgängliga via Azure-administratörsportalen eller API:et som du kan ladda ned till dina analyssystem.

#### <a name="auditing"></a>Granskning

Granskningsloggar för programåtkomst är tillgängliga i 30 dagar. Om din organisation behöver längre kvarhållning exporterar du loggarna till ett SIEM-verktyg (Security Information Event and Management), till exempel Splunk eller ArcSight.

För gransknings-, rapporterings- och haveriberedskapssäkerhetskopior dokumenterar du den nedladdningsfrekvens som krävs, vad målsystemet är och vem som ansvarar för att hantera varje säkerhetskopia. Du kanske inte behöver separata gransknings- och rapporteringssäkerhetskopior. Din säkerhetskopiering av haveriberedskap ska vara en separat entitet.

## <a name="validate-your-deployment"></a>Verifiera distributionen

Se till Mina appar distributionen testas noggrant och att en återställningsplan finns på plats.

Utför följande tester med både företagsägda enheter och personliga enheter. Dessa testfall bör också återspegla dina affärsanvändningsfall. Nedan följer några fall som baseras på typiska tekniska scenarier. Lägg till andra som är specifika för dina behov.

#### <a name="application-sso-access-test-case-examples"></a>Exempel på åtkomsttestfall för enkel inloggning för program:


| Affärsfall| Förväntat resultat |
| - | - |
| Användaren loggar in på Mina appar portalen| Användaren kan logga in och se sina program |
| Användaren startar ett federerat SSO-program| Användaren loggas in automatiskt i programmet |
| Användaren startar ett lösenordsprogram för enkel inloggning för första gången| Användaren måste installera Mina appar tillägget |
| Användaren startar ett lösenords-SSO-program en efterföljande gång| Användaren loggas in automatiskt i programmet |
| Användaren startar en app från Microsoft 365 portalen| Användaren loggas in automatiskt i programmet |
| Användaren startar en app från Managed Browser| Användaren loggas in automatiskt i programmet |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Exempel på testfall för program med självbetjäningsfunktioner


| Affärsfall| Förväntat resultat |
| - | - |
| Användaren kan hantera medlemskap i programmet| Användaren kan lägga till/ta bort medlemmar som har åtkomst till appen |
| Användaren kan redigera programmet| Användaren kan redigera programmets beskrivning och autentiseringsuppgifter för lösenord för SSO-program |


### <a name="rollback-steps"></a>Återställningssteg

Det är viktigt att planera vad du ska göra om distributionen inte går som planerat. Om konfigurationen av enkel inloggning misslyckas under distributionen måste du förstå hur du [felsöker problem med enkel](../hybrid/tshoot-connect-sso.md) inloggning och minskar påverkan på dina användare. I extrema fall kan du behöva [återställa enkel inloggning.](plan-sso-deployment.md)

## <a name="manage-your-implementation"></a>Hantera implementeringen

Använd den minst privilegierade rollen för att utföra en obligatorisk uppgift inom Azure Active Directory. [Granska de olika roller som är tillgängliga](../roles/permissions-reference.md) och välj rätt för att lösa dina behov för varje person för det här programmet. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Profiler| Roller| Azure AD-roll |
| - | - | - |
| Supportadministratör| Nivå 1-support| Inget |
| Identitetsadministratör| Konfigurera och felsöka när problem påverkar Azure AD| Global administratör |
| Programadministratör| Atterstation för användare i program, konfiguration av användare med behörighet| Inget |
| Infrastrukturadministratörer| Cert rollover owner (Ägare av certifikatsdeover)| Global administratör |
| Företagsägare/intressent| Atterstation för användare i program, konfiguration av användare med behörighet| Inget |


Du kan använda [Privileged Identity Management för](../privileged-identity-management/pim-configure.md) att hantera dina roller för att ge ytterligare granskning, kontroll och åtkomstgranskning för användare med katalogbehörigheter.

## <a name="next-steps"></a>Nästa steg

[Planera en distribution av Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Planera en distribution av programproxy](application-proxy-deployment-plan.md)

