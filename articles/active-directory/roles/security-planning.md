---
title: Säkra åtkomst metoder för administratörer i Azure AD | Microsoft Docs
description: Se till att organisationens administrativa åtkomst-och administratörs konton är säkra. För system arkitekter och IT-proffs som konfigurerar Azure AD, Azure och Microsoft Online Services.
services: active-directory
keywords: ''
author: rolyon
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f2fe29ede2bf0f92049d1ae82bae87326057a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594286"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD

Säkerheten för företags till gångar beror på integriteten hos de privilegierade konton som administrerar dina IT-system. Cyberhot-angripare använder sig av stöld av autentiseringsuppgifter för att rikta in dig på administratörs konton och annan privilegie rad åtkomst för att försöka få åtkomst till känsliga data.

För moln tjänster är förebyggande och svar gemensamma ansvar för moln tjänst leverantören och kunden. Mer information om de senaste hoten till slut punkter och molnet finns i [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/security/operations/security-intelligence-report). Den här artikeln hjälper dig att utveckla en översikt över hur du kan stänga luckorna mellan dina aktuella planer och de rikt linjer som beskrivs här.

> [!NOTE]
> Microsoft strävar efter att ha den högsta nivån av förtroende, transparens, standard avvikelse och regelefterlevnad. Lär dig mer om hur Microsoft Global incident Response-teamet minimerar effekterna av attacker mot moln tjänster och hur säkerheten är inbyggd i Microsofts affärs produkter och moln tjänster på [Microsoft Trust Center – säkerhet](https://www.microsoft.com/trustcenter/security) och Microsofts efterlevnadsprinciper på [Microsoft Trust Center – efterlevnad](https://www.microsoft.com/trustcenter/compliance).

Traditionellt var organisations säkerheten fokuserad på ingångs-och utgångs punkter i ett nätverk som säkerhets perimeter. Men SaaS appar och personliga enheter på Internet har gjort den här metoden mindre effektiv. I Azure AD ersätter vi nätverkets säkerhets perimeter med autentisering i organisationens identitets lager, med användare som tilldelats privilegierade administrativa roller i kontrollen. Deras åtkomst måste skyddas, oavsett om miljön är lokal, moln eller hybrid.

För att skydda privilegie rad åtkomst krävs ändringar av:

* Processer, administrativa metoder och kunskaps hantering
* Tekniska komponenter, till exempel värd skydd, konto skydd och identitets hantering

Skydda privilegie rad åtkomst på ett sätt som hanteras och rapporteras i de Microsoft-tjänster du bryr dig om. Om du har lokala administratörs konton kan du läsa mer i rikt linjerna för lokal och hybrid privilegie rad åtkomst i Active Directory för att [skydda privilegie rad åtkomst](/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> Vägledningen i den här artikeln handlar främst om funktioner i Azure Active Directory som ingår i Azure Active Directory Premium planerna P1 och P2. Azure Active Directory Premium P2 ingår i EMS E5 Suite och Microsoft 365 E5 Suite. Den här vägledningen förutsätter att din organisation redan har Azure AD Premium P2-licenser som har köpts för dina användare. Om du inte har dessa licenser kanske en del av vägledningen inte gäller för din organisation. Den globala administratören i den här artikeln innebär också samma sak som "företags administratör" eller "innehavaradministratör".

## <a name="develop-a-roadmap"></a>Utveckla en översikt

Microsoft rekommenderar att du utvecklar och följer en översikt för att skydda privilegie rad åtkomst mot cyberhot-attacker. Du kan alltid justera din översikt så att den passar dina befintliga funktioner och särskilda krav i din organisation. Varje steg i översikten bör öka kostnaderna och svårigheten för angripare att attackera privilegie rad åtkomst för dina lokala, molnbaserade och hybrid till gångar. Microsoft rekommenderar följande fyra översikts steg. Schemalägg de mest effektiva och snabbaste implementeringarna först. Den här artikeln kan vara din guide, baserat på Microsofts upplevelser med cyberhot-attackens incident-och svars implementering. Tids linjerna för den här översikten är ungefärliger.

![Faser i översikten med tids rader](./media/security-planning/roadmap-timeline.png)

* Steg 1 (24-48 timmar): kritiska objekt som vi rekommenderar att du gör omedelbart

* Steg 2 (2-4 veckor): minimera de oftast använda angrepps metoderna

* Steg 3 (1-3 månader): utveckla synlighet och bygga fullständig kontroll över administratörs aktivitet

* Steg 4 (sex månader och senare): Fortsätt att skapa försvar för ytterligare härdning av din säkerhets plattform

Det här översikts ramverket är utformat för att maximera användningen av Microsofts tekniker som du kanske redan har distribuerat. Överväg att binda in till alla säkerhets verktyg från andra leverantörer som du redan har distribuerat eller som överväger att distribuera.

## <a name="stage-1-critical-items-to-do-right-now"></a>Steg 1: kritiska objekt att göra just nu

![Steg 1 viktiga objekt att göra först](./media/security-planning/stage-one.png)

Steg 1 i översikten fokuserar på kritiska uppgifter som är snabba och enkla att implementera. Vi rekommenderar att du gör dessa objekt direkt inom de första 24-48 timmarna för att säkerställa en grundläggande nivå av säker privilegie rad åtkomst. Det här steget i den skyddade översikten över privilegie rad åtkomst innehåller följande åtgärder:

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Aktivera Azure AD Privileged Identity Management

Vi rekommenderar att du aktiverar Azure AD Privileged Identity Management (PIM) i din Azure AD-produktions miljö. När du har aktiverat PIM får du e-postaviseringar för privilegie rad åtkomst roll ändringar. Meddelanden ger tidig varning när ytterligare användare läggs till i privilegierade roller.

Azure AD Privileged Identity Management ingår i Azure AD Premium P2 eller EMS E5. För att hjälpa dig att skydda åtkomsten till program och resurser lokalt och i molnet registrerar du dig för den [Enterprise Mobility + Security kostnads fria 90-dagars utvärderings versionen](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Azure AD Privileged Identity Management och Azure AD Identity Protection övervaka säkerhets aktivitet med hjälp av Azure AD repor ting, granskning och aviseringar.

När du har aktiverat Azure AD Privileged Identity Management:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som är en global administratör för din Azure AD-produktions organisation.

2. Om du vill välja den Azure AD-organisation där du vill använda Privileged Identity Management väljer du ditt användar namn i det övre högra hörnet av Azure Portal.

3. På Azure Portal-menyn väljer du **alla tjänster** och filtrerar listan för **Azure AD Privileged Identity Management**.

4. Öppna Privileged Identity Management från listan **alla tjänster** och fäst den på din instrument panel.

Se till att den första personen som använder PIM i din organisation är tilldelad rollen **säkerhets administratör** och **administratör för privilegie rad roll** . Endast privilegierade roll administratörer kan hantera roll tilldelningar för Azure AD-katalog för användare. Säkerhets guiden i PIM vägleder dig genom den inledande identifierings-och tilldelnings upplevelsen. Du kan avsluta guiden utan att göra några ytterligare ändringar för tillfället.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifiera och kategorisera konton som har privilegier för hög privilegier

När du har aktiverat Azure AD Privileged Identity Management kan du Visa de användare som finns i följande Azure AD-roller:

* Global administratör
* Privilegie rad roll administratör
* Exchange-administratör
* SharePoint-administratör

Om du inte har Azure AD Privileged Identity Management i din organisation kan du använda PowerShell- [API: et](/powershell/module/azuread/get-azureaddirectoryrolemember). Börja med den globala administratörs rollen eftersom en global administratör har samma behörigheter i alla moln tjänster som din organisation prenumererar på. Dessa behörigheter beviljas oavsett var de tilldelades: i Microsoft 365 administrations Center, Azure Portal eller av Azure AD-modulen för Microsoft PowerShell.

Ta bort alla konton som inte längre behövs i dessa roller. Kategorisera sedan de återstående konton som har tilldelats administratörs roller:

* Tilldelad till administrativa användare, men används även för icke-administrativa syfte (till exempel personligt e-post)
* Tilldelad till administrativa användare och används endast i administrations syfte
* Delas över flera användare
* För situationer med nöd åtkomst till bryta glas
* För automatiserade skript
* För externa användare

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definiera minst två nödfalls åtkomst konton

Det är möjligt för en användare att av misstag låsa sig av sin roll. Om till exempel en federerad lokal identitetsprovider inte är tillgänglig kan användarna inte logga in eller aktivera ett befintligt administratörs konto. Du kan förbereda för oavsiktlig brist på åtkomst genom att lagra två eller flera återställnings konton för åtkomst.

Med hjälp av konton för nöd åtkomst kan du begränsa privilegie rad åtkomst i en Azure AD-organisation. Dessa konton är mycket privilegierade och har inte tilldelats till vissa individer. Konton för nöd åtkomst är begränsade till nödfall för scenarier med "Break glas" där normala administrativa konton inte kan användas. Se till att du styr och minskar nöd kontots användning enbart till den tid för vilken det är nödvändigt.

Utvärdera de konton som är tilldelade eller berättigade till den globala administratörs rollen. Om du inte ser några moln konton som använder onmicrosoft.com- \* domänen (för "Break glas"-åtkomst till nöd situationer) skapar du dem. Mer information finns i [Hantera administratörs konton för nöd åtkomst i Azure AD](security-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Aktivera Multi-Factor Authentication och registrera alla andra icke-federerade administratörs konton med hög behörighet

Kräv Azure AD Multi-Factor Authentication (MFA) vid inloggning för alla enskilda användare som är permanent kopplade till en eller flera av administratörs rollerna för Azure AD: global administratör, privilegie rad roll administratör, Exchange-administratör och SharePoint-administratör. Använd guiden för att aktivera [Multi-Factor Authentication (MFA) för dina administratörs konton](../authentication/howto-mfa-userstates.md) och se till att alla användare har registrerat sig på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) . Mer information finns under steg 2 och steg 3 i guiden [Skydda åtkomsten till data och tjänster i Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Steg 2: minimera ofta använda attacker

![Steg 2 begränsa angrepp som ofta används](./media/security-planning/stage-two.png)

Steg 2 i översikten fokuserar på att begränsa de mest använda angrepps teknikerna för stöld och missbruk av autentiseringsuppgifter och kan implementeras inom cirka 2-4 veckor. Det här steget i översikten över säkra privilegie rad åtkomst innehåller följande åtgärder.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Genomför en inventering av tjänster, ägare och administratörer

Ökningen i "ta med din egen enhet" och arbeta från hem principer och tillväxten för trådlösa anslutningar gör det viktigt att övervaka vem som ansluter till nätverket. En säkerhets granskning kan avslöja enheter, program och program i nätverket som din organisation inte har stöd för och som utgör hög risk. Mer information finns i [Översikt över Azure Security Management och Monitoring](../../security/fundamentals/management-monitoring-overview.md). Se till att du inkluderar alla följande uppgifter i inventerings processen.

* Identifiera de användare som har administrativa roller och de tjänster som de kan hantera.
* Använd Azure AD PIM för att ta reda på vilka användare i din organisation som har administratörs åtkomst till Azure AD.
* Utöver de roller som definierats i Azure AD kommer Microsoft 365 att ha en uppsättning administratörs roller som du kan tilldela till användare i din organisation. Varje administratörs roll mappar till vanliga affärs funktioner och ger personer i organisationen behörighet att utföra specifika uppgifter i [Microsoft 365 administrations centret](https://admin.microsoft.com). Använd Microsoft 365 administrations Center för att ta reda på vilka användare i din organisation som har administratörs åtkomst till Microsoft 365, inklusive via roller som inte hanteras i Azure AD. Mer information finns i [om Microsoft 365 admin-roller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) och [säkerhets rutiner för Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Gör inventeringen i tjänster som din organisation använder, till exempel Azure, Intune eller Dynamics 365.
* Se till att dina konton som används i administrations syfte:

  * Ha fungerande e-postadresser kopplade till sig
  * Har registrerats för Azure AD Multi-Factor Authentication eller Använd MFA lokalt
* Be användarna om sin affärs motivering för administrativ åtkomst.
* Ta bort administratörs åtkomsten för de individer och tjänster som inte behöver det.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifiera Microsoft-konton i administrativa roller som måste växlas till arbets-eller skol konton

Om dina ursprungliga globala administratörer återanvänder sina befintliga Microsoft-konto autentiseringsuppgifter när de började använda Azure AD, ersätter du Microsoft-kontona med enskilda molnbaserade eller synkroniserade konton.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Se till att separata användar konton och vidarebefordran av e-post för globala administratörs konton

Personliga e-postkonton phished ofta av cyberhot-angripare, en risk som gör att privata e-postadresser inte är godtagbara för globala administratörs konton. Skapa dedikerade konton för varje användare med administratörs behörighet för att hjälpa till att skilja Internet risker från administrativa privilegier.

* Se till att skapa separata konton för användare som ska utföra globala administratörs uppgifter.
* Se till att dina globala administratörer inte oavsiktligt öppnar e-post eller kör program med deras administratörs konton.
* Se till att dessa konton har e-postmeddelandet vidarebefordras till en fungerande post låda.
* Konton för global administratör (och andra privilegierade grupper) bör vara molnbaserade konton utan bindningar till lokala Active Directory.

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Se till att lösen orden för administrativa konton nyligen har ändrats

Se till att alla användare har loggat in på sina administrativa konton och ändrat sina lösen ord minst en gång under de senaste 90 dagarna. Kontrol lera också att alla delade konton har fått sina lösen ord nyligen ändrade.

#### <a name="turn-on-password-hash-synchronization"></a>Aktivera synkronisering av lösen ords-hash

Azure AD Connect synkroniserar ett hash-värde för hashen för en användares lösen ord från lokala Active Directory till en molnbaserad Azure AD-organisation. Du kan använda Password hash-synkronisering som en säkerhets kopia om du använder Federation med Active Directory Federation Services (AD FS) (AD FS). Den här säkerhets kopian kan vara användbar om dina lokala Active Directory-eller AD FS-servrar är tillfälligt otillgängliga.

Med hash-synkronisering av lösen ord kan användarna logga in på en tjänst genom att använda samma lösen ord som de använder för att logga in på den lokala Active Directory-instansen. Med hash-synkronisering av lösen ord kan identitets skydd identifiera komprometterade autentiseringsuppgifter genom att jämföra lösen ords hashar med lösen ord som har visat sig vara komprometterade. Mer information finns i [implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Kräv Multi-Factor Authentication för användare i privilegierade roller och exponerade användare

Azure AD rekommenderar att du kräver Multi-Factor Authentication (MFA) för alla dina användare. Se till att du funderar på vilka användare som skulle ha en betydande inverkan om deras konto har komprometterats (till exempel ekonomi personal). MFA minskar risken för ett angrepp på grund av ett komprometterat lösen ord.

Aktivera:

* [MFA använder principer för villkorlig åtkomst](../authentication/howto-mfa-getstarted.md) för alla användare i din organisation.

Om du använder Windows Hello för företag kan MFA-kravet uppfyllas med hjälp av inloggnings upplevelsen i Windows Hello. Mer information finns i [Windows Hello](/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Konfigurera identitets skydd

Azure AD Identity Protection är ett algoritmbaserade övervaknings-och rapporterings verktyg som identifierar potentiella sårbarheter som påverkar organisationens identiteter. Du kan konfigurera automatiserade svar på de identifierade misstänkta aktiviteterna och vidta lämpliga åtgärder för att lösa dem. Mer information finns i [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>Få Microsoft 365 säkra poäng (om du använder Microsoft 365)

Säkra Poäng tittar på dina inställningar och aktiviteter för de Microsoft 365-tjänster som du använder och jämför dem med en bas linje som upprättats av Microsoft. Du får ett resultat baserat på hur justerat du är med säkerhets praxis. Alla som har administratörs behörighet för en Microsoft 365 Business Standard-eller Enterprise-prenumeration kan komma åt säkra poäng på [https://securescore.office.com](https://securescore.office.com/) .

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Granska vägledningen för Microsoft 365 säkerhet och efterlevnad (om du använder Microsoft 365)

[Planen för säkerhet och efterlevnad](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) beskriver metoden för en Office 365-kund att konfigurera Office 365 och aktivera andra EMS-funktioner. Gå sedan igenom steg 3-6 i så här [skyddar du åtkomsten till data och tjänster i Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) och guiden för att [övervaka säkerhet och efterlevnad i Microsoft 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Konfigurera Microsoft 365 aktivitets övervakning (om du använder Microsoft 365)

Övervaka din organisation för användare som använder Microsoft 365 för att identifiera personal som har ett administratörs konto, men kanske inte behöver Microsoft 365 åtkomst eftersom de inte loggar in på dessa portaler. Mer information finns [i aktivitets rapporter i Microsoft 365 administrations centret](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Upprätta incidenter/svars plan för nöd situationer

Att upprätta en lyckad incident svars funktion kräver avsevärd planering och resurser. Du måste ständigt övervaka för cyberhot-attacker och upprätta prioriteringar för incident hantering. Samla in, analysera och rapportera incident data för att skapa relationer och upprätta kommunikation med andra interna grupper och planera ägare. Mer information finns i [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Skydda lokala privilegierade administratörs konton, om de inte redan gjorts

Om din Azure Active Directory organisation är synkroniserad med lokal Active Directory, följer du rikt linjerna i [säkerhets översikten för privilegie rad åtkomst](/windows-server/identity/securing-privileged-access/securing-privileged-access): det här steget omfattar:

* Skapa separata administratörs konton för användare som behöver utföra lokala administrativa uppgifter
* Distribuera privilegierade åtkomst arbets stationer för Active Directory administratörer
* Skapa unika lokala administratörs lösen ord för arbets stationer och servrar

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare steg för organisationer som hanterar åtkomst till Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Slutför en inventering av prenumerationer

Använd Enterprise Portal och Azure Portal för att identifiera de prenumerationer i organisationen som är värdar för produktions program.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Ta bort Microsoft-konton från administratörs roller

Microsoft-konton från andra program, till exempel Xbox, Live och Outlook, bör inte användas som administratörs konton för din organisations prenumerationer. Ta bort administratörs status från alla Microsoft-konton och Ersätt med Azure AD (till exempel chris@contoso.com ) arbets-eller skol konton. I administratörs syfte beror på konton som autentiseras i Azure AD och inte i andra tjänster.

#### <a name="monitor-azure-activity"></a>Övervaka Azure-aktivitet

Azure-aktivitetsloggen innehåller historik över händelser på prenumerationsnivå i Azure. Den innehåller information om vem som skapade, uppdaterat och tagit bort vilka resurser och när dessa händelser inträffade. Mer information finns i [Granska och ta emot meddelanden om viktiga åtgärder i din Azure-prenumeration](../../azure-monitor/alerts/alerts-activity-log.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare steg för organisationer som hanterar åtkomst till andra molnappar via Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurera principer för villkorlig åtkomst

Förbered principer för villkorlig åtkomst för lokala och molnbaserade program. Om du har användare som är anslutna till arbets platsen kan du få mer information om hur du konfigurerar [lokal villkorlig åtkomst med hjälp av Azure Active Directory enhets registrering](../../active-directory-b2c/overview.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Steg 3: ta kontroll över administratörs aktivitet

![Steg 3: ta kontroll över administratörs aktivitet](./media/security-planning/stage-three.png)

Steg 3 bygger på åtgärder från steg 2 och bör implementeras inom cirka 1-3 månader. Det här steget i den skyddade översikten över privilegie rad åtkomst innehåller följande komponenter.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Slutför en åtkomst granskning av användare i administratörs roller

Fler företags användare får privilegie rad åtkomst via moln tjänster, vilket kan leda till ohanterad åtkomst. Användare idag kan bli globala administratörer för Microsoft 365, Azure-prenumerations administratörer eller ha administratörs åtkomst till virtuella datorer eller via SaaS-appar.

Din organisation bör ha alla anställda som hanterar vanliga affärs transaktioner som användare utan privilegier, och tilldelar sedan endast administratörs rättigheter vid behov. Slutför åtkomst granskningar för att identifiera och bekräfta de användare som är berättigade att aktivera administratörs behörighet.

Vi rekommenderar att du gör följande:

1. Bestäm vilka användare som är Azure AD-administratörer, aktivera administrations åtkomst på begäran, just-in-Time-administratör och rollbaserade säkerhets kontroller.
2. Konvertera användare som inte har någon tydlig motivering för administratör privilegie rad åtkomst till en annan roll (om det inte finns någon berättigad roll, ta bort dem).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Fortsätt distributionen av starkare autentisering för alla användare

Kräv mycket utsatta användare att ha modern, stark autentisering, till exempel Azure AD MFA eller Windows Hello. Exempel på mycket utsatta användare är:

* C-Suite-chefer
* Hög nivå hanterare
* Kritisk IT-och säkerhets personal

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Använda dedikerade arbets stationer för administration för Azure AD

Angripare kan försöka nå privilegierade konton så att de kan störa integriteten och äktheten av data. De använder ofta skadlig kod som ändrar program logiken eller snooping som administratören anger en autentiseringsuppgift. Arbetsstationer för privilegierad åtkomst (PAW, Privileged Access Workstation) ger tillgång till ett dedikerat operativsystem för känsliga uppgifter som är skyddat mot Internetattacker och hotvektorer. Att avgränsa dessa känsliga uppgifter och konton från de dagliga användnings datorerna och enheterna ger starkt skydd från:

* Nätfiske-attacker
* Sårbarheter för program och operativ system
* Personifiering av attacker
* Attacker mot autentiseringsuppgifter som tangenttryckning, pass-The-hash och pass-The-Ticket

Genom att distribuera privilegierade åtkomst arbets stationer kan du minska risken för att administratörer anger sina autentiseringsuppgifter i en stationär miljö som inte har härdats. Mer information finns i [arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Granska National Institute of Standards och teknik rekommendationer för hantering av incidenter

National Institute of Standards and Technology (NIST) innehåller rikt linjer för incident hantering, särskilt för analys av incident-relaterade data och bestämning av lämpligt svar på varje incident. Mer information finns i [(NIST) dator säkerhets incident hanterings guide (SP 800-61, revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementera Privileged Identity Management (PIM) för JIT till ytterligare administrativa roller

För Azure Active Directory använder du [Azure AD Privileged Identity Managements](../privileged-identity-management/pim-configure.md) funktion. Tidsbegränsad aktivering av privilegierade roller fungerar genom att göra det möjligt att:

* Aktivera administratörs behörighet för en speciell uppgift
* Tillämpa MFA under aktiverings processen
* Använd aviseringar för att informera administratörer om out-of-band-ändringar
* Gör det möjligt för användare att behålla sin privilegie rad åtkomst för en förkonfigurerad tids period
* Tillåt säkerhets administratörer att:

  * Identifiera alla privilegierade identiteter
  * Visa gransknings rapporter
  * Skapa åtkomst granskningar för att identifiera alla användare som är berättigade att aktivera administratörs behörighet

Om du redan använder Azure AD Privileged Identity Management justerar du tids ramarna för tidsbegränsade privilegier vid behov (till exempel underhålls fönster).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Bestäm exponering för lösenordsbaserade inloggnings protokoll (om du använder Exchange Online)

Vi rekommenderar att du identifierar varje potentiell användare som kan vara oåterkallelig till organisationen om deras autentiseringsuppgifter har komprometterats. För dessa användare kan du ange starka autentiseringskrav och använda villkorlig åtkomst i Azure AD för att hindra dem från att logga in på sina e-postmeddelanden med hjälp av användar namn och lösen ord. Du kan blockera [äldre autentisering med villkorlig åtkomst](../conditional-access/block-legacy-authentication.md)och du kan [blockera grundläggande autentisering](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) via Exchange Online.

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Slutför en gransknings bedömning för roller för Microsoft 365 roller (om du använder Microsoft 365)

Utvärdera om alla administratörer användare har rätt roller (ta bort och omtilldela enligt denna utvärdering).

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Granska den säkerhets incident hanterings metod som används i Microsoft 365 och jämför med din egen organisation

Du kan hämta den här rapporten från [hantering av säkerhets incidenter i Microsoft 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Fortsätt att skydda lokala privilegierade administratörs konton

Om din Azure Active Directory är ansluten till den lokala Active Directory följer du rikt linjerna i [översikten över säkerhets privilegie rad åtkomst](/windows-server/identity/securing-privileged-access/securing-privileged-access): steg 2. I det här steget:

* Distribuera privilegierade åtkomst arbets stationer för alla administratörer
* Krav på MFA
* Använd tillräckligt mycket administratör för att underhålla domänkontrollanten, vilket minskar risken för attacker på domäner
* Distribuera avancerad hot utvärdering för attack identifiering

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare steg för organisationer som hanterar åtkomst till Azure

#### <a name="establish-integrated-monitoring"></a>Upprätta integrerad övervakning

[Azure Security Center](../../security-center/security-center-introduction.md):

* Tillhandahåller integrerad säkerhetsövervakning och princip hantering i dina Azure-prenumerationer
* Hjälper till att upptäcka hot som annars kan gå vidare till
* Fungerar med ett brett utbud av säkerhetslösningar

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventera dina privilegierade konton i värd Virtual Machines

Du behöver vanligt vis inte ge användare obegränsade behörigheter till alla dina Azure-prenumerationer eller-resurser. Använd administratörs roller i Azure AD för att endast ge åtkomst till de användare som behöver utföra sina jobb. Du kan använda administratörs roller i Azure AD för att låta en administratör hantera virtuella datorer i en prenumeration, medan en annan kan hantera SQL-databaser inom samma prenumeration. Mer information finns i [Vad är rollbaserad åtkomst kontroll i Azure](../../active-directory-b2c/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementera PIM för administratörs roller i Azure AD

Använd Privileged Identity Management med administratörs roller i Azure AD för att hantera, kontrol lera och övervaka åtkomst till Azure-resurser. Att använda PIM skyddar genom att minska exponerings tiden för privilegier och öka din insyn i användningen genom rapporter och aviseringar. Mer information finns i [Vad är Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Använd Azures logg integrering för att skicka relevanta Azure-loggar till dina SIEM-system

Med Azure logg integrering kan du integrera obehandlade loggar från dina Azure-resurser till din organisations befintliga SIEM-system (Security information and Event Management). [Azure logg integrering](/previous-versions/azure/security/fundamentals/azure-log-integration-overview) samlar in Windows-händelser från Windows Loggboken-loggar och Azure-resurser från:

* Azure aktivitets loggar
* Azure Security Center-aviseringar
* Azure-resursloggar

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Ytterligare steg för organisationer som hanterar åtkomst till andra molnappar via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementera användar etablering för anslutna appar

Med Azure AD kan du automatisera skapandet och underhåll av användar identiteter i molnappar som Dropbox, Salesforce och ServiceNow. Mer information finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrera informations skydd

Med Microsoft Cloud App Security kan du undersöka filer och ange principer baserat på Azure Information Protection klassificerings etiketter, vilket ger bättre synlighet och kontroll över dina moln data. Genomsök och klassificera filer i molnet och Använd Azure information Protection-etiketter. Mer information finns i [Azure information Protection-integrering](/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurera villkorlig åtkomst

Konfigurera villkorlig åtkomst baserat på en grupp, plats och program känslighet för [SaaS-appar](https://azure.microsoft.com/overview/what-is-saas/) och Azure AD-anslutna appar. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Övervaka aktivitet i anslutna molnappar

Vi rekommenderar att du använder [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) för att säkerställa att användar åtkomst också skyddas i anslutna program. Den här funktionen skyddar företagets åtkomst till molnappar och skyddar dina administratörs konton, så att du kan:

* Utöka synlighet och kontroll över molnappar
* Skapa principer för åtkomst, aktiviteter och data delning
* Identifiera riskfyllda aktiviteter, onormala beteenden och hot automatiskt
* Förhindra data läckage
* Minimera risker och automatiserat skydd av hot och princip tillämpning

Cloud App Security SIEM-agenten integrerar Cloud App Security med SIEM-servern för att aktivera centraliserad övervakning av Microsoft 365 aviseringar och aktiviteter. Den körs på servern och hämtar aviseringar och aktiviteter från Cloud App Security och strömmar dem till SIEM-servern. Mer information finns i [Siem-integrering](/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses"></a>Steg 4: Fortsätt att skapa försvar

![Steg 4: anta en aktiv säkerhets position](./media/security-planning/stage-four.png)

Steg 4 i översikten bör implementeras på sex månader och mer. Slutför din översikt för att förstärka dina privilegierade åtkomst skydd mot potentiella attacker som är kända i dag. För säkerhetshot i morgon rekommenderar vi att du visar säkerhet som en pågående process för att öka kostnaderna och minska antalet lyckade angripare mål för din miljö.

Att skydda privilegie rad åtkomst är viktigt för att upprätta säkerhets garantier för företagets till gångar. Det bör dock ingå i ett komplett säkerhets program som tillhandahåller säkerhets garantier som pågår. Det här programmet bör innehålla element som:

* Policy
* Operations
* Informations säkerhet
* Servrar
* Program
* Datorer
* Enheter
* Cloud Fabric

Vi rekommenderar följande metoder när du hanterar privilegierade åtkomst konton:

* Se till att administratörerna gör sin dagliga verksamhet som användare utan privilegier
* Bevilja endast privilegie rad åtkomst vid behov och ta bort den efteråt (just-in-Time)
* Behåll gransknings aktivitets loggar som rör privilegierade konton

Mer information om hur du skapar en komplett säkerhets översikt finns i [resurser för IT-arkitektur i Microsoft Cloud](https://almbok.com/office365/microsoft_cloud_it_architecture_resources). Om du vill delta i Microsofts tjänster för att hjälpa dig att implementera någon del av din översikt kan du kontakta din Microsoft-representant eller se [utveckla viktiga cyberhot försvar för att skydda ditt företag](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Det här slutliga pågående steget av den säkrade åtkomst planen innehåller följande komponenter.

### <a name="general-preparation"></a>Allmän förberedelse

#### <a name="review-admin-roles-in-azure-ad"></a>Granska administratörs roller i Azure AD

Ta reda på om de aktuella inbyggda administratörs rollerna för Azure AD fortfarande är uppdaterade och se till att användarna bara har de roller de behöver. Med Azure AD kan du tilldela separata administratörer för att hantera olika funktioner. Mer information finns i [tilldela administratörs roller i Azure Active Directory](permissions-reference.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Granska användare som har administration av Azure AD-anslutna enheter

Mer information finns i [så här konfigurerar du hybrid Azure Active Directory anslutna enheter](../devices/hybrid-azuread-join-plan.md).

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>Granska medlemmar av [inbyggda Microsoft 365 administratörs roller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Hoppa över det här steget om du inte använder Microsoft 365.
‎
#### <a name="validate-incident-response-plan"></a>Verifiera incident svars plan

För att förbättra din plan rekommenderar Microsoft att du regelbundet validerar att din plan fungerar som förväntat:

* Gå igenom din befintliga väg karta för att se vad som missades
* Baserat på Postmortem analys, ändra befintliga eller definiera nya metoder
* Se till att din uppdaterade incident svars plan och praxis distribueras i hela organisationen


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Ytterligare steg för organisationer som hanterar åtkomst till Azure 

Avgör om du behöver [överföra ägarskapet för en Azure-prenumeration till ett annat konto](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Bryt glas": vad som händer i nödfall

![Konton för åtkomst till nöd brytar glas](./media/security-planning/emergency.jpeg)

1. Meddela nyckel hanterare och säkerhets chefer med information om incidenten.

2. Granska din angrepps Spelbok.

3. Öppna ditt "Break glas"-konto användar namn och lösen ord kombination för att logga in på Azure AD.

4. Få hjälp från Microsoft genom att [öppna en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Titta på [inloggnings rapporterna för Azure AD](../reports-monitoring/overview-reports.md). Det kan finnas en stund mellan en händelse som inträffar och när den ingår i rapporten.

6. Om din lokala infrastruktur federerade och din AD FS-server inte är tillgänglig, kan du tillfälligt växla från federerad autentisering till att använda hash-synkronisering av lösen ord för Hybrid miljöer. Med den här växeln återställs domän federationen till hanterad autentisering tills den AD FS servern blir tillgänglig.

7. Övervaka e-post för privilegierade konton.

8. Se till att spara säkerhets kopior av relevanta loggar för potentiella kriminal tekniska och juridisk undersökning.

Mer information om hur Microsoft Office 365 hanterar säkerhets incidenter finns [i hantering av säkerhets incidenter i Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>Vanliga frågor och svar: svar för att skydda privilegie rad åtkomst  

**F:** Vad gör jag om jag inte har implementerat några skyddade åtkomst komponenter ännu?

**Svar:** Definiera minst två Break-glas-konto, tilldela MFA till dina privilegierade administratörs konton och skilj användar konton från globala administratörs konton.

**F:** Vad är det viktigaste problemet som måste åtgärdas först efter en överträdelse?

**Svar:** Se till att du kräver den starkaste autentiseringen för mycket utsatta individer.

**F:** Vad händer om våra privilegierade administratörer har inaktiverats?

**Svar:** Skapa ett globalt administratörs konto som alltid hålls uppdaterat.

**F:** Vad händer om det bara finns en global administratör kvar och de inte kan nås?

**Svar:** Använd ett av dina rast glas konton för att få omedelbar privilegie rad åtkomst.

**F:** Hur kan jag skydda administratörer inom organisationen?

**Svar:** Låt administratörer alltid utföra sin dagliga verksamhet som standard "ej privilegierade" användare.

**F:** Vilka är de bästa metoderna för att skapa administratörs konton i Azure AD?

**Svar:** Reservera privilegie rad åtkomst för vissa administratörs uppgifter.

**F:** Vilka verktyg finns för att minska beständiga administratörs åtkomst?

**Svar:** Privileged Identity Management (PIM) och Azure AD admin-roller.

**F:** Vad är Microsoft-positionen för att synkronisera administratörs konton till Azure AD?

**Svar:** Nivå 0-administratörs konton används endast för lokala AD-konton. Sådana konton är vanligt vis inte synkroniserade med Azure AD i molnet. Nivå 0-administratörs konton inkluderar konton, grupper och andra till gångar som har direkt eller indirekt administrativ styrning av den lokala Active Directory skogen, domäner, domänkontrollanter och till gångar.

**F:** Hur skyddar vi administratörer från att tilldela slumpmässig administratörs åtkomst i portalen?

**Svar:** Använd icke-privilegierade konton för alla användare och de flesta administratörer. Börja med att utveckla organisationens organisation för att avgöra vilka få administratörs konton som ska vara privilegierade. Och Övervakare för nyligen skapade administrativa användare.

## <a name="next-steps"></a>Nästa steg

* [Microsoft säkerhets Center för produkt säkerhet](https://www.microsoft.com/trustcenter/security) – säkerhetsfunktioner i Microsofts moln produkter och tjänster

* [Microsoft Trust Center – kompatibilitet](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – Microsofts omfattande uppsättning erbjudanden för regelefterlevnad för moln tjänster

* [Vägledning om hur du gör en riskbedömning](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – hantera krav på säkerhet och efterlevnad för Microsofts moln tjänster

### <a name="other-microsoft-online-services"></a>Andra Microsoft Online Services

* [Microsoft Intune säkerhet](https://www.microsoft.com/trustcenter/security/intune-security) – Intune tillhandahåller hantering av mobila enheter, hantering av mobila program och funktioner för dator hantering från molnet.

* [Microsoft Dynamics 365 Security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 är den Microsoft-molnbaserade lösningen som förenar funktionerna för kund Relations hantering (CRM) och Enterprise Resource Planning (ERP).
