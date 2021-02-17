---
title: Azures säkerhets bas linje för Azure DevTest Labs
description: Azures säkerhets bas linje för Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c192f881dd4a8544b9c833eece56fb3765cd07f8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582028"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azures säkerhets bas linje för Azure DevTest Labs

Azures säkerhets bas linje för Azure DevTest Labs innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar på Azure.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering
**Vägledning:** Microsoft hanterar tids källor för Azure-resurser. Du kan dock hantera tids inställningar för synkronisering för dina beräknings resurser.

I följande artikel finns information om hur du konfigurerar tidssynkronisering för Azure Compute-resurser: [tidssynkronisering för virtuella Windows-datorer i Azure](../virtual-machines/windows/time-sync.md). 

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Utforskaren

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar
**Vägledning:** Aktivera diagnostikinställningar för Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Aktivitets loggar ger insikt i de åtgärder som utfördes på Azure DevTest Labs instanser på hanterings Plans nivå. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på hanterings Plans nivå för dina DevTest Labs-instanser.

Mer information finns i [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål](../azure-monitor/essentials/diagnostic-settings.md).

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser
**Vägledning:** Aktivera diagnostikinställningar för Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Aktivitets loggar ger insikt i de åtgärder som utfördes på Azure DevTest Labs instanser på hanterings Plans nivå. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på hanterings Plans nivå för dina DevTest Labs-instanser.

Mer information finns i [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål](../azure-monitor/essentials/diagnostic-settings.md).

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system
**Vägledning:** Azure DevTest Labs virtuella datorer (VM) skapas och ägs av kunden. Det är därför organisationens ansvar att övervaka den. Du kan använda Azure Security Center för att övervaka beräknings-OS. Data som samlas in av Security Center från operativ systemet innehåller OS-typ och version, OS (Windows-händelseloggen), processer som körs, dator namn, IP-adresser och inloggad användare. Log Analytics agent samlar även in krasch dum par-filer.

