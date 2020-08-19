---
title: Azures säkerhets bas linje för säkerhets kopiering
description: Azures säkerhets bas linje för säkerhets kopiering
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 72c038bda88ecc9b7518f1f764a41bd4c49f52c6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589426"
---
# <a name="azure-security-baseline-for-backup"></a>Azures säkerhets bas linje för säkerhets kopiering

Azures säkerhets bas linje för säkerhets kopiering innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: ej tillämpligt; Du kan inte associera ett virtuellt nätverk, ett undernät eller en nätverks säkerhets grupp med ett Recovery Services valv. När du säkerhetskopierar en virtuell Azure-dator överförs data via Azure-stamnätet. När du säkerhetskopierar från en lokal dator skapas en krypterad tunnel med en angiven slut punkt i Azure och autentiseringsuppgifter används för att förkonfigurera data innan de skickas via den krypterade tunneln.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: ej tillämpligt; Du kan inte associera ett virtuellt nätverk, ett undernät eller en nätverks säkerhets grupp med ett Recovery Services valv. När du säkerhetskopierar en virtuell Azure-dator överförs data via Azure-stamnätet. När du säkerhetskopierar från en lokal dator skapas en krypterad tunnel med en angiven slut punkt i Azure och autentiseringsuppgifter används för att förkonfigurera data innan de skickas via den krypterade tunneln.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: de slut punkter som används av Azure Backup (inklusive Microsoft Azure Recovery Services agent) hanteras av Microsoft. Du ansvarar för eventuella ytterligare kontroller som du vill distribuera till dina lokala system.

