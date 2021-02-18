---
title: Azures säkerhets bas linje för Azure Sentinel
description: Azure Sentinel Security-slutpunkten ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d95a2318d94f52352c9546aedc24d0111c5ba0a4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571659"
---
# <a name="azure-security-baseline-for-azure-sentinel"></a>Azures säkerhets bas linje för Azure Sentinel

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview.md) till Azure Sentinel. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter **säkerhets kontrollerna** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Sentinel. **Kontroller** som inte gäller för Azure Sentinel har uteslutits. Om du vill se hur Azure Sentinel fullständigt mappar till Azures säkerhets benchmark kan du läsa mer i den [fullständiga mappnings filen för Azure Sentinel Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: du kan inte koppla ett virtuellt nätverk, undernät eller en nätverks säkerhets grupp direkt med Azure Sentinel. Du kan dock aktivera en privat Azure-slutpunkt för Log Analytics arbets ytan som är kopplad till Azure Sentinel för att begränsa kommunikationen till och från dina privata nätverk.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till din Azure Sentinel-arbetsyta. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska resurser sker.

- [Så här aktiverar du granskning i Azure Sentinel](resources.md)

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: gransknings loggar för Azure Sentinel upprätthålls i Azures aktivitets loggar. Du kan visa dessa data genom att strömma den från Azures aktivitets logg till Azure Sentinel där du sedan kan utföra forskning och analys på den.

- [Så här aktiverar du granskning i Azure Sentinel](resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: gransknings loggar för Azure Sentinel upprätthålls i Azures aktivitets loggar. Du kan visa dessa data genom att strömma den från Azures aktivitets logg till Azure Sentinel där du sedan kan utföra forskning och analys på den.

- [Så här aktiverar du granskning i Azure Sentinel](resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperiod för Log Analytics arbets ytor som är kopplade till dina Azure Sentinel-arbetsytor enligt organisationens regler för efterlevnad.

- [Ange parametrar för logg bevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: gransknings loggar för Azure Sentinel upprätthålls i Azures aktivitets loggar. Du kan visa dessa data genom att strömma den från Azure aktivitets logg till Azure Sentinel där du kan utföra sökningar och analyser på den. Analysera och övervaka loggar från dina Azure Sentinel-aktivitets logg instanser för avvikande beteende. Använd avsnittet "loggar" i Azure Sentinel-arbetsytan för att köra frågor eller skapa aviseringar baserat på dina Sentinel-loggar.

- [Så här aktiverar du granskning i Azure Sentinel](resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: gransknings loggar för Azure Sentinel upprätthålls i Azures aktivitets loggar. Du kan visa dessa data genom att strömma den från Azure aktivitets logg till Azure Sentinel där du kan utföra sökningar och analyser på den. Analysera och övervaka loggar från dina Azure Sentinel-aktivitets logg instanser för avvikande beteende. Använd avsnittet "loggar" i Azure Sentinel-arbetsytan för att köra frågor eller skapa aviseringar baserat på dina Sentinel-loggar.

- [Så här aktiverar du granskning i Azure Sentinel](resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: underhålla en inventering av de användar konton som har administrativ åtkomst till kontroll planet (t. ex. Azure Portal) på Azure Sentinel-arbetsytan. 

Du kan använda fönstret identitets-och åtkomst kontroll (IAM) i Azure Portal för din prenumeration för att konfigurera rollbaserad åtkomst kontroll i Azure (Azure RBAC). Rollerna tillämpas på användare, grupper, tjänstens huvud namn och hanterade identiteter i Active Directory.  Azure Sentinel använder också Azure RBAC för att tillhandahålla inbyggda administrativa roller, till exempel Azure Sentinel Contributor, som kan tilldelas användare, grupper och tjänster i Azure. 

- [Förstå anpassade roller](../role-based-access-control/custom-roles.md)

- [Förstå Azure RBAC i Azure Sentinel](roles.md)

- [Så här konfigurerar du Azure RBAC för arbets böcker](quickstart-get-visibility.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:

- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

- [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: kontroll Plans åtkomst för Azure Sentinel-arbetsytan (t. ex. Azure Portal) är tillgänglig via REST API och stöder SSO. Du autentiserar genom att ange Authorization-huvudet för dina begär anden till ett JSON Web Token som du får från Azure Active Directory.

- [Förstå Azure Log Analytics REST API](/rest/api/loganalytics/)

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: aktivera Azure Active Directory Multi-Factor Authentication och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med Azure AD Multi-Factor Authentication (MFA) aktiverat för att logga in på och konfigurera dina Azure Sentinel-relaterade resurser. 

- [Arbetsstationer för privilegierad åtkomst (PAW)](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planera en molnbaserad distribution av Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Azure Portal från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AD) som central-autentiserings-och auktoriserings system för dina Azure Sentinel-instanser. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Så här skapar och konfigurerar du en ny Azure AD-klient](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (AD) innehåller loggar för att hjälpa dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: använda Azure Active Directory (AD) som central-autentiserings-och auktoriserings system för dina Azure Sentinel-arbetsytor. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Du har åtkomst till Azure AD-inloggning, gransknings-och risk händelse logg källor, som gör att du kan integrera med Azure Sentinel eller en SIEM från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logg aviseringar i Log Analytics.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Aktivera Azure-kontroll på kort](quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: för konto inloggning beteende avvikelse i kontroll planet (t. ex. Azure Portal) använder du funktioner för Azure AD Identity Protection och risk identifiering för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Visa Azure AD-riskfylld inloggning](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig för tillfället. Customer Lockbox stöds ännu inte för Azure Sentinel eller Log Analytics-arbetsytan.

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillgängligt för tillfället

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för arbets ytor för utveckling, testning och produktions kontroll.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: om du använder en Azure eller en lokal virtuell dator som en syslog-vidarebefordrare måste du konfigurera syslog-daemon (rsyslog eller syslog-ng) för att kommunicera i TLS.

- [Ansluta din externa lösning med hjälp av vanligt händelse format](connect-common-event-format.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är inte tillgängliga än i Azure. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: du kan använda fönstret identitets-och åtkomst kontroll (IAM) i Azure Portal för att konfigurera rollbaserad åtkomst kontroll i Azure (Azure RBAC). Rollerna tillämpas på användare, grupper, tjänstens huvud namn och hanterade identiteter i Active Directory. Du kan använda inbyggda Azure-roller eller anpassade roller för enskilda användare och grupper. 

Azure Sentinel använder Azure RBAC för att tillhandahålla inbyggda roller som kan tilldelas till användare, grupper och tjänster i Azure. Med hjälp av Azure RBAC kan du använda och skapa roller i din säkerhets åtgärds grupp för att ge lämplig åtkomst till Azure Sentinel. Baserat på rollerna har du detaljerad kontroll över vilka användare med åtkomst till Azure Sentinel som kan se. Du kan tilldela Azure-roller i Azure Sentinel-arbetsytan direkt eller till en prenumeration eller resurs grupp som arbets ytan tillhör. Det finns tre angivna inbyggda Azure Sentinel-roller:

- Azure Sentinel-läsare
- Azure Sentinel-svarare
- Azure Sentinel-deltagare

Förutom Azure Sentinel-dedikerade Azure-roller finns Azure och Log Analytics inbyggda Azure-roller som kan ge en bredare uppsättning behörigheter som omfattar åtkomst till din Azure Sentinel-arbetsyta och andra resurser:

Azure-roller är ägare, deltagare och läsare. Azure-roller beviljar åtkomst över alla dina Azure-resurser, inklusive Log Analytics arbets ytor och Azure Sentinel-resurser.

Log Analytics roller är Log Analytics deltagare och Log Analytics läsare. Log Analytics roller beviljar åtkomst över alla dina Log Analytics arbets ytor.

Dessutom är varje kontroll arbets bok en Azure-resurs och du kan tilldela roller till användare för att hantera åtkomst.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Förstå anpassade roller](../role-based-access-control/custom-roles.md)

- [Förstå roller i Sentinel och Log Analytics](roles.md)

- [Så här konfigurerar du Azure RBAC för arbets böcker](quickstart-get-visibility.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure Sentinel och Azure Monitor Log Analytics arbets ytor använder för närvarande Microsoft-hanterade nycklar för att kryptera alla inneslutna data i vila. Möjligheten att ta med din egen nyckel stöds ännu inte fullt ut för Sentinel, utan kommer att ligga inom en snar framtid.

- [Översikt över Azure Monitor kundhanterad nyckel](../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview)

- [Förstå Kundhanterade nycklar i Azure Sentinel (för hands version)](customer-managed-keys.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: gransknings loggar för Azure Sentinel upprätthålls i Azures aktivitets loggar. Du kan visa dessa data genom att strömma den från Azures aktivitets logg till Azure Sentinel där du sedan kan utföra forskning och analys på den. I avsnittet "loggar" i Azure Sentinel-arbetsytan kan du skapa aviseringar för när ändringar sker i produktions-och arbets ytor i Azure Sentinel samt andra kritiska eller relaterade resurser.

- [Så här aktiverar du granskning i Azure Sentinel](resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er).  Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra Azure-resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer. 

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer.  Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för din Log Analytics arbets yta som är kopplad till din Sentinel-arbetsyta med Azure policy. Använd Azure Policy alias i namn området "Microsoft. OperationalInsights" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen för dina Log Analytics-arbetsytor.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade definitioner av Azure-principer använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. OperationalInsights" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. OperationalInsights" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Azure Sentinel stöder insamling av loggar från många källor med hjälp av olika anslutningar. Vissa av dessa anslutningar kräver installation med en Log Analytics arbets ytans nyckel. När du ställer in de här anslutningarna använder Azure Key Vault för att lagra dina nycklar för att förenkla hemlig hantering och undvika exponering för oavsiktliga autentiseringsuppgifter.

- [Anslut Sentinel till data källor](connect-data-sources.md)

- [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md) 

- [Så här ger Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Sentinel och Log Analytics), men det körs inte på kund innehåll. 

Det är ditt ansvar att förskanna allt innehåll som laddas upp till Azure-resurser som inte är Compute, inklusive Log Analytics-arbetsyta. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kund innehåll för program mot skadlig kod.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. I händelse av att en användare återkallar nyckel krypterings nyckeln, antingen genom att ta bort eller ta bort åtkomst för Azure Sentinel, inom en timme, kommer Azure Sentinel att bearbeta ändringen och beter sig som om data inte längre är tillgängliga. I det här läget kommer alla utförda åtgärder som använder permanenta lagrings resurser, till exempel data inmatning, beständiga konfigurations ändringar och incident skapande att förhindras. Tidigare lagrade data tas inte bort, men kommer inte att vara tillgängliga. Otillgängliga data regleras av data bevarande principen och tas bort i enlighet med principen.

Den enda åtgärd som är möjlig efter att krypterings nyckeln har återkallats eller tagits bort är ta bort konto.

Om åtkomsten återställs efter återkallning kommer Azure Sentinel att återställa åtkomsten till data inom en timme.

- [Så här aktiverar du Soft-Delete i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Förstå Kundhanterade nycklar i Azure Sentinel](customer-managed-keys.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](connect-azure-security-center.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)