Mer information finns i följande artiklar: 

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)
- [Förstå Azure Security Center insamling av data](../security-center/security-center-enable-data-collection.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning
***Vägledning:** I Azure Monitor anger du logg kvarhållningsperiod för Log Analytics arbets ytor som är kopplade till dina Azure DevTest Labs instanser enligt organisationens regler för efterlevnad.

Mer information finns i följande artikel: [så här anger du parametrar för logg bevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar
**Vägledning:** Aktivera diagnostiska inställningar för Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta. Kör frågor i Log Analytics till att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på de aktivitets logg data som kan ha samlats in för Azure DevTest Labs.

Mer information finns i följande artiklar:

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/diagnostic-settings.md)
- [Samla in och analysera Azure-aktivitets loggar i Log Analytics arbets yta i Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet
**Vägledning:** Använd Azure Log Analytics-arbetsytan för att övervaka och Avisera om avvikande aktiviteter i säkerhets loggar och händelser relaterade till din Azure DevTest Labs.

Mer information finns i följande artikel: så här [aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod
**Vägledning:** Inte tillämpligt. Azure DevTest Labs bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor
**Vägledning:** Inte tillämpligt. Azure DevTest Labs bearbetar eller skapar inte DNS-relaterade loggar.

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning
**Vägledning:** Azure DevTest Labs skapar Azure Compute-datorer som ägs och hanteras av kunden. Använd Microsoft Monitoring Agent på alla virtuella Azure Windows-datorer som stöds för att logga skapande händelsen för processen och `CommandLine` fältet. För virtuella Azure Linux-datorer som stöds kan du manuellt konfigurera konsol loggning per nod och använda syslog för att lagra data. Använd också Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggade data från virtuella Azure-datorer.

- [Datainsamling i Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Så här kör du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)
- [Syslog-datakällor i Azure Monitor](../azure-monitor/agents/data-sources-syslog.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll
*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton
**Vägledning:** Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att köra Ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Azure DevTest Labs roller](devtest-lab-add-devtest-user.md)  

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt
**Vägledning:** Azure Active Directory (Azure AD) har inte begreppet standard lösen ord. Andra Azure-resurser som kräver ett lösen ord kräver att ett lösen ord skapas med krav på komplexitet och minsta längd på lösen ord, vilket varierar beroende på tjänsten. Du är ansvarig för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord.

DevTest Labs har inte begreppet standard lösen ord. 

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton
**Vägledning:** Skapa standard operativa procedurer kring användningen av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:

- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

- [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](../security-center/security-center-identity-access.md)  
- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Azure DevTest Labs roller](devtest-lab-add-devtest-user.md)  

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory
**Vägledning:** DevTest Labs använder Azure AD-tjänsten för identitets hantering. Tänk på följande två viktiga aspekter när du ger användarna åtkomst till en miljö baserat på DevTest Labs:

- **Resurs hantering:** Den ger till gång till Azure Portal för att hantera resurser (skapa virtuella datorer, skapa miljöer, starta, stoppa, starta om, ta bort och tillämpa artefakter osv.). Resurs hantering görs i Azure med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Du tilldelar roller till användare och anger behörigheter för resurs-och åtkomst nivå.
- **Virtuella datorer (nätverks nivå)**: i standard konfigurationen använder virtuella datorer ett lokalt administratörs konto. Om det finns en tillgänglig domän (Azure AD Domain Services, en lokal domän eller en molnbaserad domän) kan datorer anslutas till domänen. Användare kan sedan använda sina domänbaserade identiteter med hjälp av domän anslutningens artefakt för att ansluta till datorerna. 

- [Referens arkitektur för DevTest Labs](devtest-lab-reference-architecture.md#architecture)
- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst
**Vägledning:** Aktivera Azure Active Directory (AD) Multi-Factor Authentication (MFA) och följ rekommendationerna för Azure Security Center identitets-och åtkomst hantering.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)  
- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center övervakning:*** Ja

**Ansvar:** Kund


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter
**Vägledning:** Använd Paw (Privileged Access Workstation) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)  
- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)  

**Azure Security Center övervakning:** EJ TILLÄMPLIGT

**Ansvar:** Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton
**Vägledning:** Använd Azure Active Directory (Azure AD) säkerhets rapporter för generering av loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)  
- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)  

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser
**Vägledning:** Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory
**Vägledning:** Använd Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst
**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Använd också granskning av Azure Identity Access för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/overview-reports.md)  
- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)  

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton
**Vägledning:** Du har åtkomst till Azure Active Directory (Azure AD) inloggnings aktiviteter, gransknings-och risk händelse logg källor, vilket gör att du kan integrera med alla/Monitoring-verktyg för säkerhets information och händelse hantering (SIEM).

Du kan effektivisera processen genom att skapa diagnostikinställningar för Azure Active Directory användar konton och skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan konfigurera aviseringar i Log Analytics arbets ytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning
**Vägledning:** Använd Azure Active Directory (Azure AD) risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)  
- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier
**Vägledning:** Customer Lockbox stöds för närvarande inte för Azure DevTest Labs.

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

## <a name="data-protection"></a>Dataskydd
*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information
**Vägledning:** Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information
**Vägledning:** Implementera separata prenumerationer eller hanterings grupper för utveckling, testning och produktion. Azure DevTest Labs instanser ska avgränsas av det virtuella nätverket/under nätet och taggas korrekt. 

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)
- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)
- [Så här konfigurerar du ett virtuellt nätverk för DevTest Labs](devtest-lab-configure-vnet.md)
- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)
- [Skapa och använda taggar för DevTest Labs](devtest-lab-add-tag.md)

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information
**Vägledning:** Ännu inte tillgängligt; funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure DevTest Labs.

