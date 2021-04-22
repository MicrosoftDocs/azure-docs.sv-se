---
title: Azure-säkerhetsbaslinje för Azure Firewall Manager
description: Säkerhetsbaslinjen Azure Firewall Manager procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7cb37de7c5f101ea5f72ff87ccdf94e5925a95d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864423"
---
# <a name="azure-security-baseline-for-azure-firewall-manager"></a>Azure-säkerhetsbaslinje för Azure Firewall Manager

Den här säkerhetsbaslinjen tillämpar riktlinjer [från Azure Security Benchmark version 2.0](../security/benchmarks/overview.md) till Azure Firewall Manager. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure Firewall Manager. **Kontroller** som inte är Azure Firewall Manager har undantagits.

Information om hur Azure Firewall Manager mappning helt till Azure Security Benchmark finns i den fullständiga Azure Firewall Manager [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security Benchmark: Identitetshantering](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt system för identiteter och autentisering

**Vägledning:** Azure Firewall Manager använder Azure Active Directory (Azure AD) som standardtjänst för identitets- och åtkomsthantering. Du bör standardisera Azure AD för att styra din organisations identitets- och åtkomsthantering i:

- Microsoft Cloud-resurser, till exempel Azure Portal, Azure Storage, Azure Virtual Machine (Linux och Windows), Azure Key Vault-, PaaS- och SaaS-program.

- Organisationens resurser, till exempel program i Azure eller företagets nätverksresurser.

Skyddet av Azure AD bör ha hög prioritet i organisationens säkerhetspraxis för molnet. Azure AD ger dig en identitetsskyddspoäng som hjälper dig att utvärdera identiteternas säkerhet i relation till Microsofts rekommendationer kring regelverk. Använd poängen till att mäta hur nära konfigurationen matchar rekommendationerna kring regelverk och förbättra säkerhetsläget.

Azure AD stöder extern identitet som gör att användare utan Microsoft-konto kan logga in på sina program och resurser med sin externa identitet.

- [Klientorganisation i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Använda en extern identitetsprovider för appar](../active-directory/external-identities/identity-providers.md)

- [Vad är identitetsskyddspoängen i Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning i Azure AD (SSO) för åtkomst till appar

**Vägledning:** Azure Firewall Manager använder Azure Active Directory för att tillhandahålla identitets- och åtkomsthantering för Azure-resurser, molnprogram och lokala program. I det här ingår företagsidentiteter som anställda samt externa identiteter som partner, och leverantörer. På så sätt kan enkel inloggning (SSO) hantera och skydda åtkomsten till organisationens data och resurser både lokalt och i molnet. Anslut alla användare, appar och enheter till Azure AD för en smidig och säker åtkomst samt bättre insyn och kontroll.

- [Förstå SSO för appar i Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd kraftfulla verifieringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning:** Azure Firewall Manager använder Azure Active Directory som stöder starka autentiseringskontroller via multifaktorautentisering (MFA) och starka lösenordslösa metoder.
- Multifaktorautentisering – aktivera MFA i Azure AD och följ rekommendationerna kring hantering av identiteter och åtkomst i Azure Security Center så att du använder de bästa regelverken i din MFA-konfiguration. MFA kan tillämpas för alla användare, vissa användare eller på användarnivå baserat på inloggningsförhållanden och riskfaktorer.
- Autentisering utan lösenord – det finns tre alternativ för autentisering utan lösenord: Windows Hello för företag, appen Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort.

För administratörsanvändare och privilegierade användare ser du till att den högsta nivån av den starka autentiseringsmetoden används, följt av att distribuera lämplig stark autentiseringsprincip till andra användare.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduktion till alternativ för lösenordsfri autentisering för Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Standardprincip för lösenord för Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminera felaktiga lösen ord med Azure AD-lösenordsskydd](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och skicka aviseringar vid kontoavvikelser

**Vägledning:** Azure Firewall Manager är integrerad med Azure Active Directory som tillhandahåller följande datakällor:
- Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa datakällor kan integreras med Azure Monitor, Azure Sentinel siem-system eller SIEM-system från tredje part.

För närvarande stöds inte åtgärder som omger brandväggsprincipregelsamlingsgrupper av aktivitetsloggen. Det här är ett känt problem som åtgärdas i framtida uppdateringar.

Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen.

Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda Active Directory-signaler för att identifiera, upptäcka och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Firewall Manager kända problem](overview.md#known-issues)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Begränsa åtkomsten till Azure-resurser baserat på villkor

**Vägledning:** Azure Firewall Manager stöder villkorlig Azure Active Directory-åtkomst (Azure AD) för en mer detaljerad åtkomstkontroll baserat på användardefinierade villkor, till exempel användarinloggningar från vissa IP-intervall som behöver använda MFA för inloggning. Du kan även använda detaljerade policyer för hantering av sessionsautentisering för olika användningsfall.

- [Översikt över villkorsstyrd åtkomst i Azure](../active-directory/conditional-access/overview.md)

- [Vanliga policyer för villkorsstyrd åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurera hantering av sessionsautentisering med villkorsstyrd åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Azure Security Benchmark: Privilegierad åtkomst](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Skydda och begränsa privilegierade användare

**Vägledning:** Azure Firewall Manager använder Azure Active Directory (Azure AD) för identitet och åtkomst. De viktigaste inbyggda rollerna är Azure AD är Global administratör och Privilegierad rolladministratör eftersom användare som tilldelats dessa två roller kan delegera administratörsroller:
- Global administratör: Användare med den här rollen har åtkomst till alla administrativa funktioner i Azure AD, samt tjänster som använder Azure AD-identiteter.
- Privilegierad rolladministratör: Användare med den här rollen kan hantera rolltilldelningar i Azure AD, samt inom Azure AD Privileged Identity Management (PIM). Dessutom tillåter den här rollen hantering av alla aspekter av PIM och administrativa enheter.

Du kan ha andra kritiska roller som måste styras om du använder anpassade roller med vissa tilldelade privilegierade behörigheter. Och du kanske också vill tillämpa liknande kontroller som administratörskontot för kritiska affärstillgångar.

Du kan aktivera just-in-time (JIT)-privilegierad åtkomst till Azure-resurser och Azure AD med hjälp av Azure AD Privileged Identity Management (PIM). JIT beviljar temporära behörigheter för att utföra privilegierade uppgifter endast när användarna behöver det. PIM kan också generera säkerhetsaviseringar när det finns misstänkt eller osäker aktivitet i din Azure AD-organisation.

- [Behörigheter för administratörsrollen i Azure AD](../active-directory/roles/permissions-reference.md)

- [Använda säkerhetsaviseringar från Privileged Identity Management i Azure](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](../active-directory/roles/security-planning.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Begränsa administrativ åtkomst till affärskritiska system

**Vägledning:** Använda anpassade Azure RBAC-roller för att isolera åtkomst till regelsamlingsgrupper för Azure Firewall princip. Använd en anpassad rolldefinition i Azure för att förhindra oavsiktlig borttagning av basprinciper och ge selektiv åtkomst till regelsamlingsgrupper i en prenumeration eller resursgrupp.

Se till att du även begränsar åtkomsten till de hanterings-, identitets- och säkerhetssystem som har administrativ åtkomst till dina affärskritiska system. Angripare som komprometterar dessa hanterings- och säkerhetssystem kan omedelbart få dem att kompromettera affärskritiska tillgångar.

Alla typer av åtkomstkontroller bör anpassas till din strategi för företagssegmentering för att säkerställa konsekvent åtkomstkontroll.

- [Använda Azure Firewall för att definiera en regelhierarki](rule-hierarchy.md)

- [Azure-komponenter och referensmodell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Åtkomst till hanteringsgrupp](../governance/management-groups/overview.md#management-group-access)

- [Azure-prenumerationsadministratörer](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användarnas åtkomst regelbundet

**Vägledning:** Azure Firewall Manager använder Azure Active Directory (Azure AD)-konton för att hantera dess resurser. Granska användarkonton och åtkomsttilldelning regelbundet för att se till att kontona och deras åtkomst är giltiga. Du kan använda Azure AD-åtkomstgranskningar för att granska gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Azure AD-rapportering kan tillhandahålla loggar som hjälper dig att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa arbetsflödet för granskningsrapport för att underlätta granskningsprocessen.

Dessutom kan Azure Privileged Identity Management konfigureras för att varna när ett stort antal administratörskonton skapas och för att identifiera administratörskonton som är inaktuella eller felaktigt konfigurerade.

Vissa Azure-tjänster stöder lokala användare och roller som inte hanteras via Azure AD. Du måste hantera dessa användare separat.

- [Skapa en åtkomstgranskning av Azure-resursroller i Privileged Identity Management(PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nödåtkomst i Azure AD

**Vägledning:** Azure Firewall Manager använder Azure Active Directory för att autentisera användare som hanterar tjänsten. Om du vill förhindra att azure AD-organisationen blir utelåst av misstag ställer du in ett konto för åtkomst vid akutfall för åtkomst när normala administrativa konton inte kan användas. Konton för nödåtkomst har ofta hög behörighet och bör inte tilldelas till specifika individer. Konton för nödåtkomst är begränsade till nödsituationer där du inte kan använda normala administratörskonton.

Du bör se till att autentiseringsuppgifterna (som lösenord, certifikat eller smartkort) för nödåtkomstkonton är skyddade och bara kända av personer som har behörighet att använda dem i nödfall.

- [Hantera konton för nödåtkomst i Azure AD](../active-directory/roles/security-emergency-access.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatisera berättigandehantering 

**Vägledning:** Azure Firewall Manager är integrerad med Azure Active Directory för att hantera dess resurser. Använd azure AD-berättigandehanteringsfunktioner för att automatisera arbetsflöden för åtkomstbegäran, inklusive åtkomsttilldelningar, granskningar och förfallotid. Godkännanden med dubbla eller flera steg stöds också.

- [Vad är Azure AD-åtkomstgranskningar?](../active-directory/governance/access-reviews-overview.md)

- [Vad är berättigandehantering i Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbetsstationer med privilegierad åtkomst

**Vägledning**: Det är viktigt med säkra och isolerade arbetsstationer för att skydda känsliga roller som administratörer, utvecklare och operatörer av kritiska tjänster. Använd mycket säkra användararbetsstationen för att utföra administrativa hanteringsuppgifter med Azure Firewall Manager i produktionsmiljöer. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och Microsoft Intune när du ska distribuera en säker och hanterad arbetsstation för administrativa uppgifter. De skyddade arbetsstationerna kan hanteras centralt för att framtvinga säker konfiguration, inklusive stark autentisering, programvaru- och maskinvarubaslinjer samt begränsad logisk och nätverksåtkomst.

- [Förstå arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Distribuera en arbetsstation med privilegierad åtkomst](/security/compass/privileged-access-deployment)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning:** Azure Firewall Manager är integrerad med rollbaserad åtkomstkontroll (RBAC) i Azure för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via rolltilldelningar. Du kan tilldela dessa roller till användare, grupper av tjänstens huvudnamn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Det här är ett komplement till JIT-metoden (just in time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Använd inbyggda roller för att allokera behörighet och skapa bara en anpassad roll vid behov.

- [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Skydda känsliga data

**Vägledning:** Skydda känsliga data som konfigurationsdata för Azure Firewall-princip genom att begränsa åtkomsten med hjälp av Rollbaserad azure-Access Control (Azure RBAC), nätverksbaserade åtkomstkontroller och specifika kontroller i Azure-tjänster.

För att säkerställa konsekvent åtkomstkontroll bör alla typer av åtkomstkontroll justeras mot din strategi för företagssegmentering. Strategin för företagssegmentering bör också informeras om platsen för känsliga eller affärskritiska data och system.

När det gäller den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kundinnehåll som känsligt och skyddar det mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure alltid är skyddade har Microsoft implementerat vissa standardkontroller och funktioner för dataskydd.

- [Rollbaserad åtkomstkontroll (RBAC) i Azure](../role-based-access-control/overview.md)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Övervaka obehörig överföring av känsliga data

**Vägledning:** Azure Firewall principresurser kan bara användas av autentiserade användare. Kunderna måste se till att endast behöriga användare har åtkomst till data.

- [Skapa anpassade roller för att få åtkomst till regelsamlingsgrupper](rule-hierarchy.md#create-custom-roles-to-access-the-rule-collection-groups)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Kryptera känslig information under överföring

**Vägledning:** För att komplettera åtkomstkontroller bör data under överföring skyddas mot "out-of-band"-attacker (till exempel trafikinfångst) med hjälp av kryptering för att säkerställa att angripare inte enkelt kan läsa eller ändra data.

Azure Firewall Manager stöder datakryptering under överföring med TLS v1.2 eller högre.

Även om detta är valfritt för trafik i privata nätverk, är detta viktigt för trafik i externa och offentliga nätverk. För HTTP-trafik ser du till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS v1.2 eller högre. För fjärrhantering använder du SSH (för Linux) eller RDP/TLS (för Windows) i stället för ett okrypterat protokoll. Föråldrade SSL-, TLS- och SSH-versioner och -protokoll och svaga chiffer bör inaktiveras.

Som standard tillhandahåller Azure kryptering för data under överföring mellan Azure-datacenter.

- [Förstå kryptering under överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Information om TLS-säkerhet](/security/engineering/solving-tls1-problem) 

- [Dubbel kryptering för Azure-data under överföring](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security Benchmark: Tillgångshantering](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Ytterligare behörigheter kan krävas för att få insyn i arbetsbelastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Se till att säkerhetsteamet har åtkomst till tillgångsinventering och metadata

**Vägledning:** Se till att säkerhetsteamen har åtkomst till en kontinuerligt uppdaterad inventering av Azure Firewall Manager på Azure. De kan använda Azure Resource Graph att köra frågor mot och identifiera Azure Firewall resurser i dina prenumerationer, inklusive Azure-tjänster, program och nätverksresurser.

Tillämpa taggar på dina Azure-resurser, resursgrupper och prenumerationer för att organisera dem logiskt i en taxonomi. Varje tagg består av ett namn och ett värdepar. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center tillgångslagerhantering](../security-center/asset-inventory.md) 

- [Mer information om taggning av tillgångar finns i beslutsguiden för namngivning och taggning av resurser](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd bara godkända Azure-tjänster

**Vägledning:** Använd Azure Policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Detta omfattar att tillåta eller neka distributioner av Azure Firewall resurser. Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor till att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en viss resurstyp med Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantera säker livscykelhantering för tillgångar

**Vägledning:** Ta Azure Firewall Manager resurser när de inte längre behövs för att minimera angreppsytan. Användare kan hantera sina Azure Firewall Manager via api:Azure Portal, CLI eller REST.

- [Azure Firewall Policy CLI](/cli/azure/network/firewall/policy)

- [Azure-nätverks-CLI](/powershell/module/az.network/?preserve-view=true&view=azps-5.1.0#networking)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Azure Firewall Manager är integrerad med Azure Active Directory (Azure AD) för identitet och autentisering. Du kan använda villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resources Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Azure Security Benchmark: Loggning och hotidentifiering](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hotidentifiering för Azure-resurser

**Vägledning:** Vidarebefordra aktivitetsloggar som skapas av eller som är relaterade till brandväggsprincipen till din SIEM som kan användas för att konfigurera anpassade hotidentifieringar. Se till att du övervakar olika typer av Azure-tillgångar för potentiella hot och avvikelser. Fokusera på att få aviseringar av hög kvalitet för att minska falska positiva resultat som analytiker kan gå igenom. Aviseringar kan komma från loggdata, agenter eller andra data.

- [Skapa anpassade analysregler för att identifiera hot](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyberhotinformation med Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

- [Azure Firewall-loggar och mått](../firewall/firewall-diagnostics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering för identitets- och åtkomsthantering i Azure

**Riktlinjer**: Azure AD innehåller följande användarloggar som kan visas i Azure AD- rapportering eller integreras med Azure Monitor, Azure Sentinel eller andra SIEM/övervakningsverktyg för mer avancerade användningsfall med övervakning och analys:
- Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.

- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhetsrutiner kan hotskyddsmodulen i Azure Security Center även samla in mer detaljerade säkerhetsaviseringar från enskilda Azure Compute-resurser (virtuella datorer, containrar, App Service), data resurser (SQL DB och lagring) och lager i Azure-tjänsten. Den här funktionen ger dig insyn i kontoavvikelser i de enskilda resurserna.

För närvarande stöds inte åtgärder som omger brandväggsprincipregelsamlingsgrupper av aktivitetsloggen. Det här är ett känt problem som åtgärdas i framtida uppdateringar.

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Skydd mot hot i Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning:** Aktivitetsloggar, som är automatiskt tillgängliga, innehåller alla skrivåtgärder (PUT, POST, DELETE) för dina brandväggsprincipresurser förutom läsåtgärder (GET). Aktivitetsloggar kan användas för att hitta ett fel vid felsökning eller för att övervaka hur en användare i organisationen har ändrat en resurs.

För närvarande stöds inte åtgärder som omger brandväggsprincipregelsamlingsgrupper av aktivitetsloggen. Det här är ett känt problem som åtgärdas i framtida uppdateringar.

- [Så här samlar du in plattformsloggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Förstå loggning och olika loggtyper i Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Central hantering och analys av säkerhetsloggar

**Vägledning:** Centralisera lagring och analys av loggning för att möjliggöra korrelation. Se till att du har tilldelat en dataägare, åtkomstvägledning, lagringsplats, vilka verktyg som används för att bearbeta och komma åt data samt krav på databevarande för varje loggkälla.

Se till att du integrerar Azure-aktivitetsloggar i din centrala loggning. Mata in loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av slutpunktsenheter, nätverksresurser och andra säkerhetssystem. I Azure Monitor använder du Log Analytics-arbetsytor för att köra frågor mot och utföra analyser och använda Azure Storage-konton för långsiktig lagring och arkiveringslagring.

Dessutom kan du aktivera och registrera dina loggdata för Azure Sentinel eller en SIEM från tredje part.

Många organisationer väljer att använda Azure Sentinel för "frekventa" data som används ofta Azure Storage för "kalla" data som används mindre ofta.

- [Samla in plattformsloggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av loggar

**Vägledning:** Se till att alla lagringskonton eller Log Analytics-arbetsytor som används för att lagra brandväggsprinciploggar har kvarhållningsperioden för loggar enligt organisationens efterlevnadsregler.

I Azure Monitor kan du ange kvarhållningsperioden för Log Analytics-arbetsytan enligt organisationens efterlevnadsregler. Använd Azure Storage-, Data Lake- eller Log Analytics-arbetsytekonton för långsiktig lagring och arkiveringslagring.

- [Så här konfigurerar du Kvarhållningsperiod för Log Analytics-arbetsyta](../azure-monitor/logs/manage-cost-storage.md)

- [Lagra resursloggar i ett Azure Storage konto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera processen för svar på incidenter i Azure

**Vägledning**: Se till att organisationen har processer för svar på säkerhetsincidenter, att processerna är uppdaterade för Azure och att de tränas regelbundet för att säkerställa beredskapen.

- [Implementera säkerhet i hela företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referensguide för incidentsvar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelse – konfigurera aviseringar vid incidenter

**Vägledning**: Konfigurera kontaktinformation vid säkerhetsincidenter i Azure Security Center. Microsoft använder de här kontaktuppgifterna till att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker obehörig åtkomst till dina data. Det finns även alternativ för att anpassa aviseringar och meddelanden vid incidenter i olika Azure-tjänster baserat på åtgärdsbehovet. 

- [Så här ställer du in säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Identifiering och analys – skapa incidenter baserat på aviseringar av hög kvalitet

**Vägledning**: Se till att du har en process för att skapa aviseringar av hög kvalitet och mäta aviseringskvaliteten. På så sätt kan du lära dig av tidigare incidenter och prioritera aviseringar till analytiker så att de inte slösar tid på falska positiva identifieringar. 

Du kan basera aviseringar av hög kvalitet på erfarenheter från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar genom att sammanfoga och korrelera olika signalkällor. 

Azure Security Center tillhandahåller aviseringar av hög kvalitet för många Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

**Riktlinjer**: Se till att analytiker kan fråga och använda olika datakällor när de undersöker potentiella incidenter, så att de kan skapa en fullständig överblick över vad som hände. Diverse loggar bör samlas in för att spåra en potentiell angripares aktiviteter över hela händelsekedjan för att undvika att blinda fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

Datakällorna för undersökningen innehåller de centraliserade loggningskällor som redan har samlats in från tjänster som omfattas och system som körs, men kan även innehålla:

- Nätverksdata – Använd nätverkssäkerhetsgruppers flödesloggar, Azure Network Watcher och Azure Monitor för att avbilda nätverksflödesloggar och annan analysinformation. 

- Ögonblicksbilder av system som körs: 

    - Använd funktionen för ögonblicksbilder på den virtuella Azure-datorn för att skapa en ögonblicksbild av det aktiva systemets disk. 

    - Använd operativsystemets interna minnesdumpningsfunktion för att skapa en ögonblicksbild av det aktiva systemets minne.

    - Använd funktionen för ögonblicksbilder i Azure-tjänsterna eller din programvaras egna funktion för att skapa ögonblicksbilder av de system som körs.

Azure Sentinel tillhandahåller omfattande dataanalyser i praktiskt taget alla loggkällor och en ärendehanteringsportal för att hantera hela livscykeln för incidenter. Affärsinformation under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicksbild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Riktlinjer**: Tillhandahålla en kontext till analytiker om vilka incidenter att fokusera på först baserat på aviseringens allvarlighetsgrad och tillgångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är vid upptäckten eller analysen som användes för att utfärda aviseringen, samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera hanteringen av incidenter

**Vägledning**: Automatisera manuella återkommande uppgifter för att korta ned svarstiderna och underlätta för analytikerna. Det tar längre tid att köra uppgifter manuellt, så att varje incident tar längre tid att hantera och analytikerna hinner med färre incidenter. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="posture-and-vulnerability-management"></a>Hantering av säkerhetsposition och säkerhetsrisker

*Mer information finns i [Azure Security Benchmark: Hantering av säkerhetsposition och säkerhetsrisker](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Upprätta säkra konfigurationer för Azure-tjänster 

**Vägledning:** Automatisera och skydda distributionen och konfigurationen av Azure Firewall Manager-resurser i dina miljöer med hjälp av metoder som: Azure Resource Manager-mallar, Azure RBAC-kontroller och Azure Policy. Definiera säkra konfigurationer för dina Azure Firewall Manager-resurser vid distributionen, granska och framtvinga dessa konfigurationer genom att definiera anpassade Azure Policy-definitioner med hjälp av alias i namnområdet "Azure.Network".

- [Azure Firewall principmallreferens](/azure/templates/microsoft.network/firewallpolicies)

- [Azure Firewall Policy CLI](/cli/azure/network/firewall/policy)

- [Bild av implementering av skyddsräcken i en landningszon i företagsskala](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Underhåll säkra konfigurationer för Azure-tjänster

**Vägledning:** Azure Firewall Manager stöder Azure Resource Manager-baserade mallar och tillämpning av konfigurationsinställningar via Azure Policy. Definiera anpassade Azure Policy för att granska och tillämpa Azure Firewall Manager med hjälp av alias i namnområdet "Azure.Network".

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

- [Azure Firewall principmallreferens](/azure/templates/microsoft.network/firewallpolicies)

- [Bild av implementering av skyddsräcken i en landningszon i företagsskala](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Upprätta säkra konfigurationer för beräkningsresurser

**Vägledning:** Inte tillämpligt; Azure Firewall Manager är en tjänst för hantering av brandväggsplanet och exponerar inte den underliggande tjänstens beräkningsinfrastruktur som kunder kan konfigurera.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Utför regelbundna angreppssimuleringar

**Vägledning**: Utför intrångstester och ”red team”-aktiviteter för dina Azure-resurser och åtgärda alla kritiska säkerhetsbrister som upptäcks.
Se till att följa reglerna för intrångstester i Microsoft Cloud så att dina tester inte strider mot Microsofts policyer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Intrångstester i Azure](../security/fundamentals/pen-testing.md)

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

*Mer information finns i [Azure Security Benchmark: Säkerhetskopiering och återställning](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Azure Firewall Manager inte har begreppet kundriktade systemsäkerhetskopior hanteras den underliggande infrastrukturen av Microsoft.

För säkerhetskopieringar av resurskonfiguration använder Azure Resource Manager för att exportera brandväggsprinciper och relaterade resurser i en JavaScript Object Notation-mall (JSON) som kan användas som en konfigurationssäkerhetskopia. Du kan också exportera brandväggsprincipkonfigurationer med hjälp av funktionen exportera mall i Azure Firewall från Azure Portal. Använd Azure Automation att köra anpassade säkerhetskopieringsskript för att avbilda resurskonfigurationer för Azure Firewall Manager resurser automatiskt.

- [Distribuera säker virtuell hubb med hjälp av mall](quick-secure-virtual-hub.md)

- [Mallreferens för Microsoft Firewall-princip](/azure/templates/microsoft.network/firewallpolicies)

- [Om Azure Automation](../automation/automation-intro.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validera all säkerhetskopiering med kundhanterade nycklar

**Vägledning:** Azure Firewall Manager har inte begreppet kundriktade systemsäkerhetskopior. För alla exporterade Azure Firewall Manager regelbundet återställning med hjälp av dessa Azure Resource Manager-mallfiler.

- [Distribuera säker virtuell hubb med hjälp Azure Resource Manager mallar](quick-secure-virtual-hub.md)

- [Mallreferens för Microsoft-brandväggsprincip](/azure/templates/microsoft.network/firewallpolicies)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security Benchmark: Styrning och strategi](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiera en strategi för tillgångshantering och dataskydd 

**Vägledning**: Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärskritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   En standard för dataklassificering i enlighet med affärsrisker

-   Säkerhetsorganisationens insyn i risker och tillgångsinventering 

-   Säkerhetsorganisationens godkännande av de Azure-tjänster som används 

-   Tillgångars säkerhet genom hela livscykeln

-   Nödvändig strategi för åtkomstkontroll i enlighet med organisationens dataklassificering

-   Användning av dataskyddsfunktioner i Azure och från tredje part

-   Krav på datakryptering både under överföring och i vila

-   Lämpliga kryptografiska standarder

Läs mer i följande referenser:
- [Rekommendationer för en säkerhetsarkitektur i Azure – lagring, data och kryptering](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – regelverk kring datasäkerhet och kryptering i Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – hantering av tillgångar](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – dataskydd](../security/benchmarks/security-controls-v2-data-protection.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiera företagets segmenteringsstrategi 

**Vägledning**: Upprätta en strategi för hela företaget gällande att segmentera åtkomsten till tillgångar utifrån en kombination av identiteter, nätverk, program, prenumerationer, hanteringsgrupper och andra kontroller.

Du måste noga avväga behovet av separationsskyddet med behovet att underlätta den dagliga driften av de system som måste kommunicera med varandra och komma åt data.

Se till att segmenteringsstrategin implementeras konsekvent för olika kontrolltyper som nätverkssäkerhet, modeller för identiteter och åtkomst, modeller för appbehörighet och appåtkomst samt kontroller för mänskliga processer.

- [Vägledning om segmenteringsstrategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segmenteringsstrategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverkssegmenteringen efter företagets segmenteringsstrategi](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiera en strategi för hantering av säkerhetspositionen

**Vägledning**: Mät och minimera risker gällande enskilda tillgångar och miljöerna där de finns regelbundet. Prioritera värdefulla tillgångar och attackytor med stor exponering, som publicerade appar, in- och utgångar i nätverket och slutpunkter för användare och administratörer.

- [Azure Security Benchmark – hantering av säkerhetspositionen och säkerhetsrisker](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Justera organisationens roller och ansvarsområden

**Vägledning**: Dokumentera och förmedla en tydlig strategi gällande säkerhetsorganisationens roller och ansvarsområden. Prioritera att delegera ett tydligt ansvar för olika säkerhetsbeslut och utbilda alla kring modellen med gemensamt ansvar, och ge de tekniska teamen den utbildning som behövs kring tekniken för att skydda molnet.

- [Regelverk för Azure-säkerhet 1 – personal: utbilda teamen om molnsäkerhetsresan](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Regelverk för Azure-säkerhet 2 – personal: utbilda teamen om molnsäkerhetstekniken](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Regelverk för Azure-säkerhet 3 – personal: tilldela ansvar för molnsäkerhetsbeslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiera en strategi för nätverkssäkerhet

**Vägledning**: Upprätta en strategi för nätverkssäkerhet i Azure inom ramen för organisationens övergripande strategi för åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Centraliserade ansvarsområden kring nätverkshantering och säkerhet

-   Modell för segmentering av virtuella nätverk anpassad efter företagets segmenteringsstrategi

-   Åtgärdsstrategi för olika hot- och angreppsscenarier

-   Strategi för kantenheter på internet samt in- och utgångar

-   Strategi för hybridmoln och lokala anslutningar

-   Aktuella nätverkssäkerhetsartefakter (som nätverksdiagram och referensnätverksarkitekturer)

Läs mer i följande referenser:
- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – nätverkssäkerhet](../security/benchmarks/security-controls-v2-network-security.md)

- [Översikt över nätverkssäkerhet i Azure](../security/fundamentals/network-overview.md)

- [En strategi för företagets nätverksarkitektur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiera en strategi för identiteter och privilegierad åtkomst

**Vägledning**: Upprätta en strategi för identiteter och privilegierad åtkomst i Azure inom ramen för organisationens övergripande strategi för åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Ett centraliserat system för identiteter och autentisering och dess anslutningar till andra interna och externa identitetssystem

-   Starka autentiseringsmetoder i olika användningsfall och scenarier

-   Skydda och användare med hög behörighet

-   Övervaka och hantera avvikande användaraktiviteter  

-   Process för att granska och stämma av identiteter och åtkomstbehörighet

Läs mer i följande referenser:

- [Azure Security Benchmark – hantering av identiteter](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – privilegierad åtkomst](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över säker identitetshantering i Azure](../security/fundamentals/identity-management-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiera en strategi för loggning och hotåtgärder

**Riktlinjer**: Upprätta en strategi för loggning och hothantering för att snabbt upptäcka och åtgärda hot och samtidigt uppfylla kraven för efterlevnad. Prioritera att ge analytiker högkvalitativa varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för på integrering och manuella åtgärder. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Säkerhetsorganisationens roller och ansvarsområden 

-   En väldefinierad process för incidentsvar anpassad efter NIST eller något annat branschramverk 

-   Insamling och kvarhållning av loggar som stöd till hotidentifiering, incidentsvar och efterlevnadskrav

-   Central insyn i och korrelerande information om olika hot med hjälp av SIEM, interna Azure-funktioner och andra källor 

-   Plan för kommunikation med kunder, leverantörer och offentliga intressenter

-   Användning av plattformar för incidenthantering i Azure och från tredje part, till exempel för loggning och hotidentifiering, datautredning och motverkande åtgärder

-   Processer för hantering av incidenter och efterföljande aktiviteter som hantering av lärdomar och bevis

Läs mer i följande referenser:

- [Azure Security Benchmark – loggning och hotidentifiering](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – svar på incidenter](../security/benchmarks/security-controls-v2-incident-response.md)

- [Regelverk för Azure-säkerhet 4 – process: uppdatera processer kring incidentsvar för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, guide till beslut om loggning och rapporter](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Hantering och övervakning i företagsskala i Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)