---
title: Azures säkerhets bas linje för Site Recovery
description: Site Recovery säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf91c0a6e533acb326fe815c3e3c1088c959a603
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576721"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Azures säkerhets bas linje för Site Recovery

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Site Recovery. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Site Recovery. **Kontroller** som inte är tillämpliga på Site Recovery har uteslutits. 

Om du vill se hur Site Recovery helt mappar till Azures säkerhets mätning, se den [fullständiga Site Recovery mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Microsoft Azure Site Recovery stöder inte distribution till en Azure-Virtual Network. Konfigurera Site Recovery-tjänsten med en privat Azure-slutpunkt för att upprätthålla säker kommunikation över nätverket.

- [Stöd för Azure Site Recovery privat länk](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Site Recovery Service stöder service märken, vilket gör att kunderna endast kan öppna trafik till vissa tjänster och portar. Kunder måste tillåta "AzureSiteRecovery"-service tag i brand väggen eller nätverks säkerhets gruppen för att tillåta utgående åtkomst till Site Recovery-tjänsten.

- [Utgående anslutning med service märken](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd resurs taggar för nätverks säkerhets grupper och andra resurser som är relaterade till nätverks säkerhets-och trafikflödet. Använd fältet Beskrivning för enskilda regler för nätverks säkerhets grupper för att dokumentera de regler som tillåter trafik till och från ett nätverk. 

Inkludera någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser. 

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar. 

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md) 

- [Så här skapar du en Azure-Virtual Network](../virtual-network/quick-create-portal.md) 

- [Filtrera nätverks trafik med regler för nätverks säkerhets grupper](../virtual-network/tutorial-filter-network-traffic.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: övervaka alla ändringar av konfigurationer för nätverks resurser som är relaterade till den Site Recovery tjänsten med hjälp av Azure aktivitets loggar. Skapa aviseringar i Azure Monitor för att meddela dig när kritiska Site Recovery nätverks resurser ändras.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets loggen för gransknings loggning och skicka loggarna till en Log Analytics arbets yta, Azure Storage konto eller en Azure Event Hub för arkivering.

Använd Azures aktivitets logg data för att fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) som utförs på dina Azure-resurser.

Mata in Site Recovery loggar i Azure Monitor för att aggregera genererade säkerhets data. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda lagrings konton för långsiktig lagring eller lagring. Du kan också aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM-lösning (security incident and Event Management) från tredje part.

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Övervaka Site Recovery med Azure Monitor-loggar](monitor-log-analytics.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets loggen för gransknings loggning och skicka loggarna till en Log Analytics arbets yta, Azure Storage konto eller till en Azure Event Hub för arkivering. 

Använd Azures aktivitets logg data för att fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) som utförs på dina Azure-resurser.

Mata in Site Recovery loggar med Azure Monitor för att aggregera genererade säkerhets data. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser, och Använd lagrings konton för långsiktig/arkivera lagring. Aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM-lösning (security incident and Event Management) från tredje part.

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Övervaka Site Recovery med Azure Monitor-loggar](monitor-log-analytics.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: Ange logg kvarhållningsperiod för Log Analytics arbets ytor som är kopplade till dina Azure Recovery Services-valv med Azure Monitor enligt organisationens regler för efterlevnad. 

- [Ange parametrar för logg bevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta. 

Kör frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och insikter om aktivitets logg data som samlas in från Recovery Services-valv.

- [Övervaka Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Samla in och analysera Azure-aktivitets loggar i Log Analytics arbets yta i Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: övervaka datorer som replikeras med hjälp av Azure Site Recovery Azure Monitor loggar och Log Analytics. Använd Log Analytics i Azure Monitor för att skriva och testa logg frågor och för att interaktivt analysera loggdata. Azure Monitor samlar in aktivitets-och resurs loggar tillsammans med andra övervaknings data. 

Visualisera och fråga logg resultat och konfigurera aviseringar för att vidta åtgärder baserat på övervakade data. Konfigurera aviseringar på en Log Analytics arbets yta till Azure Sentinel eftersom det tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta möjliggör automatiserade lösningar, t. ex. spel böcker som skapas och används för att åtgärda säkerhets problem. Skapa anpassade logg aviseringar i Log Analytics arbets ytan med Azure Monitor. 

- [Övervaka Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Skapa, Visa och hantera logg aviseringar med Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: inga roller tilldelas som standard. De måste tilldelas explicit utifrån affärs behov. Roll tilldelningar kan kontrol leras med PowerShell CLI eller Azure Active Directory (Azure AD) för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Security Center identitets-och åtkomst hanterings funktioner för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton bör du dessutom använda rekommendationer från Security Center eller inbyggda Azure-principer, t. ex.: 

- Det bör finnas fler än en ägare som tilldelats din prenumeration 
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration 

- Externa konton med ägar behörigheter bör tas bort från din prenumeration

Skapa en process för att spåra identitets-och åtkomst kontroll för administrativa konton och granska den regelbundet.

- [Använda Azure Security Center för att övervaka identitet och åtkomst](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd Azure App Registration med ett huvud namn för tjänsten för att hämta en token som ska användas för att interagera med dina Recovery Services-valv via API-anrop.

- [Så här anropar du Azure REST API: er](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Registrera klient programmet (tjänstens huvud namn) med Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Information om Azure Recovery Services API](/rest/api/recoveryservices)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure AD, multifaktorautentisering och följ Security Center identitets-och åtkomst rekommendationer. 
- [Planera en Azure AD Multi-Factor Authentication-distribution](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en säker, Azure-hanterad arbets Station (kallas även en privilegie rad åtkomst arbets Station (Paw)) med Azure multifaktor-autentisering för administrativa uppgifter och för att utföra privilegierade åtgärder på Site Recovery resurser.

- [Arbetsstationer för privilegierad åtkomst (PAW)](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planera en molnbaserad distribution av Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure ads Privileged Identity Management-funktion (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.
Visa aviseringar och rapporter om riskfyllda användar beteenden med funktionen för identifiering av Azure AD-risk.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Azure Portal från enbart vissa logiska grupperingar av IP-adressintervall, regioner eller länder.
- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure AD som central-autentiserings-och auktoriserings system för Site Recovery. Azure AD skyddar data med stark kryptering för data i vila, under överföring och även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt. 

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Använd Azure AD-loggar för att identifiera inaktuella konton. 

Hantera grupp medlemskap effektivt, åtkomst till företags program och roll tilldelningar med Azure ADs identitets-och åtkomst granskningar. 

Skapa en process för att granska användar åtkomst regelbundet så att endast användare med slutförd åtkomst granskning har fortsatt åtkomst. 

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Använd Azure AD som central-autentiserings-och auktoriserings system för Site Recovery-resurser. Azure AD skyddar data med stark kryptering för data i vila och under överföring och även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Du har åtkomst till Azure AD-inloggning, gransknings-och risk händelse logg källor, som gör att du kan integrera dem med Azure Sentinel eller ett SIEM-eller övervaknings verktyg som finns på Azure Marketplace.

Effektivisera processen ytterligare genom att skapa diagnostiska inställningar för Azure AD-användarkonton och skicka gransknings-och inloggnings loggarna till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar inom en Log Analytics-arbetsyta.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Aktivera Azure-kontroll på kort](../sentinel/quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure AD som central-autentiserings-och auktoriserings system för dina Recovery Services-valv. 

Använd Azure ADs funktioner för identitets skydd för identifiering av konto inloggnings beteende och konfigurera automatiserade svar på identifierade misstänkta åtgärder, som rör användar identiteter. Hämta också data till Azure Sentinel för ytterligare undersökning.

- [Visa Azure AD-riskfylld inloggning](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer eller hanterings grupper för utveckling, testning och produktion Recovery Services valv. Separera resurser med ett virtuellt nätverk eller undernät, taggas på lämpligt sätt och skyddas av en nätverks säkerhets grupp eller Azure-brandvägg. 

Stäng av virtuella datorer, som lagrar eller bearbetar känsliga data, när de inte används. Implementera principer och procedurer för att göra den här återkommande processen. 

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Översikt över Site Recovery](site-recovery-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Använd privat länk eller privat slut punkt, nätverks säkerhets grupper och service märken för att minimera eventuella möjligheter för data exfiltrering från Site Recovery aktiverade virtuella datorer.

Microsoft hanterar den underliggande plattformen som används av Site Recovery och behandlar all kund information som känslig och skyddar mot kund data förlust och exponering. Microsoft har implementerat och har en uppsättning robusta data skydds kontroller och funktioner för att säkerställa att kunddata i Azure förblir säkra. 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

- [Replikera virtuella datorer med privata Azure-slutpunkter](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Replikera virtuella datorer med Azure Site Recovery Service-Taggar](azure-to-azure-about-networking.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Site Recovery använder en säker https-kanal, krypterad med Advanced Encryption Standard (AES 256), från Azure-arbetsbelastnings servrar till Site Recovery tjänster som ligger bakom ett Recovery Services-valv.

Aktuella TLS-versioner som stöds för Site Recovery är TLS 1,0, TLS 1,1, TLS 1,2 i regioner som är Live i slutet av 2019. TLS 1.2 är den enda TLS-version som stöds för nya regioner.

- [Förstå kryptering i överföring för Azure Site Recovery](physical-azure-set-up-source.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Site Recovery. 

Implementera en lösning från tredje part, om det behövs, i enlighet med kraven.

Microsoft hanterar den underliggande plattformen som används av Site Recovery och behandlar all kund information som känslig och skyddar mot kund data förlust och exponering. Den har implementerat och har en uppsättning robusta data skydds kontroller och-funktioner för att säkerställa att kunddata i Azure förblir säkra. 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera åtkomst till data och resurser som är relaterade till Site Recovery resurser. 

Separera arbets uppgifter med Azure RBAC och bevilja nödvändig åtkomst för dem. Använd de inbyggda Site Recovery rollerna för att kontrol lera Site Recovery hanterings åtgärder.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Använd Role-Based Access Control för att hantera Azure Site Recovery](site-recovery-role-based-linked-access-control.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Aktivera Double Encryption med både plattforms-och Kundhanterade nycklar. Den här funktionen är tillgänglig i Site Recovery. 

Site Recovery stöder kryptering i vila för data. För Azure IaaS-arbetsbelastningar krypteras data i vila med hjälp av Kryptering för lagringstjänst (SSE). 

Endast kunden har åtkomst till krypterings nyckeln när ett Recovery Services valv som är krypterat med en kundhanterad nyckel används. Microsoft underhåller aldrig en kopia, har inte åtkomst till nyckeln och dekrypterar inte data som överförs från primär till katastrof återställnings plats vid någon tidpunkt. 

- [Kund Managed Keys-stöd för Azure Site Recovery](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets loggar för att skapa aviseringar när ändringar sker i kritiska resurser. Dessa resurser kan omfatta produktions instanser av Recovery Services valv, resurser för Site Recovery tjänst och relaterade resurser.
- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga eller identifiera alla resurser, inklusive Recovery Services valv, i dina prenumerationer. Se till att du har behörighet att läsa i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för att Recovery Services valv och andra relaterade resurser, som används av Site Recovery med metadata, för att logiskt organisera dem i en taxonomi.
- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att ordna och spåra Site Recovery (Recovery Services valv) och andra relaterade resurser. 

Använd dessutom Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner: 

- Otillåtna resurstyper
- Tillåtna resurstyper

Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser baserat på kundens organisations krav.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner: 

- Otillåtna resurstyper 
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper 
- Tillåtna resurstyper

Det är viktigt att du förstår hur du skapar och hanterar principer i Azure för att du ska fortsätta att efterleva företagets standarder och servicenivåavtal.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". Detta kan förhindra att resurser skapas och ändras i en hög säkerhets miljö.

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för Recovery Services valvet med Azure policy. 

Använd Azure Policy alias i namn området "Microsoft. RecoveryServices" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen av Recovery Services Vault-resurserna i Site Recovery-tjänsten.
- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns]-effekter för att framtvinga säkra inställningar i dina Azure-resurser.
- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Välj Azure-databaser för att lagra och hantera din kod på ett säkert sätt om du använder anpassade Azure policys definitioner för dina Recovery Services-valv och relaterade resurser.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation om Azure databaser](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. RecoveryServices" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. 

Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. RecoveryServices" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. 

Använd Azure Policy [granskning], [neka] och [distribuera om det inte finns]-effekter för att automatiskt tillämpa konfigurationer för dina Azure-resurser.
- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: kunden bör hantera Site Recovery hemligheter som är integrerade med Azure Key Vault, samtidigt som haveri beredskap för Azure Disk Encryption-aktiverade virtuella datorer aktive ras. 

- [Så här skapar du ett nyckel valv](../key-vault/general/quick-create-portal.md)

- [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

- [Så här tilldelar du en åtkomst princip för nyckel valvet](../key-vault/general/assign-access-policy-portal.md)

- [Aktivera DR för Azure Disk Encryption-aktiverade virtuella datorer med Site Recovery](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Site Recovery stöder endast systemhanterade identiteter där en kund kan aktivera Systemhanterad identitet på Recovery Services valv. Samma metod gäller för resurser som används i ett katastrof återställnings erbjudande för att definiera åtkomst gränser. 

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. 

Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

- [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Så här aktiverar du Systemhanterad identitet i Recovery Services Vault](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Site Recovery), men det körs inte på ditt innehåll. Skanna alla filer som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage och Blob Storage.

Använd Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton.

- [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

- [Förstå Azure Security Centers hot identifiering för data tjänster](../security-center/azure-defender.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Site Recovery internt använder ett Azure Storage konto för att bibehålla status för haveri beredskaps lösningen, enligt vad som har kon figurer ATS av kunder på sina arbets belastningar.

Alla lagrings resurser som används av Site Recovery Services metadata med konfiguration av typen: Geo-redundant lagring med Läs behörighet (RA-GRS). Lagrings konton av typen ovan GRS (t. ex. RAGRS, RAG-ZRS) replikerar dina data till en sekundär region (hundratals mil bort från den primära platsen för data källan) för att fortsätta att betjäna haveri beredskap för kunder under drifts avbrott.

Detta är utanför kundens omfattning och Site Recovery teamet tar hand om det internt. Kunden kan säkerhetskopiera Key Vault nycklar i Azure.

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: regelbundet testa att återställa Kundhanterade nycklar.

- [Återställa Key Vault-nycklar i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: data krypteras i vila med hjälp av kryptering för lagringstjänst (SSE) med Azures infrastruktur som en tjänst (IaaS) baserat Virtual Machines. Aktivera mjuk borttagning i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du mjuk borttagning i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. 

Se till att det finns skriftliga svars planer för incidenter som definierar alla personal roller och faser av incident hantering eller hantering från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: prioritera vilka aviseringar som ska undersökas först baserat på Security Center tilldelade aviseringar – allvarlighets grad. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera prenumerationer tydligt (till exempel produktion, icke-produktion) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor och ändra planen efter behov

- [Läs NIST för att testa, träna och träna program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part. 

Skapa en process för att granska incidenter, efter förekomster, för att säkerställa att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. 

Använd Security Center Data Connector för att strömma aviseringarna till Azure Sentinel, efter behov.
- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.
- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)