Microsoft hanterar den underliggande infrastrukturen för Azure DevTest Labs och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Resursen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring
**Vägledning:** Azure DevTest Labs kräver TLS-krypterad kommunikation som standard. TLS-versioner 1,2 stöds för närvarande. Om klient biblioteket eller verktyget inte stöder TLS kan du göra det genom att aktivera okrypterade anslutningar via API: erna för Azure Portal eller hantering. I sådana fall där krypterade anslutningar inte är möjligt rekommenderar vi att du placerar labb-och klient program i ett virtuellt nätverk.

[Förstå kryptering i överförings scenariot för DevTest Labs](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/azure-devtest-labs-enforcing-tls-1-2-starting-may-01-2020/ba-p/1236279)

**Azure Security Center övervakning:** Ja

**Ansvar:** Resursen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data
**Vägledning:** Funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure DevTest Labs. Tagga instanser som innehåller känslig information som sådan och implementera en lösning från tredje part om det behövs för efterlevnad.

För den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kund innehåll som känsligt och ger fantastiska längder för att skydda mot förlust och exponering av kund information. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser
**Vägledning:** Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att styra åtkomsten till labb i Azure DevTest Labs.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)
- [Förstå roller i DevTest Labs](devtest-lab-add-devtest-user.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll
**Vägledning:** Om det är nödvändigt för att använda beräknings resurser som skapats som en del av DevTest Labs implementerar du ett tredjepartsverktyg, till exempel en automatiserad värdbaserade lösning för data förlust skydd, för att genomdriva åtkomst kontroller till data även när data kopieras av ett system.

För den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kund innehåll som känsligt och ger fantastiska längder för att skydda mot förlust och exponering av kund information. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Ej tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila
**Vägledning:** Azure DevTest Labs lagrar följande kund information:

- [Artefakt resultat](add-artifact-vm.md) som inkluderar distributions-och tilläggs loggar som genereras från att tillämpa artefakter
- [Formel dokument](devtest-lab-manage-formulas.md) som används för att skapa virtuella datorer från formler
- Operativ system och data diskar för virtuella labb datorer 

Artefakt resultat och formel dokument skickas till ett Azure Storage-konto som skapas som en del av varje labb distribution. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering kan inte inaktive ras. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar. Mer information finns i [kryptering för labb lagrings konto](encrypt-storage.md).

Som standard krypteras alla labb operativ system och data diskar med en plattforms hanterad nyckel. Alla hanterade diskar, ögonblicks bilder, avbildningar och data som skrivs till befintliga hanterade diskar krypteras automatiskt i vila med plattforms hanterade nycklar. Som labb ägare kan du konfigurera labb operativ system diskar som ska krypteras med en kundhanterad nyckel. Kryptering med en kundhanterad nyckel för labb data diskar kan för närvarande inte konfigureras via själva labbet. En prenumerations administratör kan dock konfigurera den här inställningen för labb diskar i en prenumeration för tillfället. Mer information finns i [kryptera labb DevTest Labs OS-diskar med Kundhanterade nycklar](encrypt-disks-customer-managed-keys.md).

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Resursen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser
**Vägledning:** Använd Azure Monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i DevTest Labs-instanser och andra viktiga eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)
- [Skapa aviseringar för DevTest Labs aktivitets logg händelser](create-alerts.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund



## <a name="vulnerability-management"></a>Sårbarhetshantering
*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg
**Vägledning:** Följ rekommendationerna från Azure Security Center om hur du skyddar Azure DevTest Labs-instanser och relaterade resurser.

Microsoft utför sårbarhets hantering på de underliggande resurser som har stöd för Azure DevTest Labs.

- [Förstå Azure Security Center rekommendationer](../security-center/recommendations-reference.md) 

**Azure Security Center övervakning:** Ja

**Ansvar:** Resursen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system
**Vägledning:** Använd Azure Uppdateringshantering för att se till att de senaste säkerhets uppdateringarna är installerade på dina virtuella Windows-och Linux-datorer inom DevTest Labs. För virtuella Windows-datorer kontrollerar du att Windows Update har Aktiver ATS och kon figurer ATS för automatisk uppdatering. Den här inställningen är för närvarande inte tillgänglig för konfigurering genom DevTest Labs, men labb administratören/prenumerations administratören kan konfigurera den här inställningen på de underliggande virtuella datorerna i prenumerationen. 

- [Så här konfigurerar du Uppdateringshantering för virtuella datorer i Azure](../automation/update-management/overview.md)
- [Förstå Azures säkerhets principer som övervakas av Security Center](../security-center/policy-reference.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part
***Vägledning:*** Som labb administratör kan du använda [DevTest Labs-artefakter](add-artifact-vm.md) för att automatisera uppdateringar av anpassade labb bilder, inklusive säkerhets korrigeringar och andra uppdateringar. 

Lär dig mer om [DevTest Labs image Factory](image-factory-create.md), som är en konfigurations-som-kod-lösning som skapar och distribuerar bilder automatiskt regelbundet med alla önskade konfigurationer. 

Som prenumerations administratör kan du också använda Azure Uppdateringshantering-lösningen för att hantera uppdateringar och korrigeringar för virtuella datorer med DevTest Labs. Uppdateringshantering använder den lokalt konfigurerade uppdaterings databasen för att korrigera Windows-system som stöds. Med verktyg som System Center Updates Publisher (Updates Publisher) kan du publicera anpassade uppdateringar i Windows Server Update Services (WSUS). Med det här scenariot kan Uppdateringshantering korrigera datorer som använder Configuration Manager som sitt uppdaterings lager med program vara från tredje part.

- [Uppdateringshantering lösning i Azure](../automation/update-management/overview.md)
- [Hantera uppdateringar och korrigeringar för dina virtuella datorer](../automation/update-management/overview.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker
**Vägledning:** Exportera genomsöknings resultat med jämna mellanrum och jämför resultaten för att verifiera att sårbarheter har åtgärd ATS. När du använder rekommendationen för sårbarhets hantering som föreslås av Azure Security Center kan kunden pivotera in den valda lösningens Portal för att visa historiska skannings data.

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker
**Vägledning:** Använd standard risk klassificeringarna (säkra poäng) som tillhandahålls av Azure Security Center.

- [Förstå Azure Security Center säkra Poäng](../security-center/secure-score-security-controls.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering
*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång
**Vägledning:** Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive DevTest Labs-resurser) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer och-resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)
- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata
**Vägledning:** Använd taggar för Azure-resurser för att logiskt organisera dem enligt en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)
- [Konfigurera taggar för DevTest Labs](devtest-lab-add-tag.md)

**Azure Security Center övervakning:** Inte tillgängligt

**Ansvar:** Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser
**Vägledning:** Använd taggning, hanterings grupper och separata prenumerationer och separata labb om det behövs för att organisera och spåra labb-och labbbaserade resurser. Stäm av inventering med jämna mellanrum och se till att obehöriga resurser tas bort från prenumerationen snabbt.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)
- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)
- [Så här skapar du ett labb med DevTest Labs](devtest-lab-create-lab.md)
- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)
- [Så här konfigurerar du taggar för ett labb](devtest-lab-add-tag.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser
**Vägledning:** Skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov. Som prenumerations administratör kan du också använda anpassningsbara program kontroller, en funktion i Azure Security Center som hjälper dig att definiera en uppsättning program som tillåts köras på konfigurerade grupper av labb datorer. Den här funktionen är tillgänglig för både Azure-och icke-Azure-fönster (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer.

- [Aktivera adaptiv program kontroll](../security-center/security-center-adaptive-application.md)
 
**Azure Security Center övervakning:** Ej tillämpligt **ansvar:** kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser
**Vägledning:** Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Använd också Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna. Den kan hjälpa till med hög säkerhets-baserade miljöer, till exempel de med lagrings konton.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser
**Vägledning:** Azure Automation ger fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser. Som prenumerations administratör kan du använda inventering av virtuella Azure-datorer för att automatisera insamling av information om all program vara i DevTest Labs-datorer i din prenumeration. Egenskaperna för program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. För att få åtkomst till installations datum och annan information, krävs kunden för att aktivera diagnostik på gästnivå och ta med Windows-händelseloggen till en Log Analytics-arbetsyta.

Förutom att använda Ändringsspårning för övervakning av program vara kan anpassningsbara program kontroller i Azure Security Center använda Machine Learning för att analysera de program som körs på dina datorer och skapa en lista över tillåtna från denna intelligens. Den här funktionen fören klar processen att konfigurera och underhålla principer för att tillåta listor, vilket gör att du kan undvika att oönskad program vara används i din miljö. Du kan konfigurera gransknings läge eller framtvinga läge. Gransknings läget granskar endast aktiviteten på de skyddade virtuella datorerna. Tvingande läge tvingar fram reglerna och kontrollerar att program som inte tillåts att köra är blockerade. 

- [En introduktion till Azure Automation](../automation/automation-intro.md)
- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)
- [Förstå anpassningsbara program kontroller](../security-center/security-center-adaptive-application.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program
**Vägledning:** Azure Automation ger fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser. Som prenumerations administratör kan du använda Ändringsspårning för att identifiera all program vara som är installerad på virtuella datorer som finns i DevTest Labs. Du kan implementera en egen process eller använda Azure Automation tillstånds konfiguration för att ta bort otillåten program vara.

- [En introduktion till Azure Automation](../automation/automation-intro.md)
- [Spåra ändringar i din miljö med Ändringsspårning-lösningen](../automation/change-tracking/overview.md)
- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md)

**Azure Security Center övervakning:** Inte tillgängligt

**Ansvar:** Kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program
**Vägledning:** Som prenumerations administratör kan du använda Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserad program vara körs, och all obehörig program vara blockeras från att köras på virtuella Azure-datorer som finns i DevTest Labs.

- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster
**Vägledning:** Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner: 

- Otillåtna resurstyper
- Tillåtna resurstyper

Se följande artiklar: 
- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar
**Vägledning:** Anpassningsbar program kontroll är en intelligent, automatiserad lösning från slut punkt till slut punkt från Azure Security Center, som hjälper dig att styra vilka program som kan köras på dina Azure-och icke-Azure-datorer (Windows och Linux), som finns i DevTest Labs. Observera att du måste vara prenumerations administratör för att konfigurera den här inställningen för de underliggande beräknings resurserna i DevTest Labs. Implementera en lösning från tredje part om den här inställningen inte uppfyller organisationens krav.

- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager
**Vägledning:** Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom **att konfigurera blockera åtkomst*** * för appen **Microsoft Azure hantering** .

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser
**Vägledning:** Beroende på typen av skript kan du använda konfigurationer för operativ system eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript på de virtuella datorer som finns i DevTest Labs. Du kan också använda Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserad program vara körs och all obehörig program vara blockeras från att köras på de underliggande virtuella Azure-datorerna.

- [Så här styr du körning av PowerShell-skript i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Azure Security Center övervakning:** Inte tillgängligt

**Ansvar:** Kund


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk
**Vägledning:** Program med hög risk som distribueras i din Azure-miljö kan isoleras med hjälp av virtuellt nätverk, undernät, prenumerationer, hanterings grupper och så vidare. och tillräckligt säkert med antingen en Azure-brandvägg, en brand vägg för webbaserade program (WAF) eller en nätverks säkerhets grupp (NSG).

- [Konfigurera virtuellt nätverk för DevTest Labs](devtest-lab-configure-vnet.md)
- [Översikt över Azure Firewall](../firewall/overview.md)
- [Översikt över brand väggen för webb program](../web-application-firewall/overview.md)
- [Översikt över Network-säkerhet](../virtual-network/network-security-groups-overview.md)
- [Översikt över Azure Virtual Network]()
- [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)
- [Beslutsguide för prenumerationer](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center övervakning:** Inte tillgängligt

**Ansvar:** Kund

## <a name="secure-configuration"></a>Säker konfiguration
**Mer information finns i säkerhets kontroll: säker konfiguration.**

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser
**Vägledning:** Använd Azure Policy alias för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina Azure-resurser som skapats som en del av DevTest Labs. Du kan också använda inbyggda Azure Policy definitioner.

Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)
- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)
- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)
- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system
**Vägledning:** Använd Azure Security Center rekommendationer för att underhålla säkerhetskonfigurationer på alla underliggande beräknings resurser som skapats som en del av DevTest Labs. Dessutom kan du använda anpassade operativ Systems avbildningar eller Azure Automation tillstånds konfiguration eller DevTest labb artefakter för att upprätta säkerhets konfigurationen för det operativ system som krävs av din organisation.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md)
- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)
- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md)
- [Ladda upp en virtuell hård disk och Använd den för att skapa nya virtuella Windows-datorer i Azure](../virtual-machines/windows/upload-generalized-managed.md)
- [Skapa en virtuell Linux-dator från en anpassad disk med Azure CLI](../virtual-machines/linux/upload-vhd.md)
- [Skapa och distribuera anpassade avbildningar till flera DevTest Labs](image-factory-save-distribute-custom-images.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Ej tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer
**Vägledning:** Använd Azure Policy **neka** och **distribuera om de inte finns** regler för att genomdriva säkra inställningar för Azure-resurser som har skapats som en del av DevTest Labs. Du kan också använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation.

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)
- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)
- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system
**Vägledning:** Följ rekommendationerna från Azure Security Center om att utföra sårbarhets bedömningar för dina underliggande Azure Compute-resurser som skapas som en del av ett labb. Du kan också använda Azure Resource Manager mallar, anpassade operativ Systems avbildningar eller Azure Automation tillstånds konfiguration för att underhålla säkerhets konfigurationen för det operativ system som krävs av din organisation. Du kan också använda avbildnings fabriks lösningen, som är en lösning för konfigurations-som-kod som skapar och distribuerar bilder automatiskt regelbundet med alla önskade konfigurationer.