- [Förstå nätverks-och åtkomst stöd för MARS-agenten](./backup-support-matrix-mars-agent.md#networking-and-access-support)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: ej tillämpligt; Du kan inte associera ett virtuellt nätverk, ett undernät eller en nätverks säkerhets grupp med ett Recovery Services valv. När du säkerhetskopierar en virtuell Azure-dator överförs data via Azure-stamnätet. När du säkerhetskopierar från en lokal dator skapas en krypterad tunnel med en angiven slut punkt i Azure och autentiseringsuppgifter används för att förkonfigurera data innan de skickas via den krypterade tunneln.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: de slut punkter som används av Azure Backup (inklusive Microsoft Azure Recovery Services agent) hanteras av Microsoft. Du ansvarar för eventuella ytterligare kontroller som du vill distribuera till dina lokala system.

- [Förstå nätverks-och åtkomst stöd för MARS-agenten](./backup-support-matrix-mars-agent.md#networking-and-access-support)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: om du använder mars-agenten på en virtuell Azure-dator använder du AzureBackup-taggen på din NSG eller Azure-brandvägg för att tillåta utgående åtkomst till Azure Backup.

- [Säkerhetskopiera SQL Server databaser i virtuella Azure-datorer](./backup-sql-server-database-azure-vms.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: ej tillämpligt; slut punkterna som används av Azure Backup (inklusive Microsoft Azure Recovery Services agent) hanteras av Microsoft.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: om du använder mars-agenten på en virtuell Azure-dator kan du associera den virtuella datorn med en nätverks säkerhets grupp med hjälp av beskrivningen för att ange affärs behovet för regeln

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: om du använder mars-agenten på en virtuell Azure-dator som skyddas av en NSG eller Azure-brandvägg använder du Azure aktivitets logg för att övervaka konfigurationen av NSG eller brand väggen. Du kan skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av dessa resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: ej tillämpligt; Microsoft underhåller den tids källa som används för Azure-resurser, till exempel Azure Backup, för tidsstämplar i loggarna.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: för gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics arbets yta, Azure Event Hub eller Azure Storage-konto för arkivering. Med hjälp av Azures aktivitets logg data kan du avgöra vad, vem och när som helst för Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för dina Azure-resurser.

Dessutom skickas loggar via Azure Monitor för att samla in säkerhets data som genereras av Azure Backup. Använd Log Analytics arbets ytor i Azure Monitor för att fråga och utföra analyser och Använd lagrings konton för långsiktig/lagring. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en säkerhets incident och händelse hantering från tredje part (SIEM).

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/platform/activity-log.md)

- [Använda diagnostikinställningar till för Recovery Services valv](./backup-azure-diagnostic-events.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: för gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics arbets yta, Azure Event Hub eller Azure Storage-konto för arkivering. Med hjälp av Azures aktivitets logg data kan du avgöra vad, vem och när som helst för Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för dina Azure-resurser.

Dessutom skickar Azure Backup diagnostiska händelser som kan samlas in och användas i syfte att analysera, Avisera och rapportera. Du kan konfigurera diagnostikinställningar för ett Recovery Services valv via Azure Portal. Du kan skicka en eller flera diagnostiska händelser till ett lagrings konto, en Event Hub-eller en Log Analytics-arbetsyta.

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/platform/activity-log.md)

- [Använda diagnostikinställningar till för Recovery Services valv](./backup-azure-diagnostic-events.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperiod för Log Analytics arbets ytor som är kopplade till dina Azure Recovery Services-valv enligt organisationens regler för efterlevnad.

- [Ange parametrar för logg bevarande](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Azure Backup tillhandahåller inbyggda övervaknings-och aviserings funktioner i ett Recovery Services-valv. Dessa funktioner är tillgängliga utan någon ytterligare hanterings infrastruktur. Du kan också öka skalan för övervakning och rapportering med hjälp av Azure Monitor.

Aktivera diagnostiska inställningar för Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics till att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på de aktivitets logg data som kan ha samlats in för Recovery Services valv.

- [Övervaka Azure Backup arbets belastningar](./backup-azure-monitoring-built-in-monitor.md)

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/platform/activity-log.md)

- [Samla in och analysera Azure-aktivitets loggar i Log Analytics arbets yta i Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: Azure Backup tillhandahåller inbyggda övervaknings-och aviserings funktioner i ett Recovery Services-valv. Dessa funktioner är tillgängliga utan någon ytterligare hanterings infrastruktur. Du kan också öka skalan för övervakning och rapportering med hjälp av Azure Monitor.

Aviseringar är främst scenarier där användare meddelas så att de kan vidta relevanta åtgärder. Avsnittet om säkerhets kopierings aviseringar visar aviseringar som genererats av tjänsten Azure Backup. De här aviseringarna definieras av tjänsten och du kan inte anpassa skapandet av aviseringar.

Du kan också publicera en Log Analytics arbets yta till Azure Sentinel eftersom den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem. Dessutom kan du skapa anpassade logg aviseringar i Log Analytics arbets ytan med Azure Monitor.

- [Övervaka Azure Backup arbets belastningar](./backup-azure-monitoring-built-in-monitor.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Skapa, Visa och hantera logg aviseringar med Azure Monitor](../azure-monitor/platform/alerts-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; Azure Backup bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure Backup bearbetar eller skapar inte DNS-relaterade loggar.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Support dokumentation:

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure AD har inte begreppet standard lösen ord. Andra Azure-resurser som kräver ett lösen ord tvingar ett lösen ord att skapas med komplexitets krav och minsta längd på lösen ord, vilket varierar beroende på tjänsten. Du ansvarar för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.: det bör finnas fler än en ägare som tilldelats prenumerations föråldrade konton med ägar behörighet bör tas bort från din prenumerations externa konton med ägar behörigheter bör tas bort från din prenumeration

- [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd en Azure App-registrering (tjänstens huvud namn) för att hämta en token som kan användas för att interagera med dina Recovery Services-valv via API-anrop.

- [Så här anropar du Azure REST API: er](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Registrera klient programmet (tjänstens huvud namn) med Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Information om Azure Recovery Services API](/rest/api/recoveryservices/)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: när du utför kritiska åtgärder i Azure Backup måste du ange en säkerhets kod som är tillgänglig på Azure Portal. Genom att aktivera Azure Multi-Factor Authentication lägger du till ett säkerhets lager. Endast auktoriserade användare med giltiga autentiseringsuppgifter för Azure och autentiserade från en andra enhet kan komma åt Azure Portal.

- [Multi-Factor Authentication i Azure Backup](./backup-azure-security-feature.md)

- [Planera en molnbaserad distribution av Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med Azure Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera dina Azure Backup-aktiverade resurser.

- [Arbetsstationer för privilegierad åtkomst (PAW)](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Planera en molnbaserad distribution av Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Azure Portal från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AD) som central autentiserings-och auktoriserings system för dina Azure Backup-instanser. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Så här konfigurerar du Azure Backup att använda Azure AD-inloggning](../app-service/configure-authentication-provider-aad.md)

- [Så här skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (AD) innehåller loggar för att hjälpa dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: Använd Azure Active Directory (AD) som central autentiserings-och auktoriserings system för dina Azure Backup-instanser. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Du har åtkomst till Azure AD-inloggning, gransknings-och risk händelse logg källor, som gör att du kan integrera med Azure Sentinel eller en SIEM från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logg aviseringar i Log Analytics.

- [Så här integrerar du Azures aktivitets loggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Aktivera Azure-kontroll på kort](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directory (AD) som central-autentiserings-och auktoriserings system för dina Recovery Services-valv. Använd Azure AD Identity Protection-och risk identifierings funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter för konto inloggnings beteende avvikelse i kontroll planet (Azure Portal). Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så här konfigurerar du Azure Backup att använda Azure AD-inloggning](../app-service/configure-authentication-provider-aad.md)

- [Visa Azure AD-riskfylld inloggning](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig för tillfället. Customer Lockbox stöds ännu inte för Azure Backup.

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: när du säkerhetskopierar virtuella datorer i Azure IaaS tillhandahåller Azure Backup oberoende och isolerade säkerhets kopior för att skydda mot oavsiktlig destruktion av ursprungliga data. Säkerhets kopior lagras i ett Recovery Services valv med inbyggd hantering av återställnings punkter.

Implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion Recovery Services valv. Resurser bör åtskiljas av VNet/undernät, taggas på lämpligt sätt och skyddas av en NSG eller Azure-brandvägg. Resurser som lagrar eller bearbetar känsliga data bör vara tillräckligt isolerade. För Virtual Machines lagring eller bearbetning av känsliga data implementerar du principer och procedurer för att inaktivera dem när de inte används.

Support dokumentation:

- [Översikt över Azure Backup](./backup-overview.md)

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: inte tillgänglig för tillfället. funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Backup.

Microsoft hanterar den underliggande infrastrukturen för Azure Backup och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Förstå kundens data skydd i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: säkerhets kopiering av trafik från servrar till Recovery Services-valvet överförs via en säker https-länk och krypteras med hjälp av Advanced Encryption Standard (AES) 256 när den lagras i valvet.

- [Förstå kryptering i vila i Azure Backup](./backup-encryption.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: inte tillgänglig för tillfället. funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Backup.

Microsoft hanterar den underliggande infrastrukturen för Azure Backup och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Förstå kundens data skydd i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: inte tillgängligt för tillfället

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: rollbaserad åtkomst kontroll i Azure (Azure RBAC) möjliggör detaljerad åtkomst hantering för Azure. Med hjälp av RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete.

Azure Backup innehåller tre inbyggda roller för att kontrol lera säkerhets kopierings hanterings åtgärder: säkerhets kopierings deltagare, ansvariga för säkerhets kopiering och säkerhets kopierings läsare. Du kan mappa inbyggda säkerhets kopierings roller till olika säkerhets kopierings hanterings åtgärder.

- [Konfigurera RBAC i Azure](../role-based-access-control/role-assignments-portal.md)

- [Använd rollbaserad Access Control för att hantera Azure Backup återställnings punkter](./backup-rbac-rs-vault.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar den underliggande infrastrukturen för Azure Backup och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Data skydd för Azure-kunder](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure Backup stöder kryptering för vilande data. Vid lokal säkerhetskopiering tillhandahålls kryptering i vila med hjälp av den lösenfras som du anger när du säkerhetskopierar till Azure. För moln arbets belastningar krypteras data i vila med hjälp av Kryptering för lagringstjänst (SSE). Microsoft dekrypterar aldrig dina säkerhetskopierade data.

När du säkerhetskopierar med MARS-agenten eller använder ett Recovery Services valv som är krypterat med en kundhanterad nyckel, är det bara du som har åtkomst till krypterings nyckeln. Microsoft sparar aldrig någon kopia och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa dina säkerhetskopierade data.

- [Förstå kryptering i vila för Azure Backup](./backup-encryption.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktion av Azure Recovery Services-valv samt andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: ännu inte tillgänglig; sårbarhets bedömning i Azure Security Center är ännu inte tillgänglig för Azure Backup.

Underliggande plattform som genomsöks och korrigeras av Microsoft. Granska säkerhets kontroller som är tillgängliga för Azure Backup för att minska säkerhets risker för tjänst konfigurationen.

- [Förstå säkerhets kontroller som är tillgängliga för Azure Backup](./backup-security-controls.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: inte tillgänglig för tillfället. säkerhetskonfigurationer för Azure Backup stöds ännu inte i Azure Security Center.

- [Lista över Azure Security Center PaaS-tjänster som stöds](../security-center/features-paas.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er).  Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra Azure-resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner: ej tillåtna resurs typer tillåtna resurs typer

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll en inventering av godkända Azure-resurser och program varu titlar

**Vägledning**: definiera godkända Azure-resurser och godkänd program vara för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer.  Se till att alla Azure-resurser som finns i miljön är godkända.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner: ej tillåtna resurs typer tillåtna resurs typer

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript</div>

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för Recovery Services valvet med Azure policy. Använd Azure Policy alias i namn området "Microsoft. RecoveryServices" för att skapa anpassade principer som ska granskas eller tillämpa konfigurationen för dina Recovery Services-valv.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. RecoveryServices" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. RecoveryServices" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure-resurser.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: när du konfigurerar mars-agenten lagrar du lösen frasen för kryptering i Azure Key Vault.

- [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Så här ger Key Vault autentisering med en hanterad identitet](../key-vault/general/managed-identity.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: ej tillämpligt; Hanterade identiteter stöds inte för Azure Backup.

- [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Backup), men det körs inte på kund innehållet.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Backup), men det körs inte på ditt innehåll.

Genomsök alla filer som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage osv.

Använd Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton.

- [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

- [Förstå Azure Security Centers hot identifiering för data tjänster](../security-center/threat-protection.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för resurser som säkerhets kopie ras och inte Azure Backup.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: lokalt redundant lagring (LRS) replikerar dina data tre gånger (det skapas tre kopior av dina data) i en lagrings skalnings enhet i ett Data Center. Alla datakopior finns i samma region. LRS är ett billigt alternativ för att skydda dina data från lokala maskin varu problem. Geo-redundant lagring (GRS) är standard alternativet och det rekommenderade alternativet för replikering. GRS replikerar dina data till en sekundär region (hundratals mil bort från den primära platsen för datakällan). GRS kostar mer än LRS, men GRS ger en högre nivå av hållbarhet för dina data, även om det finns ett regionalt avbrott.

Säkerhetskopiera kund hanterade nycklar inom Azure Key Vault.

- [Översikt över Azure Backup](./backup-overview.md)

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Förstå kryptering i Azure Backup](./backup-encryption.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

- [Återställa Key Vault-nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: för lokal säkerhets kopiering tillhandahålls kryptering vid vila med hjälp av den lösen fras som du anger när du säkerhetskopierar till Azure. För virtuella Azure-datorer krypteras data i vila med hjälp av kryptering för lagringstjänst (SSE). Du kan aktivera mjuk borttagning i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du mjuk borttagning i Key Vault](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Bygg ut en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla personal roller och faser för incident hantering/hantering från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Du kan också använda NISTs hanterings guide för dator säkerhet för att hjälpa dig att skapa en egen incident svars plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor och ändra planen efter behov.

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviserings indikatorn.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Strömma aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar

**Vägledning**: – [Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../security/benchmarks/security-baselines-overview.md)