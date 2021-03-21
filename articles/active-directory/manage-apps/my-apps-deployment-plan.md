---
title: Planera Azure Active Directory konfiguration av mina appar
description: Planerings guide för att effektivt använda Mina appar i din organisation.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: f63a8fd05e1a6ed5e41eeb64aa852ff01db295af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645475"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Planera Azure Active Directory konfiguration av mina appar

> [!NOTE]
> Den här artikeln är avsedd för IT-proffs som behöver planera konfigurationen av organisationens min Apps-Portal. 
>
> **Dokumentation om slutanvändare finns i [Logga in och starta appar från portalen Mina appar](../user-help/my-apps-portal-end-user-access.md)**.

Azure Active Directory (Azure AD) Mina appar är en webbaserad portal för att starta och hantera appar. Sidan Mina appar ger användare en enda plats för att starta sitt arbete och hitta alla program som de har åtkomst till. Användare får åtkomst till Mina appar på [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Varför ska jag konfigurera mina appar

Portalen Mina appar är tillgänglig för användare som standard och kan inte stängas av. Det är viktigt att konfigurera det så att de har bästa möjliga upplevelse och portalen är användbar. 

Alla program i listan Azure Active Directory företags program visas när båda följande villkor uppfylls:

* Egenskapen visibility för appen har angetts till true. 

* Appen tilldelas till alla användare eller grupper. Den visas för tilldelade användare.

Att konfigurera portalen säkerställer att rätt personer lätt kan hitta rätt appar.

 
### <a name="how-is-the-my-apps-portal-used"></a>Hur används portalen Mina appar?

Användare får åtkomst till min Apps-Portal för att:

* Identifiera och få åtkomst till alla deras organisations Azure AD-anslutna program som de har åtkomst till.

   * Det är bäst att se till att appar är konfigurerade för enkel inloggning (SSO) så att användarna får bästa möjliga upplevelse.

* Begär åtkomst till nya appar som är konfigurerade för självbetjäning.

* Skapa personliga samlingar av appar.

* Hantera åtkomst till appar för andra när du har tilldelat rollen grupp ägare eller delegerad kontroll för gruppen som används för att bevilja åtkomst till program.

Administratörer kan konfigurera:

* [Medgivande upplevelser](../manage-apps/configure-user-consent.md)  , inklusive användnings villkor.

* [Program identifierings-och åtkomst begär Anden via självbetjäning](../manage-apps/access-panel-manage-self-service-access.md).

* [Program samlingar](../manage-apps/access-panel-collections.md).

* Tilldelning av ikoner till program

* Användarvänliga namn för program

* Företags anpassning som visas i Mina appar

 

## <a name="plan-consent-configuration"></a>Planera medgivande konfiguration

Det finns två typer av medgivande: användar medgivande och medgivande för appar som har åtkomst till data.

![Skärm bild som visar medgivande konfigurationen](./media/my-apps-deployment-plan/my-apps-consent.png)

### <a name="user-consent-for-applications"></a>Användar tillstånd för program 

Användare eller administratörer måste godkänna användnings villkoren och sekretess policyn för ett program. Du måste bestämma om användare eller endast administratörer ska kunna godkänna program. **Om dina affärs regler tillåter det, rekommenderar vi att du använder administratörs medgivande för att behålla kontrollen över programmen i din klient organisation**.

Om du vill använda administratörs medgivande måste du vara global administratör för organisationen och programmen måste vara antingen:

* Registrerad i din organisation.

* Registreras i en annan Azure AD-organisation och har tidigare godkänts av minst en användare.

Om du vill tillåta användare att godkänna måste du bestämma om du vill att de ska godkänna en app eller bara under särskilda omständigheter.

Mer information finns i Konfigurera hur slutanvändare [godkänner ett program i Azure Active Directory.](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Grupp ägarens medgivande för appar som har åtkomst till data

Ta reda på om ägare av Azure AD-säkerhetsgrupper eller M365-grupper kan godkänna program för att få åtkomst till data för de grupper som de äger. Du kan inte tillåta, tillåta alla grupp ägare eller bara tillåta en delmängd av grupp ägarna.

Mer information finns i [Konfigurera grupp medgivande behörigheter](../manage-apps/configure-user-consent-groups.md).

Konfigurera sedan inställningarna för [godkännande av användar-och grupp ägare](https://portal.azure.com/) i Azure Portal.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Informera användarna om hur och när deras upplevelse kommer att ändras och hur de får support om det behövs.

Även om Mina appar vanligt vis inte skapar användar problem är det viktigt att du förbereder. Skapa guider och en lista över alla resurser för support personalen innan du startar.

#### <a name="communications-templates"></a>Kommunikations mallar

Microsoft tillhandahåller [anpassningsbara mallar för e-post och annan kommunikation](https://aka.ms/APTemplates) för Mina appar. Du kan anpassa dessa till gångar för användning i andra kommunikations kanaler efter behov för din företags kultur.

 

## <a name="plan-your-sso-configuration"></a>Planera SSO-konfigurationen

Det är bäst om SSO är aktiverat för alla appar i portalen Mina appar, så att användarna får en sömlös upplevelse utan att behöva ange sina autentiseringsuppgifter.

Azure AD stöder flera SSO-alternativ. 

* Mer information finns i [alternativ för enkel inloggning i Azure AD](sso-options.md).

* Mer information om hur du använder Azure AD som identitets leverantör för en app finns i [snabb starts serien för program hantering](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Använd federerad enkel inloggning om möjligt

För den bästa användar upplevelsen med sidan Mina appar börjar du med integrering av moln program som är tillgängliga för federerad enkel inloggning (OpenID Connect eller SAML). Federerad SSO gör det möjligt för användare att ha en enhetlig upplevelse med ett klick i en app som startar ytor och som är mer robust i konfigurations kontrollen.

Mer information om hur du konfigurerar SaaS-program (program vara som en tjänst) för enkel inloggning finns i [SaaS SSO Deployment plan]. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Att tänka på vid särskilda SSO-omständigheter

> [!TIP]
> För en bättre användar upplevelse använder du federerad enkel inloggning med Azure AD (OpenID Connect/SAML) när ett program stöder det, i stället för lösenordsbaserad SSO och ADFS.

Om du vill logga in på lösenordsbaserade SSO-program eller till program som används av Azure AD-programproxy måste användarna installera och använda säkra inloggnings tillägg för Mina appar. Användarna uppmanas att installera tillägget när de först startar det lösenordsbaserade SSO-eller Application Proxy-programmet. 

![Skärm bild av](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Detaljerad information om tillägget finns i avsnittet [Installera webb läsar tillägg för Mina appar](../user-help/my-apps-portal-end-user-access.md).

Om du måste integrera dessa program bör du definiera en mekanism för att distribuera tillägget i skala med [webbläsare som stöds](../user-help/my-apps-portal-end-user-access.md). Alternativen är:

* [Användar driven nedladdning och konfiguration för Chrome, Firefox, Microsoft Edge eller IE](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager för Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

Tillägget gör att användarna kan starta vilken app som helst från Sök fältet, hitta åtkomst till nyligen använda program och ha en länk till sidan Mina appar.

![Skärm bild av tillägget Mina appar](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Planera för mobil åtkomst

För program som använder lösenordsbaserad SSO eller nås med hjälp av [Microsoft Azure AD Application Proxy](../manage-apps/application-proxy.md)måste du använda Microsoft Edge Mobile. För andra program kan du använda en mobil webbläsare. 

### <a name="linked-sso"></a>Länkad SSO

Du kan lägga till program med hjälp av det länkade SSO-alternativet. Du kan konfigurera en program panel som länkar till URL: en för ditt befintliga webb program. Med länkad SSO kan du börja dirigera användare till portalen för Mina appar utan att migrera alla program till Azure AD SSO. Du kan gradvis flytta till Azure AD SSO-konfigurerade program utan att störa användarnas upplevelse.

## <a name="plan-the-user-experience"></a>Planera användar upplevelsen

Som standard visas alla program som användaren har åtkomst till och alla program som kon figurer ATS för självbetjänings identifiering i användarens panel för Mina appar. För många organisationer kan detta vara en mycket stor lista, som kan bli burdensome om de inte organiseras

### <a name="plan-my-apps-collections"></a>Planera samlingar för Mina appar

Varje Azure AD-program som en användare har åtkomst till visas i Mina appar i samlingen Alla appar. Använd samlingar för att gruppera relaterade program och presentera dem på en separat flik, så att de blir enklare att hitta. Du kan till exempel använda samlingar för att skapa logiska grupperingar av program för vissa jobb roller, aktiviteter, projekt och så vidare. 

Slutanvändare kan också anpassa sina erfarenheter genom att

* Skapa egna app-samlingar.

* [Dölja och ordna om program samlingar](access-panel-collections.md).

![Skärm bild av självbetjänings konfiguration](./media/my-apps-deployment-plan/collections.png)

Det finns ett alternativ för att dölja appar från Mina appar-portalen, samtidigt som du tillåter åtkomst från andra platser, till exempel Microsoft 365 portalen. Läs mer: [Dölj ett program från användarens upplevelse i Azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Endast 950 appar som en användare har åtkomst till kan nås via Mina appar. Detta inkluderar appar som döljs av antingen användaren eller administratören. 

### <a name="plan-self-service-group-management-membership"></a>Planera medlemskap i självbetjänings grupp hantering

Du kan göra det möjligt för användare att skapa och hantera egna säkerhets grupper eller Microsoft 365 grupper i Azure AD. Ägare av gruppen kan godkänna eller neka medlemskaps begär Anden och delegera kontrollen av grupp medlemskap. Funktioner för grupp hantering via självbetjäning är inte tillgängliga för e-postaktiverade säkerhets grupper eller distributions listor.

Om du vill planera för grupp medlemskap i självbetjäning kontrollerar du om du ska tillåta alla användare i din organisation att skapa och hantera grupper eller endast en delmängd av användarna. Om du tillåter en delmängd av användarna måste du konfigurera en grupp som dessa personer läggs till i. 

Mer information om hur du aktiverar de här scenarierna finns i Konfigurera självbetjänings [grupp hantering i Azure Active Directory](../enterprise-users/groups-self-service-management.md) .

### <a name="plan-self-service-application-access"></a>Planera åtkomst till självbetjänings program

Du kan göra det möjligt för användare att upptäcka och begära åtkomst till program via panelen Mina appar. För att göra det måste du först 

* Aktivera grupp hantering via självbetjäning

* Aktivera app för SSO

* skapa en grupp för program åtkomst

![Skärm bild som visar självbetjänings konfigurationen för Mina appar](./media/my-apps-deployment-plan/my-apps-self-service.png)

När användarna begär åtkomst begär de åtkomst till den underliggande gruppen och grupp ägare kan delegera behörighet att hantera grupp medlemskapet och därmed program åtkomst. Godkännande arbets flöden är tillgängliga för explicit godkännande för åtkomst till program. Användare som är god kännare får meddelanden i portalen Mina appar när det finns väntande begäran om åtkomst till programmet.

## <a name="plan-reporting-and-auditing"></a>Planera rapportering och granskning

Azure AD tillhandahåller [-rapporter som tillhandahåller tekniska och affärs insikter]. /reports-monitoring/overview-reports.md). Samar beta med dina affärs-och teknik program ägare för att ta över ägandet av dessa rapporter och använda dem regelbundet. Följande tabell innehåller några exempel på typiska rapporterings scenarier.

| Exempel| Hantera risk| Öka produktiviteten| Styrning och efterlevnad |
| - | - | - | -|
| Rapporttyper| Program behörigheter och användning| Konto etablerings aktivitet| Granska vem som har åtkomst till programmen |
| Möjliga åtgärder| Granska åtkomst; återkalla behörigheter| Åtgärda eventuella etablerings fel| Återkalla åtkomst |


Azure AD behåller de flesta gransknings data i 30 dagar. Data är tillgängliga via Azure Admin Portal eller API som du kan ladda ned till analys systemen.

#### <a name="auditing"></a>Granskning

Gransknings loggar för program åtkomst är tillgängliga i 30 dagar. Om din organisation kräver längre kvarhållning exporterar du loggarna till ett SIEM-verktyg (Security information Event and Management), till exempel Splunk eller ArcSight.

För säkerhets kopiering, rapportering och haveri beredskap, dokumentera den nödvändiga hämtnings frekvensen, vad mål systemet är och vem som ansvarar för att hantera varje säkerhets kopia. Du kanske inte behöver separata säkerhets kopior av granskning och rapportering. Din säkerhets kopiering av haveri beredskap bör vara en separat entitet.

## <a name="validate-your-deployment"></a>Verifiera distributionen

Se till att distributionen av mina appar är grundligt testade och att det finns en återställnings plan på plats.

Utför följande tester med både företagsägda enheter och personliga enheter. Dessa test fall bör även avspegla affärs användnings fall. Följande är några fall som baseras på vanliga tekniska scenarier. Lägg till andra som är speciella för dina behov.

#### <a name="application-sso-access-test-case-examples"></a>Exempel på exempel på SSO-åtkomst för programkompatibilitet:


| Affärs ärende| Förväntat resultat |
| - | - |
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

Det är viktigt att planera vad som ska göras om distributionen inte fungerar som planerat. Om SSO-konfigurationen Miss lyckas under distributionen måste du förstå hur du [felsöker SSO-problem](../hybrid/tshoot-connect-sso.md) och minskar påverkan på användarna. I extrema fall kan du behöva [återställa SSO](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>Hantera din implementering

Använd den minst privilegierade rollen för att utföra en obligatorisk uppgift i Azure Active Directory. [Granska de olika roller som är tillgängliga](../roles/permissions-reference.md) och välj rätt för att lösa dina behov för varje person för det här programmet. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Profiler| Roller| Azure AD-roll |
| - | - | - |
| Supportavdelningen-administratör| Nivå 1-stöd| Inget |
| Identitets administratör| Konfigurera och Felsök när problem påverkar Azure AD| Global administratör |
| Program administratör| Användar attestering i program, konfiguration av användare med behörigheter| Inget |
| Infrastruktur administratörer| Certifikat förnyelse ägare| Global administratör |
| Företags ägare/från intressenter| Användar attestering i program, konfiguration av användare med behörigheter| Inget |


Du kan använda [Privileged Identity Management](../privileged-identity-management/pim-configure.md) för att hantera roller för att ge ytterligare gransknings-, kontroll-och åtkomst granskning för användare med katalog behörigheter.

## <a name="next-steps"></a>Nästa steg

[Planera en distribution av Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Planera en distribution av programproxy](application-proxy-deployment-plan.md)