Dessutom hanteras och underhålls avbildningar av virtuella Azure Marketplace-datorer som publicerats av Microsoft.

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](../security-center/deploy-vulnerability-assessment-vm.md)
- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md)
- [Exempelskript för att överföra en virtuell hårddisk till Azure och skapa en ny virtuell dator](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)
- [Så här skapar du en avbildnings fabrik i DevTest Labs](image-factory-create.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Resursen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt
**Vägledning:** Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps.

- [Azure databaser git-självstudie](/azure/devops/repos/git/gitworkflow)
- [Om behörigheter och grupper](/azure/devops/organizations/security/about-permissions?tabs=preview-page&view=azure-devops)
- [Integrering mellan Azure DevTest Labs-och Azure DevOps-arbetsflöde](devtest-lab-dev-ops.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt
**Vägledning:** Om du använder anpassade avbildningar använder du rollbaserad åtkomst kontroll i Azure (Azure RBAC) så att endast behöriga användare får åtkomst till avbildningarna. Med hjälp av ett delat avbildnings Galleri kan du dela dina avbildningar till vissa labb som behöver det. För behållar avbildningar lagrar du dem i Azure Container Registry och använder Azure RBAC för att se till att endast behöriga användare kan komma åt avbildningarna.

- [Förstå Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Så här konfigurerar du Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)
- [Konfigurera ett delat avbildnings Galleri för en DevTest Labs](configure-shared-image-gallery.md)
- [Lär dig mer om Azure RBAC för Container Registry](../container-registry/container-registry-roles.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration
**Vägledning:** Definiera och implementera standardinställda säkerhetskonfigurationer för Azure-resurser med hjälp av Azure Policy. Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser som skapats under DevTest Labs. Du kan också använda inbyggda princip definitioner som är relaterade till dina speciella resurser. Dessutom kan du använda Azure Automation för att distribuera konfigurations ändringar.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Använda alias](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system
**Vägledning:** Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala data Center. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett. Du kan också skriva en anpassad artefakt som kan installeras på alla labb datorer för att se till att de följer organisations principer. 

- [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](../automation/automation-dsc-onboarding.md)
- [Skapa anpassade artefakter för virtuella DevTest Labs-datorer](devtest-lab-artifact-author.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster
**Vägledning:** Använd Azure Security Center för att utföra bas linje genomsökningar för dina Azure-resurser som skapats under DevTest Labs. Du kan också använda Azure Policy för att varna och granska Azure-resursfiler.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)
 
**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system
**Vägledning:** Använd Azure Security Center för att utföra bas linje genomsökningar för OS-och Docker-inställningar för behållare.

- [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt
**Vägledning:** Använd Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

- [Konfigurera hanterad identitet för att distribuera Azure Resource Manager miljöer i DevTest Labs](use-managed-identities-environments.md)
- [Konfigurera hanterad identitet för att distribuera virtuella datorer i DevTest Labs](enable-managed-identities-lab-vms.md)
- [Så här skapar du ett nyckel valv](../key-vault/general/quick-create-portal.md)
- [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)
- [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt
**Vägledning:** Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

- [Konfigurera hanterad identitet för att distribuera Azure Resource Manager miljöer i DevTest Labs](use-managed-identities-environments.md)
- [Konfigurera hanterad identitet för att distribuera virtuella datorer i DevTest Labs](enable-managed-identities-lab-vms.md)
 
**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter
**Vägledning:** Implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- Så här konfigurerar du en inloggnings skanner

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund


## <a name="malware-defense"></a>Skydd mot skadlig kod
*Mer information finns i säkerhets kontroll: försvar mot skadlig kod.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod
**Vägledning:** Använd Microsoft Antimalware för Azure Cloud Services och Virtual Machines för att kontinuerligt övervaka och försvara dina resurser. För Linux använder du en lösning mot skadlig kod från tredje part. Använd också Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton.

- Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines
- Skydd mot hot i Azure Security Center

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute
**Vägledning:** Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure App Service som finns i ett labb), men det körs inte på ditt innehåll.
Skanna alla filer som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage och så vidare.

Använd Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton.

- Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines
- Förstå Azure Security Centers hot identifiering för data tjänster

**Azure Security Center övervakning:** Ja

**Ansvar:** Ej tillämpligt


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras
**Vägledning:** När det distribueras installeras automatiskt de senaste signatur-, plattforms-och motor uppdateringarna i Microsoft Antimalware för Azure. Följ rekommendationerna i Azure Security Center: "Compute & Apps" för att säkerställa att alla slut punkter för de underliggande beräknings resurserna för DevTest Labs är uppdaterade med de senaste signaturerna. Windows OS kan skyddas ytterligare med ytterligare säkerhet för att begränsa risken för virus-eller skadlig kodbaserade attacker med tjänsten Microsoft Defender Avancerat skydd som kan integreras med Azure Security Center.

- Så här distribuerar du Microsoft Antimalware för Azure Cloud Services och Virtual Machines
- Microsoft Defender Advanced Threat Protection

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

## <a name="data-recovery"></a>Dataåterställning
*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering
**Vägledning:** Azure DevTest Labs stöder för närvarande inte säkerhets kopiering av virtuella datorer och ögonblicks bilder. Du kan dock aktivera och konfigurera Azure Backup på de underliggande virtuella Azure-datorer som finns i DevTest Labs. Du kan också konfigurera önskad frekvens och kvarhållningsperiod för automatiska säkerhets kopieringar så länge du har tillräcklig åtkomst till de underliggande beräknings resurserna. 

- [En översikt över säkerhets kopiering av virtuella Azure-datorer](../backup/backup-azure-vms-introduction.md)
- [Säkerhetskopiera en virtuell Azure-dator från VM-inställningarna](../backup/backup-azure-vms-first-look-arm.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar
**Vägledning:** Azure DevTest Labs stöder för närvarande inte säkerhets kopiering av virtuella datorer och ögonblicks bilder. Du kan dock skapa ögonblicks bilder av dina underliggande virtuella Azure-datorer som finns i DevTest Labs eller de hanterade diskarna som är kopplade till dessa instanser med PowerShell eller REST-API: er så länge du har tillräcklig åtkomst till de underliggande beräknings resurserna. Du kan också säkerhetskopiera alla Kundhanterade nycklar i Azure Key Vault.

Aktivera Azure Backup på virtuella Azure-datorer och den önskade frekvensen och Retentions perioden. Den innehåller fullständig säkerhets kopiering av system tillstånd. Om du använder Azure Disk Encryption hanterar Azure VM Backup automatiskt säkerhets kopieringen av Kundhanterade nycklar.

- [Säkerhetskopiera virtuella Azure-datorer som använder kryptering](../backup/backup-azure-vms-encryption.md)
- [Översikt över säkerhets kopiering av virtuella Azure-datorer](../backup/backup-azure-vms-introduction.md)
- [En översikt över säkerhets kopiering av virtuella Azure-datorer](../backup/backup-azure-vms-introduction.md)
- [Säkerhetskopiera Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar
**Vägledning:** Se till att regelbundet utföra Data återställning av innehåll inom Azure Backup. Vid behov kan du prova att återställa innehåll till ett isolerat virtuellt nätverk eller en virtuell prenumeration. Du kan också testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

Om du använder Azure Disk Encryption kan du återställa den virtuella Azure-datorn med disk krypterings nycklarna. När du använder disk kryptering kan du återställa den virtuella Azure-datorn med disk krypterings nycklarna.

- [Säkerhetskopiera virtuella Azure-datorer som använder kryptering](../backup/backup-azure-vms-encryption.md)
- [Återställa filer från Azure VM backup](../backup/backup-azure-restore-files-from-vm.md)
- [Återställa Key Vault-nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [Säkerhetskopiera och återställa en krypterad virtuell dator](../backup/backup-azure-vms-encryption.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar
**Vägledning:** När du säkerhetskopierar hanterade diskar med Azure Backup krypteras de virtuella datorerna i rest med Kryptering för lagringstjänst (SSE). Azure Backup kan också säkerhetskopiera virtuella Azure-datorer som krypteras med hjälp av Azure Disk Encryption. Azure Disk Encryption integreras med BitLocker-BEKs (-krypterings nycklar) som skyddas i ett nyckel valv som hemligheter. Azure Disk Encryption integreras också med Azure Key Vault Key Encryption Keys (KeyExchange). Aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Mjuk borttagning för virtuella datorer](../backup/soft-delete-virtual-machines.md)
- [Översikt över Azure Key Vault – mjuk borttagning](../key-vault/general/soft-delete-overview.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

## <a name="incident-response"></a>Incidenthantering
*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide
**Vägledning:** Bygg ut en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla roller för personal och faser av incident hantering/hantering från identifiering till granskning efter incidenter.

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [Använd NIST hanterings guide för dator säkerhet för att hjälpa till med att skapa din egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur
**Vägledning:** Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data. Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)
- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer
**Vägledning:** Genomför övningar för att testa dina Systems svar på incident hantering på en vanlig takt för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter
**Vägledning:** Kontakt information om säkerhets incidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system
**Vägledning:** Exportera dina Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker med Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)
- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

#### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar
**Vägledning:** Använd funktionen för automatisering av arbets flöde i Azure Security Center att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)
 
Azure Security Center övervakning: * * * * ej tillämpligt

**Ansvar:** Kund


## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester
*Mer information finns i säkerhets kontroll: [inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar
**Vägledning:** Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft Cloud Red-teamning](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Resursen

## <a name="next-steps"></a>Nästa steg
Se följande artikel:

- [Säkerhets aviseringar för miljöer i Azure DevTest Labs](environment-security-alerts.md)