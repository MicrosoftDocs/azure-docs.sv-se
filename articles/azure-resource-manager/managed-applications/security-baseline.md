---
title: Azures säkerhets bas linje för Azure Managed Applications
description: Azure Managed Applications säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2a5c31270f18c2e6149d93fa522818704b9747d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588608"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Azures säkerhets bas linje för Azure Managed Applications

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../../security/benchmarks/overview.md) till Azure Managed Applications. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Managed Applications. **Kontroller** som inte är tillämpliga på Azure Managed Applications har uteslutits.

Om du vill se hur Azure Managed Applications helt mappar till Azures säkerhets mätning, se den [fullständiga Azure Managed Applications mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning**: använd Azure Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg som kon figurer ATS för dina Azure-hanterade program resurser. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (till exempel: AzureResourceManager) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Använda Azure Resource Manager Service-Taggar](../../virtual-network/service-tags-overview.md#available-service-tags)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security Benchmark: Identitetshantering](../../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt system för identiteter och autentisering

**Vägledning**: Azure Managed Applications använder Azure Active Directory (Azure AD) som standard tjänst för identitets-och åtkomst hantering. Standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering.

Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till hanterade program med hjälp av Azure AD och OAuth:

- Rollen hanterad program deltagare: används för att skapa hanterade program resurser.

- Rollen hanterad program operatör: låter dig läsa och utföra åtgärder på hanterade program resurser

- Läsare för hanterade program: låter dig läsa resurser i en hanterad app och begära JIT-åtkomst.

Läs mer i följande referenser:

- [Rollen hanterad program deltagare](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Rollen hanterad program operatör](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Läsare för hanterade program](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Klientorganisation i Azure Active Directory](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Hantera appidentiteter säkert och automatiskt

**Vägledning**: Använd Azure-hanterade identiteter för att bevilja behörigheter till dina hanterade program. Vi rekommenderar att du använder Azure-hanterad identitets funktion i stället för att skapa ett mer kraftfullt humant konto för att få åtkomst till eller köra resurser för att begränsa behovet av att hantera ytterligare autentiseringsuppgifter. Azure Managed Applicationss tjänsten kan också tilldelas en hanterad identitet internt för att autentisera till andra Azure-tjänster/-resurser som stöder Azure AD-autentisering. Detta kan vara användbart för enkel åtkomst från din Azure Managed Applications att Azure Key Vault när du hämtar hemligheter. När du använder hanterade identiteter hanteras identiteten av Azure-plattformen och du behöver inte tillhandahålla eller rotera några hemligheter.

Azure Managed Applications stöder att ditt program beviljar två typer av identiteter:

- En systemtilldelad identitet är kopplad till konfigurations resursen. Den tas bort om konfigurations resursen tas bort. En konfigurations resurs kan bara ha en tilldelad identitet.

- En användardefinierad identitet är en fristående Azure-resurs som kan tilldelas till konfigurations resursen. En konfigurations resurs kan ha flera användare tilldelade identiteter.

När hanterade identiteter inte kan utnyttjas skapar du ett huvud namn för tjänsten med begränsade behörigheter på resurs nivån för Azure-hanterade program. Konfigurera tjänstens huvud namn med autentiseringsuppgifter för certifikatet och bara återgå till klient hemligheter. I båda fallen kan Azure Key Vault användas tillsammans med Azure Managed identiteter, så att körnings miljön (t. ex. en Azure-funktion) kan hämta autentiseringsuppgiften från nyckel valvet.

- [Använda hanterade identiteter för Azure Managed Applications](publish-managed-identity.md)

- [Hanterade Azure-identiteter](../../active-directory/managed-identities-azure-resources/overview.md)

- [Huvudnamn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps) 

- [Använd Azure Key Vault för registrering av säkerhets objekt](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning i Azure AD (SSO) för åtkomst till appar

**Vägledning**: Azure Managed Applications använda Azure Active Directory för att tillhandahålla identitets-och åtkomst hantering för Azure-resurser, moln program och lokala program. I det här ingår företagsidentiteter som anställda samt externa identiteter som partner, och leverantörer. På så sätt kan enkel inloggning (SSO) hantera och skydda åtkomsten till organisationens data och resurser både lokalt och i molnet. Anslut alla användare, appar och enheter till Azure AD för en smidig och säker åtkomst samt bättre insyn och kontroll.

- [Förstå SSO för appar i Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd kraftfulla verifieringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning**: Azure Managed Applications använder Azure Active Directory som stöder starka verifierings kontroller via multifaktorautentisering och starka metoder för lösen ords kryptering.
- Multifaktorautentisering – aktivera Azure AD multifaktor-autentisering och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering för vissa rekommenderade metoder i konfigurationen av multifaktorautentisering. multifaktorautentisering kan tillämpas på alla, välja användare eller på nivån per användare baserat på inloggnings villkor och riskfaktorer.
- Autentisering utan lösenord – det finns tre alternativ för autentisering utan lösenord: Windows Hello för företag, appen Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort.

För administratörer och privilegierade användare bör du se till att den högsta nivån av stark autentisering används.

- [Så här aktiverar du multifaktorautentisering i Azure](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduktion till autentisering utan lösenord i Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och skicka aviseringar vid kontoavvikelser

**Vägledning**: Azure Managed Applications är integrerat med Azure Active Directory i som tillhandahåller följande data Källor:
- Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen.

Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda Active Directory-signaler för att identifiera, upptäcka och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitetsrapporter i Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Så visar du riskfyllda inloggningar för Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../../security-center/security-center-identity-access.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Begränsa åtkomsten till Azure-resurser baserat på villkor

**Vägledning**: Azure Managed Applications stöder villkorlig åtkomst i Azure AD för en mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, t. ex. användar inloggningar från vissa IP-intervall måste använda multifaktorautentisering för inloggning. Du kan även använda detaljerade policyer för hantering av sessionsautentisering för olika användningsfall.

- [Översikt över villkorsstyrd åtkomst i Azure](../../active-directory/conditional-access/overview.md) 

- [Vanliga policyer för villkorsstyrd åtkomst](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurera hantering av sessionsautentisering med villkorsstyrd åtkomst](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Azure Security Benchmark: Privilegierad åtkomst](../../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Skydda och begränsa privilegierade användare

**Vägledning**: Azure Managed Applications använder Azure Active Directory (Azure AD) för identitet och åtkomst. De mest kritiska inbyggda rollerna är Azure AD global administratör och den privilegierade roll administratören när användare som tilldelats dessa två roller kan delegera administratörs roller:
- Global administratör: användare med den här rollen har åtkomst till alla administrativa funktioner i Azure AD, samt tjänster som använder Azure AD-identiteter.
- Privilegie rad roll administratör: användare med den här rollen kan hantera roll tilldelningar i Azure AD, samt i Azure AD Privileged Identity Management (PIM). Dessutom tillåter den här rollen hantering av alla aspekter av PIM och administrativa enheter.

Obs! Du kan ha andra viktiga roller som behöver regleras om du använder anpassade roller med vissa privilegierade behörigheter tilldelade. Och du kanske också vill använda liknande kontroller för administratörs kontot för kritiska företags till gångar.

Du kan aktivera just-in-time (JIT)-privilegierad åtkomst till Azure-resurser och Azure AD med hjälp av Azure AD Privileged Identity Management (PIM). JIT beviljar temporära behörigheter för att utföra privilegierade uppgifter endast när användarna behöver det. PIM kan också generera säkerhetsaviseringar när det finns misstänkt eller osäker aktivitet i din Azure AD-organisation.

- [Behörigheter för administratörsrollen i Azure AD](../../active-directory/roles/permissions-reference.md)

- [Använda säkerhetsaviseringar från Privileged Identity Management i Azure](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](../../active-directory/roles/security-planning.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Begränsa administrativ åtkomst till affärskritiska system

**Vägledning**: Azure Managed Applications använder Azure RBAC för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som har privilegie rad åtkomst till de prenumerationer och hanterings grupper som de finns i.

Se till att du också begränsar åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän controllers (DCs), säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Åtkomst till hanterings grupp](../../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användarnas åtkomst regelbundet

**Vägledning**: granska användar konton och åtkomst tilldelningen regelbundet för att säkerställa att kontona och åtkomst nivån är giltiga.

Azure Managed Applications använder Azure Active Directory-konton (AAD) för att hantera sina resurser, granska användar konton och åtkomst tilldelningen regelbundet för att säkerställa att kontona och deras åtkomst är giltiga. Du kan använda åtkomst granskningar i Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

Dessutom kan Azure Privileged Identity Management också konfigureras för att varna när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade.

Obs! vissa Azure-tjänster har stöd för lokala användare och roller som inte hanteras via Azure AD. Du måste hantera dessa användare separat.

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nödåtkomst i Azure AD

**Vägledning**: Azure Managed Applications använder Azure Active Directory för att hantera sina resurser. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nödåtkomst har ofta hög behörighet och bör inte tilldelas till specifika individer. Konton för nödåtkomst är begränsade till nödsituationer där du inte kan använda normala administratörskonton.

Du bör se till att autentiseringsuppgifterna (som lösenord, certifikat eller smartkort) för nödåtkomstkonton är skyddade och bara kända av personer som har behörighet att använda dem i nödfall.

- [Hantera konton för nödåtkomst i Azure AD](../../active-directory/roles/security-emergency-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Azure Managed Applications är integrerat med Azure Active Directory för att hantera sina resurser. Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är åtkomst granskningar för Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [Vad är hantering av Azure AD-berättigande](../../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbetsstationer med privilegierad åtkomst

**Vägledning**: Det är viktigt med säkra och isolerade arbetsstationer för att skydda känsliga roller som administratörer, utvecklare och operatörer av kritiska tjänster. Använd hög säkra användar arbets stationer och/eller Azure-skydds för administrativa uppgifter relaterade till dina hanterade program. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och Microsoft Intune när du ska distribuera en säker och hanterad arbetsstation för administrativa uppgifter. Skyddade arbetsstationer kan hanteras centralt för att upprätthålla en säker konfiguration med kraftfulla baslinjer för autentisering, programvara och maskinvara samt begränsad logisk och nätverksbaserad åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Distribuera en arbetsstation med privilegierad åtkomst](/security/compass/privileged-access-deployment)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning**: Azure Managed Applications är integrerat med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via rolltilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Det här är ett komplement till JIT-metoden (just in time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Använd om möjligt inbyggda roller för att allokera behörighet och endast skapa en anpassad roll vid behov.

Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till hanterade program med hjälp av Azure AD och OAuth:

- Rollen hanterad program deltagare: används för att skapa hanterade program resurser.

- Rollen hanterad program operatör: låter dig läsa och utföra åtgärder på hanterade program resurser

- Läsare för hanterade program: låter dig läsa resurser i en hanterad app och begära JIT-åtkomst.

Läs mer i följande referenser:

- [Rollen hanterad program deltagare](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Rollen hanterad program operatör](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Läsare för hanterade program](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Välj godkännande process för Microsoft Support  

**Vägledning**: implementera en organisations godkännande process för support scenarier där Microsoft kan behöva åtkomst till dina Azure-hanterade program data. Customer Lockbox är för närvarande inte tillgängligt för scenarier med hanterade program.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Skydda känsliga data

**Vägledning**: för att ansluta till dina egna nycklar, kan du använda ditt eget lagrings konto för lagring av konfigurationsfiler för hanterade program.

- [Data skydd för hanterade konfigurationsfiler med din egen lagring](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Kryptera känsliga data i vila

**Vägledning**: definitioner av hanterade program som definierar ditt program och dess resurser kan lagras i dina egna lagrings konton som kan konfigureras med Kundhanterade krypterings nycklar.

För scenarier där du inte vill använda ditt eget lagrings utrymme för definitioner av hanterade program, tillhandahåller Azure data i rest-kryptering som standard.

- [Ta med din egen lagring för definitioner av hanterade program](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Förstå kryptering vid vila i Azure](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Så här konfigurerar du Kundhanterade krypterings nycklar](../../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security Benchmark: Tillgångshantering](../../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Obs! Ytterligare behörigheter kan krävas för att få insyn i arbetsbelastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Se till att säkerhetsteamet har åtkomst till tillgångsinventering och metadata

**Vägledning**: Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

Taggar som anges när du skapar den hanterade appen tillämpas även på den hanterade resurs gruppen. Utgivaren av programmet kan ge sin egen ytterligare taggning av de hanterade resurserna efter distributionen.

- [Taggar efter hanterat program UI-element](microsoft-common-tagsbyresource.md)

- [Skapa frågor med Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md) 

- [Mer information om att tagga till gångar finns i besluts guiden för resurs namn givning och taggning](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd bara godkända Azure-tjänster

**Vägledning**: Azure Managed Applications stöder Azure Resource Manager baserade distributioner och konfigurations tvång med hjälp av Azure policy. Använd Azure Policy till att granska och begränsa vilka tjänster användarna kan etablera i miljön. Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor till att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

- [Skapa frågor med Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantera säker livscykelhantering för tillgångar

**Vägledning**: hanterade program resurser och deras anslutna hanterade resurs grupper kan tas bort genom att ta bort den hanterade program resursen. När en hanterad program resurs tas bort, tas även den hanterade resurs gruppen och dess innehåll bort. Ytterligare livs cykel funktioner anges av utgivaren av programmet, där de kan ge konsumenten ytterligare rättigheter över livs cykeln för de underliggande hanterade resurserna via tillåtna åtgärder. Kontakta utgivaren av ditt hanterade program för vilka resurser som hanteras av konsumenten.

- [Rensa hanterade program resurser](./tutorial-create-managed-app-with-custom-provider.md?tabs=azurecli-interactive#clean-up-resources)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Azure Security Benchmark: Loggning och hotidentifiering](../../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering i hanteringen av identiteter och åtkomst i Azure

**Vägledning**: Azure AD tillhandahåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel eller andra Siem/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall: inloggningar – rapporten inloggningar innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.
Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhetsrutiner kan hotskyddsmodulen i Azure Security Center även samla in mer detaljerade säkerhetsaviseringar från enskilda Azure Compute-resurser (virtuella datorer, containrar, App Service), data resurser (SQL DB och lagring) och lager i Azure-tjänsten. Den här funktionen ger dig insyn i konto avvikelser i de enskilda resurserna.

- [Granska aktivitetsrapporter i Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivera Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Skydd mot hot i Azure Security Center](../../security-center/azure-defender.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina hanterade program resurser förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

- [Samla in plattforms loggar och mått med Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Central hantering och analys av säkerhetsloggar

**Vägledning**: centralisera loggning, lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.
Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part.

Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av loggar

**Vägledning**: kontrol lera att lagrings konton eller Log Analytics arbets ytor som används för att lagra loggar som skapats av dina hanterade program resurser har den logg kvarhållningsperiod som anges enligt organisationens regler för efterlevnad.
I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](../../azure-monitor/logs/manage-cost-storage.md)

- [Lagra resurs loggar i ett Azure Storage konto](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera processen för svar på incidenter i Azure

**Vägledning**: Se till att organisationen har processer för svar på säkerhetsincidenter, att processerna är uppdaterade för Azure och att de tränas regelbundet för att säkerställa beredskapen.

- [Implementera säkerhet i hela företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referensguide för incidentsvar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelse – konfigurera aviseringar vid incidenter

**Vägledning**: Konfigurera kontaktinformation vid säkerhetsincidenter i Azure Security Center. Microsoft använder de här kontaktuppgifterna till att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker obehörig åtkomst till dina data. Det finns även alternativ för att anpassa aviseringar och meddelanden vid incidenter i olika Azure-tjänster baserat på åtgärdsbehovet. 

- [Så här ställer du in säkerhetskontakt i Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Identifiering och analys – skapa incidenter baserat på aviseringar av hög kvalitet

**Vägledning**: Se till att du har en process för att skapa aviseringar av hög kvalitet och mäta aviseringskvaliteten. På så sätt kan du lära dig av tidigare incidenter och prioritera aviseringar till analytiker så att de inte slösar tid på falska positiva identifieringar. 

Du kan basera aviseringar av hög kvalitet på erfarenheter från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar genom att sammanfoga och korrelera olika signalkällor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

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

- [Ögonblicksbild av en Windows-dators disk](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Riktlinjer**: Tillhandahålla en kontext till analytiker om vilka incidenter att fokusera på först baserat på aviseringens allvarlighetsgrad och tillgångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center är i sökningen eller analysen som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data. Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera hanteringen av incidenter

**Vägledning**: Automatisera manuella återkommande uppgifter för att korta ned svarstiderna och underlätta för analytikerna. Det tar längre tid att köra uppgifter manuellt, så att varje incident tar längre tid att hantera och analytikerna hinner med färre incidenter. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. 

Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="posture-and-vulnerability-management"></a>Hantering av säkerhetsposition och säkerhetsrisker

*Mer information finns i [Azure Security Benchmark: Hantering av säkerhetsposition och säkerhetsrisker](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Upprätta säkra konfigurationer för Azure-tjänster 

**Vägledning**: definiera säkerhets guardrails för infrastruktur-och DevOps-team genom att göra det enkelt att konfigurera de Azure-tjänster som de använder på ett säkert sätt.

Konfigurera Azure Policy att granska och tillämpa konfigurationer för dina resurser som är relaterade till dina distributioner av hanterade program.

Du kan använda Azure-ritningar för att automatisera distribution och konfiguration av tjänster och program miljöer, inklusive Azure Resource Manager mallar, Azure RBAC-tilldelningar och Azure Policy tilldelningar i en enda skiss definition.

- [Cloud adoption Framework-landnings zon i företags skala](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Skapa och hantera principer för att använda kompatibilitet](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../../governance/blueprints/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Underhåll säkra konfigurationer för Azure-tjänster

**Vägledning**: Använd Azure Security Center för att övervaka resurser som är relaterade till Azure Managed Applications och Använd Azure policy [Deny] och [distribuera om de inte finns]-effekter för att hantera säkra konfigurationer.
- [Förstå Azure Policys effekter](../../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../../governance/blueprints/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Utför regelbundna angreppssimuleringar

**Vägledning**: Utför intrångstester och ”red team”-aktiviteter för dina Azure-resurser och åtgärda alla kritiska säkerhetsbrister som upptäcks.
Se till att följa reglerna för intrångstester i Microsoft Cloud så att dina tester inte strider mot Microsofts policyer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Intrångstester i Azure](../../security/fundamentals/pen-testing.md)

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

*Mer information finns i [Azure Security Benchmark: Säkerhetskopiering och återställning](../../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validera all säkerhetskopiering med kundhanterade nycklar

**Vägledning**: när du lagrar de hanterade program definitionerna i ditt eget lagrings konto, måste du se till att du kan återställa alla associerade Kundhanterade nycklar som används för kontots kryptering, som lagras i Azure Key Vault.

- [Ta med din egen lagring för definitioner av hanterade program](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Så här återställer du Key Vault nycklar i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Minska risken för förlorade nycklar

**Vägledning**: om du tar med din egen lagring för dina definitioner av hanterade program bör du se till att du har åtgärder för att förhindra och återställa från förlust av nycklar som används för att kryptera dina definitioner. Aktivera mjuk borttagning och tömning av skydd på Azure Key Vault som lagrar dina Kundhanterade nycklar för att skydda nycklar mot oavsiktlig eller skadlig borttagning.  

- [Ta med din egen lagring för definitioner av hanterade program](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Aktivera mjuk borttagning och rensningsskydd i Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security Benchmark: Styrning och strategi](../../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Rekommendationer för en säkerhetsarkitektur i Azure – lagring, data och kryptering](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – regelverk kring datasäkerhet och kryptering i Azure](../../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – hantering av tillgångar](../../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – dataskydd](../../security/benchmarks/security-controls-v2-data-protection.md)

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

- [Azure Security Benchmark – hantering av säkerhetspositionen och säkerhetsrisker](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

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

- [Azure Security Benchmark – nätverkssäkerhet](../../security/benchmarks/security-controls-v2-network-security.md)

- [Översikt över nätverkssäkerhet i Azure](../../security/fundamentals/network-overview.md)

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

- [Azure Security Benchmark – hantering av identiteter](../../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – privilegierad åtkomst](../../security/benchmarks/security-controls-v2-privileged-access.md)

- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över säker identitetshantering i Azure](../../security/fundamentals/identity-management-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiera en strategi för loggning och hotåtgärder

**Vägledning**: Upprätta en strategi för loggning och hotåtgärder så att du snabbt kan identifiera och åtgärda hot och samtidigt uppfylla efterlevnadskraven. Prioritera att ge analytiker tillgång till aviseringar av hög kvalitet och en användarvänlig miljö så att de kan fokusera på hoten snarare än integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Säkerhetsorganisationens roller och ansvarsområden 

-   En väldefinierad process för incidentsvar anpassad efter NIST eller något annat branschramverk 

-   Insamling och kvarhållning av loggar som stöd till hotidentifiering, incidentsvar och efterlevnadskrav

-   Central insyn i och korrelerande information om olika hot med hjälp av SIEM, interna Azure-funktioner och andra källor 

-   Plan för kommunikation med kunder, leverantörer och offentliga intressenter

-   Användning av plattformar för incidenthantering i Azure och från tredje part, till exempel för loggning och hotidentifiering, datautredning och motverkande åtgärder

-   Processer för hantering av incidenter och efterföljande aktiviteter som hantering av lärdomar och bevis

Läs mer i följande referenser:

- [Azure Security Benchmark – loggning och hotidentifiering](../../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – svar på incidenter](../../security/benchmarks/security-controls-v2-incident-response.md)

- [Regelverk för Azure-säkerhet 4 – process: uppdatera processer kring incidentsvar för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, guide till beslut om loggning och rapporter](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Hantering och övervakning i företagsskala i Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../../security/benchmarks/security-baselines-overview.md)
